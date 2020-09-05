---
title: Azure 관리 액세스에 대 한 Azure 역할 설정
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 관리에 대 한 관리 작업을 제어 하 고 위임할 Azure Portal의 RBAC (역할 기반 액세스 제어).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: f0c8fe6b8df5efef0cf3948c8d628d20c79502ff
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928686"
---
# <a name="set-azure-roles-for-administrative-access-to-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 관리 액세스를 위한 Azure 역할 설정

Azure에서는 포털 또는 Resource Manager API를 통해 관리되는 모든 서비스에 대해 [전역 역할 기반 권한 부여 모델](../role-based-access-control/role-assignments-portal.md) 을 제공합니다. 소유자, 참가자 및 읽기 권한자 역할은 사용자, 그룹 및 각 역할에 할당 된 보안 주체의 Active Directory에 대 한 *서비스 관리* 수준을 결정 합니다. 

> [!Note]
> 인덱스의 일부 또는 문서 하위 집합을 보호 하기 위한 RBAC (역할 기반 액세스 제어)는 없습니다. 검색 결과에 대한 ID 기반 액세스의 경우 보안 필터를 만들어서 ID를 기준으로 결과를 잘라 요청자에게 액세스 권한이 없어야 하는 문서를 제거할 수 있습니다. 자세한 내용은 [보안 필터](search-security-trimming-for-azure-search.md) 및 [Active Directory로 보안](search-security-trimming-for-azure-search-with-aad.md)을 참조하세요.

## <a name="management-tasks-by-role"></a>역할별 관리 작업

Azure Cognitive Search의 경우 역할은 다음 관리 작업을 지 원하는 사용 권한 수준과 연결 됩니다.

| 역할 | 작업 |
| --- | --- |
| 소유자 |api-key, 인덱스, 인덱서, 인덱서 데이터 원본 및 인덱서 일정 등 서비스 또는 해당 서비스의 개체를 만들거나 삭제합니다.<p>개수 및 스토리지 크기를 포함하여 서비스 상태를 봅니다.<p>역할 멤버 자격을 추가하거나 삭제합니다(소유자만 역할 멤버 자격을 관리할 수 있음).<p>구독 관리자 및 서비스 소유자는 소유자 역할의 자동 멤버 자격을 갖습니다. |
| 참가자 |Azure 역할 관리를 뺀 소유자와 동일한 액세스 수준입니다. 예를 들어, 참여자는 개체를 만들거나 삭제하거나, [api-keys](search-security-api-keys.md)를 보고 다시 생성할 수 있지만 역할 멤버 자격을 수정할 수는 없습니다. |
| [Search 서비스 참여자 기본 역할](../role-based-access-control/built-in-roles.md#search-service-contributor) | 참가자 역할과 동일합니다. |
| 판독기 |서비스 기본 정보 및 메트릭을 봅니다. 이 역할의 멤버는 인덱스, 인덱서, 데이터 원본 또는 주요 정보를 볼 수 없습니다.  |

역할은 서비스 엔드포인트에 대한 액세스 권한을 부여하지 않습니다. 인덱스 관리, 인덱스 채우기 및 검색 데이터 쿼리와 같은 Search 서비스 작업은 역할이 아니라 api-key를 통해 제어합니다. 자세한 내용은 [api-key 관리](search-security-api-keys.md)를 참조하세요.

## <a name="permissions-table"></a>권한 테이블

다음 표에서는 Azure Cognitive Search에서 허용 되는 작업과 특정 작업에 액세스 하는 키 잠금 해제를 요약 합니다.

| 작업(Operation) | 사용 권한 |
|-----------|-------------------------|
| 서비스 만들기 | Azure 구독 소유자 |
| 서비스 크기 조정 | 리소스의 관리 키, RBAC 소유자 또는 참가자  |
| 서비스 삭제 | 리소스의 관리 키, RBAC 소유자 또는 참가자 |
| 서비스에서 개체를 만들고, 수정하고, 삭제합니다. <br>인덱스 및 구성 요소 부분 (분석기 정의, 점수 매기기 프로필, CORS 옵션 포함), 인덱서, 데이터 원본, 동의어, 확인 기 | 리소스의 관리 키, RBAC 소유자 또는 참가자 |
| 인덱스 쿼리 | 관리자 또는 쿼리 키(RBAC 적용되지 않음) |
| 통계, 개수 및 개체 목록 반환 등의 시스템 정보 쿼리 | 관리자 키, 리소스에 대한 RBAC(소유자, 참가자, 독자) |
| 관리자 키 관리 | 관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자 |
| 쿼리 키 관리 |  관리자 키, 리소스에 대한 RBAC 소유자 또는 참가자  |

## <a name="see-also"></a>참조

+ [Powershell을 사용하여 관리](search-manage-powershell.md) 
+ [Azure Cognitive Search의 성능 및 최적화](search-performance-optimization.md)
+ [Azure Portal에서 역할 기반 Access Control를 시작](../role-based-access-control/overview.md)합니다.