---
title: Azure SQL Data Warehouse 문제 해결 | Microsoft Docs
description: Azure SQL Data Warehouse 문제 해결
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 12/04/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: dc78fbc93d625b39379e07f240eef7fbad10d194
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474847"
---
# <a name="troubleshooting-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse 문제 해결
이 문서에서는 일반적인 문제 해결 질문을 나열합니다.

## <a name="connecting"></a>연결
| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 'NT AUTHORITY\ANONYMOUS LOGON' 사용자에 대해 로그인 실패 (Microsoft SQL Server, 오류: 18456) | 이 오류는 AAD 사용자가 마스터 데이터베이스에 연결하려고 하는데 마스터에 사용자가 없는 경우에 발생합니다.  이 문제를 해결하려면 연결 시에 연결하려는 SQL Data Warehouse를 지정하거나 마스터 데이터베이스에 사용자를 추가합니다.  자세한 내용은 [보안 개요][Security overview]를 참조하세요. |
| 현재 보안 컨텍스트로는 서버 보안 주체 "MyUserName" 이(가) 데이터베이스 "master" 에 액세스할 수 없습니다. 사용자 기본 데이터베이스를 열 수 없습니다. 로그인이 실패했습니다. 사용자 'MyUserName'에 대한 로그인이 실패했습니다. (Microsoft SQL Server, 오류: 916) | 이 오류는 AAD 사용자가 마스터 데이터베이스에 연결하려고 하는데 마스터에 사용자가 없는 경우에 발생합니다.  이 문제를 해결하려면 연결 시에 연결하려는 SQL Data Warehouse를 지정하거나 마스터 데이터베이스에 사용자를 추가합니다.  자세한 내용은 [보안 개요][Security overview]를 참조하세요. |
| CTAIP 오류                                                  | 이 오류는 로그인이 SQL Data Warehouse 데이터베이스가 아닌 SQL 서버 마스터 데이터베이스에서 만들어진 경우에 발생할 수 있습니다.  이 오류가 발생하는 경우 [보안 개요][Security overview] 문서를 살펴보세요.  이 문서에서는 마스터 데이터베이스에서 로그인과 사용자를 만드는 방법과 SQL Data Warehouse 데이터베이스에서 사용자를 만드는 방법을 차례로 설명합니다. |
| 방화벽에 의해 차단                                          | Azure SQL 데이터베이스가 알려진 IP 주소만 데이터베이스에 액세스할 수 있도록 서버 및 데이터베이스 수준 방화벽으로 보호됩니다. 방화벽은 기본적으로 안전하며 이는 연결하기 전에 IP 주소 또는 주소 범위를 명시적으로 설정해야 한다는 의미입니다.  액세스를 위해 방화벽을 구성하려면 [프로비전 지침][Provisioning instructions]의 [클라이언트 IP에 대한 서버 방화벽 액세스 구성][Configure server firewall access for your client IP] 단계를 따르세요. |
| 도구 또는 드라이버에 연결할 수 없음                           | SQL Data Warehouse 사용 시에는 [SSMS][SSMS], [SSDT for Visual Studio][SSDT for Visual Studio] 또는 [sqlcmd][sqlcmd]를 사용하여 데이터를 쿼리하는 것이 좋습니다. SQL Data Warehouse에 연결하는 방법 및 관련 드라이버에 대한 자세한 내용은 [Azure SQL Data Warehouse용 드라이버][Drivers for Azure SQL Data Warehouse] 및 [Azure SQL Data Warehouse에 연결][Connect to Azure SQL Data Warehouse] 문서를 참조하세요. |

## <a name="tools"></a>도구
| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Visual Studio 개체 탐색기에 AAD 사용자가 없음           | 이는 알려진 문제입니다.  해결 방법으로 [sys.database_principals][sys.database_principals]에서 사용자를 봅니다.  SQL Data Warehouse에서 Azure Active Directory를 사용하는 방법에 대한 자세한 내용을 [Azure SQL Data Warehouse에 대한 인증][Authentication to Azure SQL Data Warehouse]을 참조하세요. |
| 수동 스크립팅, 스크립팅 마법사를 사용 하 여 또는 SSMS를 통해 연결 속도가 느린 응답 하지 않거나 오류를 생성 합니다. | 사용자가 master 데이터베이스에서 만들어졌는지 확인합니다. 스크립팅 옵션에서 엔진 버전이 “Microsoft Azure SQL Data Warehouse 버전”으로 설정되고 엔진 유형이 “Microsoft Azure SQL Database”로 설정되어 있는지도 확인합니다. |
| SSMS에서 스크립트 생성 실패                             | "종속 개체에 대한 스크립트 생성" 옵션이 "True"로 설정된 경우 SQL 데이터 웨어하우스용 스크립트 생성이 실패합니다. 이 문제를 해결하려면 사용자가 수동으로 도구 -> 옵션 -> SQL Server 개체 탐색기 -> 종속 개체에 대한 스크립트 생성 옵션으로 이동하여 false로 설정해야 합니다 |

