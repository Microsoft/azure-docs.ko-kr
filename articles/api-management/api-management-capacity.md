---
title: Azure API Management 인스턴스의 용량 | Microsoft Docs
description: 이 문서에서는 용량 메트릭이 무엇인지, 그리고 정보에 근거하여 Azure API Management 인스턴스를 확장할지 여부를 결정하는 방법을 설명합니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.openlocfilehash: fe77361c4c9bed9310f8443ed4ff37faf7ea53a9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658328"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Azure API Management 인스턴스의 용량

**용량**은 부하를 더 많이 수용하기 위해 정보에 근거하여 API Management 인스턴스를 확장할지 여부에 대한 결정을 내리는 데 가장 중요한 단일 [Azure Monitor 메트릭](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis)입니다. 이러한 구성은 복잡하며 특정 동작을 적용합니다.

이 문서에서는 **용량**이 무엇이며 어떻게 동작하는지에 대해 설명합니다. Azure Portal에서 **용량** 메트릭에 액세스하는 방법을 보여주고 API Management 인스턴스에 대한 확장이나 업그레이드를 고려할 시기를 제안합니다.

## <a name="prerequisites"></a>필수 조건

이 문서의 단계를 따르려면 다음이 필요합니다.

+ 활성 Azure 구독.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ APIM 인스턴스. 자세한 내용은 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>용량이란?

![용량 메트릭](./media/api-management-capacity/capacity-ingredients.png)

**용량**은 APIM 인스턴스의 로드 표시기입니다. 여기에는 리소스 사용량(CPU, 메모리)과 네트워크 큐 길이가 반영됩니다. CPU와 메모리 사용량은 다음을 통해 리소스 사용량을 나타냅니다.

+ 요청 전달 또는 정책 실행을 포함하는 APIM 서비스(예: 관리 작업 또는 요청 처리)
+ 선택된 운영 체제 프로세스(새로운 연결에서 SSL 핸드셰이크 비용과 관련된 프로세스 포함)

총 **용량**은 API Management 인스턴스의 모든 단위에서 가져온 자체 값의 평균입니다.

## <a name="capacity-metric-behavior"></a>용량 메트릭 동작

용량의 구문 때문에, 실제 **용량**은 많은 변수의 영향을 받을 수 있습니다. 예를 들면:

+ 연결 패턴(요청 시 새 연결 생성 및 기존 연결 재사용)
+ 요청 및 응답의 크기
+ 요청을 보내는 각 API 또는 클라이언트 수에 관해 구성된 정책

요청에 대한 작업이 복잡할수록 **용량** 소비는 더 많아집니다. 예를 들어, 복잡한 변환 정책은 간단한 요청 전달보다 훨씬 많은 CPU를 소비합니다. 느린 백 엔드 서비스 응답도 이것을 증가시킵니다.

> [!IMPORTANT]
> **용량**은 처리 중인 요청 수에 대한 직접 측정값이 아닙니다.

![용량 메트릭 스파이크](./media/api-management-capacity/capacity-spikes.png)

**용량**은 처리 중인 요청이 없어도 간헐적으로 스파이크가 발생하거나 0보다 커질 수 있습니다. 이런 현상은 시스템 또는 플랫폼 전용 작업으로 인해 발생하며 인스턴스 확장 여부를 결정할 때 고려하지 않아야 합니다.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>Azure Portal을 사용하여 용량 검사
  
![용량 메트릭](./media/api-management-capacity/capacity-metric.png)  

1. [Azure Portal](https://portal.azure.com/)에서 APIM 인스턴스로 이동합니다.
2. **메트릭(미리 보기)** 를 선택합니다.
3. 자주색 섹션의 사용 가능한 메트릭에서 **용량** 메트릭을 선택하고 기본 **평균** 집계는 그대로 유지합니다.

    > [!TIP]
    > 잘못된 해석을 피하기 위해 항상 위치별 **용량** 메트릭 분석을 확인해야 합니다.

4. 녹색 섹션에서 메트릭을 차원으로 분할할 **위치**를 선택합니다.
5. 섹션 위쪽의 막대에서 원하는 시간 범위를 선택합니다.

    예상치 못한 상황이 발생할 때 알려주도록 메트릭 경고를 설정할 수 있습니다. 예를 들어 APIM 인스턴스에서 20 분이 넘게에 대 한 예상된 최대 용량을 초과 되었습니다에 하는 경우 알림을 가져옵니다.

    >[!TIP]
    > 서비스 용량이 부족한 경우 알리도록 알림을 구성하거나 Azure Monitor 자동 크기 조정 기능을 사용하여 Azure API Management 단위를 자동으로 추가할 수 있습니다. 크기 조정 작업에는 30분 정도가 소요될 수 있으니 여기에 맞게 규칙을 계획해야 합니다.  
    > 마스터 위치만 크기 조정이 허용됩니다.

## <a name="use-capacity-for-scaling-decisions"></a>용량을 사용하여 크기 조정 결정

**용량**은 부하를 더 많이 수용하기 위해 정보에 근거하여 API Management 인스턴스를 확장할지 여부에 대한 결정을 내리기 위한 메트릭입니다. 고려 사항:

+ 장기 추세와 평균을 살펴봅니다.
+ 부하 증가와 관련이 없는 급격한 스파이크는 무시합니다("용량 메트릭 동작" 섹션에서 자세한 내용 참조).
+ 긴 시간(예: 30분)동안 **용량**의 값이 60%나 70%를 초과하는 경우 인스턴스를 업그레이드하거나 크기를 조정합니다. 서비스 또는 시나리오에 따라 다른 값이 더 적당할 수 있습니다.

>[!TIP]  
> 미리 트래픽을 예측할 수 있으면 APIM 인스턴스를 예상 워크로드에서 테스트합니다. 테넌트에서 요청 로드를 점차적으로 늘리고 최대 부하에 해당하는 용량 메트릭의 값이 무엇인지 모니터링합니다. Azure Portal을 사용하여 특정 시간에 얼마나 많은 용량이 사용되는지 이해하려면 이전 섹션의 단계를 수행합니다.

## <a name="next-steps"></a>다음 단계

[Azure API Management 서비스 인스턴스의 크기를 조정하거나 업그레이드하는 방법](upgrade-and-scale.md)