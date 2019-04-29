---
title: Azure 데이터 카탈로그 개발자 개념
description: 카탈로그 REST API를 통해 노출된 것으로 Azure 데이터 카탈로그 개념적 모델의 주요 개념을 소개합니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 42e4b545a48bcbd0ad4b7faf077ebdbfe21648b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61002678"
---
# <a name="azure-data-catalog-developer-concepts"></a>Azure 데이터 카탈로그 개발자 개념
Microsoft **Azure 데이터 카탈로그**는 데이터 원본 검색 및 크라우드소싱 데이터 원본 메타데이터에 대한 기능을 제공하는 완전히 관리되는 클라우드 서비스입니다. 개발자는 REST API를 통해 서비스를 사용할 수 있습니다. 서비스에서 구현되는 개념을 이해 하는 것은 개발자가 **Azure 데이터 카탈로그**를 성공적으로 통합하는 데 중요합니다.

## <a name="key-concepts"></a>주요 개념
합니다 **Azure Data Catalog** 개념적 모델 네 가지 주요 개념을 기반으로 합니다. **카탈로그**, **사용자가**를 **자산**, 및 **주석**합니다.

![개념][1]

*그림 1-Azure 데이터 카탈로그 단순 개념적 모델*

### <a name="catalog"></a>카탈로그
**카탈로그**는 조직이 저장하는 모든 메타데이터에 대한 최상위 컨테이너입니다. Azure 계정당 하나의 **카탈로그**가 허용합니다. 카탈로그는 Azure 구독에 연결되어 있지만, 계정에 여러 구독이 있을 수 있더라도 지정된 Azure 계정에 대해 **카탈로그**를 한 개만 만들 수 있습니다.

카탈로그에는 **사용자** 및 **자산**이 포함됩니다.

### <a name="users"></a>사용자
사용자는 카탈로그에서 작업(카탈로그 검색, 항목 추가, 편집 또는 제거 등)을 수행할 권한이 있는 보안 주체입니다.

사용자가 할 수 있는 여러 역할이 있습니다. 역할에 대한 자세한 내용은 역할 및 권한 부여 섹션을 참조하세요.

개별 사용자 및 보안 그룹을 추가할 수 있습니다.

Azure 데이터 카탈로그는 관리를 식별하고 액세스하기 위해 Azure Active Directory를 사용합니다. 각 카탈로그 사용자는 계정에 대해 Active Directory의 구성원이어야 합니다.

### <a name="assets"></a>자산
**카탈로그**에는 데이터 자산이 포함됩니다. **자산**은 카탈로그에서 관리하는 세분성의 단위입니다.

자산의 세분성은 데이터 소스에 따라 다릅니다. SQL Server 또는 Oracle 데이터베이스에서 자산은 테이블 또는 뷰가 될 수 있습니다. SQL Server Analysis Services에서 자산은 측정값, 차원, 또는 KPI(주요 성능 표시기)가 될 수 있습니다. SQL Server Reporting Services에서 자산은 보고서입니다.

**자산**은 카탈로그에서 추가하거나 제거합니다. 자산은 **검색**에서 얻게 되는 결과의 단위입니다.

**자산**은 이름, 위치, 형식과 자산을 자세히 설명하는 주석으로 구성됩니다.

### <a name="annotations"></a>주석
주석은 자산에 대한 메타데이터를 나타내는 항목입니다.

주석의 예는 설명, 태그, 스키마 및 설명서 등입니다. 자산 형식 및 주석 형식의 전체 목록은 자산 개체 모델 섹션에 있습니다.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>크라우드소싱 주석 및 사용자 관점(의견의 복합성)
Azure 데이터 카탈로그의 주요 측면은 시스템에서 메타데이터의 크라우드소싱을 지원하는 방법입니다. 하나의 의견만 있고 마지막 기록자가 우선하는 wiki 접근 방식과 대비하여, Azure 데이터 카탈로그 모델은 시스템에 여러 의견이 나란히 있도록 허용합니다.

이 방법은 여러 사용자가 지정된 자산에 대해 다른 관점을 가질 수 있는 기업 데이터의 실제 세계를 반영합니다.

* 데이터베이스 관리자는 대량 ETL 작업에 대한 서비스 수준 계약 또는 사용 가능한 처리 창에 대한 정보를 제공할 수 있습니다.
* 데이터 담당자는 자산이 적용되는 비즈니스 프로세스 또는 비즈니스가 적용되는 분류에 대한 정보를 제공할 수 있습니다.
* 재무 분석가는 보고 작업 종료의 기간 동안 데이터를 사용하는 방법에 대한 정보를 제공할 수 있습니다.

