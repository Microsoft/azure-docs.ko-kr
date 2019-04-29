---
title: Azure Data Factory에서 지원하는 컴퓨팅 환경 | Microsoft Docs
description: '데이터의 변환 또는 처리를 위해 Azure Data Factory 파이프라인(예: Azure HDInsight)에서 사용할 수 있는 컴퓨팅 환경을 알아봅니다.'
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 0e0a249c53c90d3d8d03dcdb5fbb4f11f31c54df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565721"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure Data Factory에서 지원하는 컴퓨팅 환경
> [!NOTE]
> 이 문서의 내용은 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [컴퓨팅 연결 서비스](../compute-linked-services.md)를 참조하세요.

이 문서에서는 데이터를 처리하거나 변환하는 데 사용할 수 있는 컴퓨팅 환경에 대해 설명합니다. 또한 이러한 컴퓨팅 환경을 Azure 데이터 팩터리로 연결하는 연결된 서비스를 구성할 때 Data Factory에서 지원하는 다양한 구성(on-demand 및 bring-your-own)에 대한 세부 정보를 제공합니다.

다음 테이블에는 Data Factory에서 지원하는 컴퓨팅 환경 목록과 여기서 실행할 수 있는 작업이 제공됩니다. 

| 컴퓨팅 환경                      | 활동                               |
| ---------------------------------------- | ---------------------------------------- |
| [주문형 Azure HDInsight 클러스터](#azure-hdinsight-on-demand-linked-service) 또는 [사용자 자신의 HDInsight 클러스터](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop 스트리밍](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Machine Learning 작업: 일괄 처리 실행 및 리소스 업데이트](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure 데이터 레이크 분석](#azure-data-lake-analytics-linked-service) | [데이터 레이크 분석 U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-linked-service), [SQL Server](#sql-server-linked-service) | [저장 프로시저 작업](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>Data Factory에서 지원되는 HDInsight 버전
Azure HDInsight는 언제든 배포할 수 있는 여러 Hadoop 클러스터 버전을 지원합니다. 지원되는 각 버전은 특정 버전의 HDP(Hortonworks Data Platform) 배포 및 배포의 구성 요소 집합을 생성합니다. 

Microsoft는 지원되는 HDInsight 버전 목록을 최신 Hadoop 에코시스템 구성 요소 및 수정 사항으로 업데이트합니다. 자세한 내용은 [지원되는 HDInsight 버전](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)을 참조하세요.

> [!IMPORTANT]
> Linux 기반 HDInsight 버전 3.3은 2017년 7월 31일 사용이 중지되었습니다. Data Factory 버전 1 주문형 HDInsight 연결된 서비스 고객은 2017년 12월 15일까지 최신 버전의 HDInsight로 업그레이드 및 테스트할 수 있습니다. Windows 기반 HDInsight는 2018년 7월 31일에 사용이 중지됩니다.
>
> 

### <a name="after-the-retirement-date"></a>사용 중지 날짜 이후 

2017년 12월 15일 이후:

- Data Factory 버전 1에서 주문형 HDInsight 연결된 서비스를 사용하여 Linux 기반 HDInsight 버전 3.3(또는 이전 버전) 클러스터를 더 이상 만들 수 없습니다. 
- [**osType** 및/또는 **Version** 속성](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)이 기존 Data Factory 버전1 주문형 HDInsight 연결된 서비스에 대한 JSON 정의에 명시적으로 지정되지 않은 경우 기본값이 **Version=3.1, osType=Windows**에서 **Version=\<latest HDI default version\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning), osType=Linux**로 변경됩니다.

2018년 7월 31일 이후:

- Data Factory 버전 1에서 주문형 HDInsight 연결된 서비스를 사용하여 어떠한 버전의 Windows 기반 HDInsight 클러스터도 만들 수 없습니다. 

### <a name="recommended-actions"></a>권장 작업 

