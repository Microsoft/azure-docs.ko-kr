---
title: 영역 루트에서 워크로드가 분산된 Azure 웹앱 호스트
description: 영역 루트에서 Azure DNS 별칭 레코드를 사용하여 부하가 분산된 웹앱 호스트
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: rohink
ms.openlocfilehash: 8ba96a028d51e6e5503bb4a8e6735b48033c9ba1
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937359"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>영역 루트에서 워크로드가 분산된 Azure 웹앱 호스트

DNS 프로토콜은 영역 루트에서 A 또는 AAAA 레코드를 제외한 다른 레코드의 할당을 방지합니다. 예제 영역 루트는 contoso.com입니다. 이 제한은 Traffic Manager 뒤에 워크로드가 분산된 애플리케이션 있는 애플리케이션 소유자의 문제를 나타냅니다. 영역 apex 레코드에서 Traffic Manager 프로필을 가리킬 수는 없습니다. 결과적으로, 애플리케이션 소유자는 해결 방법을 사용해야 합니다. 애플리케이션 계층에서 리디렉션을 수행하면 영역 apex에서 다른 도메인으로 리디렉션됩니다. 예는 contoso.com에서 www\.contoso.com로의 리디렉션입니다. 이 정렬은 리디렉션 함수에 대한 단일 실패 지점을 나타냅니다.

별칭 레코드를 사용하면 이 문제가 더 이상 발생하지 않습니다. 이제 애플리케이션 소유자는 영역 apex 레코드에서 외부 엔드포인트가 있는 Traffic Manager 프로필을 가리키도록 할 수 있습니다. 애플리케이션 소유자는 DNS 영역 내 다른 모든 도메인에 사용되는 Traffic Manager 프로필을 가리킬 수 있습니다.

예를 들어 contoso.com 및 www\.contoso.com는 동일한 Traffic Manager 프로필을 가리킬 수 있습니다. 단, Traffic Manager 프로필에 외부 엔드포인트만 구성되어 있어야 합니다.

이 문서에서는 사용자 도메인 루트에 대한 별칭 레코드를 만들고, 웹앱에 대한 Traffic Manager 프로필 엔드포인트를 구성하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

테스트할 Azure DNS에서 호스트할 수 있는 도메인 이름이 있어야 합니다. 이 도메인에 대한 전체 제어 권한이 있어야 합니다. 전체 제어 권한에는 도메인의 NS(이름 서버) 레코드를 설정하는 권한이 포함됩니다.

Azure DNS에서 도메인을 호스트하기 위한 지침은 [자습서: Azure DNS에 도메인 호스트](dns-delegate-domain-azure-dns.md)를 참조하세요.

이 자습서에 사용되는 예제 도메인은 contoso.com이지만 사용자 고유의 도메인 이름을 사용하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹을 만들어 이 문서에서 사용되는 모든 리소스를 보유합니다.

## <a name="create-app-service-plans"></a>App Service 계획 만들기

구성 정보에 대한 다음 표를 사용하여 리소스 그룹에 두 개의 웹 App Service 계획을 만듭니다. App Service 계획을 만드는 방법에 대한 자세한 내용은 [Azure에서 App Service 계획 관리](../app-service/app-service-plan-manage.md)를 참조하세요.


|이름  |운영 체제  |위치  |가격 책정 계층  |
|---------|---------|---------|---------|
|ASP-01     |Windows|미국 동부|개발/테스트 D1-공유|
|ASP-02     |Windows|미국 중부|개발/테스트 D1-공유|

## <a name="create-app-services"></a>App Services 만들기

각 App Service 계획에 하나씩 두 개의 웹앱을 만듭니다.

1. Azure Portal 페이지의 왼쪽 위 모서리에서 **리소스 만들기**를 선택 합니다.
2. 검색 표시줄에 **웹앱**을 입력하고 Enter 키를 누릅니다.
3. **웹 앱**을 선택 합니다.
4. **만들기**를 선택합니다.
5. 기본값을 적용하고, 다음 표를 사용하여 두 개의 웹앱을 구성합니다.

   |이름<br>(.azurewebsites.net 내에서 고유해야 합니다.)|리소스 그룹 |런타임 스택|지역|App Service 계획/위치
   |---------|---------|-|-|-------|
   |App-01|기존 리소스 사용<br>리소스 그룹 선택|.NET Core 2.2|미국 동부|ASP-01 (D1)|
   |App-02|기존 리소스 사용<br>리소스 그룹 선택|.NET Core 2.2|미국 중부|ASP-02 (D1)|

### <a name="gather-some-details"></a>몇 가지 세부 정보 수집

이제 웹 앱의 IP 주소 및 호스트 이름을 확인 해야 합니다.

1. 리소스 그룹을 열고 첫 번째 웹 앱을 선택 합니다 (이 예제에서는**앱-01** ).
2. 왼쪽 열에서 **속성**을 선택 합니다.
3. **URL** 아래의 주소를 적어 두고, **아웃바운드 IP 주소** 아래에서 목록의 첫 번째 IP 주소를 적어 둡니다. 나중에 Traffic Manager 엔드포인트을 구성할 때이 정보를 사용 합니다.
4. **App-02**에 대해 반복합니다.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

리소스 그룹에서 Traffic Manager 프로필을 만듭니다. 기본값을 사용하고 trafficmanager.net 네임스페이스 내에서 고유한 이름을 입력합니다.

Traffic Manager 프로필을 만드는 방법에 대한 자세한 내용은 [빠른 시작: 고가용성 웹 애플리케이션을 위한 Traffic Manager 프로필 만들기](../traffic-manager/quickstart-create-traffic-manager-profile.md)를 참조하세요.

