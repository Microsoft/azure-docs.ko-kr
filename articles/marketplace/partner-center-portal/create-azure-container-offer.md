---
title: Azure Container 제품 만들기 - Azure Marketplace
description: 컨테이너 제품을 만들고 Azure Marketplace에 게시하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 06/17/2020
ms.openlocfilehash: 055db043f4e695001b2ee90a47a2531284466314
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815637"
---
# <a name="create-an-azure-container-offer-in-azure-marketplace"></a>Azure Marketplace에서 Azure Container 제품 만들기

이 문서에서는 컨테이너 제품을 만들고 Azure Marketplace에 게시하는 방법을 설명합니다. 시작하기 전에 [파트너 센터에서 상업용 마켓플레이스 계정을 만드세요](create-account.md)(계정이 없는 경우). 계정이 상업용 Marketplace 프로그램에 등록되어 있는지 확인합니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요**를 선택합니다.

3. 개요 페이지에서 **+ 새 제품** > **Azure Container**를 선택합니다.

   ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-azure-container.png)

> [!TIP]
> 제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 온라인 매장에 표시 됩니다. 변경을 수행한 후에는 항상 다시 게시해야 합니다.

### <a name="offer-id-and-alias"></a>제품 ID 및 별칭

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Marketplace 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Marketplace에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 변경할 수 없습니다.

**만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-overview"></a>제품 개요

**제품 개요** 페이지에는 이 제품을 게시하는 데 필요한 단계(완료된 단계 및 예정된 단계)와 각 단계를 완료하는 데 소요되는 시간이 시각적으로 표시됩니다.

이 페이지에서는 제품의 현재 상태에 따라 다른 링크를 표시합니다. 다음은 그 예입니다.

