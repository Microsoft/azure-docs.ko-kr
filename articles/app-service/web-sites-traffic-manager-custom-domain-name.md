---
title: Configure Traffic Manager를 사용하는 앱의 DNS 이름 구성 - Azure App Service
description: 부하 분산을 위해 Traffic Manager를 포함하는 Azure App Service의 웹 앱에 대한 사용자 지정 도메인 이름을 사용합니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b207d9e3123245e5cb7dddb625f7488886591ae5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128534"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Traffic Manager를 사용하는 Azure App Service의 웹앱에 대한 사용자 지정 도메인 이름 구성
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

이 문서에서는 부하 분산을 위해 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)와 통합된 [App Service](overview.md) 앱에서 사용자 지정 도메인 이름을 사용하는 일반 지침을 제공합니다.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>DNS 레코드 이해
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>표준 모드에 대해 웹 앱 구성
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>사용자 지정 도메인에 대한 DNS 레코드 추가
> [!NOTE]
> Azure App Service Web Apps를 통해 도메인을 구입한 경우 다음 단계를 생략하고 [Web Apps 도메인 구입](manage-custom-dns-buy-domain.md) 문서의 최종 단계를 참조하세요.
> 
> 

사용자 지정 도메인과 Azure App Service의 웹앱을 연결하려면 사용자 지정 도메인의 DNS 테이블에 새 항목을 추가해야 합니다. 도메인 공급자에서 관리 도구를 사용하여 이 작업을 수행합니다.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

각각 도메인 공급자에 대한 구체적인 설명은 다르지만 사용자 지정 도메인 이름(예: **contoso.com**)*에서* 웹앱과 통합된 Traffic Manager 도메인 이름(**contoso.trafficmanager.net**)*으로* 매핑합니다.

> [!NOTE]
> 레코드를 이미 사용 중이고 앱을 우선적으로 바인딩해야 하는 경우 추가 CNAME 레코드를 만들 수 있습니다. 예를 들어 우선적으로 바인딩해야 하 **www\.contoso.com** 웹 앱에 CNAME 레코드를 만듭니다 **awverify.www** 하 **contoso.trafficmanager.net**합니다. 추가할 수 있습니다 "www\.contoso.com"을 "www" CNAME 레코드를 변경 하지 않고 웹 앱입니다. 자세한 내용은 [사용자 지정 도메인에서 웹앱에 대한 DNS 레코드 만들기][CREATEDNS]를 참조하세요.

도메인 공급자에서 DNS 레코드를 추가하거나 수정했다면 변경 내용을 저장합니다.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager 사용
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Node.js 개발자 센터](https://azure.microsoft.com/develop/nodejs/)를 참조하세요.

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
