---
title: 청사진의 수명 주기 이해
description: 청사진이 거치는 수명 주기 및 각 단계의 세부 정보를 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: a57085fa37efd56a46b740d8cbc4278dc53cf39f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683192"
---
# <a name="understand-the-lifecycle-of-an-azure-blueprint"></a>Azure Blueprint의 수명 주기 이해

Azure 내의 많은 리소스와 마찬가지로 Azure Blueprint의 청사진에는 일반적이고 자연스러운 수명 주기가 있습니다. 생성, 배포되고 더 이상 필요하거나 관련되지 않은 경우 마지막으로 삭제됩니다.
Blueprints는 표준 수명 주기 작업을 지원합니다. Blueprint는 이러한 작업을 토대로 DevOps의 핵심 요소인 Infrastructure as a Code를 관리하는 조직을 위해 일반적인 연속 통합 및 지속적인 배포 파이프라인을 지원하는 추가 상태 수준을 제공하도록 작성됩니다.

청사진 및 단계를 완벽하게 이해하기 위해 표준 수명 주기를 설명하겠습니다.

> [!div class="checklist"]
> - 청사진 만들기 및 편집
> - 청사진 게시
> - 청사진의 새 버전 만들기 및 편집
> - 청사진의 새 버전 게시
> - 특전 버전의 청사진 삭제
> - 청사진 삭제

## <a name="creating-and-editing-a-blueprint"></a>청사진 만들기 및 편집

청사진을 만들 때 아티팩트를 추가하고, 관리 그룹 또는 구독에 저장하고, 고유 이름과 고유 버전을 제공합니다. 청사진은 이제 **초안** 모드에 있으며 아직 할당될 수 없습니다. **초안** 모드에 있는 동안 계속해서 업데이트 및 변경될 수 있습니다.

절대 게시되지 않는 **초안** 모드의 청사진은 **게시된** 것과 다른 아이콘을 **청사진 정의** 페이지에 표시합니다. 합니다 **최신 버전** 으로 표시 됩니다 **초안** 청사진을 게시 되지는이 대 한 합니다.