이 예시를 지원하기 위해 각 사용자(DBA, 데이터 담당자 및 분석가)는 카탈로그에 등록된 단일 테이블에 대한 설명을 추가할 수 있습니다. 모든 설명은 시스템 및 모든 설명이 표시되는 Azure 데이터 카탈로그에서 유지 관리됩니다.

이 패턴은 개체 모델에 있는 대부분의 항목에 적용되므로 대체로 JSON 페이로드의 개체 형식이 Singleton을 예상할 수 있는 속성에 대해 배열됩니다.

예를 들어, 자산에서 루트는 설명 개체의 배열입니다. 배열 속성을 "설명" 이라고 합니다. 설명 개체에는 하나의 속성 - 설명이 있습니다. 설명을 입력하는 각 사용자가 사용자에 의해 제공된 값에 대해 생성된 설명 개체를 가져오는 것이 패턴입니다.

이 때 UX는 조합을 표시하는 방법을 선택할 수 있습니다. 디스플레이 대한 세 가지 다른 패턴이 있습니다.

* 가장 간단한 패턴은 "모두 표시"입니다. 이 패턴에서 모든 개체는 목록 보기에 표시됩니다. Azure Data Catalog 포털 UX는 설명에 대해 이 패턴을 사용합니다.
* 다른 패턴은 "병합"입니다. 이 패턴에서 다른 사용자의 모든 값 중복을 제거하여 함께 병합됩니다. Azure 데이터 카탈로그 포털 UX에서 이 패턴의 예는 태그 및 전문가 속성입니다.
* 세 번째 패턴은 "마지막 기록자 우선"입니다. 이 패턴에서는 입력한 가장 최근의 값만 표시 됩니다. friendlyName은 이 패턴의 예입니다.

## <a name="asset-object-model"></a>자산 개체 모델
주요 개념 섹션에 소개된 것처럼 **Azure 데이터 카탈로그** 개체 모델에는 자산 또는 주석일 수 있는 항목이 포함됩니다. 항목에는 선택 또는 필수가 될 수 있는 속성이 있습니다. 일부 속성은 모든 항목에 적용 됩니다. 일부 속성은 모든 자산에 적용 됩니다. 일부 속성은 특정 자산 형식에만 적용 됩니다.

### <a name="system-properties"></a>시스템 속성
<table><tr><td><b>속성 이름</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>timestamp</td><td>DateTime</td><td>마지막으로 항목이 수정된 시간. 이 필드는 항목을 삽입하고 항목이 업데이트될 때마다 서버에서 생성됩니다. 게시 작업의 입력에서 이 속성의 값은 무시됩니다.</td></tr><tr><td>id</td><td>Uri</td><td>항목의 절대 url입니다(읽기 전용). 항목에 대한 고유한 주소 지정 가능 URI입니다.  게시 작업의 입력에서 이 속성의 값은 무시됩니다.</td></tr><tr><td>형식</td><td>문자열</td><td>자산의 유형입니다(읽기 전용).</td></tr><tr><td>etag</td><td>문자열</td><td>카탈로그에서 항목을 업데이트하는 작업을 수행할 때 낙관적 동시성 제어에 사용할 수 있는 항목의 버전에 해당하는 문자열입니다. "*"은 값을 일치시키는 데 사용할 수 있습니다.</td></tr></table>

### <a name="common-properties"></a>공용 속성
이러한 속성은 모든 루트 자산 형식 및 모든 주석 형식에 적용 됩니다.

<table>
<tr><td><b>속성 이름</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr>
<tr><td>fromSourceSystem</td><td>BOOLEAN</td><td>항목의 데이터가 원본 시스템(예: Sql Server 데이터베이스, Oracle 데이터베이스 또는 사용자가 작성)에서 파생되었는지 여부를 나타냅니다.</td></tr>
</table>

### <a name="common-root-properties"></a>공용 루트 속성
<p>
이러한 속성은 모든 루트 자산 형식에 적용 됩니다.

