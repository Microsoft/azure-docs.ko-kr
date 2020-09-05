---
title: Azure Stream Analytics에서 출력 Azure SQL Database
description: 이 문서에서는 Azure Stream Analytics에 대 한 출력 Azure SQL Database 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: d61cad0be25f5aa1a4c63bf3dc128196ad80fdad
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88875854"
---
# <a name="azure-sql-database-output-from-azure-stream-analytics"></a>Azure Stream Analytics에서 출력 Azure SQL Database

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/)는 관계형 데이터 또는 관계형 데이터베이스에서 호스팅되는 콘텐츠에 종속된 애플리케이션에 대한 출력으로 사용할 수 있습니다. Azure Stream Analytics 작업은 SQL Database의 기존 테이블에 기록 합니다. 테이블 스키마는 작업 출력의 필드 및 해당 형식과 정확히 일치해야 합니다. 또한 SQL Database 출력 옵션을 통해 [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)를 출력으로 지정할 수도 있습니다. 쓰기 처리량을 향상시키는 방법을 알아보려면 [Azure SQL Database를 출력으로 사용하는 Stream Analytics](stream-analytics-sql-output-perf.md) 문서를 참조하세요.

[AZURE SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance) 를 출력으로 사용할 수도 있습니다. [SQL Managed Instance에서 공용 끝점을 구성](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure) 하 고 Azure Stream Analytics에서 다음 설정을 수동으로 구성 해야 합니다. 데이터베이스가 연결된 SQL Server를 실행하는 Azure 가상 머신도 아래 설정을 수동으로 구성하여 지원됩니다.

## <a name="output-configuration"></a>출력 구성

다음 표에는 SQL Database 출력을 만들기 위한 속성 이름 및 해당 설명이 나와 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 출력 별칭 |쿼리 출력을 이 데이터베이스로 보내기 위해 쿼리에서 사용되는 식별 이름입니다. |
| 데이터베이스 | 출력을 보내는 데이터베이스의 이름입니다. |
| 서버 이름 | 논리 SQL server 이름 또는 관리 되는 인스턴스 이름입니다. SQL Managed Instance의 경우 포트 3342를 지정 해야 합니다. 예를 들어 *sampleserver.public.database.windows.net,3342*입니다. |
| 사용자 이름 | 데이터베이스에 대한 쓰기 액세스 권한이 있는 사용자 이름입니다. Stream Analytics는 SQL 인증만 지원합니다. |
| 암호 | 데이터베이스에 연결하는 암호입니다. |
| 테이블 | 출력을 기록되는 테이블 이름입니다. 테이블 이름은 대/소문자를 구분합니다. 이 테이블의 스키마는 작업 출력에서 생성하는 필드 수 및 해당 형식과 정확히 일치해야 합니다. |
|파티션 구성표 상속| 이전 쿼리 단계의 파티션 구성표를 상속하여 여러 기록기를 사용하는 완전 병렬 토폴로지를 테이블에서 사용하도록 설정하는 옵션입니다. 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조하세요.|
|최대 일괄 처리 수| 모든 대량 삽입 트랜잭션을 통해 보내는 레코드 수에 추천되는 상한값입니다.|

Azure Stream Analytics에서 Azure Synapse Analytics(이전의 SQL Data Warehouse)로 출력할 수 있는 두 가지 어댑터, 즉, SQL Database 및 Azure Synapse가 있습니다. 다음 조건 중 하나라도 충족 되는 경우 SQL Database 어댑터 대신 Azure Synapse Analytics 어댑터를 선택 하는 것이 좋습니다.

* **처리량**: 현재 또는 미래의 예상 처리량이 10MB/초보다 큰 경우 더 나은 성능을 위해 Azure Synapse 출력 옵션을 사용합니다.

* **입력 파티션**: 8개 이상의 입력 파티션이 있는 경우 더 나은 확장을 위해 Azure Synapse 출력 옵션을 사용합니다.

## <a name="partitioning"></a>분할

분할은 사용 하도록 설정 해야 하며 쿼리의 PARTITION BY 절을 기반으로 합니다. 분할 상속 옵션을 사용 하는 경우에는 [완전히 병렬화 쿼리](stream-analytics-scale-jobs.md)를 위한 입력 분할을 따릅니다. 데이터를 Azure SQL Database에 로드할 때 더 효율적인 쓰기 처리량 성능을 달성하는 방법에 대한 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](stream-analytics-sql-output-perf.md)을 참조하세요.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

최대 **일괄 처리 횟수**를 사용 하 여 최대 메시지 크기를 구성할 수 있습니다. 기본 최대값은 1만이 고 기본 최소값은 단일 대량 삽입 당 100 행입니다. 자세한 내용은 [AZURE SQL 제한](../sql-database/sql-database-resource-limits.md)을 참조 하세요. 모든 일괄 처리는 처음에 최대 일괄 처리 수로 대량 삽입됩니다. 일괄 처리는 SQL에서 재시도 가능한 오류를 기준으로 절반(최소 일괄 처리 수까지)으로 분할됩니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell를 사용 하 여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-vs-code.md)
