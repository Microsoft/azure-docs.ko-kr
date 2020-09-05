---
title: 상업용 Marketplace에서 Dynamics 365 for Operations 제품 만들기
description: Microsoft 파트너 센터의 상업용 Marketplace 포털을 사용하는 CSP(클라우드 솔루션 공급자) 프로그램을 통하거나 Azure Marketplace, AppSource에서 나열 또는 판매하기 위해 새 Dynamics 365 for Operations 제품을 만드는 방법입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 06/17/2020
ms.openlocfilehash: 79bf5f0d6b7e3fa4735d94c7b26c1736ab25113e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817003"
---
# <a name="create-a-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 제품 만들기

이 항목에서는 Dynamics 365 for Operations 제품을 만드는 방법에 대해 설명합니다. [Microsoft Dynamics 365 for Finance and Operations](https://dynamics.microsoft.com/finance-and-operations)는 고급 재무, 운영, 제조 및 공급망 관리를 지원하는 ERP(전사적 자원 관리)입니다. Dynamics 365 for Operations에 대한 모든 제품은 인증 프로세스를 통과해야 합니다.

시작하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만듭니다](create-account.md)(아직 만들지 않은 경우). 계정이 상업용 Marketplace 프로그램에 등록되어 있는지 확인합니다.

>[!NOTE]
> 제품을 게시 한 후에는 게시에 대 한 제품을 다시 제출한 후에만 파트너 센터 및 온라인 스토어에서 제품에 대 한 편집 내용이 업데이트 됩니다.

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **상업용 Marketplace** > **개요**를 선택합니다.
3. 개요 페이지에서 **+ 새 제품** > **Dynamics 365 for Operations**를 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-dynamics-365-operations.png)

> [!NOTE]
> 제품을 게시 한 후에는 제품을 다시 게시 한 후에만 파트너 센터에서 해당 제품에 대 한 편집 내용이 온라인 매장에 표시 됩니다. 변경을 수행한 후에는 항상 다시 게시해야 합니다.

## <a name="new-offer"></a>새 제안

**제품 ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 마켓플레이스 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
- 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 여기에 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Marketplace에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.

제품을 생성하고 계속하려면 **만들기**를 선택합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>잠재 고객이 이 제품 목록과 상호 작용하게 하려면 어떻게 해야 하나요?

이 제품에 사용할 옵션을 선택합니다.

#### <a name="get-it-now-free"></a>지금 받기(무료)

앱에 액세스할 수 있는 올바른 URL(*http* 또는 *https*로 시작)을 제공하여 고객에게 제품을 무료로 제공합니다.  예를 들어 `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>평가판(목록)

`http`평가판을 받을 수 있는 올바른 URL (또는부터 시작)을 제공 하 여 고객에 게 제품을 나열 `https` 합니다. `https://contoso.com/trial/my-app`)을 입력합니다. 평가판을 나열하는 제품은 서비스에 의해 생성, 관리 및 구성되며 Microsoft에서 관리하는 구독을 포함하지 않습니다.

> [!NOTE]
> 평가판 링크를 통해 애플리케이션에서 수신하는 토큰은 앱에서 자동으로 계정을 만드는 Azure AD(Active Directory)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용하는 인증에 Microsoft 계정이 지원되지 않습니다.

#### <a name="contact-me"></a>연락처