<table><tr><td><b>속성 이름</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr><tr><td>이름</td><td>문자열</td><td>데이터 원본 위치 정보에서 파생된 이름</td></tr><tr><td>dsl</td><td>DataSourceLocation</td><td>고유하게 데이터 원본을 설명하고 자산에 대한 식별자 중 하나입니다. (이중 ID 섹션 참조).  dsl의 구조는 프로토콜 및 원본 유형에 따라 달라집니다.</td></tr><tr><td>DataSource</td><td>DataSourceInfo</td><td>자산 형식에 대한 세부 정보.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>이 자산을 가장 최근에 등록한 사용자를 설명합니다.  사용자(upn)뿐만 아니라 표시 이름(lastName과 firstName)에 대한 고유 ID를 포함합니다.</td></tr><tr><td>containerId</td><td>문자열</td><td>데이터 원본에 대한 컨테이너 자산의 ID입니다. 이 속성은 컨테이너 형식에 대해 지원되지 않습니다.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>일반적인 단일 항목이 아닌 주석 속성
이러한 속성은 모든 단일 항목이 아닌 주석 형식에 적용됩니다(자산별 여러 개를 허용하는 주석).

<table>
<tr><td><b>속성 이름</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr>
<tr><td>key</td><td>문자열</td><td>현재 컬렉션의 주석을 고유하게 식별하는 사용자 지정된 키입니다. 키 길이는 256자를 초과할 수 없습니다.</td></tr>
</table>

### <a name="root-asset-types"></a>루트 자산 형식
루트 자산 형식은 카탈로그에 등록 될 수 있는 다양한 유형의 데이터 자산을 나타내는 자산 형식입니다. 각 루트 형식에는 뷰에 포함된 자산 및 주석을 설명하는 뷰가 있습니다. 뷰 이름은 REST API를 사용하여 자산을 게시할 때, 해당하는 {view_name} URL 세그먼트에 사용되어야 합니다.

<table><tr><td><b>자산 형식(뷰 이름)</b></td><td><b>추가 속성</b></td><td><b>데이터 형식</b></td><td><b>허용된 주석</b></td><td><b>설명</b></td></tr><tr><td>테이블 ("tables")</td><td></td><td></td><td>설명<p>FriendlyName<p>태그<p>스키마<p>ColumnDescription<p>ColumnTag<p> 전문가<p>미리 보기<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>문서화<p></td><td>테이블은 모든 테이블 형식 데이터를 나타냅니다.  예를 들면 다음과 같습니다. SQL 테이블, SQL 보기, Analysis Services 테이블 형식 테이블, Analysis Services 다차원 차원, Oracle 테이블 등입니다.   </td></tr><tr><td>측정값("measures")</td><td></td><td></td><td>설명<p>FriendlyName<p>태그<p>전문가<p>AccessInstruction<p>문서화<p></td><td>이 유형은 Analysis Services 측정값을 나타냅니다.</td></tr><tr><td></td><td>측정값</td><td>열</td><td></td><td>측정값을 설명하는 메타데이터</td></tr><tr><td></td><td>isCalculated </td><td>BOOLEAN</td><td></td><td>측정값이 계산할지 여부를 지정합니다.</td></tr><tr><td></td><td>측정값 그룹</td><td>문자열</td><td></td><td>측정값에 대한 물리적 컨테이너</td></tr><td>KPI("kpis")</td><td></td><td></td><td>설명<p>FriendlyName<p>태그<p>전문가<p>AccessInstruction<p>문서화</td><td></td></tr><tr><td></td><td>측정값 그룹</td><td>문자열</td><td></td><td>측정값에 대한 물리적 컨테이너</td></tr><tr><td></td><td>goalExpression</td><td>문자열</td><td></td><td>KPI의 목표값을 반환하는 MDX 숫자 식 또는 계산.</td></tr><tr><td></td><td>valueExpression</td><td>문자열</td><td></td><td>KPI의 실제 값을 반환하는 MDX 숫자 식.</td></tr><tr><td></td><td>statusExpression</td><td>문자열</td><td></td><td>특정 시점에 지정한 KPI의 상태를 나타내는 MDX 식.</td></tr><tr><td></td><td>trendExpression</td><td>문자열</td><td></td><td>시간이 지남에 따라 KPI 값을 계산하는 MDX 식.  특정 비즈니스 컨텍스트에서 유용한 시간 기반 조건이 추세가 될 수 있습니다.</td>
<tr><td>보고서("reports")</td><td></td><td></td><td>설명<p>FriendlyName<p>태그<p>전문가<p>AccessInstruction<p>설명서<p></td><td>이 형식은 SQL Server Reporting Services 보고서를 나타냅니다. </td></tr><tr><td></td><td>assetCreatedDate</td><td>문자열</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>문자열</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>문자열</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>문자열</td><td></td><td></td></tr><tr><td>컨테이너("containers")</td><td></td><td></td><td>설명<p>FriendlyName<p>태그<p>전문가<p>AccessInstruction<p>문서화<p></td><td>이 형식은 SQL 데이터베이스, Azure Blob 컨테이너 또는 Analysis Services 모델과 같은 다른 자산의 컨테이너를 나타냅니다.</td></tr></table>

