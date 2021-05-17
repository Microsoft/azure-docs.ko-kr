---
title: 애플리케이션 개발 개요
description: 사용 가능한 연결 라이브러리 및 SQL Database에 연결하는 애플리케이션에 대한 모범 사례를 알아봅니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782980"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>애플리케이션 개발 개요 - SQL Database 및 SQL Managed Instance

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

이 문서는 Azure의 데이터베이스에 연결하기 위한 코드를 작성하면서 개발자가 알고 있어야 하는 기본적인 사항을 안내합니다. 이 문서는 Azure SQL Database와 Azure SQL Managed Instance 둘 다에 적용됩니다.

## <a name="language-and-platform"></a>언어 및 플랫폼

다양한 [프로그래밍 언어와 플랫폼](connect-query-content-reference-guide.md)을 사용하여 Azure SQL Database에 연결하고 쿼리할 수 있습니다. 데이터베이스에 연결하는 데 사용할 수 있는 [샘플 애플리케이션](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0)을 찾을 수 있습니다.

[cheetah](https://github.com/wunderlist/cheetah), [sql-cli](https://www.npmjs.com/package/sql-cli), [VS Code](https://code.visualstudio.com/)와 같은 오픈 소스 도구를 활용할 수 있습니다. 또한 Azure SQL Database는 [Visual Studio](https://www.visualstudio.com/downloads/) 및 [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)와 같은 Microsoft 도구로 작동합니다. Azure Portal, PowerShell 및 REST API를 사용하면 추가 생산성을 얻을 수도 있습니다.

## <a name="authentication"></a>인증

Azure SQL Database에 대한 액세스는 로그인과 방화벽으로 보호됩니다. Azure SQL Database는 SQL Server와 [Azure Active Directory 인증](authentication-aad-overview.md) 사용자와 로그인을 둘 다 지원합니다. Azure Active Directory 로그인은 SQL Managed Instance에서만 사용할 수 있습니다. 

[데이터베이스 액세스 및 로그인 관리](logins-create-manage.md)에 대해 알아보세요.

## <a name="connections"></a>Connections

클라이언트 연결 논리에서 기본 시간 제한을 30초로 재정의합니다. 기본값 15초는 인터넷에 종속된 연결 시간으로 너무 짧습니다.

[연결 풀](/dotnet/framework/data/adonet/sql-server-connection-pooling)을 사용하는 경우 프로그램에서 활발하게 사용하지 않고 다시 사용할 준비를 하지 않으면 연결을 즉시 닫아야 합니다.

인프라 또는 연결 오류로 인해 트랜잭션이 롤백 될 수 있기 때문에 장기 실행 트랜잭션은 피합니다. 가능하면 여러 개의 작은 트랜잭션으로 트랜잭션을 분할하고 [일괄 처리를 사용하여 성능을 향상시킵니다](../performance-improve-use-batching.md).

## <a name="resiliency"></a>복원력

Azure SQL Database는 기본 인프라 또는 클라우드 엔터티 간의 통신에서 발생하는 일시적인 오류를 예상할 수 있는 클라우드 서비스입니다. Azure SQL Database는 일시적인 인프라 오류에 대해 복원력이 있지만, 이러한 오류는 연결에 영향을 미칠 수 있습니다. SQL Database에 연결하는 동안 일시적인 오류가 발생하면, 코드는 [호출을 다시 시도](troubleshoot-common-connectivity-issues.md)해야 합니다. 여러 클라이언트가 동시에 재시도하여 서비스에 부담을 가중하지 않도록 재시도 논리에 백오프 논리를 사용하는 것이 좋습니다. 재시도 논리는 [SQL Database 클라이언트 프로그램에 대한 오류 메시지](troubleshoot-common-errors-issues.md)에 따라 달라집니다.

Azure SQL 데이터베이스에서 계획된 유지 관리 이벤트를 준비하는 방법에 대한 자세한 내용은 [Azure SQL Database의 Azure 유지 관리 이벤트 계획](planned-maintenance.md)을 참조하세요.

## <a name="network-considerations"></a>네트워크 고려 사항

- 클라이언트 프로그램을 호스팅하는 컴퓨터에서 방화벽이 포트 1433에서 발신 TCP 통신을 허용하는지 확인합니다.  추가 정보: [Azure SQL Database 방화벽 구성](firewall-configure.md).
- Azure VM(가상 머신)에서 클라이언트가 실행되는 동안 클라이언트 프로그램이 SQL Database에 연결하는 경우에는, VM의 특정 포트 범위를 열어야 합니다. 추가 정보: [ADO.NET 4.5 및 SQL Database에 대한 1433 이외의 포트](adonet-v12-develop-direct-route-ports.md).
- Azure SQL Database에 대한 클라이언트 연결이 프록시를 바이패스하고 데이터베이스와 직접 상호 작용하는 경우가 있습니다. 1433 이외의 포트가 중요해집니다. 자세한 내용은 [Azure SQL Database 연결 아키텍처](connectivity-architecture.md) 및 [ADO.NET 4.5 및 SQL Database에 대한 1433 이외의 포트](adonet-v12-develop-direct-route-ports.md)를 참조하세요.
- SQL Managed Instance의 인스턴스에 대한 네트워킹 구성은 [SQL Managed Instance에 대한 네트워크 구성](../managed-instance/how-to-content-reference-guide.md#network-configuration)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[SQL Database](sql-database-paas-overview.md)와 [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md)의 모든 기능을 살펴봅니다.

시작하려면 [Azure SQL Database](quickstart-content-reference-guide.md)와 [Azure SQL Managed Instances](../managed-instance/quickstart-content-reference-guide.md) 가이드를 참조하세요.