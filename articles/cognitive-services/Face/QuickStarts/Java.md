---
title: '빠른 시작: Azure REST API 및 Java로 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 이미지에서 얼굴을 감지하기 위해 Java와 함께 Azure Face REST API를 사용합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 8aaf0b25a20f24739bb556583cd020d8f11eaf2c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549527"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>빠른 시작: REST API 및 Java를 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 이미지에서 사람 얼굴을 감지하기 위해 Java와 함께 Azure Face REST API를 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동**을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 원하는 Java IDE

## <a name="create-the-java-project"></a>Java 프로젝트 만들기

1. IDE에서 새 명령줄 Java 앱을 만들고 **main** 메서드와 함께 **Main** 클래스를 추가합니다.
1. Java 프로젝트로 다음 라이브러리를 가져옵니다. Maven을 사용하는 경우 각 라이브러리에 대한 Maven 좌표가 제공됩니다.
   - [Apache HTTP 클라이언트](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpclient:4.5.6)
   - [Apache HTTP 코어](https://hc.apache.org/downloads.cgi)(org.apache.httpcomponents:httpcore:4.4.10)
   - [JSON 라이브러리](https://github.com/stleary/JSON-java)(org.json:json:20180130)
   - [Apache Commons 로깅](https://commons.apache.org/proper/commons-logging/download_logging.cgi)(commons-logging:commons-logging:1.1.2)

## <a name="add-face-detection-code"></a>얼굴 감지 코드 추가

프로젝트의 기본 클래스를 엽니다. 여기에 이미지를 로드하고 얼굴을 감지하는 데 필요한 코드를 추가합니다.

### <a name="import-packages"></a>패키지 가져오기

파일 맨 위에 다음 `import` 문을 추가합니다.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>필수 필드 추가

**Main** 클래스를 다음 코드로 바꿉니다. 이 데이터는 Face 서비스에 연결하는 방법과 입력 데이터를 가져올 위치를 지정합니다. `subscriptionKey` 필드를 구독 키의 값으로 업데이트하고, 올바른 엔드포인트 문자열이 포함되도록 `uriBase` 문자열을 변경해야 합니다. `imageWithFaces` 값을 다른 이미지 파일을 가리키는 경로로 설정할 수도 있습니다.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

`faceAttributes` 필드는 단순히 특정 유형의 특성 목록입니다. 감지된 얼굴에 대해 검색할 정보를 지정합니다.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    private static final String uriBase =
        "https://<My Endpoint String>.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>얼굴 감지 REST API 호출

다음 코드로 **main** 메서드를 추가합니다. Face API에 대한 REST 호출을 구성하여 원격 이미지의 얼굴 정보를 감지합니다(검색할 얼굴 특성을 지정하는 `faceAttributes` 문자열). 그런 다음, 출력 데이터를 JSON 문자열에 기록합니다.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>JSON 응답 구문 분석

이전 코드 바로 아래에 다음 블록을 추가합니다. 그러면 반환된 JSON 데이터가 콘솔에 인쇄되기 전에 보다 쉽게 읽을 수 있는 형식으로 변환합니다. 마지막으로 try-catch 블록, **main** 메서드 및 **Main** 클래스를 닫습니다.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>앱 실행

코드를 컴파일하고 실행합니다. 성공적인 응답은 얼굴 데이터를 쉽게 읽을 수 있는 JSON 형식으로 콘솔 창에서 표시합니다. 예를 들면 다음과 같습니다.

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Face API에 대한 REST 호출을 사용하여 이미지의 얼굴을 감지하고 해당 특성을 반환하는 간단한 Java 콘솔 애플리케이션을 만들었습니다. 다음으로, Face API 참조 설명서를 살펴보고 지원되는 시나리오에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)