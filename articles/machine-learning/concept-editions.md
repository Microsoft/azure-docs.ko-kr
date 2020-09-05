---
title: Azure Machine Learning Enterprise 및 Basic Edition
description: Azure Machine Learning 버전 간의 차이점에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: fbe3f0109ca3af2cf9ff44061c7882e6bd72c8a6
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88508605"
---
# <a name="enterprise-preview-and-basic-editions-of-azure-machine-learning"></a>Enterprise (preview) 및 기본 버전의 Azure Machine Learning 

Azure Machine Learning은 기계 학습 요구에 맞게 조정 된 두 가지 버전을 제공 합니다. 버전에 따라 개발자 및 데이터 과학자가 작업 영역에서 사용할 수 있는 기계 학습 도구가 달라집니다.

## <a name="choose-an-edition"></a>버전 선택

Edition은 작업 영역을 만들 때마다 할당합니다. 고객은 이 기간 동안 컴퓨팅 및 기타 Azure 리소스에서 발생하는 비용을 지불해야 합니다. [Azure Machine Learning에 대 한 비용을 관리](concept-plan-manage-cost.md)하는 방법을 알아봅니다.

[기본 작업 영역을 Enterprise (preview) 버전으로 업그레이드](how-to-manage-workspace.md#upgrade)하는 방법에 대해 알아봅니다. 

## <a name="whats-in-each-edition"></a>각 버전의 기능

### <a name="data-for-machine-learning-capabilities"></a>Machine Learning 기능에 대 한 데이터  

| 기능                     | 버전                 |
|------------------------------------------------------------------------------------|:-----------:|
| 레이블 지정: studio에서 [레이블 지정 프로젝트 만들기 및 관리](tutorial-labeling.md) (웹)                                                | 모두                     |
| 레이블 지정: studio의 Labeler (웹)                                    | 모두                     |
| 레이블 지정: 개인 인력 사용                               | 모두                     |
| 레이블 지정: [ML 보조 이미지 분류 및 개체 검색](how-to-label-images.md)                  | Enterprise edition만 해당 |
| 데이터 집합 + datastores: Python에서 만들기 및 관리                       | 모두                     |
| 데이터 집합 + datastores: studio에서 만들기 및 관리 (웹)                         | 모두                     |
| 드리프트: Python에서 데이터 집합 모니터 보기 및 관리                           | 모두                     |
| 드리프트: 스튜디오에서 데이터 집합 모니터 보기 및 관리 (웹)                            | Enterprise edition만 해당 |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>자동 학습 기능 (AutoML)

| 기능    | 버전                 |
|------------------------------------------------------------------------------------|:-----------:|
| [전자 필기장에서 Automl 실험](how-to-configure-auto-train.md) 만들기 및 실행               | 모두                     |
| [Studio에서 Automl 실험 만들기 및 실행 (웹)](how-to-use-automated-ml-for-ml-models.md)   | Enterprise edition만 해당 |
| 업계 최고의 AutoML 예측 기능             | Enterprise edition만 해당 |
| 심층 학습 및 기타 고급 학습자 지원 | Enterprise edition만 해당 |
| 대량 데이터 지원 분류 및 회귀 작업 (최대 100 GB)                     | Enterprise edition만 해당 |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>담당 Machine Learning

| 기능    | 버전                 |
|------------------------------------------------------------------------------------|:-----------:|
| [모델 설명 가능성](how-to-machine-learning-interpretability-automl.md)                                              | 모두                     |
| [차등 개인 정보](how-to-differential-privacy.md)                          | 모두                     |
| 데이터 시트를 구현할 사용자 지정 태그    | 모두                     |
| 공평 AzureML 통합                                      | 모두                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>빌드 및 학습 기능

| 기능    | 버전                 |
|------------------------------------------------------------------------------------|:-----------:|
| Visual Studio Code 통합                                                     | 모두                     |
| 보충 학습                                                             | 모두                     |
| 실험 UI                                                                 | 모두                     |
| Jupyter, JupyterLab 통합                                                    | 모두                     |
| Python SDK 지원                                                                 | 모두                     |
| R SDK 지원                                                                      | 모두                     |
| ML 파이프라인: Python에서 만들기, 실행 및 게시                           | 모두                     |
| ML 파이프라인: Python에서 예약 된 파이프라인의 실행을 만들고, 편집 하 고, 삭제 합니다.| 모두                     |
| ML 파이프라인: Python SDK에서 파이프라인 끝점 만들기                                   | 모두                     |
| ML 파이프라인: 스튜디오에서 실행 세부 정보 보기 (웹)                                              | 모두                     |
| ML 파이프라인: 디자이너에서 만들기, 실행, 시각화 및 게시                  | Enterprise edition만 해당 |
| ML 파이프라인: 디자이너에서 파이프라인 끝점 만들기 | Enterprise edition만 해당 |
| 통합 전자 필기장에 대 한 관리 되는 계산 인스턴스                                 | 모두                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>배포 및 모델 관리 기능

| 기능                            | 버전                 |
|------------------------------------------------------------------------------------|:-----------:|
| Machine Learning 및 Azure ML CLI에 대 한 Azure DevOps 확장                 | 모두                     |
| [Event Grid 통합](how-to-use-event-grid.md)                                                             | 모두                     |
| Azure Machine Learning과 Azure Stream Analytics 통합                       | 모두                     |
| SDK에서 ML 파이프라인 만들기                                                         | 모두                     |
| Batch 추론                                                                  | 모두                     |
| FPGA 기반 하드웨어 가속 모델                                             | 모두                     |
| 모델 프로 파일링                                                                    | 모두                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>보안, 거 버 넌 스 및 제어 기능

| 기능     | 버전                 |
|------------------------------------------------------------------------------------|:-----------:|
| Azure [RBAC (역할 기반 액세스 제어)](how-to-assign-roles.md) 지원                                           | 모두                     |
| 계산에 대 한 [VNet (Virtual Network)](how-to-enable-virtual-network.md) 지원                                         | 모두                     |
| 끝점 인증 점수 매기기                                                    | 모두                     |
| [작업 영역 개인 링크](how-to-configure-private-link.md)                                                            | 모두                     |
| 작업 영역에서 [할당량 관리](how-to-manage-quotas.md)                                                 | Enterprise edition만 해당 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning [버전 개요 및 가격 책정 페이지](https://azure.microsoft.com/pricing/details/machine-learning/)에서 사용할 수 있는 기능에 대해 자세히 알아보세요. 

[Basic 작업 영역을 Enterprise Edition으로 업그레이드](how-to-manage-workspace.md#upgrade)하는 방법에 대해 알아보세요. 