[Azure Portal](../create-blueprint-portal.md#create-a-blueprint) 또는 [REST API](../create-blueprint-rest-api.md#create-a-blueprint)를 사용하여 청사진을 만들고 편집합니다.

## <a name="publishing-a-blueprint"></a>청사진 게시

**초안** 모드의 청사진에 계획된 모든 변경 내용이 적용되면 **게시**되고 할당에 사용될 수 있습니다. 청사진의 **게시된** 버전은 변경될 수 없습니다.
**게시**되면 청사진은 **초안** 청사진과 다른 아이콘으로 표시되고 **최신 버전** 열에서 제공된 버전 번호를 표시합니다.

[Azure Portal](../create-blueprint-portal.md#publish-a-blueprint) 또는 [REST API](../create-blueprint-rest-api.md#publish-a-blueprint)를 사용하여 청사진을 게시합니다.

## <a name="creating-and-editing-a-new-version-of-the-blueprint"></a>청사진의 새 버전 만들기 및 편집

청사진의 **게시된** 버전은 변경될 수 없습니다. 그렇지만 새 버전의 청사진을 기존 청사진에 추가하고 필요에 따라 수정할 수 있습니다. 기존 청사진을 편집하여 변경합니다. 새 변경 내용이 저장되면 이제 청사진은 **게시되지 않은 변경 내용**을 포함합니다. 이러한 변경 내용은 청사진의 새 **초안** 버전이 됩니다.

[Azure Portal](../create-blueprint-portal.md#edit-a-blueprint)을 사용하여 청사진을 편집합니다.

## <a name="publishing-a-new-version-of-the-blueprint"></a>청사진의 새 버전 게시

편집된 각 청사진을 할당하려면 먼저 **게시**해야 합니다. **게시되지 않은 변경 내용**이 청사진에 적용되었지만 아직 **게시**되지 않은 경우 **청사진 게시** 단추를 청사진 편집 페이지에서 사용할 수 있습니다. 단추가 보이지 않는 경우 청사진은 이미 **게시**되었으며 **게시되지 않은 변경 내용**이 없는 것입니다.

> [!NOTE]
> 하나의 청사진에는 각각 구독에 할당될 수 있는 여러 개의 **게시된** 버전이 있을 수 있습니다.

**게시되지 않은 변경 내용**이 있는 청사진을 게시하려면 새 청사진을 게시할 때와 동일한 단계를 사용합니다.

## <a name="deleting-a-specific-version-of-the-blueprint"></a>특전 버전의 청사진 삭제

청사진의 각 버전은 고유한 개체이며 개별적으로 **게시**될 수 있습니다. 이와 같이 청사진의 각 버전을 삭제할 수도 있습니다. 청사진의 버전을 삭제하는 것은 해당 청사진의 다른 버전에 영향을 주지 않습니다.

> [!NOTE]
> 활성 할당이 있는 청사진을 삭제하는 것은 불가능합니다. 먼저 할당을 삭제한 다음, 제거하려는 버전을 삭제합니다.

1. 왼쪽 창에서 **모든 서비스**를 선택합니다. **청사진**을 검색하고 선택합니다.

1. 선택 **Blueprint 정의** 페이지에서 왼쪽 및 버전을 삭제 하려는 청사진을 찾으려고 필터 옵션을 사용 합니다. 편집 페이지를 열려면 클릭합니다.

1. **게시된 버전** 탭을 클릭하고 삭제하려는 버전을 찾습니다.

1. 삭제 및 선택 버전을 마우스 오른쪽 단추로 클릭 **이 버전을 삭제**합니다.

## <a name="deleting-the-blueprint"></a>청사진 삭제

코어 청사진을 삭제할 수도 있습니다. 코어 청사진을 삭제하면 **초안** 또는 **게시됨** 청사진을 포함하여 해당 청사진의 모든 청사진 버전도 삭제됩니다. 청사진의 버전을 삭제하는 것과 마찬가지로 코어 청사진을 삭제해도 청사진 버전의 기존 할당이 제거되지 않습니다.

> [!NOTE]
> 활성 할당이 있는 청사진을 삭제하는 것은 불가능합니다. 먼저 할당을 삭제한 다음, 제거하려는 버전을 삭제합니다.

[Azure Portal](../create-blueprint-portal.md#delete-a-blueprint) 또는 [REST API](../create-blueprint-rest-api.md#delete-a-blueprint)를 사용하여 청사진을 삭제합니다.

## <a name="assignments"></a>할당

수명 주기 동안 청사진을 구독에 할당할 수 있는 여러 지점이 있습니다. 청사진의 버전 모드가 **게시**되면 해당 버전을 구독에 할당할 수 있습니다. 이 수명 주기를 통해 새 버전이 개발되는 동안 청사진의 버전을 사용하고 적극적으로 할당할 수 있습니다.

청사진의 버전이 할당되면 할당된 위치 및 할당되는 데 사용하는 매개 변수를 이해해야 합니다. 매개 변수는 정적이거나 동적일 수 있습니다. 자세히 알아보려면 [정적 및 동적 매개 변수](parameters.md)를 참조하세요.

### <a name="updating-assignments"></a>할당 업데이트

청사진이 할당된 경우 할당을 업데이트할 수 있습니다. 다음을 포함하여 기존 할당을 업데이트하는 여러 가지 이유가 있습니다.

- [리소스 잠금](resource-locking.md) 추가 또는 제거
- [동적 매개 변수](parameters.md#dynamic-parameters) 값 변경
- 할당을 최근에 **게시된** 버전의 청사진으로 업그레이드

방법을 알아보려면 [기존 할당 업데이트](../how-to/update-existing-assignments.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [정적 및 동적 매개 변수](parameters.md) 사용 방법 이해
- [청사진 시퀀싱 순서](sequencing-order.md)를 사용자 지정하는 방법 알아보기
- [청사진 리소스 잠금](resource-locking.md)을 활용하는 방법 알아보기
- [기존 할당을 업데이트](../how-to/update-existing-assignments.md)하는 방법 알아보기
- [일반 문제 해결 방법](../troubleshoot/general.md)을 통해 청사진 할당 중에 발생하는 문제 해결