### <a name="annotation-types"></a>주석 형식
주석 형식은 카탈로그 내에서 다른 형식으로 할당할 수 있는 메타데이터의 형식을 나타냅니다.

<table>
<tr><td><b>주석 형식(중첩된 뷰 이름)</b></td><td><b>추가 속성</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr>

<tr><td>설명("descriptions")</td><td></td><td></td><td>이 속성은 자산에 대한 설명을 포함합니다. 시스템의 각 사용자는 자신의 설명을 추가할 수 있습니다.  해당 사용자만 설명 개체를 편집할 수 있습니다.  (Admins 및 자산 소유자는 설명 개체를 삭제할 수 있지만 편집할 수는 없습니다.) 시스템은 사용자의 설명을 별도로 유지합니다.  따라서 각 자산에 대한 설명의 배열이 있습니다.(아마도 데이터 소스로부터 도출 된 정보를 포함한 사람 이외에, 자산에 대한 지식에 기여한 각 사용자에 대한 배열)</td></tr>
<tr><td></td><td>description</td><td>문자열</td><td>자산에 대해 간단한 설명(2-3 줄)</td></tr>

<tr><td>태그("tags")</td><td></td><td></td><td>이 속성은 자산에 대한 태그를 정의합니다. 시스템의 각 사용자는 자산에 대해 여러 태그를 추가할 수 있습니다.  태그 개체를 만든 사용자만 편집할 수 있습니다.  (Admins 및 자산 소유자는 태그 개체를 삭제할 수 있지만 편집할 수는 없습니다.) 시스템은 사용자의 태그를 별도로 유지합니다.  따라서 각 자산에 대한 태그 개체의 배열이 있습니다.</td></tr>
<tr><td></td><td>tag</td><td>문자열</td><td>자산을 설명하는 태그입니다.</td></tr>

<tr><td>이름("friendlyName")</td><td></td><td></td><td>이 속성은 자산에 대한 친숙한 이름을 포함합니다. FriendlyName은 단일 주석입니다. 자산에 하나의 FriendlyName만을 추가할 수 있습니다.  FriendlyName 개체를 만든 사용자만 편집할 수 있습니다. (Admins 및 자산 소유자는 FriendlyName 개체를 삭제할 수 있지만 편집할 수는 없습니다.) 시스템은 사용자의 친숙한 이름을 유지합니다.</td></tr>
<tr><td></td><td>friendlyName</td><td>문자열</td><td>자산의 이름입니다.</td></tr>

<tr><td>스키마("schema")</td><td></td><td></td><td>스키마는 데이터의 구조를 설명합니다.  스키마는 특성(열, 특성, 필드, 등), 이름, 형식뿐 아니라 다른 메타데이터도 나열합니다.  이 정보는 모든 데이터 소스에서 파생됩니다.  스키마는 단일 주석입니다. 자산에 하나의 스키마만을 추가할 수 있습니다.</td></tr>
<tr><td></td><td>열</td><td>Column[]</td><td>열 개체의 배열입니다. 열을 데이터 소스에서 파생된 정보와 함께 설명합니다.</td></tr>

<tr><td>ColumnDescription("columnDescriptions")</td><td></td><td></td><td>이 속성은 열에 대한 설명을 포함합니다.  시스템의 각 사용자는 여러 열에 대해 고유한 설명을 추가할 수 있습니다(열당 최대 하나). ColumnDescription 개체를 만든 사용자만 편집할 수 있습니다.  (Admins 및 자산 소유자는 ColumnDescription 개체를 삭제할 수 있지만 편집할 수는 없습니다.) 시스템은 이러한 사용자의 열 설명을 별도로 유지합니다.  따라서 각 자산에 대한 ColumnDescription 개체의 배열이 있습니다(아마도 데이터 원본으로부터 도출된 정보를 포함한 사람 이외에 열에 대한 지식에 기여한 각 사용자에 대해 열당 하나).  ColumnDescription은 스키마에 느슨하게 바인딩되므로 동기화가 해제될 수 있습니다. ColumnDescription에서 스키마에 더 이상 존재하지 않는 열을 설명할 수 있습니다.  동기화에 설명 및 스키마를 유지하는 것은 작성자에 따라 다릅니다.  또한 데이터 원본은 열 설명 정보를 포함할 수 있으며 도구를 실행할 때 발생할 수 있는 추가 ColumnDescription 개체가 될 것입니다.</td></tr>
<tr><td></td><td>columnName</td><td>문자열</td><td>이 설명이 참조하는 열의 이름입니다.</td></tr>
<tr><td></td><td>description</td><td>문자열</td><td>열에 대한 간단한 설명입니다(2-3 줄).</td></tr>

