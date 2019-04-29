---
title: Azure Security Center에서 ID 및 액세스 모니터링 | Microsoft Docs
description: Azure Security Center의 ID 및 액세스 기능을 사용하여 사용자의 액세스 작업과 ID 관련 문제를 모니터링하는 방법을 알아봅니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7cb8d494d32f2a9c192418bfab2d5ca9b679c5fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704988"
---
# <a name="monitor-identity-and-access-in-azure-security-center-preview"></a>Azure Security Center에서 ID 및 액세스 모니터링(미리 보기)
이 문서를 통해 Azure Security Center를 사용하여 사용자의 ID를 모니터링하고 작업에 액세스할 수 있습니다.

> [!NOTE]
> ID 및 액세스 모니터링은 미리 보기로 제공되며 Security Center의 표준 계층에서만 사용할 수 있습니다. Security Center의 가격 책정 계층에 대해 자세히 알아보려면 [가격 책정](security-center-pricing.md)을 참조하세요.
>

ID는 엔터프라이즈의 제어 평면으로서 ID 보호가 최우선 순위가 되어야 합니다. 보안 경계가 네트워크 경계에서 ID 경계로 발전되었습니다. 보안 수준이 네트워크 보호에 대해서는 낮아지고 데이터를 보호하고 앱과 사용자의 보안을 관리하는 방법에 대해서는 높아집니다. 오늘날에는 추가 데이터와 앱을 클라우드로 이동하면 ID는 새 경계가 됩니다.

ID 작업을 모니터링하여 문제가 발생하기 전에 사전 대응 조치를 취하거나 사후 대응을 통해 공격 시도를 중단할 수 있습니다. ID 및 액세스 대시보드는 다음과 같은 권장 사항을 제공합니다.

- 구독에 대한 권한이 있는 계정에 대해 MFA 사용
- 구독에서 쓰기 권한이 있는 외부 계정 제거
- 구독에서 권한 있는 외부 계정 제거

> [!NOTE]
> 구독에 600개 초과의 계정이 있는 경우 Security Center는 사용자 구독에 대해 ID 권장 사항을 실행할 수 없습니다. 실행되지 않은 권장 사항은 “사용할 수 없는 평가”에 나열되며 아래에서 논의됩니다.
Security Center는 CSP(클라우드 솔루션 공급자) 파트너의 관리 에이전트에 대해 ID 권장 사항을 실행할 수 없습니다.
>
>

