---
title: 클라우드 파트너 포털을 통해 Dynamics 365 for Operations 제품을 만드는 방법 | Microsoft Docs
description: 클라우드 파트너 포털을 통해 Dynamics 365 for Operations 제품을 만드는 방법
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 93c70e038589667ae97acb86663a6179dcc81637
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776771"
---
# <a name="how-to-create-dynamics-365-for-operations-offer-via-cloud-partner-portal"></a>클라우드 파트너 포털을 통해 Dynamics 365 for Operations 제품을 만드는 방법

게시 포털에서는 여러 개별 사용자가 제품 게시 작업을 공동으로 수행할 수 있도록 역할 기반 액세스 권한을 제공합니다. 자세한 내용은 [클라우드 포털 사용자 관리](./cloud-partner-portal-manage-users.md)를 참조하세요.

게시자 계정을 대신하여 제품을 게시하려면 \"소유자\" 역할의 사용자 중 한 명이 [사용 약관](https://azure.microsoft.com/support/legal/website-terms-of-use/), [Microsoft 개인정보처리방침](https://www.microsoft.com/privacystatement/default.aspx) 및 [Microsoft Azure 인증 프로그램 계약](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/) 준수에 동의해야 합니다.

## <a name="how-to-create-a-new-dynamics-365-for-operations-offer"></a>Dynamics 365 for Operations 제품을 만드는 방법

모든 필수 구성 요소를 갖추었다면 Dynamics 365 for Operations 제품 작성을 시작할 준비가 완료된 것입니다.

