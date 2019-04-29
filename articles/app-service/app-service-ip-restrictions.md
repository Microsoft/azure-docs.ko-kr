---
title: 클라이언트 IP 제한 - Azure App Service | Microsoft Docs
description: Azure App Service를 사용 하 여 액세스 제한을 사용 하는 방법
author: ccompy
manager: stefsch
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/30/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: bb6ab29f02282a394e3f93e41682ceaec5208b75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60853282"
---
# <a name="azure-app-service-static-access-restrictions"></a>Azure App Service에 정적 액세스 제한 #

액세스 제한을 사용 하면 앱에 액세스할 수 있는 IP 주소의 허용/거부 목록 정렬 우선 순위를 정의할 수 있습니다. 허용 목록에는 IPv4 및 IPv6 주소가 포함될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 암시적 모두 거부가 표시됩니다.

App Service는 다음과 같습니다. 작업 부하를 호스팅된 작동 액세스 제한 기능 web apps, API 앱, Linux 앱, Linux 컨테이너 앱 및 함수.

앱에 대 한 요청이 이루어지면에서 IP 주소 액세스 제한 목록에 대해 계산 됩니다. 주소가 목록의 규칙을 기준으로 허용되지 않을 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 답변합니다.

액세스 제한 기능을 App Service 업스트림 작업자 호스트 프로그램 코드가 실행 되는 프런트 엔드 역할에서 구현 됩니다. 따라서 액세스 제한을 효과적으로 네트워크 Acl 됩니다.  

![액세스 제한 흐름](media/app-service-ip-restrictions/ip-restrictions-flow.png)

한 번에 대 한 포털에서 액세스 제한 기능 위에 IIS에서 ipSecurity 기능 계층을 했습니다. 현재 액세스 제한 기능은 다릅니다. IpSecurity 응용 프로그램 web.config 파일 내에서 구성할 수 있습니다 하지만 모든 트래픽을 IIS에 도달 하기 전에 프런트 엔드를 따라 액세스 제한 규칙이 적용 됩니다.

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>추가 하 고 포털에서 액세스 제한 규칙 편집 ##

앱에 액세스 제한 규칙 추가 하려면 메뉴 사용 하 여 열려는 **네트워크**>**액세스 제한** 클릭 하 고 **액세스 제한 구성**

![App Service 네트워킹 옵션](media/app-service-ip-restrictions/ip-restrictions.png)  

액세스 제한 사항 UI에서 앱에 대해 정의 된 액세스 제한 규칙 목록을 검토할 수 있습니다.

![목록 액세스 제한](media/app-service-ip-restrictions/ip-restrictions-browse.png)

규칙이 이 이미지와 같이 구성된 경우 앱은 131.107.159.0/24의 트래픽만 수락하며, 다른 IP 주소의 트래픽은 거부됩니다.

클릭할 수 있습니다 **[+] 추가** 새 액세스 제한 규칙을 추가 합니다. 규칙을 추가하면 즉시 적용됩니다. 규칙은 가장 작은 번호부터 우선적으로 적용됩니다. 단일 규칙을 추가해도 암시적 모두 거부가 적용됩니다.

![액세스 제한 규칙 추가](media/app-service-ip-restrictions/ip-restrictions-add.png)

IP 주소 표기법은 IPv4 및 IPv6 주소 둘 다에 대해 CIDR 표기법으로 지정해야 합니다. 정확한 주소를 지정하려면 1.2.3.4/32와 같이 처음 4개의 8진수로 IP 주소를 나타내고 /32로 마스크를 나타낼 수 있습니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 대한 자세한 내용은 [Classless Inter-domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요.  

기존 액세스 제한 규칙을 편집 하려면 모든 행을 클릭할 수 있습니다. 우선 순위 변경을 비롯한 편집 내용은 즉시 적용됩니다.

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/ip-restrictions-edit.png)

규칙을 삭제하려면 규칙에서 **...** 를 클릭하고 **제거**를 클릭합니다.

![액세스 제한 규칙 삭제](media/app-service-ip-restrictions/ip-restrictions-delete.png)

또한 다음 탭에서 배포 액세스를 제한할 수 있습니다. 각 규칙 추가/편집/삭제 하려면, 위와 동일한 단계를 따릅니다.

![목록 액세스 제한](media/app-service-ip-restrictions/ip-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>액세스 제한 규칙의 프로그래밍 방식으로 조작 ##

현재 CLI 또는 PowerShell 없습니다 새 액세스 제한 기능이 있지만 Resource Manager에서 앱 구성에서 PUT 작업을 사용 하 여 값을 수동으로 설정할 수 있습니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다.

Resource Manager에서 이 정보는 다음 위치에 제공됩니다.

management.azure.com/subscriptions/**구독 ID**/resourceGroups/**리소스 그룹**/providers/Microsoft.Web/sites/**웹앱 이름**/config/web?api-version=2018-02-01

앞의 예제에 대한 JSON 구문은 다음과 같습니다.

    "ipSecurityRestrictions": [
      {
        "ipAddress": "131.107.159.0/24",
        "action": "Allow",
        "tag": "Default",
        "priority": 100,
        "name": "allowed access"
      }
    ],

## <a name="function-app-ip-restrictions"></a>함수 앱 IP 제한

IP 제한은 동일한 App Service 계획 기능을 사용 하 여 함수 앱 모두에 대해 사용할 수 있습니다. Ip 제한은 사용 하지 않도록 허용 되지 않는 모든 Ip에 대 한 포털 코드 편집기 note 합니다.

[여기를 참조하세요](../azure-functions/functions-networking-options.md#inbound-ip-restrictions).