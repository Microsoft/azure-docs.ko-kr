---
title: 포털에서 Azure 관리 액세스를 위해 RBAC 역할 설정 - Azure Search
description: Azure Search 관리에 대한 관리 작업을 제어 및 위임하기 위한 Azure Portal의 RBAC(역할 기반 관리 제어)입니다.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 34def35eba1e5c1645e6e1f9a505704d153ac716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282388"
---
# <a name="set-rbac-roles-for-administrative-access"></a>관리 액세스에 대한 RBAC 역할 설정

Azure에서는 포털 또는 Resource Manager API를 통해 관리되는 모든 서비스에 대해 [전역 역할 기반 권한 부여 모델](../role-based-access-control/role-assignments-portal.md) 을 제공합니다. 소유자, 참가자 및 읽기 권한자 역할은 Active Directory 사용자, 그룹 및 각 역할에 할당된 보안 주체에 대한 *서비스 관리* 수준을 결정합니다. 

> [!Note]
> 인덱스의 일부 또는 문서의 하위 집합을 보호하기 위한 역할 기반 액세스 제어가 있습니다. 검색 결과에 대한 ID 기반 액세스의 경우 보안 필터를 만들어서 ID를 기준으로 결과를 잘라 요청자에게 액세스 권한이 없어야 하는 문서를 제거할 수 있습니다. 자세한 내용은 [보안 필터](search-security-trimming-for-azure-search.md) 및 [Active Directory로 보안](search-security-trimming-for-azure-search-with-aad.md)을 참조하세요.

## <a name="management-tasks-by-role"></a>역할별 관리 작업

Azure Search의 경우 역할은 다음 관리 작업을 지원하는 사용 권한 수준과 관련이 있습니다.

| 역할 | Task |
| --- | --- |
| 소유자 |api-key, 인덱스, 인덱서, 인덱서 데이터 원본 및 인덱서 일정 등 서비스 또는 해당 서비스의 개체를 만들거나 삭제합니다.<p>개수 및 저장소 크기를 포함하여 서비스 상태를 봅니다.<p>역할 멤버 자격을 추가하거나 삭제합니다(소유자만 역할 멤버 자격을 관리할 수 있음).<p>구독 관리자 및 서비스 소유자는 소유자 역할의 자동 멤버 자격을 갖습니다. |
| 참가자 |RBAC 역할 관리를 제외하고 소유자와 같은 수준의 액세스 권한입니다. 예를 들어, 참여자는 개체를 만들거나 삭제하거나, [api-keys](search-security-api-keys.md)를 보고 다시 생성할 수 있지만 역할 멤버 자격을 수정할 수는 없습니다. |
| [Search 서비스 참여자 기본 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor) | 참가자 역할과 동일합니다. |
| 판독기 |서비스 기본 정보 및 메트릭을 봅니다. 이 역할의 멤버는 인덱스, 인덱서, 데이터 원본 또는 주요 정보를 볼 수 없습니다.  |

역할은 서비스 엔드포인트에 대한 액세스 권한을 부여하지 않습니다. 인덱스 관리, 인덱스 채우기 및 검색 데이터 쿼리와 같은 Search 서비스 작업은 역할이 아니라 api-key를 통해 제어합니다. 자세한 내용은 [api-key 관리](search-security-api-keys.md)를 참조하세요.

## <a name="see-also"></a>참고 항목

+ [Powershell을 사용하여 관리](search-manage-powershell.md) 
+ [Azure Search의 성능 및 최적화](search-performance-optimization.md)
+ [Azure Portal에서 역할 기반 액세스 제어로 시작합니다](../role-based-access-control/overview.md).