---
title: Traffic Manager를 사용하여 트래픽 제어 - Azure App Service
description: 이 문서에서는 Azure App Service와 관련된 Azure Traffic Manager의 요약 정보를 제공합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 79fe3bce558a8315f5fbf7dbc82a4979e8e24238
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831982"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Azure Traffic Manager를 사용하여 Azure App Service 트래픽 제어
> [!NOTE]
> 이 문서에서는 Azure App Service와 관련된 Microsoft Azure Traffic Manager의 요약 정보를 제공합니다." Microsoft Azure Traffic Manager 자체에 대한 추가 정보는 이 문서의 끝 부분에 있는 링크를 방문하면 찾아볼 수 있습니다.
> 
> 

## <a name="introduction"></a>소개
Azure Traffic Manager를 사용하면 웹 클라이언트의 요청을 Azure App Service의 앱에 분산하는 방법을 제어할 수 있습니다. App Service 엔드포인트를 Azure Traffic Manager 프로필에 추가하는 경우 Azure Traffic Manager가 App Service의 상태(실행 중, 중지됨 또는 삭제됨)를 계속 추적하므로 트래픽을 수신할 엔드포인트를 결정할 수 있습니다.

## <a name="routing-methods"></a>라우팅 방법
Azure Traffic Manager는 4가지의 다른 라우팅 방법을 사용합니다. 이러한 방법은 Azure App Service와 관련된 다음 목록에서 설명합니다.

* **[우선 순위](../traffic-manager/traffic-manager-routing-methods.md#priority):** 모든 트래픽에 대해 기본 앱을 사용하고 기본 또는 백업 앱을 사용할 수 없는 경우 백업을 제공합니다.
* **[가중](../traffic-manager/traffic-manager-routing-methods.md#weighted):** 앱 집합에 균일하게 또는 정의한 가중치에 따라 트래픽을 분산합니다.
* **[성능](../traffic-manager/traffic-manager-routing-methods.md#performance):** 앱이 여러 지리적 위치에 있을 때 가장 낮은 네트워크 지연 시간을 기준으로 "가장 가까운" 앱을 사용합니다.
* **[지리적](../traffic-manager/traffic-manager-routing-methods.md#geographic):** DNS 쿼리가 시작된 지리적 위치에 따라 특정 앱으로 사용자를 전송합니다. 

자세한 내용은 [Traffic Manager 라우팅 방법](../traffic-manager/traffic-manager-routing-methods.md)을 참조하세요.

## <a name="app-service-and-traffic-manager-profiles"></a>App Service 및 Traffic Manager 프로필
App Service 앱 트래픽 제어를 구성 하려면 Azure Traffic Manager의 네 가지 중 부하 분산 방법 사용 앞에서 설명한 프로필을 만들고 (이 예제의 경우 App Service)에 대 한 트래픽을 제어 하려는 끝점을 추가 합니다 프로필입니다. 앱 상태(실행 중, 중지됨 또는 삭제됨)가 정기적으로 프로필에 전달되므로 Azure Traffic Manager가 상태에 따라 트래픽을 보낼 수 있습니다.

Azure에서 Azure Traffic Manager를 사용할 경우 다음 사항에 유의하십시오.

* 동일한 지역 내에서 앱 전용 배포의 경우 App Service는 앱 모드와 상관없이 장애 조치(failover) 및 라운드 로빈 기능을 이미 제공합니다.
* 동일한 지역에서 다른 Azure 클라우드 서비스와 함께 App Service를 사용하는 배포의 경우 엔드포인트의 두 유형을 결합하여 하이브리드 시나리오를 사용할 수 있습니다.
* 프로필에서 지역당 하나의 App Service 엔드포인트만 지정할 수 있습니다. 한 지역의 엔드포인트로 하나의 앱을 선택한 경우 해당 지역의 나머지 앱은 해당 프로필에 대해 선택할 수 없게 됩니다.
* Azure Traffic Manager 프로필에서 지정한 App Service 엔드포인트는 프로필에서 앱에 대한 구성 페이지의 **도메인 이름** 섹션에 표시되지만 이 섹션에서 구성할 수 없습니다.
* 프로필에 앱을 추가한 후 사이트를 설정한 경우 앱의 포털 페이지의 대시보드에 있는 **사이트 URL**은 해당 앱의 사용자 지정 도메인 URL을 표시합니다. 그렇지 않으면 Traffic Manager 프로필 URL(예: `contoso.trafficmanager.net`)을 표시합니다. 앱의 직접적인 도메인 이름과 Traffic Manager URL은 모두 앱 구성 페이지의 **도메인 이름** 섹션에 표시됩니다.
* 사용자 지정 도메인 이름이 예상대로 작동하지만 앱에 해당 이름을 추가하는 작업 외에도 DNS 맵이 Traffic Manager URL을 가리키도록 구성해야 합니다. App Service 앱에 대해 사용자 지정 도메인을 설정하는 방법에 대한 정보는 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)을 참조하세요.
* Standard 또는 Premium 모드의 앱만 Azure Traffic Manager 프로필에 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계
Azure Traffic Manager의 개념 및 기술 개요에 대해서는 [Traffic Manager 개요](../traffic-manager/traffic-manager-overview.md)를 참조하십시오.

App Service에서 Traffic Manager를 사용하는 방법에 대한 자세한 내용은 블로그 게시물 [Azure 웹 사이트에서 Azure Traffic Manager 사용](https://blogs.msdn.com/b/waws/archive/2014/03/18/using-windows-azure-traffic-manager-with-waws.aspx) 및 [이제 Azure Traffic Manager를 Azure 웹 사이트와 통합할 수 있음](https://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/)을 참조하세요.

