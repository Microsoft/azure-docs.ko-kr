---
title: 상업적 Marketplace에서 새로운 Dynamics 365 비즈니스 센터 제품 만들기
description: Microsoft 파트너 센터에서 상용 Marketplace 포털을 사용 하 여 Azure Marketplace, AppSource 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 나열 하거나 판매 하기 위해 새로운 Dynamics 365 Business Central 제품을 만드는 방법입니다.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 4c0467039cf4fefd7625f1146c4bade99b49304d
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048738"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>새 Dynamics 365 비즈니스 센터 제품 만들기

이 항목에서는 새 Dynamics 365 비즈니스 센터 제품을 만드는 방법에 대해 설명 합니다. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) 은 재무, 운영, 공급망, CRM, 프로젝트 관리 및 전자 상거래를 비롯 한 다양 한 비즈니스 프로세스를 처리 하는 ERP (엔터프라이즈 리소스 계획) 시스템입니다. 프리미엄 패키지는 클래식 배포 모델 및 제조도 지원 합니다. Dynamics 365 Business Central의 모든 제안은 인증 프로세스를 통과 해야 합니다.

Dynamics 365 비즈니스 센터 제품을 만들기 시작 하려면 먼저 [파트너 센터 계정을 만들고](./create-account.md) **개요** 페이지가 선택 된 상태로 [상용 마켓플레이스 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)를 열어야 합니다.

![파트너 센터의 상용 마켓플레이스 대시보드](./media/new-offer-overview.png)

>[!Note]
> 제품이 게시 되 면 파트너 센터에서 만든 제품에 대 한 편집 내용은 시스템 에서만 업데이트 되 고 다시 게시 된 후에도 저장 됩니다. 변경을 수행한 후 게시에 대 한 제품을 제출 했는지 확인 하세요.

## <a name="create-a-new-offer"></a>새 제안 만들기

**+ 새 제품** 단추를 선택한 다음 **Dynamics 365 비즈니스 중심** 메뉴 항목을 선택 합니다. **새 제품** 대화 상자가 표시 됩니다.

### <a name="offer-id-and-alias"></a>제품 ID 및 별칭

- **제품 ID**: 계정의 각 제품에 대 한 고유 식별자입니다. 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿 (해당 하는 경우)에 대 한 URL 주소의 고객에 게 표시 됩니다. 제품 ID는 소문자 영숫자 (하이픈 및 밑줄, 공백 없음) 여야 하 고, 50 문자로 제한 되며, **만들기**를 선택한 후에는 변경할 수 없습니다.  예를 들어 여기에서 *테스트-1* 을 입력 하면 제품 URL이 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`됩니다.

- **제품 별칭**: 파트너 센터 내의 제품을 참조 하는 데 사용 되는 이름입니다. 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다. **만들기**를 선택한 후에는이 값을 변경할 수 없습니다.

**제품 ID** 및 **제품 별칭**을 입력 한 후 **만들기**를 선택 합니다. 그러면 제품의 여러 부분에 대해 작업을 수행할 수 있습니다.

## <a name="offer-setup"></a>제품 설정

**제품 설정** 페이지에서 다음 정보를 요청 합니다. 이러한 필드를 완료 한 후에는 **저장** 을 선택 해야 합니다.

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>잠재 고객이이 목록 제공 서비스와 상호 작용 하 게 하려면 어떻게 해야 하나요?

이 제안에 사용할 옵션을 선택 합니다.

#### <a name="get-it-now-free"></a>지금 받기 (무료)

앱에 액세스할 수 있는 올바른 URL ( *http* 또는 *https*로 시작)을 제공 하 여 고객에 게 제품을 무료로 제공 합니다.  예: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>무료 평가판 (목록)

평가판을 받을 수 있는 올바른 URL ( *http* 또는 *https*로 시작)을 제공 하 여 고객에 게 제품을 나열 합니다.  예: `https://contoso.com/trial/my-app` 무료 평가판을 나열 하는 제품은 서비스에서 생성, 관리 및 구성 되며 Microsoft에서 관리 하는 구독을 포함 하지 않습니다.

> [!NOTE]
> 평가판 링크를 통해 응용 프로그램에서 수신 하는 토큰은 앱에서 계정 만들기를 자동화 하는 Azure Active Directory (Azure AD)를 통해 사용자 정보를 가져오는 데만 사용할 수 있습니다. 이 토큰을 사용 하는 인증에 대해서는 Microsoft 계정이 지원 되지 않습니다.