1. [클라우드 파트너 포털](https://cloudpartner.azure.com/)에 로그인합니다.
2. 왼쪽 탐색 모음에서 \"+ 새 제품\"을 클릭하고 \"Dynamics 365 for Operations\"를 선택합니다.
3. 새 제품 \"편집기\" 보기가 열립니다. 이제 작성을 시작할 준비가 되었습니다.
4. 작성해야 할 \"양식\"은 \"편집기\" 보기 내의 왼쪽에 표시됩니다. 각 \"양식\"은 작성해야 할 필드 집합으로 구성됩니다. 필수 필드는 빨간색 별표(\*)로 표시됩니다.

Dynamics 365 for Operations를 작성하려면 다음의 4가지 기본 양식을 사용합니다.

- 제품 설정
- 기술 정보
- 상점 세부 정보
- 연락처

## <a name="how-to-fill-out-the-offer-settings-form"></a>제품 설정 양식을 작성하는 방법

제품 설정 양식은 제품 설정을 지정하는 기본 양식입니다. 아래에 서로 다른 필드를 설명합니다.

### <a name="offer-id"></a>제품 ID

게시자 프로필 내에서 제품의 고유 ID입니다. 이 ID는 제품 URL에 표시됩니다. 소문자 영숫자 문자 또는 대시(-)로만 구성할 수 있습니다. ID는 대시로 끝내면 안 되며 최대 50문자를 포함할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

예를 들어 게시자 contoso 게시자는 제품을 만드는 경우 제품 ID *작업에 대 한 샘플 dynamics365*,으로 AppSource에 뜹니다 `https://appsource.microsoft.com/marketplace/apps/**contoso**.*sample-dynamics365 for operations*?tab=Overview\`합니다.

### <a name="publisher-id"></a>게시자 ID

이 드롭다운 목록을 사용하여 이 제품을 게시할 게시자 프로필을 선택할 수 있습니다. 이 필드는 제품이 라이브 상태가 되면 잠깁니다.

이름

제품에 대한 표시 이름입니다. 이 이름이 [AppSource](https://appsource.microsoft.com)에 표시됩니다. 최대 50문자를 포함할 수 있습니다.

\"저장\"을 클릭하여 진행 상황을 저장합니다. 다음 단계에서는 제품의 기술 정보를 작성합니다.

## <a name="how-to-fill-out-the-technical-info-form"></a>기술 정보 양식 작성 방법

기술 정보 양식에는 제품 페이지에 표시할 정보가 포함됩니다. 아래에서는 각 필드 관련 지침을 설명합니다.

![새 제품 화면](./media/publish_d365_new_offer/Technical_info.png)

### <a name="solution-identifier"></a>솔루션 식별자

먼저 솔루션 식별자를 확인합니다.

1. 이 식별자를 찾으려면 LifeCycle Services로 이동하여 솔루션 관리를 선택합니다.
2. 적절한 솔루션을 선택하고 나면 패키지 개요에 솔루션 식별자가 표시됩니다. \*\*식별자가 비어 있는 경우 편집을 선택하고 패키지를 다시 게시하면 솔루션 식별자가 표시됩니다.

### <a name="validation-assets"></a>유효성 검사 자산

여기서 CAR(사용자 지정 분석 보고서)을 업로드합니다.

### <a name="does-solution-enable-translations"></a>솔루션에서 번역이 가능한가요?

\'예\' 또는 \'아니요\'를 선택합니다.

### <a name="does-solution-include-localizations"></a>솔루션에 지역화 버전이 포함되어 있나요?

\'예\' 또는 \'아니요\'를 선택합니다.

### <a name="product-version"></a>제품 버전

새 NX를 선택하고 마지막으로 저장을 클릭합니다.

## <a name="how-to-fill-out-storefront-details-form"></a>상점 정보 양식 작성 방법

먼저 제품 세부 정보를 입력합니다.

1. **제품 요약**

    \- 솔루션의 간략한 요약 정보를 입력합니다(최대 100자).

2. **제품 설명**

    \- 솔루션의 간략한 설명을 입력합니다. 설명은 솔루션의 기능 공간을 포함해야 하며, BPM 라이브러리와 직접적으로 연관된 내용이어야 합니다. 예를 들어 마케팅 콘텐츠에서 x,y,z 기능을 언급하는 경우 최종 검토 과정에서 LCS 내의 BPM 라이브러리에 해당 설명이 작성되어 있는지를 확인하게 됩니다.

![상점 정보 화면](./media/publish_d365_new_offer/offer_details.png)

### <a name="listing-details"></a>목록 세부 정보

![상점 정보 화면](./media/publish_d365_new_offer/storefront_details.png)

1. **산업** - 제공된 옵션에서 산업을 2개까지 선택합니다.
2. **범주** - 제공된 옵션에서 범주를 3개까지 선택합니다.
3. **앱 유형** - 제공된 옵션 중에서 선택합니다.
4. **앱의 도움말 링크** - 솔루션의 도움말 링크를 입력합니다.
5. **지원되는 국가/지역** - 제공된 옵션 중에서 선택합니다.
6. **지원되는 언어** - 제공된 옵션 중에서 선택합니다.
7. **앱 버전** - 릴리스하는 솔루션의 버전을 입력합니다(예: 1.0.0.0).
8. **앱 릴리스 날짜** - 솔루션의 릴리스 날짜를 mm/dd/yyyy 형식으로 입력합니다.
9. **앱이 작동하는 제품** - 앱이 작동하는 특정 제품을 추가합니다. 제품은 3개까지 추가할 수 있습니다. 제품을 추가하려면 새로 만들기 옆에 있는 더하기 기호를 클릭합니다. 그러면 앱이 작동하는 제품의 이름을 입력할 수 있는 개방형 텍스트 필드가 새로 생성됩니다.
10. **검색 키워드** - 검색 중에 사용자가 솔루션을 찾는 데 사용할 수 있는 일반적인 용어를 입력합니다. \*\*이러한 키워드는 Marketplace에 표시되지 않습니다.
11. **숨기기 키** - 공용 보기에서 제품을 숨기기 위해 제품 미리 보기 URL과 결합되는 키이며 암호는 아닙니다. 여기에는 아무 문자열이나 입력할 수 있습니다.

### <a name="marketing-artifacts"></a>마케팅 아티팩트

1. 다음으로는 **로고**, **스크린샷**을 업로드합니다. \*\*각 업로드의 크기를 잘 확인해야 합니다. 또한 모든 이미지는 PNG 형식이어야 합니다.
2. **데모 비디오** - \"+새로 만들기\"를 클릭합니다. 솔루션의 데모 비디오(YouTube 또는 Vimeo 링크만 사용 가능함)를 업로드합니다.\*\* 지정된 크기의 미리 보기를 업로드해야 비디오가 준비 단계에 표시됩니다.
3. **문서** - 솔루션과 관련된 모든 문서를 업로드합니다. 문서 이름을 입력해야 합니다.

### <a name="legal"></a>법적 정보

이 정보는 개인정보취급방침 및 사용 약관에 연결됩니다. 솔루션 개인정보취급방침 URL과 사용 약관을 입력합니다. \*\*고객은 포털에서 이러한 정책을 확인할 수 있습니다.

### <a name="customer-support"></a>고객 지원

지원 URL은 포털에서만 사용자에게 표시됩니다.

### <a name="leads-management"></a>잠재 고객 관리

잠재 고객을 저장할 CRM 시스템을 선택합니다. 선택 \"Azure Table\" 여기 있는 다음 CRM 시스템의 경우: Salesforce, Marketo, Microsoft Dynamics CRM. 여기서 선택하는 CRM 시스템은 AppSource에서 앱을 사용해 보는 최종 사용자(잠재 고객)의 세부 정보를 작성하는 곳입니다. 선택하는 CRM 시스템에 따라 아래의 해당 URL을 클릭하여 다음 필드 집합을 작성하는 방법에 대한 정보를 확인하세요.

![잠재 고객 관리 세부 정보](./media/publish_d365_new_offer/leads.png)

1. Azure 테이블의 경우 [여기](https://aka.ms/leadsettingforazuretable)를 참조하세요.
2. Dynamics CRM Online의 경우 [여기](https://aka.ms/leadsettingfordynamicscrm)를 참조하세요.
3. Marketo의 경우 [여기](https://aka.ms/leadsettingformarketo)를 참조하세요.
4. Salesforce의 경우 [여기](https://aka.ms/leadsettingforsalesforce)를 참조하세요.

## <a name="how-to-fill-out-the-contacts-form"></a>연락처 양식 작성 방법

Microsoft 및 고객 지원에서 이 정보를 사용합니다. 회사의 엔지니어링 연락처와 고객 지원 정보, 그리고 솔루션의 지원 URL을 입력합니다. Microsoft에서 게시자의 솔루션에 대해 문의 사항이 있을 때와 고객 지원 제공 시에 이 정보가 단일 연락처로 사용됩니다.

![제품 연락처 화면](./media/publish_d365_new_offer/Contacts.png)
