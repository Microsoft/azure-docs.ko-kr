---
title: Azure Security Center에서 교차 테넌트 관리 Microsoft Docs
description: Azure 위임 된 리소스 관리를 사용 하 여 Security Center에서 여러 테넌트의 보안 상태를 관리 하는 테넌트 간 관리를 설정 하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 3dc7ebe224f1edb1b0fabc40441bd399fda6c133
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355581"
---
# <a name="cross-tenant-management-in-security-center"></a>Security Center의 교차 테넌트 관리

교차 테넌트 관리를 사용 하면 [Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md) 를 활용 하 여 Security Center에서 여러 테넌트의 보안 상태를 확인 하 고 관리할 수 있습니다. 각 테넌트의 디렉터리에 로그인 하지 않고도 단일 보기에서 여러 테넌트를 효율적으로 관리 합니다.

- 서비스 공급자는 해당 테넌트 내에서 여러 고객에 대 한 리소스의 보안 상태를 관리할 수 있습니다.

- 여러 테넌트가 있는 조직의 보안 팀은 단일 위치에서 보안 상태를 보고 관리할 수 있습니다.

## <a name="set-up-cross-tenant-management"></a>교차 테넌트 관리 설정

[Azure 위임 된 리소스 관리](../lighthouse/concepts/azure-delegated-resource-management.md)를 사용 하 여 자체 테넌트에 관리 되는 테넌트 리소스에 대 한 액세스 권한을 위임 하 여 교차 테넌트 관리

> [!NOTE]
> Azure 위임 리소스 관리는 Azure Lighthouse의 주요 구성 요소 중 하나입니다.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Security Center에서 교차 테넌트 관리는 어떻게 작동 하나요?

단일 테넌트에서 여러 구독을 관리 하는 것과 같은 방법으로 여러 테넌트 간의 구독을 검토 하 고 관리할 수 있습니다.

상단 메뉴 모음에서 필터 아이콘을 클릭 하 고 각 테넌트의 디렉터리에서 보려는 구독을 선택 합니다.

  ![테넌트 필터링](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

뷰와 작업은 기본적으로 동일 합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.

- **보안 정책 관리**: 한 보기에서 [정책을](tutorial-security-policy.md)사용 하 여 많은 리소스의 보안 상태를 관리 하 고, 보안 권장 사항을 사용 하 여 작업을 수행 하 고, 보안 관련 데이터를 수집 및 관리 합니다.
- **보안 점수 및 규정 준수 상태 개선**: 테넌트 간 가시성을 통해 모든 테넌트의 전반적인 보안 상태를 확인 하 고 각 테넌트의 [보안 점수](security-center-secure-score.md) 와 [규정 준수](security-center-compliance-dashboard.md) 상태를 가장 잘 향상 시킬 수 있습니다.
- **권장 사항**수정: 한 번에 다양 한 테넌트의 많은 리소스에 대 한 [권장](security-center-recommendations.md) 사항을 모니터링 하 고 수정 합니다. 그런 다음 모든 테넌트에 가장 높은 위험을 제시 하는 취약성을 즉시 다룰 수 있습니다.
- **경고 관리**: 다른 테넌트 전체에서 [경고](security-center-alerts-overview.md) 를 검색 합니다. 조치 가능한 [수정 단계](security-center-managing-and-responding-alerts.md)를 준수 하지 않는 리소스에 대 한 조치를 취합니다.

- **고급 클라우드 방어 기능 및 기타 기능 관리**: [JIT (just-in-time) VM 액세스](security-center-just-in-time.md), [적응 네트워크 강화](security-center-adaptive-network-hardening.md), [적응 응용 프로그램 제어](security-center-adaptive-application.md)등의 다양 한 위협 검색 및 보호 서비스를 관리 합니다.
 
## <a name="next-steps"></a>다음 단계
이 문서에서는 Security Center에서 교차 테넌트 관리를 작동 하는 방법을 설명 합니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center 사용](security-center-monitoring.md) 하 여 보안 상태를 강화 하 고 Azure 리소스의 상태를 모니터링 하는 방법을 알아보세요.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [엔터프라이즈 시나리오의 Azure Lighthouse에 대해 알아보기](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) -azure Lighthouse에서 여러 azure AD 테넌트를 사용 하는 엔터프라이즈 내에서 교차 테넌트 관리를 간소화 하는 방법을 알아봅니다.