---
title: Azure Security Center에서 보안 권장 사항 관리 | Microsoft Docs
description: 이 문서에서는 Azure Security Center의 권장 사항이 Azure 리소스를 보호하고 보안 정책을 준수하는 데 어떤 도움이 되는지 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/13/2018
ms.author: rkarlin
ms.openlocfilehash: e3b4da1c1d835e9d630c000055af058aa7b45968
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60905945"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Azure Security Center에서 보안 권장 사항 관리
이 문서에서는 Azure Security Center의 권장 사항을 사용하여 Azure 리소스를 보호하는 데 도움이 되는 방법을 안내합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  이 문서는 단계별 가이드가 아닙니다.
>
>

## <a name="what-are-security-recommendations"></a>보안 권장 사항이란?
Security Center에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다.

## <a name="implementing-security-recommendations"></a>보안 권장 사항 구현
### <a name="set-recommendations"></a>권장 사항 설정
[Azure Security Center의 보안 정책 설정](tutorial-security-policy.md)에서 다음을 배울 수 있습니다.

* 보안 정책 구성.
* 데이터 수집 사용.
* 보안 정책의 일부로 표시될 권장 사항 선택.

현재 정책 권장 사항은 시스템 업데이트, 기준 규칙, 맬웨어 방지 프로그램, 서브넷 및 네트워크 인터페이스의 [네트워크 보안 그룹](../virtual-network/security-overview.md), SQL 데이터베이스 감사, SQL 데이터베이스 투명한 데이터 암호화 및 웹 애플리케이션 방화벽에 중점을 두고 있습니다.  [보안 정책 설정](tutorial-security-policy.md) 은 각 권장 사항 옵션에 대한 설명을 제공합니다.

### <a name="monitor-recommendations"></a>권장 사항 모니터링
보안 정책이 설정되면 보안 센터는 리소스의 보안 상태를 분석하여 잠재적인 취약성을 식별합니다. **개요**의 **권장 사항** 타일은 Security Center에서 식별된 권장 사항의 총 수를 알려줍니다.

![권장 사항 타일][1]

각 권장 사항에 대한 세부 정보를 보려면 **개요**에서 **권장 사항** 타일을 선택합니다. **권장 사항**이 열립니다.

![필터 권장 사항][2]

권장 사항을 필터링할 수 있습니다. 권장 사항을 필터링하려면 **권장 사항** 블레이드에서 **필터**를 선택합니다. **필터** 블레이드가 열리면 확인하려는 심각도 및 상태 값을 선택합니다.


* **권장 사항**: 권장 사항입니다.
* **보안 점수 영향**:
* **리소스**: 이 권장 사항이 적용되는 리소스를 나열합니다.
* **상태 표시줄**:  특정 권장 사항의 심각도를 설명합니다.
   * **높음 (빨강)**: 중요한 리소스(애플리케이션, VM 또는 네트워크 보안 그룹 등)에 취약점이 있으며 주의가 필요합니다.
   * **중간 (주황색)**: 취약점이 있으며 중요하지 않은 또는 추가 단계가 프로세스를 완료하거나 취약점을 제거하는 데 필요합니다.
   * **낮음 (파란색)**: 해결해야 하지만 즉시 조치하지 않아도 되는 취약점이 있습니다. (기본적으로 낮은 권장 사항은 표시되지 않지만 표시하려는 경우 낮은 권장 사항으로 필터링할 수 있습니다.) 
   * **정상 (녹색)**:
   * **사용할 수 없습니다 (회색)**:
 


> [!NOTE]
> Azure 리소스의 [클래식 및 Resource Manager 배포 모델](../azure-classic-rm.md) 을 이해하게 됩니다.
> 
> 
> ### <a name="apply-recommendations"></a>권장 사항 적용
> 모든 권장 사항을 검토한 후에 가장 먼저 적용해야 할 권장 사항을 결정합니다. 심각도 등급을 기본 매개 변수로 사용하여 가장 먼저 적용해야 할 권장 사항을 평가하는 것이 좋습니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 보안 센터의 보안 권장 사항을 소개했습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) — Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) - 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) - Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