<tr><td>ColumnTag("columnTags")</td><td></td><td></td><td>이 속성은 열에 대한 태그를 포함합니다. 시스템의 각 사용자는 지정된 열에 대해 여러 태그를 추가할 수 있으며 여러 열에 대해 태그를 추가할 수 있습니다. ColumnTag 개체를 만든 사용자만 편집할 수 있습니다. (Admins 및 자산 소유자는 ColumnTag 개체를 삭제할 수 있지만 편집할 수는 없습니다.) 시스템은 이러한 사용자의 열 태그를 별도로 유지합니다.  따라서 각 자산에 대한 ColumnTag 개체의 배열이 있습니다.  ColumnTag는 스키마에 느슨하게 바인딩되므로 동기화가 해제될 수 있습니다. ColumnTag에서 스키마에 더 이상 존재하지 않는 열을 설명할 수 있습니다.  동기화에 열 태그 및 스키마를 유지하는 것은 작성자에 따라 다릅니다.</td></tr>
<tr><td></td><td>columnName</td><td>문자열</td><td>이 태그가 참조하는 열의 이름입니다.</td></tr>
<tr><td></td><td>tag</td><td>문자열</td><td>열을 설명하는 태그입니다.</td></tr>

<tr><td>전문가("experts")</td><td></td><td></td><td>이 속성은 데이터 집합에서 전문가로 간주되는 사용자를 포함합니다. 전문가 의견(설명)은 설명 목록을 만들 때 UX 위에 풍선으로 표시됩니다. 각 사용자는 자신의 전문가를 지정할 수 있습니다. 해당 사용자만 전문가 개체를 편집할 수 있습니다. (Admins 및 자산 소유자는 전문가 개체를 삭제할 수 있지만 편집할 수는 없습니다.)</td></tr>
<tr><td></td><td>전문가</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>미리 보기("previews")</td><td></td><td></td><td>미리 보기는 자산에 대한 데이터의 상위 20 행의 스냅숏을 포함합니다. 미리 보기는 일부 형식의 자산에만 적합합니다(테이블에 대해서는 적합하지만 측정값에 대해서는 적합하지 않음).</td></tr>
<tr><td></td><td>미리 보기</td><td>object[]</td><td>열을 나타내는 개체의 배열입니다.  각 개체에는 행에 대해 해당 열에 대한 값을 가진 열에 매핑되는 속성이 있습니다.</td></tr>

