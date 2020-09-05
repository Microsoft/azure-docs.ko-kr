---
title: 예측 엔드포인트를 사용하여 Custom Vision 분류자를 통해 이미지를 프로그래밍 방식으로 테스트
titleSuffix: Azure Cognitive Services
description: API를 사용하여 Custom Vision Service 분류자를 통해 프로그래밍 방식으로 이미지를 테스트하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934755"
---
# <a name="use-your-model-with-the-prediction-api"></a>예측 API와 함께 모델 사용

모델을 학습 한 후에는 예측 API 끝점에 해당 이미지를 제출 하 여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다.

> [!NOTE]
> 이 문서에서는 C#을 사용하여 예측 API에 이미지를 제출하는 방법을 보여 줍니다. 자세한 내용 및 예제는 [예측 API 참조](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)를 참조 하세요.

## <a name="publish-your-trained-iteration"></a>학습 된 반복 게시

[Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택하고 __성능__ 탭을 선택합니다.

예측 API에 이미지를 제출 하려면 먼저 예측을 위해 반복을 게시 해야 합니다 .이 작업은 __게시__ 를 선택 하 고 게시 된 반복의 이름을 지정 하 여 수행할 수 있습니다. 이렇게 하면 Custom Vision Azure 리소스의 예측 API에서 모델에 액세스할 수 있습니다.

![성능 탭이 표시 되 고 게시 단추 주위에 빨간색 사각형이 표시 됩니다.](./media/use-prediction-api/unpublished-iteration.png)

모델이 성공적으로 게시 되 면 왼쪽 사이드바의 반복 옆에 "게시 된" 레이블이 표시 되 고, 해당 이름이 반복의 설명에 표시 됩니다.

![게시 된 레이블 주위에 빨간색 사각형과 게시 된 반복의 이름을 포함 하는 성능 탭이 표시 됩니다.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL 및 예측 키 가져오기

모델을 게시 한 후에는 __예측 URL__을 선택 하 여 필요한 정보를 검색할 수 있습니다. 그러면 예측 __URL__ 및 __예측 키__를 포함 하 여 예측 API를 사용 하기 위한 정보가 포함 된 대화 상자가 열립니다.

![성능 탭은 예측 URL 단추 주위에 빨간색 사각형으로 표시 됩니다.](./media/use-prediction-api/published-iteration-prediction-url.png)

![성능 탭은 이미지 파일 및 예측 키 값을 사용 하기 위한 예측 URL 값을 둘러싼 빨간색 사각형으로 표시 됩니다.](./media/use-prediction-api/prediction-api-info.png)


이 가이드에서는 로컬 이미지를 사용 하므로 임시 위치에 **이미지 파일이 있는 경우** URL을 복사 합니다. 해당 하는 __예측 키__ 값도 복사 합니다.

## <a name="create-the-application"></a>애플리케이션 만들기

1. Visual Studio에서 새 c # 콘솔 응용 프로그램을 만듭니다.

1. 다음 코드를 __Program.cs__ 파일의 본문으로 사용합니다.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. 다음 정보를 변경합니다.
   * 필드를 `namespace` 프로젝트의 이름으로 설정 합니다.
   * 자리 표시자를 `<Your prediction key>` 이전에 검색 한 키 값으로 바꿉니다.
   * 자리 표시자를 `<Your prediction URL>` 앞에서 검색 한 URL로 바꿉니다.

## <a name="run-the-application"></a>애플리케이션 실행

응용 프로그램을 실행 하면 콘솔에 이미지 파일의 경로를 입력 하 라는 메시지가 표시 됩니다. 그런 다음 이미지는 예측 API에 전송 되 고 예측 결과는 JSON 형식 문자열로 반환 됩니다. 다음은 예제 응답입니다.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 사용자 지정 이미지 분류자/탐지기에 이미지를 제출 하 고 c # SDK를 사용 하 여 프로그래밍 방식으로 응답을 수신 하는 방법을 배웠습니다. 다음으로, c #을 사용 하 여 종단 간 시나리오를 완료 하거나 다른 언어 SDK를 사용 하 여 시작 하는 방법을 알아봅니다.

* [빠른 시작: Custom Vision SDK](quickstarts/image-classification.md)
