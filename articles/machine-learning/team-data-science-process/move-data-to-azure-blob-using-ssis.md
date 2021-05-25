---
title: SSIS 커넥터를 사용하여 Blob 스토리지 데이터 이동 - Team Data Science Process
description: Azure용 SQL Server Integration Services 기능 팩을 사용하여 Azure Blob Storage 간에 데이터를 이동하는 방법을 알아봅니다.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 9572d612e7ee8e2fd72850ba14447e8449f0f371
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322001"
---
# <a name="move-data-to-or-from-azure-blob-storage-using-ssis-connectors"></a>SSIS 커넥터를 사용하여 Azure Blob Storage의 데이터 이동
[Azure용 SQL Server Integration Services 기능 팩](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) 에서는 Azure에 연결하고, Azure와 온-프레미스 데이터 원본 간에 데이터를 전송하며, Azure에 저장된 데이터를 처리하는 구성 요소를 제공합니다.

[!INCLUDE [blob-storage-tool-selector](../../../includes/machine-learning-blob-storage-tool-selector.md)]

고객은 온-프레미스 데이터를 클라우드로 이동한 후에는 어떤 Azure 서비스에서든지 데이터에 액세스하여 Azure 기술 도구 모음의 완전한 기능을 활용할 수 있습니다. 예를 들어 Azure Machine Learning 또는 HDInsight 클러스터에서 추후에 데이터를 사용할 수 있습니다.

이러한 Azure 리소스의 사용의 예는 [SQL](sql-walkthrough.md) 및 [HDInsight](hive-walkthrough.md) 연습에 나와 있습니다.

