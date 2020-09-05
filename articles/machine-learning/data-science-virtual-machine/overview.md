---
title: Azure Data Science Virtual Machine이란?
titleSuffix: Azure Data Science Virtual Machine
description: Azure Data Science Virtual Machine 개요 - 데이터 과학을 수행하기 위해 미리 설치 및 구성된 도구와 라이브러리가 있는 Azure 클라우드 플랫폼에서 가상 머신을 쉽게 사용할 수 있습니다.
keywords: 데이터 과학 도구, 데이터 과학 가상 머신, 데이터 과학용 도구, linux 데이터 과학
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816340"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Linux 및 Windows용 Azure Data Science Virtual Machine이란?

DSVM(Data Science Virtual Machine)은 데이터 과학 수행을 위해 특별히 구축된 Azure 클라우드 플랫폼의 사용자 지정 VM 이미지입니다. 여기에는 고급 분석을 위한 인텔리전트 애플리케이션 빌드를 바로 수행할 수 있도록 다수의 유명한 데이터 과학 도구가 미리 설치 및 구성되어 있습니다.

DSVM은 다음에서 사용할 수 있습니다.

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Azure Machine Learning과 비교

DSVM은 데이터 과학용으로 사용자 지정된 VM 이미지이지만 [AzureML](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml)(Azure Machine Learning)은 다음을 포함하는 엔드투엔드 플랫폼입니다.

+ 완전 관리형 컴퓨팅
  + 컴퓨팅 인스턴스
  + 분산 ML 작업용 컴퓨팅 클러스터
  + 실시간 점수 매기기를 위한 유추 클러스터
+ 데이터 저장소(예: Blob, ADLS Gen2, SQL DB)
+ 실험 추적
+ 모델 관리
+ Notebooks
+ 환경(conda 및 R 종속성 관리)
+ 레이블 지정
+ 파이프라인(엔드투엔드 데이터 과학 워크플로 자동화)

### <a name="comparison-with-azureml-compute-instances"></a>AzureML 컴퓨팅 인스턴스와 비교

[Azure Machine Learning 컴퓨팅 인스턴스](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance)는 완전히 구성되고 __관리__되는 VM 이미지이지만 DSVM은 __관리되지 않는__ VM입니다.

이러한 두 제품 간의 주요 차이점은 아래에 자세히 설명되어 있습니다.


|기능 |데이터 과학<br>VM |AzureML<br>컴퓨팅 인스턴스  | 
|---------|---------|---------|
| 완전 관리형 | 아니요        | 예        |
|언어 지원     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F#       | Python 및 R        |
|운영 체제     | Ubuntu<br>Windows         |    Ubuntu     |
|미리 구성된 GPU 옵션     |  예       |    예     |
|스케일 업 옵션 | 예 | 예 |
|SSH 액세스    | 예        |    예     |
|RDP 액세스    | 예        |     아니요    |
|기본 제공<br>호스트된 Notebooks     |   아니요<br>(추가 구성 필요)      |      예   |
|기본 제공 SSO     | 아니요 <br>(추가 구성 필요)         |    예     |
|기본 제공 협업     | 아니요         | 예        |
|사전 설치된 도구     |  Jupyter(랩), RStudio Server, VSCode,<br> Visual Studio, PyCharm, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache Drill       |     Jupyter(랩)<br> RStudio Server   |

## <a name="sample-use-cases"></a>샘플 사용 사례

다음은 DSVM 고객을 위한 몇 가지 일반적인 사용 사례를 보여줍니다.

### <a name="short-term-experimentation-and-evaluation"></a>단기 실험 및 평가

DSVM을 사용하면, 특별히 게시된 [샘플 및 연습](./dsvm-samples-and-walkthroughs.md) 중 일부를 진행하여 새로운 데이터 과학 [도구](./tools-included.md)를 평가하거나 학습할 수 있습니다.

### <a name="deep-learning-with-gpus"></a>GPU를 사용한 딥 러닝

DSVM에서 학습 모델은 GPU(그래픽 처리 장치)를 기반으로 하는 하드웨어에서 딥 러닝 알고리즘을 사용할 수 있습니다. DSVM을 사용하면 Azure 플랫폼의 VM 확장 기능을 활용하여 필요에 따라 클라우드에서 GPU 기반 하드웨어를 사용할 수 있습니다. 큰 모델을 학습할 때 또는 동일한 OS 디스크를 유지하면서 고속 계산이 필요한 경우 GPU 기반 VM으로 전환할 수 있습니다. DSVM에서 N 시리즈 GPU 지원 가상 머신 SKU 중 하나를 선택할 수 있습니다. 참고 GPU 지원 가상 머신 SKU는 Azure 체험 계정에서 지원되지 않습니다.

DSVM의 Windows 버전에는 GPU 드라이버, 프레임워크 및 GPU 버전 딥 러닝 프레임워크가 미리 설치되어 있습니다. Linux 버전의 경우 Ubuntu DSVM에서 GPU에 대한 딥 러닝이 활성화됩니다. 

GPU를 기반으로 하지 않는 Azure 가상 머신에 DSVM의 Ubuntu 또는 Windows 버전을 배포할 수도 있습니다. 이 경우 모든 딥 러닝 프레임워크는 CPU 모드로 다시 대체됩니다.

[사용 가능한 딥 러닝 및 AI 프레임워크에 대해 자세히 알아보세요.](dsvm-tools-deep-learning-frameworks.md)

### <a name="data-science-training-and-education"></a>데이터 과학 학습 및 교육

데이터 과학 수업을 가르치는 엔터프라이즈 트레이너 및 강사는 대개 가상 머신 이미지를 제공합니다. 이미지를 통해 학생에게 일관된 설정을 제공하여 샘플이 예상대로 작동하도록 할 수 있습니다.

DSVM은 지원 및 비호환 문제를 덜어주는 일관된 설정을 사용하여 주문형 환경을 생성합니다. 이러한 환경을 빈번하게 구축해야 하는 경우, 특히 짧은 수업의 경우, 실질적인 이점이 있습니다.


## <a name="whats-included-on-the-dsvm"></a>DSVM에는 무엇이 포함되나요?

[여기](tools-included.md)에서 Windows 및 Linux DSVM의 전체 도구 목록을 참조하세요.

## <a name="next-steps"></a>다음 단계

다음 문서에서 자세한 내용을 알아보세요.

+ Windows:
  + [Windows DSVM 설정](provision-vm.md)
  + [Windows DSVM의 데이터 과학](vm-do-ten-things.md)

+ Linux:
  + [Linux DSVM(Ubuntu) 설정](dsvm-ubuntu-intro.md)
  + [Linux DSVM의 데이터 과학](linux-dsvm-walkthrough.md)
