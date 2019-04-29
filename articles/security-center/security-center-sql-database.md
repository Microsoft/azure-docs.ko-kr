---
title: Azure Security Center 및 Azure SQL Database 서비스 | Microsoft Docs
description: 이 문서에서는 Security Center가 Azure SQL Database 서비스에서 데이터를 보호하는 데 어떻게 도움이 되는지 보여 줍니다.
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704614"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center 및 Azure SQL Database 서비스
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/)를 통해 위협을 예방하고 감지하며 대응할 수 있습니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

이 문서에서는 Security Center가 Azure SQL Database 서비스에서 데이터를 보호하는 데 어떻게 도움이 되는지 보여 줍니다.

## <a name="why-use-security-center"></a>보안 센터를 사용해야 하는 이유
보안 센터를 사용하면 모든 서버 및 데이터베이스의 보안에 가시성을 제공하여 SQL Database에서 데이터를 보호할 수 있습니다. 보안 센터를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

* SQL Database 암호화 및 감사를 위한 정책을 정의합니다.
* 모든 구독 간에 SQL Database 리소스의 보안을 모니터링합니다.
* 보안 문제를 신속하게 파악하고 해결합니다.
* [Azure SQL Database 위협 감지](../sql-database/sql-database-threat-detection.md)에 대한 경고를 통합합니다.

Security Center는 SQL Database 리소스를 보호하는 것 외에도, Azure 가상 머신, Cloud Services, App Services, 가상 네트워크 등에 대한 보안 모니터링 및 관리도 제공합니다. [여기](security-center-intro.md)에서 보안에 대해 자세히 알아보세요.

## <a name="prerequisites"></a>필수 조건
보안 센터를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 보안 센터의 무료 계층은 구독을 사용하여 사용하도록 설정됩니다. 보안 센터의 무료 및 표준 계층에 대한 자세한 내용은 [보안 센터 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)을 참조하세요.

