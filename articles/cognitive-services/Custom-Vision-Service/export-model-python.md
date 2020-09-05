---
title: '자습서: Python으로 TensorFlow 모델 실행 - Custom Vision Service'
titleSuffix: Azure Cognitive Services
description: Python으로 TensorFlow 모델을 실행합니다. 이 문서는 Custom Vision 서비스의 이미지 분류 프로젝트에서 내보낸 모델에만 적용됩니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: af0e9cd66cf64366a6f563148fa6b075161151f4
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87875441"
---
# <a name="tutorial-run-tensorflow-model-in-python"></a>자습서: Python으로 TensorFlow 모델 실행

Custom Vision Service에서 [TensorFlow 모델을 내보낸](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) 후에 이 빠른 시작을 참조하여 이 모델을 로컬로 사용하여 이미지를 분류하는 방법을 알아봅니다.

> [!NOTE]
> 이 자습서는 이미지 분류 프로젝트에서 내보낸 모델에만 적용됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 사용하려면 다음을 수행해야 합니다.

- Python 2.7+ 또는 Python 3.5+ 중 하나를 설치합니다.
- pip를 설치합니다.

다음으로, 다음과 같은 패키지를 설치해야 합니다.

```
pip install tensorflow
pip install pillow
pip install numpy
pip install opencv-python
```

## <a name="load-your-model-and-tags"></a>모델 및 태그 로드

다운로드한 zip 파일에는 model.pb 및 labels.txt가 포함되어 있습니다. 이러한 파일은 학습된 모델 및 분류 레이블을 나타냅니다. 첫 번째 단계는 프로젝트에 모델을 로드하는 것입니다.

```Python
import tensorflow as tf
import os

graph_def = tf.compat.v1.GraphDef()
labels = []

# These are set to the default names from exported models, update as needed.
filename = "model.pb"
labels_filename = "labels.txt"

# Import the TF graph
with tf.io.gfile.GFile(filename, 'rb') as f:
    graph_def.ParseFromString(f.read())
    tf.import_graph_def(graph_def, name='')

# Create a list of labels.
with open(labels_filename, 'rt') as lf:
    for l in lf:
        labels.append(l.strip())
```

## <a name="prepare-an-image-for-prediction"></a>예측을 위해 이미지 준비

예측을 위한 이미지를 준비하기 위해 수행해야 하는 몇 가지 단계가 있습니다. 다음 단계는 학습 동안 수행되는 이미지 조작을 모방한 것입니다.

### <a name="open-the-file-and-create-an-image-in-the-bgr-color-space"></a>파일 열기 및 BGR 색 공간에 이미지 만들기

```Python
from PIL import Image
import numpy as np
import cv2

# Load from a file
imageFile = "<path to your image file>"
image = Image.open(imageFile)

# Update orientation based on EXIF tags, if the file has orientation info.
image = update_orientation(image)

# Convert to OpenCV format
image = convert_to_opencv(image)
```

### <a name="handle-images-with-a-dimension-1600"></a>1600보다 큰 차원으로 이미지 처리

```Python
# If the image has either w or h greater than 1600 we resize it down respecting
# aspect ratio such that the largest dimension is 1600
image = resize_down_to_1600_max_dim(image)
```

### <a name="crop-the-largest-center-square"></a>가장 큰 가운데 사각형 자르기

```Python
# We next get the largest center square
h, w = image.shape[:2]
min_dim = min(w,h)
max_square_image = crop_center(image, min_dim, min_dim)
```

### <a name="resize-down-to-256x256"></a>256x256까지 크기 조정

```Python
# Resize that square down to 256x256
augmented_image = resize_to_256_square(max_square_image)
```

### <a name="crop-the-center-for-the-specific-input-size-for-the-model"></a>모델의 특정 입력 크기에 맞게 가운데 자르기

```Python
# Get the input size of the model
with tf.compat.v1.Session() as sess:
    input_tensor_shape = sess.graph.get_tensor_by_name('Placeholder:0').shape.as_list()
network_input_size = input_tensor_shape[1]

# Crop the center for the specified network_input_Size
augmented_image = crop_center(augmented_image, network_input_size, network_input_size)

```

위의 단계는 다음과 같은 도우미 함수를 사용합니다.

```Python
def convert_to_opencv(image):
    # RGB -> BGR conversion is performed as well.
    image = image.convert('RGB')
    r,g,b = np.array(image).T
    opencv_image = np.array([b,g,r]).transpose()
    return opencv_image

def crop_center(img,cropx,cropy):
    h, w = img.shape[:2]
    startx = w//2-(cropx//2)
    starty = h//2-(cropy//2)
    return img[starty:starty+cropy, startx:startx+cropx]

def resize_down_to_1600_max_dim(image):
    h, w = image.shape[:2]
    if (h < 1600 and w < 1600):
        return image

    new_size = (1600 * w // h, 1600) if (h > w) else (1600, 1600 * h // w)
    return cv2.resize(image, new_size, interpolation = cv2.INTER_LINEAR)

def resize_to_256_square(image):
    h, w = image.shape[:2]
    return cv2.resize(image, (256, 256), interpolation = cv2.INTER_LINEAR)

def update_orientation(image):
    exif_orientation_tag = 0x0112
    if hasattr(image, '_getexif'):
        exif = image._getexif()
        if (exif != None and exif_orientation_tag in exif):
            orientation = exif.get(exif_orientation_tag, 1)
            # orientation is 1 based, shift to zero based and flip/transpose based on 0-based values
            orientation -= 1
            if orientation >= 4:
                image = image.transpose(Image.TRANSPOSE)
            if orientation == 2 or orientation == 3 or orientation == 6 or orientation == 7:
                image = image.transpose(Image.FLIP_TOP_BOTTOM)
            if orientation == 1 or orientation == 2 or orientation == 5 or orientation == 6:
                image = image.transpose(Image.FLIP_LEFT_RIGHT)
    return image
```

## <a name="classify-an-image"></a>이미지 분류

이미지가 텐서로서 준비되면 예측을 위해 모델을 통해 보낼 수 있습니다.

```Python

# These names are part of the model and cannot be changed.
output_layer = 'loss:0'
input_node = 'Placeholder:0'

with tf.compat.v1.Session() as sess:
    try:
        prob_tensor = sess.graph.get_tensor_by_name(output_layer)
        predictions = sess.run(prob_tensor, {input_node: [augmented_image] })
    except KeyError:
        print ("Couldn't find classification output layer: " + output_layer + ".")
        print ("Verify this a model exported from an Object Detection project.")
        exit(-1)
```

## <a name="view-the-results"></a>결과 보기

모델을 통해 이미지 텐서를 실행한 결과를 레이블에 다시 매핑해야 합니다.

```Python
    # Print the highest probability label
    highest_probability_index = np.argmax(predictions)
    print('Classified as: ' + labels[highest_probability_index])
    print()

    # Or you can print out all of the results mapping labels to probabilities.
    label_index = 0
    for p in predictions:
        truncated_probablity = np.float64(np.round(p,8))
        print (labels[label_index], truncated_probablity)
        label_index += 1
```

## <a name="next-steps"></a>다음 단계

다음으로, 모바일 애플리케이션에 모델을 래핑하는 방법을 알아봅니다.
* [Android 애플리케이션에서 내보낸 Tensorflow 모델 사용](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Swift iOS 애플리케이션에서 내보낸 CoreML 모델 사용](https://go.microsoft.com/fwlink/?linkid=857726)
* [Xamarin을 사용하는 iOS 애플리케이션에서 내보낸 CoreML 모델 사용](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)
