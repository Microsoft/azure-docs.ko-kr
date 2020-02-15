---
title: Azure Machine Learning과 Machine Learning Studio(클래식)
description: Azure Machine Learning과 Machine Learning Studio(클래식)의 차이점
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 10/29/2019
ms.openlocfilehash: a3122a3ae1687369b87d193efc693b3b7c659aac
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "76311464"
---
# <a name="how-azure-machine-learning-differs-from-machine-learning-studio-classic"></a>Azure Machine Learning과 Machine Learning Studio(클래식)의 차이점

이 문서에서는 Azure Machine Learning의 기능 및 인터페이스를 Machine Learning Studio(클래식)와 비교합니다. 

## <a name="about-machine-learning-studio-classic"></a>Machine Learning Studio(클래식) 정보
[Machine Learning Studio(클래식)](studio/what-is-ml-studio.md)는 코드 작성 없이 기계 학습 솔루션을 빌드, 테스트, 배포할 수 있는 끌어서 놓기 방식의 시각적 공동 작업 영역입니다. 미리 빌드되고 미리 구성된 기계 학습 알고리즘과 데이터 처리 모듈 그리고 전용 컴퓨팅 플랫폼을 사용합니다.

## <a name="about-azure-machine-learning"></a>Azure Machine Learning 정보

한편 [Azure Machine Learning](overview-what-is-azure-ml.md)은 디자이너(미리 보기)라는 웹 인터페이스 **및** 여러 SDK와 CLI를 모두 제공하여 데이터를 빠르게 준비하고 기계 학습 모델을 학습 및 배포합니다. Azure Machine Learning을 사용하면 크기 조정, 여러 프레임워크 지원, 자동화된 Machine Learning 및 파이프라인 지원과 같은 고급 ML 기능을 사용할 수 있습니다.

Azure Machine Learning 디자이너는 Studio(클래식)와 비슷한 끌어서 놓기 환경을 제공합니다. 그러나 Studio(클래식) 전용 컴퓨팅 플랫폼과는 달리, 디자이너는 사용자 고유의 컴퓨팅 리소스를 사용하고 확장 가능하며 Azure Machine Learning에 완전히 통합됩니다.  

> [!TIP]
> 현재 Machine Learning Studio(클래식)를 사용하거나 평가하는 고객은 ML 모듈 끌어서 놓기 __기능과 더불어__ 확장성, 버전 제어 및 엔터프라이즈 보안을 제공하는 [Azure Machine Learning 디자이너](https://docs.microsoft.com/azure/machine-learning/concept-designer)(미리 보기)를 사용하는 것이 좋습니다.

## <a name="comparison-azure-machine-learning-vs-machine-learning-studio-classic"></a>비교: Azure Machine Learning과 Machine Learning Studio(클래식)

다음은 간략하게 비교한 내용입니다.

||  Azure Machine Learning 디자이너|Studio(클래식) |
|---| --- | --- |
||디자이너는 미리 보기 상태이며 Azure Machine Learning은 GA입니다.|GA(일반 공급) | 
|끌어서 놓기 인터페이스| yes | yes|
|실험| 컴퓨팅 대상으로 크기 조정|크기 조정(10GB 학습 데이터 제한) | 
|인터페이스용 모듈| [인기 있는 많은 모듈](algorithm-module-reference/module-reference.md) | 다수 |
|컴퓨팅 대상 학습| AML 컴퓨팅(GPU/CPU)|전용 컴퓨팅 대상, CPU만 해당|
|컴퓨팅 대상 추론| 실시간 유추를 위한 Azure Kubernetes Service <br/>일괄 처리 유추를 위한 AML 계산|전용 웹 서비스 형식, 사용자 지정 불가능 | 
|ML 파이프라인| 파이프라인 제작 <br/> 게시된 파이프라인 <br/> 파이프라인 엔드포인트 <br/> [ML 파이프라인에 대해 자세히 알아보기](concept-ml-pipelines.md)|지원되지 않음 | 
|ML Ops| 구성 가능한 배포, 모델 및 파이프라인 버전 관리|기본 모델 관리 및 배포 | 
|모델| 학습 작업에 따라 다양한 표준 형식|독점적이며 이식 불가능 형식| 
|자동화된 모델 교육|아직 디자이너에서는 수행할 수 없지만 인터페이스 및 SDK를 통해서는 수행 가능합니다.| 예 | 

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning 시작

다음 리소스는 Azure Machine Learning을 시작하는 데 도움이 될 수 있습니다.

- [Azure Machine Learning 개요](tutorial-first-experiment-automated-ml.md) 읽기 

- 자동 가격을 예측하려면 [첫 번째 디자이너 파이프라인을 만듭니다](tutorial-designer-automobile-price-train-score.md).

![Azure Machine Learning 디자이너 예](media/concept-designer/designer-drag-and-drop.gif)

## <a name="next-steps"></a>다음 단계

Azure Machine Learning에는 디자이너의 끌어서 놓기 기능 외에도 다른 도구가 있습니다.  
  + [Python Notebook을 사용하여 ML 모델 학습 및 배포](tutorial-1st-experiment-sdk-setup.md)
  + [R Markdown을 사용하여 ML 모델을 학습 및 배포](tutorial-1st-r-experiment.md) 
  + [자동화된 기계 학습을 사용하여 ML 모델 학습 및 배포](tutorial-designer-automobile-price-train-score.md) 
  + [기계 학습 CLI를 사용하여 모델 학습 및 배포](tutorial-train-deploy-model-cli.md)

