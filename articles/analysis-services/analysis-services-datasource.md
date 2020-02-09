---
title: Azure Analysis Services에서 지원되는 데이터 원본 | Microsoft Docs
description: Azure Analysis Services에서 테이블 형식 1200 이상 데이터 모델에 대해 지원 되는 데이터 원본 및 커넥터에 대해 설명 합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4a99bfe8d8235400f9122423aa4592fc6898abc1
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922280"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services에서 지원되는 데이터 원본

Analysis Services 프로젝트를 사용 하 여 Visual Studio에서 데이터 가져오기 또는 테이블 가져오기 마법사에 표시 된 데이터 원본 및 커넥터는 Azure Analysis Services 및 SQL Server Analysis Services 모두에 대해 표시 됩니다. 그러나 표시 된 일부 데이터 원본 및 커넥터는 Azure Analysis Services에서 지원 되지 않습니다. 연결할 수 있는 데이터 원본의 유형은 모델 호환성 수준, 사용 가능한 데이터 커넥터, 인증 유형 및 온-프레미스 데이터 게이트웨이 지원과 같은 여러 요소에 따라 달라 집니다. 다음 표에서는 Azure Analysis Services에 대해 지원 되는 데이터 원본을 설명 합니다.

## <a name="azure-data-sources"></a>Azure 데이터 원본

