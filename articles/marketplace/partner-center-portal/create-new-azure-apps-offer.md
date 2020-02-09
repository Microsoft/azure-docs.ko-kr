---
title: 상업적 Marketplace에서 새 Azure 앱 제품 만들기
description: Microsoft 파트너 센터에서 상용 Marketplace 포털을 사용 하 여 Azure Marketplace, AppSource 또는 CSP (클라우드 솔루션 공급자) 프로그램을 통해 나열 하거나 판매 하기 위해 새 Azure Apps 제품을 만드는 방법입니다.
author: MaggiePucciEvans
manager: evansma
ms.author: evansma
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 69d7bc81f5adc4797be86f946fdb656b4b6cc682
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760903"
---
# <a name="create-an-azure-application-offer"></a>Azure 애플리케이션 제품 만들기

상용 marketplace에 Azure 응용 프로그램 제품을 게시 하는 단계는 여기에 설명 되어 있습니다.

## <a name="azure-application-offer-type"></a>Azure 응용 프로그램 제공 유형

이 항목에서는 Azure 응용 프로그램 제품에 대 한 기본 사항을 간략하게 설명 합니다.  Marketplace에 새 Azure 응용 프로그램 제품을 게시 하는 프로세스를 시작 하기 전에 이러한 개념을 잘 알고 있어야 합니다.

### <a name="publishing-overview"></a>게시 개요

