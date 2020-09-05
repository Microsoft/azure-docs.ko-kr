---
title: Azure에서 지원 되는 FHIR 기능-FHIR 용 Azure API
description: 이 문서에서는 Azure API for FHIR에서 구현 되는 FHIR 사양의 기능을 설명 합니다.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: bdf328222fef1f763776bd26c47f5cd4d65e487e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000008"
---
# <a name="features"></a>기능

FHIR 용 azure API는 Azure 용 Microsoft FHIR 서버를 완전히 관리 하는 배포를 제공 합니다. 서버는 [Fhir](https://hl7.org/fhir) 표준의 구현입니다. 이 문서에는 FHIR 서버의 주요 기능이 나열 되어 있습니다.

## <a name="fhir-version"></a>FHIR 버전

지원 되는 최신 버전: `4.0.1`

이전 버전은 현재 다음과 같이 지원 됩니다. `3.0.2`

## <a name="rest-api"></a>REST API

| API                            | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| 읽기                           | 예       | 예       | 예       |                                                     |
| vread                          | 예       | 예       | 예       |                                                     |
| update                         | 예       | 예       | 예       |                                                     |
| 낙관적 잠금으로 업데이트 | 예       | 예       | 예       |                                                     |
| update (조건부)           | 예       | 예       | 예       |                                                     |
| 패치나                          | 아니요        | 아니요        | 아니요        |                                                     |
| delete                         | 예       | 예       | 예       |                                                     |
| delete (조건부)           | 아니요        | 아니요        | 예        |                                                     |
| create                         | 예       | 예       | 예       | POST/PUT 모두 지원                               |
| create (조건부)           | 예       | 예       | 예       |                                                     |
| 검색                         | Partial   | Partial   | Partial   | 아래 참조                                           |
| 연결 된 검색                 | 아니요        | 예       | 아니요        |                                           |
| 역방향 연결 된 검색         | 아니요        | 아니요        | 아니요        |                                            |
| capabilities                   | 예       | 예       | 예       |                                                     |
| 일괄 처리                          | 예       | 예       | 예       |                                                     |
| 트랜잭션                    | 아니요        | 예       | 아니요        |                                                     |
| history                        | 예       | 예       | 예       |                                                     |
| 페이징                         | Partial   | Partial   | Partial   | `self` 및 `next` 가 지원 됩니다.                     |
| 매개자                 | 아니요        | 아니요        | 예        |                                                     |

## <a name="search"></a>검색

모든 검색 매개 변수 유형이 지원 됩니다. 

| 검색 매개 변수 유형 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견 |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | 예       | 예       | 예       |         |
| Date/DateTime         | 예       | 예       | 예       |         |
| String                | 예       | 예       | 예       |         |
| 토큰                 | 예       | 예       | 예       |         |
| 참조             | 예       | 예       | 예       |         |
| 복합             | 예       | 예       | 예       |         |
| 수량              | 예       | 예       | 예       |         |
| URI                   | 예       | 예       | 예       |         |
| 특수               | 아니요        | 아니요        | 아니요        |         |


| 한정자             | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 주석 |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | 예       | 예       | 예       |         |
|`:exact`               | 예       | 예       | 예       |         |
|`:contains`            | 예       | 예       | 예       |         |
|`:text`                | 예       | 예       | 예       |         |
|`:in` 토큰          | 아니요        | 아니요        | 아니요        |         |
|`:below` 토큰       | 아니요        | 아니요        | 아니요        |         |
|`:above` 토큰       | 아니요        | 아니요        | 아니요        |         |
|`:not-in` 토큰      | 아니요        | 아니요        | 아니요        |         |
|`:[type]` 참조일  | 아니요        | 아니요        | 아니요        |         |
|`:below` uri         | 예       | 예       | 예       |         |
|`:not`                 | 아니요        | 아니요        | 아니요        |         |
|`:above` uri         | 아니요        | 아니요        | 아니요        | 문제 [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| 공통 검색 매개 변수 | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 주석 |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | 예       | 예       | 예       |         |
| `_lastUpdated`          | 예       | 예       | 예       |         |
| `_tag`                  | 예       | 예       | 예       |         |
| `_profile`              | 예       | 예       | 예       |         |
| `_security`             | 예       | 예       | 예       |         |
| `_text`                 | 아니요        | 아니요        | 아니요        |         |
| `_content`              | 아니요        | 아니요        | 아니요        |         |
| `_list`                 | 예       | 예       | 예       |         |
| `_has`                  | 아니요        | 아니요        | 아니요        |         |
| `_type`                 | 예       | 예       | 예       |         |
| `_query`                | 아니요        | 아니요        | 아니요        |         |

| 검색 작업       | 지원 됨-PaaS | 지원 됨-OSS (SQL) | 지원 됨-OSS (Cosmos DB) | 의견 |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | 아니요        | 아니요        | 아니요        |         |
| `_sort`                 | 아니요        | Partial   | Partial        |   `_sort=_lastUpdated`가 지원됨       |
| `_score`                | 아니요        | 아니요        | 아니요        |         |
| `_count`                | 예       | 예       | 예       |         |
| `_summary`              | Partial   | Partial   | Partial   | `_summary=count`가 지원됨 |
| `_include`              | 아니요        | 예       | 아니요        |         |
| `_revinclude`           | 아니요        | 예       | 아니요        | 포함 된 항목은 100 개로 제한 됩니다. |
| `_contained`            | 아니요        | 아니요        | 아니요        |         |
| `_elements`             | 아니요        | 아니요        | 아니요        |         |

## <a name="persistence"></a>지속성

Microsoft FHIR 서버에는 플러그형 지 속성 모듈이 있습니다 (참조 [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

현재 FHIR 서버 오픈 소스 코드는 [Azure Cosmos DB](../cosmos-db/index-overview.md) 및 [SQL Database](https://azure.microsoft.com/services/sql-database/)에 대 한 구현을 포함 합니다.

Cosmos DB는 전역적으로 분산 된 다중 모델 (SQL API, MongoDB API 등) 데이터베이스입니다. 서로 다른 [일관성 수준을](../cosmos-db/consistency-levels.md)지원 합니다. 기본 배포 템플릿은 일관성을 사용 하 여 FHIR 서버를 구성 `Strong` 하지만 요청 헤더를 사용 하 여 요청 별로 요청에서 일관성 정책을 수정할 수 있습니다 (일반적으로 완화 됨) `x-ms-consistency-level` .

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

FHIR 서버는 액세스 제어를 위해 [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 를 사용 합니다. 특히 `FhirServer:Security:Enabled` 구성 매개 변수를로 설정 하 `true` 고 `/metadata` Fhir 서버에 대 한 모든 요청 (제외)에서 `Authorization` 요청 헤더를로 설정 해야 `Bearer <TOKEN>` 하는 경우 RBAC (역할 기반 Access Control)가 적용 됩니다. 토큰은 클레임에 정의 된 하나 이상의 역할을 포함 해야 합니다 `roles` . 지정 된 리소스에 대해 지정 된 작업을 허용 하는 역할이 토큰에 포함 되어 있으면 요청이 허용 됩니다.

현재 지정 된 역할에 대해 허용 되는 작업은 API에 *전역적* 으로 적용 됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 용 Azure API에서 지원 되는 FHIR 기능에 대해 읽었습니다. 그런 다음, FHIR 용 Azure API를 배포 합니다.
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 배포](fhir-paas-portal-quickstart.md)
