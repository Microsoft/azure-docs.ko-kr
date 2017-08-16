---
title: "데이터 팩터리 개요, 데이터 통합 서비스 | Microsoft Docs"
description: "Azure Data Factory가 무엇인지 알아봅니다. 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 데이터 통합 서비스입니다."
keywords: "데이터 통합, 클라우드 데이터 통합, Azure 데이터 팩터리란"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: cec68cb5-ca0d-473b-8ae8-35de949a009e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/21/2017
ms.author: shlo
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: 537bdee67ed9648c3cba2099553d847399609705
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# Azure Data Factory 소개
<a id="introduction-to-azure-data-factory" class="xliff"></a> 
## Azure 데이터 팩터리란 무엇인가요?
<a id="what-is-azure-data-factory" class="xliff"></a>
빅 데이터의 세계에서 기존 데이터는 어떻게 비즈니스에 활용될까요? 온-프레미스 데이터 소스 또는 기타 다양한 데이터 소스의 참조 데이터를 사용하여 클라우드에서 생성된 데이터를 보강할 수 있을까요? 예를 들어 게임 회사는 클라우드에서 게임에 의해 생성되는 많은 로그를 수집합니다. 이러한 로그를 분석하여 고객 선호도, 인구 통계, 사용 동작 등에 대한 정보를 얻고, 업셀 및 교차 판매 기회를 포착하고, 탁월한 새 기능을 개발하여 비즈니스 성장을 견인하고, 고객에게 더 나은 환경을 제공하려고 합니다. 

이러한 로그를 분석하려면 회사는 온-프레미스 데이터 저장소에 있는 고객 정보, 게임 정보, 마케팅 캠페인 정보와 같은 참조 데이터를 사용해야 합니다. 따라서 회사는 클라우드 데이터 저장소에서 로그 데이터를 수집하고 온-프레미스 데이터 저장소에서 데이터를 참조하려고 합니다. 그런 다음 클라우드(Azure HDInsight)에서 Hadoop을 사용하여 데이터를 처리하고 Azure SQL Data Warehouse와 같은 클라우드 데이터 웨어하우스 또는 SQL Server와 같은 온-프레미스 데이터 저장소에 결과 데이터를 게시합니다. 이 워크플로를 매주 한 번 실행하려고 합니다. 

회사가 온-프레미스 및 클라우드 데이터 저장소에서 데이터를 수집할 수 있는 워크플로를 만들고, Hadoop 등의 기존 계산 서비스를 사용하여 데이터를 변환하거나 처리하고, 사용할 BI 응용 프로그램의 온-프레미스 및 클라우드 데이터 저장소에 결과를 게시할 수 있는 플랫폼이 필요합니다. 

![데이터 팩터리 개요](media/data-factory-introduction/what-is-azure-data-factory.png) 

Azure Data Factory는 이러한 종류의 시나리오에 대한 플랫폼입니다. **데이터 이동 및 데이터 변환을 오케스트레이션하고 자동화하기 위해 클라우드에서 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 데이터 통합 서비스**입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소에서 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 일정을 조정하며, Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics 및 Azure Machine Learning과 같은 계산 서비스를 사용하여 데이터를 처리/변환하고, 사용할 BI(비즈니스 인텔리전스) 응용 프로그램의 Azure SQL Data Warehouse와 같은 데이터 저장소에 출력 데이터를 게시할 수 있습니다.  

기존의 ETL(추출 및 변환 및 로드) 플랫폼이 아닌 EL(추출 및 로드)한 다음 TL(변환 및 로드) 플랫폼이 지지를 얻고 있습니다. 파생된 열 추가, 행의 수 계산, 데이터 정렬 등과 같은 변환을 수행하기 보다는 계산 서비스를 사용하여 데이터를 변환/처리하는 변환을 수행합니다. 

현재 Azure Data Factory에서 사용되고 워크플로에 의해 생성된 데이터는 **시간 조각화 데이터**(매시간, 매일, 매주 등)입니다. 예를 들어 파이프라인은 하루에 한 번 입력 데이터를 읽고, 데이터를 처리하고 출력 데이터를 생성합니다. 또한 워크플로를 한 번만 실행할 수 있습니다.  
  

## 작동 원리
<a id="how-does-it-work" class="xliff"></a> 
Azure Data Factory의 파이프라인(데이터 기반 워크플로)는 일반적으로 다음 세 단계를 수행합니다.

![Azure Data Factory의 세 단계](media/data-factory-introduction/three-information-production-stages.png)

### 연결 및 수집
<a id="connect-and-collect" class="xliff"></a>
기업에는 다양한 형식의 데이터가 서로 다른 원본에 있습니다. 따라서 정보 생산 시스템을 구축하는 첫 번째 단계에서는 필요한 모든 데이터 원본과 SaaS 서비스, 파일 공유, FTP, 웹 서비스 등과 같은 처리에 연결하고 후속 처리에 필요한 데이터를 중앙의 위치로 이동합니다.

