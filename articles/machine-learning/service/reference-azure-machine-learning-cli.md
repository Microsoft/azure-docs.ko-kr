---
title: Machine Learning CLI 확장
titleSuffix: Azure Machine Learning service
description: Azure CLI용 Azure Machine Learning CLI 확장에 대해 알아봅니다. Azure CLI는 Azure 클라우드의 리소스로 작업할 수 있도록 해주는 플랫폼 간 명령줄 유틸리티입니다. Machine Learning 확장을 사용하면 Azure Machine Learning 서비스로 작업할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 2504ca9cb785529a9eab321c2521db46390632b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752992"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Azure Machine Learning 서비스용 CLI 확장 사용

Azure Machine Learning CLI는 Azure 플랫폼용 플랫폼 간 명령줄 인터페이스인 [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)에 대한 확장입니다. 이 확장은 명령줄에서 Azure Machine Learning 서비스로 작업하는 데 필요한 명령을 제공합니다. 이 확장을 사용하면 기계 학습 워크플로를 자동화하는 스크립트를 만들 수 있습니다. 예를 들어, 다음 작업을 수행하는 스크립트를 만들 수 있습니다.

+ 실험을 실행하여 Machine Learning 모델 생성

+ 고객이 사용할 수 있도록 Machine Learning 모델 등록

+ 기계 학습 모델 패키징, 배포 및 수명 주기 추적

CLI는 Azure Machine Learning SDK를 대체하는 것이 아닙니다. 다음과 같이 매개 변수가 많은 작업을 처리하는 데 최적화된 보완 도구입니다.

* 계산 리소스 만들기

* 매개 변수가 있는 실험 제출

* 모델 등록

* 이미지 만들기

* 서비스 배포

## <a name="prerequisites"></a>필수 조건


* CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

## <a name="install-the-extension"></a>확장 설치

Machine Learning CLI 확장을 설치하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension add -s https://azuremlsdktestpypi.blob.core.windows.net/wheels/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1/azure_cli_ml-1.0.10-py2.py3-none-any.whl --pip-extra-index-urls  https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
```

메시지가 표시되면 `y`를 선택하여 확장을 설치합니다.

확장이 설치되었는지 확인하려면 다음 명령을 사용하여 ML 관련 하위 명령 목록을 표시합니다.

```azurecli-interactive
az ml -h
```

> [!TIP]
> 확장을 업데이트하려면 확장을 __제거__한 후 __설치__해야 합니다. 이 방법으로 최신 버전을 설치합니다.

## <a name="remove-the-extension"></a>확장 제거

CLI 확장을 제거하려면 다음 명령을 사용합니다.

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>리소스 관리

다음 명령은 CLI를 사용하여 Azure Machine Learning에서 사용하는 리소스를 관리하는 방법을 보여 줍니다.


+ Azure Machine Learning 서비스 작업 영역을 만듭니다.

    ```azurecli-interactive
    az ml workspace create -n myworkspace -g myresourcegroup
    ```

+ 기본 작업 영역을 설정합니다.

    ```azurecli-interactive
    az configure --defaults aml_workspace=myworkspace group=myresourcegroup
    ```
    
+ AKS 클러스터 연결

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a name="experiments"></a>실험

다음 명령은 CLI를 사용하여 실험으로 작업하는 방법을 보여 줍니다.

* 실험을 제출하기 전에 프로젝트(실행 구성)에 연결합니다.

    ```azurecli-interactive
    az ml project attach --experiment-name myhistory
    ```

* 실험 실행을 시작합니다. 이 명령을 사용할 때는 실행 구성이 포함된 runconfig 파일의 이름을 지정합니다. 컴퓨팅 대상은 실행 구성을 사용하여 모델에 대한 학습 환경을 만듭니다. 이 예제에서는 `./aml_config/myrunconfig.runconfig` 파일에서 실행 구성이 로드됩니다.

    ```azurecli-interactive
    az ml run submit -c myrunconfig train.py
    ```

    runconfig 파일에 대한 자세한 내용은 [RunConfig](#runconfig) 섹션을 참조하세요.

* 제출된 실험 목록을 확인합니다.

    ```azurecli-interactive
    az ml history list
    ```

## <a name="model-registration-image-creation--deployment"></a>모델 등록, 이미지 만들기 및 배포

다음 명령은 학습된 모델을 등록한 후 프로덕션 서비스로 배포하는 방법을 보여 줍니다.

+ Azure Machine Learning에 모델을 등록합니다.

  ```azurecli-interactive
  az ml model register -n mymodel -m sklearn_regression_model.pkl
  ```

+ 기계 학습 모델 및 종속성이 포함된 이미지를 만듭니다. 

  ```azurecli-interactive
  az ml image create container -n myimage -r python -m mymodel:1 -f score.py -c myenv.yml
  ```

+ 컴퓨팅 대상에 이미지를 배포합니다.

  ```azurecli-interactive
  az ml service create aci -n myaciservice --image-id myimage:1
  ```

## <a id="runconfig"></a> Runconfig 파일

실행 구성은 모델 학습용 학습 환경을 구성하는 데 사용됩니다. 이 구성은 SDK를 사용하여 메모리에서 만들거나, runconfig 파일에서 로드할 수 있습니다.

runconfig 파일은 학습 환경의 구성을 설명하는 텍스트 문서입니다. 예를 들어 학습 스크립트의 이름과 모델 학습에 필요한 conda 종속성이 포함된 파일을 나열합니다.

`az ml project attach` 명령을 사용하여 프로젝트를 연결하면 Azure Machine Learning CLI에서 `docker.runconfig` 및 `local.runconfig`라는 두 개의 기본 `.runconfig` 파일을 만듭니다. 

[RunConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py) 클래스를 사용하여 실행 구성을 만드는 코드가 있는 경우 `save()` 메서드를 사용하여 `.runconfig` 파일에 유지할 수 있습니다.

다음은 `.runconfig` 파일 내용의 예입니다.

```text
# The script to run.
script: train.py
# The arguments to the script file.
arguments: []
# The name of the compute target to use for this run.
target: local
# Framework to execute inside. Allowed values are "Python" ,  "PySpark", "CNTK",  "TensorFlow", and "PyTorch".
framework: PySpark
# Communicator for the given framework. Allowed values are "None" ,  "ParameterServer", "OpenMpi", and "IntelMpi".
communicator: None
# Automatically prepare the run environment as part of the run itself.
autoPrepareEnvironment: true
# Maximum allowed duration for the run.
maxRunDurationSeconds:
# Number of nodes to use for running job.
nodeCount: 1
# Environment details.
environment:
# Environment variables set for the run.
  environmentVariables:
    EXAMPLE_ENV_VAR: EXAMPLE_VALUE