### <a name="create-endpoints"></a>엔드포인트 만들기

이제 두 개의 웹앱에 대한 엔드포인트를 만들 수 있습니다.

1. 리소스 그룹을 열고 Traffic Manager 프로필을 선택 합니다.
2. 왼쪽 열에서 **엔드포인트**을 선택 합니다.
3. **추가**를 선택합니다.
4. 다음 표를 사용하여 엔드포인트를 구성합니다.

   |유형  |이름  |대상 파악  |위치  |사용자 지정 헤더 설정|
   |---------|---------|---------|---------|---------|
   |외부 엔드포인트     |End-01|App-01에 기록한 IP 주소|미국 동부|호스트:\<App-01에 기록한 URL\><br>예: **호스트:app-01.azurewebsites.net**|
   |외부 엔드포인트     |End-02|App-02에 기록한 IP 주소|미국 중부|호스트:\<App-02에 기록한 URL\><br>예: **호스트:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>DNS 영역 만들기

테스트에 기존 DNS 영역을 사용하거나 새 영역을 만들 수 있습니다. Azure에서 새 DNS 영역을 만들고 위임하려면 [자습서: Azure DNS에 도메인 호스트](dns-delegate-domain-azure-dns.md)를 참조하세요.

## <a name="add-a-txt-record-for-custom-domain-validation"></a>사용자 지정 도메인 유효성 검사에 대한 TXT 레코드 추가

웹 앱에 사용자 지정 호스트 이름을 추가 하면 특정 TXT 레코드를 검색 하 여 도메인의 유효성을 검사 합니다.

1. 리소스 그룹을 열고 DNS 영역을 선택 합니다.
2. **레코드 집합**을 선택합니다.
3. 다음 표를 사용 하 여 레코드 집합을 추가 합니다. 값에 대해 이전에 기록한 실제 웹 앱 URL을 사용 합니다.

   |이름  |유형  |값|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>사용자 지정 도메인 추가

두 웹 앱에 대한 사용자 지정 도메인을 추가 합니다.

1. 리소스 그룹을 열고 첫 번째 웹 앱을 선택 합니다.
2. 왼쪽 열에서 **사용자 지정 도메인**을 선택 합니다.
3. **사용자 지정**도메인에서 **사용자 지정 도메인 추가**를 선택 합니다.
4. **사용자 지정 도메인**에서 사용자 지정 도메인 이름을 입력 합니다. 예를 들어 contoso.com입니다.
5. **유효성 검사**를 선택합니다.

   도메인은 유효성 검사를 통과 하 고 **호스트 이름 가용성** 및 **도메인 소유권**옆에 녹색 확인 표시를 표시 해야 합니다.
5. **사용자 지정 도메인 추가**를 선택합니다.
6. **사이트에 할당된 호스트 이름** 아래에서 새 호스트 이름을 보려면 브라우저를 새로 고칩니다. 페이지의 새로 고침은 항상 변경 내용을 즉시 표시 하지는 않습니다.
7. 두 번째 웹앱에 대해 이 절차를 반복합니다.

## <a name="add-the-alias-record-set"></a>별칭 레코드 집합 추가

이제 apex 영역에 대한 별칭 레코드를 추가 합니다.

1. 리소스 그룹을 열고 DNS 영역을 선택 합니다.
2. **레코드 집합**을 선택합니다.
3. 다음 표를 사용하여 레코드 집합을 추가합니다.

   |이름  |유형  |별칭 레코드 집합  |별칭 형식  |Azure 리소스|
   |---------|---------|---------|---------|-----|
   |@     |A|예|Azure 리소스|Traffic Manager - 프로필|


## <a name="test-your-web-apps"></a>웹앱 테스트

이제 웹 앱에 연결할 수 있는지 테스트 하 고 부하를 분산 하 고 있음을 테스트할 수 있습니다.

1. 웹 브라우저를 열고 도메인으로 이동합니다. 예를 들어 contoso.com입니다. 기본 웹앱 페이지가 표시됩니다.
2. 첫 번째 웹앱을 중지합니다.
3. 웹 브라우저를 닫고, 몇 분 정도 기다립니다.
4. 웹 브라우저를 시작하고 도메인으로 이동합니다. 기본 웹앱 페이지가 계속 표시됩니다.
5. 두 번째 웹앱을 중지합니다.
6. 웹 브라우저를 닫고, 몇 분 정도 기다립니다.
7. 웹 브라우저를 시작하고 도메인으로 이동합니다. 웹앱이 중지되었음을 나타내는 오류 403이 표시됩니다.
8. 두 번째 웹앱을 시작합니다.
9. 웹 브라우저를 닫고, 몇 분 정도 기다립니다.
10. 웹 브라우저를 시작하고 도메인으로 이동합니다. 기본 웹앱 페이지가 다시 표시됩니다.

## <a name="next-steps"></a>다음 단계

별칭 레코드에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [자습서: Azure 공용 IP 주소를 참조하도록 별칭 레코드 구성](tutorial-alias-pip.md)
- [자습서: Traffic Manager를 사용하여 apex 도메인 이름을 지원하도록 별칭 레코드 구성](tutorial-alias-tm.md)
- [DNS FAQ](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

활성 DNS 이름을 마이그레이션하는 방법에 대한 자세한 내용은 [Azure App Service에 활성 dns 이름 마이그레이션](../app-service/manage-custom-dns-migrate-domain.md)을 참조 하세요.
