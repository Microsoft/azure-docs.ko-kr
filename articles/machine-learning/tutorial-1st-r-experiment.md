---
title: '자습서: R을 사용하여 기계 학습 모델 만들기(미리 보기)'
titleSuffix: Azure Machine Learning
description: 이 자습서에서는 Azure Machine Learning R SDK를 사용하여 자동차 사고로 인한 사망 확률을 예측하는 로지스틱 회귀 모델을 만듭니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: bb2a7d8ef55e993726b185e5652c8dff9e96b23e
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056366"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>자습서: R을 사용하여 기계 학습 모델 만들기(미리 보기)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

> [!IMPORTANT]
> Azure Machine Learning R SDK는 현재 공개 미리 보기로 제공됩니다.
> 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 자습서에서는 Azure Machine Learning R SDK(미리 보기)를 사용하여 자동차 사고로 인한 사망 확률을 예측하는 로지스틱 회귀 모델을 만듭니다. Azure Machine Learning 클라우드 리소스가 R과 함께 작동하여 모델 교육 및 배포를 위한 확장 가능한 환경을 제공하는 방법을 살펴봅니다.  

이 자습서에서는 다음 작업을 수행합니다.
> [!div class="checklist"]
> * Azure Machine Learning 작업 영역 만들기
> * 작업 영역에서 이 자습서를 실행하는 데 필요한 파일로 Notebook 폴더 복제
> * 작업 영역에서 RStudio 열기
> * 데이터 로드 및 학습 준비
> * 원격 학습에 사용할 수 있도록 데이터 저장소에 데이터 업로드
> * 원격으로 모델을 학습하는 컴퓨팅 리소스 만들기
> * 사망 확률을 예측하는 `caret` 모델 학습
> * 예측 엔드포인트 배포
> * R에서 모델 테스트

Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 사용해 보세요.


## <a name="create-a-workspace"></a>작업 영역 만들기

Azure Machine Learning 작업 영역은 기계 학습 모델을 실험하고, 학습시키고, 배포하는 데 사용하는 클라우드의 기본 리소스입니다. Azure 구독 및 리소스 그룹을 서비스에서 사용하기 쉬운 개체에 연결합니다. 

Azure 리소스를 관리하기 위한 웹 기반 콘솔인 Azure Portal을 통해 작업 영역을 만듭니다. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> **작업 영역** 및 **구독**을 적어 둡니다. 올바른 작업 영역에 실험을 만들려면 이 정보가 필요합니다. 


## <a name="clone-a-notebook-folder"></a><a name="azure"></a>Notebook 폴더 복제