# Python details
  python:
# user_managed_dependencies=True indicates that the environmentwill be user managed. False indicates that AzureML willmanage the user environment.
    userManagedDependencies: false
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
  docker:
# Set True to perform this run inside a Docker container.
    enabled: true
# Base image used for Docker-based runs.
    baseImage: mcr.microsoft.com/azureml/base:0.2.4
# Set False if necessary to work around shared volume bugs.
    sharedVolumes: true
# Run with NVidia Docker extension to support GPUs.
    gpuSupport: false
# Extra arguments to the Docker run command.
    arguments: []
# Image registry that contains the base image.
    baseImageRegistry:
# DNS name or IP address of azure container registry(ACR)
      address:
# The username for ACR
      username:
# The password for ACR
      password:
# Spark details
  spark:
# List of spark repositories.
    repositories:
    - https://mmlspark.azureedge.net/maven
    packages:
    - group: com.microsoft.ml.spark
      artifact: mmlspark_2.11
      version: '0.12'
    precachePackages: true
# Databricks details
  databricks:
# List of maven libraries.
    mavenLibraries: []
# List of PyPi libraries
    pypiLibraries: []
# List of RCran libraries
    rcranLibraries: []
# List of JAR libraries
    jarLibraries: []
# List of Egg libraries
    eggLibraries: []
# History details.
history:
# Enable history tracking -- this allows status, logs, metrics, and outputs
# to be collected for a run.
  outputCollection: true
# whether to take snapshots for history.
  snapshotProject: true
# Spark configuration details.
spark:
  configuration:
    spark.app.name: Azure ML Experiment
    spark.yarn.maxAppAttempts: 1
# HDI details.
hdi:
# Yarn deploy mode. Options are cluster and client.
  yarnDeployMode: cluster
# Tensorflow details.
tensorflow:
# The number of worker tasks.
  workerCount: 1
# The number of parameter server tasks.
  parameterServerCount: 1
# Mpi details.
mpi:
# When using MPI, number of processes per node.
  processCountPerNode: 1
# data reference configuration details
dataReferences: {}
# Project share datastore reference.
sourceDirectoryDataStore:
# AmlCompute details.
amlcompute:
# VM size of the Cluster to be created.Allowed values are Azure vm sizes. The list of vm sizes is available in 'https://docs.microsoft.com/azure/cloud-services/cloud-services-sizes-specs
  vmSize:
# VM priority of the Cluster to be created.Allowed values are "dedicated" , "lowpriority".
  vmPriority:
# A bool that indicates if the cluster has to be retained after job completion.
  retainCluster: false
# Name of the cluster to be created. If not specified, runId will be used as cluster name.
  name:
# Maximum number of nodes in the AmlCompute cluster to be created. Minimum number of nodes will always be set to 0.
  clusterMaxNodeCount: 1
```
