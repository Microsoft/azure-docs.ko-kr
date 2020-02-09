---
title: 기존 웹 서비스 다시 학습
titleSuffix: ML Studio (classic) - Azure
description: Azure Machine Learning Studio (클래식)에서 새로 학습 된 모델을 사용 하도록 모델을 다시 학습 하 고 기존 웹 서비스를 업데이트 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: peterclu
ms.author: amlstudiodocs
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/14/2019
ms.openlocfilehash: eac7674ae4a88621a803c70bd55a88e65b2cb7e9
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838686"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>클래식 Studio (클래식) 웹 서비스 다시 학습 및 배포

기계 학습 모델 다시 학습은 사용 가능한 가장 관련성 있는 데이터를 기반으로 계속 정확성을 유지하는 한 가지 방법입니다. 이 문서에서는 클래식 Studio (클래식) 웹 서비스를 다시 학습 하는 방법을 보여 줍니다. 새 Studio (클래식) 웹 서비스를 다시 학습 하는 방법에 대 한 지침은 [이 방법 문서를 참조 하세요.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>필수 조건

이 문서에서는 이미 다시 학습 실험과 예측 실험이 둘 다 있다고 가정합니다. 이 단계는 [기계 학습 모델 다시 학습 및 배포](/azure/machine-learning/studio/retrain-machine-learning-model)에서 설명합니다. 그러나 기계 학습 모델을 새로운 웹 서비스로 배포하는 대신, 예측 실험을 클래식 웹 서비스로 배포하겠습니다.
     
## <a name="add-a-new-endpoint"></a>새 엔드포인트 추가

배포한 예측 웹 서비스는 원래의 학습 및 점수 매기기 실험 학습된 모델과 동기화를 유지하는 기본 점수 매기기 엔드포인트를 포함합니다. 새로 학습된 모델을 사용하여 웹 서비스를 업데이트하려면 새 점수 매기기 엔드포인트를 만들어야 합니다.

웹 서비스에 새로 엔드포인트을 추가할 수 있는 두 가지 방법이 있습니다.

* 프로그래밍 방식
* Azure 웹 서비스 포털 사용

> [!NOTE]
> 엔드포인트를 학습 웹 서비스가 아닌 예측 웹 서비스에 추가해야 합니다. 학습 및 예측 웹 서비스를 모두 올바르게 배포한 경우 나열된 두 개의 별도 웹 서비스가 표시됩니다. 예측 웹 서비스는 "[예측 exp.]"로 끝나야 합니다.
>

### <a name="programmatically-add-an-endpoint"></a>프로그래밍 방식으로 엔드포인트를 추가합니다.

이 [GitHub 리포지토리](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint)에서 제공된 샘플 코드를 사용하여 점수 매기기 엔드포인트를 추가할 수 있습니다.

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Azure 웹 서비스 포털을 사용하여 엔드포인트 추가

1. Machine Learning Studio (클래식)의 왼쪽 탐색 열에서 웹 서비스를 클릭 합니다.
1. 웹 서비스 대시보드 아래쪽에서 **엔드포인트 관리 미리 보기**를 클릭합니다.
1. **추가**를 클릭합니다.
1. 새 엔드포인트에 대한 이름 및 설명을 입력합니다. 로깅 수준 및 예제 데이터 사용 여부를 선택합니다. 자세한 내용은 [Machine Learning 웹 서비스에 대해 로깅 사용](web-services-logging.md)을 참조하세요.

## <a name="update-the-added-endpoints-trained-model"></a>추가된 엔드포인트의 학습된 모델 업데이트

### <a name="retrieve-patch-url"></a>패치 URL 검색

### <a name="option-1-programmatically"></a>옵션 1: 프로그래밍 방식으로

프로그램 방식으로 올바른 패치 URL을 가져오려면 다음 단계를 수행합니다.

1. [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) 샘플 코드를 실행합니다.
1. AddEndpoint의 출력에서 *HelpLocation* 값을 찾아 URL을 복사합니다.

   ![addEndpoint 샘플의 출력에 있는 HelpLocation.](./media/retrain-classic/addEndpoint-output.png)
1. 웹 서비스에 대한 도움말 링크를 제공하는 페이지로 이동하려면 브라우저에 URL을 붙여 넣습니다.
1. **리소스 업데이트** 링크를 클릭하여 패치 도움말 페이지를 엽니다.

### <a name="option-2-use-the-azure-machine-learning-web-services-portal"></a>옵션 2: Azure Machine Learning 웹 서비스 포털 사용

웹 포털을 사용하여 올바른 패치 URL을 가져오려면 다음 단계를 수행합니다.

1. [Azure Machine Learning 웹 서비스](https://services.azureml.net/) 포털에 로그인합니다.
1. 맨 위에 있는 **웹 서비스** 또는 **기존 웹 서비스**를 클릭합니다.
1. 사용 중인 점수 매기기 웹 서비스를 클릭합니다(웹 서비스의 기본 이름을 수정하지 않은 경우 “[Scoring Exp.]”로 끝남).
1. **+새로 만들기**를 클릭합니다.
1. 엔드포인트가 추가된 후 엔드포인트 이름을 클릭합니다.
1. **패치** URL 아래의 **API 도움말**을클 릭하여 패치 도움말 페이지를 엽니다.

> [!NOTE]
> 예측 웹 서비스 대신 학습 웹 서비스에 엔드포인트을 추가한 경우 **업데이트 리소스** 링크를 클릭 하면 다음과 같은 오류가 표시 됩니다. "죄송 합니다 .이 기능은 지원 되지 않거나이 컨텍스트에서 사용할 수 없습니다. 이 웹 서비스에 업데이트할 수 있는 리소스가 없습니다. 불편을 끼쳐 드려 죄송합니다. 이 워크플로를 개선하도록 작업 중입니다.”
>

PATCH 도움말 페이지에는 사용해야 하는 PATCH URL이 들어 있으며 호출하는 데 사용할 수 있는 샘플 코드가 제공됩니다.

![패치 URL.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>엔드포인트 업데이트

이제 학습된 모델을 사용하여 이전에 만든 점수 매기기 엔드포인트를 업데이트할 수 있습니다.

다음 샘플 코드는 *BaseLocation*, *RelativeLocation*, *SasBlobToken* 및 PATCH URL을 사용하여 엔드포인트를 업데이트하는 방법을 보여 줍니다.

    private async Task OverwriteModel()
    {
        var resourceLocations = new
        {
            Resources = new[]
            {
                new
                {
                    Name = "Census Model [trained model]",
                    Location = new AzureBlobDataReference()
                    {
                        BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
                    }
                }
            }
        };

        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

            using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
            {
                request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
                HttpResponseMessage response = await client.SendAsync(request);

                if (!response.IsSuccessStatusCode)
                {
                    await WriteFailedResponse(response);
                }

                // Do what you want with a successful response here.
            }
        }
    }

호출에 대한 *apiKey* 및 *endpointUrl*은 엔드포인트 대시보드에서 가져올 수 있습니다.

*리소스*의 *Name* 매개 변수의 값은 예측 실험의 저장된 학습된 모델의 리소스 이름과 일치해야 합니다. 리소스 이름을 가져오려면:

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **Machine Learning**을 클릭합니다.
1. 이름 아래에서 작업 영역을 클릭한 다음 **웹 서비스**를 클릭합니다.
1. 이름 아래에서 **Census Model[예측 exp.]** 을 클릭합니다.
1. 추가한 새 엔드포인트를 클릭합니다.
1. 엔드포인트 대시보드에서 **업데이트 리소스**를 클릭합니다.
1. 웹 서비스에 대한 업데이트 리소스 API 설명서 페이지에서 **업데이트할 수 있는 리소스** 아래에 **리소스 이름**이 있습니다.

엔드포인트 업데이트를 완료하기 전에 SAS 토큰이 만료되는 경우 작업 ID로 GET을 수행하여 새 토큰을 가져와야 합니다.

코드가 성공적으로 실행된 경우 새 엔드포인트는 다시 학습된 모델을 사용하여 약 30초 내에 시작해야 합니다.

## <a name="next-steps"></a>다음 단계

웹 서비스를 관리하거나 여러 실험 실행을 추적하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [웹 서비스 포털 살펴보기](manage-new-webservice.md)
* [실험 반복 관리](manage-experiment-iterations.md)