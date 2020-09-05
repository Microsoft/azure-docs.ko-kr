---
title: App Service 계획 관리
description: 만들기, 이동, 크기 조정 및 삭제와 같은 App Service 계획을 관리 하는 다양 한 작업을 수행 하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 규모, App Service 계획, 변경, 만들기, 관리, 운영
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: bb0765a1d7934d60f787ed277dd3bd1f9bc1359b
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962947"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Azure에서 App Service 계획 관리

[Azure App Service 계획](overview-hosting-plans.md)은 App Service 앱이 실행해야 하는 리소스를 제공합니다. 이 가이드에서는 App Service 계획의 관리 방법을 보여 줍니다.

## <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

> [!TIP]
> App Service 환경이 있는 경우 [App Service 환경에서 App Service 계획 만들기](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)를 참조하세요.

빈 App Service 계획을 만들거나 앱 생성의 일부로 계획을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기**를 선택 합니다.

   ![Azure Portal에서 리소스를 만듭니다.][createResource] 

1. **새**  >  **웹 앱** 또는 다른 종류의 app service 앱을 선택 합니다.

   ![Azure Portal에서 앱을 만듭니다.][createWebApp] 

2. App Service 계획을 구성 하기 전에 **인스턴스 세부 정보** 섹션을 구성 하십시오. **게시** 및 **운영** 체제와 같은 설정은 App Service 요금제에 대 한 사용 가능한 가격 책정 계층을 변경할 수 있습니다. **지역은** App Service 요금제가 생성 되는 위치를 결정 합니다. 
   
3. **App Service 계획** 섹션에서 기존 계획을 선택 하거나 **새로 만들기**를 선택 하 여 계획을 만듭니다.

   ![App Service 계획을 만듭니다.][createASP] 

4. 계획을 만들 때 새 계획의 가격 책정 계층을 선택할 수 있습니다. **Sku 및 크기**에서 **크기 변경** 을 선택 하 여 가격 책정 계층을 변경 합니다. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>다른 App Service 계획으로 앱 이동

원본 계획과 대상 계획이 _동일한 리소스 그룹 및 지리적 지역_에 있으면 다른 App Service 계획으로 앱을 이동할 수 있습니다.

> [!NOTE]
> Azure는 각각의 새 App Service 계획을 내부적으로 웹 공간이라는 배포 단위에 배포합니다. 각 지역에는 많은 웹 공간이 있을 수 있지만 앱은 동일한 웹 공간에서 만들어진 계획 간에만 이동할 수 있습니다. App Service Environment는 격리된 웹 공간이므로 앱은 동일한 App Service Environment의 계획 간에 이동할 수 있지만 서로 다른 App Service Environment의 계획 간에는 이동할 수 없습니다.
>
> 계획을 만들 때 원하는 웹 공간을 지정할 수 없지만 계획이 기존 계획과 동일한 웹 공간에 만들어졌는지 확인할 수 있습니다. 간단히, 동일한 리소스 그룹 및 지역 조합으로 만들어진 모든 계획은 동일한 웹 공간에 배포됩니다. 예를 들어, 리소스 그룹 A 및 지역 B에 계획을 만든 경우 이후에 리소스 그룹 A 및 지역 B에서 만드는 모든 계획은 동일한 웹 공간에 배포됩니다. 계획이 만들어진 후에는 계획을 이동할 수 없으므로, 계획을 또 다른 리소스 그룹으로 이동해도 이 계획을 또 다른 계획으로 “동일한 웹 공간”에 이동할 수는 없습니다.
> 

1. [Azure Portal](https://portal.azure.com)에서 **app services** 를 검색 하 고 선택 하 여 이동 하려는 앱을 선택 합니다.

2. 왼쪽 메뉴에서 **App Service 계획 변경**을 선택 합니다.

3. **App Service 계획** 드롭다운에서 앱을 이동할 기존 계획을 선택 합니다. 드롭다운에는 현재 App Service 계획과 동일한 리소스 그룹 및 지역에 있는 계획만 표시 됩니다. 이러한 계획이 없는 경우 기본적으로 계획을 만들 수 있습니다. **새로 만들기**를 선택 하 여 새 계획을 수동으로 만들 수도 있습니다.

4. 계획을 만드는 경우 새 계획의 가격 책정 계층을 선택할 수 있습니다. **가격 책정 계층**에서 기존 계층을 선택 하 여 변경 합니다. 
   
   > [!IMPORTANT]
   > 더 높은 계층의 계획에서 하위 계층의 계획으로 앱을 이동 하는 경우 (예: **D1** 에서 **F1**로) 앱은 대상 계획의 특정 기능을 잃을 수 있습니다. 예를 들어 앱에서 TLS/SSL 인증서를 사용 하는 경우 다음 오류 메시지가 표시 될 수 있습니다.
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. 작업을 마쳤으면 **확인**을 선택합니다.
   
   ![App Service 계획 선택기입니다.][change] 

## <a name="move-an-app-to-a-different-region"></a>다른 영역으로 앱 이동

앱이 실행되는 영역은 App Service 계획이 있는 영역입니다. 그러나 App Service 계획의 영역은 변경할 수 없습니다. 앱을 다른 영역으로 이동하는 한 가지 대안은 앱 복제입니다. 복제를 수행하면 모든 하위 지역의 새로운 또는 기존 App Service 계획으로 앱이 복사됩니다.

메뉴의 **개발 도구** 섹션에서 **앱 복제**를 찾을 수 있습니다.

> [!IMPORTANT]
> 복제에는 몇 가지 제한 사항이 있습니다. [Azure App Service 앱 복제](app-service-web-app-cloning.md)에서 이에 대한 내용을 확인할 수 있습니다.

## <a name="scale-an-app-service-plan"></a>App Service 계획 크기 조정

App Service 계획의 가격 책정 계층을 강화하려면 [Azure에서 앱 강화](manage-scale-up.md)를 참조하세요.

앱 인스턴스 수를 규모 확장하려면 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md)을 참조하세요.

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>App Service 계획 삭제

예기치 않은 요금 청구를 방지하기 위해 App Service 계획에서 마지막 앱을 삭제하면 App Service도 기본적으로 해당 계획을 삭제합니다. 그 대신 계획을 유지하려는 경우 계획을 **무료** 계층으로 변경하면 요금이 청구되지 않습니다.

> [!IMPORTANT]
> 연결된 앱이 없는 App Service 계획이더라도 구성된 VM 인스턴스가 지속적으로 유지되기 때문에 요금이 부과됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure에서 앱 강화](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png