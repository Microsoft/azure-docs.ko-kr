---
title: 상업적 Marketplace에서 새 SaaS 제품 만들기
description: Microsoft 파트너 센터에서 상용 Marketplace 포털을 사용 하 여 Azure Marketplace, AppSource 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 나열 하거나 판매 하기 위해 새 SaaS (Software as a Service) 제품을 만드는 방법입니다.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/22/2020
ms.openlocfilehash: 4cac2fcd16f779a6b31e36ca175801eedaa31d5d
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705822"
---
# <a name="create-a-new-saas-offer"></a>새 SaaS 제품 만들기

SaaS (Software as a Service) 제품을 만들기 시작 하려면 먼저 [파트너 센터 계정을 만들고](./create-account.md) **개요** 탭을 선택 하 여 [상용 마켓플레이스 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)를 열어야 합니다.

![파트너 센터의 상용 마켓플레이스 대시보드](./media/new-offer-overview.png)

>[!Note]
> 제품이 게시 되 면 파트너 센터에서 만든 제품에 대 한 편집 내용은 시스템 에서만 업데이트 되 고 다시 게시 된 후에도 저장 됩니다. 변경을 수행한 후 게시에 대 한 제품을 제출 해야 합니다.

\+ **새 제품** 을 선택 합니다. 단추를 클릭 한 다음, 서비스 메뉴 항목 **으로 소프트웨어** 를 선택 합니다.

