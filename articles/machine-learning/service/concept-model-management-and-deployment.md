---
title: 'MLOps: 관리, 배포 및 ML 모델 모니터링'
titleSuffix: Azure Machine Learning service
description: 'MLOps에 대 한 Azure Machine Learning 서비스를 사용 하는 방법 알아보기: 배포, 관리 및 지속적으로 개선 하려면 모델을 모니터링 합니다. Azure Machine Learning Service를 사용하여 학습시킨 모델을 로컬 컴퓨터 또는 다른 원본에서 배포할 수 있습니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 5cbb7f13214a86f528521fdeb1ffa1374ca813ef
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331700"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps: Azure Machine Learning Service를 사용하여 모델 관리, 배포 및 모니터링

이 문서에서는 Azure Machine Learning 서비스를 사용 하 여 모델의 수명 주기를 관리 하는 방법에 알아봅니다. Azure Machine Learning에 기계 학습 솔루션의 일관성을 확인 하 고 품질을 개선 하는 Machine Learning 작업 (MLOps) 접근 방식을 사용 합니다. Azure Machine Learning 서비스는 다음 MLOps 기능을 제공합니다.

* Azure 파이프라인과 통합 합니다. 모델에 대 한 연속 통합 및 배포 워크플로 정의 합니다.
* 학습 된 모델의 여러 버전을 유지 관리 하는 모델 레지스트리입니다.
* 모델 유효성 검사 합니다. 자동으로 학습 된 모델의 유효성을 검사 하 고 프로덕션에 배포 하는 것에 대 한 최적의 구성을 선택 합니다.
* IoT Edge 장치에 로컬로 또는 클라우드에서 웹 서비스로 모델을 배포 합니다.
* 모델의 다음 버전에서 향상 된 드라이브 수 있도록 배포 된 모델의 성능을 모니터링 합니다.

MLOps 및 Azure Machine Learning 서비스에 적용 되는 개념에 더 귀를 다음 비디오를 시청 합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Azure 파이프라인과 통합

모델을 학습 하는 지속적인 통합 프로세스를 만들려는 Azure 파이프라인을 사용할 수 있습니다. 일반적인 시나리오는 데이터 과학자가 프로젝트의 경우 Git 리포지토리로 변경 확인 하는 경우 Azure 파이프라인 교육 실행을 시작 합니다. 그런 다음 학습된 된 모델의 성능 특징을 보려면 실행의 결과 검사할 수 있습니다. 모델을 웹 서비스로 배포 하는 파이프라인을 만들 수도 있습니다.

합니다 [Azure Machine Learning 확장](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) 쉽게 Azure 파이프라인을 사용 하 여 작동 합니다. 다음과 같은 향상 기능이 Azure 파이프라인을 제공합니다.

* 서비스 연결을 정의 하는 경우 작업 영역 선택할을 수 있습니다.
* 하면 학습 된 모델을 학습 하는 파이프라인의 생성에 의해 트리거되도록 하는 파이프라인을 해제 합니다.

