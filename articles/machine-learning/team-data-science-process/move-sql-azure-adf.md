---
title: Azure Data Factory를 사용하여 SQL Server 데이터를 SQL Azure로 이동 - 팀 데이터 과학 프로세스
description: 온-프레미스와 클라우드의 데이터베이스 간에 데이터를 매일 이동하는 두 데이터 마이그레이션 활동으로 구성된 ADF 파이프라인을 설정합니다.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 59f8b8b253fc914e5723a9c41475ec78bc3f376e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61429351"
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Azure Data Factory를 사용하여 온-프레미스 SQL Server에서 SQL Azure로 데이터 이동

이 문서에서는 ADF(Azure Data Factory)를 사용하여 Azure Blob Storage를 통해 온-프레미스 SQL Server Database에서 SQL Azure Database로 데이터를 이동하는 방법을 보여 줍니다.

Azure SQL Database로 데이터를 이동하는 다양한 옵션을 요약한 표는 [Azure Machine Learning을 위해 Azure SQL Database로 데이터 이동](move-sql-azure.md)을 참조하세요.

## <a name="intro"></a>소개: ADF란 무엇이며 데이터를 마이그레이션하는 데 사용하려면 언제 사용해야 하나요?
Azure 데이터 팩터리는 데이터의 이동과 변환을 오케스트레이션하고 자동화하는 완전히 관리되는 클라우드 기반의 데이터 통합 서비스입니다. ADF 모델의 핵심 개념은 파이프라인입니다. 파이프라인은 각각 데이터 세트에 포함된 데이터에 수행할 작업을 정의하는 활동의 논리적 그룹화입니다. 연결된 서비스는 데이터 팩터리가 데이터 리소스에 연결하기 위해 필요한 정보를 정의하는 데 사용됩니다.

ADF와 함께 기존 데이터 처리 서비스는 가용성이 높고 클라우드에서 관리되는 데이터 파이프라인으로 구성될 수 있습니다. 이러한 데이터 파이프라인에서 데이터 수집, 준비, 변환, 분석 및 게시를 예약할 수 있으며 ADF가 복잡한 데이터 및 처리 종속성을 관리하고 오케스트레이션합니다. 클라우드에서 솔루션을 신속하게 구축 및 배포할 수 있으며 증가하는 온-프레미스 및 클라우드 데이터 원본을 연결합니다.

다음 경우에 ADF 사용을 고려합니다.

* 온-프레미스 및 클라우드 리소스 둘 다에 액세스하는 하이브리드 시나리오에서 데이터를 지속적으로 마이그레이션해야 하는 경우
* 데이터를 트랜잭션 처리하거나 수정해야 할 때 또는 마이그레이션 중에 비즈니스 논리를 추가할 때

