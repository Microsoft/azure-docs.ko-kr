---
title: Azure 데이터베이스 보안 검사 목록 | Microsoft Docs
description: 이 문서에서는 Azure 데이터베이스 보안에 대한 일단의 검사 목록을 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 424453e70e5b62e408f408cd5ae8169cddb14dd7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121694"
---
# <a name="azure-database-security-checklist"></a>Azure 데이터베이스 보안 검사 목록

Azure 데이터베이스에는 보안을 향상시키기 위해 액세스를 제한하고 제어하는 데 사용할 수 있는 다양한 기본 제공 보안 컨트롤이 포함되어 있습니다.

내용은 다음과 같습니다.

-   IP 주소로 연결을 제한하는 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)을 만들 수 있는 방화벽
-   Azure Portal에서 액세스할 수 있는 서버 수준 방화벽
-   SSMS에서 액세스할 수 있는 데이터베이스 수준 방화벽 규칙
-   보안 연결 문자열을 사용하여 데이터베이스에 대한 보안 연결
-   액세스 관리 사용
-   데이터 암호화.
-   SQL Database 감사
-   SQL Database 위협 검색

## <a name="introduction"></a>소개
클라우드 컴퓨팅에는 다양한 애플리케이션 사용자, 데이터베이스 관리자 및 프로그래머에게 익숙하지 않은 새로운 보안 패러다임이 필요합니다. 그 결과로 일부 조직에서는 인식된 보안 위험으로 인해 데이터 관리를 위한 클라우드 인프라를 구현하는 것을 망설일 수 있습니다. 그러나 Microsoft Azure 및 Microsoft Azure SQL Database에 기본 제공된 보안 기능을 더 잘 이해하면 이러한 문제의 대부분을 완화할 수 있습니다.

## <a name="checklist"></a>검사 목록
이 검사 목록을 검토하기 전에 [Azure 데이터베이스 보안 모범 사례](https://docs.microsoft.com/azure/security/azure-database-security-best-practices) 문서를 참조하는 것이 좋습니다. 모범 사례를 이해하면 이 검사 목록을 최대한 활용할 수 있습니다. 따라서 이 검사 목록을 사용하여 Azure 데이터베이스 보안의 중요한 문제를 해결했는지 확인할 수 있습니다.


|검사 목록 범주| 설명|
| ------------ | -------- |
|**데이터 보호**||
| <br> 진행 중/전송 중 암호화| <ul><li>[전송 계층 보안](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol) - 데이터가 네트워크로 이동할 때의 데이터 암호화용</li><li>데이터베이스에는 TLS(전송 계층 보안)를 통한 [TDS(Tabular Data Stream)](https://msdn.microsoft.com/library/dd357628.aspx) 프로토콜에 기반한 클라이언트로부터의 보안 통신이 필요합니다.</li></ul> |
|<br>휴지 상태의 암호화| <ul><li>[투명한 데이터 암호화](https://go.microsoft.com/fwlink/?LinkId=526242) - 비활성 데이터가 디지털 형식으로 물리적으로 저장되는 경우</li></ul>|
|**액세스 제어**||  
|<br> 데이터베이스 액세스 | <ul><li>[인증](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)(Azure Active Directory 인증) - AD 인증은 Azure Active Directory에서 관리되는 ID를 사용합니다.</li><li>[권한 부여](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) - 사용자에게 필요한 최소 권한을 부여합니다.</li></ul> |
|<br>애플리케이션 액세스| <ul><li>[행 수준 보안](https://msdn.microsoft.com/library/dn765131) - 보안 정책을 사용하는 동시에 사용자의 ID, 역할 또는 실행 컨텍스트에 따라 행 수준 액세스를 제한합니다.</li><li>[동적 데이터 마스킹](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) - 권한 및 정책을 사용하여 권한이 없는 사용자에게 중요한 데이터를 마스킹함으로써 이러한 데이터의 노출을 제한합니다.</li></ul>|
|**사전 모니터링**||  
| <br>추적 및 검색| <ul><li>[감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) - 데이터베이스 이벤트를 추적하고 [Azure Storage 계정](https://docs.microsoft.com/azure/storage/storage-create-storage-account)의 감사 로그/활동 로그에 이벤트를 기록합니다.</li><li>[Azure Monitor 활동 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)를 사용하여 Azure 데이터베이스 상태를 추적합니다.</li><li>[위협 검색](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) - 데이터베이스에 대한 잠재적인 보안 위협을 나타내는 비정상적인 데이터베이스 활동을 검색합니다. </li></ul> |
|<br>Azure Security Center| <ul><li>[데이터 모니터링](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases) - SQL 및 기타 Azure 서비스에 대한 중앙 집중식 보안 모니터링 솔루션으로 Azure Security Center를 사용합니다.</li></ul>|       

## <a name="conclusion"></a>결론
Azure 데이터베이스는 다양한 조직 및 규정 준수 요구 사항을 충족하는 모든 보안 기능을 갖춘 강력한 데이터베이스 플랫폼입니다. 데이터에 대한 물리적 액세스를 제어하고 투명한 데이터 암호화, 셀 수준 암호화 또는 행 수준 보안이 포함된 다양한 데이터 보안 옵션을 파일, 열 또는 행 수준에서 사용하여 데이터를 쉽게 보호할 수 있습니다. 또한 Always Encrypted를 사용하면 암호화된 데이터에 대한 작업을 수행하여 애플리케이션 업데이트 프로세스를 간소화할 수 있습니다. 따라서 SQL Database 활동의 감사 로그에 액세스하면 필요한 정보를 얻을 수 있으므로 데이터에 액세스하는 방법과 시기를 알 수 있습니다.

## <a name="next-steps"></a>다음 단계
몇 가지 간단한 단계만 거치면 악의적인 사용자 또는 무단 액세스로부터 데이터베이스를 보호하는 기능을 크게 향상시킬 수 있습니다. 이 자습서에서는 다음에 대해 알아봅니다.

- 서버 및/또는 데이터베이스에 대한 [방화벽 규칙](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) 설정
- [암호화](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption)를 사용하여 데이터 보호
- [SQL Database 감사](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) 사용

