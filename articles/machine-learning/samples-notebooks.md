---
title: 예제 Jupyter Notebook
titleSuffix: Azure Machine Learning
description: Jupyter Notebook 예제를 찾아 사용하여 SDK용 Azure Machine Learning Python을 검색합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 10cf06a829b991bfe15d7b24c4fc61d0fe22eaaf
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653718"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Jupyter Notebooks를 사용하여 Azure Machine Learning 검색

[예제 Azure Machine Learning Notebook 리포지토리](https://github.com/azure/machinelearningnotebooks)에는 최신 Azure Machine Learning Python SDK 샘플이 포함되어 있습니다. 이러한 Jupyter Notebook은 사용자가 SDK를 살펴보고 사용자 고유의 Machine Learning 프로젝트를 위한 모델로 사용할 수 있도록 설계되었습니다.

이 문서는 다음 환경에서 리포지토리에 액세스하는 방법을 보여 줍니다.

- [Azure Machine Learning 컴퓨팅 인스턴스](#notebookvm)
- [사용자 고유의 Notebook 서버 구현](#byo)
- [데이터 과학 가상 머신](#dsvm)

> [!NOTE]
> 리포지토리를 복제하면 **tutorials** 폴더에서 자습서 Notebook, **how-to-use-azureml** 폴더에서 기능별 Notebook을 확인할 수 있습니다.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Azure Machine Learning 컴퓨팅 인스턴스에 대한 샘플 가져오기

샘플을 시작하는 가장 쉬운 방법은 [자습서: 환경 및 작업 영역 설정](tutorial-1st-experiment-sdk-setup.md)을 완료하는 것입니다. 완료되면 전용 Notebook 서버에 SDK 및 샘플 리포지토리가 사전 로드됩니다. 다운로드 또는 설치가 필요 없습니다.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Notebook 서버에서 샘플 가져오기

로컬 개발용으로 사용자 고유의 Notebook 서버를 구현하려는 경우 아래 단계를 따르세요.

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

이러한 지침은 빠른 시작 및 자습서 Notebook에 필요한 기본 SDK 패키지를 설치합니다. 다른 샘플 Notebook에서는 추가 구성 요소를 설치해야 할 수 있습니다. 자세한 내용은 [Python용 Azure Machine Learning SDK 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install)를 참조하세요.

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>DSVM에서 샘플 가져오기

DSVM(Data Science Virtual Machine)은 데이터 과학 수행을 위해 특별히 구축된 사용자 지정 VM 이미지입니다. [DSVM을 만드는](how-to-configure-environment.md#dsvm) 경우 SDK 및 Notebook 서버가 사용자를 위해 설치 및 구성됩니다. 그러나 작업 영역을 만들고 샘플 리포지토리는 복제해야 합니다.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>다음 단계

[샘플 Notebook](https://github.com/Azure/MachineLearningNotebooks)을 살펴보고 Azure Machine Learning에서 제공하는 기능을 확인합니다.

GitHub 샘플 프로젝트 및 예제는 다음 리포지토리를 참조하세요.
+ [Microsoft/MLOps](https://github.com/Microsoft/MLOps)
+ [Microsoft/MLOpsPython](https://github.com/microsoft/MLOpsPython)

이러한 자습서를 시도합니다.

- [MNIST를 사용하여 이미지 분류 모델 학습 및 배포](tutorial-train-models-with-aml.md)

- [데이터를 준비하고 자동화된 기계 학습을 사용하여 NYC 택시 데이터 세트로 회귀 모델 학습](tutorial-auto-train-models.md)