- 제품이 초안 상태인 경우 - [초안 제품 삭제](update-existing-offer.md#delete-a-draft-offer)
- 제품이 라이브 상태인 경우 - [제품 판매 중지](update-existing-offer.md#stop-selling-an-offer-or-plan)
- 제품이 미리 보기 상태인 경우 - [라이브 상태로 전환](publishing-status.md#publisher-approval)
- 게시자 로그 아웃을 완료하지 않은 경우 - [게시 취소](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>제품 설정

다음 단계에 따라 제품을 설정합니다.

### <a name="customer-leads--optional"></a>잠재 고객 - 선택 사항

파트너 센터를 사용하여 제안을 상업용 Marketplace에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결할 수 있습니다. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. **잠재 고객을 보내려는 잠재 고객 대상을 선택합니다**. 파트너 센터에서 지원하는 CRM 시스템은 다음과 같습니다.

   - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
   - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
   - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

   > [!NOTE]
   > CRM 시스템이 위에 나열되지 않은 경우 [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) 또는 [Https 엔드포인트](commercial-marketplace-lead-management-instructions-https.md)를 사용하여 잠재 고객 데이터를 저장한 다음, CRM 시스템으로 데이터를 내보냅니다.

2. 파트너 센터에 게시하는 동안 제품을 잠재 고객 대상에 연결합니다.
3. 잠재 고객 대상에 대한 연결이 제대로 구성되었는지 확인합니다. 파트너 센터에 게시한 후에는 연결의 유효성을 검사하고 테스트 잠재 고객을 보냅니다. 제품을 라이브로 전환하기 전에 미리 볼 때 미리보기 환경에서 직접 제품을 구매하여 잠재 고객 연결을 테스트할 수도 있습니다.
4. 잠재 고객이 손실되지 않도록 잠재 고객 대상에 대한 연결이 업데이트된 상태로 유지되어야 합니다.

몇 가지 추가 잠재 고객 관리 리소스는 다음과 같습니다.

- [잠재 고객 관리 개요](commercial-marketplace-get-customer-leads.md)
- [잠재 고객 관리 FAQ](../lead-management-for-cloud-marketplace.md#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](../lead-management-for-cloud-marketplace.md#publishing-config-errors)
- [잠재 고객 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF(팝업 차단이 해제되어 있는지 확인)

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="properties"></a>속성

이 페이지에서 Marketplace에서 제품을 그룹화하는 데 사용되는 범주와 제품을 지원하는 법적 계약을 정의할 수 있습니다.

#### <a name="category"></a>Category

범주 및 하위 범주를 선택 하 여 제품을 적절 한 마켓플레이스 검색 영역에 넣습니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음**을 선택 합니다.

[제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주 및 하위 범주의 전체 목록을 참조 하세요. 컨테이너는 항상 컨테이너 **아래에 표시 되 고** **컨테이너 이미지** 범주에 표시 됩니다.

#### <a name="legal"></a>법적 정보

제품에 대한 사용 약관을 제공해야 합니다. 옵션에는

- Microsoft 상업용 Marketplace 표준 계약을 사용합니다.
- 사용자 고유의 사용 약관을 제공합니다.

#### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업용 Marketplace 표준 계약

상업용 Marketplace에서 트랜잭션을 용이하게 하기 위해 표준 계약 템플릿을 제공합니다. 표준 계약에 따라 솔루션을 제공하도록 선택할 수 있으며 사용자가 한 번만 확인하고 수락하면 됩니다. 사용자 지정 사용 약관을 만들지 않으려는 경우에 선택할 수 있는 좋은 방법입니다.

표준 계약에 대한 자세한 내용은 [Microsoft 상업용 Marketplace의 표준 계약](../standard-contract.md)을 참조하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF를 다운로드할 수도 있습니다(팝업 차단이 해제되어 있는지 확인).

표준 계약을 사용 하려면 * * Microsoft의 상용 marketplace에 대 한 표준 계약 사용] (.)을 선택 합니다. /standard-contract.md)

> [!NOTE]
> Microsoft 상업용 Marketplace의 표준 계약을 사용하여 제품을 게시한 후에는 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. 표준 계약 또는 사용자 고유의 사용 약관에 따라 솔루션을 제공합니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Microsoft의 상업용 Marketplace의 표준 계약 확인란을 사용하는 방법을 보여 줍니다.":::

##### <a name="your-own-terms-and-conditions"></a>사용자 고유의 사용 약관

사용자 고유의 사용자 지정 약관을 제공하려면 **사용 약관** 상자에 입력합니다. 이 상자에는 텍스트 문자를 제한 없이 입력할 수 있습니다. 고객은 이러한 약관에 동의해야 제품을 사용할 수 있습니다.

다음 섹션인 제품 목록으로 진행하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

여기에서 상업용 Marketplace에 표시되는 제품 세부 정보를 정의합니다. 여기에는 제품 이름, 설명 및 이미지가 포함됩니다.

> [!NOTE]
> 제품 설명이 구로 시작되는 경우 제품 세부 정보를 영어로 지정할 필요가 없습니다. "이 애플리케이션은 [영어가 아닌 언어]로만 제공됩니다." 또한 제품 목록 세부 정보에 사용된 것과 다른 언어로 콘텐츠를 제공하는 유용한 링크를 제공해도 괜찮습니다.

### <a name="name"></a>속성

여기에 입력하는 이름은 제품의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력한 텍스트로 미리 채워집니다. 이 이름은 나중에 변경할 수 있습니다.

이름은 다음과 같습니다.

- 상표로 등록될 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다.
- 50자를 초과할 수 없습니다.
- 이모지를 포함할 수 없습니다.

### <a name="search-results-summary"></a>검색 결과 요약

제품에 대한 간단한 설명입니다. 최대 100자까지 입력할 수 있으며 마켓플레이스 검색 결과에 사용됩니다.

### <a name="long-summary"></a>긴 요약

제품에 대한 자세한 설명입니다. 최대 256자까지 입력할 수 있으며 Marketplace 검색 결과에 사용됩니다.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>개인정보처리방침 링크

조직의 개인정보처리방침 웹 주소를 입력합니다. 제품이 개인 정보 법률 및 규정을 준수하는지 확인해야 합니다. 웹 사이트에 유효한 개인정보처리방침을 게시해야 합니다.

#### <a name="useful-links"></a>유용한 링크

제품에 대한 추가 온라인 문서를 제공합니다. 최대 25개의 링크를 추가할 수 있습니다. 링크를 추가하려면 **+ 링크 추가**를 선택하고 다음 필드를 완료합니다.

- **제목** - 제품의 세부 정보 페이지에 이 제목이 표시됩니다.
- **링크(URL)** - 고객이 온라인 설명서를 볼 수 있는 링크를 입력합니다. 링크는 http:// 또는 https://로 시작되어야 합니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처** 및 **엔지니어링 연락처**의 이름, 이메일 및 전화번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있습니다. CSP(클라우드 솔루션 공급자) 파트너에도 제공될 수 있습니다.

- 지원 연락처(필수 사항): 일반적인 지원 질문입니다.
- 엔지니어링 연락처(필수 사항): 기술 관련 질문 및 인증 문제를 처리할 담당자입니다.
- CSP 프로그램 연락처(선택 사항): CSP 프로그램과 관련된 재판매인 질문입니다.

**지원 연락처** 섹션에서 글로벌 Azure, Azure Government 또는 둘 다에서 제품을 사용할 수 있는지에 따라 파트너가 제품에 대한 지원을 찾을 수 있는 **지원 웹 사이트**를 제공합니다.

**CSP 프로그램 연락처** 섹션에서 CSP 파트너가 제품에 대한 마케팅 자료를 찾을 수 있는 링크(**CSP 프로그램 마케팅 자료**)를 제공합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 Marketplace 목록 리소스

제품 목록을 만드는 방법에 대한 자세한 내용은 [목록에 제품 추가 모범 사례](../gtm-offer-listing-best-practices.md)를 참조하세요.

### <a name="marketplace-images"></a>Marketplace 이미지

제품에 사용할 로고 및 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 흐린 이미지는 거부됩니다.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

#### <a name="store-logos"></a>스토어 로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작고** **중간** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로 바꿀 수 있습니다.

- **큼** (216 x 216에서 350 x 350 px, required)
- **중간** (90 x 90 px, 선택 사항)
- **작음** (48 x 48 px, 선택 사항)

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>스크린샷(선택 사항)

제품의 작동 방식을 보여 주는 최대 5개의 스크린샷을 추가합니다. 각각 1280 x 720 픽셀 크기 및 PNG 형식이어야 합니다.

#### <a name="videos-optional"></a>동영상(선택 사항)

제품을 설명하는 최대 5개의 비디오를 추가합니다. 비디오의 이름, 웹 주소 및 비디오의 썸네일 PNG 이미지를 1280 x 720 픽셀 크기로 입력합니다.

#### <a name="offer-examples"></a>제안 예제

다음 예에서는 제품 목록 필드가 제품의 다른 위치에 표시되는 방법을 보여 줍니다.

Azure Marketplace의 **제품 목록** 페이지를 보여 줍니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Azure Marketplace의 제품 목록 페이지를 보여 줍니다." :::

Azure Marketplace의 검색 결과를 보여 줍니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Azure Marketplace의 검색 결과를 보여 줍니다.":::

Azure Portal의 **제품 목록** 페이지를 보여 줍니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Azure Portal의 제품 목록 페이지를 보여 줍니다.":::

Azure Portal의 검색 결과를 보여 줍니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Azure Portal의 검색 결과를 보여 줍니다.":::

## <a name="preview"></a>미리 보기

미리 보기 탭에서 제품을 라이브로 게시하기 전에 유효성을 검사하기 위해 제한된 **미리 보기 대상**을 선택할 수 있습니다.

> [!IMPORTANT]
> **미리 보기**에서 제품을 본 후 **라이브로 전환**을 선택하여 제품을 공개 게시해야 합니다.

Azure 구독 ID GUID와 각각에 대한 선택적 설명과 함께 미리 보기 대상을 지정합니다. 이러한 필드는 고객에게 표시되지 않습니다.

> [!NOTE]
> Azure 구독 ID는 Azure Portal의 구독 페이지에서 확인할 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로 추가하거나(최대 10개), CSV 파일을 업로드하여 추가합니다(최대 100개). 이러한 구독 ID를 추가하여 제품이 라이브로 게시되기 전에 제품을 미리 볼 수 있는 사용자를 결정합니다. 제품이 이미 라이브 상태인 경우 제품의 변경 내용 또는 업데이트를 테스트하는 미리 보기 대상 그룹을 선택할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="plan-overview"></a>플랜 개요

이 탭에서 동일한 제품 내에서 다른 플랜 옵션을 제공할 수 있습니다. 요금제 (이전의 Sku)는 글로벌 클라우드, 정부 클라우드 및 계획에서 참조 하는 이미지와 같은 사용 가능한 클라우드 측면에서 다를 수 있습니다. 상업용 Marketplace에서 제품을 나열하려면 하나 이상의 플랜을 설정해야 합니다.

각 제품에 대해 최대 100 요금제를 만들 수 있습니다. 최대 45 개는 개인 일 수 있습니다. [Microsoft 상업적 marketplace에서 비공개 제품](../private-offers.md)의 비공개 요금제에 대해 자세히 알아보세요.

플랜을 만들면 **플랜 개요** 탭에 다음과 같은 항목이 표시됩니다.

- 플랜 이름
- 가격 책정 모델
- Azure 지역 (글로벌 또는 정부)
- 현재 게시 상태
- 모든 사용 가능한 작업

플랜 개요에서 사용할 수 있는 작업은 플랜의 현재 상태에 따라 달라집니다. 해당 기능은 아래와 같습니다.

- **초안 삭제** – 플랜 상태가 초안인 경우입니다.
- **판매 플랜 중지** – 플랜 상태가 실시간으로 게시된 경우입니다.

### <a name="create-new-plan"></a>새 플랜 만들기

**새 플랜 만들기**를 선택합니다. **새 플랜** 대화 상자가 표시됩니다.

**플랜 ID** 상자에서 이 제품의 각 플랜에 대한 고유한 플랜 식별자를 만듭니다. 이 ID는 제품 웹 주소의 고객에게 표시됩니다. 소문자와 숫자, 대시 또는 밑줄과 최대 50자만 사용할 수 있습니다.

> [!NOTE]
> **만들기**를 선택한 후에는 플랜 ID를 변경할 수 없습니다.

**플랜 이름** 상자에서 이 플랜에 대한 이름을 입력합니다. 고객이 제품 내에서 선택할 플랜을 확인할 때 이 이름이 표시됩니다. 이 제품의 각 플랜에 대해 고유한 이름을 만듭니다. 예를 들어 **Windows Server 2016** 및 **Windows Server 2019** 계획에 **Windows Server** 제품 이름을 사용할 수 있습니다.

### <a name="plan-setup"></a>플랜 설정

이 탭에서 플랜을 사용할 수 있는 클라우드를 선택할 수 있습니다. 이 탭에서의 사용자의 답변은 다른 탭에 표시되는 필드에 영향을 줍니다.

#### <a name="azure-regions"></a>Azure 지역

Azure Container 제품에 대 한 모든 계획은 **Azure Global**에서 자동으로 사용할 수 있습니다.  요금제는 상업적 marketplace를 사용 하는 모든 글로벌 Azure 지역의 고객이 사용할 수 있습니다. 자세한 내용은 [지리적 가용성 및 통화 지원](../marketplace-geo-availability-currencies.md)을 참조하세요.

[Azure Government](../../azure-government/documentation-government-welcome.md) 옵션을 선택 하 여 솔루션을 여기에 표시 합니다. 이는 미국 연방, 주, 지방 또는 부족 정부 기관과 여기서 일할 자격이 있는 파트너의 고객에 대한 액세스를 제어하는 정부 커뮤니티 클라우드입니다. 게시자는 이 클라우드 커뮤니티에 대한 모든 규정 준수 제어, 보안 조치 및 모범 사례를 담당해야 합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다. 결과와 다를 수 있으므로 Azure Government에 [게시](../../azure-government/documentation-government-manage-marketplace-partners.md)하기 전에 해당 영역 내에서 솔루션을 테스트하고 확인합니다. 솔루션을 만들고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청합니다.

> [!NOTE]
> 계획을 게시 하 고 특정 지역에서 사용할 수 있게 되 면 해당 지역을 제거할 수 없습니다.

#### <a name="azure-government-certifications"></a>Azure Government 인증

이 옵션은 **Azure 지역**에서 **Azure Government** 선택 된 경우에만 볼 수 있습니다.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다.

이러한 프로그램에 대한 인증서를 표시하기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이러한 링크는 프로그램의 목록에 직접 연결하거나 자신의 웹 사이트에 연결할 수 있습니다. 이러한 링크는 Azure Government 고객만 볼 수 있습니다.

### <a name="plan-listing"></a>플랜 목록

이 탭에는 현재 제품 내의 서로 다른 각 플랜에 대한 특정 정보가 표시됩니다.

### <a name="plan-name"></a>플랜 이름

플랜을 만들 때 지정한 이름으로 미리 채워집니다. 필요에 따라 이 이름을 변경할 수 있습니다. 최대 50자까지 지정할 수 있습니다. 이 이름은 Azure Marketplace 및 Azure Portal에서 이 플랜의 제목으로 표시됩니다. 플랜을 사용할 준비가 되면 기본 모듈 이름으로 사용됩니다.

### <a name="plan-summary"></a>플랜 요약

소프트웨어 플랜(제품 아님)에 대한 간략한 요약입니다. 이 요약은 Azure Marketplace 검색 결과에 표시되며 최대 100자까지 포함할 수 있습니다.

### <a name="plan-description"></a>플랜 설명

이 소프트웨어 플랜을 고유하게 만드는 방법과 제품 내 플랜 간의 차이점을 설명합니다. 제품이 아닌 플랜을 설명해야 합니다. 이 설명은 **제품 목록** 페이지의 Azure Portal과 Azure Marketplace에 표시됩니다. 플랜 요약에 입력한 것과 동일한 콘텐츠일 수 있고 최대 2000자까지 포함할 수 있습니다.

이러한 필드를 완료한 후에 **저장**을 선택합니다.

#### <a name="plan-examples"></a>계획 예제

다음 예에서는 플랜 목록 필드를 서로 다른 뷰에 표시하는 방법을 보여 줍니다.

플랜 세부 정보를 볼 때 Azure Marketplace의 필드는 다음과 같습니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Azure Marketplace에서 플랜 세부 정보를 볼 때 표시되는 필드를 보여 줍니다.":::

Azure Portal에 대한 플랜 세부 정보는 다음과 같습니다.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Azure Portal에 대한 플랜 정보를 보여 줍니다.":::

### <a name="plan-availability"></a>플랜 가용성

고객이 Marketplace에서 검색하거나 찾아보거나 구매할 수 없도록 게시된 제품을 숨기려면 **가용성** 탭에서 **플랜 숨기기** 확인란을 선택합니다.

이 필드는 다음과 같은 경우에 사용됩니다.

- 이 제품은 다른 응용 프로그램을 통해 참조 될 때 간접적으로 사용 됩니다.
- 제품을 개별적으로 구매하면 안 됩니다.
- 이 플랜이 초기 테스트에 사용되었으며 더 이상 관련이 없습니다.
- 임시 또는 계절성 제품에 플랜을 사용했으며 더 이상 제공되지 않습니다.

## <a name="technical-configuration"></a>기술 구성

컨테이너 이미지는 프라이빗 [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)에 호스트되어야 합니다. **기술 구성** 탭에서 Azure Container Registry 내의 컨테이너 이미지 리포지토리에 대한 참조 정보를 제공합니다.

제품이 게시된 후에는 컨테이너 이미지가 특정 퍼블릭 컨테이너 레지스트리의 Azure Marketplace로 복사됩니다. 컨테이너 이미지 사용에 대한 모든 요청은 Azure Marketplace 프라이빗 컨테이너 레지스트리가 아닌 퍼블릭 컨테이너 레지스트리에서 제공됩니다. 자세한 내용은 [Azure Container 기술 자산 준비](create-azure-container-technical-assets.md)를 참조하세요.

### <a name="image-repository-details"></a>이미지 리포지토리 세부 정보

**이미지 리포지토리 세부 정보** 탭에서 다음 정보를 제공합니다.

**Azure 구독 ID** – 구독 ID를 제공하여 사용량이 보고되고 컨테이너 이미지를 포함하는 Azure Container Registry에 대한 서비스 요금이 청구될 수 있도록 합니다. 이 ID는 Azure Portal의 [구독 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 찾을 수 있습니다.

**Azure 리소스 그룹 이름** – 컨테이너 이미지로 Azure Container Registry를 포함하는 [리소스 그룹](../../azure-resource-manager/management/manage-resource-groups-portal.md) 이름을 제공합니다. 리소스 그룹은 위의 구독 ID에서 액세스할 수 있어야 합니다. Azure Portal의 [리소스 그룹](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) 페이지에서 이름을 찾을 수 있습니다.

**Azure Container Registry 이름** – 컨테이너 이미지를 포함하는 [Azure Container Registry](../../container-registry/container-registry-intro.md)의 이름을 제공합니다. 컨테이너 레지스트리는 앞에서 제공한 Azure 리소스 그룹에 있어야 합니다. 전체 로그인 서버 이름이 아닌 레지스트리 이름만 포함합니다. 이름에서 **azurecr.io**를 생략해야 합니다. Azure Portal의 [컨테이너 레지스트리 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries)에서 레지스트리 이름을 찾을 수 있습니다.

**Azure Container Registry에 대 한 관리자 사용자 이름** – 컨테이너 이미지가 있는 Azure Container Registry에 연결 된 [관리자 사용자 이름을](../../container-registry/container-registry-authentication.md#admin-account)제공 합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호가 필요합니다. 관리 사용자 이름 및 암호를 가져오려면 Azure CLI(명령줄 인터페이스)를 사용하여 **admin enabled** 속성을 **True**로 설정합니다. 필요에 따라 Azure Portal에서 **관리 사용자**를 **사용**으로 설정할 수 있습니다.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="컨테이너 레지스트리 업데이트 대화 상자를 보여 줍니다.":::

**Azure Container Registry의 암호** – Azure Container Registry와 연결되고 컨테이너 이미지가 있는 관리 사용자 이름의 암호를 제공합니다. 회사에서 레지스트리에 액세스할 수 있도록 하려면 사용자 이름 및 암호가 필요합니다. **Container Registry** > **액세스 키**로 이동하거나 Azure CLI에서 [show 명령](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)을 사용하여 Azure Portal의 암호를 가져올 수 있습니다

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="액세스 키 메뉴를 보여 줍니다.":::

**Azure Container Registry 내 리포지토리 이름입니다**. 이미지를 포함하는 Azure Container Registry 리포지토리의 이름을 제공합니다. 레지스트리에 이미지를 푸시할 때 리포지토리의 이름을 포함합니다. [Container Registry](https://azure.microsoft.com/services/container-registry/) > **리포지토리 페이지**로 이동하여 리포지토리의 이름을 찾을 수 있습니다. 자세한 내용은 [Azure Portal에서 컨테이너 레지스트리 리포지토리 보기](../../container-registry/container-registry-repositories.md)를 참조하세요.

> [!NOTE]
> 이름이 설정되면 변경할 수 없습니다. 계정의 각 제품마다 고유한 이름을 사용합니다.

### <a name="image-tags-for-new-versions-of-your-offer"></a>제품의 새 버전에 대한 이미지 태그

고객은 Azure Marketplace에서 자동으로 업데이트를 받아서 게시할 수 있어야 합니다. 업데이트하지 않으려면 특정 버전의 이미지를 유지할 수 있어야 합니다. 이 작업을 수행하려면 이미지를 업데이트할 때마다 새 이미지 태그를 추가해야 합니다.

### <a name="image-tag"></a>이미지 태그

이 필드는 지원되는 모든 플랫폼에서 최신 버전의 이미지를 가리키는 **최신** 태그를 포함해야 합니다. 또한 버전 태그를 포함해야 합니다(예: xx.xx.xx, xx는 숫자). 고객은 여러 플랫폼을 대상으로 하는 [매니페스트 태그](https://github.com/estesp/manifest-tool)를 사용해야 합니다. 매니페스트 태그에서 참조하는 모든 태그는 업로드할 수 있도록 추가되어야 합니다.

모든 매니페스트 태그(최신 태그 제외)는 X.Y **-** 또는 X.Y.Z로 시작해야 합니다(X, Y, Z는 정수입니다). 예를 들어 **최신** 태그가 1.0.1-linux-x64, 1.0.1-linux-arm32 및 1.0.1-windows-arm32를 가리키는 경우 이러한 6개의 태그를 이 필드에 추가해야 합니다. 자세한 내용은 [Azure Container 기술 자산 준비](create-azure-container-technical-assets.md)를 참조하세요.

> [!NOTE]
> 테스트 중에 이미지를 식별할 수 있도록 이미지에 테스트 태그를 추가해야 합니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료한 후 이를 제출하여 검토하고 게시할 수 있습니다.

포털의 오른쪽 위 모서리에서 **검토 및** **게시**를 선택합니다.

검토 페이지에서 다음을 수행할 수 있습니다.

- 제품의 각 섹션에서 완료 상태를 확인합니다. 제품의 모든 섹션이 완료로 표시될 때까지 게시할 수 없습니다.
  - **시작되지 않음** - 시작되지 않았으며 완료해야 합니다.
  - **완료되지 않음** - 수정해야 하는 오류가 있거나 추가 정보를 제공해야 합니다. 도움말은 이 문서 앞부분의 섹션을 참조하세요.
  - **완료** – 오류 없이 필요한 모든 데이터를 포함합니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료되어야 합니다.
- 제품이 올바르게 테스트되었는지 확인하기 위해 인증 팀에 테스트 지침을 제공합니다. 또한 제품을 이해하는 데 유용한 보충 정보를 제공합니다.

게시하기 위해 제품을 제출하려면 **게시**를 선택합니다.

Microsoft에서 제품의 미리 보기 버전이 검토 및 승인 준비가 되었음을 알려 주는 메일을 보내 드립니다.

공개에 제품을 게시 하려면 파트너 센터로 이동 하 여 **라이브**를 선택 합니다.

## <a name="next-step"></a>다음 단계

- [상업용 마켓플레이스에서 기존 제품 업데이트](update-existing-offer.md)