#### <a name="contact-me"></a>연락처

CRM (고객 관계 관리) 시스템을 연결 하 여 고객 연락처 정보를 수집 합니다. 고객에 게 정보를 공유할 수 있는 권한을 요청 하는 메시지가 표시 됩니다. 제품을 찾은 제품 이름, ID 및 marketplace 원본과 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. CRM을 구성 하는 방법에 대 한 자세한 내용은 [연결 리드 management](#connect-lead-management)를 참조 하세요. 

### <a name="test-drive"></a>시험 사용

시험 사용한 고객에 게 제품을 고객에 게 제공 하는 좋은 방법은 ' 구매 전 시도 '를 선택 하 여 변환 및 높은 우량 잠재 고객을 생성 하는 옵션을 제공 하는 것입니다. [테스트 드라이브에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

테스트 드라이브를 사용 하도록 설정 하려면 **테스트 드라이브 사용** 확인란을 선택 합니다. 그런 다음, [테스트 드라이브 기술 구성](#test-drive-technical-configuration) 에서 데모 환경을 구성 해야 합니다 .이 구성에서는 고객이 고정 기간 동안 제품을 사용해 볼 수 있습니다. 

#### <a name="type-of-test-drive"></a>테스트 드라이브의 유형입니다.

다음 옵션 중에서 선택합니다.

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : 솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용 합니다.
- **[Dynamics 365 For Business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : Microsoft는 회사 중앙 엔터프라이즈 리소스 계획 시스템 (재무, 운영, 공급망, CRM 등)에 대해 테스트 드라이브 서비스 (프로 비전 및 배포 포함)를 호스트 하 고 유지 관리 합니다.  
- **[Dynamics 365 For Customer engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : Microsoft는 고객 참여 시스템 (판매, 서비스, 프로젝트 서비스, 현장 서비스 등)에 대해 테스트 드라이브 서비스 (프로 비전 및 배포 포함)를 호스팅하고 유지 관리 합니다.  
- **[운영에 대 한 Dynamics 365](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : Microsoft는 재무 및 운영 enterprise 리소스 계획 시스템 (재무, 운영, 제조, 공급망 등)에 대 한 테스트 드라이브 서비스 (프로 비전 및 배포 포함)를 호스트 하 고 유지 관리 합니다. 
- **[논리 앱](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : 모든 복합 솔루션 아키텍처를 포괄 하는 배포 템플릿입니다. 모든 사용자 지정 제품은이 유형의 테스트 드라이브를 사용 해야 합니다.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : 사용자 지정 작성 된 대시보드에 대 한 포함 링크입니다. 대화형 Power BI 시각적 개체를 시연 하려는 제품은이 유형의 테스트 드라이브를 사용 해야 합니다. 포함된 Power BI URL만 업로드하면 됩니다.

#### <a name="additional-test-drive-resources"></a>추가 테스트 드라이브 리소스

- [테스트 드라이브 기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [테스트 드라이브 마케팅 모범 사례](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [시험 드라이브 개요 1 호출기](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>리드 관리 연결

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [리드 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조 하세요.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

## <a name="properties"></a>속성

**속성** 페이지를 통해 marketplace에서 제품을 그룹화 하는 데 사용 되는 범주와 산업, 앱 버전 및 제품을 지 원하는 법적 계약을 정의할 수 있습니다. 이 페이지를 완료 한 후 **저장** 을 선택 합니다.

### <a name="category"></a>범주

제품을 적절 한 마켓플레이스 검색 영역에 추가 하는 데 사용 되는 최소 1 개 및 최대 3 개 범주를 선택 합니다. 제품이 제품 설명에서 이러한 범주를 어떻게 지원 하는지 확인 해야 합니다. 

### <a name="industry"></a>산업

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

제품의 버전 번호를 입력 합니다. 이 버전은 제품의 세부 정보 페이지에 표시 됩니다.

### <a name="terms-and-conditions"></a>사용 약관

사용 **약관** 필드에 고유한 약관을 제공 합니다. 사용 약관을 찾을 수 있는 URL을 제공할 수도 있습니다. 고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

## <a name="offer-listing"></a>제품 목록

제품 목록 페이지에는 제품이 나열 될 언어가 표시 됩니다. 현재 **영어 (미국)** 만 사용할 수 있는 옵션입니다.

각 언어/시장에서 marketplace 세부 정보 (제품 이름, 설명, 이미지 등)를 정의 해야 합니다. 이 정보를 제공 하려면 언어/시장 이름을 선택 합니다.

> [!NOTE]
> 제품 설명 (예: 설명, 문서, 스크린샷, 사용 약관 등)은 영어로 시작 하지 않아도 됩니다. "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다." 또한 제품 목록 콘텐츠에 사용 되는 것 이외의 언어로 콘텐츠를 제공 하는 *데 유용한 링크 URL* 을 제공 하는 것도 허용 됩니다.

### <a name="name"></a>이름

여기에 입력 하는 이름은 고객에 게 제품 목록의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제공 별칭** 에 입력 한 텍스트로 미리 채워져 있지만이 값을 변경할 수 있습니다. 이 이름은 상표 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50 자를 초과할 수 없으며, 모든 emojis를 포함할 수 없습니다.

### <a name="short-description"></a>간단한 설명

Marketplace 검색 결과에서 사용할 수 있는 제품에 대 한 간략 한 설명을 제공 합니다 (최대 100 자).

### <a name="description"></a>설명

제품에 대 한 더 긴 설명 (최대 3000 자)을 제공 합니다. 이 설명은 marketplace 목록 개요에서 고객에 게 표시 됩니다. 제품의 가치 제안, 주요 이점, 범주 및/또는 업계 연결, 앱 내 구매 기회 및 필요한 공개를 포함 합니다. 

설명 작성에 대 한 몇 가지 팁은 다음과 같습니다.  

- 설명의 처음 몇 문장에서 제품의 가치 제안을 명확하게 설명합니다. 값 제안에 다음 항목을 포함 합니다.
  - 제품 설명
  - 제품을 활용 하는 사용자의 유형입니다.
  - 고객이 요구 하거나 제품의 문제를 해결 하는 어려움
- 처음 몇 문장은 검색 엔진 결과에 표시될 수 있다는 점에 유의하세요.  
- 제품을 판매하는 특징과 기능에 의존하지 않습니다. 대신, 전달하는 가치에 중점을 둡니다.  
- 산업별 어휘 또는 혜택 기반 단어를 최대한 많이 사용합니다.
- HTML 태그를 사용 하 여 설명의 서식을 지정 하 고 더 유용 하 게 만들어 보세요.

제품 설명을 보다 유용 하 게 사용 하려면 서식 있는 텍스트 편집기를 사용 하 여 설명의 서식을 지정 합니다.

![서식 있는 텍스트 편집기 사용](./media/text-editor2.png)

서식 있는 텍스트 편집기를 사용 하려면 다음 지침을 따르십시오.

- 콘텐츠 형식을 변경 하려면 아래와 같이 서식을 지정할 텍스트를 강조 표시 하 고 텍스트 스타일을 선택 합니다.

     ![서식 있는 텍스트 편집기를 사용 하 여 텍스트 형식 변경](./media/text-editor3.png)

- 글머리 기호 또는 번호 매기기 목록을 텍스트에 추가 하려면 아래 옵션을 사용 합니다.

     ![서식 있는 텍스트 편집기를 사용 하 여 목록 추가](./media/text-editor4.png)

- 텍스트에 들여쓰기를 추가 하거나 제거 하려면 아래 옵션을 사용 합니다.

     ![서식 있는 텍스트 편집기를 사용 하 여 들여쓰기](./media/text-editor5.png)

### <a name="search-keywords"></a>검색 키워드

필요에 따라 고객이 marketplace에서 제품을 찾을 수 있도록 최대 3 개의 검색 키워드를 입력할 수 있습니다. 최상의 결과를 위해 설명에 이러한 키워드를 사용 해 보세요.

### <a name="products-your-app-works-with"></a>앱과 연동되는 제품

앱이 특정 제품에서 작동 하는 것을 고객에 게 알리려면 여기에 최대 3 개의 제품 이름을 입력 합니다.

### <a name="support-urls"></a>지원 Url

이 섹션에서는 고객이 제품에 대해 더 이해 하는 데 도움이 되는 링크를 제공할 수 있습니다.

#### <a name="help-link"></a>도움말 링크

고객이 제품에 대해 자세히 알아볼 수 있는 URL을 입력 합니다.

#### <a name="privacy-policy-url"></a>개인 정보 취급 방침 URL

조직의 개인 정보 취급 방침에 대 한 URL을 입력 합니다. 사용자는 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 하 고 유효한 개인 정보 취급 방침을 제공 하는 일을 담당 합니다.

### <a name="contacts"></a>Contacts

이 섹션에서는 **지원 연락처** 와 **엔지니어링 연락처**에 대 한 이름, 전자 메일 및 전화 번호를 제공 해야 합니다. 이 정보는 고객에 게 표시 되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에 게 제공 될 수 있습니다.

**지원 연락처** 섹션에서 CSP 파트너가 제품에 대 한 지원을 찾을 수 있는 **지원 URL** 도 제공 해야 합니다.

### <a name="supporting-documents"></a>지원 문서

백서, 브로슈어, 검사 목록, 프레젠테이션 등의 관련 마케팅 문서를 하나 이상 제공 합니다. 이러한 문서는 .pdf 형식 이어야 합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

이 섹션에서는 고객에 게 제품을 표시할 때 사용 되는 로고 및 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식 이어야 합니다.

#### <a name="store-logos"></a>매장 로고

**소형 (48 x 48)** 및 **큼 (216 x 216)** 의 두 가지 크기로 제품 로고를 제공 합니다.

#### <a name="hero"></a>Hero

주인공 이미지는 선택 사항입니다. 제공 하는 경우 815 x 290 픽셀을 측정 해야 합니다.

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷 추가 3 개 이상의 스크린샷을 필요 하며 최대 5 개까지 추가할 수 있습니다. 모든 스크린샷 1280 x 720 픽셀 이어야 합니다.

#### <a name="videos"></a>비디오

필요에 따라 제품을 보여 주는 비디오를 최대 5 개까지 추가할 수 있습니다. 이러한 비디오는 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 항목에 대해 비디오 이름, 해당 URL 및 비디오의 미리 보기 이미지 (1280 x 720 픽셀)를 입력 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

- [Marketplace 제품 목록에 대 한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>가용성

**가용성** 페이지에서는 제품을 사용할 수 있도록 하는 위치 및 방법에 대 한 옵션을 제공 합니다.

### <a name="markets"></a>시장

이 섹션에서는 제품을 사용할 수 있는 시장을 지정할 수 있습니다. 이렇게 하려면 시장 **편집**을 선택 합니다. 그러면 **시장 선택** 팝업 창이 표시 됩니다.

기본적으로 시장을 선택 하지 않지만 제품을 게시 하려면 시장을 하나 이상 선택 해야 합니다. **모두 선택** 을 클릭 하 여 가능한 모든 시장에서 제품을 사용할 수 있도록 하거나 추가 하려는 특정 시장을 선택 합니다. 완료 되 면 **저장**을 선택 합니다.

여기에서 선택한 항목은 새로운 합병에만 적용 됩니다. 사용자가 특정 시장에 앱을 이미 보유 하 고 있으며 나중에 해당 시장을 제거 하는 경우 해당 시장에서 제품이 이미 있는 사용자는 계속 사용할 수 있지만 해당 시장의 새로운 고객이 제품을 얻을 수는 없습니다.

> [!IMPORTANT]
> 이러한 요구 사항이 여기 나 파트너 센터에 나열 되지 않은 경우에도 로컬 법적 요구 사항을 충족 하는 것은 사용자의 책임입니다.

모든 시장, 지방 법률, 제한 또는 기타 요인을 선택 하더라도 일부 국가 및 지역에는 특정 제품이 나열 되지 않을 수 있습니다.

### <a name="preview-audience"></a>사용자 미리 보기

제품을 광범위 한 marketplace 제품에 게시 하기 전에 먼저 제한 된 **미리 보기 대상**사용자가 사용할 수 있도록 해야 합니다. 여기에서 **숨기기 키** (소문자 및/또는 숫자만 사용 하는 문자열)를 입력 합니다. 미리 보기 대상의 멤버는이 숨김 키를 토큰으로 사용 하 여 marketplace에서 제품의 미리 보기를 볼 수 있습니다.

그런 다음 제품을 사용할 수 있도록 설정 하 고 미리 보기 제한을 제거할 준비가 되 면 **숨기기 키** 를 제거 하 고 다시 게시 해야 합니다.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 페이지에서는 제품에 연결 하는 데 사용 되는 기술 세부 정보를 정의 합니다. 이 연결을 통해 최종 고객을 위해 제품을 프로 비전 할 수 있습니다.

### <a name="package-type"></a>패키지 유형

제안에 적용 되는 옵션을 선택 합니다.

- **추가**기능: 추가 기능 앱은 Dynamics 365 비즈니스 중부의 경험 및 기존 기능을 확장 합니다. 자세한 내용은 [추가 기능 앱](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps)을 참조 하세요.
- **연결**: Dynamics 365 비즈니스 중부와 타사 솔루션 또는 서비스 간에 지점 간 연결을 설정 해야 하는 시나리오에서 connect 앱을 사용할 수 있습니다. 자세한 내용은 [연결](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps)을 참조 하세요.

### <a name="file-upload"></a>파일 업로드

위에서 **추가** 를 선택한 경우 제품의 패키지 파일 및 종속성이 있는 모든 확장에 대 한 패키지 파일을 업로드 합니다.

#### <a name="extensions-package-file"></a>확장 패키지 파일

제품에 대 한 확장 패키지 파일 (app.config) 파일을 업로드 합니다.

#### <a name="library-package-file"></a>라이브러리 패키지 파일

제품을 marketplace에 게시 되지 않는 다른 확장과 함께 설치 해야 하는 경우에 필요 합니다. 그렇다면 해당 앱 파일을 여기에 업로드 합니다.

#### <a name="dependency-package-file"></a>종속성 패키지 파일

제품을 marketplace에 이미 게시 된 다른 확장과 함께 설치 해야 하는 경우에 필요 합니다. 이 경우 `.app` 또는 `.zip` 파일을 여기에 업로드 합니다.

### <a name="url-to-app-installation"></a>앱 설치 URL

위의 **연결** 을 선택한 경우 여기에 앱 설치 URL을 제공 합니다.

## <a name="test-drive-technical-configuration"></a>시험 드라이브 기술 구성

[제품 설치](#offer-setup) 페이지에서 **테스트 드라이브 사용** 을 선택한 경우 고객이 제품의 시험 사용을 경험할 수 있도록 여기에 세부 정보를 제공 해야 합니다.

**시험** 사용 페이지에서는 고객이 제품을 구입 하기 전에 제품을 사용해 볼 수 있도록 데모 (또는 "시험 사용")를 설정할 수 있습니다. [테스트 드라이브인 이란?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)문서에서 자세히 알아보세요. 제품에 대 한 시험 사용을 더 이상 제공 하지 않으려는 경우 **[제품 설치](#offer-setup)** 페이지로 돌아가 **테스트 드라이브 사용**을 선택 취소 합니다.

각각의 기술 구성 요구 사항을 포함 하는 다음과 같은 종류의 테스트 드라이브를 사용할 수 있습니다.

- [Azure 리소스 관리자](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [논리 앱](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (기술 구성이 필요 하지 않음)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Azure Resource Manager 테스트 드라이브에 대 한 기술 구성

솔루션을 구성 하는 모든 Azure 리소스를 포함 하는 배포 템플릿입니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용 합니다. [Azure Resource Manager 테스트 드라이브](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)를 설정 하는 방법에 대해 자세히 알아보세요.

- **지역** (필수): 현재 테스트 드라이브를 사용할 수 있는 26 개의 Azure 지원 지역이 있습니다. 일반적으로 가장 큰 수의 고객을 예상 하는 지역에서 테스트 드라이브를 사용할 수 있도록 하 여 최상의 성능을 위해 가장 가까운 지역을 선택할 수 있게 합니다. 구독에서 선택 하는 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인 해야 합니다.

- **인스턴스**: 제품을 사용할 수 있는 지역 수를 곱하여 유형 (핫 또는 콜드) 및 사용할 수 있는 인스턴스 수를 선택 합니다.

**핫**:이 유형의 인스턴스가 배포 되 고 선택한 지역에 따라 액세스를 대기 합니다. 고객은 배포를 기다릴 필요 없이 테스트 드라이브의 *핫* 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객이 전체 배포를 기다리지 않고 *핫* 인스턴스를 사용할 수 없는 경우 고객 사용에 대 한 삭제를 원하지 않으므로 *핫* 인스턴스를 하나 이상 사용 하는 것이 좋습니다.

**콜드**:이 유형의 인스턴스는 지역별 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 시험 드라이브를 요청할 때 배포 하기 위해 전체 테스트 드라이브 리소스 관리자 템플릿이 필요 하므로 *콜드* 인스턴스는 *핫* 인스턴스 보다 로드 속도가 훨씬 느립니다. 이는 테스트 드라이브의 기간에 대해서만 비용을 지불 하면 되므로 항상 *핫* 인스턴스와 마찬가지로 Azure 구독에서 실행 *되지 않습니다* .

- **테스트 드라이브 Azure Resource Manager 템플릿**: Azure Resource Manager 템플릿이 포함 된 .Zip을 업로드 합니다.  빠른 시작 문서 [Azure Portal를 사용 하 여 Azure Resource Manager 템플릿을 만들고 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)하는 방법에 대 한 자세한 정보를 Azure Resource Manager.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 시간 (시간)을 입력 합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 이 기간은 전체 시간에 의해서만 설정 될 수 있습니다 (예: "2" 시간, "1.5"은 유효 하지 않음).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Dynamics 365 시험 드라이브에 대 한 기술 구성

Microsoft는이 유형의 테스트 드라이브를 사용 하 여 서비스 프로 비전 및 배포를 호스팅하고 유지 관리 하 여 테스트 드라이브를 설정 하는 복잡성을 제거할 수 있습니다. 이 유형의 호스트 된 테스트 드라이브에 대 한 구성은 테스트 드라이브가 비즈니스 중부, 고객 참여 또는 작업 대상을 대상으로 하는지 여부에 관계 없이 동일 합니다.

- **최대 동시 테스트 드라이브** 수 (필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정 합니다. 테스트 드라이브가 활성화 된 상태에서 각 동시 사용자는 Dynamics 365 라이선스를 사용 하므로 최대 제한 집합을 지 원하는 데 사용할 수 있는 충분 한 라이선스가 있는지 확인 해야 합니다. 권장 값은 3~5입니다.

- **테스트 드라이브 기간** (필수): 시간 수를 정의 하 여 테스트 드라이브가 활성 상태로 유지 되는 시간을 입력 합니다. 이 시간이 지난 후에는 세션이 종료 되 고 더 이상 라이선스 중 하나를 사용 하지 않습니다. 제품의 복잡도에 따라 2-24 시간 값을 설정 하는 것이 좋습니다. 이 기간은 전체 시간에 의해서만 설정 될 수 있습니다 (예: "2" 시간, "1.5"은 유효 하지 않음).  사용자는 시간이 부족 하 고 테스트 드라이브에 다시 액세스 하려는 경우 새 세션을 요청할 수 있습니다.

- **인스턴스 URL** (필수): 고객이 테스트 드라이브를 시작 하는 url입니다. 일반적으로 샘플 데이터 (예: https://testdrive.crm.dynamics.com))가 설치 된 앱을 실행 하는 Dynamics 365 인스턴스의 URL입니다.

- **인스턴스 웹 API url** (필수): Microsoft 365 계정에 로그인 하 고 **설정** \&gt;로 이동 하 여 DYNAMICS 365 인스턴스의 Web API url을 검색 합니다. **사용자 지정** \&gt; **개발자 리소스** \&gt; **인스턴스 웹 API (서비스 루트 URL)** 에서 여기에 있는 URL을 복사 합니다 (예: https://testdrive.crm.dynamics.com/api/data/v9.0)).

- **역할 이름** (필수): 테스트 드라이브 (예: 테스트 드라이브-역할) 중에 사용자에 게 할당 되는 사용자 지정 Dynamics 365 테스트 드라이브에서 정의한 보안 역할 이름을 제공 합니다.

### <a name="technical-configuration-for-logic-app-test-drive"></a>논리 앱 테스트 드라이브에 대 한 기술 구성

모든 사용자 지정 제품은 다양 한 복잡 한 솔루션 아키텍처를 포함 하는이 유형의 테스트 드라이브 배포 템플릿을 사용 해야 합니다. 논리 앱 테스트 드라이브를 설정 하는 방법에 대 한 자세한 내용은 GitHub의 [작업](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) 및 [고객 참여](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) 를 참조 하세요.

- **지역** (필수, 단일 선택 드롭다운 목록): 현재 테스트 드라이브를 사용할 수 있는 26 개의 Azure 지원 지역이 있습니다. 논리 앱에 대 한 리소스는 선택한 지역에 배포 됩니다. 논리 앱에 특정 지역에 저장 된 사용자 지정 리소스가 있는 경우 여기에서 해당 지역이 선택 되어 있는지 확인 합니다. 가장 좋은 방법은 포털의 Azure 구독에서 논리 앱을 로컬로 완전히 배포 하 고이를 선택 하기 전에 제대로 작동 하는지 확인 하는 것입니다.

- **최대 동시 테스트 드라이브** 수 (필수): 한 번에 테스트 드라이브를 사용할 수 있는 최대 고객 수를 설정 합니다. 이러한 테스트 드라이브는 이미 배포 되어 있으므로 고객이 배포를 기다리지 않고 즉시 액세스할 수 있습니다.

- **테스트 드라이브 기간** (필수): 테스트 드라이브가 활성 상태로 유지 되는 시간 (시간)을 입력 합니다. 이 기간이 종료 되 면 테스트 드라이브가 자동으로 종료 됩니다.

- **Azure 리소스 그룹 이름** (필수): 논리 앱 테스트 드라이브가 저장 된 [azure 리소스 그룹](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) 이름을 입력 합니다.

- **Azure 논리 앱 이름** (필수): 사용자에 게 테스트 드라이브를 할당 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

- **논리 앱 이름 프로 비전** 해제 (필수): 고객이 완료 되 면 테스트 드라이브를 프로 비전 하는 논리 앱의 이름을 입력 합니다. 이 논리 앱은 위의 Azure 리소스 그룹에 저장 해야 합니다.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Power BI 테스트 드라이브에는 기술 구성이 필요 하지 않습니다.

대화형 Power BI 시각적 개체를 시연 하려는 제품은 포함 된 링크를 사용 하 여 사용자 지정 된 대시보드를 테스트 드라이브로 공유할 수 있으며 추가 기술 구성은 필요 하지 않습니다. [Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview) 템플릿 앱 설정에 대해 자세히 알아보세요.

### <a name="deployment-subscription-details"></a>배포 구독 정보

사용자를 대신 하 여 테스트 드라이브를 배포 하기 위해 별도의 고유한 Azure 구독을 만들고 제공 합니다. (Power BI 테스트 드라이브에는 필요 하지 않음)

- **Azure 구독 id** (Azure Resource Manager 및 논리 앱에 필요): 리소스 사용량 보고 및 청구를 위해 azure 계정 서비스에 대 한 액세스 권한을 부여 하는 구독 Id를 입력 합니다. 아직 없는 경우 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 왼쪽 메뉴의 **구독** 탭으로 이동 하 여 Azure 구독 ID를 찾을 수 있습니다. 이 탭을 선택 하면 구독 ID (예: "a83645ac-1234-5ab6-6789-1h234g764ghty")가 표시 됩니다.

- **AZURE ad 테넌트 id** (필수): AZURE ACTIVE DIRECTORY (AD) [테넌트 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 * * 속성을 선택 하 고 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용 하 여 조직의 테넌트 ID를 조회할 수도 있습니다.

- **AZURE ad 테넌트 이름** (동적 365에 필요): AZURE ACTIVE DIRECTORY (AD) 이름을 입력 합니다. 이 이름을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고, 오른쪽 위 모서리에 있는 테넌트 이름이 계정 이름 아래에 나열 됩니다.

- **AZURE ad 앱 id** (필수): AZURE ACTIVE DIRECTORY (AD) [응용 프로그램 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 **앱 등록**를 선택 하 고 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다.

- **AZURE ad 앱 클라이언트 암호** (필수): azure ad 응용 프로그램 [클라이언트 암호](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)를 입력 합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택 하 고 **앱 등록**을 선택한 다음 테스트 드라이브 앱을 선택 합니다. 그런 다음 **인증서 및 비밀**을 선택 하 고, **새 클라이언트 암호**를 선택 하 고, 설명을 입력 하 고, **만료**됨 **을 선택 하** 고, **추가**를 선택 합니다. 값을 복사 해야 합니다. 이 작업을 수행 하기 전에 페이지에서 다른 곳으로 이동 하지 마세요. 그렇지 않으면 값에 액세스할 수 없습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

### <a name="test-drive-marketplace-listings"></a>테스트 드라이브 마켓플레이스 목록

**시험** 사용 탭에 있는 **Marketplace 목록** 옵션은 테스트 드라이브를 사용할 수 있는 언어를 표시 합니다. 현재 **영어 (미국)** 는 사용할 수 있는 유일한 위치입니다. 언어 이름을 선택 하 여 테스트 드라이브 환경을 설명 하는 정보를 입력 합니다.

- **설명** (필수): 시험 사용에 대 한 설명, 표시 될 내용, 사용자가 시험해 볼 기능, 탐색할 기능 및 사용자가 제품을 확보할 지 여부를 결정 하는 데 도움이 되는 모든 관련 정보를 설명 합니다. 이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 

- **액세스 정보** (Azure Resource Manager 및 논리 시험 사용에 필요):이 테스트 드라이브에 액세스 하 고 사용 하기 위해 고객이 알아야 할 사항에 대해 설명 합니다. 제품 사용에 대 한 시나리오와 고객이 테스트 드라이브를 통해 기능에 액세스 하기 위해 알아야 하는 사항을 정확히 살펴봅니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서** (필수): 테스트 드라이브 환경을 자세히 연습 합니다. 사용자 설명서는 시험 사용에 대 한 고객의 의견을 정확 하 게 파악 하 고 있을 수 있는 질문에 대 한 참조로 사용할 수 있습니다. 업로드 후 파일은 PDF 형식 이어야 하며 (최대 255 자) 이름이 지정 되어야 합니다.

- **비디오: 비디오 추가** (선택 사항): 비디오를 YouTube 또는 Vimeo에 업로드 하 고 링크 및 미리 보기 이미지 (533 x 324 픽셀)를 사용 하 여 여기에서 참조할 수 있으므로 고객이 제품의 기능을 성공적으로 사용 하는 방법을 비롯 하 여 테스트 드라이브를 보다 잘 이해 하는 데 도움이 되는 정보를 볼 수 있습니다.
  - **이름** (필수)
  - **URL (YouTube 또는 Vimeo만 해당)** (필수)
  - **축소판 그림 (533 x 324 px)** : 이미지 파일은 PNG 형식 이어야 합니다.

## <a name="supplemental-content"></a>추가 콘텐츠

이 페이지에서는 제품의 유효성을 검사 하는 데 도움이 되는 제품에 대 한 추가 정보를 제공할 수 있습니다. 이 정보는 고객에 게 표시 되거나 marketplace에 게시 되지 않습니다.

### <a name="target-release"></a>대상 릴리스

솔루션이 대상으로 하는 Microsoft Dynamics Business Central의 릴리스 ( **현재**, **다음 주**또는 **다음 부**)를 표시 합니다. 이 정보를 사용 하 여 솔루션을 적절 하 게 테스트할 수 있습니다.

### <a name="supported-editions"></a>지원되는 버전

제품에 Microsoft Dynamics 365 Business Central의 Premium 버전이 필요한 경우 **premium** 만을 선택 합니다. 그렇지 않으면 **Essentials** 및 **Premium**을 모두 선택 합니다.

### <a name="key-usage-scenario"></a>주요 사용 시나리오

제품의 키 사용 시나리오가 문서 (.pdf 형식)에 나열 되어 있는 `.pdf` 파일을 업로드 해야 합니다. Marketplace에 대 한 제품을 승인 하기 전에 유효성 검사 팀에서 여기에 나열 된 모든 시나리오를 확인할 수 있습니다.

### <a name="app-tests-automation"></a>앱 테스트 자동화

선택적으로 **앱 테스트 자동화** 파일 (. 앱)을 업로드할 수 있습니다.

### <a name="test-accounts"></a>테스트 계정

인증 팀이 제품을 제대로 검토 하기 위해 테스트 계정이 필요한 경우에는 **테스트 계정** 정보를 사용 하 여 .pdf, .doc 또는 .docx 파일을 업로드 합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 필수 섹션을 모두 완료 한 후 포털의 오른쪽 위 모서리에서 **게시** 를 선택 합니다. **검토 및 게시** 페이지로 리디렉션됩니다. 

이 제품을 처음 게시 하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다.
    - *시작 되지 않음* -섹션이 작업 되지 않아 완료 해야 함을 의미 합니다.
    - *불완전* -섹션에 수정 해야 하거나 더 많은 정보를 제공 해야 하는 오류가 있음을 의미 합니다. 섹션으로 돌아가서 업데이트 합니다.
    - *완료* -섹션이 완료 되었고 필요한 모든 데이터가 제공 되었으며 오류가 없음을 의미 합니다. 제품의 모든 섹션은 제품을 제출 하기 전에 완전 한 상태 여야 합니다.
- **인증에 대 한 참고 사항** 섹션에서는 앱을 이해 하는 데 도움이 되는 보조 노트 뿐만 아니라 앱이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다.
- **제출**을 선택 하 여 게시에 대 한 제품을 제출 합니다. 제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 경우 전자 메일을 보냅니다. 파트너 센터로 돌아가서 제품을 공개 (또는 개인 제품의 경우 비공개 사용자에 게 공개)에 게시 하기 위해 **live live** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