|데이터 원본  |메모리 내  |DirectQuery  |메모 |
|---------|---------|---------|---------|
|Azure SQL Database      |   예      |    예      |<sup>[2](#azprovider)</sup>, <sup> [3](#azsqlmanaged)</sup>|
|Azure SQL Data Warehouse      |   예      |   예       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   예       |    아닙니다.      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   예       |    아닙니다.      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  예        |  아닙니다.        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   예       |    아닙니다.      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   예       |    아닙니다.      |<sup>[1](#tab1400a)</sup>, <sup> [5](#gen2)</sup>|
|Azure HDInsight HDFS    |     예     |   아닙니다.       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   예       |   아닙니다.       |<sup>[1](#tab1400a)</sup>, <sup> [4](#databricks)</sup>|
||||

**참고:**    
<a name="tab1400a">1</a> - 테이블 형식 1400 이상 모델에만 해당됩니다.  
<a name="azprovider">2</a> -테이블 형식 1200 이상 모델에서 *공급자* 데이터 원본으로 지정 하면 메모리 내 및 DirectQuery 모델 모두에 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (권장), SQL Server Native Client 11.0 또는 Data Provider에 대한 .NET Framework SQL Server 필요 합니다.    
<a name="azsqlmanaged">3</a> -Azure SQL Database Managed Instance 지원 됩니다. 관리 되는 인스턴스는 개인 IP 주소를 사용 하 여 Azure VNet 내에서 실행 되므로 인스턴스에서 공용 엔드포인트을 사용 하도록 설정 해야 합니다. 사용 하도록 설정 하지 않으면 [온-프레미스 데이터 게이트웨이가](analysis-services-gateway.md) 필요 합니다.    
<a name="databricks">4</a> -Spark 커넥터를 사용 하는 Azure Databricks 현재 지원 되지 않습니다.   
<a name="gen2">5</a> -ADLS Gen2 connector는 현재 지원 되지 않지만 Azure Blob Storage 커넥터를 ADLS Gen2 데이터 원본과 함께 사용할 수 있습니다.   

## <a name="other-data-sources"></a>기타 데이터 원본

|데이터 원본 | 메모리 내 | DirectQuery |메모   |
|  --- | --- | --- | --- |
|Access 데이터베이스     |  예 | 아닙니다. |  |
|Active Directory     |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  예 | 아닙니다. |  |
|분석 플랫폼 시스템     |  예 | 아닙니다. |  |
|CSV 파일  |예 | 아닙니다. |  |
|PowerApps     |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|Excel 통합 문서     |  예 | 아닙니다. |  |
|교환      |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|폴더      |예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |예 | 아닙니다. |  |
|JSON 문서      |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|이진의 줄      | 예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|MySQL Database     | 예 | 아닙니다. |  |
|OData 피드      |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|ODBC 쿼리     | 예 | 아닙니다. |  |
|OLE DB     |   예 | 아닙니다. |  |
|Oracle  | 예  |예  | <sup>[9](#oracle)</sup> |
|PostgreSQL 데이터베이스   | 예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|Salesforce 개체|  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|Salesforce 보고서 |예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  예 | 아닙니다. |  |
|SAP Business Warehouse    |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
|SharePoint 목록      |   예 | 아닙니다. | <sup>[6](#tab1400b)</sup>, <sup> [11](#filesSP)</sup> |
|SQL Server |예   | 예  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> | 
|SQL Server 데이터 웨어하우스 |예   | 예  | <sup>[7](#sqlim)</sup>, <sup> [8](#instgw)</sup> |
|Sybase 데이터베이스     |  예 | 아닙니다. |  |
|Teradata | 예  | 예  | <sup>[10](#teradata)</sup> |
|TXT 파일  |예 | 아닙니다. |  |
|XML 테이블    |  예 | 아닙니다. | <sup>[6](#tab1400b)</sup> |
| | | |

**참고:**    
<a name="tab1400b">6</a> -테이블 형식 1400 이상 모델에만 해당 합니다.  
<a name="sqlim">7</a> -테이블 형식 1200 이상 모델에서 *공급자* 데이터 원본으로 지정 하는 경우 Data Provider에 대해 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL (권장), SQL Server Native Client 11.0 또는 .NET Framework SQL Server를 지정 합니다.  
<a name="instgw">8</a> -MSOLEDBSQL를 데이터 공급자로 지정 하는 경우 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에 [SQL Server 용 Microsoft OLE DB 드라이버](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server) 를 다운로드 하 여 설치 해야 할 수 있습니다.  
<a name="oracle">9</a> -테이블 형식 1200 모델의 경우 또는 테이블 형식 1400 + 모델의 *공급자* 데이터 원본으로 .net의 Oracle Data Provider를 지정 합니다.  
<a name="teradata">10</a> -테이블 형식 1200 모델의 경우 또는 테이블 형식 1400 + 모델의 *공급자* 데이터 원본으로 .net의 Teradata Data Provider를 지정 합니다.   
<a name="filesSP">11</a> -온-프레미스 SharePoint의 파일은 지원 되지 않습니다.

Azure Analysis Services 서버에서 온-프레미스 데이터 원본에 연결 하려면 [온-프레미스 게이트웨이가](analysis-services-gateway.md)필요 합니다. 게이트웨이를 사용하는 경우 64비트 공급자는 필수입니다. 

## <a name="understanding-providers"></a>공급자 이해

Visual Studio에서 테이블 형식 1400 이상 모델 프로젝트를 만들 때 기본적으로 데이터 **가져오기**를 사용 하 여 데이터 원본에 연결할 때 데이터 공급자를 지정 하지 않습니다. 테이블 형식 1400 이상 모델에서는 [파워 쿼리](/power-query/power-query-what-is-power-query) 커넥터를 사용 하 여 데이터 원본 및 Analysis Services 간의 연결, 데이터 쿼리 및 매시업을 관리 합니다. 이러한 연결 속성 설정이 사용자를 위해 설정 되는 경우 이러한 연결을 *구조화* 된 데이터 원본 연결이 라고도 합니다. 그러나 레거시 데이터 원본을 사용 하도록 설정할 수 있습니다. 이 기능을 사용 하는 경우 **테이블 가져오기 마법사** 를 사용 하 여 기존에는 테이블 형식 1200에서 지원 되는 특정 데이터 원본에 연결 하거나 *기존*모델 또는 *공급자* 데이터 원본으로 사용할 수 있습니다. 공급자 데이터 원본으로 지정 하는 경우 특정 데이터 공급자 및 기타 고급 연결 속성을 지정할 수 있습니다. 예를 들어 온-프레미스 SQL Server 데이터 웨어하우스 또는 레거시 데이터 원본으로 Azure SQL Database에 연결할 수 있습니다. 그런 다음 SQL Server MSOLEDBSQL 데이터 공급자에 대한 OLE DB 드라이버를 선택할 수 있습니다. 이 경우 OLE DB 데이터 공급자를 선택 하면 파워 쿼리 커넥터 보다 향상 된 성능을 제공할 수 있습니다. 

Visual Studio에서 테이블 가져오기 마법사를 사용 하는 경우 데이터 원본에 연결 하려면 데이터 공급자가 필요 합니다. 기본 데이터 공급자가 선택 됩니다. 필요한 경우 데이터 공급자를 변경할 수 있습니다. 모델에서 메모리 내 저장소 또는 DirectQuery를 사용 하 고 있는지 여부와 모델을 배포 하는 Analysis Services 플랫폼을 사용 하 여 성능에 따라 선택할 수 있습니다.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>테이블 형식 1400 이상 모델 프로젝트에서 공급자 데이터 원본 지정

공급자 데이터 소스를 사용 하려면 Visual Studio에서 **도구** > **옵션** > **Analysis Services 테이블 형식** > **데이터 가져오기**를 클릭 하 고 **레거시 데이터 원본 사용**을 선택 합니다.

![레거시 데이터 원본 사용](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

레거시 데이터 원본을 사용 하는 경우 **테이블 형식 모델 탐색기**에서 **데이터** 원본을 마우스 오른쪽 단추로 클릭 하 > 데이터 **원본에서 가져오기 (레거시)** 를 클릭 합니다.

![테이블 형식 모델 탐색기의 레거시 데이터 원본](media/analysis-services-datasource/aas-import-legacy-datasources.png)

테이블 형식 1200 모델 프로젝트와 마찬가지로 **테이블 가져오기 마법사** 를 사용 하 여 데이터 원본에 연결 합니다. 연결 페이지에서 **고급**을 클릭 합니다. **고급 속성 설정**에서 데이터 공급자 및 기타 연결 설정을 지정 합니다.

![레거시 데이터 원본 고급 속성](media/analysis-services-datasource/aas-import-legacy-advanced.png)


## <a name="impersonation"></a>가장
경우에 따라 다른 가장 계정을 지정해야 할 수도 있습니다. Visual Studio 또는 SSMS에서 가장 계정을 지정할 수 있습니다.

온-프레미스 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.
* Windows 인증을 사용하는 경우 Windows 사용자/암호를 설정합니다. SQL Server의 경우 특정 가장 계정을 사용한 Windows 인증은 메모리 내 데이터 모델에 대해서만 지원됩니다.

클라우드 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.

## <a name="oauth-credentials"></a>OAuth 자격 증명

메모리 내 모드를 사용 하는 1400 이상 호환성 수준에서 테이블 형식 모델의 경우 Azure SQL Database, Azure SQL Data Warehouse, Dynamics 365 및 SharePoint 목록에서 OAuth 자격 증명을 지원 합니다. Azure Analysis Services는 장기 실행 새로 고침 작업에 대한 시간 제한을 방지 하기 위해 OAuth 데이터 원본에 대한 토큰 새로 고침을 관리 합니다. 유효한 토큰을 생성 하려면 SSMS를 사용 하 여 자격 증명을 설정 합니다.

직접 쿼리 모드는 OAuth 자격 증명을 사용 하 여 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계
[온-프레미스 게이트웨이](analysis-services-gateway.md)   
[서버 관리](analysis-services-manage.md)   

