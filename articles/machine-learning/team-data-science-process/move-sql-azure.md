---
title: Azure SQL Database에 데이터 이동 - Team Data Science Process
description: 플랫 파일(CSV 또는 TSV 형식) 또는 온-프레미스 SQL Server에 저장된 데이터에서 Azure SQL 데이터베이스로 데이터를 이동합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/04/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d1634552522a3d1056f9af29386b6ae32754cae0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429300"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Azure Machine Learning을 위해 Azure SQL Database로 데이터 이동

이 문서에서는 플랫 파일(CSV 또는 TSV 형식) 또는 온-프레미스 SQL Server에 저장된 데이터에서 Azure SQL 데이터베이스로 데이터를 이동하기 위한 옵션에 대해 간략히 설명합니다. 클라우드로 데이터를 이동하는 이러한 작업은 팀 데이터 과학 프로세스의 일부입니다.

Machine Learning을 위해 온-프레미스 SQL Server로 데이터를 이동하기 위한 옵션을 간략히 설명하는 토픽은 [Azure Virtual Machine의 SQL Server로 데이터 이동](move-sql-server-virtual-machine.md)을 참조하세요.

다음 표에서는 Azure SQL Database로 데이터를 이동하는 옵션을 요약합니다.

| <b>원본</b> | <b>대상: Azure SQL Database</b> |
| --- | --- |
| <b>플랫 파일(CSV 또는 TSV 형식)</b> |[대량 삽입 SQL 쿼리](#bulk-insert-sql-query) |
| <b>온-프레미스 SQL Server</b> |1.[플랫 파일로 내보내기](#export-flat-file)<br> 2. [SQL Database 마이그레이션 마법사](#insert-tables-bcp)<br> 3. [데이터베이스 백업 및 복원](#db-migration)<br> 4. [Azure 데이터 팩터리](#adf) |

## <a name="prereqs"></a>필수 조건
여기에 설명된 절차를 위해서는 다음이 필요합니다.

* **Azure 구독**. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
* **Azure 저장소 계정**. 이 자습서에서는 데이터 저장을 위해 Azure 저장소 계정을 사용합니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md) 문서를 참조하세요. 저장소 계정을 만든 후에는 저장소 액세스에 사용되는 계정 키를 확보해야 합니다. [저장소 액세스 키 관리](../../storage/common/storage-account-manage.md#access-keys)를 참조하세요.
* **Azure SQL Database**에 대한 액세스. Azure SQL Database를 설정해야 하는 경우, [Microsoft Azure SQL Database 시작](../../sql-database/sql-database-get-started.md)에서 Azure SQL Database의 새 인스턴스를 프로비전하는 방법에 대한 정보를 제공합니다.
* 로컬로 설치 및 구성된 **Azure PowerShell** . 자세한 내용은 [Azure PowerShell 설치 및 구성법](/powershell/azure/overview)을 참조하세요.

**데이터**: [NYC Taxi 데이터 세트](https://chriswhong.com/open-data/foil_nyc_taxi/)를 사용하여 마이그레이션 프로세스를 시연합니다. NYC Taxi 데이터 세트는 여정 데이터 및 요금에 대한 정보를 포함하며 Azure Blob 스토리지: [NYC Taxi 데이터](https://www.andresmh.com/nyctaxitrips/)에서 제공됩니다. 이러한 파일의 샘플 및 설명은 [NYC Taxi Trips 데이터 세트 설명](sql-walkthrough.md#dataset)에 제공됩니다.

자신의 데이터 세트에 여기에 설명된 절차를 도입하거나 NYC Taxi 데이터 세트를 사용하여 설명된 대로 단계를 따릅니다. NYC Taxi 데이터 세트를 온-프레미스 SQL Server 데이터베이스에 업로드하려면 [SQL Server Database로 대량 데이터 가져오기](sql-walkthrough.md#dbload)에 설명된 절차를 따릅니다. 이러한 지침은 Azure Virtual Machine의 SQL Server에 대한 내용이지만 온-프레미스 SQL Server로 업로드하는 절차는 동일합니다.

## <a name="file-to-azure-sql-database"></a> 플랫 파일 원본에서 Azure SQL 데이터베이스로 데이터 이동
대량 삽입 SQL 쿼리를 사용하여 플랫 파일(CSV 또는 TSV 형식)의 데이터를 Azure SQL 데이터베이스로 이동할 수 있습니다.

### <a name="bulk-insert-sql-query"></a> 대량 삽입 SQL 쿼리
대량 삽입 SQL 쿼리를 사용하는 절차에 대한 단계는 플랫 파일 원본에서 Azure VM의 SQL Server로 데이터를 이동하는 섹션의 내용과 유사합니다. 자세한 내용은 [대량 삽입 SQL 쿼리](move-sql-server-virtual-machine.md#insert-tables-bulkquery)를 참조하세요.

## <a name="sql-on-prem-to-sazure-sql-database"></a> 온-프레미스 SQL Server에서 Azure SQL 데이터베이스로 데이터 이동
원본 데이터가 온-프레미스 SQL Server에 저장된 경우 다양한 방법으로 Azure SQL 데이터베이스로 데이터를 이동할 수 있습니다.

1. [플랫 파일로 내보내기](#export-flat-file)
2. [SQL Database 마이그레이션 마법사](#insert-tables-bcp)
3. [데이터베이스 백업 및 복원](#db-migration)
4. [Azure Data Factory](#adf)

처음 세 개 단계는 이와 동일한 절차를 다루는 [Azure 가상 머신의 SQL Server로 데이터 이동](move-sql-server-virtual-machine.md) 의 해당 섹션과 매우 유사합니다. 이 항목의 해당 섹션에 대한 링크가 다음 절차에 제공됩니다.

### <a name="export-flat-file"></a>플랫 파일로 내보내기
플랫 파일로 내보내기 위한 단계는 [플랫 파일로 내보내기](move-sql-server-virtual-machine.md#export-flat-file)에서 다루는 내용과 유사합니다.

### <a name="insert-tables-bcp"></a>SQL Database 마이그레이션 마법사
SQL Database 마이그레이션 마법사를 사용하는 단계는 [SQL Database 마이그레이션 마법사](move-sql-server-virtual-machine.md#sql-migration)에서 다루는 내용과 유사합니다.

### <a name="db-migration"></a>데이터베이스 백업 및 복원
데이터베이스 백업 및 복원을 사용하는 단계는 [데이터베이스 백업 및 복원](move-sql-server-virtual-machine.md#sql-backup)에서 다루는 내용과 유사합니다.

### <a name="adf"></a>Azure Data Factory
ADF(Azure Data Factory)를 사용하여 Azure SQL 데이터베이스로 데이터를 이동하는 절차는 [Azure Data Factory를 사용하여 온-프레미스 SQL Server에서 SQL Azure로 데이터 이동](move-sql-azure-adf.md) 토픽에 제공됩니다. 이 토픽에서는 ADF를 사용하여 Azure Blob Storage를 통해 온-프레미스 SQL Server 데이터베이스에서 Azure SQL 데이터베이스로 데이터를 이동하는 방법을 보여 줍니다.

온-프레미스 및 클라우드 리소스를 모두 액세스하는 하이브리드 시나리오에서 데이터를 지속적으로 마이그레이션해야 하는 경우, 데이터를 트랜잭션되거나 수정해야 하거나 마이그레이션할 때 비즈니스 논리를 추가해야 하는 경우 ADF를 사용하는 것이 좋습니다. ADF에서는 정기적으로 데이터 이동을 관리하는 간단한 JSON 스크립트를 사용하여 작업 예약 및 모니터링이 가능합니다. 또한 복잡한 작업을 지원하는 기타 기능도 포함하고 있습니다.