<tr><td>AccessInstruction("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>문자열</td><td>콘텐츠의 mime 형식입니다.</td></tr>
<tr><td></td><td>콘텐츠</td><td>문자열</td><td>이 데이터 자산에 액세스하는 방법에 대한 지침입니다. 콘텐츠는 URL, 메일 주소 또는 명령 집합일 수 있습니다.</td></tr>

<tr><td>TableDataProfile("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>int</td><td>데이터 집합의 행 수입니다.</td></tr>
<tr><td></td><td>size</td><td>long</td><td>데이터 집합의 크기(바이트)입니다.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>문자열</td><td>스키마가 마지막으로 수정된 시간입니다.</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>문자열</td><td>데이터 집합이 마지막으로 수정된 시간입니다(데이터 추가, 수정 또는 삭제).</td></tr>

<tr><td>ColumnsDataProfile("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>열</td></td><td>ColumnDataProfile[]</td><td>열 데이터 프로필의 배열입니다.</td></tr>

<tr><td>ColumnDataClassification("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>문자열</td><td>이 분류가 참조하는 열의 이름입니다.</td></tr>
<tr><td></td><td>분류</td><td>문자열</td><td>이 열에 있는 데이터의 분류입니다.</td></tr>

<tr><td>설명서("documentation")</td><td></td><td></td><td>지정된 자산은 하나의 설명서에만 연결될 수 있습니다.</td></tr>
<tr><td></td><td>mimeType</td><td>문자열</td><td>콘텐츠의 mime 형식입니다.</td></tr>
<tr><td></td><td>콘텐츠</td><td>문자열</td><td>설명서 내용입니다.</td></tr>

</table>

### <a name="common-types"></a>일반 형식
일반 형식 속성에 대 한 형식으로 사용할 수 있지만 항목은 없습니다.

<table>
<tr><td><b>일반 형식</b></td><td><b>속성</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>sourceType</td><td>문자열</td><td>데이터 원본 형식을 설명합니다.  예를 들면 다음과 같습니다. SQL Server, Oracle 데이터베이스 등입니다.  </td></tr>
<tr><td></td><td>ObjectType</td><td>문자열</td><td>데이터 원본에서 개체의 형식을 설명합니다. 예를 들면 다음과 같습니다. 테이블, SQL Server 용 뷰입니다.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>protocol</td><td>문자열</td><td>필수 사항입니다. 데이터 원본과 통신하는 데 사용되는 프로토콜을 설명합니다. 예: SQl Server의 경우 "tds", Oracle의 경우 "oracle" 등. 현재 지원되는 프로토콜의 목록은 <a href="https://docs.microsoft.com/azure/data-catalog/data-catalog-dsr">데이터 원본 참조 사양 - DSL 구조</a>를 참조하세요.</td></tr>
<tr><td></td><td>address</td><td>Dictionary<string, object></td><td>필수 사항입니다. 주소는 참조되는 데이터 원본을 식별하는 데 사용되는 프로토콜에 해당하는 데이터 집합입니다. 주소 데이터는 특정 프로토콜로 범위가 지정됩니다. 즉, 프로토콜을 모르면 의미가 없습니다.</td></tr>
<tr><td></td><td>인증</td><td>문자열</td><td>선택 사항입니다. 데이터 원본과의 통신에 사용되는 인증 체계입니다. 예: windows, oauth, 등.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Dictionary<string, object></td><td>선택 사항입니다. 데이터 원본에 연결하는 방법에 대한 추가적인 정보입니다.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>백 엔드는 게시하는 동안 AAD에 대해 제공되는 속성의 유효성 검사를 수행하지 않습니다.</td></tr>
<tr><td></td><td>upn</td><td>문자열</td><td>사용자의 고유한 전자 메일 주소입니다. objectId가 제공되지 않거나 "lastRegisteredBy" 속성의 컨텍스트에 있는 경우 지정되어야 합니다. 그렇지 않은 경우 선택 사항입니다.</td></tr>
<tr><td></td><td>objectId</td><td>Guid</td><td>사용자 또는 보안 그룹 AAD ID입니다. 선택 사항입니다. upn이 제공되지 않은 경우 지정해야 합니다. 그렇지 않은 경우 선택 사항입니다.</td></tr>
<tr><td></td><td>firstname</td><td>문자열</td><td>사용자의 이름(표시 용). 선택 사항입니다. "lastRegisteredBy" 속성의 컨텍스트에서만 유효합니다. "역할", "권한" 및 "전문가"에 대한 보안 주체를 제공할 때 지정할 수 없습니다.</td></tr>
<tr><td></td><td>Lastname</td><td>문자열</td><td>사용자 성(표시 용). 선택 사항입니다. "lastRegisteredBy" 속성의 컨텍스트에서만 유효합니다. "역할", "권한" 및 "전문가"에 대한 보안 주체를 제공할 때 지정할 수 없습니다.</td></tr>

<tr><td>열</td><td></td><td></td><td></td></tr>
<tr><td></td><td>이름</td><td>문자열</td><td>열 또는 특성의 이름입니다.</td></tr>
<tr><td></td><td>형식</td><td>문자열</td><td>열 또는 특성의 데이터 형식입니다. 허용되는 형식은 자산의 데이터 소스 형식에 따라 달라집니다.  형식의 하위 집합만 지원됩니다.</td></tr>
<tr><td></td><td>maxLength</td><td>int</td><td>열 또는 특성에 허용되는 최대 길이입니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr>
<tr><td></td><td>자릿수</td><td>byte</td><td>열 또는 특성에 대한 자릿수입니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr>
<tr><td></td><td>isNullable</td><td>BOOLEAN</td><td>열에 null 값의 허용 여부. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr>
<tr><td></td><td>식</td><td>문자열</td><td>값에 계산된 열이 있는 경우 이 필드에는 값을 표현하는 식이 포함됩니다. 데이터 소스에서 파생됩니다. 일부 소스 형식에만 적용됩니다.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>문자열</td><td>열의 이름입니다.</td></tr>
<tr><td></td><td>형식 </td><td>문자열</td><td>열의 형식입니다.</td></tr>
<tr><td></td><td>min </td><td>문자열</td><td>데이터 집합의 최소값입니다.</td></tr>
<tr><td></td><td>max </td><td>문자열</td><td>데이터 집합의 최대값입니다.</td></tr>
<tr><td></td><td>avg </td><td>double</td><td>데이터 집합의 평균 값입니다.</td></tr>
<tr><td></td><td>stdev </td><td>double</td><td>데이터 집합의 표준 편차입니다.</td></tr>
<tr><td></td><td>nullCount </td><td>int</td><td>데이터 집합에 있는 null 값의 개수입니다.</td></tr>
<tr><td></td><td>distinctCount  </td><td>int</td><td>데이터 집합에 있는 고유한 값의 개수입니다.</td></tr>


</table>

## <a name="asset-identity"></a>자산 ID
Azure Data Catalog는 DataSourceLocation "dsl" 속성의 "address" 속성 모음의 ID 및 "protocol" 속성을 사용하여 카탈로그 내 자산을 호출하는 데 사용되는 자산의 ID를 생성합니다.
예를 들어, "tds" 프로토콜에는 "server", "database", "schema", "object"라는 ID 속성이 있습니다. 프로토콜 및 ID 속성의 조합은 SQL Server 테이블 자산의 ID를 생성하는 데 사용됩니다.
Azure Data Catalog에는 몇 가지 데이터 원본 프로토콜이 기본으로 제공되며 [데이터 원본 참조 사양 - DSL 구조](data-catalog-dsr.md)에 목록이 있습니다.
지원되는 프로토콜 집합은 프로그래밍 방식으로 확장될 수 있습니다(Data Catalog REST API 참조를 참조하세요). 카탈로그의 관리자는 사용자 지정 데이터 원본 프로토콜을 등록할 수 있습니다. 다음 표에서 사용자 지정 프로토콜을 등록하는 데 필요한 속성을 설명합니다.

### <a name="custom-data-source-protocol-specification"></a>사용자 지정 데이터 원본 프로토콜 사양
<table>
<tr><td><b>형식</b></td><td><b>속성</b></td><td><b>데이터 형식</b></td><td><b>설명</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>namespace</td><td>문자열</td><td>프로토콜의 네임스페이스입니다. 네임스페이스의 길이는 1~255자 사이여야 하고, 점(.)으로 구분된 비지 않은 부분을 하나 이상 포함해야 합니다. 각 부분의 길이는 1~255자 사이여야 하고, 문자로 시작하고 문자와 숫자만 포함해야 합니다.</td></tr>
<tr><td></td><td>이름</td><td>문자열</td><td>프로토콜의 이름입니다. 이름의 길이는 1~255자 사이여야 하고, 문자로 시작하고 문자, 숫자, 대시(-) 문자만 포함해야 합니다.</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty[]</td><td>ID 속성 목록이며, 하나 이상 20 이하의 속성을 포함해야 합니다. 예를 들어, "server", "database", "schema", "object"는 "tds" 프로토콜의 ID 속성입니다.</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet[]</td><td>ID 목록 집합입니다. 유효한 자산의 ID를 나타내는 ID 속성 집합을 정의합니다. 하나 이상 20 이하의 집합을 포함해야 합니다. 예를 들어, {"server", "database", "schema" and "object"}는 "tds" 프로토콜에 대한 ID 집합이며, Sql Server 테이블 자산의 ID를 정의합니다.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>이름</td><td>문자열</td><td>속성의 이름입니다. 이름의 길이는 1~100자 사이여야 하고, 문자로 시작해야 하고 문자와 숫자만 포함할 수 있습니다.</td></tr>
<tr><td></td><td>형식</td><td>문자열</td><td>속성의 유형입니다. 지원되는 값: "bool", boolean", "byte", "guid", "int", "integer", "long", "string", "url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>속성 값을 사용할 때 대/소문자를 무시할지 여부를 나타냅니다. "string" 형식의 속성에 대해서만 지정할 수 있습니다. 기본값은 False입니다.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool[]</td><td>URL 경로의 각 세그먼트에 대해 대/소문자를 무시할지 여부를 나타냅니다. "url" 형식의 속성에 대해서만 지정할 수 있습니다. 기본값은 [false]입니다.</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>이름</td><td>문자열</td><td>ID 집합의 이름입니다.</td></tr>
<tr><td></td><td>properties</td><td>string[]</td><td>이 ID 집합에 포함된 ID 속성의 목록입니다. 중복 항목을 포함할 수 없습니다. ID 집합에 의해 참조되는 각 속성은 프로토콜의 "identityProperties" 목록에 정의되어야 합니다.</td></tr>

</table>

## <a name="roles-and-authorization"></a>역할 및 권한 부여
Microsoft Azure 데이터 카탈로그는 자산 및 주석에 대한 CRUD 작업에 대해 권한 부여 기능을 제공합니다.

## <a name="key-concepts"></a>주요 개념
Azure 데이터 카탈로그는 두 가지 권한 부여 메커니즘을 사용합니다.

* 역할 기반 권한 부여
* 사용 권한 기반의 권한 부여

### <a name="roles"></a>역할
세 가지 역할이 있습니다. **관리자**하십시오 **소유자**, 및 **참가자**합니다.  각 역할에는 다음 표에 요약된 범위와 권한이 있습니다.

<table><tr><td><b>역할</b></td><td><b>범위</b></td><td><b>권한</b></td></tr><tr><td>관리자</td><td>카탈로그(카탈로그의 모든 자산/주석)</td><td>Read Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>소유자</td><td>각 자산(루트 항목)</td><td>Read Delete ViewRoles

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>참가자</td><td>각 개별 자산 및 주석</td><td>읽기/업데이트/삭제/권한보기/참고(Read Update Delete ViewRoles Note): 항목에서 읽기(Read) 권한을 참여자가 해지하는 경우 모든 권한이 취소 됩니다.</td></tr></table>

> [!NOTE]
> **읽기**, **업데이트**, **삭제**, **역할 보기** 권한은 모든 항목 (자산 또는 주석)에 적용할 수 있으며, 반면에 **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions**는 루트 자산에만 적용됩니다.
> 
> **삭제** 권한은 항목뿐만 그 아래에 있는 모든 하위 항목 또는 단일 항목에 적용됩니다. 예를 들어, 자산을 삭제하면 해당 자산에 대한 모든 주석도 삭제됩니다.
> 
> 

### <a name="permissions"></a>권한
권한은 액세스 제어 항목의 목록입니다. 각 액세스 제어 항목은 보안 주체에 대한 권한의 집합을 할당합니다. 권한은 자산(즉, 루트 항목)에만 지정할 수 있으며 자산 및 모든 하위 항목에 적용됩니다.

**Azure Data Catalog** 미리 보기 중에 권한 목록에서 **읽기** 권한만이 지원되어 자산의 표시를 제한하는 시나리오를 사용합니다.

기본적으로 모든 인증된 사용자는 권한의 주체 집합에 표시를 제한하지 않는 한 카탈로그에 있는 모든 항목에 대해 **읽기** 권한이 있습니다.

## <a name="rest-api"></a>REST API
**PUT** 및 **POST** 보기 항목 요청은 역할 및 권한을 제어하는 데 사용할 수 있습니다. 항목 페이로드 외에도 두 시스템 속성은 **roles** 및 **permissions**를 지정할 수 있습니다.

> [!NOTE]
> **permissions**은 루트 항목에만 적용됩니다.
> 
> **소유자** 역할은 루트 항목에만 적용됩니다.
> 
> 기본적으로 카탈로그에서 항목을 만들면 **참여자**는 현재 인증된 사용자로 설정됩니다. 누구나 항목을 업데이트할 수 있도록 하려면 항목을 처음 게시할 때 **역할** 속성에서 **참여자**를 &lt;Everyone&gt; 특수 보안 주체로 설정해야 합니다(다음 예를 참조). **참여자**는 변경할 수 없으며 항목의 수명 동안 동일하게 유지됩니다. **관리자**나 **소유자**도 **참여자**를 변경할 수 있는 권한은 없습니다. 명시적 설정에 대해 지원 되는 유일한 값은 **참가자** 됩니다 &lt;Everyone&gt;: **참가자** 항목을 만든 사용자로만 설정할 수 있습니다 또는 &lt;Everyone&gt;합니다.
> 
> 

### <a name="examples"></a>예
**항목을 게시할 때 참여자를 &lt;Everyone&gt;으로 설정합니다.**
특수 보안 주체 &lt;Everyone&gt;에는 objectId "00000000-0000-0000-0000-000000000201"이 있습니다.
  **POST** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> 일부 HTTP 클라이언트 구현은 302에 대한 응답으로 요청을 다시 보낼 수 있지만, 일반적으로 요청의 권한 부여 헤더를 제거합니다. Azure Data Catalog에 요청을 보내려면 권한 부여 헤더가 필요하기 때문에 Azure Data Catalog가 지정한 리디렉션 위치로 요청을 다시 보낼 때 권한 부여 헤더가 계속 제공되어야 합니다. 다음은 .NET HttpWebRequest 개체를 사용하여 이를 보여 주는 샘플 코드입니다.
> 
> 

**본문**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **소유자를 할당 하 고 기존 루트 항목에 대 한 표시 여부를 제한**: **PUT** https:\//api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> PUT에서 본문에는 항목 페이로드를 지정할 필요가 없습니다 것: PUT만 역할 및/또는 권한을 업데이트를 사용할 수 있습니다.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
