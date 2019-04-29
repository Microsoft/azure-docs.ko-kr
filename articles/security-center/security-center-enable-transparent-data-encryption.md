---
title: Azure Security Center에서 투명한 데이터 암호화 사용 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **투명한 데이터 암호화 사용**을 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704035"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Azure Security Center에서 투명한 데이터 암호화 사용
Azure Security Center가 아직 TDE(투명한 데이터 암호화)를 사용하도록 설정하지 않은 경우 SQL 데이터베이스에서 TDE를 사용하도록 설정하는 것이 좋습니다. TDE는 데이터를 보호하며, 애플리케이션을 변경할 필요 없이 휴지 상태의 데이터베이스, 연결된 백업 및 트랜잭션 로그 파일을 암호화하여 준수 요구를 충족하도록 도와줍니다. 자세한 내용은 [Azure SQL Database를 사용한 투명한 데이터 암호화](https://msdn.microsoft.com/library/dn948096)를 참조하세요.

이러한 권장 지침은 Azure SQL 서비스에만 적용되며 가상 머신에서 실행되는 SQL은 포함하지 않습니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  단계별 가이드는 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항** 블레이드에서 **투명한 데이터 암호화 사용**을 선택합니다.
   ![투명한 데이터 암호화 사용][1]
2. 이렇게 하면 **SQL 데이터베이스에서 투명한 데이터 암호화 사용** 블레이드가 열립니다. TDE를 사용하도록 설정할 SQL 데이터베이스를 선택합니다.
   ![SQL DB를 선택하여 TDE 활성화][2]
3. **투명한 데이터 암호화** 블레이드의 데이터 암호화에서 **켜기**를 선택하고 블레이드의 위쪽 리본에서 **저장**을 선택합니다.
   ![TDE 켜기][3]

   선택한 SQL Database에서 TDE가 사용되도록 설정되면 **암호화 상태**가 **암호화됨**으로 변경됩니다.    

   ![암호화 상태][4]

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 "투명한 데이터 암호화 사용"을 구현하는 방법을 보여 주었습니다. SQL TDE에 대한 자세한 내용은 다음을 참조하세요.

* [Azure SQL Database를 사용한 투명한 데이터 암호화](https://msdn.microsoft.com/library/dn948096)
* [투명한 데이터 암호화(TDE) 시작](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) -- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
