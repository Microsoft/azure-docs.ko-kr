---
title: 방화벽 사용
titleSuffix: Azure Machine Learning
description: Azure 방화벽을 사용 하 여 Azure Machine Learning 작업 영역에 대 한 액세스를 제어 합니다. Azure Machine Learning가 제대로 작동 하도록 방화벽을 통해 허용 해야 하는 호스트에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/17/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 581feff516e0f0cd820c94290d4aaa729cc4d3a4
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88889943"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>방화벽 뒤의 작업 영역을 사용 하 여 Azure Machine Learning

이 문서에서는 Azure Machine Learning 작업 영역 및 공용 인터넷에 대 한 액세스를 제어 하도록 Azure 방화벽을 구성 하는 방법에 대해 알아봅니다.   Azure Machine Learning 보안 설정에 대 한 자세한 내용은 [Azure Machine Learning 엔터프라이즈 보안](concept-enterprise-security.md) 을 참조 하세요.

이 문서의 정보는 [Azure 방화벽](../firewall/tutorial-firewall-deploy-portal.md)사용을 기반으로 하지만 다른 방화벽 제품과 함께 사용할 수 있습니다. 방화벽을 통한 통신을 허용 하는 방법에 대 한 질문이 있는 경우 사용 중인 방화벽에 대 한 설명서를 참조 하세요.

## <a name="application-rules"></a>애플리케이션 규칙

방화벽에서이 문서의 주소로 들어오고 나가는 트래픽을 허용 하는 _응용 프로그램 규칙_ 을 만듭니다.

> [!TIP]
> 네트워크 규칙을 추가 하는 경우 __프로토콜__ 을 any로 설정 하 고 포트를로 설정 `*` 합니다.
>
> Azure 방화벽 구성에 대 한 자세한 내용은 [Azure 방화벽 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)을 참조 하세요.

## <a name="microsoft-hosts"></a>Microsoft 호스트

올바르게 구성 되지 않은 경우 방화벽에서 작업 영역을 사용 하 여 문제를 일으킬 수 있습니다. Azure Machine Learning 작업 영역에서 사용 되는 다양 한 호스트 이름이 있습니다.

이 섹션의 호스트는 Microsoft에서 소유 하며 작업 영역의 적절 한 기능을 제공 하는 데 필요한 서비스를 제공 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **\*. batchai.core.windows.net** | 클러스터 학습 |
| **ml.azure.com** | Azure Machine Learning Studio |
| **default.exp-tas.com** | Azure Machine Learning studio에서 사용 |
| **\*. azureml.ms** | Azure Machine Learning Api에서 사용 |
| **\*. experiments.azureml.net** | Azure Machine Learning에서 실행 되는 실험에서 사용 |
| **\*. modelmanagement.azureml.net** | 모델을 등록 하 고 배포 하는 데 사용 됩니다.|
| **mlworkspace.azure.ai** | 작업 영역을 볼 때 Azure Portal에서 사용 됩니다. |
| **\*. aether.ms** | Azure Machine Learning 파이프라인을 실행할 때 사용 됩니다. |
| **\*. instances.azureml.net** | Azure Machine Learning 계산 인스턴스 |
| **\*. instances.azureml.ms** | 작업 영역에서 개인 링크를 사용 하도록 설정한 경우 계산 인스턴스 Azure Machine Learning |
| **windows.net** | Azure Blob Storage |
| **vault.azure.net** | Azure Key Vault |
| **azurecr.io** | Azure Container Registry |
| **mcr.microsoft.com** | 기본 docker 이미지용 Microsoft Container Registry |
| **your-acr-server-name.azurecr.io** | Azure Container Registry 가상 네트워크 뒤에 있는 경우에만 필요 합니다. 이 구성에서는 Microsoft 환경에서 구독의 ACR 인스턴스로 개인 링크를 만듭니다. Azure Machine Learning 작업 영역에 ACR 서버 이름을 사용 합니다. |
| **\*. notebooks.azure.net** | Azure Machine Learning studio에서 노트북에 필요 합니다. |
| **graph.windows.net** | 노트북에 필요 |

## <a name="python-hosts"></a>Python 호스트

이 섹션의 호스트는 Python 패키지를 설치 하는 데 사용 됩니다. 개발, 학습 및 배포 중에 필요 합니다. 

| **호스트 이름** | **용도** |
| ---- | ---- |
| **anaconda.com** | 기본 패키지를 설치 하는 데 사용 됩니다. |
| **\*. anaconda.org** | 리포지토리 데이터를 가져오는 데 사용 됩니다. |
| **pypi.org** | 기본 인덱스에서 종속성을 나열 하는 데 사용 되며 (있는 경우) 사용자 설정에서 인덱스를 덮어쓰지 않습니다. 인덱스를 덮어쓰는 경우 ** \* pythonhosted.org**도 허용 해야 합니다. |

## <a name="r-hosts"></a>R 호스트

이 섹션의 호스트는 R 패키지를 설치 하는 데 사용 됩니다. 개발, 학습 및 배포 중에 필요 합니다.

> [!IMPORTANT]
> 내부적으로 Azure Machine Learning 용 R SDK는 Python 패키지를 사용 합니다. 따라서 방화벽을 통해 Python 호스트도 허용 해야 합니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **cloud.r-project.org** | CRAN 패키지를 설치할 때 사용 됩니다. |

## <a name="azure-government-region"></a>Azure Government 지역

Azure Government 영역에 대 한 필수 Url입니다.

| **호스트 이름** | **용도** |
| ---- | ---- |
| **usgovarizona.api.ml.azure.us** | 미국 애리조나 지역 |
| **usgovvirginia.api.ml.azure.us** | 미국 버지니아 지역 |

## <a name="next-steps"></a>다음 단계

* [자습서: Azure Portal을 사용하여 Azure Firewall 배포 및 구성](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure Virtual Network 내에서 Azure ML 실험 및 유추 작업 보호](how-to-enable-virtual-network.md)