데이터 팩터리가 없으면 기업은 사용자 지정 데이터 이동 구성 요소를 구축하거나 사용자 지정 서비스를 작성하여 이러한 데이터 원본과 처리를 통합해야 합니다. 이 경우 비용이 많이 들고 이러한 서비스를 통합 및 관리하기 어려우며, 엔터프라이즈급 모니터링 및 경고 기능과 완전히 관리되는 서비스에서 제공할 수 있는 제어 기능이 부족한 경우가 많습니다.

Data Factory가 있으면 향후 분석에 사용할 수 있도록 데이터 파이프라인에서 복사 작업을 사용하여 온-프레미스 및 클라우드 원본 데이터 저장소에서 클라우드의 중앙 데이터 저장소로 데이터를 이동할 수 있습니다. 예를 들어 Azure Data Lake Store에서 데이터를 수집하고 나중에 Azure Data Lake Analytics 계산 서비스를 사용하여 데이터를 변환할 수 있습니다. 또는 Azure HDInsight Hadoop 클러스터를 사용하여 Azure Blob Storage에서 데이터를 수집하고 나중에 변환할 수 있습니다.

### 변환 및 보강
<a id="transform-and-enrich" class="xliff"></a>
데이터가 클라우드에서 중앙 집중화된 데이터 저장소에 표시되면 HDInsight Hadoop, Spark, Data Lake Analytics 및 Machine Learning과 같은 계산 서비스를 사용하여 수집된 데이터를 변환하려고 합니다. 이제 유지 관리할 수 있고 제어된 일정에 따라 변환된 데이터를 안정적으로 생성하여 프로덕션 환경에 신뢰할 수 있는 데이터를 공급하려고 합니다. 

### 게시
<a id="publish" class="xliff"></a> 
클라우드에서 SQL Server와 같은 온-프레미스 원본으로 변환된 데이터를 제공하거나, BI(비즈니스 인텔리전스) 및 분석 도구와 기타 응용 프로그램에서 사용하도록 클라우드 저장소 원본에 유지합니다.

## 핵심 구성 요소
<a id="key-components" class="xliff"></a>
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 데이터 팩터리)가 있을 수 있습니다. Azure Data Factory는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼을 제공하는 네 가지 핵심 구성 요소로 구성됩니다. 

### 파이프라인
<a id="pipeline" class="xliff"></a>
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 활동 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어 Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 이 방식의 장점은 파이프라인을 통해 개별 활동 단위가 아닌 하나의 집합으로써 활동을 관리할 수 있다는 점입니다. 예를 들어 각 활동을 개별적으로 배포하고 예약하는 대신 파이프라인을 배포하고 예약할 수 있습니다. 

### 작업
<a id="activity" class="xliff"></a>
파이프라인에는 하나 이상의 작업이 있을 수 있습니다. 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 복사 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 활동 및 데이터 변환 활동이라는 두 종류의 활동을 지원합니다.

### 데이터 이동 활동
<a id="data-movement-activities" class="xliff"></a>
데이터 팩터리의 복사 활동은 원본 데이터 저장소의 데이터를 싱크 데이터 저장소로 복사합니다. Data Factory는 다음과 같은 데이터 저장소를 지원합니다. 모든 소스의 데이터를 모든 싱크에 쓸 수 있습니다. 데이터 저장소를 클릭하면 해당 저장소에서/저장소로 데이터를 복사하는 방법을 확인할 수 있습니다.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

자세한 내용은 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 참조하세요.

### 데이터 변환 활동
<a id="data-transformation-activities" class="xliff"></a>
[!INCLUDE [data-factory-transformation-activities](../../includes/data-factory-transformation-activities.md)]

자세한 내용은 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서를 참조하세요.

### 사용자 지정 .NET 활동
<a id="custom-net-activities" class="xliff"></a>
복사 작업에서 지원하지 않는 데이터 저장소에서/로 데이터를 이동해야 하거나 사용자 고유한 논리를 사용하여 데이터를 변환해야 하는 경우 **사용자 지정 .NET 활동**을 만듭니다. 사용자 지정 활동을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 활동 사용](data-factory-use-custom-activities.md)을 참조하세요.

### 데이터 집합
<a id="datasets" class="xliff"></a>
작업은 0개 이상의 데이터 집합을 입력으로 사용하고 하나 이상의 데이터 집합을 출력으로 사용합니다. 데이터 집합은 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 예를 들어 Azure Blob 데이터 집합은 파이프라인이 데이터를 읽어야 하는 Azure Blob Storage에서 Blob 컨테이너 및 폴더를 지정합니다. 또는 Azure SQL 테이블 데이터 집합은 작업에 의해 작성되는 출력 데이터에 대한 테이블을 지정합니다. 

