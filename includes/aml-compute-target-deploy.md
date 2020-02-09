---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/23/2019
ms.openlocfilehash: ba1e0eee5de65364d8e5e762195f7de98384c2a7
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122589"
---
| 컴퓨팅 대상 | 사용 대상 | GPU 지원 | FPGA 지원 | Description |
| ----- | ----- | ----- | ----- | ----- |
| [로컬&nbsp;웹&nbsp;서비스](../articles/machine-learning/how-to-deploy-and-where.md#local) | 테스트/디버깅 | &nbsp; | &nbsp; | 제한 된 테스트 및 문제 해결에 사용 합니다. 하드웨어 가속은 로컬 시스템에서 라이브러리를 사용 하는지에 따라 달라 집니다.
| [Azure Machine Learning compute 인스턴스&nbsp;웹&nbsp;서비스](../articles/machine-learning/how-to-deploy-and-where.md#notebookvm) | 테스트/디버깅 | &nbsp; | &nbsp; | 제한 된 테스트 및 문제 해결에 사용 합니다.
| [AKS(Azure Kubernetes Service)](../articles/machine-learning/how-to-deploy-and-where.md#aks) | 실시간 유추 |  [예](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (웹 서비스 배포) | [예](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |대규모 프로덕션 배포에 사용 합니다. 배포 된 서비스의 빠른 응답 시간 및 자동 크기 조정을 제공 합니다. Azure Machine Learning SDK를 통해 클러스터 자동 크기 조정을 지원 하지 않습니다. AKS 클러스터의 노드를 변경 하려면 Azure Portal의 AKS 클러스터에 대한 UI를 사용 합니다. AKS는 디자이너에 사용할 수 있는 유일한 옵션입니다. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-and-where.md#aci) | 테스트 또는 개발 | &nbsp;  | &nbsp; | 48 GB 미만의 RAM이 필요한 낮은 규모의 CPU 기반 작업에 사용 합니다. |
| [Azure Machine Learning 계산 클러스터](../articles/machine-learning/how-to-use-parallel-run-step.md) | 모드 일괄 처리&nbsp;유추 | [예](../articles/machine-learning/how-to-use-parallel-run-step.md) (기계 학습 파이프라인) | &nbsp;  | 서버를 사용 하지 않는 계산에서 일괄 처리 점수 매기기를 실행 합니다. 는 보통 및 낮은 우선 순위 Vm을 지원 합니다. |
| [Azure Functions](../articles/machine-learning/how-to-deploy-functions.md) | 모드 실시간 유추 | &nbsp; | &nbsp; | &nbsp; |
| [Azure IoT Edge](../articles/machine-learning/how-to-deploy-and-where.md#iotedge) | 모드 IoT&nbsp;모듈 |  &nbsp; | &nbsp; | IoT 장치에서 ML 모델을 배포 하 고 제공 합니다. |
| [Azure Data Box Edge](../articles/databox-online/data-box-edge-overview.md)   | Via IoT Edge |  &nbsp; | 예 | IoT 장치에서 ML 모델을 배포 하 고 제공 합니다. |

> [!NOTE]
> 로컬, Azure Machine Learning 계산 인스턴스 및 Azure Machine Learning 계산 클러스터와 같은 계산 대상이 학습 및 실험을 위해 GPU를 지원 하지만 __웹 서비스로 배포할 때__ gpu를 사용 하는 것은 Azure Kubernetes service 에서만 지원 됩니다.
>
> __기계 학습 파이프라인과의 점수__ 를 매길 때 GPU를 사용 하는 것은 Azure Machine Learning 계산 에서만 지원 됩니다.