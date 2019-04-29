---
title: 모델에 대 한 여러 끝점을 만들려면
titleSuffix: Azure Machine Learning Studio
description: PowerShell을 사용하여 알고리즘은 동일하지만 다른 학습 데이터 세트로 여러 Machine Learning 모델 및 웹 서비스 엔드포인트를 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: a191a7adc2c43337b663fc44a8ef40df9d8ffef4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773709"
---
# <a name="use-powershell-to-create-studio-models-and-web-service-endpoints-from-one-experiment"></a>PowerShell을 사용하여 한 실험에서 Studio 모델 및 웹 서비스 엔드포인트 만들기

일반적인 기계 학습 문제는 동일한 학습 워크플로를 포함하고 동일한 알고리즘을 사용하지만 서로 다른 학습 데이터 세트를 입력으로 사용하려는 것입니다. 이 문서에서는 단일 실험을 사용하여 Azure Machine Learning Studio에서 대규모로 이 작업을 수행하는 방법을 보여 줍니다.

예를 들어, 글로벌 자전거 임대 프랜차이즈 업체를 소유하고 있다고 가정해 보겠습니다. 기록 데이터를 기반으로 임대 수요를 예측하는 회귀 모델을 구축하려고 합니다. 전 세계에 1,000개의 임대 위치가 있으며 각 위치에 대한 데이터 세트를 수집했습니다. 여기에는 각 위치와 관련된 날짜, 시간, 날씨 및 트래픽과 같은 중요한 특징이 포함됩니다.

모든 위치의 모든 데이터 세트에 대한 병합된 버전을 사용하여 모델을 한 번 학습할 수 있습니다. 하지만 위치마다 환경이 고유하므로 각 위치에 대한 데이터 세트를 사용하여 회귀 모델을 별도로 학습하는 것이 더 나은 방법입니다. 이렇게 하면 학습된 각 모델이 서로 다른 저장소 크기, 볼륨, 지리, 인구, 자전거 친화적인 교통 환경 등을 고려할 수 있습니다.

이것이 가장 좋은 방법일 수 있지만 Azure Machine Learning Studio에서 각각 고유한 위치를 나타내는 1,000번의 학습 실험을 만들고 싶지는 않을 것입니다. 이는 엄청난 작업일 뿐 아니라, 각 실험마다 학습 데이터 세트를 제외하고는 모든 구성 요소가 동일하므로 비효율적입니다.

다행히 [Azure Machine Learning Studio 재학습 API](/azure/machine-learning/studio/retrain-machine-learning-model)를 사용하고 [Azure Machine Learning Studio PowerShell](powershell-module.md)로 작업을 자동화하여 이 작업을 수행할 수 있습니다.

> [!NOTE]
> 샘플이 더 빨리 실행되도록 위치 수를 1,000개에서 10개로 줄입니다. 하지만 동일한 원리와 절차가 1,000개의 위치에 적용됩니다. 그러나 1,000개 데이터 세트에서 학습하려는 경우 다음 PowerShell 스크립트를 병렬로 실행하는 것이 좋습니다. 이 작업을 수행하는 방법은 이 문서의 범위를 벗어나는 내용이지만 인터넷에서 PowerShell 다중 스레딩의 예제를 찾을 수 있습니다.  
> 
> 

