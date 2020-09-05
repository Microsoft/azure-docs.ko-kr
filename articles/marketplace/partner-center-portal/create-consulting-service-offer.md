---
title: 컨설팅 서비스 제품 만들기 - Microsoft 상업용 마켓플레이스
description: 파트너 센터에서 Microsoft AppSource 또는 Azure Marketplace에 컨설팅 서비스 제품을 게시하는 방법을 알아봅니다.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 03fa302d2e8aa173419912b99d60734c7a4e0356
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814267"
---
# <a name="create-a-consulting-service-offer"></a>컨설팅 서비스 제품 만들기

이 문서에서는 [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에 컨설팅 서비스 제품을 게시하는 방법을 설명합니다. AppSource의 Microsoft [Dynamics 365](https://dynamics.microsoft.com/) 및 Power Platform을 기반으로 하는 컨설팅 서비스 제품을 나열합니다. Azure Marketplace의 Microsoft Azure를 기반으로 하는 컨설팅 서비스 제품을 나열합니다. 시작하기 전에 [파트너 센터에서 상업용 마켓플레이스 계정을 만드세요](create-account.md)(계정이 없는 경우). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

제품을 만들기 전에 [컨설팅 서비스 필수 구성 요소](consulting-service-prerequisites.md)에서 필수 구성 요소를 검토합니다.

## <a name="publishing-benefits"></a>게시 이점

상업용 마켓플레이스에 게시할 경우의 이점:

- Microsoft 브랜드를 활용하여 회사를 홍보할 수 있습니다.
- 잠재적으로 AppSource의 Office 365 및 Dynamics 365 사용자는 1억 명이 넘고, Azure Marketplace를 사용하는 조직은 200,000곳이 넘습니다.
- 이러한 마켓플레이스에서 우량 잠재 고객을 확보할 수 있습니다.
- Microsoft 현장 팀 및 통신 판매 팀을 통해 서비스를 홍보할 수 있습니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 마켓플레이스** > **개요**를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **컨설팅 서비스**를 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-consulting-service.png)

>[!NOTE]
>제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 온라인 매장에 표시 됩니다. 변경을 수행한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 제품의 웹 주소에 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
- **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

**만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="connect-lead-management"></a>잠재 고객 관리 시스템 연결

파트너 센터를 사용하여 마켓플레이스에 제품을 게시할 때는 CRM(고객 관계 관리) 또는 마케팅 자동화 시스템에 _연결해야 합니다_. 이렇게 하면 누군가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다.

1. **연결**을 선택하여 잠재 고객을 보낼 위치를 지정합니다. 파트너 센터는 다음과 같은 시스템을 지원합니다.

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
- [리드 관리 faq](../lead-management-for-cloud-marketplace.md#frequently-asked-questions))
- [일반적인 리드 구성 오류](../lead-management-for-cloud-marketplace.md#publishing-config-errors))
- [잠재 고객 관리 개요](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF(팝업 차단이 해제되어 있는지 확인)

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="properties"></a>속성

이 페이지에서는 컨설팅 서비스 솔루션을 사용하기에 가장 적합한 기본 제품을 설정하고, 컨설팅 서비스 유형을 설정하고, 적용 가능한 제품을 선택할 수 있습니다.

1. 드롭다운 목록에서 **기본 제품**을 선택합니다.
2. 드롭다운 목록에서 **컨설팅 서비스 유형**을 선택합니다.

    - **평가**: 솔루션의 적용 가능성을 확인하고 예상 비용 및 타이밍을 제공하기 위한 고객 환경 평가입니다.
    - **브리핑**: 프레임워크, 데모 및 고객 예제를 사용하여 고객의 관심을 끌기 위한 솔루션 또는 컨설팅 서비스를 소개합니다.
    - **구현**: 완벽하게 작동하는 솔루션으로 이어지는 완전한 설치입니다. 2주 이내에 구현할 수 있는 솔루션으로 제한하세요.
    - **개념 증명**: 솔루션이 고객의 요구 사항을 충족하는지 확인하기 위한 제한된 범위 구현입니다.
    - **워크샵**: 고객의 시설에서 수행되는 대화형 참여입니다. 고객의 데이터 또는 환경을 기반으로 하는 교육, 브리핑, 평가 또는 데모가 포함될 수 있습니다.

3. **Azure** 를 기본 제품으로 선택한 경우 최대 3 개의 **솔루션 영역**을 선택 합니다. 이렇게 하면 Azure Marketplace 고객이 제품을 보다 쉽게 찾을 수 있습니다. Azure를 선택하지 않은 경우 이 단계를 건너뜁니다.

    - 분석
    - 앱 현대화
    - 보관
    - AI + 기계 학습
    - Backup
    - 빅 데이터
    - 데이터 플랫폼
    - 데이터 센터 관리
    - DevOps
    - 재해 복구
    - ID
    - 사물 인터넷
    - 마이그레이션
    - 네트워킹
    - 보안
    - Storage

1. **Azure** 를 기본 제품으로 선택한 경우 최대 6 개의 **산업**을 선택할 수 있습니다. 이렇게 하면 Azure Marketplace 고객이 제품을 보다 쉽게 찾을 수 있습니다. [제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)를 참조 하세요. Azure를 선택 하지 않은 경우이 단계를 건너뜁니다.
1. 기본 제품에 Azure가 아닌 *기타*를 선택한 경우 최대 3개의 **적용 가능한 제품**을 선택합니다. 이렇게 하면 AppSource 고객이 제품을 보다 쉽게 찾을 수 있습니다. 자세한 내용은 [Microsoft AppSource 컨설팅 서비스 목록 지침](https://go.microsoft.com/fwlink/?LinkId=828734&amp;clcid=0x409)(PDF)을 참조하세요.
1. Azure *이외의 기본 제품을* 선택한 경우 각 업계에 대해 최대 2 개의 **산업** 및 두 개의 **감축할** 를 선택할 수 있습니다. 이렇게 하면 AppSource 고객이 제품을 보다 쉽게 찾을 수 있습니다. [제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)를 참조 하 여 산업 및 감축할의 전체 목록을 참조 하세요.
1. 회사에서 획득한 역량 중 컨설팅 서비스 제품 목록에 표시할 최대 3개의 **역량**을 추가합니다. Azure Expert MSP 및 Azure 네트워킹 MSP를 제외한 하나 이상의 역량이 필요합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

여기에서 마켓플레이스에 표시되는 제품 세부 정보를 정의합니다. 여기에는 제품 이름, 설명, 이미지 등이 포함됩니다. 이 제품을 구성하는 동안 [상업용 마켓플레이스 인증 정책 페이지](https://docs.microsoft.com/legal/marketplace/certification-policies#800-consulting-services)에 자세히 설명된 정책을 따라야 합니다.

> [!NOTE]
> 제품 설명이 구로 시작되는 경우 제품 세부 정보를 영어로 지정할 필요가 없습니다. &quot;이 애플리케이션은 [영어가 아닌 언어]로만 제공됩니다.&quot; 또한 제품 목록 세부 정보에 사용된 것과 다른 언어로 콘텐츠를 제공하는 유용한 링크를 제공해도 괜찮습니다.

다음은 Azure Marketplace에서 제공 정보를 표시 하는 방법의 예입니다. 나열 된 가격은 예를 들기 위한 것 이며 실제 비용을 반영 하지는 않습니다.

:::image type="content" source="media/example-consulting-service-offer.png" alt-text="이 제품이 Azure Marketplace 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 가격
3. 솔루션 영역
4. 산업
5. Offer name
6. 요약
7. Description
8. 스크린샷/비디오

<br>다음은 Microsoft AppSource에서 제공 정보를 표시 하는 방법의 예입니다. 나열 된 가격은 예를 들기 위한 것 이며 실제 비용을 반영 하지는 않습니다.

:::image type="content" source="media/example-consulting-service-offer-appsource.png" alt-text="이 제품이 Microsoft AppSource 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 가격
3. 제품
4. 산업
5. Offer name
6. 요약
7. Description
8. 스크린샷/비디오
9. 문서

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

### <a name="keywords"></a>키워드

기본 제품 및 컨설팅 서비스와 관련된 검색 키워드를 최대 3개까지 입력합니다. 이렇게 하면 나중에 제품을 쉽게 찾을 수 있습니다.

### <a name="duration"></a>Duration

이러한 고객 소통의 예상 기간을 설정합니다.

### <a name="contact-information"></a>연락처 정보

**기본** 및 **보조 연락처**에 이름, 이메일 및 전화 번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않습니다. Microsoft에 제공되며, CSP(클라우드 솔루션 공급자) 파트너에게 제공될 수 있습니다.

### <a name="supporting-documents"></a>지원 문서

제품의 지원 PDF 문서를 최대 3개(하나 이상) 추가합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

제품에 사용할 로고 및 이미지를 제공합니다. 모든 이미지는 .png 형식이어야 합니다. 흐린 이미지는 거부됩니다.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

#### <a name="store-logos"></a>스토어 로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작은** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로이를 바꿀 수 있습니다.

- **큼** (216 x 216에서 350 x 350 px, required)
- **작음** (48 x 48 px, 선택 사항)

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots-optional"></a>스크린샷(선택 사항)

제품의 작동 방식을 보여 주는 최대 5개의 스크린샷을 추가합니다. 각각 1280 x 720 픽셀 크기 및 PNG 형식이어야 합니다.

#### <a name="videos-optional"></a>동영상(선택 사항)

제품을 설명하는 동영상을 최대 4개까지 추가합니다. 동영상의 이름, 해당 웹 주소(URL) 및 동영상의 썸네일 PNG 이미지(1280 x 720 픽셀 크기)를 입력합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

가격 책정, 시장 및 프라이빗 키와 같은 요소를 정의합니다.

1. **시장**: 제품을 사용할 수 있는 시장을 설정합니다. 제품당 하나의 시장만 선택할 수 있습니다.
    1. 미국 또는 캐나다 시장에서 **편집 상태**(또는 **시/도**)를 선택하여 제품을 사용할 수 있는 위치를 지정합니다.
2. **대상 그룹 미리 보기**: 제품의 프라이빗 대상 그룹을 설정하는 데 사용되는 **숨기기 키**를 구성합니다.
3. **가격 책정**: 제품이 **무료**인지 **유료**인지 지정합니다.

    > [!NOTE]
    > 컨설팅 서비스 제품은 이 목록에만 해당합니다. 모든 트랜잭션은 상업용 마켓플레이스 외부에서 직접 수행됩니다.

4. 유료 제품의 경우 **가격 및 통화**를 지정하고 가격이 **고정** 가격인지, **예상** 가격인지 지정합니다. 예상 가격일 경우 가격에 영향을 주는 요소를 설명에 지정해야 합니다.
5. 계속하기 전에 **초안 저장**을 선택합니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 필수 섹션을 모두 완료한 후에는 제품을 제출하여 검토하고 게시할 수 있습니다.

1. 컨설팅 서비스 제품을 게시할 준비가 되면 **검토 및 게시**를 클릭합니다.
2. 최종 제출 페이지에서 세부 정보를 검토합니다.
3. 필요한 경우 제품의 세부 정보에 대한 설명이 필요하다고 생각되면 인증 팀에게 메모를 남깁니다.
4. 준비가 되면 **제출**을 선택합니다.
5. **제품 개요** 페이지에는 제품의 현재 게시 단계가 표시됩니다.

각 제품 게시 단계의 예상 소요 시간에 대한 자세한 내용은 [상업용 마켓플레이스 제품의 게시 상태 확인](publishing-status.md)을 참조하세요.

## <a name="update-your-existing-consulting-service-offers"></a>기존 컨설팅 서비스 제품 업데이트

- [상업용 마켓플레이스에서 기존 제품 업데이트](update-existing-offer.md)
