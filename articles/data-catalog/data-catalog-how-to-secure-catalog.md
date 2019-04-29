---
title: Azure Data Catalog에 대한 액세스를 보호하는 방법
description: 이 문서에서는 데이터 카탈로그 및 해당 데이터 자산을 보호하는 방법을 설명합니다.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 6c09b509399647f4cacbc96427200da5a1b00ac9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61000793"
---
# <a name="how-to-secure-access-to-data-catalog-and-data-assets"></a>데이터 카탈로그 및 데이터 자산에 대한 액세스를 보호하는 방법
> [!IMPORTANT]
> 이 기능은 Azure Data Catalog 표준 버전에서만 사용할 수 있습니다.

Azure Data Catalog에서 데이터 카탈로그에 액세스할 수 있는 사용자 및 이 사용자가 카탈로그의 메타데이터에 대해 수행할 수 있는 작업(등록, 주석 달기, 소유권 가져오기)을 지정할 수 있습니다. 

## <a name="catalog-users-and-permissions"></a>카탈로그 사용자 및 사용 권한
사용자나 그룹에 데이터 카탈로그에 대한 액세스 권한을 제공하고 사용 권한을 설정하는 방법은 다음과 같습니다.

1. [데이터 카탈로그 홈페이지](https://www.azuredatacatalog.com)에서 도구 모음의 **설정**을 클릭합니다.

    ![데이터 카탈로그 - 설정](media/data-catalog-how-to-secure-catalog/data-catalog-settings.png)
2. [설정] 페이지에서 **카탈로그 사용자** 섹션을 확장합니다.
    ![카탈로그 사용자 - 추가](media/data-catalog-how-to-secure-catalog/data-catalog-add-button.png)
3. **추가**를 클릭합니다.
4. 정규화된 **사용자 이름**이나 카탈로그와 연결된 AAD(Azure Active Directory)의 **보안 그룹** 이름을 입력합니다. 사용자나 그룹을 둘 이상 추가하는 경우 쉼표(`,’)를 구분 기호로 사용합니다.
    ![카탈로그 사용자 - 사용자 또는 그룹](media/data-catalog-how-to-secure-catalog/data-catalog-users-groups.png)
5. **Enter** 키나 **Tab** 키를 눌러 텍스트 상자 밖으로 나옵니다. 
6.  모든 사용 권한(**주석 달기**, **등록** 및 **소유권 가져오기**)이 이러한 사용자나 그룹에 기본적으로 할당되어 있는지 확인합니다. 즉, 사용자나 그룹이 [데이터 자산을 등록]( data-catalog-how-to-register.md)하고, [데이터 자산에 주석을 달고]( data-catalog-how-to-annotate.md), [데이터 자산에 대한 소유권을 가져올]( data-catalog-how-to-manage.md) 수 있습니다. 
    ![카탈로그 사용자 - 기본 사용 권한](media/data-catalog-how-to-secure-catalog/data-catalog-default-permissions.png)
7.  사용자 또는 그룹에 카탈로그에 대한 읽기 권한만 부여하려면 사용자나 그룹의 **주석 달기** 옵션을 선택 취소합니다. 그러면 사용자나 그룹이 카탈로그의 데이터 자산에 주석을 달 수 없지만 볼 수는 있습니다. 
8.  사용자 또는 그룹이 데이터 자산을 등록하지 못하게 하려면 해당 사용자나 그룹의 **등록** 옵션을 선택 취소합니다.
9.  사용자가 데이터 자산의 소유권을 가져오지 못하게 하려면 해당 사용자나 그룹의 **소유권 가져오기** 옵션을 선택 취소합니다. 
10. 카탈로그 사용자에서 사용자/그룹을 삭제하려면 목록 아래쪽에서 사용자/그룹에 대해 **x**를 클릭합니다. 
    ![카탈로그 사용자 - 사용자 삭제](media/data-catalog-how-to-secure-catalog/data-catalog-delete-user.png)

    > [!IMPORTANT]
    > 사용자를 직접 추가하고 사용 권한을 할당하기보다는 카탈로그 사용자에 보안 그룹을 추가하는 것이 좋습니다. 그런 다음, 사용자의 역할과 사용자에게 필요한 카탈로그에 대한 액세스 권한과 일치하는 보안 그룹에 사용자를 추가합니다.

## <a name="special-considerations"></a>특별 고려 사항

- 보안 그룹에 할당되는 권한은 추가됩니다. 예를 들어 사용자가 두 그룹에 속한다고 가정합니다. 한 그룹에는 주석을 다는 권한이 있고 다른 그룹에는 이 권한이 없다면, 사용자는 주석을 달 수 있는 권한이 있습니다. 
- 사용자에게 명시적으로 할당된 사용 권한이 사용자가 속한 그룹에 할당된 사용 권한을 재정의합니다. 앞의 예에서 사용자를 카탈로그 사용자에 명시적으로 추가했고 주석 달기 권한은 할당하지 않는다고 가정합니다. 이 경우 사용자는 주석 달기 권한이 있는 그룹의 구성원이지만 데이터 자산에 주석을 달 수 없습니다.

## <a name="next-steps"></a>다음 단계
- [Azure Azure Data Catalog 시작](data-catalog-get-started.md)