ADF에서는 정기적으로 데이터 이동을 관리하는 간단한 JSON 스크립트를 사용하여 작업 예약 및 모니터링이 가능합니다. 또한 복잡한 작업을 지원하는 기타 기능도 포함하고 있습니다. ADF에 대한 자세한 내용은 [Azure 데이터 팩터리(ADF)](https://azure.microsoft.com/services/data-factory/)를 참조하세요.

## <a name="scenario"></a>시나리오
두 가지 데이터 마이그레이션 작업을 구성하는 ADF 파이프라인을 설정했습니다. 데이터는 매일 온-프레미스 SQL 데이터베이스와 클라우드의 Azure SQL Database 간에 이동됩니다. 두 활동은 다음과 같습니다.

* 온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage 계정으로 데이터 복사
* Azure Blob Storage 계정에서 Azure SQL Database로 데이터를 복사합니다.

> [!NOTE]
> 여기에 표시된 단계는 ADF 팀이 제공하는 자세한 자습서 [온-프레미스 SQL Server 데이터베이스에서 Azure Blob Storage로 데이터 복사](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal/)에서 조정되었습니다. 필요한 경우 해당 항목의 관련 섹션에 대한 참조가 제공됩니다.
>
>

## <a name="prereqs"></a>필수 조건
이 자습서에서는 사용자가 다음을 보유하고 있다고 가정합니다.

* **Azure 구독**. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 등록할 수 있습니다.
* **Azure 저장소 계정**. 이 자습서에서는 데이터 저장을 위해 Azure 저장소 계정을 사용합니다. Azure 저장소 계정이 없는 경우 [저장소 계정 만들기](../../storage/common/storage-quickstart-create-account.md) 문서를 참조하세요. 저장소 계정을 만든 후에는 저장소 액세스에 사용되는 계정 키를 확보해야 합니다. [저장소 액세스 키 관리](../../storage/common/storage-account-manage.md#access-keys)를 참조하세요.
* **Azure SQL Database**에 대한 액세스. Azure SQL Database에 항목을 설정 해야 합니다 [Getting Started with Microsoft Azure SQL Database](../../sql-database/sql-database-get-started.md) Azure SQL Database의 새 인스턴스를 프로 비전 하는 방법에 대해 설명 합니다.
* 로컬로 설치 및 구성된 **Azure PowerShell** . 자세한 내용은 [Azure PowerShell 설치 및 구성법](/powershell/azure/overview)을 참조하세요.

> [!NOTE]
> 이 절차에서는 [Azure 포털](https://portal.azure.com/)을 사용합니다.
>
>

## <a name="upload-data"></a> 온-프레미스 SQL Server에 데이터 업로드
[NYC Taxi 데이터 세트](https://chriswhong.com/open-data/foil_nyc_taxi/)를 사용하여 마이그레이션 프로세스를 시연합니다. 해당 게시물에서 설명한 것처럼 NYC Taxi 데이터 세트는 Azure Blob Storage [NYC Taxi 데이터](https://www.andresmh.com/nyctaxitrips/)에서 제공됩니다. 데이터에는 두 개 파일이 있습니다. trip_data.csv 파일에는 여정 세부 정보가 들어 있고 trip_far.csv 파일에는 각 여정에 대한 요금 세부 정보가 들어 있습니다. 이러한 파일의 샘플 및 설명은 [NYC Taxi Trips 데이터 세트 설명](sql-walkthrough.md#dataset)에 제공됩니다.

자신의 데이터 세트에 여기에 제공된 절차를 도입하거나 NYC Taxi 데이터 세트를 사용하여 설명된 대로 단계를 따릅니다. NYC Taxi 데이터 세트를 온-프레미스 SQL Server 데이터베이스에 업로드하려면 [SQL Server Database로 대량 데이터 가져오기](sql-walkthrough.md#dbload)에 설명된 절차를 따릅니다. 이러한 지침은 Azure Virtual Machine의 SQL Server에 대한 내용이지만 온-프레미스 SQL Server로 업로드하는 절차는 동일합니다.

## <a name="create-adf"></a> Azure 데이터 팩터리 만들기
[Azure Portal](https://portal.azure.com/)에서 새 Azure Data Factory 및 리소스 그룹을 만들기 위한 지침은 [Azure Data Factory 만들기](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-data-factory)에서 제공됩니다. 새 ADF 인스턴스의 이름은 *adfdsp*이고 생성된 리소스 그룹은 *adfdsprg*입니다.

## <a name="install-and-configure-azure-data-factory-integration-runtime"></a>Azure Data Factory 통합 런타임 설치 및 구성
통합 런타임은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 고객 관리형 데이터 통합 인프라입니다. 이전에는 이 런타임을 “데이터 관리 게이트웨이”라고 했습니다.

설정할 [파이프라인을 만드는 방법에 대 한 지침](https://docs.microsoft.com/azure/data-factory/tutorial-hybrid-copy-portal#create-a-pipeline)

## <a name="adflinkedservices"></a>데이터 리소스에 연결할 연결된 서비스 만들기
연결된 서비스는 Azure 데이터 팩터리가 데이터 리소스에 연결하기 위해 필요한 정보를 정의합니다. 이 시나리오에는 연결된 서비스가 필요한 3개의 리소스가 있습니다.

1. 온-프레미스 SQL Server
2. Azure Blob Storage
3. Azure SQL 데이터베이스

연결된 서비스를 만들기 위한 단계별 절차가 [연결된 서비스 만들기](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)에 제공됩니다.


## <a name="adf-tables"></a>데이터 세트에 액세스하는 방법을 지정하는 테이블 정의 및 만들기
다음 스크립트 기반 프로시저로 데이터 세트의 구조, 위치 및 가용성을 지정하는 테이블을 만듭니다. 테이블을 정의하는 데 JSON 파일이 사용됩니다. 이러한 파일의 구조에 대한 자세한 내용은 [데이터 세트](../../data-factory/concepts-datasets-linked-services.md)를 참조하세요.

> [!NOTE]
> [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) cmdlet을 실행하기 전에 `Add-AzureAccount` cmdlet을 실행하여 명령 실행을 위해 Azure 구독을 선택했는지 확인해야 합니다. 이 cmdlet의 설명서는 [Add-azureaccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0)를 참조하세요.
>
>

테이블에서 JSON 기반 정의는 다음 이름을 사용합니다.

* 온-프레미스 SQL Server의 **테이블 이름**은 *nyctaxi_data*
* Azure Blob Storage 계정의 **컨테이너 이름**은 *containername*

이 ADF 파이프라인에는 3개의 테이블 정의가 필요합니다.

1. [SQL 온-프레미스 테이블](#adf-table-onprem-sql)
2. [Blob 테이블](#adf-table-blob-store)
3. [SQL Azure 테이블](#adf-table-azure-sql)

> [!NOTE]
> 이러한 절차에서는 Azure PowerShell을 사용하여 ADF 활동을 정의하고 만듭니다. 그러나 이러한 작업은 Azure 포털을 사용해서도 수행할 수 있습니다. 자세한 내용은 [데이터 세트 만들기](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)를 참조하세요.
>
>

### <a name="adf-table-onprem-sql"></a>SQL 온-프레미스 테이블
온-프레미스 SQL Server에 대한 테이블 정의는 다음 JSON 파일에 지정됩니다.

```json
{
    "name": "OnPremSQLTable",
    "properties":
    {
        "location":
        {
            "type": "OnPremisesSqlServerTableLocation",
            "tableName": "nyctaxi_data",
            "linkedServiceName": "adfonpremsql"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1,
            "waitOnExternal":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

여기서는 열 이름이 포함되지 않았습니다. 여기에 열 이름을 포함하여 열 이름을 sub-select할 수 있습니다(자세한 내용은 [ADF 설명서](../../data-factory/copy-activity-overview.md) 항목 참조).

테이블의 JSON 정의를 *onpremtabledef.json* 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\temp\onpremtabledef.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 테이블을 만듭니다.

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Blob 테이블
출력 blob 위치에 대한 테이블 정의는 다음과 같습니다(온-프레미스에서 수집된 데이터를 Azure blob에 매핑).

```json
{
    "name": "OutputBlobTable",
    "properties":
    {
        "location":
        {
            "type": "AzureBlobLocation",
            "folderPath": "containername",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            },
            "linkedServiceName": "adfds"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

테이블의 JSON 정의를 *bloboutputtabledef.json* 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\temp\bloboutputtabledef.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 테이블을 만듭니다.

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json

### <a name="adf-table-azure-sql"></a>SQL Azure 테이블
SQL Azure 출력에 대한 테이블 정의가 다음과 같습니다(이 스키마는 blob에서 가져온 데이터를 매핑).

```json
{
    "name": "OutputSQLAzureTable",
    "properties":
    {
        "structure":
        [
            { "name": "column1", "type": "String"},
            { "name": "column2", "type": "String"}
        ],
        "location":
        {
            "type": "AzureSqlTableLocation",
            "tableName": "your_db_name",
            "linkedServiceName": "adfdssqlazure_linked_servicename"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

테이블의 JSON 정의를 *AzureSqlTable.json*이라는 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\temp\AzureSqlTable.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 테이블을 만듭니다.

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json


## <a name="adf-pipeline"></a>파이프라인 정의 및 만들기
파이프라인에 포함될 활동을 지정하고 다음 스크립트 기반 프로시저로 파이프라인을 만듭니다. 파이프라인 속성을 정의하는 데 JSON 파일이 사용됩니다.

* 스크립트는 **파이프라인 이름** 이 *AMLDSProcessPipeline*이라고 가정합니다.
* 또한 파이프라인이 매일 정기적으로 실행되도록 파이프라인의 주기성을 설정하고 작업에 대한 기본 실행 시간을 사용합니다(오전 12시 UTC).

> [!NOTE]
> 다음 절차에서는 Azure PowerShell을 사용하여 ADF 파이프라인을 정의하고 만듭니다. 그러나 이러한 작업은 Azure 포털을 사용해서도 수행할 수 있습니다. 자세한 내용은 [파이프라인 만들기](../../data-factory/tutorial-hybrid-copy-portal.md#create-a-pipeline)를 참조하세요.
>
>

이전에 제공된 테이블 정의를 사용하여 ADF에 대한 파이프라인 정의는 다음과 같이 지정됩니다.

```json
{
    "name": "AMLDSProcessPipeline",
    "properties":
    {
        "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
        "activities":
        [
            {
                "name": "CopyFromSQLtoBlob",
                "description": "Copy data from on-premises SQL server to blob",
                "type": "CopyActivity",
                "inputs": [ {"name": "OnPremSQLTable"} ],
                "outputs": [ {"name": "OutputBlobTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from nyctaxi_data"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            },
            {
                "name": "CopyFromBlobtoSQLAzure",
                "description": "Push data to Sql Azure",
                "type": "CopyActivity",
                "inputs": [ {"name": "OutputBlobTable"} ],
                "outputs": [ {"name": "OutputSQLAzureTable"} ],
                "transformation":
                {
                    "source":
                    {
                        "type": "BlobSource"
                    },
                    "sink":
                    {
                        "type": "SqlSink",
                        "WriteBatchTimeout": "00:5:00",
                    }
                },
                "Policy":
                {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 2,
                    "timeout": "02:00:00"
                }
            }
        ]
    }
}
```

파이프라인의 이 JSON 정의를 *pipelinedef.json* 파일로 복사하고 알려진 위치에 저장합니다(여기서는 *C:\temp\pipelinedef.json*으로 간주). 다음 Azure PowerShell cmdlet을 사용하여 ADF에 파이프라인을 만듭니다.

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>파이프라인 시작
이제 다음 명령을 사용하여 파이프라인을 실행할 수 있습니다.

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

*startdate* 및 *enddate* 매개 변수 값을 파이프라인을 실행할 실제 날짜로 바꿔야 합니다.

파이프라인이 실행된 후에는 blob에 대해 선택한 컨테이너에 표시된 데이터를 하루에 한 개 파일로 볼 수 있습니다.

데이터를 증분 방식으로 파이프하는 ADF 제공 기능을 활용하지 않았습니다. 이 작업을 수행하는 방법 및 ADF에서 제공하는 기타 기능에 대한 자세한 내용은 [ADF 설명서](https://azure.microsoft.com/services/data-factory/)를 참조하세요.
