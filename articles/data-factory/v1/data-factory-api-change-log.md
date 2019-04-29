---
title: Data Factory - .NET API 변경 로그 | Microsoft Docs
description: Azure Data Factory에 대한 특정 .NET API 버전의 주요 변경 내용, 기능 추가 사항, 버그 수정 등을 설명합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 863f3500c84eeab1c3dac19141cd334fc6961694
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567251"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure 데이터 팩터리 - .NET API 변경 로그
> [!NOTE]
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 

이 문서에서는 특정 버전의 Azure Data Factory SDK 변경 내용에 대해 설명합니다.  [여기](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>버전 4.11.0
기능 추가 사항:

* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* 다음과 같은 원본 복사 형식이 추가되었습니다.
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4.10.0 버전
* 다음 선택적 속성이 TextFormat에 추가되었습니다.
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* 다음과 같은 원본 복사 형식이 추가되었습니다.
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) 속성을 AzureMLBatchExecutionActivity에 추가합니다.
  * 다중 웹 서비스 입력을 Azure Machine Learning 실험에 전달하도록 설정합니다.

## <a name="version-491"></a>버전 4.9.1
### <a name="bug-fix"></a>버그 수정
* [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)에 대한 WebApi 기반 인증이 더 이상 사용되지 않습니다.

## <a name="version-490"></a>버전 4.9.0
### <a name="feature-additions"></a>기능 추가 사항
* CopyActivity에 [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) 및 [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) 속성을 추가합니다. 기능에 대한 자세한 내용은 [준비된 복사](data-factory-copy-activity-performance.md#staged-copy) 를 참조하세요.

### <a name="bug-fix"></a>버그 수정
* [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) 인스턴스를 사용하는 [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) 메서드의 오버로드를 지정합니다.
* [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) 및 [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx)를 CopySink에 옵션으로 표시합니다.

## <a name="version-480"></a>버전 4.8.0
### <a name="feature-additions"></a>기능 추가 사항
* 복사 성능을 조정할 수 있도록 하기 위해 복사 작업 유형에 다음과 같은 옵션 속성이 추가되었습니다.
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>버전 4.7.0
### <a name="feature-additions"></a>기능 추가 사항
* 파일을 ORC(Optimized Row Columnar) 형식으로 복사하기 위해 새 StorageFormat 형식 [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) 형식이 추가되었습니다.
* [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) 및 PolyBaseSettings 속성을 SqlDWSink에 추가합니다.
  * PolyBase를 사용하여 SQL Data Warehouse로 데이터를 복사할 수 있도록 합니다.

## <a name="version-461"></a>버전 4.6.1
### <a name="bug-fixes"></a>버그 수정
* 작업 창을 나열하기 위한 HTTP 요청을 해결합니다.
  * 요청 페이로드에서 리소스 그룹 이름 및 데이터 팩터리 이름을 제거합니다.

## <a name="version-460"></a>버전 4.6.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음 속성이 [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)에 추가되었습니다.
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [데이터 세트](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* 다음 속성이 [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)에 추가되었습니다.
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* 데이터가 JSON 형식인 데이터 세트를 정의하는 [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) 유형 [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) 유형이 새로 추가되었습니다.

## <a name="version-450"></a>버전 4.5.0
### <a name="feature-additions"></a>기능 추가 사항
* [작업 창에 대한 작업 목록](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)이 추가되었습니다.
  * 엔터티 형식(즉, 데이터 팩터리, 데이터 세트, 파이프라인 및 작업)을 기준으로 필터를 사용하여 작업 창을 검색하는 메서드가 추가되었습니다.
* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* 다음과 같은 원본 복사 형식이 추가되었습니다.     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>버전 4.4.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음 연결된 서비스 유형이 복사 작업에 대한 데이터 원본 및 싱크로 추가되었습니다.
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). 개념 정보 및 예제는 [Azure Storage SAS 연결된 서비스](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) 를 참조하세요.

## <a name="version-430"></a>버전 4.3.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음 연결된 서비스 유형이 복사 작업에 대한 데이터 원본으로 추가되었습니다.
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). 개념 정보 및 예제는 [Data Factory를 사용하여 HDFS에서 데이터 이동](data-factory-hdfs-connector.md) 을 참조하세요.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). 개념 정보 및 예제는 [Azure Data Factory를 사용하여 ODBC 데이터 저장소에서 데이터 이동](data-factory-odbc-connector.md) 을 참조하세요.

## <a name="version-420"></a>버전 4.2.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음과 같은 새로운 활동 유형이 추가되었습니다. [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). [업데이트 리소스 활동을 사용하여 Azure ML 모델 업데이트](data-factory-azure-ml-batch-execution-activity.md)를 참조하세요.
* 새 선택 속성 [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx)가 [AzureMLLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)에 추가되었습니다.
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) 및 [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) 속성이 [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) 클래스에 추가되었습니다.
* 데이터 팩터리 서비스에 대한 클라이언트 호출의 시간 제한 구성을 허용합니다.

## <a name="version-410"></a>Version 4.1.0
### <a name="feature-additions"></a>기능 추가 사항
* 다음에 링크된 서비스 유형이 추가 되었습니다.
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 다음 활동 유형이 추가 되었습니다.
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 다음과 같은 데이터 세트가 추가 되었습니다.
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 다음 복사 작업에 대한 원본 및 싱크 유형은 추가 되었습니다.
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>버전 4.0.1
### <a name="breaking-changes"></a>주요 변경 내용
다음 클래스의 이름이 변경되었습니다. 새 이름은 4.0.0 릴리스 전의 클래스 원래 이름이었습니다.

| 4.0.0의 이름 | 4.0.1의 이름 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>버전 4.0.0
### <a name="breaking-changes"></a>주요 변경 내용
* 다음 클래스/인터페이스의 이름이 변경되었습니다.

| 이전 이름 | 새 이름 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| 테이블 |[데이터 세트](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **List** 메서드는 이제 페이징된 결과를 반환합니다. 응답에 비어 있지 않은 **NextLink** 속성이 포함된 경우 클라이언트 애플리케이션은 모든 페이지가 반환될 때까지 다음 페이지를 계속 가져와야 합니다.  다음은 예제입니다.

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** 파이프라인 API는 전체 세부 정보 대신 파이프라인의 요약만 반환합니다. 예를 들어 파이프라인 요약의 작업에는 이름과 형식만 포함됩니다.

### <a name="feature-additions"></a>기능 추가 사항
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) 클래스는 두 가지 새로운 속성인 **SliceIdentifierColumnName** 및 **SqlWriterCleanupScript**를 지원하여 Azure SQL Data Warehouse에 대한 idempotent 복사를 지원합니다. 이러한 속성에 대한 자세한 내용은 [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) 문서를 참조하세요.
* 이제 복사 작업의 일부로 Azure SQL Database 및 Azure SQL Data Warehouse 소스에 대한 저장 프로시저 실행을 지원합니다. [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) 및 [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) 클래스에는 **SqlReaderStoredProcedureName** 및 **StoredProcedureParameters**와 같은 속성이 있습니다. 이러한 속성에 대한 자세한 내용은 Azure.com에서 [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) 및 [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) 문서를 참조하세요.  
