---
title: '빠른 시작: Azure REST API 및 Node.js로 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 이미지에서 얼굴을 감지하기 위해 Node.js와 함께 Azure Face REST API를 사용합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-javascript
ms.openlocfilehash: 245cb90fa49e844b6e190f6acb8a44f339fd29dd
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539321"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-nodejs"></a>빠른 시작: Face REST API 및 Node.js를 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 이미지에서 사람 얼굴을 감지하기 위해 Node.js와 함께 Azure Face REST API를 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
- [Visual Studio Code](https://code.visualstudio.com/download) 같은 코드 편집기

## <a name="set-up-the-node-environment"></a>노드 환경 설정

프로젝트를 만들 폴더로 이동하고 *facedetection.js*라는 새 파일을 만듭니다. 그런 다음, `axios` 모듈을 이 프로젝트에 설치합니다. 그러면 스크립트로 HTTP 요청을 만들 수 있습니다.

```shell
npm install axios --save
```

## <a name="write-the-nodejs-script"></a>Node.js 스크립트 작성

다음 코드를 *facedetection.js*에 붙여넣습니다. 이 필드는 Face 서비스에 연결하는 방법과 입력 데이터를 가져올 위치를 지정합니다. [환경 변수를 만들고](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows#configure-an-environment-variable-for-authentication) Face 구독 키와 엔드포인트를 이 환경 변수에 추가합니다. 사용자 고유의 입력 이미지를 가리키도록 `imageUrl` 필드를 변경하려고 할 수 있습니다.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```javascript
'use strict';

const axios = require('axios').default;

// Add a valid subscription key and endpoint to your environment variables.
let subscriptionKey = process.env['FACE_SUBSCRIPTION_KEY']
let endpoint = process.env['FACE_ENDPOINT'] + '/face/v1.0/detect'

// Optionally, replace with your own image URL (for example a .jpg or .png URL).
let imageUrl = 'https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/faces.jpg'
```

그런 다음, 아래 코드를 추가하여 Face API를 호출하고 입력 이미지에서 얼굴 특성 데이터를 가져옵니다. `returnFaceAttributes` 필드는 검색할 얼굴 특성을 지정합니다. 의도된 용도에 따라 이 문자열을 변경하려고 할 수 있습니다.


```javascript
// Send a POST request
axios({
    method: 'post',
    url: endpoint,
    params : {
        returnFaceId: true,
        returnFaceLandmarks: false,
        returnFaceAttributes: 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise'
    },
    data: {
        url: imageUrl,
    },
    headers: { 'Ocp-Apim-Subscription-Key': subscriptionKey }
}).then(function (response) {
    console.log('Status text: ' + response.status)
    console.log('Status text: ' + response.statusText)
    console.log()
    //console.log(response.data)
    response.data.forEach((face) => {
      console.log('Face ID: ' + face.faceId)
      console.log('Face rectangle: ' + face.faceRectangle.top + ', ' + face.faceRectangle.left + ', ' + face.faceRectangle.width + ', ' + face.faceRectangle.height)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Head pose: ' + JSON.stringify(face.faceAttributes.headPose))
      console.log('Gender: ' + face.faceAttributes.gender)
      console.log('Age: ' + face.faceAttributes.age)
      console.log('Facial hair: ' + JSON.stringify(face.faceAttributes.facialHair))
      console.log('Glasses: ' + face.faceAttributes.glasses)
      console.log('Smile: ' + face.faceAttributes.smile)
      console.log('Emotion: ' + JSON.stringify(face.faceAttributes.emotion))
      console.log('Blur: ' + JSON.stringify(face.faceAttributes.blur))
      console.log('Exposure: ' + JSON.stringify(face.faceAttributes.exposure))
      console.log('Noise: ' + JSON.stringify(face.faceAttributes.noise))
      console.log('Makeup: ' + JSON.stringify(face.faceAttributes.makeup))
      console.log('Accessories: ' + JSON.stringify(face.faceAttributes.accessories))
      console.log('Hair: ' + JSON.stringify(face.faceAttributes.hair))
      console.log()
    });
}).catch(function (error) {
    console.log(error)
});
```

## <a name="save-and-run-the-script"></a>스크립트 저장 및 실행

변경한 후에는 명령 프롬프트를 열고 `node` 명령으로 파일을 실행합니다.

```
node facedetection.js
```

다음은 `response.data`의 전체 JSON 데이터입니다. 예를 들면 다음과 같습니다.

```json
[
  {
    "faceId": "ae8952c1-7b5e-4a5a-a330-a6aa351262c9",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "b1bb3cbe-5a73-4f8d-96c8-836a5aca9415",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Face 서비스를 호출하는 Node.js 스크립트를 작성하여 이미지에서 얼굴을 감지하고 특성을 반환했습니다. 이제 Face API 참조 설명서에서 자세한 내용을 알아보겠습니다.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