- 최신 Hadoop 에코시스템 구성 요소 및 수정 사항을 사용하려면, 영향을 받는 Data Factory 버전 1 주문형 HDInsight 연결된 서비스 정의에서 [**osType** 및 **Version** 속성](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)을 새로운 Linux 기반 HDInsight 버전(HDInsight 3.6)으로 업데이트합니다. 
- 2017년 12월 15일 이전에 영향을 받은 연결된 서비스를 참조하는 Data Factory 버전 1 Hive, Pig, MapReduce 및 Hadoop 스트리밍 작업을 테스트합니다. 새로운 **osType** 및 **Version** 기본값(**Version=3.6**, **osType=Linux**)이나 업그레이드할 명시적인 HDInsight 버전 및 OS 종류와 호환되는지 확인합니다. 
  호환성에 대해 자세히 알아보려면 [Windows 기반 HDInsight 클러스터에서 Linux 기반 클러스터로 마이그레이션](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux) 및 [HDInsight에서 사용할 수 있는 Hadoop 구성 요소 및 버전은 무엇인가요?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions)를 참조하세요. 
- Data Factory 버전 1 주문형 HDInsight 연결된 서비스를 사용하여 Windows 기반 HDInsight 클러스터를 계속 만들려면, 2017년 12월 15일 전에 **osType**을 명시적으로 **Windows**라고 설정합니다. 2018년 7월 31일 전에 Linux 기반 HDInsight 클러스터로 마이그레이션하는 것이 좋습니다. 
- 주문형 HDInsight 연결된 서비스를 사용하여 Data Factory 버전 1 DotNet 사용자 지정 작업을 실행 중인 경우, Azure Batch 연결된 서비스를 대신 사용하도록 DotNet 사용자 지정 작업 JSON 정의를 업데이트합니다. 자세한 내용은 [데이터 팩터리 파이프라인에서 사용자 지정 작업 사용](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)을 참조하세요. 

> [!Note]
> Data Factory 버전 1에서 기존의, 자체(bring-your-own) 클러스터 HDInsight 연결된 디바이스를 사용하거나 Azure Data Factory에서 자체(bring-your-own) 및 주문형 HDInsight 연결된 서비스를 사용하는 경우에는 별도의 조치가 필요하지 않습니다. 이러한 시나리오에는 HDInsight 클러스터의 최신 버전 지원 정책이 이미 적용되고 있습니다. 
>
> 


## <a name="on-demand-compute-environment"></a>주문형 컴퓨팅 환경
주문형 구성에서는 Data Factory가 컴퓨팅 환경을 완벽하게 관리합니다. 데이터 처리를 위해 작업을 제출하기 전에 Data Factory가 컴퓨팅 환경을 자동으로 생성합니다. 작업을 마치면 Data Factory가 컴퓨팅 환경을 제거합니다. 

주문형 컴퓨팅 환경에 대해 연결된 서비스를 만들 수 있습니다. 연결된 서비스를 사용하여 컴퓨팅 환경을 구성하고 작업 실행, 클러스터 관리 및 부트스트랩 작업에 대한 세부적인 설정을 제어합니다.

> [!NOTE]
> 주문형 구성은 현재 HDInsight 클러스터에 대해서만 지원됩니다.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight 주문형 연결된 서비스
Data Factory는 데이터 처리를 위해 Windows 기반 또는 Linux 기반 주문형 HDInsight 클러스터를 자동으로 만들 수 있습니다. 클러스터는 클러스터와 연결된 저장소 계정과 동일한 지역에 만들어집니다. JSON **linkedServiceName** 속성을 사용하여 클러스터를 만듭니다.

주문형 HDInsight 연결된 서비스에 대한 다음 *키* 포인트에 유의합니다.

* 주문형 HDInsight 클러스터는 Azure 구독에 표시되지 않습니다. Data Factory 서비스는 사용자를 대신해 주문형 HDInsight 클러스터를 관리합니다.
* 주문형 HDInsight 클러스터에서 실행하는 작업에 대한 로그는 HDInsight 클러스터와 연결된 스토리지 계정에 복사됩니다. 이러한 로그에 액세스하려면 Azure Portal에서 **활동 실행 세부 정보** 창으로 이동합니다. 자세한 내용은 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md)를 참조하세요.
* HDInsight 클러스터가 가동되고 작업을 실행하는 동안만 요금이 청구됩니다.

