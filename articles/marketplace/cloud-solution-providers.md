---
title: 클라우드 솔루션 공급자 - Microsoft 상업용 마켓플레이스
description: 상업용 마켓 플레이스에서 Microsoft CSP(클라우드 솔루션 공급자) 프로그램 파트너 채널을 통해 제품을 판매하는 방법에 대해 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 07/14/2020
ms.openlocfilehash: 51f4c9a5748896eff9e9f3ae9719b2e7e7cdd4e7
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607479"
---
# <a name="cloud-solution-provider-program"></a>클라우드 솔루션 공급자 프로그램

이 문서에서는 CSP(클라우드 솔루션 공급자) 프로그램에서 사용할 수 있도록 제품을 구성하는 방법을 설명합니다. [상용 marketplace online 스토어](comparing-appsource-azure-marketplace.md)를 통해 제품을 게시 하는 것 외에도 CSP 프로그램을 통해 판매 하 여 프로그램에서 사용 하는 수백만 개의 Microsoft 고객에 게 연락할 수 있습니다.

옵트인 기반으로 CSP 프로그램에서 사용할 수 있게 신규 또는 기존 제품을 구성할 수 있어 CSP 파트너가 제품을 판매하고 고객을 위한 번들 솔루션을 만들 수 있습니다.

게시자는 최종 고객에게 고장 수리 지원을 제공하고 CSP 프로그램 및/또는 고객의 파트너에게 지원 요청 메커니즘을 제공하는 역할을 합니다. 고객의 계층 1 지원 요청을 처리할 수 있도록 CSP 프로그램의 파트너에게 사용자 설명서, 학습 및 서비스 상태/중단 알림(해당 하는 경우)을 제공하는 것이 가장 좋습니다.  

다음은 CSP 프로그램의 파트너가 판매하도록 옵트인할 수 있는 제품입니다.

- SaaS(Software-as-a-Service) 서비스 제품
- VM(가상 머신)
- 솔루션 템플릿
- 관리되는 애플리케이션

> [!NOTE]
> 컨테이너 및 BYOL (사용자 라이선스) VM 계획은 기본적으로 CSP 프로그램에서 파트너에 의해 판매 되도록 옵트인 됩니다.

## <a name="how-to-configure-an-offer"></a>제품을 구성 하는 방법

파트너 센터에서 제품을 만들 때 CSP 프로그램 옵트인 설정을 구성 합니다. [게시자 환경 변경에 대해 자세히 알아봅니다.](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293)

### <a name="partner-center-opt-in"></a>파트너 센터 옵트인

옵트인 환경은 CSP 대리점 대상 모듈 아래에 있습니다.

![CSP 재판매인 대상 그룹](media/marketplace-publishers-guide/csp-reseller-audience.png)

다음 세 가지 옵션 중에서 선택 합니다.

1. CSP 프로그램의 모든 파트너
2. 선택한 CSP 프로그램의 특정 파트너.
3. CSP 프로그램에 파트너가 없습니다.

#### <a name="option-1-any-partner-in-the-csp-program"></a>옵션 1: CSP 프로그램의 모든 파트너