## <a name="set-up-the-training-experiment"></a>학습 실험 설정
[Cortana Intelligence 갤러리](https://gallery.azure.ai)에 있는 예제 [학습 실험](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1)을 사용합니다. [Azure Machine Learning Studio](https://studio.azureml.net) 작업 영역에서 이 실험을 엽니다.

> [!NOTE]
> 이 예제를 함께 수행하려면 무료 작업 영역보다는 표준 작업 영역을 사용하는 것이 좋습니다. 고객당 엔드포인트 하나(총 10개 엔드포인트)를 만들고 체험 작업 영역은 3개 엔드포인트로 제한되므로 이 경우 표준 작업 영역이 필요합니다. 체험 작업 영역만 있는 경우 3개의 위치만 허용하도록 스크립트를 변경합니다.
> 
> 

이 실험에서는 **데이터 가져오기** 모듈을 사용하여 Azure 저장소 계정에서 학습 데이터 세트 *customer001.csv* 를 가져옵니다. 모든 자전거 임대 위치에서 학습 데이터 집합을 수집하고 이를 *rentalloc001.csv*에서 *rentalloc10.csv*까지 범위의 파일 이름으로 동일한 Blob Storage 위치에 저장한다고 가정해 보겠습니다.

![판독기 모듈에서 Azure blob에서 데이터를 가져옵니다.](./media/create-models-and-endpoints-with-powershell/reader-module.png)

**웹 서비스 출력** 모듈이 **모델 학습** 모듈에 추가되었습니다.
이 실험을 웹 서비스로 배포할 때 해당 출력과 연결된 엔드포인트에서 학습된 모델을 .ilearner 파일 형식으로 반환합니다.

또한 **데이터 가져오기** 모듈에서 사용하는 URL을 정의하는 웹 서비스 매개 변수를 설정합니다. 이렇게 하면 각 위치에 대한 모델을 학습하기 위한 개별 학습 데이터 세트를 지정하는 데 매개 변수를 사용할 수 있습니다.
다른 방법으로 이 작업을 수행할 수도 있습니다. 웹 서비스 매개 변수와 함께 SQL 쿼리를 사용하여 SQL Azure 데이터베이스에서 데이터를 가져올 수 있습니다. 또는 **웹 서비스 입력** 모듈을 사용하여 데이터 세트를 웹 서비스에 전달할 수 있습니다.

![웹 서비스 출력 모듈을 Trained Model 모듈 출력](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

이제 학습 데이터 세트로 *rental001.csv* 기본값을 사용하여 이 학습 실험을 실행해 보겠습니다. **평가** 모듈의 출력을 보면(출력을 클릭하고 **시각화** 선택) *AUC* = 0.91이라는 적절한 성능을 보이는 것을 확인할 수 있습니다. 이제 이 학습 실험의 웹 서비스 출력을 배포할 준비가 되었습니다.

## <a name="deploy-the-training-and-scoring-web-services"></a>학습 및 점수 매기기 웹 서비스 배포
학습 웹 서비스를 배포하려면 실험 캔버스 아래 **웹 서비스 설정** 단추를 클릭하고 **웹 서비스 배포**를 선택합니다. 이 웹 서비스를 "Bike Rental Training"(자전거 대여 학습)이라고 합니다.

이제 점수 매기기 웹 서비스를 배포해야 합니다.
이 작업을 수행하려면 캔버스 아래에서 **웹 서비스 설정**을 클릭하고 **예측 웹 서비스**를 선택합니다. 점수 매기기 실험을 만듭니다.
웹 서비스로 작동하려면 몇 가지 사소한 측면을 조정해야 합니다. 입력 데이터에서 레이블 열 “cnt”를 제거하고 출력을 인스턴스 ID 및 해당 예측 값으로만 제한합니다.

해당 작업을 직접 저장하려면 미리 준비된 갤러리에서 [예측 실험](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1)을 열면 됩니다.

웹 서비스를 배포하려면 예측 실험을 실행하고 캔버스 아래의 **웹 서비스 배포** 단추를 클릭합니다. 점수 매기기 웹 서비스 이름을 "Bike Rental Scoring"(자전거 대여 점수 매기기)으로 지정합니다.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>PowerShell에서 10개의 동일한 웹 서비스 엔드포인트 만들기
이 웹 서비스는 기본 엔드포인트를 함께 제공합니다. 하지만 업데이트할 수 없으므로 기본 엔드포인트에는 그다지 관심이 없습니다. 각 위치당 하나씩 10개의 추가 엔드포인트를 만들어야 합니다. 이 작업은 PowerShell을 사용하여 수행할 수 있습니다.

먼저 PowerShell 환경을 설정합니다.

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and is properly set to point to the valid Workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

그런 다음 아래 PowerShell 명령을 실행합니다.

    # Create 10 endpoints on the scoring web service.
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpointName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

이제 10개의 엔드포인트를 만들었고 모두 *customer001.csv*에서 학습된 동일한 학습 모델을 포함합니다. Azure Portal에서 끝점을 볼 수 있습니다.

![포털에서 학습 된 모델의 목록을 보려면](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>PowerShell을 사용하여 별도의 학습 데이터 세트를 사용하도록 엔드포인트 업데이트
다음 단계는 각 고객의 개별 데이터에서 고유하게 학습된 모델로 엔드포인트를 업데이트하는 것입니다. 하지만 먼저 **Bike Rental Training** 웹 서비스에서 이러한 모델을 생성해야 합니다. **Bike Rental Training** 웹 서비스를 다시 살펴보겠습니다. 10가지 서로 다른 모델을 생성하기 위해서는 10개의 서로 다른 학습 데이터 세트로 BES 엔드포인트를 10번 호출해야 합니다. **InovkeAmlWebServiceBESEndpoint** PowerShell cmdlet을 사용하여 이 작업을 수행합니다.

Blob Storage 계정에 대한 자격 증명을 `$configContent`, 즉 `AccountName`, `AccountKey` 및 `RelativeLocation` 필드에 제공해야 합니다. `AccountName`은 **Azure Portal**(*저장소* 탭)에 표시되는 계정 이름 중 하나일 수 있습니다. 저장소 계정에서 클릭하면 아래쪽의 **선택키 관리** 단추를 누르고 *기본 선택키*를 복사하여 해당 `AccountKey`를 찾을 수 있습니다. `RelativeLocation`은 새 모델을 저장할 저장소의 상대적인 경로입니다. 예를 들어 다음 스크립트의 경로 `hai/retrain/bike_rental/`은 `hai`라는 컨테이너를 가리키고 `/retrain/bike_rental/`은 하위 폴더입니다. 현재 포털 UI 통해 하위 폴더를 만들 수는 없지만 [여러 Azure Storage 탐색기](../../storage/common/storage-explorers.md)에서 이 작업을 수행할 수 있습니다. 다음과 같이 학습된 새 모델(.iLearner 파일)을 저장할 새 컨테이너를 저장소에 만드는 것이 좋습니다. 저장소 페이지 아래쪽의 **추가** 단추를 클릭하고 `retrain`으로 이름을 지정합니다. 요약하면 다음 스크립트의 필수 변경 내용은 `AccountName`, `AccountKey` 및 `RelativeLocation`(:`"retrain/model' + $seq + '.ilearner"`)과 관련이 있습니다.

    # Invoke the retraining API 10 times
    # This is the default (and the only) endpoint on the training web service
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

> [!NOTE]
> BES 엔드포인트는 이 작업에 대해 지원되는 유일한 모드입니다. RRS는 학습된 모델을 생성하는 데 사용할 수 없습니다.
> 
> 

위와 같이 서로 다른 BES 작업 구성 json 파일 10개를 구성하는 대신 구성 문자열을 동적으로 만듭니다. 그런 다음 **InvokeAmlWebServceBESEndpoint** cmdlet의 *jobConfigString* 매개 변수에 피드합니다. 디스크에 사본을 보관할 필요가 없습니다.

모든 작업이 제대로 진행되면 잠시 후에 *model001.ilearner*에서 *model010.ilearner*까지 .iLearner 파일 10개가 Azure Storage 계정에 표시됩니다. 이제 **Patch-AmlWebServiceEndpoint** PowerShell cmdlet을 사용하여 이러한 모델로 점수 매기기 웹 서비스 엔드포인트 10개를 업데이트할 준비가 되었습니다. 앞에서 프로그래밍 방식으로 만든 기본이 아닌 엔드포인트만 패치할 수 있습니다.

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '<my_blob_sas_token>'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }

이 작업은 매우 신속하게 실행해야 합니다. 실행이 완료되면 예측 웹 서비스 엔드포인트 10개가 성공적으로 만들어집니다. 각 엔드포인트에는 한 임대 위치와 관련된 데이터 세트에서 고유하게 학습된 학습 모델이 포함되며, 모두 하나의 학습 실험에서 생성됩니다. 이를 확인하려면 **InvokeAmlWebServiceRRSEndpoint** cmdlet을 사용하고 동일한 입력 데이터를 제공하여 이러한 엔드포인트를 호출해 볼 수 있습니다. 모델이 서로 다른 학습 집합으로 학습되었기 때문에 다른 예측 결과가 표시됩니다.

## <a name="full-powershell-script"></a>전체 PowerShell 스크립트
전체 소스 코드 목록은 다음과 같습니다.

    Import-Module .\AzureMLPS.dll
    # Assume the default configuration file exists and properly set to point to the valid workspace.
    $scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
    $trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

    # Create 10 endpoints on the scoring web service
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        Write-Host ('adding endpoint ' + $endpontName + '...')
        Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
    }

    # Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
    $trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
    $submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
    $apiKey = $trainingSvcEp.PrimaryKey;
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
        $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
        Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
        Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
    }

    # Patch the 10 endpoints with respective .ilearner models
    $baseLoc = 'http://bostonmtc.blob.core.windows.net/'
    $sasToken = '?test'
    For ($i = 1; $i -le 10; $i++){
        $seq = $i.ToString().PadLeft(3, '0');
        $endpointName = 'rentalloc' + $seq;
        $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
        Write-Host ('Patching endpoint ' + $endpointName + '...');
        Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
    }
