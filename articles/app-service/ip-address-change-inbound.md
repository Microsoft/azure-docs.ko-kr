---
title: 인바운드 IP 주소 변경 준비 - Azure App Service
description: 인바운드 IP 주소를 변경하려는 경우 앱이 변경 후 작업을 계속할 수 있도록 수행할 작업에 대해 알아봅니다.
services: app-service\web
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 06/28/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: aaa89b5a3bb1af6878ed21e0160a534a1c989228
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270067"
---
# <a name="how-to-prepare-for-an-inbound-ip-address-change"></a>인바운드 IP 주소 변경 준비하는 방법

Azure App Service 앱의 인바운드 IP 주소가 변경된다는 알림을 받은 경우 이 문서의 지침을 따릅니다.

## <a name="determine-if-you-have-to-do-anything"></a>어떤 작업을 수행해야 하는 경우 확인

* 옵션 1: App Service 앱에 사용자 지정 도메인이 없는 경우 작업이 필요하지 않습니다.

* 옵션 2: CNAME 레코드(URI를 가리키는 DNS 레코드)만 도메인 등록 포털(타사 DNS 공급자 또는 Azure DNS)에서 구성되는 경우 작업이 필요하지 않습니다.

* 옵션 3: A 레코드(IP 주소를 직접 가리키는 DNS 레코드)가 도메인 등록 포털(타사 DNS 공급자 또는 Azure DNS)에서 구성되는 경우 기존 IP 주소를 새 주소로 바꿉니다. 다음 섹션의 지침을 따르면 새 IP 주소를 찾을 수 있습니다.

* 옵션 4: 애플리케이션이 Load Balancer, IP 필터 또는 앱의 IP 주소를 요구하는 다른 모든 IP 메커니즘의 배후에 있는 경우 기존 IP 주소를 새 주소로 바꿉니다. 다음 섹션의 지침을 따르면 새 IP 주소를 찾을 수 있습니다.

## <a name="find-the-new-inbound-ip-address-in-the-azure-portal"></a>Azure Portal에서 새 인바운드 IP 주소 찾기

앱에 지정되는 새 인바운드 IP 주소는 **가상 IP 주소** 필드의 포털에 있습니다. 이제 이 새 IP 주소와 기존 주소 모두 앱에 연결되고 나서 나중에 기존 주소의 연결이 끊어집니다.

1.  [Azure Portal](https://portal.azure.com)을 엽니다.

2.  왼쪽 탐색 메뉴에서 **App Service**를 선택합니다.

3.  목록에서 App Service 앱을 선택합니다.

1.  앱이 함수 앱인 경우 [함수 앱 인바운드 IP 주소](../azure-functions/ip-addresses.md#function-app-inbound-ip-address)를 참조하세요.

4.  **설정** 헤더 아래의 왼쪽 탐색에서 **속성**을 클릭하고 **가상 IP 주소**라는 섹션을 찾습니다.

5. IP 주소를 복사하고 도메인 레코드 또는 IP 메커니즘을 다시 구성합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure에서 시작한 IP 주소 변경을 준비하는 방법을 설명합니다. Azure App Service의 IP 주소에 대한 자세한 내용은 [Azure App Service에서 인바운드 및 아웃바운드 IP 주소](overview-inbound-outbound-ips.md)를 참조하세요.
