---
title: Azure 애플리케이션 제품 만들기 - Microsoft 상업용 Marketplace
description: 파트너 센터의 상업용 Marketplace 포털에서 새 Azure 애플리케이션 제품을 만들기 위한 단계 및 고려 사항을 알아봅니다. Azure Marketplace 또는 CSP(클라우드 솔루션 공급자) 프로그램을 통해 Azure 애플리케이션 제품을 나열하거나 판매할 수 있습니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: 071d3fc972e99d61a226492350b7eee146584df6
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815671"
---
# <a name="create-an-azure-application-offer"></a>Azure 애플리케이션 제품 만들기

이 문서에서는 상업용 Marketplace에서 새 Azure 애플리케이션 제품을 만들기 위한 단계 및 고려 사항을 설명합니다. 새 Azure 애플리케이션 제품을 만들기 전에 관련 개념을 잘 알고 있어야 합니다.

새 Azure 애플리케이션 제품을 게시하기 전에 [파트너 센터에서 상업용 Marketplace 계정을 만들고](create-account.md) 계정이 상업용 Marketplace 프로그램에 등록되어 있는지 확인합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure 애플리케이션 제품을 디자인, 빌드 및 테스트하려면 Azure 플랫폼 및 제품을 빌드하는 데 사용되는 기술에 관한 기술적 지식이 둘 다 필요합니다. 엔지니어링 팀에는 다음 Microsoft 기술에 대한 지식이 있어야 합니다.

