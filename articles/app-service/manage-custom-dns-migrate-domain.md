---
title: 활성 DNS 이름 마이그레이션 - Azure App Service | Microsoft Docs
description: 가동 중지 시간 없이 라이브 사이트에 이미 할당된 사용자 지정 DNS 도메인 이름을 Azure App Service로 마이그레이션하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6215230a52bcb5c44f54747b447dc5f64e6af650
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130384"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Azure App Service로 활성 DNS 이름 마이그레이션

이 문서에서는 가동 중지 시간 없이 [Azure App Service](../app-service/overview.md)로 활성 DNS 이름을 마이그레이션하는 방법을 보여 줍니다.

라이브 사이트 및 해당 DNS 도메인 이름을 App Service로 마이그레이션하는 경우 DNS 이름이 이미 라이브 트래픽 서비스를 제공하고 있습니다. 활성 DNS 이름을 App Service 앱에 미리 바인딩하여 마이그레이션하는 동안 DNS 확인에 가동 중지 시간이 발생하지 않도록 할 수 있습니다.

DNS 확인의 중단을 염려하지 않는 경우에는 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 방법을 완료하려면 다음이 필요합니다.

- [App Service 앱이 무료 계층에 있지 않은지 확인합니다](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>도메인 이름을 먼저 바인딩

사용자 지정 도메인을 먼저 바인딩하는 경우 DNS 레코드를 변경하기 전에 다음 두 가지를 수행합니다.

- 도메인 소유권 확인
- 앱에 대해 도메인 이름 사용

마지막으로 이전 사이트에서 App Service 앱으로 사용자 지정 DNS 이름을 마이그레이션하는 경우 DNS 확인에 가동 중지 시간이 발생하지 않습니다.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="create-domain-verification-record"></a>도메인 확인 레코드 만들기

도메인 소유권을 확인하려면 TXT 레코드를 추가합니다. TXT 레코드는 _awverify.&lt;subdomain>_ 에서 _&lt;appname>.azurewebsites.net_으로 매핑합니다. 

필요한 TXT 레코드는 마이그레이션할 DNS 레코드에 따라 달라집니다. 예제를 보려면 다음 표를 참조하세요(`@`은 일반적으로 루트 도메인을 나타냄).

| DNS 레코드 예제 | TXT 호스트 | TXT 값 |
| - | - | - |
| \@(root) | _awverify_ | _&lt;appname>.azurewebsites.net_ |
| www(하위) | _awverify.www_ | _&lt;appname>.azurewebsites.net_ |
| \*(와일드카드) | _awverify.\*_ | _&lt;appname>.azurewebsites.net_ |

DNS 레코드 페이지에서 마이그레이션할 DNS 이름의 레코드 종류에 유의합니다. App Service는 CNAME 및 A 레코드로부터의 매핑을 지원합니다.

> [!NOTE]
> CloudFlare와 같은 특정 공급자의 경우 `awverify.*`는 유효한 레코드가 아닙니다. 이 레코드 대신 `*`만 사용하세요.

> [!NOTE]
> 와일드카드 `*` 레코드는 기존 CNAME 레코드가 있는 하위 도메인 유효성을 검사하지 않습니다. 각 하위 도메인에 대해 TXT 레코드를 명시적으로 만들어야 할 수 있습니다.


### <a name="enable-the-domain-for-your-app"></a>앱에 대해 도메인 사용

[Azure Portal](https://portal.azure.com) 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. 

![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**사용자 지정 도메인** 페이지에서 **호스트 이름 추가** 옆의 **+** 아이콘을 선택합니다.

![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

`www.contoso.com`과 같이 TXT 레코드를 추가한 정규화된 도메인 이름을 입력합니다. 와일드카드 도메인(예: \*.contoso.com)의 경우 와일드카드 도메인과 일치하는 DNS 이름을 사용할 수 있습니다. 

**유효성 검사**를 선택합니다.

**호스트 이름 추가** 단추가 활성화됩니다. 

**호스트 이름 레코드 종류**가 마이그레이션할 DNS 레코드 종류로 설정되어 있는지 확인합니다.

**호스트 이름 추가**를 선택합니다.

![앱에 DNS 이름 추가](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

새 호스트 이름이 앱의 **사용자 지정 도메인** 페이지에 반영되는 데에는 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고쳐 보세요.

![추가된 CNAME 레코드](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

이제 Azure 앱에서 사용자 지정 DNS 이름을 사용할 수 있도록 지정되었습니다. 

## <a name="remap-the-active-dns-name"></a>활성 DNS 이름 다시 매핑

남아 있는 유일한 작업은 활성 DNS 레코드가 App Service를 가리키도록 다시 매핑하는 것입니다. 현재는 여전히 이전 사이트를 가리키고 있습니다.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>앱의 IP 주소 복사(A 레코드만 해당)

CNAME 레코드를 다시 매핑하는 경우 이 섹션을 건너뛰세요. 

A 레코드를 다시 매핑하려면 **사용자 지정 도메인** 페이지에 표시되는 App Service 앱의 외부 IP 주소가 필요합니다.

오른쪽 상단 모서리에서 **X**를 선택하여 **호스트 이름 추가** 페이지를 닫습니다. 

**사용자 지정 도메인** 페이지에서 앱의 IP 주소를 복사합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>DNS 레코드 업데이트

다시 도메인 공급자의 DNS 레코드 페이지에서 다시 매핑할 DNS 레코드를 선택합니다.

`contoso.com` 루트 도메인 예제의 경우 다음 표의 예제와 같은 A 또는 CNAME 레코드를 다시 매핑합니다. 

| FQDN 예 | 레코드 형식 | 호스트 | 값 |
| - | - | - | - |
| contoso.com(루트) | A | `@` | [앱의 IP 주소 복사](#info)에서 가져온 IP 주소 |
| www\.contoso.com (하위) | CNAME | `www` | _&lt;appname>.azurewebsites.net_ |
| \*.contoso.com(와일드카드) | CNAME | _\*_ | _&lt;appname>.azurewebsites.net_ |

설정을 저장합니다.

DNS 쿼리는 DNS가 전파된 직후 App Service 앱에 대해 확인을 시작해야 합니다.

## <a name="next-steps"></a>다음 단계

사용자 지정 SSL 인증서를 App Service에 바인딩하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 SSL 인증서 바인딩](app-service-web-tutorial-custom-ssl.md)