## <a name="performance"></a>성능
| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| 쿼리 성능 문제 해결                            | 특정 쿼리 문제를 해결하려는 경우 [쿼리 모니터링 방법 알아보기][Learning how to monitor your queries] 문서부터 참조합니다. |
| 쿼리 성능 및 계획이 부적합하여 종종 통계가 누락됨 | 성능 저하의 가장 일반적인 원인은 테이블에 대한 통계 부족입니다.  통계를 만드는 방법 및 통계가 성능에 중요한 이유에 대한 자세한 내용은 [테이블 통계 유지 관리][Statistics]를 참조하세요. |
| 낮은 동시성/큐에 쿼리 대기                             | 동시성과 함께 메모리 할당을 적절히 적용하려면 [워크로드 관리][Workload management]를 이해하는 것이 중요합니다. |
| 모범 사례 구현 방법                              | 쿼리 성능을 향상시키는 방법은 [SQL Data Warehouse 모범 사례][SQL Data Warehouse best practices] 문서에서 알아볼 수 있습니다. |
| 크기 조정을 통해 성능을 향상시키는 방법                      | 때때로 성능을 향상시키기 위한 솔루션은 [SQL Data Warehouse 크기를 조정][Scaling your SQL Data Warehouse]하여 단순히 더 많은 계산 능력을 쿼리에 추가하는 것입니다. |
| 인덱스 품질 저하로 인한 쿼리 성능 저하     | 경우에 따라 [columnstore 인덱스 품질 저하][Poor columnstore index quality]로 인해 쿼리가 느려질 수 있습니다.  자세한 내용 및 [세그먼트 품질을 개선하기 위해 인덱스 다시 작성][Rebuild indexes to improve segment quality] 방법에 대해서는 이 문서를 참조하세요. |

## <a name="system-management"></a>시스템 관리
| 문제                                                        | 해결 방법                                                   |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| Msg 40847: 서버가 허용되는 데이터베이스 트랜잭션 단위 할당량인 45000을 초과하므로 작업을 수행할 수 없습니다. | 만들려는 데이터베이스의 [DWU][DWU]를 줄이거나 또는 [할당량 증가를 요청][request a quota increase]합니다. |
| 공간 사용률 조사                              | 시스템의 공간 사용률을 이해하려면 [테이블 크기][Table sizes]를 참조하세요. |
| 테이블 관리 도움말                                    | 테이블 관리에 대한 도움이 필요한 경우 [테이블 개요][Overview] 문서를 참조하세요.  이 문서에는 [테이블 데이터 유형][Data types], [테이블 배포][Distribute], [테이블 인덱싱][Index],  [테이블 분할][Partition], [테이블 통계 유지 관리][Statistics] 및 [임시 테이블][Temporary]과 같이 좀 더 자세한 항목으로 연결되는 링크가 포함되어 있습니다. |
| TDE(투명한 데이터 암호화) 진행률 표시줄이 Azure Portal에서 업데이트되지 않음 | [powershell](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption)을 통해 TDE의 상태를 볼 수 있습니다. |

## <a name="polybase"></a>Polybase
| 문제                                           | 해결 방법                                                   |
| :---------------------------------------------- | :----------------------------------------------------------- |
| 대용량 행으로 인해 로드 실패                | 현재 대용량 행 지원은 Polybase에서 사용할 수 없습니다.  즉, 테이블에 VARCHAR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)가 포함되어 있으면 데이터를 로드하는 데 외부 테이블을 사용할 수 없습니다.  대용량 행의 로드는 현재 Azure Data Factory(BCP 포함), Azure Stream Analytics, SSIS, BCP 또는 .NET SQLBulkCopy 클래스를 통해서만 지원됩니다. 대용량 행에 대한 PolyBase 지원은 후속 릴리스에 추가될 예정입니다. |
| MAX 데이터 형식을 갖는 테이블의 bcp 로드 실패 | 일부 시나리오에서는 테이블의 끝에 VARCHAR(MAX), NVARCHAR(MAX) 또는 VARBINARY(MAX)를 배치하도록 요구하는 알려진 문제가 있습니다.  MAX 열을 테이블 끝으로 이동해보세요. |

