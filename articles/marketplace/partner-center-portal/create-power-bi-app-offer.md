---
title: Microsoft AppSource에서 Power BI 앱 제안 만들기
description: Power BI 앱 제품을 만들고 Microsoft AppSource에 게시하는 방법을 알아봅니다.
author: navits09
ms.author: navits
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: ebc3ad6957890423314bb2d8623c7b4708aed10b
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815416"
---
# <a name="create-a-power-bi-app-offer"></a>Power BI 앱 제품 만들기

이 문서에서는 Power BI 앱 제품을 만들고 Microsoft [AppSource](https://appsource.microsoft.com/)에 게시하는 방법을 설명합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만드세요](create-account.md)(계정이 없는 경우). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Power BI 서비스 앱**을 선택합니다.

   ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-power-bi-app.png)

> [!NOTE]
> 제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 온라인 매장에 표시 됩니다. 변경을 수행한 후에는 항상 다시 게시해야 합니다.

> [!IMPORTANT]
> **Power BI 서비스 앱**이 표시되지 않거나 사용할 수 없는 경우 계정에 이 제품 유형을 만들 수 있는 권한이 없는 것입니다. 개발자 계정에 등록하는 것을 포함하여 이 제품 유형에 대한 모든 [요구 사항](create-power-bi-app-overview.md)이 충족되었는지 확인합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 여기에 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

**만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-overview"></a>제품 개요

이 페이지에는 이 제품을 게시하는 데 필요한 단계(완료된 단계 및 예정된 단계)와 각 단계를 완료하는 데 소요되는 시간이 시각적으로 표시됩니다.

사용자의 선택에 따라 이 제품에 대한 작업을 수행할 수 있는 링크가 포함되어 있습니다. 다음은 그 예입니다.

