---
title: Keras
date: 2017-07-06
categories: Deep Learning
tags:
- Deep Learning
- libs
- keras
---
> 针对版本v2.0.5，参照[官方文档](https://keras.io/)  

## Getting Started

  Keras的核心数据结构是模型（`model`)。 最简单的模型是`Sequential`，它是层之间的线性叠加，更加复杂的结构请参考 [`Keras functional API`](https://keras.io/getting-started/functional-api-guide/)。  
### 简单的例子
* 初始化一个Sequential模型  
  ```
  from keras.models import Sequential  
  model = Sequential()  
  ```
* 搭建网络  
  ```
  from keras.layers import Dense, Activation  
  model.add(Dense(units=64, input_dim=100))  
  model.add(Activation('relu'))  
  model.add(Dense(units=10))  
  model.add(Activation('softmax'))  
  ```
* 设置学习方式  
  ```
  model.compile(loss='categorical_crossentropy',  
              optimizer='sgd',  
              metrics=['accuracy'])  
  ```
  也可以进行更复杂的定义：  
  ```
  model.compile(loss=keras.losses.categorical_crossentropy,  
              optimizer=keras.optimizers.SGD(lr=0.01, momentum=0.9, nesterov=True))  
  ```

* 迭代训练  
  ```
  # x_train and y_train are Numpy arrays --just like in the Scikit-Learn API.  
  model.fit(x_train, y_train, epochs=5, batch_size=32)  
  ```
  也可以手工自己喂batch：  
  ```
  model.train_on_batch(x_batch, y_batch)  
  ```

* 评估结果  
  ```
  loss_and_metrics = model.evaluate(x_test, y_test, batch_size=128)
  ```
* 生成预测的结果  
  ```  
  classes = model.predict(x_test, batch_size=128)`  
  ```
### Sequential Model导读

* 创建  
  - 通过传递一个包含层的实例的列表给构造函数创建
  ```
  from keras.models import Sequential  
  from keras.layers import Dense, Activation  

  model = Sequential([  
      Dense(32, input_shape=(784,)),  
      Activation('relu'),  
      Dense(10),  
      Activation('softmax'),  
  ])  
  ```
  - 通过`.add()`方法添加层  
  ```
  model = Sequential()  
  model.add(Dense(32, input_dim=784))  
  model.add(Activation('relu'))  
  ```

* 指定输入的形状  
  - `input_shape`  
  在第一层要指定输入的形状。`input_shape`参数是一个tuple或者None（表示输入形状可以是任意正数）。它不包含batch的维度。  
  - `input_dim`和`input_length`  
  一些层支持使用`input_dim`和`input_length`来代替`input_shape`指定输入的形状。  
  - `batch_size`  
  如果需要明确batch的大小，可以使用这个参数传入层中。  

* `compile`方法  
  它用来配置学习的处理方式。它接受以下三个参数：  
  - optimizer  
  它可以是已存在的优化器的标识符，如`rmsprop`或者`adagrad`，或者是一个`Optimizer`类的实例。  
  - 损失函数  
  同样，它可以是一些已有的标识符，如`categorical_crossentropy`或者`mse`，也可以是一个目标函数。  
  - metrics  
  一个metrics的列表（意味着可以有多个）。metric既可以是已有的metirc的标识符也可以是自定义的函数。  
  ```
  # For a multi-class classification problem
  model.compile(optimizer='rmsprop',
                loss='categorical_crossentropy',
                metrics=['accuracy'])

  # For a binary classification problem
  model.compile(optimizer='rmsprop',
                loss='binary_crossentropy',
                metrics=['accuracy'])

  # For a mean squared error regression problem
  model.compile(optimizer='rmsprop',
                loss='mse')

  # For custom metrics  
  import keras.backend as K  

  def mean_pred(y_true, y_pred):  
      return K.mean(y_pred)  
  
  model.compile(optimizer='rmsprop',  
                loss='binary_crossentropy',  
                metrics=['accuracy', mean_pred])  
  ```

* 训练  
  使用`fit`函数，输入是Numpy array形式的输入数据和标签。  
 
  - 参数  
  `fit(self, x, y, batch_size=32, epochs=10, verbose=1, callbacks=None, validation_split=0.0, validation_data=None, shuffle=True, class_weight=None, sample_weight=None, initial_epoch=0)`
  > verbose : 0表示不显示数据，1表示显示进度条，2表示只显示一个数据  
  - 返回  
  返回一个`History`实例。`History.history`属性记录了训练过程的损失值和metric值。  

* 例子  
  这个[链接](https://github.com/fchollet/keras/tree/master/examples)里有一些例子，让你学会使用keras   
  也可以直接在[这里](https://keras.io/getting-started/sequential-model-guide/#training)看几个例子。


### Functional API导读
  简单来说就是不用Sequential的方法，而直接使用`keras.models`或者`keras.layers`里面的函数来搭建网络。  
* 所有的模型都像层一样，是可以被调用的，调用是，不仅调用了模型的结构，也调用了它的参数   
  ```
  x = Input(shape=(784,))
  y = model(x)
  ```
* 多输入和多输出结构  
  ```
  model = Model(inputs=[main_input, auxiliary_input], outputs=[main_output, auxiliary_output])
  model.compile(optimizer='rmsprop', loss='binary_crossentropy',
              loss_weights=[1., 0.2])
  model.fit([headline_data, additional_data], [labels, labels],
          epochs=50, batch_size=32)
  
  ```
  如果给输入输出层命名了(eg. ` main_input`, `main_output`)，也可以这样写这两层：  
  ```
  model.compile(optimizer='rmsprop',
                loss={'main_output': 'binary_crossentropy', 'aux_output': 'binary_crossentropy'},
                loss_weights={'main_output': 1., 'aux_output': 0.2})

  # And trained it via:
  model.fit({'main_input': headline_data, 'aux_input': additional_data},
            {'main_output': labels, 'aux_output': labels},
            epochs=50, batch_size=32)
  ```
* 共享的层  
  不仅结构共享，权重也是共享的  
  ```
  # This layer can take as input a matrix
  # and will return a vector of size 64
  shared_lstm = LSTM(64)

  # When we reuse the same layer instance
  # multiple times, the weights of the layer
  # are also being reused
  # (it is effectively *the same* layer)
  encoded_a = shared_lstm(tweet_a)
  encoded_b = shared_lstm(tweet_b)
  ```
* 层的`node`  
  当某层被调用时会输出一个张量，同时会添加一个`node`，根据被调用的次数，`node`的编号由0开始递增。  
  `layer.ouput` 得到当前层的输出张量，新版本的`ouput`属性替代了原来的`get_output()`  
  `layer.ouput_shape` 得到输出的形状  
  如果一个层被调用多次，那么输出时，就会产生混乱，如下：  
  ```
  a = Input(shape=(140, 256))
  b = Input(shape=(140, 256))

  lstm = LSTM(32)
  encoded_a = lstm(a)
  encoded_b = lstm(b)

  lstm.output
  ```
  上述代码最后一行会报错，那么，就要使用`get_output_at(node_index)`这个方法来代替`output`了，如下：  
  ```
  lstm.get_output_at(0)
  ```
  当输入/输出的形状在不同node可能发生改变时，也要使用类似的方法，来代替`layer.output_shape/layer.input_shape`，如下：  
  ```
  conv.get_input_shape_at(0)
  ```

* 更多的例子  
  请参考这个[页面](https://keras.io/getting-started/functional-api-guide/)。
  

## Models

### 关于models
  Keras里面一共有两类models：`Sequential model`和`使用functional API的Model类`  
  这些models共有几个方法：  
  * `model.summary()` 打印model的summary表示  
  * `model.get_config` 返回一个字典，记录模型的配置，model可以通过它来重新实例化：  
      ```
      config = model.get_config()
      model = Model.from_config(config)
      # or, for Sequential:
      model = Sequential.from_config(config)
      ```
  * `model.get_weights()` 返回一个列表，里面以Numpy array的形式记载了模型中所有权重张量的值  
  * `model.set_weights(weights)` 设置权重值，weight是一个Numpy array构成的列表  
  * `model.to_json()` 以JSON字符串的格式，返回模型的表示。这个表示**只包括结构，不包括权重**，也可以从JSON字符串中重载模型：  
      ```
      from models import model_from_json

      json_string = model.to_json()
      model = model_from_json(json_string) 
      ```
  * `model.to_yaml()` 与json相似  
  * `model.save_weights(filepath)` 将权重存为HDF5文件  
  * `model.load_weight(filepath, by_name=False` 从HDF5文件中载入参数，当`by_name=True`时，只载入名字相同的层的参数  

### Sequential
> 参照[这个文档](https://keras.io/models/sequential/)
  * 属性： 
    - `model.layers` 一个包含模型的所有层的列表  

  * 方法：  
    - compile  
      配置学习过程  
    - fit  
      训练模型  
    - evaluate  
      以batch为单位，计算输入的损失  
    - pridict  
      生成输入样本的输出  
    - train_on_batch  
      在一个batch的样本上更新梯度一次  
    - test_on_batch  
      只评估一个batch的样本  
    - predict_on_batch   
      返回一个batch的样本的输出  
    - fit_generator  
      根据一个python的generator，以batch为单位，训练模型  
    - evaluate_generator  
      根据一个数据生成器来评估模型  
    - predict_generator  
      预测从数据生成器得到的输入样本
    - get_layer  
      根据索引或名字返回模型相应的层  

### Model(functional API)
> 参照[这个文档](https://keras.io/models/model/)
  * `Model`
    通过给`Model`类输入张量和输出张量，即能够初始化`Model`类，如下：  
    ```
    from keras.models import Model
    from keras.layers import Input, Dense

    a = Input(shape=(32,))
    b = Dense(32)(a)
    model = Model(inputs=a, outputs=b)
    ```
  * 属性  
    - `model.layers`   
    - `model.inputs`  
    - `model.outputs`  
  * 方法  
    - compile
    - fit
    - evaluate
    - predict 
    - train_on_batch
    - test_on_batch  
    - predict_on_batch  
    - fit_generator  
    - evaluate_generator  
    - predict_generator  
    - get_layer
  > 方法貌似都一样  
  
### Layers
> 参照[这个文档](https://keras.io/layers/about-keras-layers/)
  * 共有的方法  
    - `layer.get_weights()`
    - `layer.set_weights()`
    - `layer.get_config()`  
    layer可以通过如下方式重新初始化：  
    ```
    layer = Dense(32)
    config = layer.get_config()
    reconstructed_layer = Dense.from_config(config)
    ```
    或者  
    ```
    from keras import layers

    config = layer.get_config()
    layer = layers.deserialize({'class_name': layer.__class__.__name__,
                                'config': config})
    ```
#### 核心层
  * Dense  
    ```
    keras.layers.core.Dense(units, activation=None, use_bias=True, kernel_initializer='glorot_uniform', bias_initializer='zeros', kernel_regularizer=None, bias_regularizer=None, activity_regularizer=None, kernel_constraint=None, bias_constraint=None)
    ```   
    - 相当于这个操作`output = activation(dot(input, kernel) + bias)`   
    - units 表示输出的个数（神经元个数）  
    - 里面也可以添加正则化的配置  
    - 还可以对权重大小进行约束  

  * Activation
    ```
    keras.layers.core.Activation(activation)
    ```
    - activation可以是keras里的[激活函数](https://keras.io/activations/)，也可以是一个th或tf操作  
  * Dropout  
    ```
    keras.layers.core.Dropout(rate, noise_shape=None, seed=None)
    ``` 
  
  * 
