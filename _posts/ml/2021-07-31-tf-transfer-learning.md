---
layout: post
title: "[텐서플로우] Transfer Learning"
categories: ml
---

텐서플로우 로 Transfer Learning 을 구현합니다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Transfer Learning 이란

- 다른 사람이 이미 학습시킨 모델을 가져다 쓰고 싶을 때
- 웬만한 모델들은 케라스에 다 내장되어 있고 weight 만 다운로드 받아서 전체 혹은 일부를 로드해서 쓸 수 있음

## 구현

- Inception 모델 사용
- `layer.trainable`을 통해 학습을 시킬지 안시킬지 선택할 수 있음
- 내가 원하는 부분까지만 가져올 수 있음. 여기선 `mixed7` 레이어까지만 가져옴

```python
import os

from tensorflow.keras import layers
from tensorflow.keras import Model
from tensorflow.keras.applications.inception_v3 import InceptionV3

local_weights_file = '../datasets/inception_v3_weights_tf_dim_ordering_tf_kernels_notop.h5'

pre_trained_model = InceptionV3(input_shape = (150, 150, 3), 
                                include_top = False, 
                                weights = None)

pre_trained_model.load_weights(local_weights_file)

for layer in pre_trained_model.layers:
    layer.trainable = False
  
# pre_trained_model.summary()

last_layer = pre_trained_model.get_layer('mixed7')
print('last layer output shape: ', last_layer.output_shape)
last_output = last_layer.output
```


```
last layer output shape:  (None, 7, 7, 768)
```

### 모델 만들기

```python
from tensorflow.keras.optimizers import RMSprop

# Flatten the output layer to 1 dimension
x = layers.Flatten()(last_output)
# Add a fully connected layer with 1,024 hidden units and ReLU activation
x = layers.Dense(1024, activation='relu')(x)
# Add a dropout rate of 0.2
x = layers.Dropout(0.2)(x)                  
# Add a final sigmoid layer for classification
x = layers.Dense  (1, activation='sigmoid')(x)           

model = Model( pre_trained_model.input, x) 

model.compile(
    optimizer = RMSprop(lr=0.0001), 
    loss = 'binary_crossentropy', 
    metrics = ['accuracy']
)
```