Azure Machine Learning을 사용 하 여 Azure 파이프라인을 사용 하 여에 대 한 자세한 내용은 참조는 [Azure 파이프라인을 사용 하 여 기계 학습 모델의 연속 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning) 문서 및 [Azure Machine Learning 서비스 MLOps](https://aka.ms/mlops) 리포지토리.

## <a name="convert-and-optimize-models"></a>변환 하 고 모델 최적화

모델을 변환 [열고 신경망 네트워크 Exchange](https://onnx.ai) (ONNX) 성능이 향상 될 수 있습니다. 평균적으로 ONNX 변환할 2 배로 성능 향상 시킬 수 있습니다.

Azure Machine Learning 서비스를 사용 하 여 ONNX에 자세한 내용은 참조는 [만들기 및 ML 모델을 가속화](concept-onnx.md) 문서.

## <a name="register-models"></a>모델 등록

모델을 등록하면 작업 영역에서 Azure 클라우드의 모델을 저장하고 버전을 지정할 수 있습니다. 모델 레지스트리에서는 학습된 모델을 쉽게 구성하고 추적할 수 있습니다.

> [!TIP]
> 등록 된 모델은 모델을 구성 하는 하나 이상의 파일에 대 한 논리적 컨테이너입니다. 예를 들어, 여러 파일에 저장 된 모델에 있으면 등록할 수 있습니다를 단일 모델로 Azure Machine Learning 작업 영역에서 합니다. 등록이 완료 되 면 다음 또는 등록 된 모델을 배포할 수 있으며 등록 된 모든 파일을 수신 합니다.
 
등록된 모델은 이름 및 버전으로 식별됩니다. 모델을 기존 이름과 동일한 이름으로 등록할 때마다 레지스트리에서 버전을 증가시킵니다. 또한 모델을 검색할 때 사용할 수 있는 추가 메타데이터 태그를 등록 중에 제공할 수도 있습니다. Azure Machine Learning 서비스는 Python 3.5.2를 사용 하 여 로드 이상이 될 수 있는 모든 모델을 지원 합니다.

> [!TIP]
> 또한 Azure Machine Learning 서비스 외부에서 학습 모델을 등록할 수 있습니다.

활성 배포에 사용 되는 등록 된 모델을 삭제할 수 없습니다.

자세한 내용은 [모델 배포](how-to-deploy-and-where.md#registermodel)의 모델 등록 섹션을 참조하세요.

pickle 형식으로 저장된 모델을 등록하는 예제는 [자습서: 이미지 분류 모델 학습시키기](tutorial-deploy-models-with-aml.md)를 참조하세요.

## <a name="package-and-debug-models"></a>모델 패키지 및 디버그

모델을 프로덕션 환경에 배포 하기 전에 Docker 이미지로 패키지 됩니다. 대부분의 경우에서 이미지 만들기 과정 자동으로 백그라운드에서 배포 합니다. 고급 시나리오에 대 한 이미지를 수동으로 지정할 수 있습니다.

배포 관련 문제를 실행 하는 경우 문제 해결 및 디버깅에 대 한 로컬 개발 환경에 배포할 수 있습니다.

자세한 내용은 [모델을 배포할](how-to-deploy-and-where.md#registermodel) 하 고 [배포 문제 해결](how-to-troubleshoot-deployment.md)합니다.

## <a name="validate-and-profile-models"></a>유효성 검사 및 모델을 프로 파일링

Azure Machine Learning 서비스 모델을 배포할 때 사용할 이상적인 CPU 및 메모리 설정을 확인 하려면 프로 파일링을 사용할 수 있습니다. 모델 유효성 검사는 사용자가 제공한 데이터를 사용 하 여 프로 파일링 프로세스에 대 한이 프로세스의 일부로 발생 합니다.

## <a name="use-models"></a>모델 사용

클라우드 또는 로컬 개발 환경에서 웹 서비스로 학습 된 기계 학습 모델을 배포할 수 있습니다. 또한 Azure IoT Edge 장치로 모델을 배포할 수 있습니다. 배포는 추론을 위해 CPU, GPU, 또는 현장에서 프로그래밍 FPGA gate array ()를 사용할 수 있습니다. 또한 Power BI에서 모델을 사용할 수 있습니다.

모델을 웹 서비스나 IoT Edge 장치를 사용할 때 다음 항목을 제공 합니다.

* 서비스/장치에 전송 된 데이터의 점수를 매기는 데 사용 되는 모델입니다.
* 시작 스크립트가 있습니다. 이 스크립트는 요청을 수락, 모델을 사용 하 여 데이터 점수 매기기 및 응답을 반환 합니다.
* 모델 및 입력 스크립트에 필요한 종속성을 설명 하는 conda 환경 파일입니다.
* 모든 추가 자산 텍스트, 데이터 등 모델 및 입력 스크립트에 필요한 합니다.

이러한 자산을 Docker 이미지로 패키지 되 고 웹 서비스 또는 IoT Edge 모듈 배포 합니다.

필요에 따라 추가 배포를 조정 하려면 다음 매개 변수를 사용할 수 있습니다.

* GPU를 사용 하도록 설정 합니다. Docker 이미지에 GPU 지원을 사용 하도록 설정 하는 데 사용 합니다. Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning Compute 또는 Azure Virtual Machines와 같은 Microsoft Azure 서비스에서 이미지를 사용 해야 합니다.
* 추가 docker 파일 단계: Docker 이미지를 만들 때 실행할 추가 Docker 단계를 포함 하는 파일입니다.
* 기본 이미지: 기본 이미지로 사용 하는 사용자 지정 이미지입니다. 사용자 지정 이미지를 사용 하지 않는 경우 기본 이미지는 Azure Machine Learning 서비스에서 제공 됩니다.

대상 배포 플랫폼의 구성을 제공할 수도 있습니다. 예를 들어 VM 제품군 형식, 사용 가능한 메모리 및 Azure Kubernetes Service에 배포 하는 경우에 코어 수입니다.

이미지가 만들어지고, Azure Machine Learning 서비스에 필요한 구성 요소도 추가 됩니다. 예를 들어, 자산 웹 서비스를 실행 하 고 IoT Edge를 사용 하 여 상호 작용 해야 합니다.

> [!NOTE]
> 수정 하거나 웹 서버 또는 Docker 이미지에서 사용 하는 IoT Edge 구성 요소를 변경할 수 없습니다. Azure Machine Learning 서비스는 웹 서버 구성 및 테스트 하 고 Microsoft에서 지원 되는 IoT Edge 구성 요소를 사용 합니다.

### <a name="web-service"></a>웹 서비스

모델을 사용할 수 있습니다 **웹 서비스** 다음을 사용 하 여 계산 대상:

* Azure Container Instance
* Azure Kubernetes Service
* 로컬 개발 환경

웹 서비스로 모델을 배포 하려면 다음 항목을 제공 해야 합니다.

* 모델 또는 모델의 앙상블입니다.
* 모델을 사용 하는 데 필요한 종속성입니다. 예를 들어 요청을 수락 하 고 모델, conda 종속성을 호출 하는 스크립트 등입니다.
* 배포 구성 방법 및 위치를 설명 하는 모델을 배포 하려면.

자세한 내용은 [모델을 배포할](how-to-deploy-and-where.md)합니다.

### <a name="iot-edge-devices"></a>IoT Edge 장치


통해 IoT 장치를 사용 하 여 모델을 사용할 수 있습니다 **Azure IoT Edge 모듈**합니다. IoT Edge 모듈은 유추 또는 장치에서 점수 매기기 모델을 사용 하도록 설정 하는 하드웨어 장치에 배포 됩니다.

자세한 내용은 [모델을 배포할](how-to-deploy-and-where.md)합니다.

### <a name="analytics"></a>분석

Microsoft Power BI는 데이터 분석을 위해 기계 학습 모델을 사용 하 여 지원 합니다. 자세한 내용은 [Power BI (미리 보기)에서 Azure Machine Learning 통합](https://docs.microsoft.com/power-bi/service-machine-learning-integration)합니다.

## <a name="monitor-and-collect-data"></a>모니터링 하 고 데이터를 수집 합니다.

모니터링을 사용 하면 반환 되는 예측 모델을 전송 되는 어떤 데이터를 이해할 수 있습니다.

이 정보가 모델에 사용 되는 방식을 이해 하도록 도와줍니다. 수집 된 입력된 데이터를 모델의 학습 이후 버전에서는 유용할 수도 있습니다.

자세한 내용은 [모델 데이터 컬렉션을 사용하도록 설정하는 방법](how-to-enable-data-collection.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 서비스를 사용하여 [모델을 배포할 수 있는 방법과 위치](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다. 배포의 예제를 참조 하세요. [자습서: Azure Container Instances에서 이미지 분류 모델을 배포할](tutorial-deploy-models-with-aml.md)합니다.

만드는 방법을 알아봅니다 [Azure 파이프라인을 사용 하 여 기계 학습 모델의 연속 통합 및 배포](/azure/devops/pipelines/targets/azure-machine-learning)합니다. 

[웹 서비스로 배포된 모델을 사용](how-to-consume-web-service.md)하는 클라이언트 애플리케이션과 서비스를 만드는 방법을 알아봅니다.