다른 제품 유형을 선택 하면 이전 [Cloud 파트너 포털](https://cloudpartner.azure.com/)으로 리디렉션될 수 있습니다. 현재는 파트너 센터의 상용 Marketplace 포털에서 SaaS 및 Dynamics 365 제공만 사용할 수 있습니다.

![파트너 센터에서 제안 창 만들기](./media/new-offer-click.png)

**새 제품** 대화 상자가 표시 됩니다.

![새 제품 대화 상자](./media/new-offer-popup.png)

## <a name="offer-id-and-alias"></a>제품 ID 및 별칭

- **제품 ID**: 계정의 각 제품에 대 한 고유 식별자입니다. 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿 (해당 하는 경우)에 대 한 URL 주소의 고객에 게 표시 됩니다. 제품 ID는 소문자, 영숫자 (하이픈 및 밑줄을 포함 하 고 공백 없음) 이어야 합니다. **제품 ID** 는 50 자로 제한 되며, *만들기*를 선택한 후에는 변경할 수 없습니다.  
예: 테스트-제품-1
<br>결과 URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **제품 별칭**: 파트너 센터 포털 내에서 제품을 참조 하는 데 사용 되는 이름입니다. 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 *제품 이름* 및 기타 값과 다릅니다. *만들기*를 선택한 후에는이 값을 변경할 수 없습니다.

<br>예: 테스트 제안 1&#8482;

**만들기**를 선택합니다.  이 제품에 대 한 **제품 개요** 페이지가 생성 됩니다.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>제품 개요

**제품 개요** 페이지에는 다음이 포함 됩니다.

- **게시 상태** 는이 제품을 게시 하는 데 필요한 단계를 시각적으로 표시 하 고 각 단계를 완료 하는 데 걸리는 시간을 표시 합니다. 불완전 한 게시 단계 아이콘은 회색으로 표시 됩니다.

- **제안 개요** 메뉴에는이 제품에 대 한 작업을 수행 하기 위한 링크 목록이 포함 되어 있습니다. 이 작업 목록은 제품에 대해 선택한 내용에 따라 변경 됩니다.  
    - 제안이 초안 삭제 초안이 면
    - 제품이 실시간 판매 중지 제품 인 경우
    - 제품이 미리 보기 상태 이면 라이브로 전환 합니다.
    - 게시자 로그 아웃을 완료 하지 않은 경우 게시 취소

## <a name="offer-setup"></a>제품 설정

**제품 설정** 탭에서 다음 정보를 요청 합니다. 이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

- **Microsoft를 통해 판매 하 시겠습니까?** (예/아니요)
    - **예**, microsoft를 대신 하 여 microsoft 호스팅 marketplace 트랜잭션을 사용 하 여 제품을 판매 하 고 싶습니다. 디스크나 
    - **아니요**. 마켓플레이스을 통해 제품을 나열 하 고 Microsoft와는 독립적으로 통화 트랜잭션을 처리 하는 것이 좋습니다.

### <a name="sell-through-microsoft"></a>Microsoft를 통해 판매

Microsoft를 통해 판매 되는 고객 검색 및 취득은 Microsoft에서 사용자를 대신 하 여 marketplace 트랜잭션을 호스트 하 고 Microsoft의 전 세계적으로 사용 가능한 상거래 기능을 활용할 수 있도록 하는 것입니다.

#### <a name="saas-offer-requirements"></a>SaaS 제품 요구 사항

파트너 센터에서 상업적 Marketplace를 사용 하 여 SaaS (Software as a Service) 제품을 나열 하려면 다음 조건을 충족 해야 합니다.

- 사용자 제품은 id 관리 및 인증을 위해 [Azure Active Directory (AZURE AD)](https://azure.microsoft.com/services/active-directory/) 를 사용 해야 합니다.
- 제품에서 SaaS 처리 [api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) 를 사용 하 여 Azure Marketplace와 통합 해야 합니다.
- 더 광범위 한 요구 사항은 [SaaS 제품 게시 가이드](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide)를 참조 하세요.

#### <a name="saas-pricing-and-billing-options"></a>SaaS 가격 및 청구 옵션
SaaS 솔루션을 게시자의 Azure 구독에서 실행 하는 경우 고객이 지불 하는 라이선스 요금은 소프트웨어를 배포 하는 인프라 비용을 포함 합니다. Azure 인프라 사용은 사용자, 파트너, 직접 관리 및 청구 됩니다. 실제 인프라 사용 요금은 고객에게 표시되지 않습니다. 게시자는 Azure 인프라 사용 요금을 소프트웨어 라이선스 가격으로 번들 해야 합니다. 

SaaS는 요금제 청구 서비스를 사용 하 여 정액 요금, 사용자 당 요금 또는 소비 요금을 기준으로 월별 또는 연간 요금 청구를 지원 합니다. Microsoft의 상용 marketplace는 에이전시 모델에서 작동 합니다. 즉, 게시자가 가격을 설정 하 고, Microsoft 청구서 고객을 원천 하 고, Microsoft는 고객 요금을 지불 하는 동시에 게시자에 게 수익을

다음 표에서는 에이전시 모델을 보여 주기 위한 비용 및 지급 분석의 예를 보여 줍니다.

|**라이선스 비용**|**$100/월**|
|:---|:---|
|Azure 사용량 비용(D1/1개 코어)|고객이 아닌 게시자에게 직접 청구됩니다.|
|Microsoft에서 청구 하는 고객|월 $100.00 (게시자는 라이선스 요금으로 발생 하거나 통과 한 인프라 비용을 고려해 야 함)|

|**Microsoft에 청구되는 요금**|**$100/월**|
|:---|:---|
|Microsoft는 라이선스 비용의 80%를 지불합니다. <br>*Microsoft는 공인 SaaS 앱에 대 한 *라이선스 비용의 90%를 지불* 합니다.|매월 $80.00 <br>월 90.00 *$* *|

- 이 예제에서 Microsoft는 귀하의 소프트웨어 라이선스에 대해 고객에 게 $100.00를 청구 하 고 게시자에 게 $80.00을 지불 합니다.
- **줄어든 Marketplace 서비스 요금** 으로 한정 된 파트너는 2019 년 5 월 2020 일까 야 년 5 월부터 SaaS 제품에 대 한 트랜잭션 요금이 감소 하는 것을 볼 수 있습니다. 이 시나리오에서 Microsoft는 귀하의 소프트웨어 라이선스에 대해 $100.00를 청구 하 고 게시자에 게 $90.00을 지불 합니다.

> [!NOTE]
> **Marketplace 서비스 요금 절감**: 상업적 marketplace에 게시 한 특정 SaaS 제품의 경우 Microsoft는 Microsoft 게시자 계약에 설명 된 대로 20%에서 Marketplace 서비스 요금을 10%까지 줄일 수 있습니다. 제안을 받으려면 Microsoft에서 하나 이상의 제품을 IP 공동 판매 준비 또는 IP 공동 판매 우선 순위로 지정 해야 합니다.  해당 월에 대해이 감소 한 Marketplace 서비스 요금을 받으려면 각 월이 끝날 때까지 영업일 (5) 이상 이어야 합니다.  낮은 Marketplace 서비스 요금은 Vm, 관리 되는 앱 또는 상용 Marketplace를 통해 제공 되는 기타 제품에는 적용 되지 않습니다.  줄어든 Marketplace 서비스 요금은 2020 년 5 월 1 일부 터 2019 년 6 월 30 일 사이 Microsoft에서 수집한 라이선스 요금에 대 한 한정 된 제품에만 제공 됩니다.  이 시간 후에 Marketplace 서비스 요금은 일반적인 금액으로 반환 됩니다.

### <a name="list-through-microsoft"></a>Microsoft의 목록

Marketplace 목록을 만들어 Microsoft로 비즈니스를 홍보 하세요. Microsoft가 아닌 제품을 나열 하도록 선택 하는 것은 Microsoft가 소프트웨어 라이선스 트랜잭션에 직접 참여 하지 않는다는 것을 의미 합니다. 연결 된 트랜잭션 요금은 없으며 게시자는 고객 으로부터 수집 된 소프트웨어 라이선스 수수료의 100%를 유지 합니다. 그러나 게시자는 주문 처리, 계량, 청구, 청구, 지불 및 수집을 비롯 하 여 소프트웨어 라이선스 트랜잭션의 모든 측면을 지원 해야 합니다.

- **잠재 고객이이 목록 제공 서비스와 상호 작용 하 게 하려면 어떻게 해야 하나요?**

##### <a name="get-it-now-free"></a>지금 받기 (무료)
[Azure Active Directory (AZURE AD)를 사용 하 여 한 번의 클릭으로 인증](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)을 통해 평가판을 얻을 수 있는 올바른 URL ( *http* 또는 *https*로 시작)을 제공 하 여 고객에 게 제품을 무료로 제공 합니다.  예: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>무료 평가판 (목록)
[AZURE AD (Azure Active Directory)를 사용 하 여 한 번의 클릭으로 인증](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials)을 통해 평가판을 얻을 수 있는 올바른 URL ( *http* 또는 *https*로 시작)을 제공 하 여 고객에 게 제품을 나열 합니다.  예: `https://contoso.com/trial/saas-app` 무료 평가판을 나열 하는 제품은 서비스에서 생성, 관리 및 구성 되며 Microsoft에서 관리 하는 구독을 포함 하지 않습니다.

> [!NOTE]
> 평가판 링크를 통해 응용 프로그램에서 수신 하는 토큰은 Azure AD를 통해 사용자 정보를 가져와서 앱에서 계정 만들기를 자동화 하는 데만 사용할 수 있습니다. 이 토큰을 사용 하는 인증에는 MSA (Microsoft 계정)가 지원 되지 않습니다.

##### <a name="contact-me"></a>연락처
CRM (고객 관계 관리) 시스템을 연결 하 여 고객 연락처 정보를 수집 합니다. 고객에 게 정보를 공유할 수 있는 권한을 요청 하는 메시지가 표시 됩니다. 제품을 찾은 제품 이름, ID 및 marketplace 원본과 함께 이러한 고객 세부 정보는 구성 된 CRM 시스템으로 전송 됩니다. CRM을 구성 하는 방법에 대 한 자세한 내용은 [연결 리드 management](#connect-lead-management)를 참조 하세요.

## <a name="example-marketplace-offer-listing"></a>Marketplace 제품 목록 예

![노트를 사용 하는 예제 marketplace 제품 목록](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>시험 사용을 사용하도록 설정

시험 사용한 고객에 게 제품을 고객에 게 제공 하는 좋은 방법은 ' 구매 전 시도 '를 선택 하 여 변환 및 높은 우량 잠재 고객을 생성 하는 옵션을 제공 하는 것입니다. [테스트 드라이브에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **테스트 드라이브 사용** (확인란)

테스트 드라이브를 사용 하도록 설정 하면 고객이 고정 기간 동안 제품을 사용해 볼 수 있도록 시연 환경을 구성 하 라는 메시지가 표시 됩니다. 

### <a name="type-of-test-drive"></a>테스트 드라이브의 유형입니다.

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

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>추가 리드 관리 리소스
- [잠재 고객 관리 FAQ](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [일반적인 잠재 고객 구성 오류](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [리드 관리 개요 1 호출기](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

## <a name="properties"></a>속성

**속성** 탭에서는 마켓플레이스에서 제품을 그룹화 하는 데 사용 되는 범주와 산업, 제품을 지 원하는 법적 계약 및 앱 버전을 정의 하 라는 메시지가 표시 됩니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

### <a name="category"></a>범주

제품을 적절 한 마켓플레이스 검색 영역으로 그룹화 하는 데 사용 되는 최소 1 개 (1) 및 최대 3 개 범주를 선택 합니다. 제품 설명에서 제품이 이러한 범주를 어떻게 지원 하는지 확인 합니다.

### <a name="industry"></a>산업

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>앱 버전

이 필드는 선택 사항이 며 AppSource marketplace에서 제품의 버전 번호를 식별 하는 데 사용 됩니다.

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace에 대 한 표준 계약

Microsoft에서는 표준 계약 템플릿을 제공 합니다.

- **Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하나요?**

고객을 위한 조달 프로세스를 간소화 하 고 소프트웨어 공급 업체에 대 한 법적 복잡성을 줄이기 위해 Microsoft는 marketplace에서 트랜잭션을 용이 하 게 하는 Microsoft 상업적 marketplace에 대 한 표준 계약을 제공 합니다. 상업적 marketplace 게시자는 사용자 지정 약관을 만드는 대신 표준 계약에 따라 소프트웨어를 제공 하도록 선택할 수 있으며,이는 고객이 한 번만 심사 하 고 수락 하면 됩니다. 표준 계약은 https://go.microsoft.com/fwlink/?linkid=2041178 에서 찾을 수 있습니다.

"상용 marketplace에 대 한 표준 계약 사용" 확인란을 선택 하 여 사용자 고유의 사용자 지정 약관을 제공 하는 대신 표준 계약을 사용 하도록 선택할 수 있습니다.

![표준 계약 확인란 사용](./media/use-standard-contract.png)

> [!NOTE]
> Microsoft 상업적 marketplace에 대 한 표준 계약을 사용 하 여 제품을 게시 하면 사용자 고유의 사용자 지정 약관을 사용할 수 없습니다. "Or" 시나리오입니다. 표준 계약이 **나** 사용자 고유의 사용 약관에 따라 솔루션을 제공 합니다. 표준 계약의 조건을 수정 하려면 표준 계약 개정을 통해이 작업을 수행할 수 있습니다.

#### <a name="standard-contract-amendments"></a>표준 계약 개정

표준 계약 개정 let은 간소화를 위해 표준 계약 용어를 선택 하 고 제품 또는 비즈니스에 대 한 용어를 사용자 지정 합니다. 고객은 계약에 대 한 개정을 검토 하 여 Microsoft 표준 계약을 이미 검토 하 고 수락한 경우에만 해당 계약에 대 한 검토를 수행 해야 합니다.

상용 marketplace 게시자에 사용할 수 있는 개정에는 두 가지 종류가 있습니다.

- 유니버설 개정: 이러한 개정은 모든 고객에 대 한 표준 계약에 전체적으로 적용 됩니다. 유니버설 개정 구매 흐름에서 제품의 모든 고객에 게 표시 됩니다. 고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.
- Custom 개정: 이러한 개정는 Azure 테넌트 Id를 통해서만 특정 고객을 대상으로 하는 표준 계약에 대 한 특별 한 개정입니다. 게시자는 대상으로 지정할 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제안의 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다.  고객은 제품을 사용 하기 전에 표준 계약 및 수정 내용에 동의 해야 합니다.

>[!NOTE]
> 이러한 두 가지 유형의 개정 stack이 서로 위에 있습니다. 사용자 지정 개정를 대상으로 하는 고객은 구매 중에 표준 계약에 대 한 범용 수정도 받게 됩니다.

**Microsoft의 상용 marketplace에 대 한 표준 계약에 대 한 범용 수정 용어**:이 상자에 유니버설 수정 용어를 입력 합니다. 제품 당 단일 범용 수정 기능을 제공할 수 있습니다. 이 상자에는 개수에 제한 없이 문자를 입력할 수 있습니다. 이러한 용어는 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에 게 표시 됩니다.

**Microsoft의 상용 marketplace에 대 한 표준 계약에 대 한 사용자 지정 수정 용어**: **사용자 지정 수정 용어 추가**를 선택 하 여 시작 합니다. 제품 당 최대 10 개의 사용자 지정 수정 단어를 제공할 수 있습니다.

- **사용자 지정 수정 용어**: 사용자 지정 수정 약관 상자에 사용자 지정 수정 용어를 입력 합니다. 이 상자에는 개수에 제한 없이 문자를 입력할 수 있습니다. 이러한 사용자 지정 약관에 대해 지정 하는 테넌트 Id의 고객만 Azure Portal의 제품 구매 흐름에 사용자 지정 수정 용어가 표시 됩니다.  
- **테넌트 id** (필수): 각 사용자 지정 수정은 최대 20 개의 테넌트 id를 대상으로 지정할 수 있습니다. 사용자 지정 수정 항목을 추가 하는 경우 하나 이상의 테넌트 ID를 제공 해야 합니다. 테넌트 ID는 Azure에서 고객을 식별 합니다. Portal.azure.com > Azure Active Directory > 속성으로 이동 하 여이 ID를 고객에 게 요청할 수 있습니다. 디렉터리 ID 값은 테넌트 ID (예: 50c464d3-4930-494c-963c-1e951d15360e)입니다. 사용자의 도메인 이름 URL을 사용 하 여 고객의 테넌트 ID를 조회할 수도 있습니다. [Microsoft Azure 및 Office 365 테넌트 id?](https://www.whatismytenantid.com)
- **설명** (선택 사항): 필요에 따라 테넌트 ID에 대 한 친숙 한 설명을 제공 하 여, 수정으로 대상으로 하는 고객을 식별할 수 있습니다.

#### <a name="terms-and-conditions"></a>조건

사용자 고유의 사용자 지정 사용 약관을 제공 하려는 경우 사용 약관 필드에 해당 조건을 입력 하도록 선택할 수 있습니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다. 사용 약관에 더 긴 설명이 필요한 경우 사용 약관을 찾을 수 있는이 필드에 단일 URL 링크를 입력 합니다. 고객이 활성 링크로 표시 됩니다.

고객은 이러한 약관에 동의 해야 제품을 사용해 볼 수 있습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

## <a name="offer-listing"></a>제품 목록

제품 목록 탭에는 제품을 사용할 수 있는 언어 (및 시장)가 표시 되 고 현재 영어 (미국)만 사용할 수 있는 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 마켓플레이스 세부 정보 (제품 이름, 설명, 검색어 등)를 정의 해야 합니다.

> [!NOTE]
> 제품 설명, 문서, 스크린샷, 사용 약관 및 개인 정보 취급 방침 등 제품 목록 콘텐츠는 영어로 시작 하지 않아도 됩니다. "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다. 또한 제품 목록 콘텐츠에 사용 되는 것 이외의 언어로 콘텐츠를 제공 하는 *데 유용한 링크 URL* 을 제공 하는 것도 허용 됩니다.

### <a name="offer-listings"></a>제품 목록

제품 및 마케팅 자산에 대 한 설명을 포함 하 여 marketplace에 표시 되는 세부 정보를 제공 합니다.

- **이름** (필수): 여기에 정의 된 이름은 선택한 marketplace에서 제품의 제목으로 표시 됩니다. 이전 **새 제안** 항목에 따라 이름이 미리 채워집니다. 이름은 상표 일 수 있습니다. 이 클래스는 상표 및 저작권 기호가 아닌 경우에는이 모 지를 포함할 수 없으며 50 자로 제한 되어야 합니다.
- **요약** (필수): marketplace 목록 검색 결과에 사용할 제품에 대 한 간단한 설명을 제공 합니다. 이 필드에는 최대 100 자의 텍스트를 입력할 수 있습니다.
- **설명** (필수): marketplace 목록에 표시 되는 제품에 대 한 설명을 제공 합니다. 가치 제안, 주요 이점, 범주 또는 업계 연결, 앱 내 구매 기회, 필요한 공개 및 자세한 내용을 볼 수 있는 링크를 포함 하는 것이 좋습니다.
이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 추가 팁은 [유용한 앱 설명 작성](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description)문서를 참조 하세요.
- **검색 키워드**: 고객이 marketplace에서 제품을 찾는 데 사용할 수 있는 최대 3 개의 검색 키워드를 입력 합니다.
- **시작 지침** (필수): 잠재적 고객에 대해 앱 사용을 구성 하 고 시작 하는 방법을 설명 합니다.  이 빠른 시작에는 보다 자세한 온라인 설명서에 대 한 링크가 포함 될 수 있습니다. 이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다.

#### <a name="description"></a>**설명**

이 필드는 필수입니다. **설명**에 포함할 항목:

* 설명의 처음 몇 문장에서 제품의 가치 제안을 명확하게 설명합니다.  
* 처음 몇 문장은 검색 엔진 결과에 표시될 수 있다는 점에 유의하세요.  
* 제품을 판매하는 특징과 기능에 의존하지 않습니다. 대신, 전달하는 가치에 중점을 둡니다.  
* 산업별 어휘 또는 혜택 기반 단어를 최대한 많이 사용합니다.

가치 제안의 핵심 구성 요소에 포함되어야 하는 정보는 다음과 같습니다.

* 제품에 대한 설명입니다.
* 제품의 혜택을 받는 사용자 유형
* 고객이 요구 하거나 제품의 문제를 해결 합니다.

제품 **설명을** 보다 유용 하 게 사용 하려면 서식 있는 텍스트 편집기를 사용 하 여 설명의 서식을 지정 합니다.

![서식 있는 텍스트 편집기 사용](./media/text-editor2.png)

서식 있는 텍스트 편집기를 사용 하려면 다음 지침을 따르십시오.

- 콘텐츠 형식을 변경 하려면 아래와 같이 서식을 지정할 텍스트를 강조 표시 하 고 텍스트 스타일을 선택 합니다.

     ![서식 있는 텍스트 편집기를 사용 하 여 텍스트 형식 변경](./media/text-editor3.png)

- 글머리 기호 또는 번호 매기기 목록을 텍스트에 추가 하려면 아래 옵션을 사용 합니다.

     ![서식 있는 텍스트 편집기를 사용 하 여 목록 추가](./media/text-editor4.png)

- 텍스트에 들여쓰기를 추가 하거나 제거 하려면 아래 옵션을 사용 합니다.

     ![서식 있는 텍스트 편집기를 사용 하 여 들여쓰기](./media/text-editor5.png)

#### <a name="links"></a>링크

- **개인 정보 취급 방침** (필수): 조직의 개인 정보 취급 방침에 대 한 링크입니다. 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 하 고 유효한 개인 정보 취급 방침을 제공 해야 합니다.
- **Csp 프로그램 마케팅 자료** (선택 사항): 제품을 [CSP (클라우드 솔루션 공급자)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) 프로그램으로 확장 하도록 선택 하는 경우 마케팅 자료에 대 한 링크를 제공 합니다. CSP는 CSP 파트너가 제품을 번들, 시장 및 재판매 수 있도록 하 여 제품을 더 광범위 한 범위의 고객으로 확장 합니다. 이러한 대리점은 제품 마케팅에 대 한 자료에 액세스 해야 합니다. 자세한 내용은 [출시 전 서비스](https://partner.microsoft.com/reach-customers/gtm)를 참조 하세요.
- **유용한 링크** (선택 사항): **제목** 및 **URL**을 제공 하 여 나열 된 앱 또는 관련 서비스에 대 한 선택적 보조 온라인 문서입니다. **+ URL 추가**를 클릭 하 여 유용한 링크를 더 추가 합니다.

#### <a name="contact-information"></a>연락처 정보

- **연락처**: 각 고객 연락처에 대해 직원 **이름**, **전화 번호**및 **전자 메일** 주소를 제공 합니다.  이는 공개적으로 표시 *되지* 않습니다. 지원 **URL** 은 **지원 담당자** 그룹에도 필요 합니다.  이 정보 *는* 공개적으로 표시 됩니다.

**지원 담당자** (필수): 일반 지원 질문의 경우

**엔지니어링 연락처** (필수): 기술 관련 질문입니다.

**채널 관리자 연락처** (필수): CSP 프로그램과 관련 된 재판매인 질문의 경우

#### <a name="files-and-images"></a>파일 및 이미지

- **문서** (필수): 제품에 대 한 관련 마케팅 문서를 PDF 형식으로 추가 하 여 제품에 대 한 최소 1 개 (1) 및 최대 3 개의 문서를 제공 합니다.
- **이미지** (선택 사항): 제품 로고 이미지가 marketplace 전체에 표시 될 수 있으며, 크기는 작음: 48 x 48 픽셀 _(필수),_ 보통: 90 x 90 픽셀, 크게: 216 x 216 픽셀 _(필수),_ 전체: 255 x 115 픽셀, 주인공: 815 x 290 픽셀입니다. 모든 이미지는에 있어야 합니다. PNG 형식입니다.
- **스크린샷** (필수): 제품을 보여 주는 스크린샷 추가 최대 5 개 (5)의 스크린샷에 추가 될 수 있으며 1280 x 720 픽셀에 크기를 지정 해야 합니다. 모든 이미지는에 있어야 합니다. PNG 형식입니다.
- **비디오** (선택 사항): 제품을 보여 주는 비디오에 대 한 링크를 추가 합니다. 고객에게 제품과 함께 표시되는 YouTube 및/또는 Vimeo 동영상의 링크를 사용할 수 있습니다. 또한 비디오의 미리 보기 이미지를 입력 해야 합니다 .이 이미지는 PNG 형식의 1280 x 720 픽셀로 크기가 지정 됩니다. 제품 당 최대 4 개의 비디오를 표시할 수 있습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

- [Marketplace 제품 목록에 대 한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>미리 보기

**미리 보기** 탭에서는 제품을 광범위 한 marketplace 대상에 게시 하기 전에 제품을 릴리스할 수 있는 제한 된 **미리 보기 대상을** 정의할 수 있습니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 확인 한 후 **Live Go** 를 선택 하 여 제품을 marketplace 공개 대상에 게 live 게시할 수 있습니다.

- **미리 보기 대상 정의: 선택적 설명과 함께 단일 AAD/MSA 계정 전자 메일을 줄 단위로 추가 합니다.**

Live 게시 전에 제품의 유효성을 검사 하는 데 도움이 되도록 기존 Microsoft 계정 (MSA) 또는 Azure Active Directory 계정에 대해 수동으로 최대 10 개의 전자 메일 주소를 추가 하거나 20 (20)을 추가 합니다. 이러한 계정을 추가 하 여 marketplace에 게시 하기 전에 제품에 대 한 미리 보기 액세스를 허용 하는 대상 그룹을 정의 합니다. 제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 테스트 하기 위한 미리 보기 대상을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 사용자는 개인 대상과 다릅니다. 미리 보기 대상 그룹은 마켓플레이스에 라이브 게시 _되기 전에_ 제품에 대 한 액세스를 허용 합니다. 계획을 만들어 개인 사용자만 사용할 수 있도록 설정할 수도 있습니다. **계획 목록** 탭에서 **비공개 요금제** 인 개인 사용자를 정의할 수 있습니다. 그런 다음 Azure 테넌트 Id를 사용 하 여 최대 2만 고객의 개인 사용자를 정의할 수 있습니다.

## <a name="technical-configuration"></a>기술 구성

**기술 구성** 탭에서는 제품에 연결 하는 데 사용 되는 기술 세부 정보 (URL 경로, webhook, 테넌트 id 및 앱 id)를 정의 합니다. 이 연결을 통해 최종 고객을 위해 제품을 프로 비전 할 수 있습니다. 수집 된 필드의 사용을 설명 하는 다이어그램은 SaaS 처리 [api](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2)에 대 한 설명서에서 확인할 수 있습니다.

- **방문 페이지 url** (필수): marketplace에서 제품을 확보 한 후 고객이 게 될 사이트 Url을 정의 합니다. 이 URL은 고객이 페이지로 라우팅되는 경우 토큰을 수신 하는 엔드포인트입니다. 이 토큰은 처리 Api에서 해결을 사용 하 여 프로 비전 세부 정보를 교환할 수 있습니다. 이러한 세부 정보 및 수집 하는 다른 항목은 등록을 완료 하 고 구매를 활성화 하는 사용자 환경에서 빌드된 고객 대화형 웹 페이지의 일부로 사용할 수 있습니다.

- **연결 webhook** (필수): Microsoft에서 고객을 대신 하 여 사용자에 게 보내야 하는 모든 비동기 이벤트의 경우 (예: SaaS 구독이 유효 하지 않음) 연결 webhook를 제공 해야 합니다. 웹 후크 시스템이 아직 없는 경우 가장 간단한 구성은 게시 되는 모든 이벤트를 수신 대기 하 고 적절 하 게 처리 하는 HTTP 엔드포인트 논리 앱을 보유 하는 것입니다 (예: https:\//prod-1westus.logic.azure.com:443/work). 자세한 내용은 [Logic Apps의 HTTP 엔드포인트를 통해 워크플로 호출, 트리거 또는 중첩](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint)을 참조하세요.

- **AZURE ad 테넌트 ID** (필수): Azure Portal 내에서는 두 서비스 간의 연결에 대 한 유효성을 검사할 수 있도록 [AD (Azure Active Directory) 앱을 만들어야](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 합니다. [테넌트 ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 찾으려면 Azure Active Directory로 이동 하 고 **속성**을 선택한 다음 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.

- **AZURE AD 앱 id** (필수): [응용 프로그램 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 및 인증 키도 필요 합니다. 이러한 값을 얻으려면 Azure Active Directory로 이동 하 여 **앱 등록**을 선택한 다음 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다. 인증 키를 찾으려면 **설정** 으로 이동 하 고 **키**를 선택 합니다. 설명 및 기간을 제공 하 고 숫자 값이 제공 됩니다.

>[!Note]
>Azure 응용 프로그램 ID는 게시자 ID와 연결 되므로 모든 제품에서 동일한 응용 프로그램 ID를 사용 해야 합니다.

## <a name="plan-overview"></a>계획 개요

**계획 개요** 탭에서는 동일한 제품 내에서 다양 한 계획 옵션을 제공할 수 있습니다. 이러한 계획 (Sku 라고도 함)은 버전, 수익 화 또는 서비스 계층의 측면에서 다를 수 있습니다. Marketplace에서 제품을 판매 하려면 계획을 하나 이상 설정 해야 합니다.

일단 만들어지면 계획 이름, Id, 가격 책정 모델, 가용성 (공용 또는 개인), 현재 게시 상태 및 사용 가능한 모든 동작이 표시 됩니다.

**계획 개요** 에서 사용할 수 있는 **작업** 은 계획의 현재 상태에 따라 달라 지 며 다음을 포함할 수 있습니다.

- 계획 상태가 **초안** -초안 삭제 인 경우
- 계획 상태가 **라이브** -계획 판매 중지 또는 개인 대상 사용자 동기화 인 경우

**새 계획 만들기** (Microsoft를 통해 판매를 선택 하는 사용자에 대 한 계획 중 하나)

- **계획 ID:** 이 제안의 각 계획에 대 한 고유한 계획 ID를 만듭니다. 이 ID는 제품 URL 및 Azure Resource Manager 템플릿 (해당 하는 경우)의 고객에 게 표시 됩니다. 소문자, 영숫자, 대시 또는 밑줄만 사용 합니다. 이 계획 ID에는 최대 50 자까지 사용할 수 있습니다. 만들기를 선택한 후에는 ID를 수정할 수 없습니다.
- **계획 이름:** 제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 이 제안의 각 계획에 대 한 고유한 제안 이름을 만듭니다. 계획 이름은 동일한 제품에 포함 될 수 있는 소프트웨어 요금제를 구분 하는 데 사용 됩니다 (예: 제품 이름: Windows Server; 요금제: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>계획 목록

**계획 목록** 탭에는 요금제를 사용할 수 있는 언어 (및 시장)가 표시 됩니다. 현재 영어 (미국)는 사용할 수 있는 유일한 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 마켓플레이스 세부 정보 (제품 이름, 설명, 검색어 등)를 정의 해야 합니다.

#### <a name="plan-listing-details"></a>계획 목록 세부 정보

계획 언어 중 하나를 선택 하면 **이름** 및 설명 등의 **계획 목록** 정보가 표시 됩니다 **.**

- **이름**: 미리 보기 **새 요금제** 항목을 기준으로 미리 채워져 있으며 marketplace에 표시 되는 제품의 "소프트웨어 계획" 제목으로 표시 됩니다.
- **설명:** 이 설명에서는이 소프트웨어 계획의 고유한 정의와 제품 내 다른 소프트웨어 요금제의 차이점을 설명할 수 있는 기회를 제공 합니다. 최대 500 자까지 포함할 수 있습니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

#### <a name="plan-pricing-and-availability"></a>가격 책정 및 가용성 계획

**가격 책정 및 가용성** 탭에서는이 요금제를 사용할 수 있는 시장, 원하는 수익 화 모델, 가격 및 청구 기간을 구성할 수 있습니다. 또한 계획이 모든 사용자에 게 표시 되도록 할지 아니면 특정 고객 에게만 표시 되도록 할지를 지정할 수 있습니다 (개인 사용자).

##### <a name="enabling-free-trials"></a>무료 평가판 사용

상업적 marketplace를 통한 SaaS 제품을 사용 하면 Microsoft를 통해 판매 하는 경우 1 개월 무료 평가판을 제공할 수 있습니다. 요금제 요금제를 제외한 모든 청구 모델 및 용어의 경우 무료 평가판이 지원 됩니다. 이 옵션을 사용 하면 고객은 한 달의 무료 액세스를 통해 낮은 장벽을 입력할 수 있습니다.  제품 내에서 요금제에 대 한 무료 평가판을 사용 하도록 선택 하는 경우 고객은 초기 1 개월 기간이 끝나기 전에 유료 구독으로 변환할 수 없습니다.  이 기간 동안 제품을 구매 하는 고객은 무료 평가판을 사용할 수 있는 지원 되는 요금제를 사용해 보고 둘 사이를 변환할 수 있습니다.  유료 구독으로의 변환은 해당 기간이 끝날 때 자동으로 수행 됩니다.

>[!Note]
>고객이 무료 평가판을 사용 하지 않고 요금제로 전환 하도록 선택 하는 경우에는 변환이 수행 되지만 무료 평가판은 즉시 손실 됩니다.  또한 고객이 요금제에 대 한 요금을 지불 하 고 나면 무료 평가판을 지 원하는 SKU로 변환 되는 경우에도 더 이상 동일한 구독에서 더 이상 무료 평가판을 얻을 수 없습니다.

제품의 각 요금제에 대해 무료 평가판을 구성 하는 기능을 사용할 수 있습니다. 각 제품에 대 한 가격 책정 및 가용성으로 이동 하 여 1 개월 평가판을 허용 하는 확인란을 선택 하면 됩니다.

![1 개월 무료 평가판 확인란](./media/free-trial-enable.png)

>[!Note]
>불가능 제안을 무료 평가판으로 게시 한 후에는 해당 계획에 대해 사용 하지 않도록 설정할 수 없습니다. 첫 번째 게시에 대해이 설정이 올바른지 확인 하 여 계획을 다시 만들 필요가 없도록 합니다.

현재 무료 평가판에 참여 하는 고객 구독에 대 한 정보를 얻으려면 true 또는 false로 표시 되는 `isFreeTrial`새 API 속성을 사용 합니다. 자세한 내용은 [SaaS Get SUBSCRIPTION API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription)를 참조 하세요.

>[!Note]
>Marketplace 계량 서비스를 활용 하는 요금제에 대 한 무료 평가판은 지원 되지 않습니다.

#### <a name="markets"></a>시장

- **시장 편집** (선택 사항)

모든 요금제는 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 요금제를 사용할 수 있도록 하려는 시장 위치에 대 한 확인란을 선택 합니다. "세금 송금" 국가를 선택 하는 데 사용 되는 검색 상자와 단추를 사용 하 여 Microsoft 송금 판매 및 세금을 대신 사용 합니다.

요금제에 대 한 가격을 미국 달러 (USD)로 설정 하 고 다른 시장 위치를 추가 하는 경우 새 시장의 가격은 현재 환율이 따라 계산 됩니다. 게시 하기 전에 항상 각 시장의 가격을 검토 해야 합니다. 변경 내용을 저장 한 후 "가격 (.xlsx) 내보내기" 링크를 사용 하 여 가격 책정을 검토할 수 있습니다.

#### <a name="pricing"></a>가격 책정

- **가격 책정 모델**: 고정 요금 또는 좌석 기반

**기본 요율:** 월별 또는 연간 가격의 단일 가격 가격으로 제품에 대 한 액세스를 사용 하도록 설정 합니다. 이를 사이트 기반 가격 책정이 라고도 합니다. 이 가격 책정 모델을 사용 하는 경우 필요에 따라 표준 단위에 따라 고객에 게 요금을 청구 하기 위해 marketplace 계량 서비스 API를 사용 하는 요금제 요금제를 정의할 수 있습니다.  요금제 요금 청구에 대 한 자세한 내용은 [marketplace 계량 서비스를 사용 하 여 요금제 요금제](./saas-metered-billing.md)를 참조 하세요.

**사용자 당:** 제품에 액세스 하는 사용자 수 또는 사용자 수를 기준으로 가격으로 제품에 대 한 액세스를 사용 하도록 설정 합니다. 이 사용자 기반 모델을 사용 하면 가격에 따라 허용 되는 사용자의 최소 및 최대 수를 설정할 수 있습니다. 이러한 방식으로 여러 계획을 구성 하 여 사용자 수를 기준으로 다양 한 가격을 구성할 수 있습니다.  이러한 필드는 선택 사항입니다. 이를 선택 하지 않은 경우 사용자 수는 제한이 없는 것으로 해석 됩니다 (최소 1, 시스템에서 지원할 수 있는 최대). 이러한 필드는 계획에 대 한 업데이트의 일부로 편집할 수 있습니다.

게시 된 후에는 청구 가격 책정 모델을 변경할 수 없습니다. 또한 동일한 제품에 대 한 모든 요금제는 동일한 가격 책정 모델을 공유 해야 합니다.

- **청구 기간**: 월별 또는 연간

고객이 나열 된 가격을 지불 해야 하는 빈도를 선택 합니다. 하나 이상의 월별 또는 연간 가격을 제공 해야 하며, 그렇지 않으면 두 옵션을 모두 고객이 사용할 수 있습니다.

- **가격**: 월 당 usd 또는 연간 usd

현지 통화 (USD = 미국 달러)로 설정 된 가격은 설치 중 사용 가능한 현재 환율을 사용 하 여 선택한 모든 시장의 현지 통화로 변환 됩니다. 가격 책정 스프레드시트를 내보내고 각 시장에서 가격을 검토 하 여 게시 하기 전에 이러한 가격의 유효성을 검사 합니다. 개별 시장에서 사용자 지정 가격을 설정 하려면 가격 책정 스프레드시트를 수정 하 고 가져오세요. 이 가격을 확인 하 고 이러한 설정을 소유할 책임이 있습니다.
*\*가격 책정 데이터를 내보낼 수 있도록 가격 책정 변경 내용을 먼저 저장 해야 합니다.*

계획이 게시 된 후 변경 될 수 있는 사항에 대 한 몇 가지 제한 사항이 있으므로 게시 하기 전에 가격을 신중 하 게 검토 합니다.

- 계획이 게시 된 후에는 가격 책정 모델을 변경할 수 없습니다.
- 요금제에 대 한 청구 기간이 게시 된 후에는 나중에 제거할 수 없습니다.
- 요금제의 시장 가격은 게시 된 후에는 나중에 변경할 수 없습니다.

### <a name="plan-audience"></a>대상 계획

모든 사용자 또는 선택한 특정 대상에 게 표시 되도록 각 계획을 구성 하는 옵션이 있습니다. Azure AD 테넌트 Id를 사용 하 여이 제한 된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

#### <a name="privacy"></a>개인 정보

- **이 계획은 개인 계획입니다** (선택 사항).

계획을 비공개로 설정 하 고 선택한 제한 된 대상 그룹에만 표시 하려면이 확인란을 선택 합니다. 비공개 계획으로 게시 한 후에는 대상 그룹을 업데이트 하거나 계획을 모든 사용자가 사용할 수 있도록 선택할 수 있습니다. 모든 사용자에 게 표시 되는 계획을 게시 한 후에는 모든 사용자에 게 계속 표시 되어야 합니다. (요금제를 개인 계획으로 다시 구성할 수 없습니다.)

- **제한 된 대상 그룹 (테넌트 Id)**

이 비공개 요금제에 대 한 액세스 권한이 있는 대상 그룹을 할당 합니다. 지정 된 각 테넌트 ID에 대 한 설명을 포함 하는 옵션과 함께 테넌트 Id를 사용 하 여 액세스 권한을 할당 합니다. .Csv 스프레드시트 파일을 가져오는 경우 최대 10 개의 테넌트 Id를 추가 하거나 2만 고객 테넌트 Id를 추가할 수 있습니다.

테넌트는 ID가 GUID (GUID (Globally Unique Identifier)로 표시 되 고, 리소스를 식별 하는 데 사용 되는 128 비트 정수) 인 조직의 표현입니다. 조직 또는 앱 개발자가 Microsoft와의 관계를 만들 때(예: Azure, Microsoft Intune 또는 Microsoft 365에 등록) 조직 또는 앱 개발자가 받는 Azure AD의 전용 인스턴스입니다. 각 Azure AD 테넌트는 서로 전혀 다르고 다른 Azure AD 테넌트와 별개입니다. 테넌트를 확인 하려면 응용 프로그램을 관리 하는 데 사용 하려는 계정으로 Azure Portal에 로그인 합니다. 테넌트가 있으면 자동으로 로그인되고 계정 이름 바로 아래에 테넌트 이름이 표시됩니다. Azure Portal의 오른쪽 위에 있는 사용자의 계정 이름을 가리켜 사용자의 이름, 이메일, 디렉터리/테넌트 ID(GUID) 및 사용자의 도메인을 표시합니다. 계정이 여러 테넌트와 연결되어 있으면, 계정 이름을 선택하여 테넌트 간에 전환할 수 있는 메뉴를 열 수 있습니다. 각 테넌트가 자기만의 ID를 가지고 있습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용 하 여 조직의 테넌트 ID를 조회할 수도 있습니다.

SaaS 제공이 테넌트 Id를 사용 하 여 개인 사용자를 정의 하는 동안 다른 제품 유형은 Azure 구독 Id (Guid로도 표시 됨)를 사용할 수 있습니다.

> [!NOTE]
> 비공개 사용자 (또는 제한 된 대상)는 미리 보기 대상과 다릅니다. **[미리 보기](#preview)** 탭에서 미리 보기 대상 그룹을 정의할 수 있습니다. 제품을 marketplace에 게시 하기 *전에* 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 개인 대상 지정은 특정 계획에만 적용 되지만 미리 보기 대상은 모든 요금제를 볼 수 있지만 (비공개 또는 아님) 계획을 테스트 하 고 유효성을 검사 하는 동안에는 제한 된 미리 보기 기간 동안만 볼 수 있습니다.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Marketplace 제품 내 계획의 예제 목록

![노트와 함께 나열 된 마켓플레이스 계획 예제](./media/marketplace-plan.svg)

## <a name="test-drive"></a>시험 사용

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="cloud-solution-provider-csp-reseller-audience"></a>CSP (클라우드 솔루션 공급자) 대리점 대상

CSP 프로그램에서 제품을 사용할 수 있도록 리더로 선택 클라우드 솔루션 공급자는 고객에 게 번들로 제공 되는 솔루션의 일부로 제품을 판매할 수 있습니다. 자세한 내용은 [클라우드 솔루션 공급자](https://go.microsoft.com/fwlink/?linkid=2111109)를 참조 하세요.

## <a name="publish"></a>게시

제품의 필수 섹션을 모두 완료 한 후에는 포털의 오른쪽 위 모서리에서 **게시** 를 선택 합니다. **검토 및 게시** 페이지로 다시 이동 됩니다.

#### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

이 제품을 처음으로 게시 하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다.
    - *시작 되지 않음* -섹션이 작업 되지 않아 완료 해야 함을 의미 합니다.
    - *불완전* -섹션에 수정 해야 하거나 더 많은 정보를 제공 해야 하는 오류가 있음을 의미 합니다. 섹션으로 돌아가서 업데이트 해야 합니다.
    - *완료* -섹션이 완료 되었고 필요한 모든 데이터가 제공 되었으며 오류가 없음을 의미 합니다. 제품의 모든 섹션은 제품을 제출 하기 전에 완전 한 상태 여야 합니다.
- 앱을 이해 하는 데 도움이 되는 보조 노트 뿐만 아니라 앱이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다.
- **제출**을 선택 하 여 게시에 대 한 제품을 제출 합니다. 제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 시기를 알려 주는 전자 메일을 보내 드리겠습니다. 파트너 센터로 돌아가서 제품을 공개 (또는 개인 제품의 경우 비공개 사용자에 게 공개)에 게시 하려면 [ **라이브]** 를 선택 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
