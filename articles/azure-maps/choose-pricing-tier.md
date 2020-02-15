---
title: 적절 한 가격 책정 계층 선택 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 맵에서 제공 되는 가격 책정 계층에 대해 알아봅니다.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 048ecdc9b96edcdebecb4cd7df0630aa991aa4d1
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210075"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps에서 적절한 가격 책정 계층 선택

Azure Maps는 S0 및 S1의 두 가격 책정 계층을 제공 합니다. 이 문서의 목적은 요구 사항에 적합한 가격 책정 계층을 선택하도록 도와주는 것입니다. 적절 한 가격 책정 계층을 선택 하려면 다음 두 가지 질문을 직접 확인 합니다.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>동시 사용자를 몇 명이나 지원할 계획입니까? 
S0 및 S1 가격 책정 계층이 처리할 수 있는 데이터 처리량은 서로 다릅니다. S0 가격 책정 계층은 **초당 최대 50개의 쿼리**를 처리합니다. 반면 S1 계층은 **초당 50 개 이상의 쿼리**를 처리 합니다.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>어떤 지리 공간적 기능을 사용할 계획입니까?
핵심 지리 공간적 Api가 서비스 요구 사항을 충족 하는 경우 S0 가격 책정 계층이 적합 합니다. 애플리케이션에 대한 보다 고급 기능을 원하는 경우에는 S1 가격 책정 계층을 선택하는 것이 좋습니다. 고급 기능에는 항공 plus 하이브리드 이미지, 경로 범위 가져오기 및 batch 지 오 코딩이 포함 됩니다. **가격 책정 계층 기능** 표를 검토 하 여 응용 프로그램에 가장 적합 한 가격 책정 계층을 선택 합니다.

### <a name="pricing-tier-capabilities"></a>가격 책정 계층 기능

| 기능                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 지도 렌더링                              | ✓                   | ✓       |
| 위성 이미지                       |                     | ✓        |
| 검색                                  | ✓                    | ✓        |
| 일괄 검색                            |                     | ✓        |
| 라우팅                                   | ✓                    |✓        |
| 일괄 처리 라우팅                            |                    | ✓        |
| 행렬 라우팅                          |                     | ✓        |
| 경로 범위 (등시성)                |                     | ✓        |
| 트래픽                                |✓                    |✓        |
| 표준 시간대                               |✓                    |✓        |
| 지리적 위치 (미리 보기)                    |✓                   |✓        |
| Spatial Operations                        |                    |✓        |
| 지 오 펜싱                                |                    |✓        |
| Azure Maps 데이터 (미리 보기)                |                     | ✓        |
| 모바일 (미리 보기)                       |                     | ✓        |
| 날씨 (미리 보기)                        |✓                    |✓        |

다음과 같은 추가 사항을 고려 합니다.
* 어떤 유형의 엔터프라이즈가 있습니까?
* 응용 프로그램의 중요도는 어떻게 되나요?

### <a name="pricing-tier-targeted-customers"></a>가격 책정 계층 대상 고객

S0 및 S1 가격 책정 계층에 대해 자세히 알아보려면 **가격 책정 계층 대상 고객** 표를 참조하세요. 자세한 내용은 [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)을 참조하세요. 

| 가격 책정 계층  |     대상 고객                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 가격 책정 계층은 개념 증명 개발부터 응용 프로그램 프로덕션 및 배포에 대 한 초기 단계 테스트부터 모든 프로덕션 단계의 응용 프로그램에 대해 작동 합니다. 그러나이 계층은 소규모 개발 이나 사용자가 낮은 동시 사용자 또는 둘 다를 사용 하는 고객을 위해 설계 되었습니다. <p>|
| S1            |    <p>S1 가격 책정 계층은 대규모 엔터프라이즈 응용 프로그램, 중요 업무용 응용 프로그램 또는 대용량 동시 사용자를 포함 하는 고객을 위한 것입니다. 고급 지리 공간 서비스를 필요로 하는 고객에게도 적합합니다.</p>|

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 보고 변경하는 방법에 대해 자세히 알아보기:

> [!div class="nextstepaction"] 
> [가격 책정 계층 관리](how-to-manage-pricing-tier.md)