* [Azure 서비스](https://azure.microsoft.com/services/)에 관한 기본적 이해
* [Azure 애플리케이션을 디자인 및 설계](https://azure.microsoft.com/solutions/architecture/)하는 방법
* [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)에 관한 실무 지식
* [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)에 관한 실무 지식
* [JSON](https://www.json.org/)에 관한 실무 지식

### <a name="technical-documentation-and-resources"></a>기술 문서 및 리소스

상업용 Marketplace용으로 Azure 애플리케이션 제품을 준비할 때 다음 리소스를 검토합니다.

* [Azure Resource Manager 템플릿 이해](../../azure-resource-manager/resource-group-authoring-templates.md)

* 빠른 시작:

    * [Azure Quickstart 템플릿](https://azure.microsoft.com/documentation/templates/)
    * [Azure 템플릿 모범 사례 가이드](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [애플리케이션 정의 게시](../../managed-applications/publish-service-catalog-app.md)
    * [서비스 카탈로그 앱 배포](../../managed-applications/deploy-service-catalog-quickstart.md)

* 자습서:

    * [정의 파일 만들기](../../managed-applications/publish-service-catalog-app.md)
    * [마켓플레이스 애플리케이션 게시](../../managed-applications/publish-marketplace-app.md)

* 샘플:

    * [Azure CLI](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [관리되는 애플리케이션 솔루션](../../managed-applications/sample-projects.md)

[Azure Marketplace용 관리형 애플리케이션 및 솔루션 템플릿 빌드](https://channel9.msdn.com/Events/Build/2018/BRK3603) 동영상에서는 Azure 애플리케이션 제품 유형을 포괄적으로 소개합니다.

* 제공 되는 제품 유형
* 필요한 기술 자산
* Azure Resource Manager 템플릿을 제작 하는 방법
* 앱 UI 개발 및 테스트
* 앱 제공을 게시 하는 방법
* 응용 프로그램 검토 프로세스

### <a name="suggested-tools"></a>권장되는 도구

Azure 애플리케이션을 관리하는 데 도움이 되는 다음 스크립팅 환경 중 하나 또는 둘 다를 선택합니다.

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

개발 환경에 다음 도구를 추가하는 것이 좋습니다.

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)(다음 확장 포함):
    * 확장: [Azure Resource Manager 도구](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * 확장: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * 확장: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

[Azure 개발자 도구](https://azure.microsoft.com/tools/) 페이지에서 사용 가능한 도구를 확인할 수 있습니다. 또한 Visual Studio를 사용하는 경우 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)에서 검토할 수 있습니다.

## <a name="types-of-azure-application-plans"></a>Azure 애플리케이션 플랜 유형

Azure 애플리케이션 플랜에는 관리형 애플리케이션 및 솔루션 템플릿의 두 가지 종류가 있습니다.

* **솔루션 템플릿**은 Marketplace에서 솔루션을 게시하는 주요 방법 중 하나입니다. 솔루션에 단일 VM(가상 머신) 이외의 추가 배포 및 구성 자동화가 필요한 경우 이 플랜 유형을 사용합니다. 솔루션 템플릿을 사용하면 복잡한 IaaS 솔루션을 제공하기 위한 VM, 네트워킹 및 스토리지 리소스를 포함하여 둘 이상의 리소스를 제공하는 작업을 자동화할 수 있습니다.  솔루션 템플릿을 빌드하는 방법에 관한 자세한 내용은 [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)를 참조하세요.

* **관리형 애플리케이션**은 솔루션 템플릿과 비슷하지만 하나의 주요 차이점이 있습니다. 관리되는 애플리케이션에서 리소스는 앱 게시자가 관리하는 리소스 그룹에 배포됩니다. 리소스 그룹은 소비자의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. 게시자는 지속적인 솔루션 지원을 위한 비용을 지정합니다. 관리형 애플리케이션을 사용하여 완전 관리형 턴키 애플리케이션을 손쉽게 빌드하고 고객에게 제공합니다.  관리되는 애플리케이션의 장점 및 유형에 대한 자세한 내용은 [Azure Managed Applications 개요](../../managed-applications/overview.md)를 참조하세요.

## <a name="technical-requirements"></a>기술적인 요구 사항

모든 Azure 애플리케이션에는 `.zip` 보관 파일의 루트 폴더에 두 개 이상의 파일이 포함됩니다.

* [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md)이라는 Resource Manager 템플릿 파일.  이 템플릿은 고객의 Azure 구독에 배포할 리소스를 정의합니다. Resource Manager 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조하세요.

* [createUiDefinition.json](../../managed-applications/create-uidefinition-overview.md)이라는 Azure 애플리케이션 만들기 환경의 사용자 인터페이스 정의.  사용자 인터페이스에서 소비자가 매개 변수 값을 제공할 수 있도록 하는 요소를 지정합니다.

모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여 GUID](../azure-partner-customer-usage-attribution.md)를 포함해야 합니다. 

각 애플리케이션 플랜의 게시 요구 사항을 알아보려면 [솔루션 템플릿 제품 게시 요구 사항](../marketplace-solution-templates.md) 및 [관리형 애플리케이션 제품 게시 요구 사항](../marketplace-managed-apps.md)을 참조하세요.

## <a name="create-a-new-offer"></a>새 제안 만들기

>[!NOTE]
>제품을 게시 한 후에는 제품을 다시 게시할 때까지 파트너 센터에서 해당 제품에 대 한 편집 내용이 온라인 매장에 표시 되지 않습니다. 변경한 후에는 항상 제품을 다시 게시해야 합니다.

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.

1. 왼쪽 메뉴에서 **상업용 Marketplace** > **개요**를 선택합니다.

1. 개요 페이지에서 **+ 새 제품** > **Azure 애플리케이션**을 선택합니다.

    ![왼쪽 탐색 메뉴를 보여 줍니다.](./media/new-offer-azure-app.png)

1. **새 제품** 페이지에서 **제품 ID**를 입력합니다. 계정에 있는 각 제품의 고유 식별자입니다.

     * 이 ID는 마켓플레이스 제품 및 Azure Resource Manager 템플릿의 웹 주소에서 고객에게 표시됩니다(해당하는 경우).
     * 소문자와 숫자만 사용할 수 있습니다. 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
     * **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

1. **제품 별칭**을 입력합니다. 파트너 센터에서 제품에 사용되는 이름입니다.

     * 이 이름은 마켓플레이스에서 사용되지 않으며 고객에게 표시되는 제품 이름 및 기타 값과 다릅니다.
     * **만들기**를 선택한 후에는 제품 별칭을 변경할 수 없습니다.

1. **만들기**를 선택하여 제품을 생성하고 계속합니다.

## <a name="offer-setup"></a>제품 설정

**제품 설정** 페이지에서는 제품의 시험 사용 및 잠재 고객 관리를 구성할 수 있습니다.

### <a name="test-drive"></a>시험 사용

시험 사용은 “구입 전 체험” 옵션을 제공하여 잠재적인 고객에게 제품을 선보임으로써 구매 전환율을 높이고 적절한 잠재 고객을 창출할 수 있는 유용한 방법입니다. [시험 사용에 관해 자세히 알아보세요](../what-is-test-drive.md).

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란 선택을 취소합니다. 이 항목 뒤에 나오는 [시험 사용 기술 구성](#test-drive-technical-configuration) 섹션에서 시험 사용 환경을 구성합니다.

자세한 내용은 [상업용 Marketplace에서 제품 시험 사용](test-drive.md)을 참조하세요. [시험 사용 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)를 읽어보고 [시험 사용 개요 PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)를 다운로드할 수도 있습니다(팝업 차단기가 꺼져 있는지 확인).

>[!Note]
>모든 Azure 애플리케이션은 Azure Resource Manager 템플릿을 사용하여 구현되므로 Azure 애플리케이션에 사용할 수 있는 유일한 시험 사용 유형은 [Azure Resource Manager 기반 시험 사용](../azure-resource-manager-test-drive.md)입니다.

### <a name="customer-leads"></a>잠재 고객

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

자세한 내용은 [잠재 고객 관리 개요](./commercial-marketplace-get-customer-leads.md)를 참조하세요.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

**속성** 페이지에서는 marketplace에서 제품을 그룹화 하는 데 사용 되는 범주, 앱 버전 및 제품을 지 원하는 법적 계약을 정의 합니다.

### <a name="category"></a>범주

범주 및 하위 범주를 선택 하 여 제품을 적절 한 마켓플레이스 검색 영역에 넣습니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음**을 선택 합니다.

[제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주 및 하위 범주의 전체 목록을 참조 하세요.

### <a name="legal"></a>법적 정보

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>제품 목록

이 페이지에서는 상업용 Marketplace 제품의 복사본 및 이미지를 관리합니다.

### <a name="marketplace-details"></a>마켓플레이스 세부 정보

> [!NOTE]
> 제품 설명이 “[영어가 아닌 언어]에서만 사용할 수 있습니다.”로 시작하는 경우 제품 목록 콘텐츠(예: 설명, 문서, 스크린샷, 사용 약관)는 영어로 작성되지 않아도 됩니다. 또한 제품 목록 콘텐츠에 사용되는 것 이외의 언어로 콘텐츠를 제공하는 *유용한 링크 URL*을 제공할 수 있습니다.

다음은 Azure Marketplace에서 제공 정보를 표시 하는 방법의 예입니다. 나열 된 가격은 예를 들기 위한 것 이며 실제 비용을 반영 하지는 않습니다.

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="이 제품이 Azure Marketplace 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 로고
2. 범주
3. 지원 주소 (링크)
4. 사용 약관
5. 개인 정보 취급 방침 주소 (링크)
6. Offer name
7. 요약
8. Description
9. 스크린샷/비디오

<br>다음은 Azure Portal에서 제공 정보를 표시 하는 방법의 예입니다.

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="이 제품이 Azure Portal 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 제목
2. Description
3. 유용한 링크
4. 스크린샷

#### <a name="name"></a>속성

여기에 입력하는 이름은 고객에게 제품 목록의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭**에 입력한 텍스트로 미리 채워져 있지만 이 값을 변경할 수 있습니다. 이 이름은 상표로 등록될 수 있으며 상표 또는 저작권 기호를 포함할 수 있습니다. 이름은 50자를 초과할 수 없으며 이모지는 포함할 수 없습니다.

#### <a name="search-results-summary"></a>검색 결과 요약

최대 100자의 제품에 관한 간단한 설명을 제공합니다. 이 설명은 검색 결과에 사용될 수 있습니다.

#### <a name="long-summary"></a>긴 요약

최대 256자의 제품에 관한 더 긴 설명을 제공합니다. 이 설명은 검색 결과에 사용될 수 있습니다.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>검색 키워드

필요에 따라 고객이 Marketplace에서 제품을 찾을 수 있도록 최대 3개의 검색 키워드를 입력할 수 있습니다. 최상의 결과를 얻기 위해 설명에 관련 키워드도 사용합니다.

#### <a name="privacy-policy-link"></a>개인정보처리방침 링크

조직의 개인정보처리방침 URL을 입력합니다. 앱이 개인 정보 보호 법률 및 규정을 준수하는지 확인하고 유효한 개인정보처리방침을 제공하는 것은 귀하의 책임입니다.

### <a name="useful-links"></a>유용한 링크

**+ 링크 추가**를 선택하여 솔루션에 관한 선택적 보조 온라인 문서의 링크를 추가합니다.

### <a name="contact-information"></a>연락처 정보

**지원 연락처**, **엔지니어링 연락처** 및 **CSP 프로그램 연락처**의 이름, 메일 및 전화번호를 제공합니다. 이 정보는 고객에게 표시되지 않지만 Microsoft에 제공되며 CSP 파트너에게 제공될 수 있습니다. 일부 연락처에는 추가 정보가 필요할 수 있습니다.

### <a name="marketplace-media"></a>마켓플레이스 미디어

제품에 사용할 로고 및 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 이미지가 흐리면 제출이 거부됩니다.

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

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 최대 5개의 스크린샷을 추가합니다. 각 스크린샷은 PNG 형식의 1280 x 720 픽셀 크기여야 합니다. 각 스크린샷에는 캡션이 포함되어야 합니다.

#### <a name="videos"></a>동영상

제품을 설명하는 최대 5개의 동영상을 추가합니다. 동영상은 외부 동영상 서비스에서 호스트해야 합니다. 각 동영상의 이름, 웹 주소 및 동영상의 썸네일 PNG 이미지(1280 x 720 픽셀)를 입력합니다.

#### <a name="additional-marketplace-listing-resources"></a>추가 마켓플레이스 목록 리소스

- [Marketplace 제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="preview-audience"></a>미리 보기 대상 그룹

미리 보기 탭에서 제품을 광범위한 마켓플레이스 대상 그룹에 라이브로 게시하기 전에 유효성을 검사하기 위해 제한된 **미리 보기 대상 그룹**을 선택합니다.

> [!IMPORTANT]
> 미리 보기에서 제품을 확인한 후 **라이브 상태로 전환**을 선택하여 제품을 상업용 Marketplace 대상 그룹에 게시합니다.

미리 보기 대상 그룹은 Azure 구독 ID GUID 및 각각의 선택적 설명을 통해 식별됩니다. 해당 필드는 고객에게 표시되지 않습니다. Azure 구독 ID는 Azure Portal의 **구독** 페이지에서 찾을 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로 추가하거나(최대 10개), CSV 파일을 업로드하여 추가합니다(최대 100개). 해당 구독 ID를 추가하여 제품이 라이브로 게시되기 전에 제품을 미리 볼 수 있는 사용자를 정의합니다. 제품이 이미 라이브 상태인 경우 제품의 변경 내용 또는 업데이트를 테스트하기 위한 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 제품이 마켓플레이스에 라이브로 게시되기 ‘전에’ 해당 제품에 액세스할 수 있습니다. 제품이 마켓플레이스에 완전히 게시된 후에 미리 보기 대상 그룹에서 모든 플랜(프라이빗 대상 그룹만 사용할 수 있는 플랜 포함)을 보고 유효성을 검사할 수 있습니다. 프라이빗 대상 그룹(**가격 책정 및 가용성** 플랜 탭에서 정의됨)은 특정 플랜에 단독으로 액세스할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="technical-configuration"></a>기술 구성

제품에 Marketplace 계량 서비스 API를 사용하여 계량 이벤트를 내보내는 관리형 애플리케이션이 포함된 경우에만 이 섹션을 완료합니다. **Azure Active Directory 테넌트 ID** 및 서비스에서 계량 이벤트를 내보낼 때 사용할 **Azure Active Directory 애플리케이션 ID**를 입력합니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="technical-configuration-offer-level"></a>기술 구성(제품 수준)

>[!Note]
>제품 수준 기술 세부 정보는 선택 사항입니다.  해당 세부 정보는 계량형 청구를 사용하여 관리형 애플리케이션을 게시하고 Azure AD 보안 토큰을 사용하여 인증할 서비스가 있는 경우에만 구성해야 합니다. 자세한 내용은 다양한 인증 옵션에 관한 [인증 전략](./marketplace-metering-service-authentication.md)을 참조하세요.

기술 구성은 [Marketplace 계량 서비스 API](./marketplace-metering-service-apis.md)를 사용하여 관리형 애플리케이션의 계량 이벤트를 내보내는 서비스를 식별하는 데 사용되는 세부 정보(테넌트 ID 및 앱 ID)를 정의합니다.  계량 이벤트를 내보낼 때 서비스에서 사용할 ID를 입력합니다.

* **AZURE ad 테 넌 트 ID** (필수): Azure Portal 내에서 두 서비스 간의 연결이 인증 된 통신 뒤에 있는지 확인할 수 있도록 [AD (Azure Active Directory) 앱을 만들어야](../../active-directory/develop/howto-create-service-principal-portal.md) 합니다. [테 넌 트 ID](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 찾으려면 Azure Active Directory로 이동 하 고 **속성**을 선택한 다음 나열 된 **디렉터리 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 확인 합니다.
* **AZURE AD 앱 id** (필수): [응용 프로그램 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)와 인증 키도 필요 합니다. 해당 값을 얻으려면 Azure Active Directory로 이동하고, **앱 등록**을 선택한 다음, 나열된 **애플리케이션 ID** 번호(예: 50c464d3-4930-494c-963c-1e951d15360e)를 검색합니다. 인증 키를 찾으려면 **설정**으로 이동하고 **키**를 선택합니다. 설명 및 기간을 입력해야 숫자 값이 제공됩니다.

>[!Note]
>Azure 애플리케이션 ID는 게시자 ID와 연결되며 이 게시자 계정 내에서만 다시 사용할 수 있습니다.

>[!Note]
>이 구성은 [Batch 사용 이벤트](marketplace-metering-service-apis.md#metered-billing-batch-usage-event)를 사용 하려는 경우에 필요 합니다.  [사용 이벤트](marketplace-metering-service-apis.md#metered-billing-single-usage-event)를 제출 하려는 경우 [인스턴스 메타 데이터 서비스](../../active-directory/managed-identities-azure-resources/overview.md) 를 사용 하 여 [JWT (JSON web token) 전달자 토큰](pc-saas-registration.md#how-to-get-the-publishers-authorization-token)을 가져올 수도 있습니다.

## <a name="plan-overview"></a>플랜 개요

이 탭에서 동일한 제품 내에서 여러 가지 플랜 옵션을 제공할 수 있습니다. 이러한 요금제 (이전의 Sku)는 계획 유형 (솔루션 템플릿 및 관리 되는 응용 프로그램), 수익 화 또는 대상의 측면에서 다를 수 있습니다. 마켓플레이스에 제품을 나열하기 위해 하나 이상의 플랜을 구성합니다.

각 제품에 대해 최대 100 요금제를 만들 수 있습니다. 최대 45 개는 개인 일 수 있습니다. [Microsoft 상업적 marketplace에서 비공개 제품](../private-offers.md)의 비공개 요금제에 대해 자세히 알아보세요.

플랜이 만들어지면 이 탭에 플랜 이름, ID, 플랜 유형, 가용성(퍼블릭 또는 프라이빗), 현재 게시 상태 및 사용 가능한 작업이 표시됩니다.

**플랜 개요**에서 사용할 수 있는 **작업**은 플랜의 현재 상태에 따라 달라지며 다음이 포함될 수 있습니다.

* 플랜 상태가 **초안**인 경우 - 초안을 삭제합니다.
* 플랜 상태가 **라이브**인 경우 - 플랜 판매를 중지하거나 프라이빗 대상 그룹을 동기화합니다.

### <a name="create-new-plan"></a>새 플랜 만들기

***플랜 ID*** – 이 제품에서 각 플랜의 고유한 플랜 ID를 만듭니다. 이 ID는 고객에게 제품 URL로 표시됩니다.  소문자 영숫자, 대시 또는 밑줄만 사용합니다. 이 플랜 ID에는 최대 50자를 사용할 수 있습니다. 만들기를 선택한 후에는 이 ID를 수정할 수 없습니다.

***계획 이름*** – 제품 내에서 선택할 계획을 결정할 때 고객에 게이 이름이 표시 됩니다. 이 제품의 각 플랜에 고유한 제품 이름을 만듭니다. 플랜 이름은 동일한 제품에 포함될 수 있는 소프트웨어 플랜을 구분하는 데 사용됩니다(예: 제품 이름: Windows Server, 플랜: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>플랜 설정

이 탭에서는 플랜 유형의 개략적인 구성, 플랜이 다른 플랜의 패키지를 다시 사용하는지 여부 및 플랜을 사용할 수 있는 클라우드를 설정할 수 있습니다. 이 탭에 있는 응답은 동일한 플랜에 대해 다른 탭에 표시되는 필드에 영향을 줍니다.

#### <a name="plan-type"></a>플랜 유형
제품의 플랜 유형을 선택합니다. **솔루션 템플릿** 플랜은 고객이 완전히 관리합니다. **관리형 애플리케이션** 플랜을 사용하면 게시자가 고객 대신 애플리케이션을 관리할 수 있습니다. 자세한 내용은 [Azure 애플리케이션 플랜 유형](#types-of-azure-application-plans)을 참조하세요.

#### <a name="re-use-technical-configuration"></a>기술 구성 다시 사용

동일한 유형의 플랜이 둘 이상 있고 해당 플랜의 패키지가 동일한 경우 **이 플랜은 다른 플랜의 패키지를 다시 사용합니다.** 를 선택할 수 있습니다.  이 옵션을 선택하면 이 제품에서 패키지를 다시 사용할 동일한 유형의 다른 플랜 중 하나를 선택할 수 있습니다.

>[!Note]
>다른 플랜의 패키지를 다시 사용하면 전체 기술 구성 탭이 해당 플랜에 표시되지 않습니다. 향후 적용할 업데이트를 포함하여 다른 플랜의 기술 구성 세부 정보가 해당 플랜에도 사용됩니다.<br><br>해당 플랜이 게시된 후에는 이 설정을 변경할 수 없습니다.

#### <a name="azure-regions-cloud"></a>Azure 지역(클라우드)

플랜은 하나 이상의 Azure 지역에서 사용할 수 있어야 합니다.

**Azure 글로벌** 옵션을 선택하여 상업용 Marketplace 통합이 있는 모든 Azure 글로벌 지역의 고객이 플랜을 사용할 수 있도록 합니다. 자세한 내용은 [지리적 가용성 및 통화 지원](../marketplace-geo-availability-currencies.md)을 참조하세요.

**Azure Government** 옵션을 선택하여 [Azure Government](../../azure-government/documentation-government-welcome.md) 지역에서 플랜을 사용할 수 있도록 합니다. 해당 지역은 미국 연방, 주, 지방 또는 부족 단체의 고객뿐만 아니라 관련 고객에게 서비스를 제공할 수 있는 파트너에게도 제어된 액세스를 제공합니다. 게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리되어 미국에만 있는 데이터 센터와 네트워크를 사용합니다.

[Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)에 게시하기 전에 특정 엔드포인트가 다를 수 있으므로 환경에서 플랜을 테스트하고 유효성을 검사합니다. 플랜을 설정하고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/)에서 평가판 계정을 요청합니다.

>[!NOTE]
>플랜이 게시되고 특정 Azure 지역에서 사용할 수 있으면 해당 지역을 제거할 수 없습니다.

#### <a name="azure-government-certifications"></a>Azure Government 인증

이 옵션은 **Azure Government**를 선택한 경우에만 표시됩니다.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 관한 설명에 연결되는 링크일 수 있습니다. 이 링크는 Azure Government 고객에게만 표시됩니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="plan-listing"></a>플랜 목록

여기에서 플랜의 목록 세부 정보를 구성합니다. 이 탭에는 동일한 제품의 플랜 간에 다를 수 있는 특정 정보가 표시됩니다.

#### <a name="plan-name"></a>플랜 이름

플랜을 만들 때 지정한 이름으로 미리 채워집니다. 이 이름은 마켓플레이스에 이 플랜의 제목으로 표시되며 100자로 제한됩니다.

#### <a name="plan-summary"></a>플랜 요약

제품이 아니라 플랜의 간단한 요약을 제공합니다. 이 요약은 100자로 제한됩니다.

#### <a name="plan-description"></a>플랜 설명

이 소프트웨어 플랜을 고유하게 만드는 방법과 제품 내 플랜 간의 차이점을 설명합니다. 제품이 아닌 플랜을 설명해야 합니다. 플랜 설명은 최대 2,000자를 포함할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="availability-solution-template-plans-only"></a>가용성(솔루션 템플릿 플랜에만 해당)

플랜을 모든 사용자에게 또는 특정 고객(프라이빗 대상 그룹)에게만 표시하거나, 다른 솔루션 템플릿 또는 관리형 애플리케이션에서만 사용하도록 플랜을 숨길지 여부를 지정할 수 있습니다.

#### <a name="plan-visibility"></a>플랜 표시 여부

모든 사용자 또는 선택한 특정 대상 그룹에만 표시되도록 각 플랜을 구성할 수 있습니다. Azure 구독 ID를 사용하여 제한된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

플랜을 프라이빗으로 설정하고 선택한 제한된 대상 그룹에만 표시하려면 **프라이빗 플랜입니다.** 를 선택합니다. 프라이빗 플랜으로 게시된 후 대상 그룹을 업데이트하거나 모든 사용자가 해당 플랜을 사용할 수 있도록 지정할 수 있습니다. 플랜이 모든 사용자에게 표시되도록 게시된 후에는 모든 사용자에게 계속 표시되어야 하며, 프라이빗 플랜으로 다시 구성할 수 없습니다.

플랜을 프라이빗으로 설정하는 경우 **Azure 구독 ID**와 설명을 입력합니다. 각 대상 그룹은 해당 프라이빗 플랜에 액세스할 수 있습니다. 할당된 각 Azure 구독 ID에 관한 설명을 포함하는 옵션과 함께 Azure 구독 ID를 사용하여 액세스 권한을 할당합니다. CSV 파일을 가져와서 개별적으로 고객 구독 ID 최대 10개 또는 20,000개를 추가합니다. Azure 구독 ID는 GUID로 표시되며 문자는 소문자여야 합니다.

>[!Note]
>프라이빗 또는 제한된 대상 그룹은 **미리 보기** 탭에서 정의한 미리 보기 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 제품이 마켓플레이스에 라이브로 게시되기 ‘전에’ 해당 제품에 액세스할 수 있습니다. 프라이빗 대상 그룹 선택은 특정 플랜에만 적용되지만, 미리 보기 대상 그룹은 유효성 검사를 위해 모든 플랜(프라이빗 및 퍼블릭)을 볼 수 있습니다.

#### <a name="hide-plan"></a>플랜 숨기기

다른 솔루션 템플릿 또는 관리형 애플리케이션에서 참조되는 경우에만 솔루션 템플릿을 간접적으로 배포하려면 이 확인란을 선택하여 솔루션 템플릿을 게시하지만 고객이 직접 검색하지 못하도록 숨깁니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="pricing-and-availability-managed-application-plans-only"></a>가격 책정 및 가용성(관리형 애플리케이션 플랜에만 해당)

이 플랜을 제공할 **시장**, 솔루션 관리의 월별 **가격 책정** 및 특정 고객에게만 표시해야 하는 경우(프라이빗 대상 그룹) **플랜 표시 여부**를 구성하는 데 사용합니다.

계속하기 전에 **초안 저장**을 선택합니다.

#### <a name="markets"></a>시장

모든 플랜은 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 플랜을 제공할 시장 위치의 확인란을 선택합니다. Microsoft가 게시자를 대신하여 판매세와 이용세를 송금하는 “세금 송금” 국가/지역을 선택하는 검색 상자 및 단추가 도움이 되도록 포함되어 있습니다.

이미 플랜 가격을 미국 달러(USD)로 설정하고 다른 시장 위치를 추가하는 경우 새 시장의 가격은 현재 환율에 따라 계산됩니다. 게시하기 전에 항상 각 시장의 가격을 검토합니다. 변경 내용을 저장한 후 “가격 내보내기(.xlsx)” 링크를 사용하여 가격 책정을 검토할 수 있습니다.

#### <a name="pricing"></a>가격 책정

이 플랜의 월별 가격을 제공합니다.  이 가격은 이 솔루션을 통해 배포된 리소스에 의해 발생하는 Azure 인프라 또는 종량제 소프트웨어 비용에 추가됩니다.

또한 월별 가격 외에 [요금 청구](./azure-app-metered-billing.md)를 사용하여 비표준 단위의 사용량에 해당하는 가격을 설정할 수 있습니다.  월별 가격을 0으로 설정하고 원하는 경우 요금 청구를 사용하여 전용 요금을 청구할 수 있습니다.

USD(미국 달러)로 설정된 가격은 저장할 때 현재 환율을 사용하여 선택한 모든 시장의 현지 통화로 변환됩니다. 가격 책정 스프레드시트를 내보내고 각 시장에서 가격을 검토하여 게시하기 전에 해당 가격의 유효성을 검사합니다. 개별 시장에서 사용자 지정 가격을 설정하려면 가격 책정 스프레드시트를 수정하고 가져옵니다.

>[!Note]
>가격 책정 데이터를 내보낼 수 있도록 먼저 가격 책정 변경 내용을 저장해야 합니다.

플랜이 게시된 후에 변경 가능한 내용에는 일부 제한이 있으므로 게시하기 전에 가격을 신중하게 검토해야 합니다.  

>[!Note]
>플랜의 시장 가격이 게시된 후에는 나중에 변경할 수 없습니다.

#### <a name="plan-visibility"></a>플랜 표시 여부

모든 사용자 또는 선택한 특정 대상 그룹에만 표시되도록 각 플랜을 구성할 수 있습니다. Azure 구독 ID를 사용하여 제한된 대상 그룹의 멤버 자격을 할당할 수 있습니다.

플랜을 프라이빗으로 설정하고 선택한 제한된 대상 그룹에만 표시하려면 **프라이빗 플랜입니다.** 를 선택합니다. 프라이빗 플랜으로 게시된 후 대상 그룹을 업데이트하거나 모든 사용자가 해당 플랜을 사용할 수 있도록 지정할 수 있습니다. 플랜이 모든 사용자에게 표시되도록 게시된 후에는 모든 사용자에게 계속 표시되어야 하며, 프라이빗 플랜으로 다시 구성할 수 없습니다.

>[!Note]
>프라이빗 또는 제한된 대상 그룹은 **미리 보기** 탭에서 정의한 미리 보기 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 제품이 마켓플레이스에 라이브로 게시되기 ‘전에’ 해당 제품에 액세스할 수 있습니다. 프라이빗 대상 그룹 선택은 특정 플랜에만 적용되지만, 미리 보기 대상 그룹은 유효성 검사를 위해 모든 플랜(프라이빗 및 퍼블릭)을 볼 수 있습니다.

플랜을 프라이빗으로 설정하는 경우 **Azure 구독 ID**와 설명을 입력합니다. 각 대상 그룹은 해당 프라이빗 플랜에 액세스할 수 있습니다. 할당된 각 Azure 구독 ID에 관한 설명을 포함하는 옵션과 함께 Azure 구독 ID를 사용하여 액세스 권한을 할당합니다. CSV 파일을 가져와서 개별적으로 고객 구독 ID 최대 10개 또는 20,000개를 추가합니다. Azure 구독 ID는 GUID로 표시되며 문자는 소문자여야 합니다.

>[!Note]
>사설 제안은 클라우드 솔루션 공급자 프로그램 (CSP)의 대리점을 통해 설정 된 Azure 구독에서 지원 되지 않습니다.

### <a name="technical-configuration"></a>기술 구성

이 탭에서 고객이 플랜을 배포할 수 있도록 하는 배포 패키지를 업로드할 수 있습니다.

>[!Note]
>**플랜 설정** 탭에서 다른 플랜의 패키지를 다시 사용하도록 이 플랜을 구성한 경우에는 이 탭이 표시되지 않습니다.

#### <a name="package-details"></a>패키지 세부 정보

이 탭에서는 기술 구성의 초안 버전을 편집할 수 있습니다.

**버전** – 기술 구성의 현재 버전을 할당합니다.  이 페이지에 변경 내용을 게시할 때마다 이 버전을 증분합니다. 버전은 `{integer}.{integer}.{integer}` 형식이어야 합니다.

**패키지 파일**(.zip) – 이 패키지에는 플랜에 필요한 모든 템플릿 파일뿐만 아니라 `.zip` 파일로 패키지된 추가 리소스가 포함됩니다.

모든 Azure 애플리케이션 플랜 패키지는 `.zip` 보관 파일의 루트 폴더에 다음 두 개의 파일을 포함해야 합니다.

* [mainTemplate.json](../../azure-resource-manager/resource-group-overview.md)이라는 Resource Manager 템플릿 파일. 이 템플릿은 고객 Azure 구독에 대한 리소스 배포를 자동화합니다.  Resource Manager 템플릿의 예는 [Azure 빠른 시작 템플릿 갤러리](https://azure.microsoft.com/documentation/templates/) 또는 해당 [GitHub: Azure Resource Manager 빠른 시작 템플릿](https://github.com/azure/azure-quickstart-templates) 리포지토리를 참조하세요.
* [createUiDefinition.json](../../azure-resource-manager/managed-application-createuidefinition-overview.md)이라는 Azure 애플리케이션 만들기 환경의 사용자 인터페이스 정의.

지원되는 최대 파일 크기는 다음과 같습니다.

* 전체 압축 `.zip` 보관 파일 크기로 최대 1GB
* `.zip` 보관 파일 내에서 압축되지 않은 개별 파일에 대해 최대 1GB  

모든 새 Azure 애플리케이션 제품은 [Azure 파트너 고객 사용량 기여](../azure-partner-customer-usage-attribution.md) GUID도 포함해야 합니다.

>[!Note]
>파일을 업로드하는 동안 문제가 발생한 경우 로컬 네트워크가 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스를 차단하지 않는지 확인하세요.

관리형 애플리케이션 플랜의 경우 이 탭에서 추가 정보가 필요합니다.

#### <a name="previously-published-packages"></a>이전에 게시된 패키지

**이전에 게시된 패키지** 하위 탭에서는 기술 구성의 게시된 모든 버전을 볼 수 있습니다.

#### <a name="enable-just-in-time-jit-access"></a>JIT(Just-In-Time) 액세스 사용

이 플랜에 JIT(Just-In-Time) 액세스를 사용하도록 설정하려면 이 옵션을 선택합니다.  JIT 액세스를 사용하면 문제 해결 또는 유지 관리를 위해 관리형 애플리케이션의 리소스에 대한 높은 액세스 권한을 요청할 수 있습니다. 항상 리소스에 대한 읽기 전용 액세스 권한을 가지지만 특정 기간 동안 더 큰 액세스 권한을 가질 수 있습니다.  자세한 내용은 [Azure Managed Applications의 JIT(Just-In-Time) 액세스 사용 및 요청](../../managed-applications/request-just-in-time-access.md)을 참조하세요.  관리형 애플리케이션의 소비자가 사용자 계정에 영구 액세스 권한을 부여하도록 요구하려면 이 옵션의 선택을 취소합니다.

>[!Note]
>이 기능을 지원하려면 `createUiDefinition.json` 파일을 업데이트해야 합니다.  

#### <a name="deployment-mode"></a>배포 모드

이 플랜을 배포할 때 **완료** 또는 **증분 배포 모드**를 구성할지 여부를 선택합니다. 

* **완료 모드**에서 고객이 애플리케이션을 다시 배포하면 `mainTemplate.json`에 정의되지 않은 관리되는 리소스 그룹의 리소스가 제거됩니다. 
* **증분 모드**에서 애플리케이션을 다시 배포하면 기존 리소스가 변경되지 않습니다.

배포 모드에 관한 자세한 내용은 [Azure Resource Manager 배포 모드](../../azure-resource-manager/deployment-modes.md)를 참조하세요.

#### <a name="notification-endpoint-url"></a>알림 엔드포인트 URL

이 플랜 버전의 관리형 애플리케이션 인스턴스에서 모든 CRUD 작업에 관한 알림을 수신하려면 HTTPS Webhook 엔드포인트를 지정합니다.

#### <a name="customize-allowed-customer-actions"></a>허용되는 고객 작업 사용자 지정

기본적으로 사용할 수 있는 “`*/read`” 작업 외에 관리되는 리소스에서 고객이 할 수 있는 작업을 지정하려면 이 옵션을 선택합니다.

고객이 여기에서 수행할 수 있도록 할 추가 작업을 세미콜론으로 구분하여 나열합니다.  자세한 내용은 [Azure 리소스에 대한 거부 할당 이해](../../role-based-access-control/deny-assignments.md)를 참조하세요. 사용 가능한 작업에 대해서는 [Azure Resource Manager 리소스 공급자 작업](../../role-based-access-control/resource-provider-operations.md)을 참조하세요. 예를 들어 소비자가 가상 머신을 다시 시작하도록 허용하려면 허용되는 작업에 `Microsoft.Compute/virtualMachines/restart/action`을 추가합니다.

#### <a name="global-azure--azure-government-cloud"></a>글로벌 Azure/Azure Government 클라우드

지원되는 각 클라우드에서 이 관리형 애플리케이션에 대한 관리 액세스 권한이 있는 사용자를 표시합니다. 관리 되는 리소스 그룹에 대 한 사용 권한을 부여 하려는 사용자, 그룹 또는 응용 프로그램은 AD (Azure Active Directory) id를 사용 하 여 식별 됩니다.

**Azure Active Directory 테 넌 트 id** – 사용 권한을 부여 하려는 사용자, 그룹 또는 응용 프로그램의 id를 포함 하는 Azure AD 테 넌 트 id (디렉터리 ID 라고도 함)입니다. Azure Portal에서 [Azure Active Directory 속성](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)의 Azure AD 테 넌 트 ID를 찾을 수 있습니다.

**권한 부여** – 관리되는 리소스 그룹에 권한을 부여하려는 사용자, 그룹 또는 애플리케이션의 Azure Active Directory 개체 ID를 추가합니다. 사용자를 해당 보안 주체 ID로 식별하며, 이 ID는 [Azure Portal의 Azure Active Directory 사용자 블레이드](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)에서 찾을 수 있습니다.

각 보안 주체의 Azure AD 기본 제공 역할(소유자 또는 기여자) 중 하나를 목록에서 선택합니다. 선택하는 역할은 보안 주체가 고객 구독의 리소스에 대해 가질 수 있는 권한을 설명합니다. 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요. RBAC(역할 기반 액세스 제어)에 관한 자세한 내용은 [Azure Portal에서 RBAC 시작](../../role-based-access-control/overview.md)을 참조하세요.

>[!Note]
>클라우드당 100개까지 권한 부여를 추가할 수 있지만 일반적으로 Active Directory 사용자 그룹을 만들고 “보안 주체 ID”에 해당 ID를 지정하는 것이 더 쉽습니다. 이렇게 하면 플랜이 배포된 후 관리 그룹에 더 많은 사용자를 추가하고 더 많은 권한 부여만을 추가하기 위해 플랜을 업데이트할 필요성을 줄일 수 있습니다.

#### <a name="policy-settings"></a>정책 설정

배포된 솔루션의 규정 준수 요구 사항을 지정하려면 [Azure Policy](../../governance/policy/overview.md)를 관리형 애플리케이션에 적용합니다. 매개 변수 값의 형식 및 정책 정의는 [Azure Policy 샘플](../../governance/policy/samples/index.md)을 참조하세요. 최대 5개 정책 및 각 정책 옵션의 인스턴스 하나만 구성할 수 있습니다. 일부 정책에는 추가 매개 변수가 필요합니다. 표준 SKU는 감사 정책에 필요합니다. 정책 이름은 50자로 제한됩니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="co-sell-with-microsoft"></a>Microsoft와 공동 판매

공동 판매 탭에서 정보를 제공하는 것은 완전히 제품 게시를 위한 선택 사항입니다. 공동 판매 준비 및 IP 공동 판매 준비 상태를 달성해야 합니다. 제공하는 정보는 Microsoft 영업 팀에서 고객 요구 사항에 적합한지 평가할 때 솔루션을 자세히 알아보는 데 사용됩니다. 고객에게 직접 제공되지 않습니다.

이 탭에 관한 자세한 내용은 [파트너 센터의 공동 판매 옵션](commercial-marketplace-co-sell.md)을 참조하세요.

## <a name="resell-through-csps"></a>CSP를 통한 재판매

CSP([클라우드 솔루션 공급자](https://azure.microsoft.com/offers/ms-azr-0145p/)) 프로그램에서 파트너가 사용할 수 있도록 설정하여 제품 범위를 확장합니다. 이렇게 하면 재판매인은 고객에게 제품을 판매하고 번들 솔루션을 만들 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="test-drive"></a>시험 사용

제품을 게시하기 전에 고객이 사용해 볼 수 있도록 데모(시험 사용)를 설정합니다. 고객이 일정 기간 동안 제품을 사용해 볼 수 있도록 데모 환경을 만들려면 [상업용 Marketplace에서 제품 시험 사용](test-drive.md)을 참조하세요.

시험 사용을 사용하도록 설정하려면 [제품 설정](#test-drive) 탭에서 **시험 사용을 사용하도록 설정** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 이 확인란 선택을 취소합니다.

### <a name="test-drive-technical-configuration"></a>시험 사용 기술 구성

- **AZURE ad 앱 id** (필수): AZURE ACTIVE DIRECTORY (AD) [응용 프로그램 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인 하 고 왼쪽 메뉴에서 Active Directory 탭을 선택한 다음 **앱 등록**를 선택 하 고 나열 된 **응용 프로그램 ID** 번호 (예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다.

#### <a name="deployment-subscription-details"></a>배포 구독 세부 정보

사용자를 대신해서 시험 사용을 배포하려면 별도의 고유한 Azure 구독을 만들고 제공합니다(Power BI 시험 사용에는 필요하지 않음).

* **Azure 구독 ID**(Azure Resource Manager 및 Logic Apps에 필요) - 리소스 사용량 보고 및 청구를 위해 Azure 계정 서비스에 대한 액세스 권한을 부여하는 구독 ID를 입력합니다. 아직 없는 경우 시험 사용에 사용할 [별도의 Azure 구독을 만드는 것](../../billing/billing-create-subscription.md)이 좋습니다. [Azure Portal](https://portal.azure.com/)에 로그인하여 왼쪽 메뉴의 **구독** 탭으로 이동하면 Azure 구독 ID를 확인할 수 있습니다. 이 탭을 선택 하면 구독 ID (예: "a83645ac-1234-5ab6-6789-1h234g764ghty")가 표시 됩니다.
* **AZURE ad 테 넌 트 id** (필수) – ad (Azure Active Directory) [테 넌 트 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인하고, 왼쪽 메뉴에서 Active Directory 탭을 선택하고, **속성**을 선택한 다음, 나열된 **디렉터리 ID** 번호(예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다. [https://www.whatismytenantid.com](https://www.whatismytenantid.com)에서 도메인 이름 URL을 사용하여 조직의 테넌트 ID를 조회할 수도 있습니다.
* **Azure AD 테넌트 이름**(Dynamic 365에 필요) – Azure AD(Active Directory) 이름을 입력합니다. 이 이름을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 그러면 오른쪽 위 계정 이름 아래에 테넌트 이름이 나열됩니다.
* **AZURE ad 앱 id** (필수) – AZURE ACTIVE DIRECTORY (AD) [응용 프로그램 id](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)를 입력 합니다. 이 ID를 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인하고, 왼쪽 탐색 메뉴에서 Active Directory 탭을 선택하고, **앱 등록**을 선택한 다음, 나열된 **애플리케이션 ID** 번호(예: 50c464d3-4930-494c-963c-1e951d15360e)를 찾습니다.
* **Azure Active Directory 응용 프로그램 클라이언트 암호** (필수) – Azure AD 응용 프로그램 [클라이언트 암호](../../active-directory/develop/howto-create-service-principal-portal.md#create-a-new-application-secret)를 입력 합니다. 이 값을 찾으려면 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 왼쪽 탐색 메뉴에서 **Azure Active Directory** 탭을 선택하고, **앱 등록**을 선택한 다음, 시험 사용 앱을 선택합니다. 그런 다음, **인증서 및 비밀**을 선택하고, **새 클라이언트 암호**를 선택하고, 설명을 입력하고, **만료**에서 **안 함**을 선택한 다음, **추가**를 선택합니다. 이 페이지를 나가기 전에 값을 복사해야 합니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="marketplace-listing-optional"></a>마켓플레이스 목록(선택 사항)

시험 사용 환경을 설명합니다.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **설명**(필수) – 시험 사용, 데모 대상, 사용자의 실험 목표, 알아볼 기능 및 사용자가 제품을 가져올지 여부를 결정하는 데 도움이 되는 관련 정보를 설명합니다. 이 필드에는 최대 3,000자의 텍스트를 입력할 수 있습니다. 
* **액세스 정보**(Azure Resource Manager 및 논리 시험 사용에 필요) - 이 시험 사용에 액세스하고 사용하기 위해 고객이 알아야 할 사항을 설명합니다. 제품 사용에 대한 시나리오와 고객이 시험 사용을 통해 기능에 액세스하기 위해 알아야 할 사항을 정확히 살펴봅니다. 이 필드에는 최대 10,000자의 텍스트를 입력할 수 있습니다.
* **사용자 설명서**(필수) – 시험 사용 환경의 심층 연습입니다. 사용자 설명서는 고객이 시험 사용을 통해 얻을 수 있는 사항을 정확하게 다루며, 고객에게 있을 수 있는 질문에 대한 참조로 사용됩니다. 파일은 PDF 형식이어야 하며 업로드한 후 최대 255자로 이름이 지정되어야 합니다.
* **동영상: 동영상 추가**(선택 사항) – 동영상은 YouTube 또는 Vimeo에 업로드되고 여기에서 링크 및 썸네일 이미지(533 x 324픽셀)로 참조되므로, 고객은 제품의 기능을 성공적으로 사용하고 혜택을 강조하는 시나리오를 이해하는 방법을 포함하여 시험 사용을 보다 잘 이해하는 데 도움이 되는 연습 내용을 볼 수 있습니다.
  * **이름**(필수)
  * **주소**(YouTube 또는 Vimeo만 해당, 필수)
  * **축소판 그림** (이미지 파일은 PNG 형식 이어야 하며 533 x 324 픽셀 이어야 함).

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="publish"></a>게시

제품의 필수 섹션을 모두 완료한 후에 포털의 오른쪽 위에서 **검토 및 게시**를 선택합니다.

제품의 각 섹션에 대한 완료 상태를 검토합니다.
    - *시작 되지 않음* -섹션이 작업 되지 않아 완료 해야 함을 의미 합니다.
    - *불완전* -섹션에 수정 해야 하는 오류가 있거나 추가 정보를 제공 해야 함을 의미 합니다. 섹션으로 돌아가서 업데이트합니다.
    - *Complete* – 섹션이 완료 되었고 필요한 모든 데이터가 제공 되었으며 오류가 없음을 의미 합니다. 제품을 제출하려면 먼저 제품의 모든 섹션이 완료됨 상태여야 합니다.

이 제품을 처음 게시하는 경우 앱을 이해하는 데 도움이 되는 추가 정보뿐만 아니라 앱이 올바르게 테스트되는지 확인하는 테스트 지침을 인증 팀에 제공할 수 있습니다.

**제출**을 선택하여 게시할 제품을 제출합니다. 제품의 미리 보기 버전을 검토하고 승인할 수 있는 시기를 알려주는 메일을 보내 드립니다.

파트너 센터로 돌아가서 제품을 퍼블릭에 게시(또는 프라이빗 제품을 프라이빗 대상 그룹에 게시)하려면 **라이브 상태로 전환**을 선택합니다.

### <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

게시 프로세스의 **수동 유효성 검사** 단계는 제품 및 관련 기술 자산(특히 Azure Resource Manager 템플릿)의 광범위한 검토를 나타내며, 문제는 일반적으로 PR(끌어오기 요청) 링크로 표시됩니다. 이러한 PR을 보고 응답하는 방법에 대한 설명은 [검토 피드백 처리](./azure-apps-review-feedback.md)를 참조하세요.

하나 이상의 게시 단계에서 오류가 발생하면 제품을 게시하기 전에 해당 오류를 수정합니다.

## <a name="next-steps"></a>다음 단계

* [상업용 Marketplace에서 기존 제품 업데이트](./update-existing-offer.md)