> [!IMPORTANT]
> 주문형 HDInsight 클러스터를 프로비전하는 데 일반적으로 *20분* 이상이 걸립니다.
>
> 

### <a name="example"></a>예
다음 JSON는 Linux 기반 주문형 HDInsight 연결된 서비스를 정의합니다. Data Factory는 데이터 조각을 처리할 때 *Linux 기반* HDInsight 클러스터를 자동으로 만듭니다. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> HDInsight 클러스터는 JSON **linkedServiceName** 속성에 지정한 Azure Blob Storage에 *기본 컨테이너*를 만듭니다. 기본적으로 HDInsight는 클러스터가 삭제될 때 이 컨테이너를 삭제하지 않습니다. 주문형 HDInsight 연결된 서비스에서는 기존 라이브 클러스터(**timeToLive**)가 없는 경우 조각을 처리해야 할 때마다 HDInsight 클러스터가 만들어집니다. 클러스터는 처리가 완료되면 삭제됩니다. 
>
> 처리되는 조각이 많을수록 Blob Storage에 컨테이너가 많아집니다. 작업의 문제 해결에 컨테이너가 필요하지 않으면 저장소 비용을 줄이기 위해 컨테이너를 삭제할 수 있습니다. 이러한 컨테이너의 이름은 `adf<your Data Factory name>-<linked service name>-<date and time>` 패턴을 따릅니다. [Microsoft Storage 탐색기](https://storageexplorer.com/) 같은 도구를 사용하여 Blob 스토리지에서 컨테이너를 삭제할 수 있습니다.
>
> 

### <a name="properties"></a>properties
| 자산                     | 설명                              | 필수 |
| ---------------------------- | ---------------------------------------- | -------- |
| 형식                         | type 속성을 **HDInsightOnDemand**로 설정합니다. | 예      |
| clusterSize                  | 클러스터의 작업자 및 데이터 노드 수입니다. HDInsight 클러스터는 속성에 대해 지정하는 작업자 노드 수 외에 2개의 헤드 노드로 생성됩니다. 노드의 크기는 Standard_D3이며 4개의 코어가 있습니다. 4개 작업자 노드 클러스터는 24개 코어(작업자 노드용 4\*4 = 16코어 및 헤드 노드용 2\*4 = 8코어)를 사용합니다. Standard_D3 계층에 대한 자세한 내용은 [HDInsight에서 Linux 기반 Hadoop 클러스터 만들기](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요. | 예      |
| timeToLive                   | 주문형 HDInsight 클러스터에 대한 허용된 유휴 시간입니다. 클러스터에 다른 활성 작업이 없는 경우 활동 실행이 완료될 때 주문형 HDInsight 클러스터가 유지될 기간을 지정합니다.<br /><br />예를 들어 활동 실행에 6분이 걸리고 **timeToLive**가 5분으로 설정된 경우 클러스터는 활동 실행을 처리하는 6분 후에 5분 동안 유지됩니다. 또 다른 활동 실행이 6분간 실행되는 경우 동일한 클러스터에 의해 처리됩니다.<br /><br />주문형 HDInsight 클러스터 만들기는 비용이 많이 드는 작업이며 시간이 걸릴 수 있습니다. 필요에 따라 이 설정을 사용하면 주문형 HDInsight 클러스터를 다시 사용하여 데이터 팩터리의 성능을 향상시킵니다.<br /><br />**timeToLive** 값을 **0**으로 설정한 경우 클러스터는 활동 실행을 마치는 즉시 삭제됩니다. 하지만 높은 값을 설정하면 클러스터가 유휴 상태로 유지되어 불필요하게 많이 비용이 발생할 수 있습니다. 필요에 따라 적절한 값을 설정하는 것이 중요합니다.<br /><br />**timeToLive** 값이 적절하게 설정되는 경우 여러 파이프라인은 주문형 HDInsight 클러스터의 인스턴스를 공유할 수 있습니다. | 예      |
| 버전                      | HDInsight 클러스터의 버전입니다. HDInsight 버전은 [지원되는 HDInsight 버전](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions)을 참조하세요. 이 값을 지정하지 않으면 [마지막 HDI 기본값](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)이 사용됩니다. | 아닙니다.       |
| linkedServiceName            | 데이터를 저장 및 처리하기 위해 주문형 클러스터에서 사용하는 Azure Storage 연결된 서비스입니다. HDInsight 클러스터는 저장소 계정과 동일한 지역에 생성됩니다.<p>현재 Azure Data Lake Store를 저장소로 사용하는 주문형 HDInsight 클러스터를 만들 수 없습니다. HDInsight 처리의 결과 데이터를 Data Lake Store에 저장하려면 활동 복사를 사용하여 Blob Storage의 데이터를 Data Lake Store로 복사합니다. </p> | 예      |
| additionalLinkedServiceNames | HDInsight 연결된 서비스에 대한 추가 저장소 계정을 지정합니다. Data Factory는 사용자 대신 저장소 계정을 등록합니다. 이러한 저장소 계정은 HDInsight 클러스터와 동일한 지역에 있어야 합니다. HDInsight 클러스터는 **linkedServiceName** 속성에 지정된 저장소 계정과 동일한 지역에 만들어집니다. | 아닙니다.       |
| osType                       | 운영 체제 유형입니다. 허용되는 값은 **Linux** 및 **Windows**입니다. 값을 지정하지 않으면 **Linux**가 사용됩니다.  <br /><br />Linux 기반 HDInsight 클러스터를 사용하는 것이 좋습니다. Windows HDInsight의 사용 중지 날짜는 2018년 7월 31일입니다. | 아닙니다.       |
| hcatalogLinkedServiceName    | HCatalog 데이터베이스를 가리키는 Azure SQL 연결된 서비스 이름입니다. 주문형 HDInsight 클러스터는 SQL Database를 metastore로 사용하여 만들어집니다. | 아닙니다.       |

#### <a name="example-linkedservicenames-json"></a>예제: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>고급 속성
또한 주문형 HDInsight 클러스터의 세부적인 구성에 다음 속성을 지정할 수 있습니다.

| 자산               | 설명                              | 필수 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 만들어지는 HDInsight 클러스터에 대한 핵심 구성 매개 변수(core-site.xml)를 지정합니다. | 아닙니다.       |
| hBaseConfiguration     | HDInsight 클러스터에 대한 HBase 구성 매개 변수(hbase-site.xml)를 지정합니다. | 아닙니다.       |
| hdfsConfiguration      | HDInsight 클러스터에 대한 HDFS 구성 매개 변수(hdfs-site.xml)를 지정합니다. | 아닙니다.       |
| hiveConfiguration      | HDInsight 클러스터에 대한 Hive 구성 매개 변수(hive-site.xml)를 지정합니다. | 아닙니다.       |
| mapReduceConfiguration | HDInsight 클러스터에 대한 MapReduce 구성 매개 변수(mapred-site.xml)를 지정합니다. | 아닙니다.       |
| oozieConfiguration     | HDInsight 클러스터에 대한 Oozie 구성 매개 변수(oozie-site.xml)를 지정합니다. | 아닙니다.       |
| stormConfiguration     | HDInsight 클러스터에 대한 Storm 구성 매개 변수(storm-site.xml)를 지정합니다. | 아닙니다.       |
| yarnConfiguration      | HDInsight 클러스터에 대한 YARN 구성 매개 변수(yarn-site.xml)를 지정합니다. | 아닙니다.       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>예제: 고급 속성을 포함하는 주문형 HDInsight 클러스터 구성

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>노드 크기
헤드, 데이터 및 ZooKeeper 노드의 크기를 지정하려면 다음 속성을 사용합니다. 

| 자산          | 설명                              | 필수 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | 헤드 노드의 크기를 설정합니다. 기본값은 **Standard_D3**입니다. 자세한 내용은 [노드 크기 지정](#specify-node-sizes)을 참조하세요. | 아닙니다.       |
| dataNodeSize      | 데이터 노드의 크기를 설정합니다. 기본값은 **Standard_D3**입니다. | 아닙니다.       |
| zookeeperNodeSize | ZooKeeper 노드의 크기를 설정합니다. 기본값은 **Standard_D3**입니다. | 아닙니다.       |

#### <a name="specify-node-sizes"></a>노드 크기 지정
이전 섹션에 설명된 속성에 대해 지정해야 하는 문자열 값은 [가상 머신 크기](../../virtual-machines/linux/sizes.md)를 참조하세요. 값은 [가상 머신 크기](../../virtual-machines/linux/sizes.md)에서 참조되는 cmdlet 및 API를 준수해야 합니다. 큰(기본값) 데이터 노드의 크기에는 7GB의 메모리가 포함됩니다. 이 시나리오에는 충분하지 않을 수 있습니다. 

D4 크기의 헤드 노드 및 작업자 노드를 만들려는 경우 **headNodeSize** 및 **dataNodeSize** 속성에 대한 값으로 **Standard_D4**를 지정합니다. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

이러한 속성에 잘못된 값을 설정하면 다음 메시지가 표시될 수 있습니다.

  클러스터를 만들지 못했습니다. 예외: 클러스터 만들기 작업을 완료할 수 없습니다. 작업이 실패했습니다. 오류 코드는 '400'입니다. 클러스터의 상태가 '오류'로 남아 있습니다. 메시지: 'PreClusterCreationValidationFailure' 
  
이 메시지가 표시되면 [가상 머신 크기](../../virtual-machines/linux/sizes.md) 테이블의 cmdlet 및 API 이름을 사용하고 있는지 확인합니다.  

> [!NOTE]
> 현재 Data Factory는 Data Lake Store를 기본 저장소로 사용하는 HDInsight 클러스터를 지원하지 않습니다. Azure Storage를 HDInsight 클러스터의 기본 저장소로 사용합니다. 
>
> 


## <a name="bring-your-own-compute-environment"></a>사용자 고유의 컴퓨팅 환경 가져오기
기존 컴퓨팅 환경을 Data Factory에서 연결된 서비스로 등록할 수 있습니다. 컴퓨팅 환경은 사용자가 관리합니다. Data Factory 서비스는 컴퓨팅 환경을 사용하여 활동을 실행합니다.

이 구성의 형식은 다음의 컴퓨팅 환경에 대해 지원됩니다.

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure 데이터 레이크 분석
* Azure SQL Database, Azure SQL Data Warehouse, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 연결된 서비스
HDInsight 연결된 서비스를 만들어서 데이터 팩터리로 사용자의 자체적인 HDInsight 클러스터를 등록할 수 있습니다.

### <a name="example"></a>예

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>properties
| 자산          | 설명                              | 필수 |
| ----------------- | ---------------------------------------- | -------- |
| 형식              | type 속성을 **HDInsight**로 설정합니다. | 예      |
| clusterUri        | HDInsight 클러스터의 URI입니다.        | 예      |
| 사용자 이름          | 기존 HDInsight 클러스터에 연결하는 데 사용할 사용자 계정의 이름입니다. | 예      |
| 암호          | 사용자 계정의 암호입니다.   | 예      |
| linkedServiceName | HDInsight 클러스터에서 사용하는 Blob Storage를 참조하는 스토리지 연결된 서비스의 이름입니다. <p>현재는 이 속성에 대한 Data Lake Store 연결된 서비스를 지정할 수 없습니다. HDInsight 클러스터가 Data Lake Store에 대한 액세스 권한이 있으면 Hive 또는 Pig 스크립트에서 Data Lake Store의 데이터에 액세스할 수 있습니다. </p> | 예      |

## <a name="azure-batch-linked-service"></a>Azure Batch 연결된 서비스
Batch 연결된 서비스를 만들어 가상 머신(VM)의 Batch 풀을 데이터 팩터리에 등록할 수 있습니다. Batch 또는 HDInsight를 사용하여 Microsoft .NET 사용자 지정 활동을 실행할 수 있습니다.

Batch 서비스를 처음 사용하는 경우:

* [Azure Batch 기본 사항](../../batch/batch-technical-overview.md)에 대해 알아봅니다.
* [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet에 대해 알아봅니다. 이 cmdlet을 사용하여 Batch 계정을 만듭니다. 또는 [Azure Portal](../../batch/batch-account-create-portal.md)을 사용하여 Batch 계정을 만들 수 있습니다. cmdlet 사용에 대한 자세한 내용은 [PowerShell을 사용하여 Batch 계정 관리](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)를 참조하세요.
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet에 대해 자세히 알아봅니다. 이 cmdlet을 사용하여 Batch 풀을 만듭니다.

### <a name="example"></a>예

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

**accountName** 속성의 경우 일괄 처리 계정의 이름에 **.\<region name\>** 을 추가합니다. 예를 들면 다음과 같습니다.

```json
"accountName": "mybatchaccount.eastus"
```

또 다른 옵션은 **batchUri** 엔드포인트를 제공하는 것입니다. 예를 들면 다음과 같습니다.

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>properties
| 자산          | 설명                              | 필수 |
| ----------------- | ---------------------------------------- | -------- |
| 형식              | type 속성을 **AzureBatch**로 설정합니다. | 예      |
| accountName       | Batch 계정의 이름입니다.         | 예      |
| accessKey         | Batch 계정에 대한 액세스 키입니다.  | 예      |
| poolName          | VM의 풀 이름입니다.    | 예      |
| linkedServiceName | Batch 연결된 서비스와 관련된 저장소 연결된 서비스의 이름입니다. 이 연결된 서비스는 활동을 실행하는 데 필요한 파일을 스테이징하고 활동 실행 로그를 저장하는 데 사용됩니다. | 예      |

## <a name="azure-machine-learning-linked-service"></a>Azure Machine Learning 연결된 서비스
Machine Learning 연결된 서비스를 만들어서 Machine Learning 일괄 처리 점수 매기기 엔드포인트를 데이터 팩터리에 등록합니다.

### <a name="example"></a>예

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>properties
| 자산   | 설명                              | 필수 |
| ---------- | ---------------------------------------- | -------- |
| Type       | type 속성을 **AzureML**로 설정합니다. | 예      |
| mlEndpoint | 일괄 처리 점수 매기기 URL입니다.                   | 예      |
| apiKey     | 게시된 작업 영역 모델의 API입니다.     | 예      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 연결된 서비스
Data Lake Analytics 연결된 서비스를 만들어서 Data Lake Analytics 컴퓨팅 서비스를 Azure 데이터 팩터리에 연결할 수 있습니다. 파이프라인에서 데이터 레이크 분석 U-SQL 작업은 이 연결된 서비스를 가리킵니다. 

다음 표에서는 JSON 정의에서 사용되는 일반 속성을 설명합니다.

| 자산                 | 설명                              | 필수                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| 형식                 | type 속성을 **AzureDataLakeAnalytics**로 설정합니다. | 예                                      |
| accountName          | Data Lake Analytics 계정 이름입니다.  | 예                                      |
| dataLakeAnalyticsUri | Data Lake Analytics URI입니다.           | 아닙니다.                                       |
| subscriptionId       | Azure 구독 ID입니다.                    | 아닙니다.<br /><br />(지정하지 않으면 데이터 팩터리 구독이 사용됩니다.) |
| resourceGroupName    | Azure 리소스 그룹 이름입니다.                | 아닙니다.<br /><br /> (지정하지 않으면 데이터 팩터리 리소스 그룹이 사용됩니다.) |

### <a name="authentication-options"></a>인증 옵션
Data Lake Analytics 연결된 서비스에 대해 서비스 주체 또는 사용자 자격 증명을 사용한 인증을 선택할 수 있습니다.

#### <a name="service-principal-authentication-recommended"></a>서비스 주체 인증(권장)
서비스 주체 인증을 사용하려면 Azure AD(Azure Active Directory)에서 애플리케이션 엔터티를 등록합니다. 그런 다음, Data Lake Store에 Azure AD 액세스를 부여합니다. 자세한 단계는 [서비스 간 인증](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.
* 애플리케이션 UI
* 애플리케이션 키 
* 테넌트 ID

다음 속성을 지정하여 서비스 주체 인증을 사용합니다.

| 자산                | 설명                              | 필수 |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | 애플리케이션의 클라이언트 ID입니다.     | 예      |
| servicePrincipalKey | 애플리케이션의 키입니다.           | 예      |
| tenant              | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)입니다. 이 정보를 얻으려면 Azure Portal의 오른쪽 위 모서리에 마우스를 올려 놓습니다. | 예      |

**예제: 서비스 주체 인증**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>사용자 자격 증명 인증
Data Lake Analytics에 대한 사용자 자격 증명 인증의 경우 다음 속성을 지정합니다.

| 자산          | 설명                              | 필수 |
| :---------------- | :--------------------------------------- | :------- |
| 권한 부여 | Data Factory Editor에서 **권한 부여** 단추를 선택합니다. 이 속성에 자동 생성된 권한 부여 URL을 할당하는 자격 증명을 입력합니다. | 예      |
| sessionid     | OAuth 권한 부여 세션에서 가져온 OAuth 세션 ID입니다. 각 세션 ID는 고유하고 한 번만 사용될 수 있습니다. 이 설정은 Data Factory 편집기를 사용하는 경우 자동으로 생성됩니다. | 예      |

**예제: 사용자 자격 증명 인증**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>토큰 만료
**권한 부여** 단추를 선택하여 생성한 권한 부여 코드는 설정된 간격 후에 만료됩니다. 

인증 토큰이 만료되면 다음 오류 메시지가 표시될 수도 있습니다. 

  자격 증명 작업 오류: invalid_grant - AADSTS70002: 자격 증명의 유효성 검사 오류. AADSTS70008: 제공된 액세스 권한 부여가 만료되었거나 해지됩니다. 추적 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 상관관계 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 타임스탬프: 2015-12-15 21:09:31Z

다음 테이블에서는 사용자 계정 유형별 만료 날짜를 보여줍니다. 

| 사용자 유형                                | 다음 시간 후에 만료                            |
| :--------------------------------------- | :--------------------------------------- |
| Azure AD(Hotmail, Live 등)에서 관리되지 *않는* 사용자 계정 | 12시간                                 |
| Azure AD에서 관리*되는* 사용자 계정 | 마지막 조각이 실행된 후 14일 <br /><br />OAuth 기반 연결된 서비스를 기반으로 하는 조각이 14일마다 한 번 이상 실행된 경우 90일 |

이 오류를 방지하거나 해결하려면 토큰이 만료될 때 **권한 부여** 단추를 사용하여 다시 인증합니다. 그런 다음, 연결된 서비스를 재배포합니다. 다음 코드를 사용하여 프로그래밍 방식으로 **sessionId** 및 **authorization** 속성의 값을 생성할 수도 있습니다.

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

이 코드 예제에 사용된 Data Factory 클래스에 대한 자세한 내용은 다음을 참조하세요.
* [AzureDataLakeStoreLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

**WindowsFormsWebAuthenticationDialog** 클래스의 Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll에 대한 참조를 추가합니다. 

## <a name="azure-sql-linked-service"></a>Azure SQL 연결된 서비스
SQL 연결된 서비스를 만들고 [저장 프로시저 활동](data-factory-stored-proc-activity.md)에 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다. 자세한 내용은 [Azure SQL 커넥터](data-factory-azure-sql-connector.md#linked-service-properties)를 참조하세요.

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL Data Warehouse 연결된 서비스
SQL Data Warehouse 연결된 서비스를 만들고 [저장 프로시저 활동](data-factory-stored-proc-activity.md)에 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출합니다. 자세한 내용은 [Azure SQL Data Warehouse 커넥터](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)를 참조하세요.

## <a name="sql-server-linked-service"></a>SQL Server 연결된 서비스
SQL Server 연결된 서비스를 만들고 [저장 프로시저 활동](data-factory-stored-proc-activity.md) 에서 사용하여 Data Factory 파이프라인에서 저장 프로시저를 호출할 수 있습니다. 자세한 내용은 [SQL Server 커넥터](data-factory-sqlserver-connector.md#linked-service-properties)를 참조하세요.

