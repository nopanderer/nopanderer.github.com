---
layout: post
title: "[텐서플로우] ImageDataGenerator"
categories: ml
---

텐서플로우 ImageDataGenerator 에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 실제 이미지 데이터 전처리

실제 이미지 데이터의 경우 디렉토리 구조만 잘 만들어 놓으면 텐서플로우의 `ImageDataGenerator` 를 이용해서 쉽게 데이터 전처리 및 라벨링을 할 수 있음


`datasets/horses-or-human` 디렉토리가 다음과 같은 구조라고 하자.

```
datasets
+-- horses-or-human
    +-- horse
        +-- horse001.jpg
        +-- horse002.jpg
        +-- ...
    +-- human
        +-- human001.jpg
        +-- human002.jpg
        +-- ...
```

`horses-or-human` 서브 디렉토리명을 라벨로 하여 하위 이미지들이 자동으로 라벨링되어 처리된다.

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator

train_datagen = ImageDataGenerator(rescale=1/255)
validation_datagen = ImageDataGenerator(rescale=1/255)

train_generator = train_datagen.flow_from_directory(
    'datasets/horse-or-human/',  # This is the source directory for training images
    target_size=(150, 150),  # All images will be resized to 150x150
    batch_size=128,
    # Since we use binary_crossentropy loss, we need binary labels
    class_mode='binary')

validation_generator = validation_datagen.flow_from_directory(
    'datasets/validation-horse-or-human/',  # This is the source directory for training images
    target_size=(150, 150),  # All images will be resized to 150x150
    batch_size=32,
    # Since we use binary_crossentropy loss, we need binary labels
    class_mode='binary')
```

다음과 같이 출력

```
Found 1027 images belonging to 2 classes.
Found 256 images belonging to 2 classes.
```

학습시에 generator 를 전달

```python
history = model.fit(
    train_generator,
    steps_per_epoch=8,  
    epochs=15,
    verbose=1,
    validation_data = validation_generator,
    validation_steps=8)
```

## Data Augmentation

라벨링 뿐만 아니라, Data Augmentation 에 필요한 옵션들도 파라미터로 전달할 수 있음

```python
from tensorflow.keras.preprocessing.image import ImageDataGenerator

# All images will be rescaled by 1./255
train_datagen = ImageDataGenerator(
      rescale=1./255,
      rotation_range=40,
      width_shift_range=0.2,
      height_shift_range=0.2,
      shear_range=0.2,
      zoom_range=0.2,
      horizontal_flip=True,
      fill_mode='nearest')

validation_datagen = ImageDataGenerator(rescale=1/255)

# Flow training images in batches of 128 using train_datagen generator
train_generator = train_datagen.flow_from_directory(
        '../datasets/horse-or-human/',  # This is the source directory for training images
        target_size=(300, 300),  # All images will be resized to 150x150
        batch_size=128,
        # Since we use binary_crossentropy loss, we need binary labels
        class_mode='binary')

# Flow training images in batches of 128 using train_datagen generator
validation_generator = validation_datagen.flow_from_directory(
        '../datasets/validation-horse-or-human/',  # This is the source directory for training images
        target_size=(300, 300),  # All images will be resized to 150x150
        batch_size=32,
        # Since we use binary_crossentropy loss, we need binary labels
        class_mode='binary')
```

- rotation_range: 0도에서 40도까지 랜덤하게 회전
- width_shift_range: 0~20 퍼센트만큼 랜덤하게 이동시킴
- shear_range: 시계반대방향으로 20퍼만큼 랜덤하게 기울임
- zoom_range: +-20퍼센트만큼 확대
- horizontal_flip: 좌우반전
- fill_mode: augmentation 하면서 주위 픽셀을 어떻게 채울 것인지

다음과 같이 출력

```
Found 1027 images belonging to 2 classes.
Found 256 images belonging to 2 classes.
```
