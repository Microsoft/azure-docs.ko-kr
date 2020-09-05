---
title: Azure Machine Learning에 대 한 Git 통합
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 로컬 Git 리포지토리와 통합 하는 방법에 대해 알아봅니다. Git 리포지토리의 로컬 디렉터리에서 학습 실행을 제출할 때 리포지토리, 분기 및 현재 커밋에 대 한 정보는 실행의 일부로 추적 됩니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 154d28e4df9bad68f3c5e93208ccf62ba2721663
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144824"
---
# <a name="git-integration-for-azure-machine-learning"></a>Azure Machine Learning에 대 한 Git 통합

[Git](https://git-scm.com/) 은 프로젝트를 공유 하 고 공동 작업할 수 있는 인기 있는 버전 제어 시스템입니다. 

작업 추적을 위한 Git 리포지토리를 완벽 하 게 지원 Azure Machine Learning-공유 작업 영역 파일 시스템에 직접 리포지토리를 복제 하거나 로컬 워크스테이션에서 Git를 사용 하거나 CI/CD 파이프라인에서 Git를 사용할 수 있습니다.

Azure Machine Learning에 작업을 제출할 때 원본 파일이 로컬 git 리포지토리에 저장 된 경우 리포지토리에 대 한 정보는 학습 프로세스의 일부로 추적 됩니다.

Azure Machine Learning는 로컬 git 리포지토리의 정보를 추적 하므로 특정 중앙 리포지토리에 연결 되지 않습니다. GitHub, GitLab, Bitbucket, Azure DevOps 또는 다른 모든 git 호환 서비스에서 리포지토리를 복제할 수 있습니다.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>작업 영역 파일 시스템에 Git 리포지토리 복제
Azure Machine Learning는 작업 영역에 있는 모든 사용자에 게 공유 파일 시스템을 제공 합니다.
이 파일 공유에 Git 리포지토리를 복제 하려면 계산 인스턴스 & 만들어 터미널을 여는 것이 좋습니다.
터미널이 열리면 전체 Git 클라이언트에 액세스할 수 있고 Git CLI 환경을 통해 Git를 복제 하 고 사용할 수 있습니다.

사용자가 작업 분기에서 직접 충돌 하지 않도록 사용자 디렉터리에 리포지토리를 복제 하는 것이 좋습니다.

인증할 수 있는 Git 리포지토리 (GitHub, Azure Repos, BitBucket 등)를 복제할 수 있습니다.

Git CLI를 사용 하는 방법에 대 한 가이드는 [여기](https://guides.github.com/introduction/git-handbook/)에서 읽어 보세요.

## <a name="track-code-that-comes-from-git-repositories"></a>Git 리포지토리에서 제공 되는 코드 추적

Python SDK 또는 Machine Learning CLI에서 학습 실행을 제출 하면 모델을 학습 하는 데 필요한 파일이 작업 영역에 업로드 됩니다. `git`개발 환경에서이 명령을 사용할 수 있는 경우 업로드 프로세스에서이 명령을 사용 하 여 파일이 git 리포지토리에 저장 되었는지 확인 합니다. 그렇다면 git 리포지토리의 정보는 학습 실행의 일부로도 업로드 됩니다. 이 정보는 학습 실행을 위해 다음 속성에 저장 됩니다.

| 속성 | 값을 가져오는 데 사용 되는 Git 명령입니다. | 설명 |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | 리포지토리가 복제 된 URI입니다. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | 리포지토리가 복제 된 URI입니다. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | 실행을 제출한 활성 분기입니다. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | 실행을 제출한 활성 분기입니다. |
| `azureml.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출 된 코드의 커밋 해시입니다. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | 실행을 위해 제출 된 코드의 커밋 해시입니다. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`분기/커밋이 변경 되었으면이 고, 그렇지 않으면입니다. 그렇지 않으면 `false` 입니다. |

이 정보는 평가기, machine learning 파이프라인 또는 스크립트 실행을 사용 하는 실행을 위해 전송 됩니다.

개발 환경의 git 리포지토리에 학습 파일이 없거나 `git` 명령을 사용할 수 없는 경우 git 관련 정보는 추적 되지 않습니다.

> [!TIP]
> 개발 환경에서 git 명령을 사용할 수 있는지 확인 하려면 셸 세션, 명령 프롬프트, PowerShell 또는 기타 명령줄 인터페이스를 열고 다음 명령을 입력 합니다.
>
> ```
> git --version
> ```
>
> 이 설치 되어 있는 경우 경로에와 유사한 응답이 표시 `git version 2.4.1` 됩니다. 개발 환경에 git을 설치 하는 방법에 대 한 자세한 내용은 [git 웹 사이트](https://git-scm.com/)를 참조 하세요.

## <a name="view-the-logged-information"></a>기록 된 정보 보기

Git 정보는 학습 실행을 위한 속성에 저장 됩니다. Azure Portal, Python SDK 및 CLI를 사용 하 여이 정보를 볼 수 있습니다. 

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com)에서 작업 영역을 선택 합니다.
1. __실험__을 선택 하 고 실험 중 하나를 선택 합니다.
1. __실행 번호__ 열에서 실행 중 하나를 선택 합니다.
1. __로그__를 선택한 다음 __로그__ 및 __azureml__ 항목을 확장 합니다. __ ### \_ Azure__로 시작 하는 링크를 선택 합니다.

    ![포털의 # # #_azure 항목](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

기록 된 정보에는 다음 JSON과 유사한 텍스트가 포함 됩니다.

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

학습 실행을 제출한 후 [실행](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) 개체가 반환 됩니다. `properties`이 개체의 특성에는 로깅된 git 정보가 포함 됩니다. 예를 들어 다음 코드는 커밋 해시를 검색 합니다.

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

`az ml run`CLI 명령은 실행에서 속성을 검색 하는 데 사용할 수 있습니다. 예를 들어 다음 명령은 실험에서 마지막으로 실행 된 속성을 반환 합니다 `train-on-amlcompute` .

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

자세한 내용은 [az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) reference 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [모델 학습에 컴퓨팅 대상 사용](how-to-set-up-training-targets.md)
