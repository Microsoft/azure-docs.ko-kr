---
title: Traffic Manager를 사용 하 여 DNS 이름 구성
description: 부하 분산을 위해 Traffic Manager와 통합 되는 Azure App Service 앱에 대 한 사용자 지정 도메인을 구성 하는 방법에 대해 알아봅니다.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 0e8d5fa14678a2a26234dfcd73f4a50af62ca7aa
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962879"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Traffic Manager 통합을 사용 하 여 Azure App Service에서 사용자 지정 도메인 이름 구성

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

[Azure Traffic Manager](../traffic-manager/index.yml) 을 사용 하 여 [Azure App Service](overview.md)에 대 한 트래픽 부하를 분산 하는 경우 ** \<traffic-manager-endpoint> trafficmanager.net**를 사용 하 여 App Service 앱에 액세스할 수 있습니다. \.사용자에 게 보다 쉽게 알아볼 수 있는 도메인 이름을 제공 하기 위해 App Service 앱과 함께 www contoso.com와 같은 사용자 지정 도메인 이름을 할당할 수 있습니다.

이 문서에서는 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)와 통합 된 App Service 앱을 사용 하 여 사용자 지정 도메인 이름을 구성 하는 방법을 보여 줍니다.

> [!NOTE]
> Traffic Manager 끝점을 사용 하 여 도메인 이름을 구성 하는 경우에는 [CNAME](https://en.wikipedia.org/wiki/CNAME_record) 레코드만 지원 됩니다. 레코드가 지원 되지 않기 때문에 contoso.com와 같은 루트 도메인 매핑도 지원 되지 않습니다.
> 

## <a name="prepare-the-app"></a>앱 준비

Azure Traffic Manager와 통합 된 앱에 사용자 지정 DNS 이름을 매핑하려면 웹 앱의 [App Service 계획이](https://azure.microsoft.com/pricing/details/app-service/) **표준** 계층 이상 이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

[Azure Portal](https://portal.azure.com)에서 **App Services**를 검색 하 고 선택 합니다.

**App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

앱 페이지의 왼쪽 탐색 영역에서 강화 **(App Service 계획)** 를 선택 합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **표준** 계층 이상 ( **프로덕션** 또는 **Isolated** 범주의 모든 계층)에 있는지 확인 합니다. 그렇다면 **확장** 페이지를 닫고를 건너뛰고 [CNAME 매핑 만들기](#create-the-cname-mapping)를 건너뜁니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

앱을 확장 해야 하는 경우 **프로덕션** 범주의 가격 책정 계층을 선택 합니다. 추가 옵션을 보려면 **추가 옵션 보기**를 클릭합니다.

**적용**을 클릭합니다.

## <a name="create-traffic-manager-endpoint"></a>Traffic Manager 끝점 만들기

[끝점 추가 또는 삭제](../traffic-manager/traffic-manager-manage-endpoints.md)의 단계에 따라 Traffic Manager 프로필에 App Service 앱을 끝점으로 추가 합니다.

App Service 앱이 지원 되는 가격 책정 계층에 있으면 끝점을 추가할 때 사용 가능한 App Service 대상 목록에 표시 됩니다. 앱이 목록에 없는 경우 [앱의 가격 책정 계층을 확인](#prepare-the-app)합니다.

## <a name="create-the-cname-mapping"></a>CNAME 매핑 만들기
> [!NOTE]
> [구매한 App Service 도메인](manage-custom-dns-buy-domain.md)을 구성 하려면이 섹션을 건너뛰고 [사용자 지정 도메인 사용](#enable-custom-domain)으로 이동 합니다.
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

각 도메인 공급자의 세부 사항이 변경 되는 반면, [루트가 아닌 사용자 지정 도메인 이름](#what-about-root-domains) (예: **www.contoso.com**) *에서* 앱과 통합 된**contoso.trafficmanager.net**(Traffic Manager 도메인 이름) *로* 매핑합니다. 

> [!NOTE]
> 레코드를 이미 사용 중이고 앱을 우선적으로 바인딩해야 하는 경우 추가 CNAME 레코드를 만들 수 있습니다. 예를 들어 **www \. contoso.com** 를 앱에 미리 바인딩하려면 **AWVERIFY** 에서 **contoso.trafficmanager.net**로 CNAME 레코드를 만듭니다. 그런 다음 \. "www" CNAME 레코드를 변경할 필요 없이 "www contoso.com"를 앱에 추가할 수 있습니다. 자세한 내용은 [Azure App Service에 활성 DNS 이름 마이그레이션](manage-custom-dns-migrate-domain.md)을 참조 하세요.

도메인 공급자에서 DNS 레코드를 추가하거나 수정했다면 변경 내용을 저장합니다.

### <a name="what-about-root-domains"></a>루트 도메인은 무엇 인가요?

Traffic Manager는 CNAME 레코드를 사용 하는 사용자 지정 도메인 매핑만 지원 하 고 DNS 표준은 루트 도메인 (예: **contoso.com**) 매핑을 위한 CNAME 레코드를 지원 하지 않기 때문에 Traffic Manager 루트 도메인에 대 한 매핑을 지원 하지 않습니다. 이 문제를 해결 하려면 앱 수준에서 URL 리디렉션을 사용 합니다. 예를 들어 ASP.NET Core에서 [URL 재작성](/aspnet/core/fundamentals/url-rewriting)을 사용할 수 있습니다. 그런 다음 Traffic Manager를 사용 하 여 하위 도메인 (**www.contoso.com**)의 부하를 분산 합니다.

고가용성 시나리오의 경우 루트 도메인에서 각 앱 복사본의 IP 주소로 가리키는 여러 *a 레코드* 를 만들어 Traffic Manager 없이 내결함성 DNS 설치를 구현할 수 있습니다. 그런 다음 [동일한 루트 도메인을 모든 앱 복사본에 매핑합니다](app-service-web-tutorial-custom-domain.md#map-an-a-record). 동일한 도메인 이름이 동일한 지역에 있는 두 개의 다른 앱에 매핑될 수 없기 때문에이 설정은 앱 복사본이 다른 지역에 있는 경우에만 작동 합니다.

## <a name="enable-custom-domain"></a>사용자 지정 도메인 사용
도메인 이름에 대 한 레코드가 전파 된 후 브라우저를 사용 하 여 사용자 지정 도메인 이름이 App Service 앱으로 확인 되는지 확인 합니다.

> [!NOTE]
> CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. 와 같은 서비스를 사용 하 여 <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> CNAME을 사용할 수 있는지 확인할 수 있습니다.
> 
> 

1. 도메인 확인에 성공 하면 [Azure Portal](https://portal.azure.com) 에서 앱 페이지로 돌아갑니다.
2. 왼쪽 탐색 영역에서 **사용자 지정 도메인**  >  **호스트 이름 추가**를 선택 합니다.
4. 이전에 매핑한 사용자 지정 도메인 이름을 입력 하 고 **유효성 검사**를 선택 합니다.
5. **호스트 이름 레코드 형식**이 **CNAME(www\.example.com 또는 하위 도메인)** 으로 설정되어 있는지 확인합니다.

6. App Service 앱은 이제 Traffic Manager 끝점과 통합 되기 때문에 **CNAME 구성**아래에 Traffic Manager 도메인 이름이 표시 됩니다. 이를 선택 하 고 **사용자 지정 도메인 추가**를 클릭 합니다.

    ![앱에 DNS 이름 추가](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에서 SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)