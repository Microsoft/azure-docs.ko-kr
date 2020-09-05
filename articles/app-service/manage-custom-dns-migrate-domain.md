---
title: 활성 DNS 이름 마이그레이션
description: 가동 중지 시간 없이 라이브 사이트에 이미 할당된 사용자 지정 DNS 도메인 이름을 Azure App Service로 마이그레이션하는 방법을 알아봅니다.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: c51745b7760573aa3c6ae067e9a6c1cc315f8e56
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871397"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Azure App Service로 활성 DNS 이름 마이그레이션

이 문서에서는 가동 중지 시간 없이 [Azure App Service](../app-service/overview.md)로 활성 DNS 이름을 마이그레이션하는 방법을 보여 줍니다.

라이브 사이트 및 해당 DNS 도메인 이름을 App Service로 마이그레이션하는 경우 DNS 이름이 이미 라이브 트래픽 서비스를 제공하고 있습니다. 활성 DNS 이름을 App Service 앱에 미리 바인딩하여 마이그레이션하는 동안 DNS 확인에 가동 중지 시간이 발생하지 않도록 할 수 있습니다.

DNS 확인의 중단을 염려하지 않는 경우에는 [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)을 참조하세요.

## <a name="prerequisites"></a>전제 조건

이 방법을 완료하려면 다음이 필요합니다.

- [App Service 앱이 무료 계층에 있지 않은지 확인합니다](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>도메인 이름을 먼저 바인딩

사용자 지정 도메인 미리을 바인딩하는 경우 기존 DNS 레코드를 변경 하기 전에 다음 두 가지 작업을 모두 수행 합니다.

- 도메인 소유권 확인
- 앱에 대해 도메인 이름 사용

마지막으로 이전 사이트에서 App Service 앱으로 사용자 지정 DNS 이름을 마이그레이션하는 경우 DNS 확인에 가동 중지 시간이 발생하지 않습니다.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>도메인 확인 ID 가져오기

[도메인 확인 Id 가져오기](app-service-web-tutorial-custom-domain.md#get-domain-verification-id)의 단계를 수행 하 여 앱에 대 한 도메인 확인 id를 가져옵니다.

### <a name="create-domain-verification-record"></a>도메인 확인 레코드 만들기

도메인 소유권을 확인 하려면 도메인 확인을 위해 TXT 레코드를 추가 합니다. TXT 레코드의 호스트 이름은 매핑할 DNS 레코드 유형의 유형에 따라 달라 집니다. 다음 표를 참조 하세요 ( `@` 일반적으로 루트 도메인을 나타냄).

| DNS 레코드 예제 | TXT 호스트 | TXT 값 |
| - | - | - |
| \@(root) | _asuid_ | [앱에 대 한 도메인 확인 ID](app-service-web-tutorial-custom-domain.md#get-domain-verification-id) |
| www(하위) | _asuid. www_ | [앱에 대 한 도메인 확인 ID](app-service-web-tutorial-custom-domain.md#get-domain-verification-id) |
| \*(와일드카드) | _asuid_ | [앱에 대 한 도메인 확인 ID](app-service-web-tutorial-custom-domain.md#get-domain-verification-id) |

DNS 레코드 페이지에서 마이그레이션할 DNS 이름의 레코드 종류에 유의합니다. App Service는 CNAME 및 A 레코드로부터의 매핑을 지원합니다.

> [!NOTE]
> 와일드카드 `*` 레코드는 기존 CNAME 레코드가 있는 하위 도메인 유효성을 검사하지 않습니다. 각 하위 도메인에 대해 TXT 레코드를 명시적으로 만들어야 할 수 있습니다.

### <a name="enable-the-domain-for-your-app"></a>앱에 대해 도메인 사용

1. [Azure Portal](https://portal.azure.com) 앱 페이지 왼쪽 탐색 영역에서 **사용자 지정 도메인**을 선택합니다. 

    ![사용자 지정 도메인 메뉴](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **사용자 지정** 도메인 페이지에서 **사용자 지정 도메인 추가**를 선택 합니다.

    ![호스트 이름 추가](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. 마이그레이션할 정규화 된 도메인 이름을 입력 합니다 .이 이름은 만든 TXT 레코드 (예:, 또는)에 해당 합니다 `contoso.com` `www.contoso.com` `*.contoso.com` . **유효성 검사**를 선택합니다.

    **사용자 지정 도메인 추가** 단추가 활성화됩니다. 

1. **호스트 이름 레코드 종류**가 마이그레이션할 DNS 레코드 종류로 설정되어 있는지 확인합니다. **호스트 이름 추가**를 선택합니다.

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

**사용자 지정 도메인** 페이지에서 앱의 IP 주소를 복사합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>DNS 레코드 업데이트

다시 도메인 공급자의 DNS 레코드 페이지에서 다시 매핑할 DNS 레코드를 선택합니다.

`contoso.com` 루트 도메인 예제의 경우 다음 표의 예제와 같은 A 또는 CNAME 레코드를 다시 매핑합니다. 

| FQDN 예 | 레코드 형식 | 호스트 | 값 |
| - | - | - | - |
| contoso.com(루트) | A | `@` | [앱의 IP 주소 복사](#info)에서 가져온 IP 주소 |
| www \. contoso.com (sub) | CNAME | `www` | _&lt;appname> azurewebsites.net_ |
| \*.contoso.com(와일드카드) | CNAME | _\*_ | _&lt;appname> azurewebsites.net_ |

설정을 저장합니다.

DNS 쿼리는 DNS가 전파된 직후 App Service 앱에 대해 확인을 시작해야 합니다.

## <a name="migrate-domain-from-another-app"></a>다른 앱에서 도메인 마이그레이션

Azure에서 구독 또는 동일한 구독 내에서 활성 사용자 지정 도메인을 마이그레이션할 수 있습니다. 그러나 가동 중지 시간 없이 마이그레이션하는 경우 원본 앱이 필요 하 고 대상 앱은 특정 시간에 동일한 사용자 지정 도메인에 할당 됩니다. 따라서 두 앱이 동일한 배포 단위 (내부적으로는 웹 공간 이라고 함)에 배포 되지 않았는지 확인 해야 합니다. 도메인 이름은 각 배포 단위에서 하나의 앱에만 할당할 수 있습니다.

FTP/S URL의 도메인 이름을 살펴보면 앱에 대 한 배포 단위를 찾을 수 있습니다 `<deployment-unit>.ftp.azurewebsites.windows.net` . 배포 단위가 원본 앱과 대상 앱 간에 다른 지 확인 합니다. 앱의 배포 단위는의 [App Service 계획](overview-hosting-plans.md) 에 따라 결정 됩니다. 계획을 만들 때 Azure에서 임의로 선택 되며 변경할 수 없습니다. Azure는 동일한 [리소스 그룹 *및* 동일한 지역에서](app-service-plan-manage.md#create-an-app-service-plan)두 요금제를 만들 때 동일한 배포 단위에 있지만 계획이 다른 배포 단위에 있는지 확인 하는 논리는 없습니다. 다른 배포 단위로 계획을 만드는 유일한 방법은 다른 배포 단위가 나타날 때까지 새 리소스 그룹이 나 지역에 계획을 계속 만드는 것입니다.

## <a name="next-steps"></a>다음 단계

App Service에 사용자 지정 TLS/SSL 인증서를 바인딩하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에서 TLS 바인딩을 사용 하 여 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
