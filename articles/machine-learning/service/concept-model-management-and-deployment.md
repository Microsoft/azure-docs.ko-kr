---
title: ML 모델 관리, 등록, 배포 및 모니터링
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service를 통해 모델을 배포, 관리 및 모니터링하여 지속적으로 개선하는 방법을 알아봅니다. Azure Machine Learning Service를 사용하여 학습시킨 모델을 로컬 컴퓨터 또는 다른 원본에서 배포할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 1/23/2019
ms.custom: seodec18
ms.openlocfilehash: 2cd2d328d33744854bc525e5ecf1dfa3b6e4bcc8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821124"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링

이 문서에서는 Azure Machine Learning Service를 통해 모델을 배포, 관리 및 모니터링하여 지속적으로 개선하는 방법을 알아볼 수 있습니다. Azure Machine Learning을 사용하여 학습한 모델을 로컬 머신 또는 다른 원본에서 배포할 수 있습니다. 

다음 다이어그램은 전체 배포 워크플로를 보여 줍니다. [![Azure Machine Learning에 대 한 배포 워크플로](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

배포 워크플로에는 다음 단계가 포함됩니다.
1. Azure Machine Learning Service 작업 영역에서 호스팅되는 레지스트리에 **모델 등록**
1. 이식 가능한 컨테이너에서 점수 매기기 스크립트 및 종속성과 모델이 쌍을 이루는 **이미지 등록** 
1. 클라우드 또는 에지 디바이스에서 웹 서비스로 이미지 **배포**
1. **모니터링 및 데이터 수집**
1. 새 이미지를 사용하도록 배포를 **업데이트**합니다.

각 단계는 독립적으로 또는 단일 배포 명령의 일환으로 수행할 수 있습니다. 또한 이 그래픽에서 보여 주듯이 배포를 **CI/CD 워크플로**에 통합할 수 있습니다.

[![' Azure Machine Learning 지속적인 통합/지속적인 배포 (CI/CD) 주기 '](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>1단계: 모델 등록

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.
 
등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 또한 모델을 검색할 때 사용할 수 있는 추가 메타데이터 태그를 등록 중에 제공할 수도 있습니다. Azure Machine Learning 서비스는 Python 3을 사용하여 로드할 수 있는 모든 모델을 지원합니다. 

이미지에서 사용되는 모델은 삭제할 수 없습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

pickle 형식으로 저장된 모델을 등록하는 예제는 [자습서: 이미지 분류 모델 학습시키기](tutorial-deploy-models-with-aml.md)를 참조하세요.

ONNX 모델을 사용하는 방법에 대한 자세한 내용은 [ONNX 및 Azure Machine Learning](how-to-build-deploy-onnx.md) 문서를 참조하세요.

## <a name="step-2-register-image"></a>2단계: 이미지 등록

이미지를 사용하면 모델을 사용하는 데 필요한 모든 구성 요소와 함께 신뢰할 수 있는 모델을 배포할 수 있습니다. 이미지에는 다음 항목이 포함됩니다.

* 모델
* 점수 매기기 엔진
* 점수 매기기 파일 또는 애플리케이션
* 모델 점수 매기기에 필요한 모든 종속성

또한 이미지에는 로깅 및 모니터링을 위한 SDK 구성 요소가 포함될 수 있습니다. SDK 로그 데이터는 모델의 입력 및 출력을 포함하여 모델을 자세히 튜닝하거나 재학습하는 데 사용할 수 있습니다.

Azure Machine Learning은 가장 인기 있는 프레임워크를 지원하지만, 일반적으로 PIP 설치가 가능한 모든 프레임워크에서 작동할 수 있습니다.

작업 영역이 만들어지면 해당 작업 영역에서 사용하는 몇 가지 다른 Azure 리소스도 만들어집니다.
기본 이미지를 만드는 데 사용 되는 모든 개체는 작업 영역에서 Azure storage 계정에 저장 됩니다. 이미지를 만들 때 추가 메타데이터 태그를 제공할 수 있습니다. 메타데이터 태그 역시 이미지 레지스트리에 저장되며, 쿼리하여 이미지를 찾을 수 있습니다.

Azure Container Registry에 업로드 하 고 Azure Machine Learning 서비스에서 사용할 수 있는 사용자 지정 이미지를 사용할 수도 있습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#configureimage)의 이미지 구성 및 등록 섹션을 참조하세요.

## <a name="step-3-deploy-image"></a>3단계: 이미지 배포

등록된 이미지를 클라우드 또는 에지 디바이스에 배포할 수 있습니다. 배포 프로세스에서는 모델 모니터링, 부하 분산 및 자동 크기 조정에 필요한 모든 리소스를 만듭니다. 배포 중에 보안 자산을 제공하여 인증서 기반 인증을 통해 배포된 서비스에 대한 액세스를 보호할 수 있습니다. 최신 이미지를 사용하도록 기존 배포를 업그레이드할 수도 있습니다.

또한 웹 서비스 배포도 검색할 수 있습니다. 예를 들어 특정 모델 또는 이미지의 모든 배포를 검색할 수 있습니다.

[![추론 대상](media/concept-model-management-and-deployment/inferencing-targets.png)](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

클라우드의 다음 배포 대상에 이미지를 배포할 수 있습니다.

* Azure Container Instance
* Azure Kubernetes Service
* Azure FPGA 머신
* Azure IoT Edge 디바이스

서비스가 배포되면 추론 요청이 자동으로 부하 분산되고, 요청 시 모든 스파이크를 충족하도록 클러스터의 크기가 조정됩니다. [서비스에 대한 원격 분석](how-to-enable-app-insights.md)은 작업 영역과 연결된 Azure Application Insights 서비스로 캡처할 수 있습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#deploy)의 배포 섹션을 참조하세요.

## <a name="step-4-monitor-models-and-collect-data"></a>4단계: 모델 모니터링 및 데이터 수집

모델 로깅 및 데이터 캡처를 수행하는 SDK를 사용할 수 있으므로 모델에서 입력, 출력 및 기타 관련 데이터를 모니터링할 수 있습니다. 데이터는 작업 영역에 대한 Azure Storage 계정에 Blob으로 저장됩니다.

모델을 통해 SDK를 사용하려면 SDK를 점수 매기기 스크립트 또는 애플리케이션으로 가져옵니다. 그런 다음, SDK를 사용하여 매개 변수, 결과 또는 입력 세부 정보와 같은 데이터를 기록할 수 있습니다.

이미지를 배포할 때마다 모델 데이터 컬렉션을 사용하기로 결정하면 개인 Blob 저장소에 대한 자격 증명과 같이 데이터를 캡처하는 데 필요한 세부 정보가 자동으로 프로비전됩니다.

> [!Important]
> Microsoft는 모델에서 수집한 데이터를 볼 수 없습니다. 데이터는 작업 영역에 대한 Azure 저장소 계정으로 직접 보내집니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.

## <a name="step-5-update-the-deployment"></a>5단계: 배포 업데이트

모델의 업데이트는 자동으로 등록되지 않습니다. 마찬가지로 새 이미지를 등록해도 이전 버전 이미지에서 생성된 배포가 자동으로 업데이트되지는 않습니다. 대신 모델을 수동으로 등록하고 이미지를 등록한 다음 모델을 업데이트해야 합니다. 자세한 내용은 [모델 배포](how-to-deploy-and-where.md#update)의 업데이트 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스를 사용하여 [모델을 배포할 수 있는 방법과 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다. 배포의 예제를 참조 하세요. [자습서: Azure Container Instances에서 이미지 분류 모델을 배포할](tutorial-deploy-models-with-aml.md)합니다.

[웹 서비스로 배포된 모델을 사용](how-to-consume-web-service.md)하는 클라이언트 애플리케이션과 서비스를 만드는 방법을 알아봅니다.