### 연결된 서비스
<a id="linked-services" class="xliff"></a>
연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 연결된 서비스는 데이터 원본에 연결을 정의하고 데이터 집합은 데이터 구조를 나타낸다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. 그리고 Azure Blob 데이터 집합은 Blob 컨테이너 및 데이터가 포함된 폴더를 지정합니다.   

연결된 서비스는 데이터 팩터리 내에서 두 가지 용도로 사용됩니다.

* 온-프레미스 SQL Server, Oracle 데이터베이스, 파일 공유 또는 Azure Blob Storage 계정을 포함하지만 여기에 국한되지 않는 **데이터 저장소** 를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록은 [데이터 이동 활동](#data-movement-activities) 섹션을 참조하세요.
* 활동의 실행을 호스팅할 수 있는 **계산 리소스** 를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 지원되는 컴퓨팅 환경 목록은 [데이터 변환 활동](#data-transformation-activities) 섹션을 참조하세요.

### Data Factory 엔터티 간의 관계
<a id="relationship-between-data-factory-entities" class="xliff"></a>
![다이어그램: Data Factory, 클라우드 데이터 통합 서비스 - 주요 개념](./media/data-factory-introduction/data-integration-service-key-concepts.png)

**그림 2.** 데이터 집합, 활동, 파이프라인 및 연결된 서비스 간의 관계

## 지원되는 지역
<a id="supported-regions" class="xliff"></a>
현재 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서 데이터 팩터리를 만들 수 있습니다. 그러나 데이터 팩터리는 계산 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 계산 서비스에 액세스할 수 있습니다.

Azure 데이터 팩터리 자체는 데이터를 저장하지 않습니다. 데이터 기반 워크플로를 만들어서 [지원되는 데이터 저장소](#data-movement-activities) 간의 데이터 이동을 조정하고 다른 지역 또는 온-프레미스 환경에서 [계산 서비스](#data-transformation-activities)를 사용하여 데이터의 처리를 조정할 수 있습니다. 또한 프로그래밍 방식 및 UI 메커니즘을 모두 사용하여 [워크플로를 모니터링하고 관리](data-factory-monitor-manage-pipelines.md) 할 수 있습니다.

Data Factory는 **미국 서부**, **미국 동부** 및 **북유럽** 지역에서만 사용할 수 있지만, 여러 지역에서 데이터 팩터리의 데이터 이동을 지원하는 서비스를 [전역적으로](data-factory-data-movement-activities.md#global) 사용할 수 있습니다. 데이터 저장소가 방화벽 뒤에 있는 경우 온-프레미스 환경에 설치된 [데이터 관리 게이트웨이](data-factory-move-data-between-onprem-and-cloud.md)가 대신 데이터를 이동시킵니다.

예를 들어 Azure HDInsight 클러스터 및 Azure Machine Learning과 같은 계산 환경이 서유럽 지역 외부에서 실행되고 있다고 가정해보겠습니다. 북유럽에서 Azure Data Factory 인스턴스를 만들고 사용할 수 있으며 이를 사용하여 서유럽의 계산 환경에 작업을 예약할 수 있습니다. 데이터 팩터리가 계산 환경에 작업을 트리거하는 데는 몇 밀리초가 걸리지만 사용자의 계산 환경에서 작업을 실행하는 데 걸리는 시간은 변경되지 않습니다.

## 파이프라인 만들기 시작
<a id="get-started-with-creating-a-pipeline" class="xliff"></a>
이러한 도구 또는 API 중 하나를 사용하여 Azure Data Factory에서 데이터 파이프라인을 만들 수 있습니다. 

- Azure Portal
- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager 템플릿 

데이터 파이프라인을 사용하여 데이터 팩터리를 빌드하는 방법을 알아보려면 다음 자습서의 단계별 지침을 따르세요.

| 자습서 | 설명 |
| --- | --- |
| [두 클라우드 데이터 저장소 간의 데이터 이동](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |이 자습서에서는 파이프라인을 포함한 데이터 팩터리를 만들어서 Blob Storage에서 SQL Database로 **데이터를 이동** 합니다. |
| [Hadoop 클러스터를 사용하여 데이터 변환](data-factory-build-your-first-pipeline.md) |이 자습서에서는 Azure HDInsight(Hadoop) 클러스터에서 Hive 스크립트를 실행하여 **데이터를 처리** 하는 데이터 파이프라인으로 첫 번째 Azure 데이터 팩터리를 구축합니다. |
| [데이터 관리 게이트웨이를 사용하여 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) |이 자습서에서는 **온-프레미스** SQL Server 데이터베이스에서 Azure Blob으로 **데이터를 이동**하는 파이프라인이 포함된 데이터 팩터리를 구축합니다. 자습서의 일부로 컴퓨터에 데이터 관리 게이트웨이를 설치하고 구성합니다. |

