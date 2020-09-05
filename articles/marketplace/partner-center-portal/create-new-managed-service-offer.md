---
title: Microsoft 상업용 마켓플레이스에서 관리형 서비스 제품 만들기
description: 파트너 센터의 상업용 마켓플레이스 포털을 사용하여 Azure Marketplace에 나열할 새로운 관리형 서비스 제품을 만드는 방법입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.date: 08/07/2020
ms.openlocfilehash: 9ff11312b6d231927f5053868b64e0b825df371e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816986"
---
# <a name="create-a-managed-service-offer"></a>관리되는 서비스 제품 만들기

관리형 서비스 제품은 [Azure Lighthouse](../../lighthouse/overview.md) 시나리오가 가능하도록 만드는 데 유용합니다. 고객이 관리형 서비스 제품을 수락하면 [Azure 위임 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 위해 리소스를 온보딩할 수 있습니다. 시작하기 전에 [파트너 센터에서 상업용 마켓플레이스 계정을 만드세요](create-account.md)(계정이 없는 경우). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

[실버 또는 골드 클라우드 플랫폼 역량 수준](https://partner.microsoft.com/membership/cloud-platform-competency)이거나 [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp)여야 관리형 서비스 제품을 게시할 수 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **관리형 서비스**를 선택합니다.

    ![왼쪽 탐색 메뉴를 보여줍니다.](./media/new-offer-managed-service.png)

>[!NOTE]
>제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 온라인 매장에 표시 됩니다. 변경을 수행한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

* 이 ID는 Marketplace 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
* 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
* **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

* 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
* **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

**만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

[관리형 서비스 인증 정책](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services)에 따라 **잠재 대상 고객**이 필요합니다. 고객이 제품을 배포할 때마다 CRM 시스템에 레코드가 생성됩니다.

자세한 내용은 [잠재 고객 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조하세요.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서 Marketplace에서 제품을 그룹화하는 데 사용되는 범주와 제품을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>Category

적절한 마켓플레이스 검색 영역에 제품을 배치하는 데 사용할 범주를 최소 1개, 최대 5개 선택합니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원하는지 확인해야 합니다.

### <a name="terms-and-conditions"></a>사용 약관

**사용 약관** 필드에 사용자의 법적 사용 약관을 제공합니다. 사용 약관을 찾을 수 있는 URL을 제공할 수도 있습니다. 고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에서는 제품에 대한 마켓플레이스 세부 정보(예: 제품 이름, 설명 및 이미지)를 정의할 수 있습니다.

> [!NOTE]
> 제품 설명(예: 설명, 문서, 스크린샷, 사용 약관)이 "[영어가 아닌 언어]에서만 사용할 수 있습니다."로 시작하는 경우 제품 세부 정보는 영어로 작성되지 않아도 됩니다. 또한 제품 목록 콘텐츠에 사용되는 것 이외의 언어로 콘텐츠를 제공하는 *유용한 링크 URL*을 제공할 수 있습니다.

다음은 Azure Portal에서 제공 정보를 표시 하는 방법의 예입니다.

:::image type="content" source="media/example-managed-services.png" alt-text="이 제품이 Azure Portal 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 제목
2. Description
3. 유용한 링크
4. 스크린샷

### <a name="name"></a>속성

여기에 입력하는 이름은 고객에게 제품 목록의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭**에 입력한 텍스트로 미리 채워져 있지만 이 값을 변경할 수 있습니다. 이 이름은 상표로 등록될 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50자를 초과할 수 없으며 이모지는 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

마켓플레이스 검색 결과에 사용할 수 있는 제품에 대한 간략한 설명(최대 100자)을 제공합니다.

### <a name="long-summary"></a>긴 요약

제품에 대한 좀 더 긴 설명(최대 256자)을 제공합니다. 이 긴 요약은 마켓플레이스 검색 결과에도 사용할 수 있습니다.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="privacy-policy-link"></a>개인정보처리방침 링크

귀사의 개인정보처리방침(자체 사이트에서 호스트됨) URL을 입력합니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공하는 것은 귀하의 책임입니다.

### <a name="useful-links"></a>유용한 링크

솔루션에 대한 선택적 온라인 추가 문서를 제공합니다. **+ 링크 추가**를 클릭하여 유용한 링크를 더 추가합니다.

### <a name="contact-information"></a>연락처 정보

이 섹션에서는 **지원 연락처** 및 **엔지니어링 연락처**의 이름, 이메일 및 전화번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

### <a name="support-urls"></a>지원 URL

**Azure 글로벌 고객** 및/또는 **Azure Government 고객**을 위한 지원 웹 사이트가 있으면 여기에 해당 URL을 제공합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

이 섹션에서 제품을 고객에게 표시할 때 사용되는 로고 및 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식이어야 합니다.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

#### <a name="store-logos"></a>스토어 로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작고** **중간** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로 바꿀 수 있습니다.

- **큼** (216 x 216에서 350 x 350 px, required)
- **중간** (90 x 90 px, 선택 사항)
- **작음** (48 x 48 px, 선택 사항)

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 최대 5개의 스크린샷을 추가합니다. 모든 스크린샷은 1280 x 720픽셀이어야 합니다.

#### <a name="videos"></a>동영상

필요에 따라 제품을 설명하는 비디오를 5개까지 추가할 수 있습니다. 이러한 비디오는 YouTube 및/또는 Vimeo에서 호스트해야 합니다. 각 항목에 대해 비디오 이름, 해당 URL 및 비디오의 썸네일 이미지(1280 x 720픽셀)를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 Marketplace 목록 리소스

- [Marketplace 제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="preview"></a>미리 보기

제품을 광범위한 Marketplace 제품에 라이브로 게시하기 전에 먼저 제한된 미리 보기 대상이 사용할 수 있도록 해야 합니다. 이렇게 하면 제품을 고객에게 제공하기 전에 Azure Marketplace에 어떻게 나타나는지 확인할 수 있습니다. Microsoft 지원 및 엔지니어링 팀은 이 미리 보기 기간 중에 제품을 볼 수도 있습니다.

**미리 보기 대상** 섹션에 Azure 구독 ID를 입력하여 미리 보기 대상을 정의할 수 있습니다. 구독 ID를 최대 10개 입력하거나 구독 ID 최대 100개가 포함된 .csv 파일을 업로드할 수 있습니다.

이러한 구독과 연결된 고객은 제품이 활성화되기 전에 Azure Marketplace에서 볼 수 있습니다. 여기에 자체 구독을 포함해야 합니다. 그래야 제품을 미리 볼 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="plan-overview"></a>플랜 개요

각 제품에는 하나 이상의 계획 (이전의 Sku)이 있어야 합니다. 여러 플랜을 추가하여 다양한 가격으로 다양한 기능 세트를 지원하거나, 제한된 특정 고객 대상 그룹을 위한 특정 플랜을 사용자 지정할 수 있습니다. 고객은 상위 제품 아래에서 사용할 수 있는 플랜을 볼 수 있습니다.

각 제품에 대해 최대 100 요금제를 만들 수 있습니다. 최대 45 개는 개인 일 수 있습니다. [Microsoft 상업적 marketplace에서 비공개 제품](../private-offers.md)의 비공개 요금제에 대해 자세히 알아보세요.

**플랜 개요** 페이지에서 **+ 새 플랜 만들기**를 선택합니다. 그런 다음, **플랜 ID**와 **플랜 이름**을 입력합니다. 이 값은 모두 소문자 영숫자, 대시 및 밑줄만 포함할 수 있으며 최대 50자로 제한됩니다. 이 값은 고객에게 표시될 수 있으며 제품을 게시한 후에는 변경할 수 없습니다.

값을 입력했으면 **만들기**를 선택하여 플랜 작업을 계속합니다. 완료해야 하는 섹션은 3가지 즉, **플랜 목록**, **가격 책정 및 가용성**, **기술 구성**입니다.

### <a name="plan-listing"></a>플랜 목록

먼저 플랜에 대한 **검색 결과 요약**을 제공합니다. 마켓플레이스 검색 결과에 사용할 수 있는 플랜에 대한 간략한 설명(최대 100자)입니다.

다음으로 플랜에 대한 자세한 설명을 제공하는 **설명**을 입력합니다.

### <a name="pricing-and-availability"></a>가격 책정 및 가용성

현재 관리형 서비스 제품에 사용할 수 있는 가격 책정 모델은 **BYOL(사용자 라이선스 필요)** 하나뿐입니다. 즉, 고객에게 이 제품과 관련된 비용을 직접 청구할 것이며 Microsoft는 사용자에게 요금을 부과하지 않음을 의미합니다.

**플랜 표시 여부** 섹션에서 플랜이 [비공개](../../marketplace/private-offers.md)여야 하는지 여부를 지정할 수 있습니다. **비공개 플랜** 확인란을 선택하지 않은 상태로 두면, 플랜은 특정 고객(또는 특정 수의 고객)으로 제한되지 않습니다.

> [!NOTE]
> 사설 요금제는 CSP (클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정 된 구독에서 지원 되지 않습니다.

이 플랜을 특정 고객만 사용할 수 있도록 설정하려면 **예**를 선택합니다. 이렇게 하면 구독 ID를 제공하여 고객을 식별해야 합니다. 하나씩 입력하거나(구독 최대 10개) .csv 파일(모든 플랜의 구독 최대 10,000개)을 업로드하여 입력할 수 있습니다. 제품을 테스트하고 유효성을 검사할 수 있도록 여기에 고유한 구독을 포함해야 합니다.

> [!IMPORTANT]
> 플랜이 공개로 게시된 후에는 다시 비공개로 변경할 수 없습니다. 제품을 수락하고 리소스를 위임할 수 있는 고객을 제어하려면 비공개 플랜을 사용합니다. 공개 플랜에서는 특정 고객이나 특정 수의 고객으로 가용성을 제한할 수 없습니다(원하면 플랜 판매를 완전히 중단할 수는 있음). 제품을 게시할 때 **역할 정의**가 [관리형 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)로 설정된 **권한 부여**를 포함한 경우에만 고객이 제품을 수락한 후 [위임에 대한 액세스 권한을 제거](../../lighthouse/how-to/remove-delegation.md)할 수 있습니다. 고객에게 연락하여 [액세스 권한을 제거](../../lighthouse/how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers)하도록 요청할 수도 있습니다.

### <a name="technical-configuration"></a>기술 구성

플랜의 이 섹션에서는 고객 리소스 관리를 위한 인증 정보가 포함된 매니페스트를 만듭니다. 이 정보는 [Azure 위임 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 사용하도록 설정하는 데 필요합니다.

[Azure Lighthouse 시나리오의 테넌트, 역할 및 사용자](../../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles)를 검토하고 지원되는 역할과 권한 부여 정의에 대한 모범 사례를 이해해야 합니다.

> [!NOTE]
> 위에서 설명한 것처럼 **권한 부여** 항목의 사용자 및 역할은 플랜을 구매한 모든 고객에게 적용됩니다. 특정 고객에 대한 액세스를 제한하려는 경우 독점 사용을 위해 프라이빗 플랜을 게시해야 합니다.

#### <a name="manifest"></a>매니페스트

먼저 매니페스트의 **버전** 을 제공합니다. *n.n.n* 형식(예: 1.2.5)을 사용합니다.

그런 다음, **테넌트 ID**를 입력합니다. 귀사의 Azure AD(Azure Active Directory) 테넌트 ID와 연결된 GUID입니다. 즉, 귀사 고객의 리소스에 액세스하는 데 사용할 관리 테넌트입니다. 이 작업이 어려운 경우 Azure Portal의 오른쪽 위에 있는 계정 이름으로 마우스를 가져가거나 **디렉터리 전환**을 선택하여 찾을 수 있습니다.

새로운 제품 버전을 게시하고 업데이트된 매니페스트를 만들어야 하는 경우, **+ New manifest**(새 매니페스트)를 선택합니다. 이전 매니페스트 버전에서 버전 번호가 증가해야 합니다.

#### <a name="authorization"></a>권한 부여

권한 부여는 플랜을 구매한 고객의 리소스 및 구독에 액세스할 수 있는 관리 테넌트의 엔터티를 정의합니다. 이러한 각 엔터티에는 특정 수준의 액세스 권한을 부여하는 기본 제공 역할이 할당됩니다.

플랜마다 최대 20개의 권한 부여를 만들 수 있습니다.

> [!TIP]
> 대부분의 경우 일련의 개별 사용자 계정이 아닌 Azure AD 사용자 그룹 또는 서비스 주체에 역할을 할당하는 것이 좋습니다. 이렇게 하면 액세스 요구 사항이 변경될 때 플랜을 업데이트한 후 다시 게시하지 않고도 개별 사용자에 대한 액세스 권한을 추가하거나 제거할 수 있습니다. Azure AD 그룹에 역할을 할당할 때는 [**그룹 유형**이 **Office 365**가 아닌 **보안**이어야](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) 합니다. 추가 권장 사항은 [Azure Lighthouse 시나리오의 테넌트, 역할 및 사용자](../../lighthouse/concepts/tenants-users-roles.md)를 참조하세요.

각 **권한 부여**에 대해 다음을 제공해야 합니다. 그런 다음, 사용자 및 역할 정의를 더 추가하는 데 필요한 횟수만큼 **+ 권한 부여 추가**를 선택할 수 있습니다.

- **Azure AD 개체 ID**: 고객의 리소스에 대한 특정 권한(역할 정의에 정의됨)을 부여할 사용자, 사용자 그룹 또는 애플리케이션의 Azure AD 식별자입니다.
- **Azure AD 개체 표시 이름**: 고객이 이 권한 부여의 용도를 이해하는 데 도움이 되는 친숙한 이름입니다. 리소스를 위임할 때 고객에게 이 이름이 표시됩니다.
- **역할 정의**: 목록에서 사용 가능한 Azure AD 기본 제공 역할 중 하나를 선택합니다. 이 역할은 **Azure AD 개체 ID** 필드의 사용자가 고객의 리소스에 대해 갖게 되는 권한을 결정합니다. 이러한 역할에 대 한 설명은 [기본 제공 역할](../../role-based-access-control/built-in-roles.md) 및 [Azure Lighthouse에 대 한 역할 지원](../../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse)을 참조 하세요.
  > [!NOTE]
  > 적용 가능한 신규 기본 제공 역할이 Azure에 추가되면 여기에서 사용할 수 있게 됩니다. 다만, 약간의 지연이 있을 수 있습니다.
- **할당 가능한 역할**: 이 옵션은 이 권한 부여의 **역할 정의**에서 사용자 액세스 관리자를 선택한 경우에만 나타납니다. 그렇다면 여기에 할당 가능한 역할을 하나 이상 추가해야 합니다. **Azure AD 개체 ID** 필드의 사용자는 이러한 역할을 [수정할 수 있는 정책을 배포](../../lighthouse/how-to/deploy-policy-remediation.md)하는 데 필요한 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 할당할 수 있습니다. 사용자 액세스 관리자 역할에 연결된 다른 사용 권한이 이 사용자에게 적용되지 않습니다.

> [!TIP]
> 필요한 경우 [위임에 대한 액세스 권한을 제거](../../lighthouse/how-to/remove-delegation.md)할 수 있도록 **역할 정의**가 [관리형 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)로 설정된 **권한 부여**를 포함합니다. 이 역할을 할당하지 않으면 고객 테넌트의 사용자만 위임된 리소스를 제거할 수 있습니다.

플랜의 모든 섹션을 작성한 후에는 필요한 횟수만큼 **+ 새 플랜 만들기**를 선택하여 플랜을 더 만들 수 있습니다. 완료되면 **저장**을 선택합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기 제품 제출

제품의 필수 섹션을 모두 작성한 후에는 포털의 우측 상단 모서리에서 **검토 및 게시**를 선택합니다.

이 제품을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에서 완료 상태를 확인합니다.
  - **시작되지 않음** – 섹션이 작업되지 않았으므로 완료해야 합니다.
  - **완료되지 않음** – 섹션에 수정해야 하는 오류가 있거나 추가 정보를 입력해야 합니다. 섹션으로 돌아가서 업데이트합니다.
  - **완료됨** – 섹션이 완료되고 필요한 모든 데이터가 입력되었으며 오류가 발생하지 않습니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료됨 상태여야 합니다.
- **인증 참고 사항** 섹션에서 앱을 이해하는 데 도움이 되는 추가 정보뿐만 아니라 앱이 올바르게 테스트되는지 확인하는 테스트 지침을 인증 팀에 제공합니다.
- **제출**을 선택하여 게시할 제품을 제출합니다. 제품의 미리 보기 버전을 검토하고 승인할 수 있게 되면 이메일을 보내 드립니다. 파트너 센터로 돌아가서 제품을 공개 게시(또는 프라이빗 제품을 프라이빗 사용자에게 게시)하려면 **라이브 상태로 전환**을 선택합니다.

### <a name="customer-experience-and-offer-management"></a>사용자 환경 및 제품 관리

고객이 제품을 배포하면 [Azure 위임 리소스 관리](../../lighthouse/concepts/azure-delegated-resource-management.md)를 위해 구독 또는 리소스 그룹을 위임할 수 있습니다. 이 프로세스에 대한 자세한 내용은 [고객 온보딩 프로세스](../../lighthouse/how-to/publish-managed-services-offers.md#the-customer-onboarding-process)를 참조하세요.

언제든지 [제품의 업데이트된 버전을 게시](update-existing-offer.md)할 수 있습니다. 예를 들어 이전에 게시된 제품에 새 역할 정의를 추가하려고 할 수 있습니다. 이렇게 하면 제품을 이미 추가한 고객은 Azure Portal의 [**서비스 공급자**](../../lighthouse/how-to/view-manage-service-providers.md) 페이지에 업데이트를 사용할 수 있음을 알려 주는 아이콘이 표시됩니다. 각 고객은 변경 내용을 검토하고 새 버전으로 업데이트할지 여부를 결정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
- [Azure Lighthouse에 대해 알아보기](../../lighthouse/overview.md)
