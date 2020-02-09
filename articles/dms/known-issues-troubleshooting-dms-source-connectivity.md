---
title: 원본 데이터베이스 연결 문제
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service 원본 데이터베이스에 연결 하는 것과 관련 된 알려진 문제/오류 문제를 해결 하는 방법에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: b697faeded4177381f70ebb9d1f93d928b25d7b1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437802"
---
# <a name="troubleshoot-dms-errors-when-connecting-to-source-databases"></a>원본 데이터베이스에 연결할 때 DMS 오류 문제 해결

다음 문서에서는 Azure Database Migration Service (DMS)를 원본 데이터베이스에 연결할 때 발생할 수 있는 잠재적인 문제를 해결 하는 방법에 대해 자세히 설명 합니다. 아래 각 섹션은 특정 유형의 원본 데이터베이스와 관련 되어 있으며, 세부 정보와 함께 발생할 수 있는 오류를 나열 하 고 연결 문제를 해결 하는 방법에 대한 정보를 제공 합니다.

## <a name="sql-server"></a>SQL Server

원본 SQL Server 데이터베이스에 연결 하는 것과 관련 된 잠재적인 문제 및 해결 방법에 대한 자세한 사항은 다음 표에 나와 있습니다.

| 오류         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| SQL 연결에 실패 했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지와 SQL Server가 원격 연결을 허용 하도록 구성 되어 있는지 확인 하십시오.<br> | 이 오류는 서비스가 원본 서버를 찾을 수 없는 경우에 발생 합니다. 이 문제를 해결 하려면 [동적 포트나 명명 된 인스턴스를 사용 하는 경우 원본 SQL Server에 연결 오류](https://docs.microsoft.com/azure/dms/known-issues-troubleshooting-dms#error-connecting-to-source-sql-server-when-using-dynamic-port-or-named-instance)문서를 참조 하세요. |
| **오류 53** -SQL 연결에 실패 했습니다. (오류 코드 1, 2, 5, 53, 233, 258, 1225, 11001)<br><br> | 이 오류는 서비스가 원본 서버에 연결할 수 없는 경우에 발생 합니다. 문제를 해결 하려면 다음 리소스를 참조 한 후 다시 시도 하십시오. <br><br>  [연결 문제를 해결 하기 위한 대화형 사용자 가이드](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)<br><br> [Azure SQL Database로 SQL Server 마이그레이션하기 위한 필수 구성 요소](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-azure-sql-database) <br><br> [Azure SQL Database 관리 되는 인스턴스로 SQL Server 마이그레이션하기 위한 필수 구성 요소](https://docs.microsoft.com/azure/dms/pre-reqs#prerequisites-for-migrating-sql-server-to-an-azure-sql-database-managed-instance) |
| **오류 18456** -로그인이 실패 했습니다.<br> | 서비스에서 제공 된 T-sql 자격 증명을 사용 하 여 원본 데이터베이스에 연결할 수 없는 경우이 오류가 발생 합니다. 이 문제를 해결 하려면 입력 한 자격 증명을 확인 합니다. 이 표 아래의 참고에 나열 된 문제 해결 문서 또는 [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 를 참조할 수 있으며 다시 시도할 수도 있습니다. |
| 잘못 된 AccountName 값 '{0}'이 (가) 제공 되었습니다. AccountName에 필요한 형식은 DomainName\UserName입니다.<br> | 이 오류는 사용자가 Windows 인증을 선택 하지만 사용자 이름을 잘못 된 형식으로 제공 하는 경우에 발생 합니다. 문제를 해결 하려면 Windows 인증에 대한 올바른 형식으로 사용자 이름을 제공 하거나 **SQL 인증**을 선택 합니다. |

## <a name="aws-rds-mysql"></a>AWS RDS MySQL

원본 AWS RDS MySQL 데이터베이스에 연결 하는 것과 관련 된 잠재적인 문제 및 해결 방법에 대한 자세한 사항은 다음 표에 나와 있습니다.

| 오류         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 [2003]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x. x (w) 드라이버] ' {server} ' (10060)의 MySQL 서버에 연결할 수 없습니다. | MySQL ODBC 드라이버가 원본 서버에 연결할 수 없는 경우이 오류가 발생 합니다. 이 문제를 해결 하려면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요.<br> |
| **오류 [2005]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x. x (w) 드라이버] 알 수 없는 MySQL 서버 호스트 ' {server} ' | 이 오류는 서비스에서 RDS의 원본 호스트를 찾을 수 없는 경우에 발생 합니다. 나열 된 원본이 없거나 RDS 인프라에 문제가 있기 때문에 문제가 발생할 수 있습니다. 이 문제를 해결 하려면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요.<br> |
| **오류 [1045]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x. x (w) driver] 사용자 ' {user} ' @ ' {server} '에 대한 액세스 거부 (암호 사용: 예) | 이 오류는 잘못 된 자격 증명으로 인해 MySQL ODBC 드라이버가 원본 서버에 연결할 수 없는 경우에 발생 합니다. 입력 한 자격 증명을 확인 합니다. 문제가 계속 되 면 원본 컴퓨터에 올바른 자격 증명이 있는지 확인 합니다. 콘솔에서 암호를 다시 설정 해야 할 수도 있습니다. 그래도 문제가 발생 하는 경우이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요.<br> |
| **오류 [9002]** [HY000]-연결에 실패 했습니다. 오류 [HY000] [MySQL] [ODBC x. x (w) 드라이버] 연결 문자열이 적절 하지 않을 수 있습니다. 참조는 포털을 방문 하세요.| 연결 문자열 문제로 인해 연결에 실패 하는 경우이 오류가 발생 합니다. 제공 된 연결 문자열이 올바른지 확인 합니다. 이 문제를 해결 하려면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요.<br> |
| **이진 로깅에 오류가 발생 했습니다. 변수 binlog_format에 값 ' {value} '이 (가) 있습니다. ' Row '로 변경 하십시오.** | 이 오류는 이진 로깅에 오류가 있는 경우에 발생 합니다. 변수 binlog_format에 잘못 된 값이 있습니다. 문제를 해결 하려면 매개 변수 그룹의 binlog_format을 ' ROW '로 변경한 다음 인스턴스를 다시 부팅 하십시오. 자세한 내용은 [이진 로깅 옵션 및 변수](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html) 또는 [AWS RDS MySQL 데이터베이스 로그 파일 설명서](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html)를 참조 하세요.<br> |