[Azure Marketplace의 솔루션 템플릿 및 관리 되는 응용](https://channel9.msdn.com/Events/Build/2018/BRK3603) 프로그램은 Azure 응용 프로그램 제품 유형에 대 한 소개입니다.

* 제공 되는 제공 형식
* 필요한 기술 자산
* Azure Resource Manager 템플릿을 제작 하는 방법
* 앱 UI 개발 및 테스트
* 앱 제공을 게시 하는 방법
* 응용 프로그램 검토 프로세스입니다.

### <a name="types-of-azure-application-plans"></a>Azure 응용 프로그램 계획 유형

Azure 응용 프로그램 계획, 관리 되는 응용 프로그램 및 솔루션 템플릿에는 두 가지 종류가 있습니다.

* **솔루션 템플릿은** Marketplace에 솔루션을 게시 하는 주요 방법 중 하나입니다. 이 계획 유형은 단일 VM (가상 머신) 이외에 솔루션에 추가 배포 및 구성 자동화가 필요한 경우에 사용 됩니다.  솔루션 템플릿을 사용 하면 Vm, 네트워킹 및 저장소 리소스를 비롯 한 둘 이상의 리소스를 자동으로 제공 하 여 복잡 한 IaaS 솔루션을 제공할 수 있습니다.  솔루션 템플릿을 빌드하는 방법에 대 한 자세한 내용은 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 설명서를 참조 하세요.

* **관리 되는 응용 프로그램** 은 한 가지 주요 차이점을 제외 하 고 솔루션 템플릿과 유사 합니다. 관리되는 애플리케이션에서 리소스는 앱 게시자가 관리하는 리소스 그룹에 배포됩니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. 게시자는 지속적인 솔루션 지원을 위한 비용을 지정합니다. 관리 되는 응용 프로그램을 사용 하 여 완전히 관리 되는 턴키 응용 프로그램을 쉽게 빌드하고 고객에 게 제공할 수 있습니다.  관리되는 애플리케이션의 장점 및 유형에 대한 자세한 내용은 [Azure Managed Applications 개요](https://docs.microsoft.com/azure/managed-applications/overview)를 참조하세요.

모든 Azure 응용 프로그램에는 `.zip` 보관의 루트 폴더에 적어도 두 개 이상의 파일이 포함 되어 있습니다.

* [Maintemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)라는 리소스 관리자 템플릿 파일입니다.  고객의 Azure 구독에 배포할 리소스를 정의 하는 템플릿입니다.  리소스 관리자 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조 하세요.

* [Createuidefinition. json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview)이라는 Azure 응용 프로그램 만들기 환경의 사용자 인터페이스 정의입니다.  사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

모든 새 Azure 응용 프로그램 제품은 [azure 파트너 고객 사용 특성 GUID](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)를 포함 해야 합니다.

### <a name="before-you-begin"></a>시작하기 전에

빠른 시작, 자습서 및 샘플을 제공하는 다음 Azure 애플리케이션 설명서를 검토합니다.

* [Azure Resource Manager 템플릿 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* 빠른 시작:

    * [Azure Quickstart 템플릿](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates)
    * [애플리케이션 정의 게시](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [서비스 카탈로그 앱 배포](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* 자습서:

    * [정의 파일 만들기](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [마켓플레이스 애플리케이션 게시](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* 샘플:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [관리되는 애플리케이션 솔루션](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>기술 기술 자료의 기본 사항

이러한 자산을 설계, 구축 및 테스트하려면 시간이 걸리고 Azure 플랫폼과 제안을 작성하는 데 사용되는 기술에 대한 기술적 지식이 모두 필요합니다.

엔지니어링 팀에는 다음 Microsoft 기술에 대한 지식이 있어야 합니다.

* [Azure 서비스](https://azure.microsoft.com/services/)에 대 한 기본 이해
* [Azure 응용 프로그램을 디자인 하 고 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법입니다.
* [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)및 [azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)에 대 한 작업 지식.
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 대 한 작업 지식.
* [JSON](https://www.json.org/)에 대 한 실무 지식.

### <a name="suggested-tools"></a>권장되는 도구

Azure 애플리케이션을 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)(다음 확장 포함):
    * 확장: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Azure 개발자 도구](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 검토할 수 있습니다.  Visual Studio를 사용 하는 경우에도 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)합니다.

## <a name="create-an-azure-application-offer"></a>Azure 애플리케이션 제품 만들기

Azure 응용 프로그램 제품을 만들려면 먼저 [파트너 센터 계정을 만들고](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) **개요** 탭이 선택 된 상태로 [상용 마켓플레이스 대시보드](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)를 열어야 합니다.

>[!Note]
>제품이 게시 되 면 파트너 센터에서 제공 하는 제품에 대 한 편집 내용은 다시 게시 한 후에만 시스템 및 상점 업데이트 됩니다.  변경 작업을 수행한 후 게시에 대 한 제품을 제출 했는지 확인 하세요.

### <a name="create-a-new-offer"></a>새 제안 만들기

**+ 새 제품** 단추를 선택한 다음 **Azure 애플리케이션** 메뉴 항목을 선택 합니다. **새 제품** 대화 상자가 표시 됩니다.

### <a name="offer-id-and-alias"></a>제품 ID 및 별칭

* **제품 ID**: 계정의 각 제품에 대 한 고유 식별자입니다. 이 ID는 marketplace 제품 및 Azure Resource Manager 템플릿 (해당 하는 경우)에 대 한 URL 주소의 고객에 게 표시 됩니다. <br> <br> 제품 ID는 소문자 영숫자 (하이픈 및 밑줄, 공백 없음)를 포함 해야 합니다. 50 자로 제한 되며 만들기를 선택한 후에는 변경할 수 없습니다. <br> <br> 예를 들어 여기에 `test-offer-1` 입력 하는 경우 제품 URL이 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`됩니다. 

* **제품 별칭**: 파트너 센터 내의 제품을 참조 하는 데 사용 되는 이름입니다. 이 이름은 marketplace에서 사용 되지 않으며 고객에 게 표시 되는 제품 이름 및 기타 값과 다릅니다. **만들기**를 선택한 후에는이 값을 변경할 수 없습니다.

**제품 ID** 및 **제품 별칭**을 입력 한 후 **만들기**를 선택 합니다. 그러면 제품의 다른 모든 부분에서 작업을 수행할 수 있습니다.

## <a name="offer-setup"></a>제품 설정

**제품 설정** 페이지에서 다음 정보를 요청 합니다. 이러한 필드를 완료 한 후에는 **저장** 을 선택 해야 합니다.

### <a name="test-drive"></a>시험 사용

시험 사용한 고객에 게 제품을 고객에 게 제공 하는 좋은 방법은 ' 구매 전 시도 '를 선택 하 여 변환 및 높은 우량 잠재 고객을 생성 하는 옵션을 제공 하는 것입니다. [테스트 드라이브에 대해 자세히 알아보세요.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

테스트 드라이브를 사용 하도록 설정 하려면 **테스트 드라이브 사용** 확인란을 선택 합니다. 그런 다음, [테스트 드라이브 기술 구성](#types-of-azure-application-plans) 에서 데모 환경을 구성 해야 합니다 .이 구성에서는 고객이 고정 기간 동안 제품을 사용해 볼 수 있습니다. 

>[!Note]
>모든 Azure 응용 프로그램은 Azure Resource Manager 템플릿을 사용 하 여 구현 되므로 Azure 애플리케이션에 대해 구성할 수 있는 유일한 테스트 드라이브 유형은 [Azure Resource Manager 기반 테스트 드라이브](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)입니다.

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

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace에 대 한 표준 계약

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>제품 목록

제품 목록 페이지에는 제품이 나열 될 언어가 표시 됩니다. 현재 **영어 (미국)** 만 사용할 수 있는 옵션입니다.

각 언어/시장에서 marketplace 세부 정보 (제품 이름, 설명, 이미지 등)를 정의 해야 합니다. 이 정보를 제공 하려면 언어/시장 이름을 선택 합니다.

> [!NOTE]
> 제품 설명 (예: 설명, 문서, 스크린샷, 사용 약관 등)은 영어로 시작 하지 않아도 됩니다. "이 응용 프로그램은 [영어가 아닌 언어] 에서만 사용할 수 있습니다." 또한 제품 목록 콘텐츠에 사용 되는 것 이외의 언어로 콘텐츠를 제공 하는 *데 유용한 링크 URL* 을 제공 하는 것도 허용 됩니다.

### <a name="name"></a>이름

여기에 입력 하는 이름은 고객에 게 제품 목록의 제목으로 표시 됩니다. 이 필드는 제품을 만들 때 **제공 별칭** 에 입력 한 텍스트로 미리 채워져 있지만이 값을 변경할 수 있습니다. 이 이름은 상표 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50 자를 초과할 수 없으며, 모든 emojis를 포함할 수 없습니다.

### <a name="summary"></a>요약

Marketplace 검색 결과에서 사용할 수 있는 제품에 대 한 간략 한 설명을 제공 합니다 (최대 100 자).

### <a name="long-summary"></a>긴 요약

제품에 대 한 더 긴 설명 (최대 256 자)을 제공 합니다. 설명은 marketplace 검색 결과에서 사용할 수 있습니다.

### <a name="description"></a>Description

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

### <a name="search-keywords"></a>검색 키워드

필요에 따라 고객이 marketplace에서 제품을 찾을 수 있도록 최대 3 개의 검색 키워드를 입력할 수 있습니다. 최상의 결과를 위해 설명에 이러한 키워드를 사용 해 보세요.

### <a name="support-urls"></a>지원 Url

이 섹션에서는 고객이 제품에 대해 더 이해 하는 데 도움이 되는 링크를 제공할 수 있습니다.

#### <a name="privacy-policy-url"></a>개인 정보 취급 방침 URL

조직의 개인 정보 취급 방침에 대 한 URL을 입력 합니다. 사용자는 앱이 개인 정보 법률 및 규정을 준수 하는지 확인 하 고 유효한 개인 정보 취급 방침을 제공 하는 일을 담당 합니다.

#### <a name="useful-links"></a>유용한 링크

솔루션에 대 한 선택적 보조 온라인 문서를 제공 합니다.  **+ 링크 추가**를 클릭 하 여 유용한 링크를 더 추가 합니다.

### <a name="contacts"></a>연락처

이 섹션에서는 **지원 연락처** 와 **엔지니어링 연락처**에 대 한 이름, 전자 메일 및 전화 번호를 제공 해야 합니다. 이 정보는 고객에 게 표시 되지 않지만 Microsoft에서 사용할 수 있으며 CSP 파트너에 게 제공 될 수 있습니다.

**지원 연락처** 섹션에서 CSP 파트너가 제품에 대 한 지원을 찾을 수 있는 **지원 URL** 도 제공 해야 합니다.

### <a name="marketplace-images"></a>Marketplace 이미지

이 섹션에서는 고객에 게 제품을 표시할 때 사용 되는 로고 및 이미지를 제공할 수 있습니다. 모든 이미지는 .png 형식 이어야 합니다.

#### <a name="store-logos"></a>매장 로고

**소형 (48 x 48)** , **보통 (90 x 90)** 및 **큼 (216 x 216)** 의 세 가지 크기로 제품 로고를 제공 합니다.

#### <a name="hero"></a>Hero

주인공 이미지는 선택 사항입니다. 제공 하는 경우 815 x 290 픽셀을 측정 해야 합니다.

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 스크린샷 추가 스크린샷을 5 개까지 추가할 수 있습니다. 모든 스크린샷 1280 x 720 픽셀 이어야 합니다.

#### <a name="videos"></a>비디오

필요에 따라 제품을 보여 주는 비디오를 최대 5 개까지 추가할 수 있습니다. 이러한 비디오는 YouTube 및/또는 Vimeo에서 호스팅되어야 합니다. 각 항목에 대해 비디오 이름, 해당 URL 및 비디오의 미리 보기 이미지 (1280 x 720 픽셀)를 입력 합니다.

#### <a name="additional-marketplace-listing-resources"></a>리소스를 나열 하는 추가 마켓플레이스

- [Marketplace 제품 목록에 대 한 모범 사례](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>미리 보기

**미리 보기** 탭에서는 제품을 광범위 한 marketplace 대상에 게시 하기 전에 제품의 유효성을 검사할 수 있는 제한 된 **미리 보기 대상을** 정의할 수 있습니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 확인 한 후 제품이 marketplace 공용 대상 그룹에 live 게시 되기 전에 **라이브 전환** 을 선택 해야 합니다.

미리 보기 대상 사용자는 Azure 구독 ID Guid에 의해 식별 되며 각 항목에 대 한 선택적 설명과 쌍을 이룹니다.  이러한 필드는 고객에 게 표시 되지 않습니다.

Azure 구독 Id를 최대 10 개까지 수동으로 추가 하거나 CSV 파일을 업로드 하는 경우 최대 100까지 추가 합니다.  이러한 구독을 추가 하 여 제품을 완전히 게시 하기 전에 미리 보기에 대 한 액세스를 허용 하는 대상 그룹을 정의 합니다.  제품이 이미 라이브 상태인 경우 제품에 대 한 변경 내용 또는 업데이트를 테스트 하기 위한 미리 보기 대상을 정의할 수 있습니다.

>[!Note]
>미리 보기 대상 사용자는 개인 대상과 다릅니다. 미리 보기 대상 그룹은 마켓플레이스에 라이브 게시 *되기 전에* 제품에 대 한 액세스를 허용 합니다. 계획 가용성 탭을 사용 하 여 계획을 만들고 개인 사용자 에게만 사용할 수 있도록 설정할 수도 있습니다.  사용자의 미리 보기는 제품이 marketplace에 완전히 게시 된 후 개인 사용자 에게만 제공 되는 계획을 포함 하 여 모든 계획을 확인 하 고 유효성을 검사할 수 있습니다.

## <a name="plan-overview"></a>계획 개요

**계획 개요** 탭에서는 동일한 제품 내에서 다른 계획 옵션을 제공할 수 있습니다. 이러한 계획 (Cloud 파트너 포털의 Sku 라고도 함)은 계획 유형 (솔루션 템플릿 및 관리 되는 응용 프로그램), 수익 화 또는 대상의 측면에서 다를 수 있습니다.  Marketplace에서 제품을 나열 하기 위해 하나 이상의 계획을 구성 합니다.

만든 후에는 계획 이름, Id, 계획 유형, 가용성 (공용 또는 개인), 현재 게시 상태 및이 탭에서 사용 가능한 모든 작업을 볼 수 있습니다.

**계획 개요** 에서 사용할 수 있는 **작업** 은 계획의 현재 상태에 따라 달라 지 며 다음을 포함할 수 있습니다.

* 계획 상태가 **초안** – 초안 삭제 인 경우
* 계획 상태가 **라이브** – 계획 판매 중지 또는 개인 대상 사용자 동기화를 중지 합니다.

### <a name="create-new-plan"></a>새 계획 만들기

***계획 id*** -이 제안의 각 계획에 대 한 고유한 계획 id를 만듭니다. 이 ID는 제품 URL의 고객에 게 표시 됩니다.  소문자, 영숫자, 대시 또는 밑줄만 사용 합니다. 이 계획 ID에는 최대 50 자까지 사용할 수 있습니다. 만들기를 선택한 후에는이 ID를 수정할 수 없습니다.

***계획 이름*** -제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 이 제안의 각 계획에 대 한 고유한 제안 이름을 만듭니다. 계획 이름은 동일한 제품에 포함 될 수 있는 소프트웨어 요금제를 구분 하는 데 사용 됩니다 (예: 제품 이름: Windows Server; 요금제: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>설정 계획

**계획 설정** 탭에서는 계획의 유형에 대 한 고급 구성을 설정 하 고, 다른 계획의 패키지를 다시 사용 하 고, 계획을 사용할 수 있는 클라우드를 설정할 수 있습니다.  이 탭에 대 한 답변은 동일한 계획에 대 한 다른 탭에 표시 되는 필드에 영향을 줍니다.

#### <a name="plan-type"></a>계획 유형

[Azure 응용 프로그램 계획의 유형에](#types-of-azure-application-plans)서 간략히 설명 된 대로 계획에 솔루션 템플릿 또는 관리 되는 응용 프로그램이 포함 될 지 여부를 선택 합니다.

#### <a name="this-plan-reuses-packages"></a>이 계획은 패키지를 재사용 합니다.

유형이 동일 하 고 패키지가 두 개 이상 있는 경우 **이 계획에서 다른 계획의 패키지**를 다시 사용할 계획을 선택할 수 있습니다.  이 옵션을 선택 하면이 제품에서 패키지를 다시 사용 하는 동일한 유형의 다른 계획 중 하나를 선택할 수 있습니다. 

>[!Note]
>다른 계획에서 패키지를 다시 사용 하는 경우이 계획에서 전체 기술 구성 탭이 사라집니다.  미래의 모든 업데이트를 포함 하 여 다른 계획의 기술 구성 세부 정보는이 계획에도 사용 됩니다. <br> <br> 또한이 계획을 게시 한 후에는이 설정을 변경할 수 없습니다.

#### <a name="cloud-availability"></a>클라우드 가용성

이 계획은 하나 이상의 클라우드에서 사용할 수 있어야 합니다. 

Marketplace 통합을 포함 하는 모든 공용 Azure 지역에서 솔루션을 고객에 게 배포할 수 있도록 **공용 azure** 옵션을 선택 합니다.  [지리적 가용성](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies)에 대 한 자세한 정보.

**Azure Government 클라우드** 옵션을 선택 하 여 미국 연방, 주, 지방, 부족 및 파트너가 이러한 엔터티를 제공할 수 있는 고객에 게 액세스를 제어 하는 정부 커뮤니티 클라우드 인 [Azure Government 클라우드에서](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome)솔루션을 배포할 수 있도록 합니다.  게시자는이 클라우드 커뮤니티를 제공 하는 규정 준수 제어, 보안 조치 및 모범 사례를 담당 합니다.  Azure Government는 실제로 격리 된 데이터 센터 및 네트워크 (미국에만 있음)를 사용 합니다.  [Azure Government](https://aka.ms/azuregovpublish)에 게시 하기 전에 특정 엔드포인트이 다를 수 있으므로 환경에서 솔루션을 테스트 하 고 유효성을 검사 하는 것이 좋습니다. 솔루션을 준비 하 고 테스트 하려면이 [링크](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청 합니다.

>[!Note]
>계획이 특정 클라우드에서 사용 가능한 것으로 게시 되 면 해당 클라우드를 제거할 수 없습니다.

**Azure Government 클라우드 인증**

이 옵션은 클라우드 **가용성**에서 **Azure Government 클라우드** 를 선택한 경우에만 표시 됩니다.

Azure Government 서비스는 FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 및 CJIS와 같은 특정 정부 규정 및 요구 사항이 적용 되는 데이터를 처리 합니다.  이러한 프로그램에 대 한 인증을 인식 하려면 인증을 설명 하는 최대 100 링크를 제공할 수 있습니다.  이러한 링크는 프로그램의 목록에 대 한 링크 이거나 사용자의 웹 사이트에서 준수에 대 한 설명에 대 한 링크 일 수 있습니다.  이러한 링크는 Azure Government 클라우드 고객만 볼 수 있습니다.

## <a name="plan-listing"></a>계획 목록

**계획 목록** 탭에는 동일한 제품에 대 한 계획 마다 다를 수 있는 계획 관련 목록 정보가 표시 됩니다.

### <a name="name"></a>이름

사용자의 이름을 기준으로 미리 채워져 있습니다. 계획을 만들 때 할당 했습니다.  이 이름은 marketplace에 표시 되는이 "소프트웨어 계획"의 제목으로 표시 됩니다.  최대 100 자까지 포함할 수 있습니다.

### <a name="summary"></a>요약

소프트웨어 계획에 대 한 간략 한 요약을 제공 합니다.  최대 100 자까지 포함할 수 있습니다.

### <a name="description"></a>Description

이 설명에서는이 소프트웨어 계획의 고유한 정의와 제품 내 다른 소프트웨어 요금제의 차이점을 설명할 수 있는 기회를 제공 합니다. 최대 2000 자까지 포함할 수 있습니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

## <a name="availability"></a>가용성

**가용성** 탭은 솔루션 템플릿 요금제에만 표시 됩니다.  계획을 모든 사용자에 게 표시 하 고, 특정 고객 (개인 대상 그룹)에만 표시 하 고, 다른 솔루션 템플릿 또는 관리 되는 응용 프로그램 에서만 사용할 수 있도록 계획을 숨길지 여부를 지정할 수 있습니다.

### <a name="plan-audience"></a>대상 계획

모든 사용자 또는 선택한 특정 대상에 게 표시 되도록 각 계획을 구성 하는 옵션이 있습니다. Azure 구독 Id를 사용 하 여이 제한 된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

개인 **정보/개인 계획** (옵션 확인란)-계획을 비공개로 설정 하 고 선택한 제한 된 대상 그룹에만 표시 하려면이 확인란을 선택 합니다. 비공개 계획으로 게시 한 후에는 대상 그룹을 업데이트 하거나 계획을 모든 사용자가 사용할 수 있도록 선택할 수 있습니다. 모든 사용자에 게 표시 되는 계획을 게시 한 후에는 모든 사용자에 게 계속 표시 되어야 합니다. (요금제를 개인 계획으로 다시 구성할 수 없습니다.)

**제한 된 대상 그룹 (Azure 구독 id)** -이 비공개 요금제에 대 한 액세스 권한을 보유 하는 대상 그룹을 할당 합니다. 할당 된 각 Azure 구독 ID에 대 한 설명을 포함 하는 옵션과 함께 Azure 구독 Id를 사용 하 여 액세스 권한을 할당 합니다. .Csv 스프레드시트 파일을 가져오는 경우 최대 10 개의 구독 Id 또는 2만 고객 구독 Id를 추가할 수 있습니다.  Azure 구독 Id는 Guid로 표시 되며 문자는 소문자 여야 합니다.

>[!Note]
>비공개 사용자 (또는 제한 된 대상)는 [**미리 보기**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) 탭에서 정의한 미리 보기 대상과 다릅니다.  제품을 marketplace에 게시 하기 *전에* 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 개인 대상 지정은 특정 계획에만 적용 되지만 미리 보기 대상은 유효성 검사를 위해 모든 요금제 (비공개 또는 not)를 볼 수 있습니다.

### <a name="hide-plan"></a>계획 숨기기

다른 솔루션 템플릿 또는 관리 되는 응용 프로그램에서 참조 하는 경우에만 솔루션 템플릿을 간접적으로 배포 하려는 경우에는이 확인란을 선택 하 여 솔루션 템플릿을 게시 하 되 고객 검색 및 직접 검색에서 숨깁니다.

## <a name="pricing-and-availability"></a>가격 책정 및 가용성

**가격 책정 및 가용성** 탭은 관리 되는 응용 프로그램 계획에만 표시 됩니다.  이 요금제를 사용할 수 있는 시장, 솔루션 관리의 월별 가격, 계획을 모든 사용자에 게 표시할지 또는 특정 고객 에게만 표시할지 여부 (개인 사용자)를 구성할 수 있습니다.

### <a name="markets"></a>시장

모든 요금제는 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 요금제를 사용할 수 있도록 하려는 시장 위치에 대 한 확인란을 선택 합니다. "세금 송금" 국가를 선택 하는 데 사용 되는 검색 상자와 단추를 사용 하 여 Microsoft 송금 판매 및 세금을 대신 사용 합니다.

요금제에 대 한 가격을 미국 달러 (USD)로 설정 하 고 다른 시장 위치를 추가 하는 경우 새 시장의 가격은 현재 환율이 따라 계산 됩니다. 게시 하기 전에 항상 각 시장의 가격을 확인 하세요. 변경 내용을 저장 한 후 "가격 (.xlsx) 내보내기" 링크를 사용 하 여 가격 책정을 검토할 수 있습니다.

### <a name="pricing"></a>가격 책정

이 요금제에 대 한 월별 가격을 제공 합니다.  이 가격은이 솔루션을 통해 배포 된 리소스에 의해 발생 하는 Azure 인프라 또는 종 량 제 소프트웨어 비용에 추가로 부과 됩니다.

현지 통화 (USD = 미국 달러)로 설정 된 가격은 설치 중 사용 가능한 현재 환율을 사용 하 여 선택한 모든 시장의 현지 통화로 변환 됩니다. 가격 책정 스프레드시트를 내보내고 각 시장에서 가격을 검토 하 여 게시 하기 전에 이러한 가격의 유효성을 검사 합니다. 개별 시장에서 사용자 지정 가격을 설정 하려면 가격 책정 스프레드시트를 수정 하 고 가져오세요. 

>[!Note]
>가격 책정 데이터를 내보낼 수 있도록 가격 책정 변경 내용을 먼저 저장 해야 합니다.

계획이 게시 된 후 변경 될 수 있는 사항에 대 한 몇 가지 제한 사항이 있으므로 게시 하기 전에 가격을 신중 하 게 검토 합니다.  

>[!Note]
>요금제의 시장 가격은 게시 된 후에는 나중에 변경할 수 없습니다.

### <a name="plan-audience"></a>대상 계획

모든 사용자 또는 선택한 특정 대상에 게 표시 되도록 각 계획을 구성 하는 옵션이 있습니다. Azure 구독 Id를 사용 하 여이 제한 된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

개인 **정보/개인 계획** (옵션 확인란)-계획을 비공개로 설정 하 고 선택한 제한 된 대상 그룹에만 표시 하려면이 확인란을 선택 합니다. 비공개 계획으로 게시 한 후에는 대상 그룹을 업데이트 하거나 계획을 모든 사용자가 사용할 수 있도록 선택할 수 있습니다. 모든 사용자에 게 표시 되는 계획을 게시 한 후에는 모든 사용자에 게 계속 표시 되어야 합니다. (요금제를 개인 계획으로 다시 구성할 수 없습니다.)

**제한 된 대상 그룹 (Azure 구독 id)** -이 비공개 요금제에 대 한 액세스 권한을 보유 하는 대상 그룹을 할당 합니다. 할당 된 각 Azure 구독 ID에 대 한 설명을 포함 하는 옵션과 함께 Azure 구독 Id를 사용 하 여 액세스 권한을 할당 합니다. .Csv 스프레드시트 파일을 가져오는 경우 최대 10 개의 구독 Id 또는 2만 고객 구독 Id를 추가할 수 있습니다.  Azure 구독 Id는 Guid로 표시 되며 문자는 소문자 여야 합니다.

>[!Note]
>비공개 사용자 (또는 제한 된 대상)는 [**미리 보기**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) 탭에서 정의한 미리 보기 대상과 다릅니다.  제품을 marketplace에 게시 하기 *전에* 미리 보기 대상 사용자가 제품에 대 한 액세스를 허용 합니다. 개인 대상 지정은 특정 계획에만 적용 되지만 미리 보기 대상은 유효성 검사를 위해 모든 요금제 (비공개 또는 not)를 볼 수 있습니다.

## <a name="technical-configuration"></a>기술 구성 

**기술 구성** 탭에서는 고객이 요금제를 배포할 수 있도록 하는 배포 패키지를 업로드할 수 있습니다.

>[!Note]
>**계획 설정** 탭의 다른 계획에서 패키지를 다시 사용 하도록이 계획을 구성 하는 경우이 탭이 표시 되지 않습니다.

### <a name="package-details"></a>패키지 세부 정보

**패키지 세부 정보** 하위 탭를 사용 하 여 기술 구성의 초안 버전을 편집할 수 있습니다.

***버전*** -기술 구성의 현재 버전을 할당 합니다.  이 페이지에 변경 내용을 게시할 때마다이 버전을 증가 시킵니다. 버전은 `{integer}.{integer}.{integer}`형식 이어야 합니다.

***패키지 파일*** (`.zip`)-이 패키지에는이 계획에 필요한 모든 템플릿 파일 뿐만 아니라 추가 리소스 (`.zip` 파일로 패키지)가 포함 되어 있습니다.

모든 Azure 응용 프로그램 계획 패키지는 `.zip` 보관의 루트 폴더에 다음 두 개의 파일을 포함 해야 합니다.

* [Maintemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)라는 리소스 관리자 템플릿 파일입니다.  이 템플릿은 고객 Azure 구독에 리소스 배포를 자동화 합니다.  리소스 관리자 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조 하세요.

* [Createuidefinition. json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)이라는 Azure 응용 프로그램 만들기 환경의 사용자 인터페이스 정의입니다.

모든 새 Azure 응용 프로그램 제품에는 [azure 파트너 고객 사용 특성](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) GUID도 포함 되어야 합니다.

### <a name="previously-published-packages"></a>이전에 게시 된 패키지 

**이전에 게시 된 패키지** 하위 탭을 사용 하면 게시 된 모든 버전의 기술 구성을 볼 수 있습니다.

## <a name="technical-configuration-managed-application-plans-only"></a>기술 구성 (관리 되는 응용 프로그램 요금제에만 해당)

관리 되는 응용 프로그램 계획은 위에 설명 된 **버전** 및 **패키지 파일** 필드를 벗어나 **기술 구성** 탭에서 추가 복잡성을 갖습니다. 

### <a name="enable-just-in-time-jit-access"></a>JIT (just-in-time) 액세스 사용

이 계획에 JIT (Just-in-time) 액세스를 사용 하도록 설정 하려면이 옵션을 선택 합니다.  JIT 액세스를 사용 하면 문제 해결 또는 유지 관리를 위해 관리 되는 응용 프로그램의 리소스에 대 한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대 한 읽기 전용 액세스 권한이 있지만 특정 기간에 대 한 액세스를 더 많이 사용할 수 있습니다.  자세한 내용은 [Enable and request just-in-time access in a Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access)를 참조 하세요.  관리 되는 응용 프로그램의 소비자가 계정에 영구 액세스 권한을 부여 하도록 하려면이 옵션을 선택 하지 않은 상태로 둡니다.

>[!Note]
>이 기능을 지원 하기 위해 `createUiDefinition.json` 파일을 업데이트 해야 합니다.  

### <a name="deployment-mode"></a>배포 모드

이 계획을 배포할 때 **전체** 또는 **증분 배포 모드** 를 구성할 지 여부를 선택 합니다. 

* **완료 모드**에서는 고객이 응용 프로그램을 다시 배포 하면 리소스가 `mainTemplate.json`에서 정의 되지 않은 경우 관리 되는 리소스 그룹의 리소스가 제거 됩니다. 
* **증분 모드**에서 응용 프로그램을 다시 배포 하면 기존 리소스는 변경 되지 않습니다.

배포 모드에 대해 자세히 알아보려면 [Azure Resource Manager 배포 모드](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes)를 참조 하세요.

### <a name="notification-endpoint-url"></a>알림 엔드포인트 URL

이 계획 버전의 관리 되는 응용 프로그램 인스턴스에 대 한 모든 CRUD 작업에 대 한 알림을 받을 HTTPS Webhook 엔드포인트을 지정 합니다.

### <a name="customize-allowed-customer-actions"></a>허용 된 고객 작업 사용자 지정

사용자가 관리 되는 리소스에 대해 수행할 수 있는 작업 및 기본적으로 사용할 수 있는 "`*/read`" 작업을 지정 하려면이 옵션을 선택 합니다. 

고객이 여기에서 수행할 수 있도록 하는 추가 작업을 세미콜론으로 구분 하 여 나열 합니다.  자세한 내용은 [Azure 리소스에 대 한 거부 할당 이해](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments)를 참조 하세요.  사용 가능한 작업에 대해서는 [Azure Resource Manager 리소스 공급자 작업](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations)을 참조하세요. 예를 들어 소비자가 가상 머신을 다시 시작하도록 허용하려면 허용되는 작업에 `Microsoft.Compute/virtualMachines/restart/action`을 추가합니다.

### <a name="global-azure--azure-government-cloud"></a>글로벌 Azure/Azure Government 클라우드

지원 되는 각 클라우드에서 관리 되는 응용 프로그램에 대 한 관리 액세스 권한이 있는 사용자를 표시 합니다.  관리 되는 리소스 그룹에 대 한 사용 권한을 부여 하려는 사용자, 그룹 또는 응용 프로그램은 AAD (Azure Active Directory) id를 사용 하 여 식별 됩니다.

***Azure Active Directory 테넌트 id*** -사용 권한을 부여 하려는 사용자, 그룹 또는 응용 프로그램의 id가 포함 된 AAD 테넌트 id (디렉터리 ID 라고도 함)입니다.  Azure Portal의 AAD 테넌트 ID는 [Azure Active Directory에 대 한 속성](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)에서 찾을 수 있습니다.

***권한 부여-관리*** 되는 리소스 그룹에 권한을 부여 하려는 사용자, 그룹 또는 응용 프로그램의 AZURE ACTIVE DIRECTORY 개체 ID를 추가 합니다. 사용자를 보안 주체 ID로 식별 합니다 .이 ID는 Azure Portal의 [사용자 Azure Active Directory 블레이드에서](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)찾을 수 있습니다.

각 보안 주체에 대해 목록 (소유자 또는 참가자)에서 Azure AD 기본 제공 역할 중 하나를 선택 합니다. 선택한 역할은 사용자가 고객 구독의 리소스에 대해 가질 수 있는 권한을 설명 합니다. 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.  RBAC (역할 기반 액세스 제어)에 대 한 자세한 내용은 [AZURE PORTAL rbac 시작](https://docs.microsoft.com/azure/role-based-access-control/overview)을 참조 하세요.

>[!Note]
>클라우드 당 최대 100 권한 부여를 추가할 수 있지만 일반적으로 Active Directory 사용자 그룹을 만들고 "보안 주체 ID"에 해당 ID를 지정 하는 것이 더 쉽습니다.  이렇게 하면 계획이 배포 된 후 관리 그룹에 더 많은 사용자를 추가할 수 있으며, 더 많은 권한 부여를 추가 하기 위해 계획을 업데이트할 필요성이 줄어듭니다.

### <a name="policy-settings"></a>정책 설정

[Azure 정책을](https://docs.microsoft.com/azure/governance/policy/overview) 관리 되는 응용 프로그램에 적용 하 여 배포 된 솔루션에 대 한 규정 준수 요구 사항을 지정 합니다.  매개 변수 값의 형식 및 정책 정의는 [Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/index)을 참조하세요.  최대 5 개의 정책과 각 정책 옵션의 인스턴스를 하나만 구성할 수 있습니다.  일부 정책에는 추가 매개 변수가 필요 합니다.  표준 SKU는 감사 정책에 필요합니다.  정책 이름은 50 자로 제한 됩니다.

## <a name="co-sell"></a>공동 판매

Cosell 탭에 대 한 정보를 제공 하는 것은 제품을 게시 하는 데 모두 선택 사항입니다. 공동 판매 준비 및 IP 공동 판매 준비 상태를 구현 해야 합니다. 사용자가 제공 하는 정보는 Microsoft 영업 팀에서 고객 요구 사항에 대 한 적합을 평가할 때 솔루션에 대 한 자세한 정보를 제공 하는 데 사용 됩니다. 고객에 게 직접 제공 되지 않습니다.

이 탭을 완료 하는 방법에 대 한 자세한 내용은 [파트너 센터의 공동 판매 옵션](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell)을 참조 하세요.

## <a name="test-drive"></a>시험 사용

**시험** 사용 탭에서는 고객이 제품을 구입 하기 전에 제품을 사용해 볼 수 있도록 데모 (또는 "시험 사용")를 설정할 수 있습니다. [테스트 드라이브인 이란](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) 문서에서 자세히 알아보세요.  제품에 대 한 시험 사용을 더 이상 제공 하지 않으려는 경우 **제품 설치** 페이지로 돌아가 **테스트 드라이브 사용**을 선택 취소 합니다.

### <a name="technical-configuration"></a>기술 구성

Azure 응용 프로그램은 기본적으로 Azure Resource Manager 테스트 드라이브 유형을 사용 합니다.  자세한 내용은 [Azure Resource Manager 테스트 드라이브의 기술 구성](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) 을 참조 하세요.

### <a name="deployment-subscription-details"></a>배포 구독 정보

사용자를 대신 하 여 테스트 드라이브를 배포 하기 위해 별도의 고유한 Azure 구독을 만들어 제공 하세요. (Power BI 테스트 드라이브에는 필요 하지 않음)

- **Azure 구독 id** (Azure Resource Manager 및 논리 앱에 필요): 리소스 사용량 보고 및 청구를 위해 azure 계정 서비스에 대 한 액세스 권한을 부여 하는 구독 Id를 입력 합니다. 아직 없는 경우 테스트 드라이브에 사용할 [별도의 Azure 구독을 만드는](https://docs.microsoft.com/azure/billing/billing-create-subscription) 것이 좋습니다. [Azure Portal](https://portal.azure.com/) 에 로그인 하 고 왼쪽 메뉴의 **구독** 탭으로 이동 하 여 Azure 구독 ID를 찾을 수 있습니다. 탭을 선택 하면 구독 ID (예: "a83645ac-1234-5ab6-6789-1h234g764

- **AZURE ad 테넌트 id** (필수): AZURE ACTIVE DIRECTORY (AD) [테넌트 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 * * 속성을 선택 하 고 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용 하 여 조직의 테넌트 ID를 조회할 수도 있습니다.

- **AZURE ad 테넌트 이름** (동적 365에 필요): AZURE ACTIVE DIRECTORY (AD) 이름을 입력 합니다. 이 이름을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고, 오른쪽 위 모서리에 있는 테넌트 이름이 계정 이름 아래에 나열 됩니다.

- **AZURE ad 앱 id** (필수): AZURE ACTIVE DIRECTORY (AD) [응용 프로그램 id](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 **앱 등록**를 선택 하 고 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.

- **AZURE ad 앱 클라이언트 암호** (필수): azure ad 응용 프로그램 [클라이언트 암호](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)를 입력 합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 합니다. 왼쪽 메뉴에서 **Azure Active Directory** 탭을 선택 하 고 **앱 등록**을 선택한 다음 테스트 드라이브 앱을 선택 합니다. 그런 다음 **인증서 및 비밀**을 선택 하 고, **새 클라이언트 암호**를 선택 하 고, 설명을 입력 하 고, **만료**됨 **을 선택 하** 고, **추가**를 선택 합니다. 값을 복사 해야 합니다. 값을 복사 하기 전에 페이지에서 이동 하지 마세요. 그렇지 않으면 값에 액세스할 수 없습니다.

다음 섹션으로 이동 하기 전에 **저장** 해야 합니다.

### <a name="test-drive-listings-optional"></a>드라이브 목록 테스트 (선택 사항)

**테스트 드라이브 탭 아래** 에 있는 **테스트 드라이브 목록** 옵션은 테스트 드라이브를 사용할 수 있는 언어 (및 시장)를 표시 하며 현재 영어 (미국)만 사용할 수 있는 위치입니다. 또한이 페이지에는 언어별 목록과 추가 된 날짜/시간의 상태가 표시 됩니다. 각 언어/시장에 대해 테스트 드라이브 세부 정보 (설명, 사용자 설명서, 비디오 등)를 정의 해야 합니다.

- **설명** (필수): 시험 사용에 대 한 설명, 표시 될 내용, 사용자가 시험해 볼 기능, 탐색할 기능 및 사용자가 제품을 확보할 지 여부를 결정 하는 데 도움이 되는 모든 관련 정보를 설명 합니다. 이 필드에는 최대 3000 자의 텍스트를 입력할 수 있습니다. 

- **액세스 정보** (Azure Resource Manager 및 논리 시험 사용에 필요):이 테스트 드라이브에 액세스 하 고 사용 하기 위해 고객이 알아야 할 사항에 대해 설명 합니다. 제품 사용에 대 한 시나리오와 고객이 테스트 드라이브를 통해 기능에 액세스 하기 위해 알아야 하는 사항을 정확히 살펴봅니다. 이 필드에는 최대 1만 자의 텍스트를 입력할 수 있습니다.

- **사용자 설명서** (필수): 테스트 드라이브 환경을 자세히 연습 합니다. 사용자 설명서는 시험 사용에 대 한 고객의 의견을 정확 하 게 파악 하 고 있을 수 있는 질문에 대 한 참조로 사용할 수 있습니다. 업로드 후 파일은 PDF 형식 이어야 하며 (최대 255 자) 이름이 지정 되어야 합니다.

- **비디오: 비디오 추가** (선택 사항): 비디오를 YouTube 또는 Vimeo에 업로드 하 고 링크 및 미리 보기 이미지 (533 x 324 픽셀)를 사용 하 여 여기에서 참조할 수 있으므로 고객이 제품의 기능을 성공적으로 사용 하는 방법을 비롯 하 여 테스트 드라이브를 보다 잘 이해 하는 데 도움이 되는 정보를 볼 수 있습니다.
  - **이름** (필수)
  - **URL (YouTube 또는 Vimeo만 해당)** (필수)
  - **축소판 그림 (533 x 324 px)** : 이미지 파일은 PNG 형식 이어야 합니다.

이러한 필드를 완료 한 후 **저장** 을 선택 합니다.

## <a name="publish"></a>게시

### <a name="submit-offer-to-preview"></a>미리 보기로 제품 제출

제품의 필수 섹션을 모두 완료 한 후에는 포털의 오른쪽 위 모서리에서 **게시** 를 선택 합니다. **검토 및 게시** 페이지로 리디렉션됩니다. 

이 제품을 처음 게시 하는 경우 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대 한 완료 상태를 확인 합니다.
    - *시작 되지 않음* -섹션이 작업 되지 않아 완료 해야 함을 의미 합니다.
    - *불완전* -섹션에 수정 해야 하거나 더 많은 정보를 제공 해야 하는 오류가 있음을 의미 합니다. 섹션으로 돌아가서 업데이트 하세요.
    - *완료* -섹션이 완료 되었고 필요한 모든 데이터가 제공 되었으며 오류가 없음을 의미 합니다. 제품의 모든 섹션은 제품을 제출 하기 전에 완전 한 상태 여야 합니다.
- 앱을 이해 하는 데 도움이 되는 보조 노트 뿐만 아니라 앱이 올바르게 테스트 되었는지 확인 하기 위해 인증 팀에 테스트 지침을 제공 합니다.
- **제출**을 선택 하 여 게시에 대 한 제품을 제출 합니다. 제품의 미리 보기 버전을 검토 하 고 승인할 수 있는 시기를 알려 주는 전자 메일을 보내 드리겠습니다. 파트너 센터로 돌아가서 제품을 공개 (또는 개인 제품의 경우 비공개 사용자에 게 공개)에 게시 하기 위해 **live live** 를 선택 합니다.

### <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

게시 프로세스의 **수동 유효성 검사** 단계는 제품 및 관련 기술 자산 (특히 Azure Resource Manager 템플릿)의 광범위 한 검토를 나타내며, 일반적으로 문제는 PR (끌어오기 요청) 링크로 제공 됩니다. 이러한 PR을 보고 응답하는 방법에 대한 설명은 [검토 피드백 처리](./azure-apps-review-feedback.md)를 참조하세요.

하나 이상의 게시 단계에서 오류가 발생하면 해당 오류를 수정하고 제안을 다시 게시해야 합니다.

## <a name="next-steps"></a>다음 단계

- [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
