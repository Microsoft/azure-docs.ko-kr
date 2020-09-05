---
title: Azure Analysis Services에서 지원되는 데이터 원본 | Microsoft Docs
description: Azure Analysis Services에서 테이블 형식 1200 이상 데이터 모델에 대해 지원되는 데이터 원본 및 커넥터에 대해 설명합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b99ac957c9c5030b484b244223847be4aa53a39d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749094"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Azure Analysis Services에서 지원되는 데이터 원본

Analysis Services 프로젝트를 통해 Visual Studio에서 데이터 가져오기 또는 테이블 가져오기 마법사에 표시된 데이터 원본 및 커넥터는 Azure Analysis Services 및 SQL Server Analysis Services에 표시됩니다. 그러나 표시된 일부 데이터 원본 및 커넥터는 Azure Analysis Services에서 지원됩니다. 연결할 수 있는 데이터 원본의 형식은 모델 호환성 수준, 사용 가능한 데이터 커넥터, 인증 형식 및 온-프레미스 데이터 게이트웨이 지원과 같은 많은 요인에 따라 달라집니다. 다음 표에서는 Azure Analysis Services에 지원되는 데이터 원본에 대해 설명합니다.

## <a name="azure-data-sources"></a>Azure 데이터 원본

|데이터 원본  |메모리 내  |DirectQuery  |메모 |
|---------|---------|---------|---------|
|Azure SQL Database      |   예      |    예      |<sup>[2](#azprovider)</sup>, <sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics(SQL DW)      |   예      |   예       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   예       |    예      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   예       |    예      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  예        |  예        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   예       |    예      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   예       |    예      |<sup>[1](#tab1400a)</sup>, <sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     예     |   예       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   예       |   예       |<sup>[1](#tab1400a)</sup>, <sup>[4](#databricks)</sup>|
||||

**참고:**

<a name="tab1400a">1</a> - 테이블 형식 1400 이상 모델에만 해당됩니다.  
<a name="azprovider">2</a> - 테이블 형식 1200 이상 모델에서 *공급자* 데이터 원본으로 지정되면 메모리 내 및 DirectQuery 모델 모두 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL(권장), SQL Server Native Client 11.0 또는 .NET Framework Data Provider for SQL Server가 필요합니다.  
<a name="azsqlmanaged">3</a> -Azure SQL Managed Instance 지원 됩니다. SQL Managed Instance 개인 IP 주소를 사용 하 여 Azure VNet 내에서 실행 되기 때문에 인스턴스에 대해 공용 끝점을 사용 하도록 설정 해야 합니다. 사용하도록 설정하지 않으면 [온-프레미스 데이터 게이트웨이](analysis-services-gateway.md)가 필요합니다.  
<a name="databricks">4</a> - Spark 커넥터를 사용하는 Azure Databricks는 현재 지원되지 않습니다.  
<a name="gen2">5</a> - ADLS Gen2 커넥터는 현재 지원되지 않지만 Azure Blob Storage 커넥터는 ADLS Gen2 데이터 원본에서 사용할 수 있습니다.

## <a name="other-data-sources"></a>다른 데이터 원본

|데이터 원본 | 메모리 내 | DirectQuery |메모   |
|  --- | --- | --- | --- |
|Access 데이터베이스     |  예 | 예 |  |
|Active Directory     |  예 | 예 | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  예 | 예 |  |
|분석 플랫폼 시스템     |  예 | 예 |  |
|CSV 파일  |예 | 예 |  |
|Dynamics 365     |  예 | 예 | <sup>[6](#tab1400b)</sup> |
|Excel 통합 문서     |  예 | 예 |  |
|Exchange      |  예 | 예 | <sup>[6](#tab1400b)</sup> |
|폴더      |예 | 예 | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |예 | 예 |  |
|JSON 문서      |  예 | 예 | <sup>[6](#tab1400b)</sup> |
|이진의 줄      | 예 | 예 | <sup>[6](#tab1400b)</sup> |
|MySQL 데이터베이스     | 예 | 예 |  |
|OData 피드      |  예 | 예 | <sup>[6](#tab1400b)</sup> |
|ODBC 쿼리     | 예 | 예 |  |
|OLE DB     |   예 | 예 |  |
|Oracle  | 예  |예  | <sup>[9](#oracle)</sup> |
|PostgreSQL 데이터베이스   | 예 | 예 | <sup>[6](#tab1400b)</sup> |
|Salesforce 개체|  예 | 예 | <sup>[6](#tab1400b)</sup> |
|Salesforce 보고서 |예 | 예 | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  예 | 예 |  |
|SAP Business Warehouse    |  예 | 예 | <sup>[6](#tab1400b)</sup> |
|SharePoint 목록      |   예 | 예 | <sup>[6](#tab1400b)</sup>, <sup>[11](#filesSP)</sup> |
|SQL Server |예   | 예  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|SQL Server 데이터 웨어하우스 |예   | 예  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|Sybase 데이터베이스     |  예 | 예 |  |
|Teradata | 예  | 예  | <sup>[10](#teradata)</sup> |
|TXT 파일  |예 | 예 |  |
|XML 테이블    |  예 | 예 | <sup>[6](#tab1400b)</sup> |
| | | |

**참고:**  
<a name="tab1400b">6</a> - 테이블 형식 1400 이상 모델에만 해당됩니다.  
<a name="sqlim">7</a> - 테이블 형식 1200 이상 모델에서 *공급자* 데이터 원본으로 지정하는 경우 Microsoft OLE DB Driver for SQL Server MSOLEDBSQL(권장), SQL Server Native Client 11.0 또는 .NET Framework Data Provider for SQL Server를 지정합니다.  
<a name="instgw">8</a> - MSOLEDBSQL을 데이터 공급자로 지정하는 경우 온-프레미스 데이터 게이트웨이와 동일한 컴퓨터에 [Microsoft OLE DB Driver for SQL Server](https://docs.microsoft.com/sql/connect/oledb/oledb-driver-for-sql-server)를 다운로드하여 설치해야 할 수 있습니다.  
<a name="oracle">9</a> - 테이블 형식 1200 모델의 경우 또는 테이블 형식 1400 이상 모델의 *공급자* 데이터 원본으로 Oracle Data Provider for .NET을 지정합니다. 구조적 데이터 원본으로 지정 하는 경우에 [는 Oracle 관리 공급자를 사용 하도록 설정](#enable-oracle-managed-provider)해야 합니다.   
<a name="teradata">10</a> - 테이블 형식 1200 모델의 경우 또는 테이블 형식 1400 이상 모델의 *공급자* 데이터 원본으로 Teradata Data Provider for .NET을 지정합니다.  
<a name="filesSP">11</a> - 온-프레미스 SharePoint의 파일은 지원되지 않습니다.

온-프레미스 데이터 원본 및 Azure Analysis Services 서버에 연결하려면 [온-프레미스 게이트웨이](analysis-services-gateway.md)가 필요합니다. 게이트웨이를 사용하는 경우 64비트 공급자는 필수입니다.

## <a name="understanding-providers"></a>공급자 이해

Visual Studio에서 테이블 형식 1400 이상 모델 프로젝트를 만들 때 기본적으로 **데이터 가져오기**를 사용하여 데이터 원본에 연결할 경우 데이터 공급자를 지정하지 않습니다. 테이블 형식 1400 이상 모델에서는 [파워 쿼리](/power-query/power-query-what-is-power-query) 커넥터를 사용하여 데이터 원본 및 Analysis Services 간의 연결, 데이터 쿼리 및 매시업을 관리합니다. 이와 같은 경우에는 연결 속성 설정이 사용자에게 맞게 설정되어 *구조적* 데이터 원본 연결이라고도 합니다. 그러나 Visual Studio에서 모델 프로젝트에 대한 레거시 데이터 원본을 사용하도록 설정할 수 있습니다. 설정된 경우 **테이블 가져오기 마법사**를 사용하여 테이블 형식 1200 이하 모델에서 *레거시* 또는 *공급자* 데이터 원본으로 일반적으로 지원되는 특정 데이터 원본에 연결할 수 있습니다. 공급자 데이터 원본으로 지정하는 경우 특정 데이터 공급자 및 기타 고급 연결 속성을 지정할 수 있습니다. 예를 들어 기존 데이터 원본으로 SQL Server 데이터 웨어하우스 인스턴스 또는 Azure SQL Database에 연결할 수 있습니다. 그런 다음 OLE DB Driver for SQL Server MSOLEDBSQL 데이터 공급자를 선택할 수 있습니다. 이 경우 OLE DB 데이터 공급자를 선택하면 파워 쿼리 커넥터보다 향상된 성능을 제공할 수 있습니다. 

Visual Studio에서 테이블 가져오기 마법사를 사용하는 경우 데이터 원본에 연결하려면 데이터 공급자가 필요합니다. 기본 데이터 공급자가 선택됩니다. 필요한 경우 데이터 공급자를 변경할 수 있습니다. 선택한 공급자 유형에 따라 성능, 모델의 메모리 내 스토리지 또는 DirectQuery에서 사용 여부 및 모델을 배포하는 Analysis Services 플랫폼이 달라질 수 있습니다.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>테이블 형식 1400 이상 모델 프로젝트에서 공급자 데이터 원본 지정

공급자 데이터 원본을 사용하도록 설정하려면 Visual Studio에서 **도구** > **옵션** > **Analysis Services 테이블 형식** > **데이터 가져오기**를 클릭하고 **레거시 데이터 원본 사용**을 선택합니다.

![레거시 데이터 원본 사용](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

레거시 데이터 원본을 사용하도록 설정하는 경우 **테이블 형식 모델 탐색기**에서 **데이터 원본** > **데이터 원본(레거시)에서 가져오기**를 마우스 오른쪽 단추로 클릭합니다.

![테이블 형식 모델 탐색기의 레거시 데이터 원본](media/analysis-services-datasource/aas-import-legacy-datasources.png)

테이블 형식 1200 모델 프로젝트와 마찬가지로 **테이블 가져오기 마법사**를 사용하여 데이터 원본에 연결합니다. 연결 페이지에서 **고급**을 클릭합니다. **고급 속성 설정**에서 데이터 공급자 및 기타 연결 설정을 지정합니다.

![레거시 데이터 원본 고급 속성](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>가장
경우에 따라 다른 가장 계정을 지정해야 할 수도 있습니다. Visual Studio 또는 SSMS(SQL Server Management Studio)에서 가장 계정을 지정할 수 있습니다.

온-프레미스 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.
* Windows 인증을 사용하는 경우 Windows 사용자/암호를 설정합니다. SQL Server의 경우 특정 가장 계정을 사용한 Windows 인증은 메모리 내 데이터 모델에 대해서만 지원됩니다.

클라우드 데이터 원본의 경우:

* SQL 인증을 사용하는 경우 가장은 서비스 계정이어야 합니다.

## <a name="oauth-credentials"></a>OAuth 자격 증명

메모리 내 모드를 사용 하는 1400 이상 호환성 수준에서 테이블 형식 모델의 경우 Azure SQL Database, Azure Synapse (이전의 SQL Data Warehouse), Dynamics 365 및 SharePoint 목록은 OAuth 자격 증명을 지원 합니다. Azure Analysis Services는 장기 실행 새로 고침 작업에 대한 시간 제한을 방지하기 위해 OAuth 데이터 원본에 대한 토큰 새로 고침을 관리합니다. 유효한 토큰을 생성 하려면 파워 쿼리를 사용 하 여 자격 증명을 설정 합니다.

직접 쿼리 모드는 OAuth 자격 증명에서 지원되지 않습니다.

## <a name="enable-oracle-managed-provider"></a>Oracle 관리 공급자 사용

Oracle 데이터 원본에 대 한 DAX 쿼리가 예기치 않은 결과를 반환할 수 있는 경우도 있습니다. 이는 데이터 원본 연결에 사용 되는 공급자 때문에 발생할 수 있습니다.

[공급자 이해](#understanding-providers) 섹션에 설명 된 대로 테이블 형식 모델은 데이터 원본에 *구조화* 된 데이터 원본 또는 *공급자* 데이터 원본으로 연결 합니다. Oracle 데이터 원본이 공급자 데이터 원본으로 지정 된 모델의 경우 지정 된 공급자가 .NET의 Oracle Data Provider (DataAccess) 인지 확인 합니다. 

Oracle 데이터 원본이 구조적 데이터 원본으로 지정 된 경우 **MDataEngine\UseManagedOracleProvider** 서버 속성을 사용 하도록 설정 합니다. 이 속성을 설정 하면 .NET 관리 공급자에 권장 되는 Oracle Data Provider를 사용 하 여 모델이 Oracle 데이터 원본에 연결 됩니다.
 
Oracle 관리 공급자를 사용 하도록 설정 하려면

1. SQL Server Management Studio에서 서버에 연결 합니다.
2. 다음 스크립트를 사용 하 여 XMLA 쿼리를 만듭니다. **ServerName** 을 전체 서버 이름으로 바꾸고 쿼리를 실행 합니다.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. 서버를 다시 시작합니다.


## <a name="next-steps"></a>다음 단계

* [온-프레미스 게이트웨이](analysis-services-gateway.md)
* [서버 관리](analysis-services-manage.md)
