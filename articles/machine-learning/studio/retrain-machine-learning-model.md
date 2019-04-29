---
title: 웹 서비스 학습 및 배포
titleSuffix: Azure Machine Learning Studio
description: Azure Machine Learning Studio에서 새로 학습한 기계 학습 모델을 사용하도록 웹 서비스를 업데이트하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/14/2019
ms.openlocfilehash: 395a231fcf43378a51e22427254abb196a46081e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107859"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>기계 학습 모델 재학습 및 배포

기계 학습 모델 다시 학습은 사용 가능한 가장 관련성 있는 데이터를 기반으로 계속 정확성을 유지하는 한 가지 방법입니다. 이 문서에서는 Studio에서 기계 학습 모델을 다시 학습하고 새 웹 서비스로 배포하는 방법을 보여 줍니다. 클래식 웹 서비스를 다시 학습하려는 경우 [이 방법 문서를 확인](retrain-classic-web-service.md)하세요.

이 문서는 예측 웹 서비스를 이미 배포했다고 가정합니다. 예측 웹 서비스가 아직 없는 경우 [여기서 Studio 웹 서비스 배포 방법을 알아봅니다](publish-a-machine-learning-web-service.md).

다음 단계에 따라 기계 학습의 새 웹 서비스를 다시 학습하고 배포합니다.

1. **재학습 웹 서비스** 배포
1. **재학습 웹 서비스**를 사용하여 새 모델 학습
1. 새 모델을 사용하도록 기존 **예측 실험** 업데이트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>재학습 웹 서비스 배포

재학습 웹 서비스를 사용하여 새 데이터와 같은 새로운 매개 변수 세트로 모델을 다시 학습한 후 나중에 사용할 수 있게 저장할 수 있습니다. **웹 서비스 출력**을 **모델 학습**에 연결하면 학습 실험이 사용할 수 있는 새 모델을 출력합니다.

다음 단계에 따라 재학습 웹 서비스를 배포합니다.