보안 센터는 역할 기반 액세스를 지원합니다. 역할 기반 액세스 제어(RBAC)에 대해 자세히 알아보려면 [Azure Active Directory 역할 기반 Access Control](../role-based-access-control/role-assignments-portal.md)을 참조하세요. 보안 센터 FAQ에서는 [보안 센터에서 권한 처리 방법](security-center-faq.md#permissions)에 대한 정보를 제공합니다.

## <a name="access-security-center"></a>보안 센터 엑세스
[Azure 포털](https://azure.microsoft.com/features/azure-portal/)에서 Security Center에 액세스합니다. [포털에 로그인](https://portal.azure.com/)하고 **보안 센터 옵션**을 선택합니다.

![보안 센터 옵션][1]

**보안 센터** 블레이드가 열립니다.
![보안 센터 블레이드][2]

## <a name="set-security-policy"></a>보안 정책 설정
보안 정책은 지정된 구독 또는 리소스 그룹 내에서 리소스에 대해 권장되는 제어 집합을 정의합니다. 보안 센터에서 회사의 보안 요구 사항 및 애플리케이션 유형 또는 각 구독의 데이터 민감도에 따라 구독 또는 리소스 그룹에 대한 정책을 정의합니다.

SQL 감사 및 SQL 투명한 데이터 암호화(TDE)에 대한 권장 사항을 표시하는 정책을 설정할 수 있습니다.

* **SQL 감사 및 위협 감지**를 켠 경우, Security Center에서는 규정 준수, 고급 감지 및 조사를 위해 Azure 데이터베이스에 대한 액세스 감사를 활성화하는 것이 좋습니다.
* **SQL 투명한 데이터 암호화**를 켠 경우, Security Center에서는 Azure SQL Database, 연결된 백업 및 트랜잭션 로그 파일에 대해 휴지 상태의 암호화를 활성화하는 것이 좋습니다.

보안 정책을 설정하려면 보안 센터 블레이드에서 **정책** 타일을 선택합니다. **보안 정책** 블레이드에서 보안 정책을 사용하도록 설정하려는 구독을 선택합니다. **방지 정책**을 선택하고 이 구독에서 사용할 보안 권장 사항을 **켭니다**.
![보안 정책][3]

자세한 내용은 [보안 정책 설정](tutorial-security-policy.md)을 참조하세요.

## <a name="manage-security-recommendation"></a>보안 권장 사항 관리
보안 센터에서는 Azure 리소스의 보안 상태를 주기적으로 분석합니다. 보안 센터가 잠재적인 보안 취약점을 식별하는 경우 권장 사항을 만듭니다. 권장 사항은 필요한 컨트롤을 구성하는 과정을 안내합니다.

보안 정책이 설정되면 보안 센터는 리소스의 보안 상태를 분석하여 잠재적인 취약성을 식별합니다. 권장 사항은 각 줄이 한 가지 특정 권장을 나타내는 표 형식으로 표시됩니다. 다음 표를 참조로 활용하여 Azure SQL Database에 대해 사용 가능한 권장 사항이 무엇이고 적용할 경우 각 권장 사항을 통해 어떤 작업이 수행되는지 확인합니다. 권장 사항을 선택하면 보안 센터에서 권장 사항을 구현하는 방법을 설명하는 문서로 이동합니다.

| 권장 사항 | 설명 |
| --- | --- |
| [SQL Server에서 감사 및 위협 감지 사용](security-center-enable-auditing-on-sql-servers.md) |SQL Database 서버에 대한 감사 및 위협 감지를 켜는 것이 좋습니다. (SQL Database 서비스만 해당. 가상 머신에서 실행 중인 Microsoft SQL Server는 포함하지 않음) |
| [SQL 데이터베이스에서 감사 및 위협 감지 사용](security-center-enable-auditing-on-sql-databases.md) |SQL Database 데이터베이스에 대한 감사 및 위협 감지를 켜는 것이 좋습니다. (SQL Database 서비스만 해당. 가상 머신에서 실행 중인 Microsoft SQL Server는 포함하지 않음) |
| [투명한 데이터 암호화 사용](security-center-enable-transparent-data-encryption.md) |SQL Database에 대해 암호화를 활성화하는 것이 좋습니다. (SQL Database 서비스만 해당) |

Azure 리소스에 대한 권장 사항을 보려면 Security Center 블레이드에서 **권장 사항** 타일을 선택합니다. **권장 사항** 블레이드에서 자세한 내용을 볼 권장 사항을 선택합니다. 이 예제에서는 **SQL Server에서 감사 및 위협 감지 사용**을 선택하겠습니다.

![권장 사항][4]

아래와 같이 보안 센터에서 감사 및 위협 감지가 사용되지 않은 SQL Server를 보여 줍니다. 감사를 켠 후에는 보안 경고를 받을 수 있도록 위협 감지 설정 및 전자 메일 설정을 구성할 수 있습니다. 위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지할 때 이를 경고합니다. 경고는 보안 센터 대시보드에 표시됩니다.
![감사 및 위협 감지][5]

[Azure Portal에서 SQL Database 위협 감지](../sql-database/sql-database-threat-detection-portal.md)의 단계에 따라 위협 감지를 켜고 구성하며, 비정상적인 활동이 검색될 때 보안 경고가 수신되는 전자 메일 목록을 구성할 수 있습니다.

권장 사항에 대한 자세한 내용은 [보안 권장 사항 관리](security-center-recommendations.md)를 참조하세요.

## <a name="monitor-security-health"></a>보안 상태 모니터링
구독의 리소스에 대한 [보안 정책](tutorial-security-policy.md) 을 활성화한 후에 보안 센터는 리소스의 보안을 분석하여 잠재적 취약성을 식별합니다.  **리소스 보안 상태** 타일에서 리소스의 보안 상태를 볼 수 있습니다. **리소스 보안 상태** 타일에서 **데이터**를 클릭하면 **데이터 리소스** 블레이드가 열려 감사, 활성화되지 않은 투명한 데이터 암호화와 같은 문제에 대한 SQL 권장 사항을 보여 줍니다. 또한 데이터베이스의 일반 성능 상태에 대한 권장 사항이 있습니다.
![리소스 보안 상태][6]

자세한 내용은 [보안 상태 모니터링](security-center-monitoring.md)을 참조하세요.

## <a name="manage-and-respond-to-security-alerts"></a>보안 경고 관리 및 응답
Security Center는 [Azure SQL 위협 감지](../sql-database/sql-database-threat-detection.md) 및 기타 Azure 리소스에서 자동으로 로그 데이터를 수집, 분석 및 통합하여 실제 위협을 감지하고 거짓 긍정을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다.

경고를 보려면 보안 센터 블레이드에서 **보안 경고** 타일을 선택합니다. **보안 경고** 블레이드에서 경고를 선택하고 해당 경고를 트리거하는 이벤트 및 공격을 완화하기 위해 수행해야 하는 단계(있는 경우)에 대해 자세히 알아봅니다. 이 예제에서는 **잠재적인 SQL 삽입**을 선택하겠습니다.
![보안 경고][7]

아래와 같이, Security Center에서는 수정 방법에 대한 원본 IP 주소 및 권장 사항을 적용할 수 있는 경우 경고인 대상 리소스를 트리거한 항목에 대한 정보를 제공하는 추가 정보를 제공합니다.
![잠재적인 SQL 삽입][8]

자세한 내용은 [보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [보안 센터 계획 및 작업 가이드](security-center-planning-and-operations-guide.md) - 조직의 보안 요구 사항과 클라우드 관리 모델에 따라 보안 센터의 사용을 최적화하기 위한 일련의 단계 및 작업을 따릅니다.
* [Security Center 데이터 보안](security-center-data-security.md) – Security Center에서 구성 정보, 메타데이터, 이벤트 로그, 크래시 덤프 파일 등을 포함한 Azure 리소스에 대한 데이터를 수집하고 처리하는 방법을 알아봅니다.
* [Handling security incidents](security-center-incident.md) - 보안 센터에서 보안 경고 기능을 사용하여 보안 문제를 처리하는 데 도움이 되는 방법을 알아봅니다.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
