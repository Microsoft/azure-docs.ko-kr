---
title: 팀 데이터 과학 프로세스 수명 주기
description: 데이터 과학 프로젝트를 실행하는 데 필요한 단계입니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 40d1af6e6258b5026853532f7963a76d4fc389db
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837903"
---
# <a name="the-team-data-science-process-lifecycle"></a>팀 데이터 과학 프로세스 수명 주기

TDSP(팀 데이터 과학 프로세스)는 데이터 과학 프로젝트를 구조화하는 데 사용할 수 있는 권장 수명 주기를 제공합니다. 수명 주기는 일반적으로 프로젝트가 실행될 때 시작부터 끝까지 따라야 하는 단계를 간략하게 설명합니다. [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining)(Cross Industry Standard Process for Data Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process)(Knowledge Discovery in Databases) 또는 조직 자체의 사용자 지정 프로세스 등과 같은 다른 데이터 과학 수명 주기를 사용하는 경우에도 작업 기반 TDSP를 계속 사용할 수 있습니다. 

이 수명 주기는 지능형 애플리케이션의 일부로 제공되는 데이터 과학 프로젝트를 위해 설계되었습니다. 이러한 애플리케이션은 예측 분석을 위해 기계 학습 또는 인공 지능 모델을 배포합니다. 예비 데이터 과학 프로젝트 및 임시 분석 프로젝트에서 이 프로세스를 사용하여 활용할 수도 있습니다. 하지만 이러한 프로젝트의 경우 여기에 설명된 단계 중 일부가 필요하지 않을 수도 있습니다. 

## <a name="five-lifecycle-stages"></a>5가지 수명 주기 단계

TDSP 수명 주기는 반복적으로 실행되는 5가지 주요 단계로 구성됩니다. 이러한 단계에는 다음이 포함됩니다.

   1. [비즈니스 이해](lifecycle-business-understanding.md)
   2. [데이터 취득 및 이해](lifecycle-data.md)
   3. [모델링](lifecycle-modeling.md)
   4. [배포](lifecycle-deployment.md)
   5. [고객 승인](lifecycle-acceptance.md)

다음은 TDSP 수명 주기를 시각적으로 나타낸 것입니다. 

![TDSP 수명 주기](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP 수명 주기는 예측 모델을 사용하는 데 필요한 작업에 대한 지침을 제공하는 일련의 반복 단계로 모델링됩니다. 지능형 애플리케이션을 빌드하는 데 사용하려는 예측 모델을 프로덕션 환경에 배포합니다. 이 프로세스 수명 주기의 목표는 명확한 합의 종점까지 데이터 과학 프로젝트를 계속 진행하는 것입니다. 데이터 과학은 연구 및 발견을 연습하는 과정입니다. 표준화된 템플릿을 활용하는 잘 정의된 아티팩트 집합을 통해 팀 및 고객에게 작업을 전달하면 오해를 방지할 수 있습니다. 또한 이러한 템플릿을 사용하면 복잡한 데이터 과학 프로젝트를 성공적으로 완료할 확률도 높아집니다.

각 단계마다 다음 정보를 제공합니다.

   * **목표** 구체적 목적입니다.
   * **수행 방법**: 특정 작업의 개요 및 완료 방법에 대한 지침입니다.
   * **아티팩트**: 결과물 및 이를 생성하기 위한 지원입니다.

## <a name="next-steps"></a>다음 단계

특정 시나리오 프로세스의 모든 단계를 보여주는 종합적인 전체 연습도 제공됩니다. [예제 연습](walkthroughs.md) 문서는 링크 및 썸네일 설명을 포함하는 시나리오 목록을 제공합니다. 이 연습에서는 클라우드 및 온-프레미스 도구와 서비스를 워크플로 또는 파이프라인에 결합하여 지능형 애플리케이션을 만드는 방법을 설명합니다. 

Azure Machine Learning Studio를 사용하는 TDSP의 단계 실행 방법의 예제를 보려면 [Azure Machine Learning에서 TDSP 사용](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)을 참조하세요.
