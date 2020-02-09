---
title: 적절 한 가격 책정 계층 선택 | Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 맵에서 제공 되는 가격 책정 계층에 대해 알아봅니다.
author: walsehgal
ms.author: v-musehg
ms.date: 01/15/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 8764e9161f952118ca7ae28343dcd16477cf1eee
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76155760"
---
# <a name="choose-the-right-pricing-tier-in-azure-maps"></a>Azure Maps에서 적절한 가격 책정 계층 선택

Azure Maps는 S0 및 S1의 두 가격 책정 계층을 제공 합니다. 이 문서의 목적은 요구 사항에 적합한 가격 책정 계층을 선택하도록 도와주는 것입니다. 적절한 가격 책정 계층을 선택하려면 다음 두 질문에 대답합니다.

## <a name="what-geospatial-capabilities-do-i-plan-to-use"></a>어떤 지리 공간적 기능을 사용할 계획입니까?
S0 가격 책정 계층은 핵심 지리 공간적 API가 서비스 요구 사항을 충족하는 경우에 적합합니다. 애플리케이션에 대한 보다 고급 기능을 원하는 경우에는 S1 가격 책정 계층을 선택하는 것이 좋습니다. 고급 기능의 예: 항공 plus 하이브리드 이미지, 경로 범위 가져오기 및 batch 지 오 코딩. **가격 책정 계층 기능** 표를 통해 응용 프로그램에 가장 적합 한 가격 책정 계층을 선택할 수 있습니다.

## <a name="how-many-concurrent-users-do-i-plan-to-support"></a>동시 사용자를 몇 명이나 지원할 계획입니까? 
S0 및 S1 가격 책정 계층이 처리할 수 있는 데이터 처리량은 서로 다릅니다. S0 가격 책정 계층은 초당 최대 **50 개의 쿼리**를 처리 하는 반면, S1 계층은 초당 **50 개의 쿼리**를 처리 합니다.

### <a name="pricing-tier-capabilities"></a>가격 책정 계층 기능

| 기능                              |        S0           |  S1      |
|-----------------------------------------|:-------------------:|:--------:|
| 지도 렌더링                              | ✓                   | ✓       |
| 위성 이미지                       |                     | ✓        |
| 검색                                  | ✓                    | ✓        |
| 일괄 처리 검색                            |                     | ✓        |
| 라우팅                                   | ✓                    |✓        |
| 일괄 처리 라우팅                            |                    | ✓        |
| 행렬 라우팅                          |                     | ✓        |
| 경로 범위(등시선)                |                     | ✓        |
| 교통                                |✓                    |✓        |
| 표준 시간대                               |✓                    |✓        |
| 지리적 위치 (미리 보기)                    |✓                   |✓        |
| 공간 작업                        |                    |✓        |
| 지오펜싱                                |                    |✓        |
| Azure Maps 데이터 (미리 보기)                |                     | ✓        |
| 모바일 (미리 보기)                       |                     | ✓        |
| 날씨 (미리 보기)                        |✓                    |✓        |

고려할 만한 추가 데이터 요소:
* 어떤 유형의 엔터프라이즈가 있습니까?
* 응용 프로그램의 중요도는 어떻게 되나요?

### <a name="pricing-tier-targeted-customers"></a>가격 책정 계층 대상 고객

S0 및 S1 가격 책정 계층에 대해 자세히 알아보려면 **가격 책정 계층 대상 고객** 표를 참조하세요. 자세한 내용은 [Azure Maps 가격 책정](https://azure.microsoft.com/pricing/details/azure-maps/)을 참조하세요. 

| 가격 책정 계층  |     대상 고객                                                                |
|-----------------|:-----------------------------------------------------------------------------------------|
| S0            |    <p>S0 가격 책정 계층은 개념 증명 개발부터 응용 프로그램 프로덕션 및 배포에 대한 초기 단계 테스트부터 모든 프로덕션 단계의 응용 프로그램에 대해 작동 합니다. 그러나이 계층은 소규모 개발 이나 사용자가 낮은 동시 사용자 또는 둘 다를 사용 하는 고객을 위해 설계 되었습니다. <p>|
| S1            |    <p>S1 가격 책정 계층은 대기업, 중요 업무용 애플리케이션, 많은 동시 사용자를 지원해야 하는 고객에게 적합합니다. 고급 지리 공간 서비스를 필요로 하는 고객에게도 적합합니다.</p>|

## <a name="next-steps"></a>다음 단계

가격 책정 계층을 보고 변경하는 방법에 대해 자세히 알아보기:

> [!div class="nextstepaction"] 
> [가격 책정 계층 관리](how-to-manage-pricing-tier.md)