## <a name="differences-from-sql-database"></a>SQL Database와의 차이점
| 문제                                 | 해결 방법                                                   |
| :------------------------------------ | :----------------------------------------------------------- |
| 지원되지 않는 SQL Database 기능     | [지원되지 않는 테이블 기능][Unsupported table features]을 참조하세요. |
| 지원되지 않는 SQL Database 데이터 형식   | [지원되지 않는 데이터 형식][Unsupported data types]을 참조하세요.        |
| DELETE 및 UPDATE 제한 사항         | [UPDATE 해결][UPDATE workarounds], [DELETE 해결][DELETE workarounds] 및 [CTAS를 사용하여 지원되지 않는 UPDATE 및 DELETE 구문 해결][Using CTAS to work around unsupported UPDATE and DELETE syntax]을 참조하세요. |
| MERGE 문이 지원되지 않음      | [MERGE 해결 방법][MERGE workarounds]을 참조하세요.                  |
| 저장 프로시저 제한 사항          | 저장 프로시저의 몇 가지 제한을 이해하려면 [저장 프로시저 제한 사항][Stored procedure limitations]을 참조하세요. |
| UDF가 SELECT 문을 지원하지 않음 | 이 문제가 UDF의 현재 제한 사항입니다.  지원되는 구문에 대해서는 [CREATE FUNCTION][CREATE FUNCTION]을 참조하세요. |

## <a name="next-steps"></a>다음 단계
문제 해결 방법을 찾는 데 도움이 필요한 경우 다음과 같은 리소스를 사용해 보세요.

* [블로그]
* [기능 요청]
* [비디오]
* [CAT 팀 블로그]
* [지원 티켓 만들기]
* [MSDN 포럼]
* [Stack Overflow 포럼]
* [Twitter]

<!--Image references-->

<!--Article references-->
[Security overview]: sql-data-warehouse-overview-manage-security.md
[SSMS]: /sql/ssms/download-sql-server-management-studio-ssms
[SSDT for Visual Studio]: sql-data-warehouse-install-visual-studio.md
[Drivers for Azure SQL Data Warehouse]: sql-data-warehouse-connection-strings.md
[Connect to Azure SQL Data Warehouse]: sql-data-warehouse-connect-overview.md
[지원 티켓 만들기]: sql-data-warehouse-get-started-create-support-ticket.md
[Scaling your SQL Data Warehouse]: sql-data-warehouse-manage-compute-overview.md
[DWU]: sql-data-warehouse-overview-what-is.md
[request a quota increase]: sql-data-warehouse-get-started-create-support-ticket.md
[Learning how to monitor your queries]: sql-data-warehouse-manage-monitor.md
[Provisioning instructions]: sql-data-warehouse-get-started-provision.md
[Configure server firewall access for your client IP]: sql-data-warehouse-get-started-provision.md
[SQL Data Warehouse best practices]: sql-data-warehouse-best-practices.md
[Table sizes]: sql-data-warehouse-tables-overview.md#table-size-queries
[Unsupported table features]: sql-data-warehouse-tables-overview.md#unsupported-table-features
[Unsupported data types]: sql-data-warehouse-tables-data-types.md#unsupported-data-types
[Overview]: sql-data-warehouse-tables-overview.md
[Data types]: sql-data-warehouse-tables-data-types.md
[Distribute]: sql-data-warehouse-tables-distribute.md
[Index]: sql-data-warehouse-tables-index.md
[Partition]: sql-data-warehouse-tables-partition.md
[Statistics]: sql-data-warehouse-tables-statistics.md
[Temporary]: sql-data-warehouse-tables-temporary.md
[Poor columnstore index quality]: sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuild indexes to improve segment quality]: sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Workload management]: resource-classes-for-workload-management.md
[Using CTAS to work around unsupported UPDATE and DELETE syntax]: sql-data-warehouse-develop-ctas.md
[UPDATE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-update-statements
[DELETE workarounds]: sql-data-warehouse-develop-ctas.md#ansi-join-replacement-for-delete-statements
[MERGE workarounds]: sql-data-warehouse-develop-ctas.md#replace-merge-statements
[Stored procedure limitations]: sql-data-warehouse-develop-stored-procedures.md#limitations
[Authentication to Azure SQL Data Warehouse]: sql-data-warehouse-authentication.md


<!--MSDN references-->
[sys.database_principals]: /sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql
[CREATE FUNCTION]: /sql/t-sql/statements/create-function-sql-data-warehouse
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[블로그]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[CAT 팀 블로그]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[기능 요청]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[MSDN 포럼]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse
[Stack Overflow 포럼]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[비디오]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
