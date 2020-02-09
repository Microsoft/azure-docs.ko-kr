---
title: 클라우드 솔루션 공급자 | Azure Marketplace
description: 이제 게시자는 CSP (Microsoft 클라우드 솔루션 공급자) 파트너 채널을 통해 제품을 판매할 수 있습니다.
services: Azure, Marketplace, Compute, Storage, Networking, Partner Center
author: ChJenk
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/12/2019
ms.author: v-chjen
ms.openlocfilehash: 546702af671cfe9506a4fc0448f40b7b8353960c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038785"
---
# <a name="cloud-solution-providers"></a>클라우드 솔루션 공급 기업

소프트웨어 제품은 [microsoft 웹 상점](https://docs.microsoft.com/azure/marketplace/comparing-appsource-azure-marketplace)을 통한 제품의 공개 가용성 외에도 CSP (클라우드 솔루션 공급자) 프로그램의 파트너가 제공 하는 수백만 개의 정규화 된 Microsoft 고객에 게 도달할 수 있습니다.

게시자는 옵트인 (opt in)을 기반으로 CSP 프로그램의 가용성에 대 한 제품을 구성 하 고, 새 제품 또는 기존 제품에 대해 파트너가 제품을 판매 하 고 고객을 위해 번들로 제공 되는 솔루션을 만들 수 있도록 합니다.

게시자는 최종 고객에 게 중단-수정 지원을 제공 하 고 CSP 프로그램 및/또는 고객의 파트너에 게 지원을 요청 하는 메커니즘을 제공 하는 일을 담당 합니다. Csp 프로그램의 파트너가 고객의 계층 1 지원 요청을 처리할 수 있도록 사용자 문서, 학습 및 서비스 상태/중단 알림 (해당 하는 경우)을 사용 하 여 CSP 프로그램에서 파트너를 제공 하는 것이 가장 좋습니다.  

다음 제품은 CSP 프로그램에서 파트너에 의해 판매 되도록 옵트인 (opt in) 할 수 있습니다.

- SaaS (Software as a Service)의 transact-sql 제공
- VM(Virtual Machines)
- 솔루션 템플릿
- 관리 되는 응용 프로그램

> [!NOTE]
> 컨테이너 및 BYOL (사용자 라이선스) VM Sku는 기본적으로 CSP 프로그램에서 파트너에 의해 판매 되도록 옵트인 됩니다.

## <a name="how-to-configure-an-offering"></a>제품을 구성 하는 방법

CSP 프로그램 옵트인 설정은 파트너 센터 또는 Cloud 파트너 포털 제품 만들기 환경에서 구성 됩니다. [변경 게시자 환경에 대해 자세히 알아보세요](https://www.microsoftpartnercommunity.com/t5/Azure-Marketplace-and-AppSource/Cloud-Marketplace-In-Partner-Center/m-p/9738#M293).

### <a name="partner-center-opt-in"></a>파트너 센터 옵트인

파트너 센터의 CSP 대리점 대상 모듈에서 옵트인 (opt in) 환경을 찾을 수 있습니다.

![CSP 대리점 대상](media/marketplace-publishers-guide/csp-reseller-audience.png)

CSP 재판매인 대상 모듈에는 다음 세 가지 옵션 중에서 선택할 수 있습니다.

- 옵션 1: CSP 프로그램의 모든 파트너
- 옵션 2: 선택한 CSP 프로그램의 특정 파트너
- 옵션 3: CSP 프로그램에서 파트너 없음

#### <a name="option-one-any-partner-in-the-csp-program"></a>옵션 1: CSP 프로그램의 모든 파트너

![CSP 프로그램의 모든 파트너](media/marketplace-publishers-guide/csp-reseller-option-one.png)

 이 옵션을 선택 하면 CSP 프로그램의 모든 파트너가 고객에 게 제품을 재판매 수 있습니다.

#### <a name="option-two-specific-partners-in-the-csp-program-i-select"></a>옵션 2: 선택한 CSP 프로그램의 특정 파트너

![선택한 CSP 프로그램의 특정 파트너](media/marketplace-publishers-guide/csp-reseller-option-two.png)

이 옵션을 선택 하면 제품을 재판매 수 있는 CSP 프로그램의 파트너에 게 권한을 부여 합니다.

파트너에 게 권한을 부여 하려면 **Csp 파트너 선택** 을 클릭 합니다. 그러면 파트너 이름 또는 CSP AZURE ACTIVE DIRECTORY (AAD) 테넌트 ID로 검색할 수 있는 메뉴가 나타납니다.

![CSP 메뉴 선택](media/marketplace-publishers-guide/csp-pop-up-module.png)

**국가**, **역량**또는 **기술과**같은 검색 필터를 적용할 수 있습니다.

![파트너 검색을 위한 국가, 역량 및 기술 필터](media/marketplace-publishers-guide/csp-add-resellers.png)

파트너 목록을 선택한 후 **추가**를 선택 합니다.

![CSP 프로그램의 권한 부여 된 파트너 목록 예제](media/marketplace-publishers-guide/csp-add-resellers-details.png)

선택한 파트너 목록을 보여 주는 테이블이 CSP 재판매인 대상 페이지에 표시 됩니다.

![CSP 재판매인 대상 페이지의 파트너 목록이 있는 테이블](media/marketplace-publishers-guide/csp-option-two-add-reseller.png)

**초안 저장** 을 선택 하 여 변경 내용을 등록 합니다.

이 제안이 게시 되지 않은 경우 선택한 파트너에 게 제공 하는 제품을 게시 해야 합니다.

>[!NOTE]
>지정 된 지역의 CSP 프로그램에서 파트너에 게 권한을 부여 하는 경우 해당 특정 지역에 속하는 고객에 게 제품을 판매할 수 있습니다. CSP 제품을 지역에서 분류 하는 방법에 대 한 자세한 내용은 [클라우드 솔루션 공급자 프로그램 지역 시장 및 통화](https://docs.microsoft.com/partner-center/regional-authorization-overview) 를 참조 하세요.

이미 게시 된 제안의 CSP 목록을 업데이트 하는 경우 추가 파트너를 추가 하 고 **csp 대상 동기화**를 선택 합니다.

이미 권한이 부여 된 파트너 목록이 있는 제품을 사용 하 고 다른 제품에 동일한 목록을 사용 하려면 **Import/Export**를 사용 하세요. CSP 목록이 있는 제품으로 이동 하 여 **Csp 내보내기**를 선택 합니다. 함수는 다른 제안으로 가져올 수 있는 .csv 파일을 개발 합니다.

#### <a name="option-three-no-partners-in-the-csp-program"></a>옵션 3: CSP 프로그램에서 파트너 없음

![CSP 프로그램에서 파트너 없음](media/marketplace-publishers-guide/csp-reseller-option-three.png)

이 옵션을 선택 하면 CSP 프로그램의 제안을 옵트아웃 하 게 됩니다. 언제 든 지이 선택 항목을 변경할 수 있습니다.

### <a name="cloud-partner-portal-opt-in"></a>Cloud 파트너 포털 옵트인

Cloud 파트너 포털에서 옵트인은 Marketplace 또는 Storefront 탭에 설정 되어 있습니다. CSP 프로그램에서 특정 파트너를 선택할 수 있는 기능은 파트너 센터 에서만 사용할 수 있습니다.

![CPP의 CSP 옵트인 (opt in) 환경](media/marketplace-publishers-guide/csp-opt-in.png)

## <a name="deauthorize-partners-in-the-csp-program"></a>CSP 프로그램에서 파트너 권한 부여 취소

CSP 프로그램에서 파트너에 게 권한을 부여 하 고 해당 파트너가 이미 고객에 게 제품을 재판매할 하는 경우 해당 파트너에 게 권한을 부여할 수 없습니다.

CSP 프로그램의 파트너가 제품을 고객에 게 판매 하지 않은 경우 제품을 게시 한 후 CSP를 제거 하려는 경우 다음 지침을 사용 하세요.

1. [지원 요청 페이지로](https://aka.ms/marketplacepublishersupport)이동 합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경 하지 마세요.

2. **제품 버전 선택**에서 **Live 제품 관리**를 선택 합니다.
3. **문제를 가장 잘 설명 하는 범주를 선택**하는 경우 제품을 참조 하는 범주를 선택 합니다.
4. **문제를 가장 잘 설명 하는 문제를 선택**하려면 **기존 제품 업데이트**를 선택 합니다.
5. 문제 **정보 페이지로** 이동 하려면 **다음** 을 선택 하 여 문제에 대 한 세부 정보를 입력 합니다.
6. 인증 되지 않은 **CSP** 를 발급 제목으로 사용 하 고 나머지 필수 섹션을 입력 합니다.




## <a name="navigate-between-options"></a>옵션 간 이동

이 섹션을 사용 하 여 세 가지 CSP 재판매인 옵션 간을 탐색할 수 있습니다.

### <a name="navigate-from-option-one-any-partner-in-the-csp-program"></a>옵션 1: CSP 프로그램의 모든 파트너에서 이동 합니다.

제품이 현재 **옵션 1: CSP 프로그램의 모든 파트너** 이며 다른 두 옵션 중 하나로 이동 하려는 경우 다음 지침을 사용 하 여 요청을 만듭니다.

1. [지원 요청 페이지로](https://aka.ms/marketplacepublishersupport)이동 합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경 하지 마세요.

2. **제품 버전 선택**에서 **Live 제품 관리**를 선택 합니다.
3. **문제를 가장 잘 설명 하는 범주를 선택**하는 경우 제품을 참조 하는 범주를 선택 합니다.
4. **문제를 가장 잘 설명 하는 문제를 선택**하려면 **기존 제품 업데이트**를 선택 합니다.
5. 문제 **정보 페이지로** 이동 하려면 **다음** 을 선택 하 여 문제에 대 한 세부 정보를 입력 합니다.
6. 인증 되지 않은 **CSP** 를 발급 제목으로 사용 하 고 나머지 필수 섹션을 입력 합니다.

> [!NOTE]
> 옵션 2로 이동 하려고 하는데 CSP 프로그램의 파트너가 고객에 게 제품을 이미 재판매할 하는 경우 해당 파트너는 기본적으로 권한 있는 파트너 목록에 이미 있습니다.  

### <a name="navigate-from-option-two-specific-partners-in-the-csp-program-i-select"></a>옵션 2: 선택한 CSP 프로그램의 특정 파트너에서 이동 합니다.

제품이 현재 **옵션 2: 선택한 csp 프로그램의 특정 파트너** 인 경우 **Csp 프로그램의 모든 파트너**로 이동 하려는 경우 다음 지침을 사용 하 여 요청을 만듭니다.

1. [지원 요청 페이지로](https://aka.ms/marketplacepublishersupport)이동 합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경 하지 마세요.

2. **제품 버전 선택**에서 **Live 제품 관리**를 선택 합니다.
3. **문제를 가장 잘 설명 하는 범주를 선택**하는 경우 제품을 참조 하는 범주를 선택 합니다.
4. **문제를 가장 잘 설명 하는 문제를 선택**하려면 **기존 제품 업데이트**를 선택 합니다.
5. 문제 **정보 페이지로** 이동 하려면 **다음** 을 선택 하 여 문제에 대 한 세부 정보를 입력 합니다.
6. 인증 되지 않은 **CSP** 를 발급 제목으로 사용 하 고 나머지 필수 섹션을 입력 합니다.

 제품이 현재 **옵션 2: 선택한 csp 프로그램의 특정 파트너** 인 경우 **Csp 프로그램에서 옵션 3: 파트너 없음**으로 이동 하려면 이전에 권한이 부여 된 csp 프로그램의 파트너가 최종 고객에 게 제품을 재판매할 하지 않은 경우에만 해당 옵션을 탐색할 수 있습니다. 요청을 만들려면 다음 지침을 따르세요.

1. [지원 요청 페이지로](https://aka.ms/marketplacepublishersupport)이동 합니다. 처음 몇 개의 드롭다운 메뉴는 자동으로 채워집니다.

   > [!NOTE]
   > 미리 채워진 드롭다운 메뉴 선택 항목을 변경 하지 마세요.

2. **제품 버전 선택**에서 **Live 제품 관리**를 선택 합니다.
3. **문제를 가장 잘 설명 하는 범주를 선택**하는 경우 제품을 참조 하는 범주를 선택 합니다.
4. **문제를 가장 잘 설명 하는 문제를 선택**하려면 **기존 제품 업데이트**를 선택 합니다.
5. 문제 **정보 페이지로** 이동 하려면 **다음** 을 선택 하 여 문제에 대 한 세부 정보를 입력 합니다.
6. 인증 되지 않은 **CSP** 를 발급 제목으로 사용 하 고 나머지 필수 섹션을 입력 합니다.

### <a name="navigate-from-option-3-no-partners-in-the-csp-program"></a>옵션 3: CSP 프로그램에서 파트너 없음을 탐색 합니다.

제품이 현재 **옵션 3: CSP 프로그램에 파트너가 없는**경우 언제 든 지 다른 두 옵션 중 하나로 이동할 수 있습니다.

## <a name="sharing-sales-and-support-materials-with-partners-in-the-csp-program"></a>CSP 프로그램에서 파트너와 판매 및 지원 자료 공유

클라우드 솔루션 공급자 프로그램의 파트너가 제품을 가장 효과적으로 표시 하 고 조직에 참여 하 게 하려면 대리점에서 사용할 수 있는 판매 및 지원 자료를 제출 해야 합니다. 이러한 리소스는 marketplace 상점 고객에 게 노출 되지 않습니다.

### <a name="partner-center-csp-channel"></a>파트너 센터 CSP 채널

파트너 센터에서 CSP 채널에 옵트인 한 경우, 게시자는 관련 마케팅 자료 및 채널 연락처 정보를 제공 하는 URL을 제공 목록 모듈 아래에 있는 CSP 채널로 입력 해야 합니다.

![파트너 센터 CSP 참고 자료 정보](media/marketplace-publishers-guide/pc-csp-channel.png)

### <a name="cloud-partner-portal-csp-channel"></a>Cloud 파트너 포털 CSP 채널

Cloud 파트너 포털에서 CSP 채널에 옵트인 한 경우, 게시자는 관련 마케팅 자료 및 채널 연락처 정보를 CSP 채널로 호스트 하는 URL을 입력 해야 합니다.

![Cloud 파트너 포털 CSP 참고 자료 정보](media/marketplace-publishers-guide/cpp-csp-information.png)

## <a name="next-steps"></a>다음 단계

[Azure Marketplace 및 AppSource 게시자 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)를 참조 하세요.

Marketplace GTM 서비스에 대 한 자세한 내용은 [출시 전 서비스](https://partner.microsoft.com/reach-customers/gtm)를 참조 하세요.

[파트너 센터](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv) 또는 [Cloud 파트너 포털](https://cloudpartner.azure.com/) 에 로그인 하 여 제품을 만들고 구성 합니다.
