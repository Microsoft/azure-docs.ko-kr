---
title: 서버리스 SQL 풀
description: Azure Synapse Analytics의 서버리스 SQL 풀에 대해 알아봅니다.
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a09dfb2b8e29ba925a702c169a1a79c13016d5dc
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421709"
---
# <a name="serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 서버리스 SQL 풀 

모든 Azure Synapse Analytics 작업 영역에는 [Azure Data Lake](query-data-storage.md)([Parquet](query-data-storage.md#query-parquet-files), [Delta Lake](query-delta-lake-format.md), [구분된 텍스트](query-data-storage.md#query-csv-files) 형식), [Cosmos DB](query-cosmos-db-analytical-store.md?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=openrowset-key) 또는 Dataverse에서 데이터를 쿼리하는 데 사용할 수 있는 서버리스 SQL 풀 엔드포인트가 제공됩니다.

서버리스 SQL 풀은 데이터 레이크의 데이터에 대한 쿼리 서비스입니다. 다음 기능을 통해 데이터에 액세스할 수 있도록 합니다.
 
- 데이터를 특수화된 저장소에 복사하거나 로드할 필요 없이 데이터를 적절히 쿼리할 수 있는 친숙한 [T-SQL 구문](overview-features.md)입니다. 
- 가장 인기 있는 드라이버를 포함하여 광범위한 비즈니스 인텔리전스 및 임시 쿼리 도구를 제공하는 T-SQL 인터페이스를 통한 통합 연결. 

서버리스 SQL 풀은 대규모 데이터 및 계산 기능을 위해 빌드된 분산 데이터 처리 시스템입니다. 서버리스 SQL 풀을 사용하면 워크로드에 따라 몇 초에서 몇 분 내에 빅 데이터를 분석할 수 있습니다. 기본 제공 쿼리 실행 내결함성을 통해 시스템에서 큰 데이터 세트와 관련된 장기 실행 쿼리에도 높은 안정성과 성공률을 제공합니다.

서버리스 SQL 풀은 서버리스이므로 설정할 인프라 또는 유지 관리할 클러스터가 없습니다. 이 서비스에 대한 기본 엔드포인트가 모든 Azure Synapse 작업 영역 내에 제공되므로 작업 영역이 만들어지는 즉시 데이터 쿼리를 시작할 수 있습니다. 

예약된 리소스에 대해서는 요금이 부과되지 않으며, 실행하는 쿼리에 의해 처리된 데이터에 대해서만 요금이 부과되므로 이 모델은 진정한 종량제 모델입니다.  

데이터 파이프라인에서 데이터 준비, 정리 또는 보강을 위해 Apache Spark for Azure Synapse를 사용하는 경우 프로세스에서 만든 [외부 Spark 테이블](develop-storage-files-spark-tables.md)을 서버리스 SQL 풀에서 직접 쿼리할 수 있습니다. [Private Link](../security/how-to-connect-to-workspace-with-private-links.md)를 사용하여 서버리스 SQL 풀 엔드포인트를 [관리형 작업 영역 VNet](../security/synapse-workspace-managed-vnet.md)으로 가져옵니다.  

## <a name="serverless-sql-pool-benefits"></a>서버리스 SQL 풀 혜택

데이터 레이크의 데이터를 검색하거나 데이터 레이크에서 인사이트를 얻거나 기존 데이터 변환 파이프라인을 최적화해야 하는 경우 서버리스 SQL 풀을 사용하여 이점을 얻을 수 있습니다. 다음 시나리오에 적합합니다.

- 기본 검색 및 탐색 - 데이터 레이크에서 다양한 형식(Parquet, CSV, JSON)의 데이터를 빠르게 추론하여 데이터 레이크에서 인사이트를 추출하는 방법을 계획할 수 있습니다.
- 논리적 데이터 웨어하우스 – 데이터를 재배치 및 변환하지 않고도 원시 데이터 또는 서로 다른 데이터를 기반으로 하여 관계형 추상화를 제공하여 항상 데이터의 최신 보기를 사용할 수 있습니다. [논리 데이터 웨어하우스를 만드는 방법](tutorial-logical-data-warehouse.md)에 관해 자세히 알아봅니다.
- 데이터 변환 - T-SQL을 사용하여 레이크의 데이터를 변환하는 간단하고 확장 가능하며 성능이 뛰어난 방법으로 BI 및 기타 도구에 공급하거나 관계형 데이터 저장소(Synapse SQL 데이터베이스, Azure SQL Database 등)에 로드할 수 있습니다.

서버리스 SQL 풀에서는 다음과 같은 다양한 전문가 역할을 활용할 수 있습니다.

- 데이터 엔지니어는 이 서비스를 사용하여 레이크를 검색하고, 데이터를 변환 및 준비하며, 데이터 변환 파이프라인을 간소화할 수 있습니다. 자세한 내용은 이 [자습서](tutorial-data-analyst.md)를 참조하세요.
- 데이터 과학자는 OPENROWSET 및 자동 스키마 유추와 같은 기능을 통해 레이크에 있는 데이터의 내용과 구조를 빠르게 추론할 수 있습니다.
- 데이터 분석가는 친숙한 T-SQL 언어 또는 즐겨찾는 도구를 사용하여 데이터 과학자 또는 데이터 엔지니어가 만든 [데이터 및 Spark 외부 테이블을 검색](develop-storage-files-spark-tables.md)할 수 있으며, 이를 서버리스 SQL 풀에 연결할 수 있습니다.
- BI 전문가는 [레이크 및 Spark 테이블의 데이터를 기반으로 하여 Power BI 보고서를 빠르게 만들](tutorial-connect-power-bi-desktop.md) 수 있습니다.

## <a name="how-to-start-using-serverless-sql-pool"></a>서버리스 SQL 풀 사용을 시작하는 방법

서버리스 SQL 풀 엔드포인트는 모든 Azure Synapse 작업 영역 내에 제공됩니다. 친숙한 도구를 사용하여 작업 영역을 만들고 데이터 쿼리를 즉시 시작할 수 있습니다.

최상의 성능을 얻으려면 [모범 사례](best-practices-serverless-sql-pool.md)를 적용해야 합니다.

## <a name="client-tools"></a>클라이언트 도구

서버리스 SQL 풀을 통해 기존 SQL 임시 쿼리 및 비즈니스 인텔리전스 도구를 사용하여 데이터 레이크를 활용할 수 있습니다. 친숙한 T-SQL 구문을 제공하므로 TDS 연결 SQL 제품을 설정할 수 있는 도구에서 [Synapse SQL에 연결하여 쿼리](connect-overview.md)할 수 있습니다. Azure Data Studio에 연결하여 임시 쿼리를 실행하거나, Power BI에 연결하여 몇 분 내에 인사이트를 얻을 수 있습니다.

## <a name="t-sql-support"></a>T-SQL 지원

서버리스 SQL 풀은 T-SQL 쿼리 노출 영역을 제공하며, 반정형 및 비정형 데이터 쿼리와 관련된 환경을 수용하기 위해 일부 측면에서 약간 향상되고 확장되었습니다. 또한 서버리스 SQL 풀에 대한 설계로 인해 T-SQL 언어의 일부 측면이 지원되지 않습니다. 예를 들어 DML 기능은 현재 지원되지 않습니다.

- 워크로드는 다음과 같은 친숙한 개념을 사용하여 구성할 수 있습니다.
- 데이터베이스 - 서버리스 SQL 풀 엔드포인트에 여러 개의 데이터베이스가 있을 수 있습니다.
- 스키마 - 데이터베이스 내에 스키마라는 하나 이상의 개체 소유권 그룹이 있을 수 있습니다.
- 뷰, 저장 프로시저, 인라인 테이블 값 함수
- 외부 리소스 - 데이터 원본, 파일 형식 및 테이블

보안은 다음을 사용하여 적용할 수 있습니다.

- 로그인 및 사용자
- 스토리지 계정에 대한 액세스를 제어하기 위한 자격 증명
- 개체 수준당 권한 부여, 거부 및 철회
- Azure Active Directory 통합

지원되는 T-SQL은 다음과 같습니다.

- 대부분의 SQL 함수를 포함하여 전체 [SELECT](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) 노출 영역이 지원됨
- CETAS - CREATE EXTERNAL TABLE AS SELECT
- 보기 및 보안에만 관련된 DDL 문

서버리스 SQL 풀에는 로컬 스토리지가 없으며, 메타데이터 개체만 데이터베이스에 저장됩니다. 따라서 다음 개념과 관련된 T-SQL은 지원되지 않습니다.

- 테이블
- 트리거
- 구체화된 보기
- 보기 및 보안과 관련되지 않은 DDL 문
- DML 문

### <a name="extensions"></a>확장

서버리스 SQL 풀은 데이터 레이크의 파일에 상주하는 데이터를 적절히 쿼리할 수 있도록 다음 기능을 추가하여 기존 [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?view=azure-sqldw-latest&preserve-view=true) 함수를 확장합니다.

[여러 파일 또는 폴더 쿼리](query-data-storage.md#query-multiple-files-or-folders)

[PARQUET 파일 형식 쿼리](query-data-storage.md#query-parquet-files)

[DELTA 형식 쿼리](query-delta-lake-format.md)

[다양한 구분된 텍스트 형식(사용자 지정 필드 종결자, 행 종결자, 이스케이프 문자)](query-data-storage.md#query-csv-files)

[Cosmos DB 분석 저장소](query-cosmos-db-analytical-store.md?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=openrowset-key)

[선택한 열 하위 집합 읽기](query-data-storage.md#read-a-chosen-subset-of-columns)

[스키마 유추](query-data-storage.md#schema-inference)

[filename 함수](query-data-storage.md#filename-function)

[filepath 함수](query-data-storage.md#filepath-function)

[복합 형식 및 중첩되거나 반복되는 데이터 구조 작업](query-data-storage.md#work-with-complex-types-and-nested-or-repeated-data-structures)

## <a name="security"></a>보안

서버리스 SQL 풀은 데이터에 대한 액세스를 보호하는 메커니즘을 제공합니다.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory 통합 및 다단계 인증

서버리스 SQL 풀을 사용하면 [Azure Active Directory 통합](../../azure-sql/database/authentication-aad-configure.md)을 통해 데이터베이스 사용자 및 다른 Microsoft 서비스의 ID를 중앙 집중식으로 관리할 수 있습니다. 이 기능은 사용 권한 관리를 간소화하고 보안을 향상시킵니다. Azure AD(Azure Active Directory)는 Single Sign-On 프로세스를 지원하는 동시에 데이터 및 애플리케이션 보안을 향상시키기 위해 [MFA(다단계 인증)](../../azure-sql/database/authentication-mfa-ssms-configure.md)를 지원합니다.

#### <a name="authentication"></a>인증

서버리스 SQL 풀 인증은 엔드포인트에 연결할 때 사용자가 자신의 ID를 증명하는 방법을 나타냅니다. 지원되는 두 가지 인증 유형은 다음과 같습니다.

- **SQL 인증**

  이 인증 방법은 사용자 이름과 암호를 사용합니다.

- **Azure Active Directory 인증**:

  이 인증 방법은 Azure Active Directory에서 관리하는 ID를 사용합니다. Azure AD 사용자의 경우 다단계 인증을 사용하도록 설정할 수 있습니다. [가능한 경우](/sql/relational-databases/security/choose-an-authentication-mode?view=azure-sqldw-latest&preserve-view=true) Active Directory 인증(통합 보안)을 사용합니다.

#### <a name="authorization"></a>권한 부여

권한 부여는 사용자가 서버리스 SQL 풀 데이터베이스 내에서 수행할 수 있는 작업을 나타내며, 사용자 계정의 데이터베이스 역할 멤버 자격 및 개체 수준 권한으로 제어됩니다.

SQL 인증을 사용하는 경우 SQL 사용자는 서버리스 SQL 풀에만 존재하며, 권한의 범위는 서버리스 SQL 풀의 개체로 지정됩니다. 다른 서비스(예: Azure Storage)의 보안 개체에 대한 액세스는 서버리스 SQL 풀 범위에서만 존재하므로 SQL 사용자에게 직접 부여할 수 없습니다. SQL 사용자는 [지원되는 권한 부여 유형](develop-storage-files-storage-access-control.md#supported-storage-authorization-types) 중 하나를 사용하여 파일에 액세스해야 합니다.

Azure AD 인증을 사용하는 경우 사용자는 서버리스 SQL 풀 및 Azure Storage와 같은 다른 서비스에 로그인하여 Azure AD 사용자에게 권한을 부여할 수 있습니다.

### <a name="access-to-storage-accounts"></a>스토리지 계정에 액세스

서버리스 SQL 풀 서비스에 로그인한 사용자는 Azure Storage의 파일에 액세스하고 쿼리할 수 있는 권한이 있어야 합니다. 서버리스 SQL 풀에서 지원하는 권한 부여 유형은 다음과 같습니다.

- **[SAS(공유 액세스 서명)](develop-storage-files-storage-access-control.md?tabs=shared-access-signature)** 는 스토리지 계정의 리소스에 대한 위임된 액세스를 제공합니다. SAS를 사용하면 계정 키를 공유하지 않고 스토리지 계정의 리소스에 대한 액세스 권한을 클라이언트에 부여할 수 있습니다. SAS는 SAS를 사용하는 클라이언트에 부여하는 액세스 유형에 대한 세부적인 제어(유효성 간격, 부여된 권한, 허용되는 IP 주소 범위 및 허용되는 프로토콜(https/http))를 제공합니다.

- **[사용자 ID](develop-storage-files-storage-access-control.md?tabs=user-identity)** (“패스스루”라고도 함)는 서버리스 SQL 풀에 로그인한 Azure AD 사용자의 ID를 사용하여 데이터에 대한 액세스 권한을 부여하는 권한 부여 유형입니다. 데이터에 액세스하기 전에 Azure Storage 관리자가 Azure AD 사용자에게 데이터에 액세스할 수 있는 권한을 부여해야 합니다. 이 권한 부여 유형은 서버리스 SQL 풀에 로그인한 Azure AD 사용자를 사용하므로 SQL 사용자 유형에는 지원되지 않습니다.

- **[작업 영역 ID](develop-storage-files-storage-access-control.md?tabs=managed-identity)** 는 Synapse 작업 영역의 ID를 사용하여 데이터에 대한 액세스 권한을 부여하는 권한 부여 유형입니다. 데이터에 액세스하기 전에 Azure Storage 관리자가 데이터에 액세스하기 위한 권한을 작업 영역 ID에 부여해야 합니다.

### <a name="access-to-cosmos-db"></a>Cosmos DB에 액세스

[Cosmos DB 분석 저장소에 액세스](query-cosmos-db-analytical-store.md?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=openrowset-key)하려면 Cosmos DB 계정 읽기 전용 키를 사용하여 서버 수준 또는 데이터베이스 범위 자격 증명을 만들어야 합니다.

## <a name="next-steps"></a>다음 단계
엔드포인트 연결 및 파일 쿼리에 대한 추가 정보는 다음 문서에서 확인할 수 있습니다. 
- [엔드포인트에 연결](connect-overview.md)
- [파일 쿼리](develop-storage-files-overview.md)