Security Center에서 제공되는 ID 및 액세스 권장 사항의 목록에 대한 [권장 사항](security-center-identity-access.md#recommendations)을 참조하세요.

## <a name="monitoring-security-health"></a>보안 상태 모니터링
**Security Center - 개요** 대시보드에서 리소스의 보안 상태를 모니터링할 수 있습니다. **리소스** 섹션은 각 리소스 유형에 대한 심각도 보여 주는 상태 표시기입니다.

**권장 사항**을 선택하면 모든 문제 목록을 볼 수 있습니다 합니다. **리소스**에서 계산 및 앱, 데이터 보안, 네트워킹 또는 ID 및 액세스에 관련된 문제 목록을 볼 수 있습니다. 권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure Security Center에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

ID 및 액세스 권장 사항의 전체 목록은 [권장 사항](security-center-identity-access.md#recommendations)을 참조하세요.

계속하려면 **리소스** 또는 Security Center 주 메뉴에서 **ID 및 액세스**를 선택합니다.

![Security Center 대시보드][1]

## <a name="monitor-identity-and-access"></a>ID 및 액세스 모니터링
**ID 및 액세스**에는 두 개의 탭이 있습니다.

- **개요**: Security Center에 의해 식별된 권장 사항입니다.
- **구독**: 구독의 목록 및 각각의 현재 보안 상태입니다.

![ID 및 액세스][2]

### <a name="overview-section"></a>개요 섹션
**개요**에 권장 사항의 목록이 있습니다. 첫 번째 열에는 권장 사항이 나열되고, 두 번째 열에는 해당 권장 사항의 영향을 받은 구독의 총 수가 표시됩니다. 세 번째 열은 문제의 심각도를 보여 줍니다.

1. 권장 사항을 선택합니다. 권장 사항의 창이 열리고 다음이 표시됩니다.

   - 권장 사항의 설명
   - 비정상 및 정상 상태의 구독 목록
   - 실패한 평가로 인해 검색되지 않는 리소스 또는 무료 계층에서 실행되는 구독에 있는 리소스와 평가되지 않은 리소스의 목록

   ![권장 사항의 창][3]

1. 추가 세부 정보는 목록에서 구독을 선택합니다.

### <a name="subscriptions-section"></a>구독 섹션
**구독**에는 구독의 목록이 있습니다. 첫 번째 열에는 구독이 나열되고, 두 번째 열에는 각 구독에 대한 권장 사항의 총 수를 보여 줍니다. 세 번째 열은 문제의 심각도를 보여 줍니다.

![구독의 탭][4]

1. 구독을 선택합니다. 요약 보기는 다음과 같은 세 개의 탭으로 열립니다.

   - **권장 사항**: 실패한 Security Center에서 수행된 평가를 기반으로 합니다.
   - **통과된 평가**: 전달된 Security Center에서 수행된 평가의 목록입니다.
   - **사용할 수 없는 평가**: 오류로 인해 또는 구독에 600개 초과의 계정이 있어서 실행에 실패한 평가 목록입니다.

   **권장 사항**에는 선택한 구독에 대한 권장 사항 목록과 각 권장 사항의 심각도가 표시됩니다.

   ![구독 선택을 위한 권장 사항][5]

1. 권장 사항의 설명, 비정상 및 정상 구독의 목록 및 검사되지 않은 리소스의 목록을 보려면 권장 사항을 선택합니다.

   ![권장 사항의 설명][6]

   **통과된 평가**에는 통과된 평가의 목록이 나와 있습니다.  이러한 평가의 심각도는 항상 녹색입니다.

   ![통과된 평가][7]

1. 평가의 설명 및 정상 구독 목록을 보려면 목록에서 통과된 평가를 선택합니다. 실패한 모든 구독을 나열하는 비정상 구독에 대한 탭이 있습니다.

   ![통과된 평가][8]

## <a name="recommendations"></a>권장 사항
아래 테이블을 참조로 사용하여 사용 가능한 ID 및 액세스 권장 사항을 이해하고, 적용할 경우 어떻게 되는지 이해할 수 있습니다.

|리소스 종류|보안 점수|권장 사항|설명|
|----|----|----|----|
|구독|50|구독에 대한 소유자 권한이 있는 Azure Management 앱 계정에 대해 MFA 사용|계정 또는 리소스 위반을 방지하려면 관리자 권한이 있는 모든 구독 계정에 대해 MFA(Multi-factor Authentication)를 사용합니다.|
|구독|50|구독에서 Security Center를 사용하도록 설정 |지능형 위협 탐지, JIT, 애플리케이션 허용 목록 및 고급 권장 사항에 대해 모든 구독에서 Security Center를 사용하도록 설정합니다. |
|구독|50|구독에서 Security Center 표준 계층을 사용하도록 설정 |지능형 위협 탐지, JIT, 애플리케이션 허용 목록 및 고급 권장 사항에 대해 모든 구독에서 Security Center 표준 계층을 사용하도록 설정합니다.|
|구독|40|구독에 대한 쓰기 권한이 있는 Azure Management 앱 계정에 대해 MFA 사용|계정 또는 리소스 위반을 방지하려면 쓰기 권한이 있는 모든 구독 계정에 대해 MFA(Multi-factor Authentication)를 사용합니다.|
|구독|30|구독에서 소유자 권한이 있는 외부 계정 제거|모니터링되지 않는 액세스를 방지하려면 소유자 권한이 있는 외부 계정을 구독에서 제거합니다. |
|구독|30|구독에 대한 읽기 권한이 있는 Azure Management 앱 계정에 대해 MFA 사용|계정 또는 리소스 위반을 방지하려면 읽기 권한이 있는 모든 구독 계정에 대해 MFA(Multi-factor Authentication)를 사용합니다.|
|구독|25|구독에서 쓰기 권한이 있는 외부 계정 제거|모니터링되지 않는 액세스를 방지하려면 쓰기 권한이 있는 외부 계정을 구독에서 제거합니다. |
|구독|20|구독에서 소유자 권한이 있는 사용되지 않는 계정 제거|구독에서 소유자 권한이 있는 사용되지 않는 계정을 제거합니다.|
|구독|5|구독에서 더 이상 사용되지 않는 계정 제거|현재 사용자에 대해서만 액세스할 수 있게 구독에서 더 이상 사용되지 않는 계정을 제거합니다. |
|구독|5|하나의 구독에 둘 이상의 소유자 지정|관리자 액세스 중복성을 유지하려면 둘 이상의 구독 소유자를 지정합니다.|
|구독|5|구독에 최대 3명의 소유자 지정|보안이 침해된 소유자의 위반 가능성을 줄이려면 최소 3명의 구독 소유자를 지정합니다.|
|주요 자격 증명 모음|5|Key Vault에서 진단 로그 사용|로그를 사용하도록 설정하고 최대 1년 간 보존합니다. 이렇게 하면 보안 인시던트가 발생하거나 네트워크가 손상된 경우 조사 목적으로 활동 내역을 다시 만들 수 있습니다. |
|구독|15|구독에서 읽기 권한이 있는 외부 계정 제거|모니터링되지 않는 액세스를 방지하려면 읽기 권한이 있는 외부 계정을 구독에서 제거합니다.|
|구독|1|보안 연락처 세부 정보 제공|각 구독에 대한 보안 연락처 정보를 제공합니다. 연락처 정보는 전자 메일 주소 및 전화 번호입니다. 정보는 보안 팀에서 리소스가 손상된 것을 발견한 경우에 고객에게 연락하는 데 사용됩니다.|

> ![참고] MFA는 필요하지만 배제 집합이 있는 조건부 액세스 정책을 만든 경우 일부 사용자가 MFA 없이 Azure에 로그인할 수 있기 때문에 Security Center MFA 권장 평가에서 이 정책을 비준수라고 간주합니다. 
>

## <a name="next-steps"></a>다음 단계
다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

- [Azure Security Center에서 머신 및 애플리케이션 보호](security-center-virtual-machine-recommendations.md)
- [Azure Security Center에서 네트워크 보호](security-center-network-recommendations.md)
- [Azure Security Center에서 Azure SQL 서비스 및 데이터 보호](security-center-sql-service-recommendations.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Security Center에서 보안 경고 관리 및 응답](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. Security Center 사용에 관한 질문과 대답에 대한 답을 찾습니다.


<!--Image references-->
[1]: ./media/security-center-identity-access/overview.png
[2]: ./media/security-center-identity-access/identity-dashboard.png
[3]: ./media/security-center-identity-access/select-subscription.png
[4]: ./media/security-center-identity-access/subscriptions.png
[5]: ./media/security-center-identity-access/recommendations.png
[6]: ./media/security-center-identity-access/designate.png
[7]: ./media/security-center-identity-access/passed-assessments.png
[8]: ./media/security-center-identity-access/remove.png