- 제안이 초안 [삭제 제안](update-existing-offer.md#delete-a-draft-offer)인 경우
- 제품이 live의 제품 판매를 [중지](update-existing-offer.md#stop-selling-an-offer-or-plan)하는 경우
- 제품이 미리 보기 상태 이면 [라이브로 전환](publishing-status.md#publisher-approval)합니다.
- 게시자 로그 아웃을 완료 하지 않은 경우 [게시 취소](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>제품 설정

### <a name="customer-leads"></a>잠재 고객

파트너 센터를 사용하여 마켓플레이스에 제품을 게시하려면 CRM(고객 관계 관리) 시스템에 연결해야 합니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터는 다음과 같은 CRM 시스템을 지원합니다.

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > CRM 시스템이 위에 나열되지 않은 경우 [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) 또는 [Https 엔드포인트](commercial-marketplace-lead-management-instructions-https.md)를 사용하여 잠재 고객 데이터를 저장한 다음 CRM 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시할 때 제품을 잠재 고객 대상에 연결합니다.
3. 잠재 고객 대상에 대한 연결이 제대로 구성되어 있는지 확인합니다. 파트너 센터에 게시하면 연결의 유효성이 검사되고 테스트 잠재 고객을 받을 수 있습니다. 제품을 라이브로 전환하기 전에 미리 볼 때 미리 보기 환경에서 직접 제품을 구매하여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객을 잃어버리는 경우가 없도록 잠재 고객 대상에 대한 연결을 업데이트된 상태로 유지해야 합니다.

다음은 몇 가지 추가 잠재 고객 관리 리소스입니다.

- [잠재 고객 관리 개요](commercial-marketplace-get-customer-leads.md)
- [리드 관리 faq](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [일반적인 리드 구성 오류](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [잠재 고객 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF(팝업 차단이 해제되어 있는지 확인)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 마켓플레이스에서 제품을 그룹화하는 데 사용되는 범주와 산업, 앱 버전 및 제품을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>Category

범주 및 하위 범주를 선택 하 여 제품을 적절 한 마켓플레이스 검색 영역에 넣습니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음**을 선택 합니다.

[제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주 및 하위 범주의 전체 목록을 참조 하세요.

### <a name="industry"></a>업종

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="legal"></a>법적 정보

#### <a name="terms-and-conditions"></a>사용 약관

사용자 고유의 사용자 지정 약관을 제공하려면 **사용 약관** 상자에서 최대 10,000자를 입력합니다. 사용 약관에 더 긴 설명이 필요한 경우 사용 약관으로 연결되는 단일 웹 링크를 입력합니다. 이 링크는 고객에게 활성 링크로 표시됩니다.

고객은 이 약관에 동의해야 제품을 사용할 수 있습니다.

다음 섹션인 제품 목록으로 진행하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

여기에서 마켓플레이스에 표시되는 제품 세부 정보를 정의합니다. 여기에는 제품 이름, 설명, 이미지 등이 포함됩니다.

### <a name="language"></a>언어

제품을 표시할 언어를 선택합니다. 현재 **영어(미국)** 가 유일하게 사용할 수 있는 옵션입니다.

각 언어/시장의 마켓플레이스 세부 정보(제품 이름, 설명 및 이미지 등)를 정의합니다. 이 정보를 제공할 언어/시장 이름을 선택합니다.

> [!NOTE]
> 제품 설명이 “이 애플리케이션은 [영어가 아닌 언어]에서만 사용할 수 있습니다.”라는 문구로 시작하는 경우 제품 세부 정보를 영어로 작성하지 않아도 됩니다. 제품 목록 세부 정보에 사용된 것과 다른 언어로 콘텐츠를 제공하는 유용한 링크를 제공해도 좋습니다.

다음은 Microsoft AppSource에서 제공 정보를 표시 하는 방법의 예입니다. 나열 된 가격은 예를 들기 위한 것 이며 실제 비용을 반영 하지는 않습니다.

:::image type="content" source="media/example-power-bi-app.png" alt-text="이 제품이 Microsoft AppSource 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 제품
3. 범주
4. 산업
5. 지원 주소 (링크)
6. 사용 약관
7. 개인 정보 보호 정책
8. Offer name
9. 요약
10. Description
11. 스크린샷/비디오

### <a name="name"></a>속성

여기에 입력하는 이름은 제품의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력한 텍스트로 미리 채워집니다. 이 이름은 나중에 변경할 수 있습니다.

이름:

- 상표로 등록된 이름일 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다.
- 50자를 초과할 수 없습니다.
- 이모지를 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

제품에 대한 간단한 설명을 입력합니다. 최대 100자까지 입력할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

### <a name="search-keywords"></a>검색 키워드

고객이 마켓플레이스에서 제품을 찾을 수 있도록 최대 3개의 검색 키워드를 입력합니다. 최상의 결과를 얻기 위해 이 키워드를 설명에도 사용합니다.

### <a name="helpprivacy-web-addresses"></a>도움말/개인 정보 보호 웹 주소

고객이 제품을 이해하는 데 도움이 되는 링크를 제공합니다.

#### <a name="help-link"></a>도움말 링크

고객이 제품에 대해 자세히 알아볼 수 있는 웹 주소를 입력합니다.

#### <a name="privacy-policy-url"></a>개인정보처리방침 URL

조직의 개인정보처리방침 웹 주소를 입력합니다. 제품이 개인 정보 보호법과 규정을 준수하는지 확인할 책임은 본인에게 있습니다. 웹 사이트에 유효한 개인정보처리방침을 게시해야 할 책임도 있습니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처** 및 **엔지니어링 연락처**의 이름, 메일 및 전화 번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않습니다. 이 정보는 Microsoft가 사용할 수 있으며, CSP(클라우드 솔루션 공급자) 파트너에게 제공될 수 있습니다.

- 지원 연락처(필수 사항): 일반적인 지원 질문을 처리할 담당자입니다.
- 엔지니어링 연락처(필수 사항): 기술 관련 질문 및 인증 문제를 처리할 담당자입니다.
- CSP 프로그램 연락처(선택 사항): CSP 프로그램과 관련된 재판매인 질문을 처리할 담당자입니다.

**지원 연락처** 섹션에서 파트너가 제품에 대한 지원을 찾을 수 있는 **지원 웹 사이트**의 웹 주소를 제공합니다.

### <a name="supporting-documents"></a>지원 문서

PDF 형식의 백서, 브로슈어, 검사 목록, 프레젠테이션 등의 관련 마케팅 문서를 하나 이상(3개까지) 제공합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

제품에 사용할 로고 및 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 흐린 이미지는 거부됩니다.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 `https://upload.xboxlive.com` 서비스를 차단하지 않는지 확인하세요.

#### <a name="store-logos"></a>스토어 로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작은** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로이를 바꿀 수 있습니다.

- **큼** (216 x 216에서 350 x 350 px, required)
- **작음** (48 x 48 px, 선택 사항)

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 1~5개의 스크린샷을 추가합니다. 각각 1280 x 720 픽셀 크기 및 PNG 형식이어야 합니다.

#### <a name="videos-optional"></a>동영상(선택 사항)

제품을 설명하는 최대 5개의 비디오를 추가합니다. 비디오의 이름, 웹 주소 및 비디오의 미리 보기 PNG 이미지를 1280 x 720 픽셀 크기로 입력 합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 목록 리소스

제품 목록을 만드는 방법에 대한 자세한 내용은 [목록에 제품 추가 모범 사례](../gtm-offer-listing-best-practices.md)를 참조하세요.

## <a name="technical-configuration"></a>기술 구성

Power BI Service에서 앱을 프로덕션으로 승격하고 고객이 앱을 설치할 수 있도록 지원하는 Power BI 앱 설치 관리자 링크를 제공합니다. 자세한 내용은 [대시보드와 보고서를 사용하여 Power BI에 앱 게시](https://docs.microsoft.com/power-bi/service-create-distribute-apps)를 참조하세요.

## <a name="supplemental-content"></a>추가 콘텐츠

제품의 유효성을 검사하는 데 도움이 되는 제품의 추가 정보를 제공합니다. 이 정보는 고객에게 표시되거나 마켓플레이스에 게시되지는 않습니다.

### <a name="validation-assets"></a>유효성 검사 자산

필요에 따라 Microsoft 유효성 검사 팀이 앱을 구성, 연결 및 테스트하는 데 도움이 되는 지침(최대 3,000자)을 추가합니다. 일반적인 구성 설정, 계정, 매개 변수, 그리고 데이터 연결 옵션을 테스트하는 데 사용할 수 있는 그 밖의 정보를 포함합니다. 이 정보는 유효성 검사 팀만 볼 수 있으며 유효성 검사용으로만 사용됩니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료한 후에는 제품을 제출하여 검토하고 게시할 수 있습니다.

포털의 오른쪽 위 모서리에서 **검토 및 게시**를 선택합니다.

검토 페이지에서 다음을 수행할 수 있습니다.

- 제품의 각 섹션에서 완료 상태를 확인합니다. 제품의 모든 섹션이 완료로 표시될 때까지 게시할 수 없습니다.
  - **시작되지 않음** - 섹션이 시작되지 않았으며 완료해야 합니다.
  - **완료되지 않음** - 섹션에 수정해야 하는 오류가 있거나 추가 정보를 제공해야 합니다. 지침은 이 문서 앞부분의 섹션을 참조하세요.
  - **완료** - 섹션에 필요한 모든 데이터가 있고 오류가 없습니다. 제품을 제출하려면 제품의 모든 섹션이 완료되어야 합니다.
- 앱이 올바르게 테스트되었는지 확인하기 위해 인증 팀에 테스트 지침을 제공합니다. 제품을 이해하는 데 유용한 보충 정보도 제공합니다.

게시하기 위해 제품을 제출하려면 **게시**를 선택합니다.

Microsoft에서 제품의 미리 보기 버전이 검토 및 승인 준비가 되었음을 알려 주는 메일을 보내 드립니다. 공개에 제품을 게시 하려면 파트너 센터로 이동 하 여 **라이브**를 선택 합니다.