SSIS를 사용하여 하이브리드 데이터 통합 시나리오에서 일반적인 비즈니스 요구 사항을 충족하는 정식 시나리오에 대한 자세한 내용은 [Azure용 SQL Server Integration Services 통합 팩으로 더 많은 작업 수행](https://techcommunity.microsoft.com/t5/sql-server-integration-services/doing-more-with-sql-server-integration-services-feature-pack-for/ba-p/388238) 블로그를 참조하세요.

> [!NOTE]
> Azure Blob Storage에 대한 전체 소개 내용은 [Azure Blob 기본 사항](../../storage/blobs/storage-quickstart-blobs-dotnet.md) 및 [Azure Blob Service](/rest/api/storageservices/Blob-Service-Concepts)를 참조하세요.
> 
> 

## <a name="prerequisites"></a>사전 요구 사항
이 문서에 설명된 작업을 수행하려면 Azure 구독과 Azure Storage 계정을 설정해야 합니다. 데이터를 업로드하거나 다운로드하려면 Azure Storage 계정 이름과 계정 키가 필요합니다.

* **Azure 구독** 을 설정하려면 [1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **스토리지 계정** 을 만들고 계정 및 키 정보를 가져오는 방법에 대한 지침은 [Azure Storage 계정 정보](../../storage/common/storage-account-create.md)를 참조하세요.

**SSIS 커넥터** 를 사용하려면 다음을 다운로드해야 합니다.

* **SQL Server 2014 또는 2016 Standard 이상**: 설치 파일에 SQL Server Integration Services가 포함되어 있습니다.
* **Azure용 Microsoft SQL Server 2014 또는 2016 Integration Services 기능 팩**: [SQL Server 2014 Integration Services](https://www.microsoft.com/download/details.aspx?id=47366) 및 [SQL Server 2016 Integration Services](https://www.microsoft.com/download/details.aspx?id=49492) 페이지에서 각각 커넥터를 다운로드할 수 있습니다.

> [!NOTE]
> SSIS는 SQL Server와 함께 설치되지만 Express 버전에는 포함되어 있지 않습니다. 다양한 버전의 SQL Server에 포함된 애플리케이션에 대한 자세한 내용은 [SQL Server 버전](https://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/)을 참조하세요.
> 
> 

SSIS에 대한 교육 자료는 [SSIS에 대한 실습 교육](https://www.microsoft.com/sql-server/training-certification)

SISS를 사용하여 간단한 ETL(추출, 변환 및 로드) 패키지를 빌드하는 방법에 대한 자세한 내용은 [SSIS 자습서: 간단한 ETL 패키지 만들기](/sql/integration-services/ssis-how-to-create-an-etl-package)를 참조하세요.

## <a name="download-nyc-taxi-dataset"></a>NYC Taxi 데이터 세트 다운로드
여기에 설명된 예제에서는 공개적으로 제공되는 데이터 세트인 [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) 데이터 세트를 사용합니다. 이 데이터 세트는 2013년 뉴욕 시의 1억 7,300만 건에 달하는 택시 승차 기록으로 구성됩니다. 데이터는 여정 정보 데이터와 요금 데이터의 두 종류가 있습니다. 월별로 하나의 파일이 있으므로 24개의 파일이 있으며, 각 파일은 압축되지 않은 크기가 약 2GB입니다.

## <a name="upload-data-to-azure-blob-storage"></a>Azure Blob Storage에 데이터 업로드
SSIS 기능 팩을 사용하여 온-프레미스에서 Azure Blob Storage로 데이터를 이동하려면 다음과 같이 [**Azure Blob 업로드 작업**](/sql/integration-services/control-flow/azure-blob-upload-task)의 인스턴스를 사용합니다.

![configure-data-science-vm](./media/move-data-to-azure-blob-using-ssis/ssis-azure-blob-upload-task.png)

작업에 사용되는 매개 변수는 다음과 같습니다.

| 필드 | 설명 |
| --- | --- |
| **AzureStorageConnection** |기존 Azure Storage 연결 관리자를 지정하거나, blob 파일이 호스트되는 지점을 가리키는 Azure Storage 계정을 참조하는 새 Azure Storage 연결 관리자를 만듭니다. |
| **BlobContainer** |업로드된 파일을 blob으로 유지하는 blob 컨테이너의 이름을 지정합니다. |
| **BlobDirectory** |업로드한 파일이 블록 blob으로 저장되는 blob 디렉터리를 지정합니다. blob 디렉터리는 가상 계층 구조입니다. blob가 이미 있는 경우 바뀝니다. |
| **LocalDirectory** |업로드할 파일이 포함된 로컬 디렉터리를 지정합니다. |
| **FileName** |지정된 이름 패턴의 파일을 선택할 이름 필터를 지정합니다. 예를 들어 MySheet\*.xls\*는 MySheet001.xls 및 MySheetABC.xlsx와 같은 파일을 포함합니다. |
| **TimeRangeFrom/TimeRangeTo** |시간 범위 필터를 지정합니다. *TimeRangeFrom* 에서 *TimeRangeTo* 사이에 수정된 파일이 포함됩니다. |

> [!NOTE]
> 전송을 시도하기 전에 **AzureStorageConnection** 자격 증명이 올바르고 **BlobContainer** 가 있어야 합니다.
> 
> 

## <a name="download-data-from-azure-blob-storage"></a>Azure Blob Storage에서 데이터 다운로드
SSIS를 사용하여 Azure Blob Storage에서 온-프레미스 스토리지로 데이터를 다운로드하려면 [Azure Blob 다운로드 작업](/sql/integration-services/control-flow/azure-blob-download-task)의 인스턴스를 사용합니다.

## <a name="more-advanced-ssis-azure-scenarios"></a>고급 SSIS-Azure 시나리오
SSIS 기능 팩을 사용하면 패키징 작업을 통해 보다 복잡한 흐름을 처리할 수 있습니다. 예를 들어 blob 데이터를 HDInsight 클러스터에 직접 공급하여 해당 출력을 blob로 다시 다운로드한 다음 온-프레미스 스토리지에 다운로드할 수 있습니다. SSIS는 추가 SSIS 커넥터를 사용하여 HDInsight 클러스터에서 Hive 및 Pig 작업을 실행할 수 있습니다.

* SSIS를 사용하여 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하려면 [Azure HDInsight Hive 작업](/sql/integration-services/control-flow/azure-hdinsight-hive-task)을 사용합니다.
* SSIS를 사용하여 Azure HDInsight 클러스터에서 Pig 스크립트를 실행하려면 [Azure HDInsight Pig 작업](/sql/integration-services/control-flow/azure-hdinsight-pig-task)을 사용합니다.