![CSP 프로그램의 모든 파트너](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 이 옵션을 선택하면 CSP 프로그램의 모든 파트너가 고객에게 제품을 재판매할 수 있습니다.

#### <a name="option-2-specific-partners-in-the-csp-program-i-select"></a>옵션 2: 선택한 CSP 프로그램의 특정 파트너

![선택한 CSP 프로그램의 특정 파트너](media/marketplace-publishers-guide/csp-reseller-option-two.png)

이 옵션을 선택하면 제품을 재판매할 수 있는 CSP 프로그램의 파트너에게 권한을 부여합니다.

파트너에게 권한을 부여하려면 **CSP 파트너 선택**을 클릭하여 파트너 이름이나 CSP Azure Active Directory(AAD) 테넌트 ID로 검색할 수 있는 메뉴를 엽니다.

![CSP 선택 메뉴](media/marketplace-publishers-guide/csp-pop-up-module.png)

**국가**, **역량**, **기술** 등의 검색 필터를 적용할 수 있습니다.

![파트너 검색에 대한 국가/지역, 역량 및 기술 필터](media/marketplace-publishers-guide/csp-add-resellers.png)

파트너 목록을 선택한 후 **추가**를 선택합니다.

![CSP 프로그램의 권한이 부여된 파트너 목록 예](media/marketplace-publishers-guide/csp-add-resellers-details.png)

선택한 파트너 목록을 보여주는 표가 CSP 재판매인 대상 그룹 페이지에 표시됩니다.

![CSP 재판매인 대상 그룹 페이지의 파트너 목록이 포함된 표](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

**초안 저장**을 선택하여 변경 내용을 등록합니다.

이 제품이 게시되지 않은 경우 선택한 파트너가 사용할 수 있게 제품을 게시해야 합니다.

>[!NOTE]
>지정된 지역의 CSP 프로그램의 파트너에게 권한을 부여하는 경우 해당하는 특정 지역에 속하는 고객에게 제품을 판매할 수 있습니다. 지역별로 CSP 제품을 분류하는 방법에 대한 자세한 내용은 [클라우드 솔루션 공급자 프로그램 지역 시장 및 통화](https://docs.microsoft.com/partner-center/regional-authorization-overview)를 참조 하세요.

이미 게시된 제품의 CSP 목록을 업데이트하는 경우 파트너를 더 추가하고 **CSP 대상 그룹 동기화**를 선택합니다.

이미 권한이 부여 된 파트너 목록이 있는 제품을 사용하고 다른 제품에 동일한 목록을 사용하려면 **가져오기/내보내기**를 사용합니다. CSP 목록이 있는 제품으로 이동하고 **CSP 내보내기**를 선택합니다. 다른 제품으로 가져올 수 있는 .csv 파일이 만들어집니다.

#### <a name="option-3-no-partners-in-the-csp-program"></a>옵션 3: CSP 프로그램에서 파트너 없음

![CSP 프로그램에 파트너 없음](media/marketplace-publishers-guide/csp-reseller-option-three.png)

이 옵션을 선택하면 CSP 프로그램에서 제품이 옵트아웃됩니다. 언제든지 이 선택 사항을 변경할 수 있습니다.

## <a name="deauthorize-partners-in-the-csp-program"></a>CSP 프로그램의 파트너 권한 부여 취소

CSP 프로그램의 파트너에게 권한을 부여했으며 해당 파트너가 이미 고객에게 제품을 재판매한 경우 해당 파트너에게 부여된 권한을 취소할 수 없습니다.

CSP 프로그램의 파트너가 고객에게 제품을 판매하지 않았으며 제품 게시 후 CSP를 제거하려는 경우 다음 지침을 따르세요.

1. [지원 요청 페이지](https://aka.ms/marketplacepublishersupport)로 이동합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경하지 않습니다.

2. **제품 버전 선택**에서 **라이브 제품 관리**를 선택합니다.
3. **문제를 가장 잘 설명하는 범주 선택**에서 제품에 맞는 범주를 선택합니다.
4. **문제를 가장 잘 설명하는 항목 선택**에서 **기존 제품 업데이트**를 선택합니다.
5. **다음**을 선택하여 문제에 대한 세부 정보를 입력할 수 있는 **문제 세부 정보 페이지**로 이동합니다.
6. **CSP 권한 부여 취소**를 문제 제목으로 사용하고 나머지 필수 섹션을 입력합니다.

## <a name="navigate-between-options"></a>스크립트 간 이동

이 섹션을 사용하여 세 가지 CSP 재판매인 옵션 간을 이동할 수 있습니다.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>옵션 1에서 이동: CSP 프로그램의 모든 파트너

제품이 현재 **옵션 1: CSP 프로그램의 모든 파트너**이고 다른 두 옵션 중 하나로 이동하려면 다음 지침에 따라 요청을 만듭니다.

1. [지원 요청 페이지](https://aka.ms/marketplacepublishersupport)로 이동합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경하지 않습니다.

2. **제품 버전 선택**에서 **라이브 제품 관리**를 선택합니다.
3. **문제를 가장 잘 설명하는 범주 선택**에서 제품에 맞는 범주를 선택합니다.
4. **문제를 가장 잘 설명하는 항목 선택**에서 **기존 제품 업데이트**를 선택합니다.
5. **다음**을 선택하여 문제에 대한 세부 정보를 입력할 수 있는 **문제 세부 정보 페이지**로 이동합니다.
6. **CSP 권한 부여 취소**를 문제 제목으로 사용하고 나머지 필수 섹션을 입력합니다.

> [!NOTE]
> 옵션 2로 이동하려고 하는데 CSP 프로그램의 파트너가 이미 고객에게 제품을 이미 재판매한 경우 해당 파트너는 기본적으로 권한 있는 파트너 목록에 이미 있습니다.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>옵션 2에서 이동: 선택한 CSP 프로그램의 특정 파트너

제품이 현재 **옵션 2: 선택한 CSP 프로그램의 특정 파트너**이고 **옵션 1: CSP 프로그램의 모든 파트너**로 이동하려면 다음 지침에 따라 요청을 만듭니다.

1. [지원 요청 페이지](https://aka.ms/marketplacepublishersupport)로 이동합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경하지 않습니다.

2. **제품 버전 선택**에서 **라이브 제품 관리**를 선택합니다.
3. **문제를 가장 잘 설명하는 범주 선택**에서 제품에 맞는 범주를 선택합니다.
4. **문제를 가장 잘 설명하는 항목 선택**에서 **기존 제품 업데이트**를 선택합니다.
5. **다음**을 선택하여 문제에 대한 세부 정보를 입력할 수 있는 **문제 세부 정보 페이지**로 이동합니다.
6. **CSP 권한 부여 취소**를 문제 제목으로 사용하고 나머지 필수 섹션을 입력합니다.

 제품이 현재 **옵션 2: 선택한 CSP 프로그램의 특정 파트너**이고 **옵션 3: CSP 프로그램에 파트너 없음**으로 이동하려면 이전에 권한을 부여한 CSP 프로그램의 파트너가 최종 고객에게 제품을 재판매하지 않은 경우에만 해당 옵션으로 이동할 수 있습니다. 다음 지침에 따라 요청을 만듭니다.

1. [지원 요청 페이지](https://aka.ms/marketplacepublishersupport)로 이동합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경하지 않습니다.

2. **제품 버전 선택**에서 **라이브 제품 관리**를 선택합니다.
3. **문제를 가장 잘 설명하는 범주 선택**에서 제품에 맞는 범주를 선택합니다.
4. **문제를 가장 잘 설명하는 항목 선택**에서 **기존 제품 업데이트**를 선택합니다.
5. **다음**을 선택하여 문제에 대한 세부 정보를 입력할 수 있는 **문제 세부 정보 페이지**로 이동합니다.
6. **CSP 권한 부여 취소**를 문제 제목으로 사용하고 나머지 필수 섹션을 입력합니다.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>옵션 3에서 이동: CSP 프로그램에 파트너 없음

제품이 현재 **옵션 3: CSP 프로그램에 파트너 없음**이면 언제든 지 다른 두 옵션 중 하나로 이동할 수 있습니다.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>CSP 프로그램의 파트너와 판매 및 지원 자료 공유

클라우드 솔루션 공급자 프로그램의 파트너가 제품을 가장 효과적으로 표시 하 고 조직에 참여 하도록 지원 하려면 대리점에서 사용할 수 있는 판매 및 지원 자료를 제출 해야 합니다. 이러한 리소스는 온라인 상점에서 고객에 게 노출 되지 않습니다.

### <a name="partner-center-csp-channel"></a>파트너 센터 CSP 채널

파트너 센터에서 CSP 채널에 옵트인한 경우 게시자는 제품 목록 모듈 아래에 있는 CSP 채널에 관련 마케팅 자료를 호스팅하는 URL과 채널 연락처 정보를 입력해야 합니다.

![파트너 센터 CSP 참고 자료 정보](media/marketplace-publishers-guide/pc-csp-channel.png)

## <a name="next-steps"></a>다음 단계

[Azure Marketplace 및 AppSource 게시자 가이드](marketplace-publishers-guide.md)를 방문합니다.

마켓플레이스 GTM 서비스에 대해 자세히 알아보려면 [Go-To-Market 서비스](https://partner.microsoft.com/reach-customers/gtm)를 참조하세요.

[파트너 센터](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)에 로그인하여 제품을 만들고 구성합니다.
