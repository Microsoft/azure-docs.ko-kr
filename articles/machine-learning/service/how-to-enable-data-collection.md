---
title: 프로덕션 모델에서 데이터 수집
titleSuffix: Azure Machine Learning service
description: Azure Blob Storage에서 Azure Machine Learning 입력 모델 데이터를 수집하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 12/3/2018
ms.custom: seodec18
ms.openlocfilehash: a127a211157edb0b26d0495bc2ed05dd79323111
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60820404"
---
# <a name="collect-data-for-models-in-production"></a>프로덕션 환경에서 모델용 데이터 수집

이 문서에서는 AKS(Azure Kubernetes 클러스터)에 배포한 Azure Machine Learning Services에서 Azure Blob Storage로 입력 모델 데이터를 수집하는 방법을 확인할 수 있습니다. 

데이터를 수집하도록 설정하면 이렇게 수집한 데이터는 다음과 같은 용도로 활용할 수 있습니다.
* 프로덕션 데이터가 모델에 들어올 때 데이터 드리프트 모니터링

* 모델을 다시 학습시키거나 최적화할 시기를 더 효율적으로 결정

* 수집한 데이터로 모델 다시 학습시키기

## <a name="what-is-collected-and-where-does-it-go"></a>수집되는 데이터 및 데이터가 이동하는 위치

다음 데이터를 수집할 수 있습니다.
* AKS(Azure Kubernetes 클러스터)에 배포된 웹 서비스의 모델 **입력** 데이터(음성, 이미지 및 비디오는 수집되지 **않음**) 
  
* 프로덕션 입력 데이터를 사용하는 모델 예측

> [!Note]
> 이 데이터의 사전 집계 또는 사전 계산 기능은 현재 서비스에서 제공되지 않습니다.   

출력은 Azure Blob에 저장됩니다. 데이터가 Azure Blob에 추가되므로 원하는 도구를 선택하여 분석을 수행할 수 있습니다. 

Blob에서 출력 데이터의 경로 형식은 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

## <a name="prerequisites"></a>필수 조건

- Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 바로 [Azure Machine Learning Service의 체험 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.

- Azure Machine Learning 서비스 작업 영역, 스크립트가 포함된 로컬 디렉터리 및 Python용 Azure Machine Learning SDK가 설치되어 있어야 합니다. [개발 환경 구성 방법](how-to-configure-environment.md) 문서를 사용하여 이러한 필수 구성 요소를 충족하는 방법을 알아보세요.

- AKS(Azure Kubernetes Service)에 배포할 학습된 Machine Learning 모델. 이러한 모델이 없으면 [이미지 분류 모델 학습](tutorial-train-models-with-aml.md) 자습서를 참조하세요.

- Azure Kubernetes Service 클러스터입니다. 클러스터를 만들고 배포하는 방법에 대한 자세한 내용은 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서를 참조하세요.

- [환경을 설정](how-to-configure-environment.md)하고 [모니터링 SDK](https://aka.ms/aml-monitoring-sdk)를 설치합니다.

## <a name="enable-data-collection"></a>데이터 컬렉션 활성화
Azure Machine Learning 서비스 또는 기타 도구를 통해 모듈을 배포하는지에 관계없이 데이터 수집을 사용하도록 설정할 수 있습니다. 

데이터 수집을 사용하도록 설정하려면 다음 단계를 수행해야 합니다.

1. 점수 매기기 파일을 엽니다. 

1. 파일 맨 위에 [다음 코드](https://aka.ms/aml-monitoring-sdk)를 추가합니다.

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. `init()` 함수에서 데이터 수집 변수를 선언합니다.

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId*는 선택적 매개 변수이므로 모델에 필요하지 않으면 설정하지 않아도 됩니다. correlationId가 있으면 다른 데이터와의 매핑을 더 쉽게 수행할 수 있습니다. (예를 들면 다음과 같습니다: LoanNumber, CustomerId 등입니다.)
    
    *Identifier*는 나중에 Blob에서 폴더 구조를 작성하는 데 사용되며 “raw” 데이터와 “processed” 데이터를 분리하는 데 사용할 수 있습니다.

3.  `run(input_df)` 함수에 다음 코드 줄을 추가합니다.

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. 데이터 수집은 AKS에서 서비스를 배포할 때 **true**로 자동 설정되지 **않습니다** 따라서 다음과 같이 구성 파일을 업데이트해야 합니다. 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    이 구성을 변경하여 서비스 모니터링용 AppInsights도 설정할 수 있습니다.
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. 새 이미지를 만들고 서비스를 배포하려면 [배포 방법 및 위치](how-to-deploy-and-where.md) 문서를 참조하세요.


**환경 파일** 및 **점수 매기기 파일**의 종속성이 설치된 서비스가 이미 있으면 다음 단계를 수행하여 데이터 수집을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 작업 영역을 엽니다.

1. **배포** -> **서비스 선택** -> **편집**으로 이동합니다.

   ![서비스 편집](media/how-to-enable-data-collection/EditService.PNG)

1. **고급 설정**에서 **모델 데이터 컬렉션 사용** 선택을 취소합니다. 

    [![데이터 수집 선택](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   이 창에서 “Appinsights 진단 사용”을 선택하여 서비스의 상태를 추적할 수도 있습니다.  

1. **업데이트**를 선택하여 변경 내용을 적용합니다.


## <a name="disable-data-collection"></a>데이터 수집 비활성화
데이터 수집은 언제든지 중지할 수 있습니다. 데이터 수집을 사용하지 않도록 설정하려면 Python 코드 또는 Azure Portal을 사용합니다.

+ 옵션 1 - Azure Portal에서 데이터 수집을 사용하지 않도록 설정 
  1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

  1. 작업 영역을 엽니다.

  1. **배포** -> **서비스 선택** -> **편집**으로 이동합니다.

     [![편집 옵션](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. **고급 설정**에서 **모델 데이터 컬렉션 사용** 선택을 취소합니다. 

     [![데이터 수집 선택 취소](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. **업데이트**를 선택하여 변경 내용을 적용합니다.

+ 옵션 2 - Python을 사용하여 데이터 수집을 사용하지 않도록 설정

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>데이터 유효성 검사 및 분석
선호하는 도구를 선택하여 Azure Blob에 수집된 데이터를 분석할 수 있습니다. 

Blob의 데이터에 빠르게 액세스하려면 다음을 수행합니다.
1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. 작업 영역을 엽니다.
1. **Storage**를 클릭합니다.

    [![Storage](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Blob의 출력 데이터 경로는 다음 구문을 따릅니다.

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Power BI를 통해 모델 데이터 분석

1. [PowerBi Desktop](https://www.powerbi.com)을 다운로드하여 엽니다.

1. **데이터 가져오기**를 선택하고 [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)를 클릭합니다.

    [![PBI Blob 설정](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. 스토리지 계정 이름을 추가하고 스토리지 키를 입력합니다. Blob의 **설정** >> 액세스 키에서 이 정보를 찾을 수 있습니다. 

1. **modeldata** 컨테이너를 선택하고 **편집**을 클릭합니다. 

    [![PBI 탐색기](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. 쿼리 편집기에서 “이름” 열 아래를 클릭하고 스토리지 계정 1을 추가합니다. 필터에 대한 경로를 모델링합니다. 참고: 특정 연도 또는 특정 월의 파일만 보려면 필터 경로를 확장하기만 하면 됩니다. 예를 들어, /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/identifier>/year>/3에서 3월 데이터를 봅니다.

1. **이름**을 기준으로 관련된 데이터를 필터링합니다. **예측** 및 **입력**을 저장한 경우 각각에 대해 쿼리를 만들어야 합니다.

1. 파일을 결합하려면 **콘텐츠** 열 옆에 있는 양방향 화살표를 클릭합니다. 

    [![PBI 콘텐츠](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. [확인]을 클릭하면 데이터가 미리 로드됩니다.

    [![pbiCombine](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. 이제 **닫기 및 적용**을 클릭할 수 있습니다.

1.  입력 및 예측을 추가한 경우 자동으로 **RequestId**에 따라 테이블 상관 관계가 설정됩니다.

1. 모델 데이터에 대한 사용자 지정 보고서 빌드를 시작합니다.


### <a name="analyzing-model-data-using-databricks"></a>Databricks를 사용하여 모델 데이터 분석

1. [Databricks 작업 영역](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)을 만듭니다. 

1. Databricks 작업 영역으로 이동합니다. 

1. Databricks 작업 영역에서 **데이터 업로드**를 선택합니다.

    [![DB 업로드](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. 새 테이블을 만들고 **기타 데이터 원본** -> Azure Blob Storage -> Create Table in Notebook(Notebook에서 테이블 만들기)을 선택합니다.

    [![DB 테이블](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. 데이터 위치를 업데이트합니다. 다음은 예제입니다.

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![DBsetup](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. 데이터를 보고 분석하려면 템플릿의 단계를 따릅니다. 

## <a name="example-notebook"></a>예제 Notebook

[how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) Notebook은 이 문서의 개념을 보여 줍니다.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