CRM(고객 관계 관리) 시스템을 연결하여 고객 연락처 정보를 수집합니다. 고객에게 정보를 공유할 수 있는 권한을 요청합니다. 고객 세부 정보는 제품 이름, ID 및 Marketplace(고객이 제품을 찾은 소스)와 함께 구성된 CRM 시스템으로 전송됩니다. CRM을 구성하는 방법에 대한 자세한 내용은 [잠재 고객](#customer-leads)을 참조하세요.

### <a name="test-drive"></a>시험 사용

시험 사용은 "구입 전 체험" 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. 자세히 알아보려면 [테스트 드라이브인 무엇 인가요?](../what-is-test-drive.md)를 시작 합니다.

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란을 선택 취소합니다.

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조하세요.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

이 페이지에서는 Marketplace에서 제품을 그룹화하는 데 사용되는 범주와 산업, 앱 버전 및 제품을 지원하는 법적 계약을 정의할 수 있습니다.

### <a name="category"></a>Category

범주 및 하위 범주를 선택 하 여 제품을 적절 한 마켓플레이스 검색 영역에 넣습니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음**을 선택 합니다.

[제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주 및 하위 범주의 전체 목록을 참조 하세요.

### <a name="industry"></a>산업

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

제품의 버전 번호를 입력합니다. 이 버전은 제품의 세부 정보 페이지에 표시됩니다.

### <a name="terms-and-conditions"></a>사용 약관

**사용 약관** 필드에 사용자의 법적 사용 약관을 제공합니다. 사용 약관을 찾을 수 있는 URL을 제공할 수도 있습니다. 고객은 이러한 사용 약관에 동의해야 제품을 사용할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

이 페이지에는 제품에서 나열되는 언어가 표시됩니다. 현재 **영어(미국)** 가 유일하게 사용할 수 있는 옵션입니다.

각 언어/시장에서 Marketplace 세부 정보(제품 이름, 설명, 이미지 등)를 정의해야 합니다. 이 정보를 제공하려면 언어/시장 이름을 선택합니다.

> [!NOTE]
> 제품 설명(예: 설명, 문서, 스크린샷, 사용 약관 등)이 "[영어가 아닌 언어]에서만 사용할 수 있습니다."로 시작하는 경우 제품 세부 정보는 영어로 작성되지 않아도 됩니다. 또한 제품 목록 콘텐츠에 사용된 것과 다른 언어로 콘텐츠를 제공하는 *유용한 링크 URL*을 제공할 수 있습니다.

Microsoft AppSource에서 제공 정보를 표시 하는 방법의 예는 다음과 같습니다.

:::image type="content" source="media/example-azure-marketplace-d365-operations.png" alt-text="이 제품이 Microsoft AppSource 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 제품
3. 범주
4. 산업
5. 지원 주소 (링크)
6. 사용 약관
7. 개인 정보 보호 정책
8. Offer name
9. 스크린샷/비디오
10. Description

### <a name="name"></a>속성

여기에 입력하는 이름은 고객에게 제품 목록의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭**에 입력한 텍스트로 미리 채워져 있지만 이 값을 변경할 수 있습니다. 이 이름은 상표로 등록될 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50자를 초과할 수 없으며 이모지는 포함할 수 없습니다.

### <a name="short-description"></a>간단한 설명

제품에 대한 간단한 설명을 최대 100자까지 입력합니다. 이 설명은 Marketplace 검색 결과에서 사용할 수 있습니다.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

### <a name="search-keywords"></a>검색 키워드

필요에 따라 고객이 Marketplace에서 제품을 찾을 수 있도록 최대 3개의 검색 키워드를 입력할 수 있습니다. 최상의 결과를 얻으려면 설명에 이러한 키워드를 사용해 보세요.

### <a name="products-your-app-works-with"></a>앱과 연동되는 제품

고객에게 앱이 특정 제품과 호환된다는 것을 알리려면 여기에 최대 3개의 제품 이름을 입력합니다.

### <a name="support-urls"></a>지원 URL

이 섹션에서 고객이 제품을 이해하는 데 도움이 되는 링크를 제공할 수 있습니다.

#### <a name="help-link"></a>도움말 링크

고객이 제품에 대해 자세히 알아볼 수 있는 URL을 입력합니다.

#### <a name="privacy-policy-url"></a>개인정보처리방침 URL

조직의 개인정보처리방침 URL을 입력합니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공하는 것은 게시자의 책임입니다.

### <a name="contacts"></a>연락처

이 섹션에서는 **지원 연락처** 및 **엔지니어링 연락처**의 이름, 이메일 및 전화번호를 제공해야 합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에게 제공될 수 있습니다.

**지원 연락처** 섹션에서 CSP 파트너가 제품에 대한 지원을 찾을 수 있는 **지원 URL**을 제공합니다.

### <a name="supporting-documents"></a>지원 문서

백서, 브로슈어, 검사 목록, 프레젠테이션 등의 관련 마케팅 문서를 하나 이상(3개까지) 제공합니다. 이러한 문서는 PDF 형식이어야 합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

이 섹션에서 제품을 고객에게 표시할 때 사용되는 로고 및 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식이어야 합니다.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!Note]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

#### <a name="store-logos"></a>스토어 로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작은** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로이를 바꿀 수 있습니다.

- **큼** (216 x 216에서 350 x 350 px, required)
- **작음** (48 x 48 px, 선택 사항)

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [logos-appsource-only](../includes/logos-appsource-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷을 추가합니다. 스크린샷은 하나 이상 필요하며 최대 5 개까지 추가할 수 있습니다. 모든 스크린샷은 1280 x 720픽셀이어야 합니다.

#### <a name="videos"></a>동영상

필요에 따라 제품을 설명하는 비디오를 4개까지 추가할 수 있습니다. 이러한 비디오는 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 항목에 대해 동영상 이름, 해당 URL 및 동영상 썸네일 이미지(1280 x 720픽셀)를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 목록 리소스

[Marketplace 제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="availability"></a>가용성

이 페이지에서는 제품을 사용할 수 있게 하는 위치 및 방법에 대한 옵션을 제공합니다.

### <a name="markets"></a>시장

이 섹션에서 제품을 사용할 수 있는 지역/국가를 지정할 수 있습니다. 이렇게 하려면 **지역/국가 선택** 팝업 창이 표시되는 **지역/국가 편집**을 선택합니다.

기본적으로 시장은 선택되어 있지 않습니다. 제품을 게시할 지역/국가를 하나 이상 선택합니다. 가능한 모든 지역/국가에서 제품을 사용할 수 있도록 하려면 **모두 선택**을 클릭하거나 추가하려는 특정 지역/국가를 선택합니다. 완료되면 **저장**을 선택합니다.

여기에서 선택한 항목은 새로운 합병에만 적용됩니다. 사용자가 특정 지역/국가에 앱을 이미 보유하고 있으며 나중에 해당 지역/국가를 제거하는 경우 해당 지역/국가에서 제품이 이미 있는 사용자는 계속 사용할 수 있지만 해당 지역/국가의 새로운 고객이 제품을 얻을 수는 없습니다.

> [!IMPORTANT]
> 이러한 요구 사항이 여기 또는 파트너 센터에 나열되지 않은 경우에도 로컬 법적 요구 사항을 충족하는 것은 사용자의 책임입니다.

모든 지역/국가를 선택하는 경우에도 지역 법률 및 제한 사항이나 기타 요인을 통해 특정 제품이 일부 국가 및 지역에 나열되지 않을 수 있습니다.

### <a name="preview-audience"></a>미리 보기 대상

제품을 광범위한 Marketplace 제품에 라이브로 게시하기 전에 먼저 제한된 **미리 보기 대상 그룹**에서 사용할 수 있도록 해야 합니다. 여기에서 **숨기기 키**(소문자 및/또는 숫자만 사용하는 임의의 문자열)를 입력합니다. 미리 보기 대상 그룹의 멤버는 이 숨기기 키를 토큰으로 사용하여 Marketplace에서 제품의 미리 보기를 볼 수 있습니다.

그런 다음, 제품을 출시하고 미리 보기 제한을 제거할 준비가 되면 **숨기기 키**를 제거하고 다시 게시합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="technical-configuration"></a>기술 구성

이 페이지에서는 제품에 연결하는 데 사용되는 기술 세부 정보를 정의합니다. 이 연결을 통해 최종 고객을 위한 제품을 프로비저닝할 수 있습니다.

### <a name="solution-identifier"></a>솔루션 식별자

솔루션에 대한 솔루션 식별자(GUID)를 제공합니다.

솔루션 식별자를 찾으려면:

1. Microsoft Dynamics LCS(수명 주기 서비스)에서 **솔루션 관리**를 선택합니다.
2. 솔루션을 선택한 다음 **패키지 개요**에서 **솔루션 식별자**를 찾습니다. 식별자가 비어 있으면 **편집**을 선택하고 패키지를 다시 게시한 후 다시 시도하세요.

### <a name="release-version"></a>릴리스 버전

이 솔루션이 작동하는 Dynamics 365 for Finance and Operations의 버전을 선택합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="test-drive-technical-configuration"></a>시험 사용 기술 구성

이 페이지에서는 고객이 제품을 구입하기 전에 제품을 사용해 볼 수 있도록 시연("시험 사용")을 설정할 수 있습니다. [테스트 드라이브 란?](../what-is-test-drive.md)에서 자세히 알아보세요.

시험 사용을 사용하도록 설정하려면 [제품 설정](#test-drive) 탭에서 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란 선택을 취소합니다.

테스트 드라이브 설정을 마친 후 계속 하려면 **초안 저장** 을 선택 합니다.

## <a name="supplemental-content"></a>추가 콘텐츠

이 페이지에서 제품의 유효성을 검사하는 데 도움이 되는 제품의 추가 정보를 제공할 수 있습니다. 이 정보는 고객에게 표시되거나 Marketplace에 게시되지는 않습니다.

### <a name="validation-assets"></a>유효성 검사 자산

이 섹션에서 [CAR(사용자 지정 분석 보고서)](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/dev-tools/customization-analysis-report)을 업로드합니다. 이 보고서는 미리 정의된 모범 사례 규칙 집합을 기반으로 사용자 지정 및 확장 모델을 분석하여 생성됩니다.

이 파일은 .xls 또는 .xlsx 형식이어야 합니다. 두 개 이상의 보고서가 있는 경우 모든 보고서를 포함하는 .zip 파일을 업로드할 수 있습니다.

### <a name="does-solution-include-localizations"></a>솔루션에 지역화 버전이 포함되어 있나요?

솔루션에서 로컬 표준 및 정책을 사용하도록 설정하는 경우 **예**를 선택합니다(예: 다른 국가/지역에 필요한 다른 급여 규칙을 수용하는 경우). 그렇지 않은 경우 **아니요**를 선택합니다.

### <a name="does-solution-enable-translations"></a>솔루션에서 번역이 가능한가요?

솔루션의 텍스트를 다른 언어로 번역할 수 있는 경우 **예**로 응답합니다. 그렇지 않은 경우 **아니요**를 선택합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 필수 섹션을 모두 완료한 후에는 포털의 오른쪽 위 모서리에서 **검토 및 게시**를 선택합니다.

이 제품을 처음 게시하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대한 완료 상태를 확인합니다.
    - **시작되지 않음** – 섹션이 작업되지 않았으므로 완료되어야 합니다.
    - **완료되지 않음** – 섹션에 수정해야 하는 오류가 있거나 추가 정보를 입력해야 합니다. 섹션으로 돌아가서 업데이트합니다.
    - **완료됨** – 섹션이 완료되고 필요한 모든 데이터가 입력되었으며 오류가 없습니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료됨 상태여야 합니다.
- **인증 참고 사항** 섹션에서 앱을 이해하는 데 도움이 되는 추가 정보뿐만 아니라 앱이 올바르게 테스트되는지 확인하는 테스트 지침을 인증 팀에 제공합니다. 테스트 지침 및 첫 번째 게시를 완료 하는 방법에 대 한 자세한 내용은 [Appsource Dynamics 365 재무 및 작업 기능 유효성 검사](https://docs.microsoft.com/azure/marketplace/dynamics-365-finance-operations-functional-validation)를 참조 하세요.
- **제출**을 선택하여 게시할 제품을 제출합니다. 제품의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 이메일을 보내 드립니다. 파트너 센터로 돌아가서 제품을 공개로 게시 하기 위해 **live live** 를 선택 합니다.

## <a name="next-step"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