1. **웹 서비스 입력** 모듈을 데이터 입력에 연결합니다. 일반적으로 이 입력 데이터가 원래 학습 데이터와 동일한 방식으로 처리해야 합니다.
1. **웹 서비스 출력** 모듈을 **모델 학습**의 출력에 연결합니다.
1. **모델 평가** 모듈이 있는 경우 **웹 서비스 출력** 모듈을 연결하여 평가 결과를 출력할 수 있습니다.
1. 실험을 실행합니다.

    실험을 실행한 후 결과 워크플로는 다음 이미지와 비슷합니다.

    ![결과 워크플로](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    다음으로, 학습 실험을 학습된 모델 및 모델 평가 결과를 출력하는 재학습 웹 서비스로 배포합니다.

1. 실험 캔버스 맨 아래에서 **웹 서비스 설정**을 클릭합니다.
1. **웹 서비스 배포[신규]** 를 선택합니다. Azure Machine Learning Web Services 포털은 **웹 서비스 배포** 페이지에 열립니다.
1. 웹 서비스의 이름을 입력하고 결제 방식을 선택합니다.
1. **배포**를 선택합니다.

## <a name="retrain-the-model"></a>모델 재교육

이 예에서는 재학습 애플리케이션을 만드는 데 C#을 사용합니다. 또한 Python 또는 R 샘플 코드를 사용하여 이 작업을 수행할 수 있습니다.

다음 단계에 따라 재학습 API를 호출합니다.

1. Visual Studio에서 C# 콘솔 애플리케이션을 만듭니다. **새로 만들기** > **프로젝트** > **Visual C#** > **Windows 클래식 데스크톱** > **콘솔 앱(.NET Framework)**.
1. Machine Learning Web Service 포털에 로그인합니다.
1. 현재 작업 중인 웹 서비스를 클릭합니다.
1. **사용**을 클릭합니다.
1. **사용** 페이지의 맨 아래에 있는 **샘플 코드** 섹션에서 **Batch**를 클릭합니다.
1. 배치 실행을 위해 샘플 C# 코드를 복사한 다음 Program.cs 파일에 붙여 넣습니다. 네임스페이스를 그대로 유지하도록 합니다.

설명에서 지정된 대로 NuGet 패키지인 Microsoft.AspNet.WebApi.Client를 추가합니다. Microsoft.WindowsAzure.Storage.dll에 참조를 추가하려면 [Azure Storage 서비스용 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage)를 설치해야 합니다.

다음 스크린 샷은 Azure Machine Learning Web Services 포털의 **사용** 페이지를 보여줍니다.

![사용 페이지](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>apikey 선언 업데이트

**apikey** 선언을 찾습니다.

    const string apiKey = "abc123"; // Replace this with the API key for the web service

**사용** 페이지의 **기본 사용량 정보** 섹션에서 기본 키를 찾아 **apikey** 선언으로 복사합니다.

### <a name="update-the-azure-storage-information"></a>Azure Storage 정보 업데이트

BES 샘플 코드는 로컬 드라이브에서(예: "C:\temp\CensusInput.csv") Azure Storage로 파일을 업로드하고 이를 처리하고 결과를 Azure Storage에 다시 작성합니다.

1. Azure Portal에 로그인합니다.
1. 왼쪽 탐색 열에서 **추가 서비스**를 클릭하고, **저장소 계정**을 검색하고 선택합니다.
1. 저장소 계정 목록에서 하나를 선택하여 재학습된 모델을 저장합니다.
1. 왼쪽 탐색 열에서 **선택키**를 클릭합니다.
1. **기본 선택키**를 복사하고 저장합니다.
1. 왼쪽 탐색 열에서 **컨테이너**를 클릭합니다.
1. 기존 컨테이너를 선택하거나 새 컨테이너를 만들어 이름을 저장합니다.

*StorageAccountName*, *StorageAccountKey* 및 *StorageContainerName* 선언을 찾아 포털에서 저장한 값을 업데이트합니다.

    const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
    const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
    const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name

또한 코드에 지정한 위치에서 입력 파일을 사용할 수 있는지 확인해야 합니다.

### <a name="specify-the-output-location"></a>출력 위치를 지정합니다.

요청 페이로드에서 출력 위치를 지정할 때 *RelativeLocation* 에서 지정된 파일의 확장명을 csv에서 `ilearner`으로 지정해야 합니다.

    Outputs = new Dictionary<string, AzureBlobDataReference>() {
        {
            "output1",
            new AzureBlobDataReference()
            {
                ConnectionString = storageConnectionString,
                RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
            }
        },

다음은 재학습 출력 예제입니다.

![재학습 출력](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>재학습 결과 평가

애플리케이션을 실행할 때 출력은 평가 결과를 액세스하는 데 필요한 URL 및 공유 액세스 서명 토큰을 포함합니다.

*output2*에 대한 출력 결과의 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 조합하고 브라우저 주소 표시줄에 전체 URL을 붙여넣어 다시 학습된 모델의 성능 결과를 확인할 수 있습니다.

결과를 검사하여 새로 학습된 모델이 기존 모델보다 성능이 뛰어난지 여부를 확인합니다.

출력 결과에서 *BaseLocation*, *RelativeLocation* 및 *SasBlobToken*을 저장합니다.

## <a name="update-the-predictive-experiment"></a>예측 실험 업데이트

### <a name="sign-in-to-azure-resource-manager"></a>Azure Resource Manager로 로그인

먼저 사용 하 여 PowerShell 환경 내에서 Azure 계정에 로그인 합니다 [Connect AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet.

### <a name="get-the-web-service-definition-object"></a>웹 서비스 정의 개체 가져오기

다음으로 호출 하 여 웹 서비스 정의 개체를 가져올는 [Get AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservice) cmdlet.

    $wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

기존 웹 서비스의 리소스 그룹 이름을 확인 하려면 구독에서 웹 서비스를 표시 하려면 매개 변수 없이 Get AzMlWebService cmdlet을 실행 합니다. 웹 서비스를 찾은 다음 웹 서비스 ID를 살펴봅니다. 리소스 그룹 이름은 ID의 네 번째 요소로 *resourceGroups* 요소 바로 뒤에 있습니다. 다음 예제에서 리소스 그룹 이름은 Default-MachineLearning-SouthCentralUS입니다.

    Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
    Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
    Name : RetrainSamplePre.2016.8.17.0.3.51.237
    Location : South Central US
    Type : Microsoft.MachineLearning/webServices
    Tags : {}

또는 기존 웹 서비스의 리소스 그룹 이름을 결정하려면 Azure Machine Learning Web Services 포털에 로그인합니다. 웹 서비스를 선택합니다. 리소스 그룹 이름은 웹 서비스 URL의 다섯 번째 요소로 *resourceGroups* 요소 바로 뒤에 있습니다. 다음 예제에서 리소스 그룹 이름은 Default-MachineLearning-SouthCentralUS입니다.

    https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237

### <a name="export-the-web-service-definition-object-as-json"></a>JSON으로 웹 서비스 정의 개체 내보내기

새로 학습 된 모델을 사용 하도록 학습된 된 모델의 정의 수정 하려면 먼저 사용 해야 합니다 [내보내기 AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) cmdlet은 JSON 형식의 파일로 내보내야 합니다.

    Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"

### <a name="update-the-reference-to-the-ilearner-blob"></a>ilearner blob에 대한 참조 업데이트

자산에서 [학습된 모델]을 찾아, ilearner Blob의 URI와 함께 *locationInfo* 노드의 *uri* 값을 업데이트합니다. URI는 BES 재학습 호출의 출력에서 *BaseLocation* 및 *RelativeLocation*을 조합하여 만듭니다.

     "asset3": {
        "name": "Retrain Sample [trained model]",
        "type": "Resource",
        "locationInfo": {
          "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
        },
        "outputPorts": {
          "Results dataset": {
            "type": "Dataset"
          }
        }
      },

### <a name="import-the-json-into-a-web-service-definition-object"></a>JSON을 웹 서비스 정의 개체로 가져오기

사용 된 [가져오기-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/import-azmlwebservice) 수정 된 JSON 파일을 예측 실험을 업데이트 하는 데 사용할 수 있는 웹 서비스 정의 개체로 다시 변환 하는 cmdlet입니다.

    $wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"

### <a name="update-the-web-service"></a>웹 서비스 업데이트

마지막으로 사용 합니다 [업데이트 AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/update-azmlwebservice) 예측 실험을 업데이트 하는 cmdlet입니다.

    Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'

## <a name="next-steps"></a>다음 단계

웹 서비스를 관리하거나 여러 실험 실행을 추적하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [웹 서비스 포털 살펴보기](manage-new-webservice.md)
* [실험 반복 관리](manage-experiment-iterations.md)