이 예에서는 작업 영역의 클라우드 Notebook 서버를 설치하지 않는 미리 구성된 환경으로 사용합니다. 환경, 패키지 및 종속성을 제어하려면 [사용자 고유의 환경](https://azure.github.io/azureml-sdk-for-r/articles/installation.html)을 사용합니다.

모든 기술 수준의 데이터 과학 전문가용 데이터 과학 시나리오를 수행하기 위한 기계 학습 도구를 포함하는 통합 인터페이스인 Azure Machine Learning Studio에서 다음 실험 설정 및 실행 단계를 완료합니다.

1. [Azure Machine Learning Studio](https://ml.azure.com/)에 로그인합니다.

1. 해당 구독과 직접 만든 작업 영역을 선택합니다.

1. 왼쪽에서 **Notebook**을 선택합니다.

1. **Samples** 폴더를 엽니다.

1. **R** 폴더를 엽니다.

1. 버전 번호가 있는 폴더를 엽니다.  이 번호는 R SDK의 현재 릴리스를 나타냅니다.

1. **vignettes** 폴더의 오른쪽에 있는 **"..."** 를 선택한 다음, **복제**를 선택합니다.

    ![폴더 복제](media/tutorial-1st-r-experiment/clone-folder.png)

1. 작업 영역에 액세스하는 각 사용자를 표시하는 폴더 목록이 표시됩니다.  **vignettes** 폴더를 복제할 폴더를 선택합니다.

## <a name="open-rstudio"></a><a name="open"></a>RStudio 열기

컴퓨팅 인스턴스 또는 Notebook VM에서 RStudio를 사용하여 이 자습서를 실행합니다.  

1. 왼쪽에서 **Compute**를 선택합니다.

1. 컴퓨팅 리소스가 아직 없는 경우 추가합니다.

1. 컴퓨팅이 실행되면 **RStudio** 링크를 사용하여 RStudio를 엽니다.

1. RStudio에서 *vignettes* 폴더는 오른쪽 아래에 있는 **파일** 섹션의 *사용자*에서 몇 개 수준 아래입니다.  *vignettes* 아래에서 *train-and-deploy-to-aci* 폴더를 선택하여 이 자습서에 필요한 파일을 찾습니다.

> [!Important]
> 이 문서의 나머지 부분에는 *train-and-deploy-to-aci.Rmd* 파일에 표시되는 것과 동일한 콘텐츠가 포함되어 있습니다. RMarkdown 사용 경험이 있는 경우 해당 파일의 코드를 자유롭게 사용할 수 있습니다.  또는 해당 프로그램에서 또는 이 문서에서 R 스크립트나 명령줄로 코드 조각을 복사한 후 붙여 넣을 수 있습니다.  


## <a name="set-up-your-development-environment"></a>개발 환경 설정
이 자습서의 개발 작업용 설정에는 다음 작업이 포함됩니다.

* 필요한 패키지를 설치합니다.
* 컴퓨팅 인스턴스에서 원격 리소스와 통신할 수 있도록 작업 영역에 연결합니다.
* 실행을 추적하는 실험을 만듭니다.
* 학습에 사용할 원격 컴퓨팅 대상을 만듭니다.

### <a name="install-required-packages"></a>필요한 패키지를 설치합니다.
컴퓨팅 인스턴스에는 이미 CRAN의 R SDK 최신 버전이 설치되어 있습니다. 최신 버그 수정을 선택하는 대신 GitHub에서 개발 버전을 설치하려면 다음을 실행하세요.
    
```R
remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
azuremlsdk::install_azureml()
```

> [!WARNING]
> 설치 프로세스 중에 "`Would you like to install Miniconda? [Y/n]:`" 메시지가 표시되면 컴퓨팅 인스턴스에 이미 Anaconda가 설치되어 있고 Miniconda 설치가 필요하지 않으므로 "`n`"으로 응답하세요.

이제 **azuremlsdk** 패키지를 가져옵니다.

```R
library(azuremlsdk)
```

학습 및 점수 매기기 스크립트(`accidents.R` 및 `accident_predict.R`)에는 몇 가지 추가 종속성이 있습니다. 이러한 스크립트를 로컬로 실행하려는 경우에는 필요한 패키지도 있는지 확인합니다.

### <a name="load-your-workspace"></a>작업 영역 로드
기존 작업 영역에서 작업 영역 개체를 인스턴스화합니다. 다음 코드는 **config.json** 파일에서 작업 영역 정보를 로드합니다. [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html)를 사용하여 작업 영역을 검색할 수도 있습니다.

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>실험 만들기
Azure ML 실험은 일반적으로 동일한 학습 스크립트에서 실행 그룹을 추적합니다. 사고 데이터의 caret 모델을 학습하기 위해 실행을 추적하는 실험을 만듭니다.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>컴퓨팅 대상 만들기
데이터 과학자는 관리형 서비스인 AmlCompute(Azure Machine Learning 컴퓨팅)를 사용하여 Azure 가상 머신 클러스터에서 기계 학습 모델을 학습할 수 있습니다. 예를 들어 GPU가 지원되는 VM이 있습니다. 이 자습서에서는 학습 환경으로 단일 노드 AmlCompute 클러스터를 만듭니다. 아래 코드는 작업 영역에 아직 컴퓨팅 클러스터가 없으면 새로 만듭니다.

컴퓨팅 클러스터가 아직 없는 경우 프로비저닝될 때까지 몇 분 정도 기다려야 할 수 있습니다.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>학습을 위한 데이터 준비
이 자습서에서는 미국 [미국 도로교통안전국](https://cdan.nhtsa.gov/tsftables/tsfar.htm)의 데이터를 사용합니다. ([Mary C. Meyer 및 Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)에게 감사드립니다.)
이 데이터 세트에는 사망 확률을 예측하는 데 사용할 수 있는 변수와 미국에서 발생한 25,000건 이상의 자동차 사고 데이터가 포함됩니다. 먼저 데이터를 R로 가져온 다음, 분석을 위해 새 데이터 프레임 `accidents`로 변환하고 `Rdata` 파일로 내보냅니다.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>데이터 저장소에 데이터 업로드
원격 학습 환경에서 액세스할 수 있도록 클라우드에 데이터를 업로드합니다. 각 Azure Machine Learning 작업 영역에는 작업 영역에 연결된 스토리지 계정에 프로비저닝된 Azure Blob 컨테이너에 대한 연결 정보를 저장하는 기본 데이터 저장소가 제공됩니다. 다음 코드는 위에서 만든 사고 데이터를 해당 데이터 저장소에 업로드합니다.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>모델 학습

이 자습서에서는 원격 컴퓨팅 클러스터를 사용하여 업로드된 데이터에 로지스틱 회귀 모델을 맞춥니다. 작업을 제출하는 방법은 아래와 같습니다.

* 학습 스크립트 준비
* 추정기 만들기
* 작업 제출

### <a name="prepare-the-training-script"></a>학습 스크립트 준비
`accidents.R`이라는 학습 스크립트가 이 자습서와 동일한 디렉터리에 제공되었습니다. 학습용 Azure Machine Learning을 활용하기 위해 수행된 **학습 스크립트 내**에서 다음 작업 세부 정보를 확인하세요.

* 학습 스크립트에서 인수 `-d`를 사용하여 학습 데이터가 있는 디렉터리를 찾습니다. 나중에 작업을 정의하고 제출할 때 이 인수에 대한 데이터 저장소를 가리킵니다. Azure ML은 학습 작업을 위해 원격 클러스터에 스토리지 폴더를 탑재합니다.
* 학습 스크립트는 `log_metric_to_run()`을 사용하여 Azure ML의 실행 기록에 대한 메트릭으로 최종 정확도를 로깅합니다. Azure ML SDK는 학습 실행 중 다양한 메트릭을 로깅하는 일련의 로깅 API를 제공합니다. 이러한 메트릭은 실험 실행 기록에 기록되고 유지됩니다. 그런 다음, 언제든지 메트릭에 액세스하거나 [studio](https://ml.azure.com)의 실행 세부 정보 페이지에서 볼 수 있습니다. 로깅 메서드 `log_*()`의 전체 세트에 대해서는 [참조](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation)를 참조하세요.
* 학습 스크립트는 **outputs**라는 디렉터리에 모델을 저장합니다. `./outputs` 폴더는 Azure ML에서 특별히 취급됩니다. 학습하는 동안 `./outputs`에 기록된 파일은 Azure ML에 의해 실행 기록에 자동으로 업로드되고 아티팩트로 유지됩니다. `./outputs`에 학습된 모델을 저장하여 실행이 끝난 후에도 모델 파일을 액세스하고 검색할 수 있으며, 원격 교육 환경에 더 이상 액세스할 수 없습니다.

### <a name="create-an-estimator"></a>추정기 만들기

Azure ML 추정기는 컴퓨팅 대상에 대해 학습 스크립트를 실행하는 데 필요한 실행 구성 정보를 캡슐화합니다. Azure ML 실행은 지정된 컴퓨팅 대상에서 컨테이너화된 작업으로 실행됩니다. 기본적으로 학습 작업용으로 빌드된 Docker 이미지는 R, Azure ML SDK 및 일반적으로 사용되는 R 패키지 세트를 포함합니다. 여기에 포함된 기본 패키지의 전체 목록을 참조하세요.

추정기를 만들려면 다음을 정의합니다.

* 학습에 필요한 스크립트를 포함하는 디렉터리(`source_directory`). 이 디렉터리의 모든 파일은 실행을 위해 클러스터 노드로 업로드됩니다. 이 디렉터리에는 학습 스크립트와 필요한 추가 스크립트가 포함되어야 합니다.
* 실행될 학습 스크립트(`entry_script`).
* 컴퓨팅 대상(`compute_target`). 이 경우에는 이전에 만든 AmlCompute 클러스터가 필요합니다.
* 학습 스크립트에서 필요한 매개 변수(`script_params`). Azure ML은 `Rscript`를 사용하여 학습 스크립트를 명령줄 스크립트로 실행합니다. 이 자습서에서는 스크립트에 대한 단일 인수, 즉 `ds$path(target_path)`를 사용하여 액세스할 수 있는 데이터 디렉터리 탑재 지점을 지정합니다.
* 학습에 필요한 모든 환경 종속성. 학습용으로 작성된 기본 Docker 이미지는 학습 스크립트에 필요한 3개의 패키지(`caret`, `e1071` 및 `optparse`)를 이미 포함하고 있습니다.  따라서 추가 정보를 지정할 필요가 없습니다. 기본적으로 포함되지 않은 R 패키지를 사용하는 경우 추정기의 `cran_packages` 매개 변수를 사용하여 CRAN 패키지를 더 추가합니다. 구성 가능 옵션의 전체 세트에 대해서는 [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) 참조를 참조하세요.

```R
est <- estimator(source_directory = ".",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>원격 클러스터에서 작업 제출

마지막으로 클러스터에서 실행할 작업을 제출합니다. `submit_experiment()`는 실행과 인터페이스로 연결하는 데 사용하는 Run 개체를 반환합니다. 전체적으로 첫 번째 실행은 **약 10분**이 걸립니다. 그러나 스크립트 종속성이 변경되지 않는 한, 나중에 실행하기 위해 동일한 Docker 이미지가 다시 사용됩니다.  이 경우 이미지가 캐시되므로 컨테이너 시작 시간은 훨씬 더 빨라집니다.

```R
run <- submit_experiment(exp, est)
```

RStudio Viewer에서 실행 세부 정보를 볼 수 있습니다. 제공된 "웹 보기" 링크를 클릭하면 Azure Machine Learning Studio로 이동되며 해당 UI에서 실행을 모니터링할 수 있습니다.

```R
view_run_details(run)
```

모델 학습은 백그라운드에서 수행됩니다. 더 많은 코드를 실행하기 전에 모델 학습이 완료될 때까지 기다립니다.

```R
wait_for_run_completion(run, show_output = TRUE)
```

사용자와 작업 영역에 대한 액세스 권한이 있는 동료들은 여러 실험을 동시에 제출할 수 있으며, Azure ML은 컴퓨팅 클러스터에서 작업을 예약합니다. 여러 노드로 자동으로 확장되었다가 큐에 컴퓨팅 작업이 더 이상 없으면 다시 축소되도록 클러스터를 구성할 수도 있습니다. 이 구성은 팀에서 컴퓨팅 리소스를 공유하는 비용 효율적인 방법입니다.

## <a name="retrieve-training-results"></a>학습 결과 검색
모델의 학습을 마친 후에는 로깅된 메트릭과 최종 학습된 모델을 포함하여 실행 기록에 유지된 작업의 아티팩트에 액세스할 수 있습니다.

### <a name="get-the-logged-metrics"></a>로깅된 메트릭 가져오기
학습 스크립트 `accidents.R`에서 학습 데이터의 예측 정확도에 해당하는 모델의 메트릭을 로깅했습니다. [Studio](https://ml.azure.com)에서 메트릭을 보거나, 다음과 같이 로컬 세션에 R 목록으로 추출할 수 있습니다.

```R
metrics <- get_run_metrics(run)
metrics
```

여러 실험을 실행한 경우(예를 들어, 다른 변수, 알고리즘 또는 하이퍼 매개 변수 사용) 각 실행의 메트릭을 사용하여 프로덕션 환경에서 사용할 모델을 비교하고 선택할 수 있습니다.

### <a name="get-the-trained-model"></a>학습된 모델 가져오기
학습된 모델을 검색하고 결과를 로컬 R 세션에서 볼 수 있습니다. 다음 코드는 모델 파일이 포함된 `./outputs` 디렉터리의 콘텐츠를 다운로드합니다.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

예상되는 사망 확률 증가에 영향을 미치는 다음과 같은 몇 가지 요소가 표시됩니다.

* 높은 충격을 발생하는 속도 
* 남성 운전자
* 고령 동승자
* 승객

다음과 같은 경우 사망 확률이 낮아지는 것을 확인할 수 있습니다.

* 에어백 있음
* 안전 벨트 착용
* 전면 충돌 

제조업체의 차량 연식은 큰 영향을 미치지 않습니다.

이 모델을 사용하여 새 예측을 만들 수 있습니다.

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>웹 서비스로 배포

이 모델을 사용하여 충돌에 따른 사망 위험을 예측할 수 있습니다. Azure ML을 사용하여 모델을 예측 서비스로 배포합니다. 이 자습서에서는 ACI([Azure Container Instances](https://docs.microsoft.com/azure/container-instances/))에서 웹 서비스를 배포합니다.

### <a name="register-the-model"></a>모델 등록

먼저 작업 영역에 다운로드한 모델을 [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html)을 사용하여 등록합니다. 등록된 모델은 어떤 파일 모음도 될 수 있으며, 이 경우에는 R 모델 개체로 충분합니다. Azure ML은 배포를 위해 등록된 모델을 사용합니다.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>유추 종속성 정의
모델용 웹 서비스를 만들려면 먼저 입력 변수 값(JSON 형식)으로 사용하고 모델에서 예측을 출력하는 R 스크립트인 점수 매기기 스크립트(`entry_script`)를 만들어야 합니다. 이 자습서에서는 제공된 점수 매기기 파일 `accident_predict.R`을 사용합니다. 점수 매기기 스크립트는 모델을 로드하고 해당 모델을 사용해서 입력 데이터를 기준으로 예측을 수행하는 함수를 반환하는 `init()` 메서드를 포함해야 합니다. 자세한 내용은 [설명서](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details)를 참조하세요.

그런 다음, 스크립트의 패키지 종속성에 대한 Azure ML **환경**을 정의합니다. 환경을 사용하여 스크립트를 실행하는 데 필요한 R 패키지(CRAN 또는 다른 위치에서 가져옴)를 지정합니다. 스크립트가 동작 수정을 위해 참조할 수 있는 환경 변수 값도 제공할 수 있습니다. 기본적으로 Azure ML은 학습을 위해 추정기와 함께 사용되는 동일한 기본 Docker 이미지를 빌드합니다. 이 자습서에서는 특수한 환경을 요구하지 않으므로 특별한 특성이 없는 환경을 만듭니다.

```R
r_env <- r_environment(name = "basic_env")
```

대신, 배포를 위해 자체 Docker 이미지를 사용하려면 `custom_docker_image` 매개 변수를 지정합니다. 환경 정의를 위한 구성 가능 옵션의 전체 세트에 대해서는 [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) 참조를 참조하세요.

이제 점수 매기기 스크립트 및 환경 종속성을 캡슐화하는 **유추 구성**을 만드는 데 필요한 모든 환경이 구현되었습니다.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>ACI에 배포
이 자습서에서는 ACI에 서비스를 배포합니다. 이 코드는 단일 컨테이너를 프로비저닝하여 테스트 및 간단한 부하에 적합한 인바운드 요청에 응답합니다. 구성 가능한 추가 옵션에 대해서는 [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html)를 참조하세요. (프로덕션 규모 배포의 경우 [Azure Kubernetes Service에 배포](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html)할 수도 있습니다.)

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

이제 모델을 웹 서비스로 배포합니다. 배포에는 **몇 분 정도 걸릴 수 있습니다**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>배포된 서비스 테스트

이제 모델을 서비스로 배포했으므로 [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html)를 사용하여 R에서 서비스를 테스트할 수 있습니다.  예측할 새 데이터 세트를 제공하고, JSON으로 변환한 다음, 서비스로 보냅니다.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

REST 클라이언트 호출을 수락하는 웹 서비스의 HTTP 엔드포인트도 가져올 수 있습니다. 이 엔드포인트는 웹 서비스를 테스트하거나 애플리케이션에 통합하려는 모든 사용자와 공유할 수 있습니다.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 리소스는 삭제합니다. 계속 사용하려는 리소스를 삭제하지 마세요. 

다음과 같이 웹 서비스를 삭제합니다.
```R
delete_webservice(aci_service)
```

다음과 같이 등록된 모델을 삭제합니다.
```R
delete_model(model)
```

다음과 같이 컴퓨팅 클러스터를 삭제합니다.
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>모든 항목 삭제

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

또한 리소스 그룹을 유지하면서 단일 작업 영역을 삭제할 수도 있습니다. 작업 영역 속성을 표시하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* 지금까지 R에서 첫 번째 Azure Machine Learning 실험을 완료했으므로 [R용 Azure Machine Learning SDK](https://azure.github.io/azureml-sdk-for-r/index.html)에 대해 자세히 알아보세요.

* 다른 *vignettes* 폴더의 예제에서 R을 사용한 Azure Machine Learning에 대해 자세히 알아봅니다.
