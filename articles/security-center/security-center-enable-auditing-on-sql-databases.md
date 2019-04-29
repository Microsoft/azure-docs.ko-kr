---
title: Azure Security Center에서 SQL Database에 대한 감사 및 위협 감지 사용 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **SQL Database에 감사 및 위협 감지 사용**을 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 224b6755-2b36-4ecd-9af8-139a198e0df1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 1108265101f37433860d0112e4e80aee0002ab5c
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127228"
---
# <a name="enable-auditing-and-threat-detection-on-sql-databases-in-azure-security-center"></a>Azure Security Center에서 SQL Database에 대한 감사 및 위협 감지 사용
Azure Security Center가 감사 및 위협 감지를 아직 사용하도록 설정하지 않은 경우 모든 SQL 데이터베이스에 대한 감사 및 위협 감지를 켜는 것이 좋습니다. 감사 및 위협 감지는 규정 준수를 유지 관리하고, 데이터베이스 작업을 이해하고, 비즈니스 문제나 의심스러운 보안 위반을 나타낼 수 있는 불일치 및 이상 활동을 파악하는 데 도움이 될 수 있습니다.

감사를 설정한 후에는 보안 경고를 받을 수 있도록 위협 검색 설정 및 전자 메일을 구성할 수 있습니다. 위협 감지는 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 감지합니다. 이렇게 하면 잠재적인 위협 요소가 발생할 때 검색하고 대처할 수 있습니다.

이러한 권장 지침은 Azure SQL 서비스에만 적용되며 가상 머신에서 실행되는 SQL은 포함하지 않습니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  단계별 가이드는 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항** 블레이드에서 **SQL Database에 감사 및 위협 감지 활성화**를 선택합니다.  이렇게 하면 **SQL 데이터베이스에 감사 및 위협 감지 활성화** 블레이드가 열립니다.

   ![SQL 데이터베이스에 감사 활성화][1]
2. 감사를 사용하도록 설정할 SQL 데이터베이스를 선택합니다. 그러면 **감사 및 위협 감지** 블레이드가 열립니다.

3. **감사 및 위협 감지** 블레이드의 **감사**에서 **켜기**를 선택합니다.

   ![감사 및 위협 감지 켜기][2]
4. [Azure Portal에서 SQL Database 위협 감지](../sql-database/sql-database-threat-detection-portal.md)의 단계에 따라 위협 감지를 켜고 구성하며, 비정상적인 활동이 검색될 때 보안 경고가 수신되는 전자 메일 목록을 구성할 수 있습니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 "SQL 데이터베이스에 감사 및 위협 감지 활성화"를 구현하는 방법을 보여 주었습니다. SQL 데이터베이스 보안 유지에 대해 자세히 알아보려면 다음을 참조하세요.

* [SQL Database 보안 설정](../sql-database/sql-database-security-overview.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) -- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-databases/enable-auditing-on-sql-databases.png
[2]: ./media/security-center-enable-auditing-on-sql-databases/auditing-threat-detection-blade.png
