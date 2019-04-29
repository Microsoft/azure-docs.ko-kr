---
title: Azure API Management에서 그룹을 사용하여 개발자 계정 관리 | Microsoft Docs
description: Azure API Management에서 그룹을 사용하여 개발자 계정을 관리하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658440"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Azure API Management에서 개발자 계정을 관리하는 그룹을 만들고 사용하는 방법

API Management에서 그룹은 개발자에 대한 제품 표시 여부를 관리하는 데 사용됩니다. 제품이 먼저 그룹에 표시된 다음, 이러한 그룹의 개발자가 그룹과 연결된 제품을 보고 구독할 수 있습니다. 

API Management에는 다음과 같은 변경할 수 없는 시스템 그룹이 있습니다.

* **관리자** - Azure 구독 관리자가 이 그룹의 구성원입니다. 관리자는 API Management 서비스 인스턴스를 관리하며 개발자가 사용하는 API, 작업 및 제품을 만듭니다.
* **개발자** - 인증된 개발자 포털 사용자가 이 그룹에 속합니다. 개발자는 API를 사용하여 애플리케이션을 빌드하는 고객입니다. 개발자는 개발자 포털에 액세스할 수 있는 권한을 받으며 API의 작업을 호출하는 애플리케이션을 빌드합니다.
* **게스트** - API Management 인스턴스의 개발자 포털을 방문하는 인증되지 않은 개발자 포털 사용자(예: 잠재 고객)가 이 그룹에 속합니다. 예를 들어 API를 볼 수 있지만 호출할 수는 없는 기능과 같이 특정 읽기 전용 액세스 권한을 받을 수 있습니다.

이러한 시스템 그룹 외에도 관리자는 사용자 지정 그룹을 만들거나 [연관된 Azure Active Directory 테넌트에서 외부 그룹을 가져올 수 있습니다][leverage external groups in associated Azure Active Directory tenants]. 사용자 지정 및 외부 그룹은 시스템 그룹과 함께 사용되어 개발자에게 API 제품에 대한 표시 여부 및 액세스를 제공합니다. 예를 들어, 특정 파트너 조직과 관련된 개발자를 위한 하나의 사용자 지정 그룹을 만들고 관련 API만을 포함한 제품에서 API에 대한 액세스를 허용합니다. 사용자는 두 그룹 이상의 구성원이 될 수 있습니다.

이 가이드에서는 API Management 인스턴스의 관리자가 새 그룹을 추가하고 이 그룹과 새 제품 및 개발자를 연결하는 방법을 보여 줍니다.

게시자 포털에서 그룹 만들기 및 관리 외에도 API Management REST API [그룹](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) 엔터티를 사용하여 그룹을 만들고 관리할 수 있습니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>필수 조건

이 문서의 작업을 완료 합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>그룹 만들기

이 섹션에서는 API Management 계정에 새 그룹을 추가하는 방법을 보여 줍니다.

1. 화면 왼쪽의 **그룹** 탭을 선택합니다.
2. **+추가**를 클릭합니다.
3. 그룹의 고유한 이름 및 선택적 설명을 입력합니다.
4. **만들기**를 누릅니다.

    ![새 그룹 추가](./media/api-management-howto-create-groups/groups001.png)

그룹이 만들어지면 **그룹** 목록에 추가됩니다. <br/>그룹의 **이름** 또는 **설명**을 편집하려면 그룹 이름 및 **설정**을 클릭합니다.<br/>그룹을 삭제하려면 그룹 이름을 클릭하고 **삭제**를 누릅니다.

그룹이 생성되었으므로, 제품 및 개발자와 연결할 수 있습니다.

## <a name="associate-group-product"> </a>그룹과 제품 연결

1. 왼쪽의 **제품** 탭을 선택합니다.
2. 원하는 제품의 이름을 클릭합니다.
3. **액세스 제어**를 누릅니다.
4. **+ 그룹 추가**를 클릭합니다.

    ![그룹과 제품 연결](./media/api-management-howto-create-groups/groups002.png)
5. 추가하려는 그룹을 선택합니다.

    ![그룹과 제품 연결](./media/api-management-howto-create-groups/groups003.png)

    제품에서 그룹을 제거하려면 **삭제**를 클릭합니다.

    ![그룹 삭제](./media/api-management-howto-create-groups/groups004.png)

제품이 그룹과 연결되면 그룹의 개발자가 제품을 보고 구독할 수 있습니다.

> [!NOTE]
> Azure Active Directory 그룹을 추가하려면 [Azure API Management에서 Azure Active Directory를 사용하여 개발자 계정에 권한을 부여하는 방법](api-management-howto-aad.md)을 참조하세요.

## <a name="associate-group-developer"> </a>그룹과 개발자 연결

이 섹션에서는 그룹과 구성원을 연결하는 방법을 보여 줍니다.

1. 화면 왼쪽의 **그룹** 탭을 선택합니다.
2. **구성원**을 선택합니다.

    ![멤버 추가](./media/api-management-howto-create-groups/groups005.png)
3. **+추가**를 누르고 구성원을 선택합니다.

    ![멤버 추가](./media/api-management-howto-create-groups/groups006.png)
4. **선택**을 누릅니다.

개발자와 그룹 간의 연결을 추가한 후에는 **사용자** 탭에서 확인할 수 있습니다.

## <a name="next-steps"> </a>다음 단계

* 그룹에 개발자를 추가하면 개발자가 해당 그룹과 연결된 제품을 보고 구독할 수 있습니다. 자세한 내용은 [Azure API Management에서 제품을 만들고 게시하는 방법][How create and publish a product in Azure API Management]을 참조하세요.
* 게시자 포털에서 그룹 만들기 및 관리 외에도 API Management REST API [그룹](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) 엔터티를 사용하여 그룹을 만들고 관리할 수 있습니다.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
