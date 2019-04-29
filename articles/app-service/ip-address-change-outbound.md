---
title: 아웃바운드 IP 주소 변경 준비 - Azure App Service
description: 아웃바운드 IP 주소를 변경하려는 경우 앱이 변경 후 작업을 계속할 수 있도록 수행할 작업에 대해 알아봅니다.
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
ms.openlocfilehash: ac62217af096653d61a79ff29ae352c8e950f8af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61269781"
---
# <a name="how-to-prepare-for-an-outbound-ip-address-change"></a>아웃바운드 IP 주소 변경 준비하는 방법

Azure App Service 앱의 아웃바운드 IP 주소가 변경된다는 알림을 받은 경우 이 문서의 지침을 따릅니다.

## <a name="determine-if-you-have-to-do-anything"></a>어떤 작업을 수행해야 하는 경우 확인

* 옵션 1: App Service 앱이 IP 필터링, 명시적 포함 목록 또는 라우팅 또는 방화벽과 같은 아웃바운드 트래픽의 특별 처리를 사용하지 않는 경우 아무런 조치도 필요하지 않습니다.

* 옵션 2: 앱이 아웃바운드 IP 주소(아래 예제 참조)에 대해 특별 처리를 하는 경우 기존 IP 주소가 표시되는 어디에나 새 아웃바운드 IP 주소를 추가합니다. 기존 IP 주소를 바꾸지 마세요. 다음 섹션의 지침을 따르면 새 아웃바운드 IP 주소를 찾을 수 있습니다.

  예를 들어 아웃 바운드 IP 주소가 앱 외부의 방화벽에 명시적으로 포함될 수 있거나, 외부 결제 서비스에 앱에 대한 아웃바운드 IP 주소를 포함하는 허용된 목록이 있을 수 있습니다. 앱 외부 어디든 목록에서 아웃바운드 주소를 구성하는 경우 변경해야 합니다.

## <a name="find-the-outbound-ip-addresses-in-the-azure-portal"></a>Azure Portal에서 아웃바운드 IP 주소 찾기

새 아웃바운드 IP 주소는 적용되기 전에 포털에 표시됩니다. Azure에서 새 주소를 사용하기 시작하면 이전 주소는 더 이상 사용되지 않습니다. 한 번에 하나의 집합만 사용되므로 스위치가 가동할 경우 중단을 방지하려면 포함 목록의 항목에 이전 및 새 IP 주소가 있어야 합니다. 

1.  [Azure Portal](https://portal.azure.com)을 엽니다.

2.  왼쪽 탐색 메뉴에서 **App Service**를 선택합니다.

3.  목록에서 App Service 앱을 선택합니다.

1.  앱이 함수 앱인 경우 [함수 앱 아웃바운드 IP 주소](../azure-functions/ip-addresses.md#find-outbound-ip-addresses)를 참조하세요.

4.  **설정** 헤더 아래의 왼쪽 탐색에서 **속성**을 클릭하고 **아웃바운드 IP 주소**라는 섹션을 찾습니다.

5. IP 주소를 복사하여 필터링 또는 허용된 목록 같은 아웃바운드 트래픽의 특별 처리에 추가합니다. 목록에서 기존 IP 주소를 삭제하지 마세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure에서 시작한 IP 주소 변경을 준비하는 방법을 설명합니다. Azure App Service의 IP 주소에 대한 자세한 내용은 [Azure App Service에서 인바운드 및 아웃바운드 IP 주소](overview-inbound-outbound-ips.md)를 참조하세요.