> [!NOTE]
> 원본 AWS RDS MySQL 데이터베이스에 연결 하는 것과 관련 된 문제를 해결 하는 방법에 대한 자세한 내용은 다음 리소스를 참조 하세요.
> * [Amazon RDS 연결 문제에 대한 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Amazon RDS 데이터베이스 인스턴스에 연결 하는 문제를 해결할 어떻게 할까요? 있나요?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-postgresql"></a>AWS RDS PostgreSQL

원본 AWS RDS PostgreSQL 데이터베이스에 연결 하는 것과 관련 된 잠재적인 문제 및 해결 방법에 대한 자세한 사항은 다음 표에 나와 있습니다.

| 오류         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 [101]** [08001]-연결에 실패 했습니다. 오류 [08001] 제한 시간이 만료 되었습니다. | 이 오류는 Postgres 드라이버를 원본 서버에 연결할 수 없는 경우에 발생 합니다. 이 문제를 해결 하려면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요. |
| **오류: 매개 변수 wal_level에 값 ' {value} '이 (가) 있습니다. 복제를 허용 하려면 ' 논리 '로 변경 하세요.** | 이 오류는 wal_level 매개 변수의 값이 잘못 된 경우에 발생 합니다. 문제를 해결 하려면 매개 변수 그룹의 logical_replication을 1로 변경 하 고 인스턴스를 다시 부팅 합니다. 자세한 내용은 [AMAZON RDS에서](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_PostgreSQL.html)DMS 또는 PostgreSQL [를 사용 하 여 Azure PostgreSQL로 마이그레이션하기 위한 필수](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#prerequisites) 구성 요소를 참조 하세요. |

> [!NOTE]
> 원본 AWS RDS PostgreSQL 데이터베이스에 연결 하는 것과 관련 된 문제를 해결 하는 방법에 대한 자세한 내용은 다음 리소스를 참조 하세요.
> * [Amazon RDS 연결 문제에 대한 문제 해결](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Troubleshooting.html#CHAP_Troubleshooting.Connecting)
> * [Amazon RDS 데이터베이스 인스턴스에 연결 하는 문제를 해결할 어떻게 할까요? 있나요?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="aws-rds-sql-server"></a>AWS RDS SQL Server

원본 AWS RDS SQL Server 데이터베이스에 연결 하는 것과 관련 된 잠재적인 문제 및 해결 방법에 대한 자세한 사항은 다음 표에 나와 있습니다.

| 오류         | 원인 및 문제 해결 세부 정보 |
| ------------- | ------------- |
| **오류 53** -SQL 연결에 실패 했습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지와 SQL Server가 원격 연결을 허용 하도록 구성 되어 있는지 확인 하십시오. (공급자: 명명 된 파이프 공급자, 오류: 40-SQL Server에 대한 연결을 열 수 없습니다. | 이 오류는 서비스가 원본 서버에 연결할 수 없는 경우에 발생 합니다. 이 문제를 해결 하려면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요. |
| **오류 18456** -로그인이 실패 했습니다. 사용자 ' {user} '이 (가) 로그인 하지 못했습니다. | 이 오류는 제공 된 T-sql 자격 증명을 사용 하 여 서비스에서 원본 데이터베이스에 연결할 수 없는 경우에 발생 합니다. 이 문제를 해결 하려면 입력 한 자격 증명을 확인 합니다. 또한이 표 아래의 참고에 나열 된 문제 해결 문서 또는 [MSSQLSERVER_18456](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error?view=sql-server-2017) 를 참조 하 고 다시 시도할 수 있습니다. |
| **오류 87** -연결 문자열이 잘못 되었습니다. SQL Server에 연결을 설정하는 동안 네트워크 관련 또는 인스턴스 특정 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. 인스턴스 이름이 올바른지와 SQL Server가 원격 연결을 허용 하도록 구성 되어 있는지 확인 하십시오. (공급자: SQL 네트워크 인터페이스, 오류: 25-연결 문자열이 잘못 되었습니다.) | 이 오류는 연결 문자열이 잘못 되어 서비스에서 원본 서버에 연결할 수 없는 경우에 발생 합니다. 이 문제를 해결 하려면 제공 된 연결 문자열을 확인 합니다. 문제가 계속 되 면이 표 아래의 참고에 나열 된 문제 해결 문서를 참조 한 후 다시 시도 하세요. |
| **오류-서버 인증서를 신뢰할 수 없습니다.** 서버에 성공적으로 연결되었지만 로그인 중 오류가 발생했습니다. (공급자: SSL 공급자, 오류: 0-인증서 체인이 신뢰 되지 않는 기관에서 발급 되었습니다.) | 이 오류는 사용 된 인증서를 신뢰할 수 없는 경우에 발생 합니다. 문제를 해결 하려면 신뢰할 수 있는 인증서를 찾은 다음 서버에서 사용 하도록 설정 해야 합니다. 또는 연결 하는 동안 인증서 신뢰 옵션을 선택할 수 있습니다. 사용 된 인증서에 대해 잘 알고 있고 신뢰 하는 경우에만이 작업을 수행 하세요. <br> 자체 서명 된 인증서를 사용 하 여 암호화 된 SSL 연결은 강력한 보안을 제공 하지 않으며 메시지 가로채기 (man-in-the-middle) 공격에 취약 합니다. 프로덕션 환경이 나 인터넷에 연결 된 서버에서 자체 서명 된 인증서를 사용 하 여 SSL을 사용 하지 마세요. <br> 자세한 내용은 [MICROSOFT SQL SERVER DB 인스턴스](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/SQLServer.Concepts.General.SSL.Using.html) 를 사용 하 여 SSL을 사용 하거나 [자습서: DMS를 사용 하 여 Azure로 RDS SQL Server 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites)을 참조 하세요. |
| **오류 300** -사용자에 게 필요한 권한이 없습니다. 개체 ' {SERVER} ', 데이터베이스 ' {database} '에 대한 VIEW SERVER STATE 권한이 거부 되었습니다. | 이 오류는 사용자에 게 마이그레이션을 수행할 수 있는 권한이 없는 경우에 발생 합니다. 이 문제를 해결 하려면 [서버 권한 부여-transact-sql](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql?view=sql-server-2017) 또는 [자습서: DMS를 사용 하 여 Azure로 RDS SQL Server 마이그레이션](https://docs.microsoft.com/azure/dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online#prerequisites) 을 참조 하세요. |

> [!NOTE]
> 원본 AWS RDS SQL Server에 연결 하는 것과 관련 된 문제를 해결 하는 방법에 대한 자세한 내용은 다음 리소스를 참조 하세요.
>
> * [Solving Connectivity errors to SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server)(SQL Server에 대한 연결 오류 해결)
> * [Amazon RDS 데이터베이스 인스턴스에 연결 하는 문제를 해결할 어떻게 할까요? 있나요?](https://aws.amazon.com/premiumsupport/knowledge-center/rds-cannot-connect)

## <a name="known-issues"></a>알려진 문제

* [Azure SQL Database에 대한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-sql-online)
* [Azure Database for MySQL에 대한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-mysql-online)
* [Azure Database for PostgreSQL에 대한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항](https://docs.microsoft.com/azure/dms/known-issues-azure-postgresql-online)

## <a name="next-steps"></a>다음 단계

* [PowerShell Azure Database Migration Service](https://docs.microsoft.com/powershell/module/azurerm.datamigration/?view=azurermps-6.13.0#data_migration)문서를 참조 하세요.
* [Azure Portal를 사용 하 여 Azure Database for MySQL에서 서버 매개 변수를 구성 하는 방법](https://docs.microsoft.com/azure/mysql/howto-server-parameters)문서를 참조 하세요.
* [Azure Database Migration Service 사용을 위한 필수 구성 요소 개요](https://docs.microsoft.com/azure/dms/pre-reqs)문서를 참조 하세요.
* [Azure Database Migration Service 사용에 대한 FAQ](https://docs.microsoft.com/azure/dms/faq)를 참조 하세요.
