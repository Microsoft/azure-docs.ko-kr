---
title: Azure Marketplace에서 Azure 가상 머신 제품 만들기
description: Azure Marketplace에 가상 컴퓨터 제품을 게시 하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 08/07/2020
ms.openlocfilehash: 33b3dcdc4fbcf08114f6ba6d63bb949250380d63
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816527"
---
# <a name="create-an-azure-virtual-machine-offer-on-azure-marketplace"></a>Azure Marketplace에서 Azure 가상 머신 제품 만들기

이 문서에서는 Azure 가상 머신 제품을 [Azure Marketplace](https://azuremarketplace.microsoft.com/)에 만들고 게시하는 방법에 대해 설명합니다. 운영 체제, VHD(가상 하드 디스크) 및 최대 16개의 데이터 디스크가 포함된 Windows 기반 및 Linux 기반 가상 머신을 모두 처리합니다. 

시작하기 전에 [파트너 센터에서 상업용 마켓플레이스 계정을 만듭니다](create-account.md). 계정이 상업용 마켓플레이스 프로그램에 등록되어 있는지 확인합니다.

## <a name="introduction"></a>소개

### <a name="the-benefits-of-publishing-to-azure-marketplace"></a>Azure Marketplace에 게시하는 경우의 이점

제품을 Azure Marketplace에 게시하는 경우 다음을 수행할 수 있습니다.

- Microsoft 브랜드의 도움으로 회사를 홍보합니다.
- 1억 명 이상의 Office 365 및 Dynamics 365 사용자 및 20만 개 이상의 조직에 영향을 줍니다.
- 이러한 마켓플레이스에서 고품격의 잠재 고객을 확보합니다.
- Microsoft 현장 판매 및 통신 판매 팀에서 서비스를 홍보합니다.

### <a name="before-you-begin"></a>시작하기 전에

아직 검토하지 않은 경우 [가상 머신 제품 게시 가이드](../marketplace-virtual-machines.md) 및 이 Azure Virtual Machine 자료를 검토합니다.

- 빠른 시작 가이드
  - [Azure 퀵 스타트 템플릿](https://azure.microsoft.com/resources/templates/)
  - [GitHub Azure 퀵 스타트 템플릿](https://github.com/azure/azure-quickstart-templates)
- 자습서
  - [Linux VM](../../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../../virtual-machines/windows/tutorial-manage-vm.md)
- 샘플
  - [Linux VM용 Azure CLI 샘플](../../virtual-machines/linux/cli-samples.md)
  - [Linux VM용 Azure PowerShell 샘플](../../virtual-machines/linux/powershell-samples.md)
  - [Windows VM용 Azure CLI 샘플](../../virtual-machines/windows/cli-samples.md)
  - [Windows VM용 Azure PowerShell 샘플](../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

### <a name="fundamentals-in-technical-knowledge"></a>기술 지식의 기본 사항

제품 설계, 작성 및 테스트 프로세스에는 시간이 걸리고, 제품을 빌드하는 데 사용되는 Azure 플랫폼과 기술 모두에 대한 전문 지식이 필요합니다.

엔지니어링 팀은 다음 Microsoft 기술에 대한 기본적인 이해와 실무 지식을 갖추어야 합니다.

- [Azure 서비스](https://azure.microsoft.com/services/)
- [Azure 애플리케이션 설계 및 아키텍처](https://azure.microsoft.com/solutions/architecture/)
- [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage) 및 [Azure 네트워킹](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>새 제안 만들기

1. [파트너 센터](https://partner.microsoft.com/dashboard/home)에 로그인합니다.
2. 왼쪽 창에서 **상업용 마켓플레이스** > **개요**를 차례로 선택합니다.
3. **개요** 페이지에서 **새 제품** > **Azure Virtual Machine**을 차례로 선택합니다.

    ![왼쪽 창 메뉴 옵션과 "새 제품" 단추를 보여 주는 스크린샷](./media/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 제품을 게시한 후에 파트너 센터에서 편집한 내용은 해당 제품을 다시 게시한 후에만 Azure Marketplace에 표시됩니다. 제품을 변경한 후에는 항상 다시 게시해 주세요.

## <a name="new-offer"></a>새 제안

**ID**를 입력합니다. 계정의 각 제품에 대한 고유 식별자입니다.

- 이 ID는 Azure Marketplace 제품의 웹 주소와 Azure PowerShell 및 Azure CLI(해당되는 경우)에서 고객에게 표시됩니다.
- 소문자와 숫자만 사용할 수 있습니다. ID는 하이픈 및 밑줄을 포함할 수 있지만 공백은 포함할 수 없으며, 50자로 제한됩니다. 예를 들어 **test-offer-1**을 입력하면 제품 웹 주소가 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`이 됩니다.
- **만들기**를 선택한 후에는 제품 ID를 변경할 수 없습니다.

**별칭**을 입력합니다. 제품 별칭은 파트너 센터에서 제품에 사용되는 이름입니다.

- 이 이름은 Azure Marketplace에서 사용되지 않습니다. 고객에게 표시되는 제품 이름 및 다른 값과는 다릅니다.

**만들기**를 선택하여 제품을 생성하고 계속 진행합니다.

## <a name="offer-setup"></a>제품 설정

### <a name="test-drive"></a>시험 사용

*시험 사용*은 잠재 고객에게 제품을 소개하는 좋은 방법입니다. 고객에게 "구입 전 사용해 보기" 옵션을 제공하여 전환을 촉진하고 훌륭한 자격이 있는 잠재 고객을 창출할 수 있습니다. 자세한 내용은 [시험 사용이란?](../what-is-test-drive.md)을 참조하세요.

일정 기간 동안 시험 사용을 사용하도록 설정하려면 **시험 사용** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 확인란의 선택을 취소합니다.

추가 시험 사용 리소스는 다음과 같습니다.

- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [마케팅 모범 사례](../what-is-test-drive.md)
- [시험 사용 개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF 파일을 다운로드합니다(팝업 차단이 해제되어 있는지 확인).

### <a name="customer-leads"></a>잠재 고객

파트너 센터를 사용하여 제품을 상업용 마켓플레이스에 게시하는 경우 CRM(고객 관계 관리) 시스템에 연결합니다. 이렇게 하면 누군가가 제품에 관심을 보이거나 제품을 사용하는 즉시 고객 연락처 정보를 받을 수 있습니다. 시험 사용을 사용하도록 설정하려면 CRM에 연결해야 합니다(이전 섹션 참조). 그렇지 않으면 CRM에 연결하는 것은 선택 사항입니다.

1. 잠재 고객을 보내려는 잠재 고객 대상을 선택합니다. 파트너 센터에서 지원하는 CRM 시스템은 다음과 같습니다.
    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) for Customer Engagement
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > CRM 시스템이 여기에 나열되지 않은 경우 [Azure Table 스토리지](commercial-marketplace-lead-management-instructions-azure-table.md) 또는 [HTTPS 엔드포인트](commercial-marketplace-lead-management-instructions-https.md)를 사용하여 잠재 고객 데이터를 저장합니다. 그런 다음, 데이터를 CRM 시스템으로 내보냅니다.

1. 파트너 센터에 게시할 때 제품을 잠재 고객 대상에 연결합니다.
1. 잠재 고객 대상에 대한 연결이 제대로 구성되어 있는지 확인합니다. 파트너 센터에 게시되면 Microsoft에서 연결의 유효성을 검사하고 테스트 잠재 고객을 보냅니다. 제품이 라이브 모드로 전환되기 전에 해당 제품을 미리 보는 한편, 미리 보기 환경에서 제품을 직접 배포하여 잠재 고객 연결을 테스트할 수도 있습니다.
1. 잠재 고객이 손실되지 않도록 잠재 고객 대상에 대한 연결이 업데이트된 상태로 유지되어야 합니다.

1. 계속하기 전에 **초안 저장**을 선택합니다.

## <a name="properties"></a>속성

**속성** 페이지에서 제품을 지 원하는 Azure Marketplace, 응용 프로그램 버전 및 법적 계약을 그룹화 하는 데 사용 되는 범주를 정의 합니다.

### <a name="category"></a>범주

범주 및 하위 범주를 선택 하 여 제품을 적절 한 마켓플레이스 검색 영역에 넣습니다. 제품 설명에서 제품이 해당 범주를 지원하는 방법을 설명해야 합니다. 선택:

- 기본 및 보조 범주 (선택 사항)를 포함 하 여 적어도 하나 이상의 범주입니다.
- 각 주 및/또는 보조 범주에 대해 최대 두 개의 하위 범주 제안에 적용 되는 하위 범주가 없으면 **해당 없음**을 선택 합니다.

[제품 목록 모범 사례](../gtm-offer-listing-best-practices.md)에서 범주 및 하위 범주의 전체 목록을 참조 하세요. 가상 컴퓨터는 항상 Azure Marketplace의 **계산** 범주에 표시 됩니다.

### <a name="legal"></a>법적 정보

고객에게 제품 사용 약관을 제공해야 합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

- **사용자 고유의 사용 약관 사용**

   사용자 고유의 사용자 지정 약관을 제공하려면 **사용 약관** 상자에서 최대 10,000자의 텍스트를 입력합니다. 더 긴 설명이 필요하면 사용 약관에 대한 단일 웹 주소를 입력합니다. 이는 고객에게 활성 링크로 표시됩니다.

   고객은 이러한 사용 약관에 동의해야 제품을 사용해 볼 수 있습니다.

- **Microsoft 상업용 마켓플레이스 표준 계약 사용**

   고객을 위한 조달 프로세스를 간소화하고 소프트웨어 공급업체의 법적 복잡성을 줄이기 위해 Microsoft는 상업용 마켓플레이스 표준 계약을 제공합니다. 표준 계약에 따라 소프트웨어를 제공하는 경우 고객은 이 계약을 한 번만 읽고 동의해야 하며 사용자 지정 사용 약관을 만들 필요가 없습니다.

   **Microsoft 상업용 마켓플레이스의 표준 계약을 사용하시겠습니까?** 확인란을 선택한 다음, 팝업 창에서 **동의함**을 선택합니다(보려면 위쪽으로 스크롤해야 할 수도 있음).

   ![파트너 센터의 법적 정보 창과 "Microsoft 상업용 마켓플레이스의 표준 계약을 사용하시겠습니까?" 확인란을 보여 주는 스크린샷](media/use-standard-contract.png)

  > [!NOTE]
  > 상업용 마켓플레이스 표준 계약을 사용하여 제품이 게시되면 사용자 고유의 사용 약관을 사용할 수 없습니다. 표준 계약 또는 사용자 고유의 사용 약관에 따라 솔루션을 제공할 수 있습니다.

  자세한 내용은 [Microsoft 상업용 마켓플레이스 표준 계약](../standard-contract.md)을 참조하세요. [표준 계약](https://go.microsoft.com/fwlink/?linkid=2041178) PDF 파일을 다운로드합니다(팝업 차단이 해제되어 있는지 확인).

  **표준 계약 수정안**

  표준 계약 수정안을 사용하면 간단히 하기 위해 표준 계약 사용 약관을 선택하고 제품 또는 비즈니스에 대한 용어를 만들 수 있습니다. 고객은 Microsoft 표준 계약을 이미 검토하고 동의한 경우에만 계약 수정안을 검토해야 합니다. 두 가지 유형의 수정안이 있습니다.

  * **범용 수정안**: 이러한 수정안은 모든 고객의 표준 계약에 일반적으로 적용됩니다. 이는 구매 흐름에서 제품의 모든 고객에게 표시됩니다. 고객은 표준 계약 및 수정안의 사용 약관에 동의해야 제품을 사용할 수 있습니다. 제품당 하나의 범용 수정안을 제공할 수 있습니다. 이 상자에서는 문자를 무제한으로 입력할 수 있습니다. 이러한 사용 약관은 검색 및 구매 흐름 중에 AppSource, Azure Marketplace 및/또는 Azure Portal의 고객에게 표시됩니다.

  * **사용자 지정 수정안**: 표준 계약에 대한 특별 수정안은 Azure 테넌트 ID를 통해 특정 고객을 대상으로 합니다. 대상으로 지정하려는 테넌트를 선택할 수 있습니다. 테넌트의 고객만 제품의 구매 흐름에서 사용자 지정 수정안 사용 약관을 제공받습니다. 고객은 표준 계약 및 수정안의 사용 약관에 동의해야 제품을 사용할 수 있습니다.

    1. 먼저 **사용자 지정 수정 약관 추가(최대 10개)** 를 선택합니다. 제품당 최대 10개의 사용자 지정 수정안 사용 약관을 제공할 수 있습니다. 다음을 수행합니다.

       a. **사용자 지정 수정 약관** 상자에서 사용자 고유의 수정안 사용 약관을 입력합니다. 문자는 무제한으로 입력할 수 있습니다. 이러한 사용자 지정 사용 약관에 대해 지정한 테넌트 ID의 고객만 Azure Portal의 제품 구매 흐름에서 이러한 약관을 확인할 수 있습니다.

       b. (필수) **테넌트 ID**를 제공합니다. 각 사용자 지정 수정안은 최대 20개의 테넌트 ID를 대상으로 할 수 있습니다. 사용자 지정 수정안을 추가하는 경우 Azure에서 고객을 식별할 수 있는 하나 이상의 테넌트 ID를 제공해야 합니다. 고객은 Azure에서 **Azure Active Directory** > **속성**을 차례로 선택하여 이를 확인할 수 있습니다. 디렉터리 ID 값은 테넌트 ID입니다(예: 50c464d3-4930-494c-963c-1e951d15360e). 또한 [내 Microsoft Azure 및 Office 365 테넌트 ID는 무엇인가요?](https://www.whatismytenantid.com/)에서 도메인 이름 웹 주소를 사용하여 조직의 테넌트 ID를 확인할 수도 있습니다.

       다. (선택 사항) 테넌트 ID에 대해 친숙한 **설명**을 제공합니다. 이는 수정안이 적용되는 대상 고객을 식별하는 데 도움이 됩니다.

        > [!NOTE]
        > 이러한 두 가지 유형의 수정안은 서로 쌍을 이룹니다. 또한 사용자 지정 수정안이 적용되는 대상 고객은 구매 중에 표준 계약의 범용 수정안을 받을 수 있습니다.

    1. 계속하기 전에 **초안 저장**을 선택합니다.

## <a name="offer-listing"></a>제품 목록

**제품 목록** 페이지에서 제품 이름, 설명, 링크 및 연락처와 같은 제품 세부 정보를 정의합니다.

> [!NOTE]
> 제안 설명이 "이 응용 프로그램은 에서만 사용할 수 있습니다." 라는 구로 시작 하는 경우 설명, 문서, 스크린샷, 사용 약관 등의 제품 목록에는 영어를 사용할 필요가 없습니다 \<non-English language> . 또한 콘텐츠를 제품 목록 콘텐츠에 사용된 언어 이외의 언어로 제공하는 사이트에 연결하기 위한 URL을 제공할 수 있습니다.

### <a name="marketplace-details"></a>마켓플레이스 세부 정보

#### <a name="name"></a>속성

여기에 입력한 이름은 고객에게 제품 목록의 제목으로 표시됩니다. 이 필드는 제품을 만들 때 **제품 별칭** 상자에 입력한 이름으로 자동으로 채워집니다. 이 이름은 나중에 변경할 수 있습니다. 이름은 다음과 같습니다.

- 상표일 수 있습니다. 상표 및 저작권 기호를 포함할 수 있습니다.
- 50자를 초과할 수 없습니다.
- 이모지를 포함할 수 없습니다.

#### <a name="search-results-summary"></a>검색 결과 요약

Azure Marketplace 검색 결과에 표시할 제품에 대한 간단한 설명을 제공합니다. 최대 100자까지 포함할 수 있습니다.

#### <a name="long-summary"></a>긴 요약

Azure Marketplace 검색 결과에 표시할 제품에 대한 자세한 설명을 제공합니다. 최대 256자까지 포함할 수 있습니다.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>개인정보처리방침 링크

조직의 개인 정보 보호 정책에 대한 웹 주소(URL)를 입력합니다. 제품이 개인 정보 보호 법률 및 규정을 준수하는지 확인합니다. 또한 웹 사이트에 유효한 개인 정보 보호 정책을 게시해야 합니다.

### <a name="useful-links"></a>유용한 링크

제품에 대한 온라인 추가 문서를 제공합니다. 링크를 추가하려면 **링크 추가**를 선택하고, 다음 필드를 완성합니다.

- **Name**: 고객은 세부 정보 페이지에서 이름을 볼 수 있습니다.
- **링크(URL)** : 고객이 온라인 문서를 볼 수 있는 링크를 입력합니다.

### <a name="customer-support-links"></a>고객 지원 링크

고객이 지원 팀에 연락할 수 있는 지원 웹 사이트를 제공합니다.

- Azure 글로벌 지원 웹 사이트
- Azure Government 지원 웹 사이트

### <a name="partner-support-contact"></a>파트너 지원 연락처

고객이 지원 티켓을 열면 Microsoft 파트너에서 사용할 연락처 정보를 제공합니다. 이 정보는 Azure Marketplace에 나열되지 않습니다.

- 속성
- Email
- Phone

### <a name="engineering-contact"></a>엔지니어링 연락처

인증 관련 문제를 포함하여 제품에 문제가 있으면 Microsoft에서 사용할 연락처 정보를 제공합니다. 이 정보는 Azure Marketplace에 나열되지 않습니다.

- 속성
- Email
- Phone

### <a name="azure-marketplace-media"></a>Azure Marketplace 미디어

제품에 사용할 로고와 이미지를 제공합니다. 모든 이미지는 PNG 형식이어야 합니다. 이미지가 흐리면 제출이 거부됩니다.

[!INCLUDE [logotips](../includes/graphics-suggestions.md)]

>[!NOTE]
>파일을 업로드하는 데 문제가 있으면 파트너 센터에서 사용하는 https://upload.xboxlive.com 서비스가 로컬 네트워크에서 차단되지 않는지 확인합니다.

#### <a name="azure-marketplace-logos"></a>Azure Marketplace 로고

**큰** 크기의 로고에 대 한 PNG 파일을 제공 합니다. 파트너 센터는이를 사용 하 여 **작고** **중간** 로고를 만듭니다. 필요에 따라 나중에 다른 이미지로 바꿀 수 있습니다.

- **큼** (216 x 216에서 350 x 350 px, required)
- **중간** (90 x 90 px, 선택 사항)
- **작음** (48 x 48 px, 선택 사항)

이러한 로고는 목록의 다른 위치에서 사용 됩니다.

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>스크린샷

제품의 작동 방식을 보여 주는 최대 5개의 스크린샷을 추가합니다. 각 스크린샷은 PNG 형식의 1280&times; 720 픽셀 크기여야 합니다. 각 스크린샷에는 캡션이 포함되어야 합니다.

#### <a name="videos"></a>동영상

제품을 설명하는 최대 5개의 비디오를 추가합니다. 비디오는 외부 비디오 서비스에서 호스팅해야 합니다. 각 비디오의 이름, 웹 주소 및 비디오의 썸네일 PNG 이미지(1280&times; 720 픽셀)를 입력합니다.

리소스를 나열하는 추가 마켓플레이스는 [마켓플레이스 제품 목록에 대한 모범 사례](../gtm-offer-listing-best-practices.md)를 참조하세요.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="preview"></a>미리 보기

더 광범위한 상업용 마켓플레이스 대상 그룹에 라이브로 게시하기 전에 **미리 보기** 탭을 선택한 다음, 제한된 **대상 그룹 미리 보기**를 선택하여 제품의 유효성을 확인합니다.

> [!IMPORTANT]
> **미리 보기** 창에서 제품을 확인한 후 **라이브 상태로 전환**을 선택하여 상업용 마켓플레이스 퍼블릭 대상 그룹을 위한 제품을 게시합니다.

미리 보기 대상 그룹은 Azure 구독 ID GUID를 통해 각각에 대한 선택적 설명과 함께 식별됩니다. 이러한 필드는 고객에게 표시되지 않습니다. Azure 구독 ID는 Azure Portal의 **구독** 페이지에서 확인할 수 있습니다.

하나 이상의 Azure 구독 ID를 개별적으로(최대 10개의 ID) 추가하거나 CSV 파일(최대 100개의 ID)을 업로드하여 추가합니다. 이러한 구독 ID를 추가하면 제품이 라이브로 게시되기 전에 미리 볼 수 있는 사용자를 정의할 수 있습니다. 제품이 이미 라이브 상태인 경우에도 제품에 대한 변경 내용 또는 업데이트를 테스트하기 위해 미리 보기 대상 그룹을 정의할 수 있습니다.

> [!NOTE]
> 미리 보기 대상 그룹은 프라이빗 대상 그룹과 다릅니다. 미리 보기 대상 그룹은 Azure Marketplace에서 라이브로 게시되기 _전에_ 해당 제품에 액세스할 수 있습니다. 제품이 Azure Marketplace에 완전히 게시된 후에 미리 보기 대상 그룹에서 모든 플랜(프라이빗 대상 그룹만 사용할 수 있는 플랜 포함)을 보고 유효성을 검사할 수 있습니다. 프라이빗 대상 그룹(**가격 책정 및 가용성** 플랜 창에서 정의됨)은 특정 플랜에 단독으로 액세스할 수 있습니다.

다음 섹션으로 진행하기 전에 **초안 저장**을 선택합니다.

## <a name="plan-overview"></a>플랜 개요

파트너 센터의 동일한 제품 내에서 다양한 플랜 옵션을 제공할 수 있습니다. 제품에는 수익 화 대상 그룹, Azure 지역, 기능 또는 VM 이미지에 따라 달라질 수 있는 하나 이상의 계획 (이전의 SKU)이 필요 합니다.

각 제품에 대해 최대 100 요금제를 만들 수 있습니다. 최대 45 개는 개인 일 수 있습니다. [Microsoft 상업적 marketplace에서 비공개 제품](../private-offers.md)의 비공개 요금제에 대해 자세히 알아보세요.

플랜이 만들어지면 **플랜 개요** 탭을 선택하여 다음 항목을 표시합니다.

- 플랜 이름
- 라이선스 모델
- 대상 그룹(퍼블릭 또는 프라이빗)
- 현재 게시 상태
- 사용 가능한 작업

**플랜 개요** 창에서 사용할 수 있는 작업은 플랜의 현재 상태에 따라 달라집니다.

- 플랜 상태가 초안인 경우 **초안 삭제**를 선택합니다.
- 플랜 상태가 라이브로 게시되는 경우 **플랜 판매 중지** 또는 **프라이빗 대상 그룹 동기화**를 선택합니다.

### <a name="create-a-new-plan"></a>새 플랜 만들기

위쪽에서 **새 플랜 만들기**를 선택합니다. **새 플랜** 대화 상자가 표시됩니다.

**플랜 ID** 상자에서 이 제품의 각 플랜에 대한 고유한 플랜 ID를 만듭니다. 이 ID는 제품 웹 주소의 고객에게 표시됩니다. 소문자와 숫자, 대시 또는 밑줄과 최대 50자만 사용할 수 있습니다.

> [!NOTE]
> **만들기**를 선택한 후에는 플랜 ID를 변경할 수 없습니다.

**플랜 이름** 상자에서 이 플랜에 대한 이름을 입력합니다. 이 이름은 제품 내에서 선택할 플랜을 결정할 때 고객에게 표시됩니다. 플랜 간의 차이를 명확하게 나타내는 고유한 이름을 만듭니다. 예를 들어 *종량제*, *BYOL*, *고급* 및 *엔터프라이즈* 플랜이 포함된 **Windows Server**를 입력할 수 있습니다.

**만들기**를 선택합니다.

### <a name="plan-setup"></a>플랜 설정

플랜 유형에 대한 상위 수준 구성을 설정하고, 다른 플랜의 기술 구성을 다시 사용할지 여부를 지정하고, 플랜을 사용할 수 있는 Azure 지역을 식별합니다. 여기서 선택한 항목에 따라 동일한 플랜에 대한 다른 창에 표시되는 필드가 결정됩니다.

#### <a name="reuse-a-technical-configuration"></a>기술 구성 다시 사용

동일한 유형의 플랜이 둘 이상 있고 패키지가 동일한 경우 **이 플랜은 다른 플랜의 기술 구성을 다시 사용합니다.** 를 선택할 수 있습니다. 이 옵션을 사용하면 이 제품에 대해 동일한 유형의 다른 플랜 중 하나를 선택하고 해당 기술 구성을 다시 사용할 수 있습니다.

> [!NOTE]
> 다른 플랜의 기술 구성을 다시 사용하는 경우 전체 **기술 구성** 탭이 이 플랜에서 숨겨집니다. 향후의 업데이트를 포함하여 다른 플랜의 기술 구성 세부 정보도 이 플랜에 사용됩니다. 플랜이 게시된 후에는 이 설정을 변경할 수 없습니다.

#### <a name="azure-regions"></a>Azure 지역

플랜은 하나 이상의 Azure 지역에서 사용할 수 있어야 합니다.

**Azure 글로벌** 옵션을 선택하여 상업용 마켓플레이스 통합이 있는 모든 Azure 글로벌 지역의 고객이 플랜을 사용할 수 있도록 합니다. 자세한 내용은 [지리적 가용성 및 통화 지원](../marketplace-geo-availability-currencies.md)을 참조하세요.

**Azure Government** 옵션을 선택하여 [Azure Government](../../azure-government/documentation-government-welcome.md) 지역에서 플랜을 사용할 수 있도록 합니다. 이 지역은 미국 연방, 주, 지방 또는 부족 단체의 고객뿐만 아니라 이러한 고객에게 서비스를 제공할 수 있는 파트너에게도 제어된 액세스를 제공합니다. 게시자는 규정 준수 제어, 보안 조치 및 모범 사례를 담당합니다. Azure Government는 물리적으로 격리된 데이터 센터 및 네트워크를 사용합니다(미국에만 있음).

[Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md)에 게시하기 전에 특정 엔드포인트가 다를 수 있으므로 환경에서 플랜을 테스트하고 유효성을 검사합니다. 플랜을 설정하고 테스트하려면 [Microsoft Azure Government 평가판](https://azure.microsoft.com/global-infrastructure/government/request/) 페이지에서 평가판 계정을 요청합니다.

> [!NOTE]
> 플랜이 게시되고 특정 Azure 지역에서 사용할 수 있으면 해당 지역을 제거할 수 없습니다.

#### <a name="azure-government-certifications"></a>Azure Government 인증

이 옵션은 이전 섹션에서 **Azure Government**를 Azure 지역으로 선택한 경우에만 표시됩니다.

Azure Government 서비스는 특정 정부 규정 및 요구 사항이 적용되는 데이터를 처리합니다. 예를 들어 FedRAMP, NIST 800.171(DIB), ITAR, IRS 1075, DoD L4 및 CJIS가 있습니다. 관련 프로그램에 대한 인증을 알리기 위해 해당 프로그램을 설명하는 최대 100개의 링크를 제공할 수 있습니다. 이러한 링크는 프로그램의 목록에 직접 연결되는 링크이거나 사용자 고유의 웹 사이트에 있는 목록의 규정 준수에 대한 설명에 연결되는 링크일 수 있습니다. 이러한 링크는 Azure Government 고객에게만 표시됩니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="plan-listing"></a>플랜 목록

이 섹션에서는 플랜에 대한 목록 세부 정보를 구성합니다. 이 창에는 동일한 제품의 다른 플랜과 다를 수 있는 특정 정보가 표시됩니다.

#### <a name="plan-name"></a>플랜 이름

이 필드는 사용자가 계획을 만들 때 지정한 이름으로 자동으로 채워집니다. 이 이름은 Azure Marketplace에서 이 플랜의 제목으로 표시됩니다. 100자로 제한됩니다.

#### <a name="plan-summary"></a>플랜 요약

제품이 아니라 플랜에 대한 간단한 요약을 제공합니다. 이 요약은 100자로 제한됩니다.

#### <a name="plan-description"></a>플랜 설명

이 소프트웨어 플랜을 고유하게 만드는 요소를 설명하고, 제품 내 플랜 간의 차이점을 설명합니다. 제품이 아니라 플랜에 대해서만 설명합니다. 플랜 설명은 최대 2,000자까지 포함할 수 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="pricing-and-availability"></a>가격 책정 및 가용성

이 창에서 구성하는 항목은 다음과 같습니다.

- 이 플랜을 사용할 수 있는 시장 모든 요금제는 하나 이상의 [시장](../marketplace-geo-availability-currencies.md)에서 사용할 수 있어야 합니다.
- 시간당 가격
- 플랜을 모든 사용자에게 표시할지, 아니면 특정 고객(프라이빗 대상 그룹)에게만 표시할지 여부

#### <a name="markets"></a>시장

모든 플랜은 하나 이상의 시장에서 사용할 수 있어야 합니다. 이 플랜을 구매할 수 있는 모든 시장 위치에 대한 확인란을 선택합니다. (이러한 시장의 사용자는 제품을 ["플랜 설정"](#plan-setup) 섹션에서 선택한 모든 Azure 지역에 계속 배포할 수 있습니다.) **세금 납부** 단추는 Microsoft에서 사용자를 대신하여 판매세(sales tax)와 사용세(use tax)를 납부하는 국가/지역을 표시합니다. 중국에 게시하는 경우 *체험* 또는 *BYOL(사용자 라이선스 필요)* 플랜으로 제한됩니다.

플랜의 가격을 미국 달러(USD) 통화로 이미 설정했고 다른 시장 위치를 추가하는 경우 새 시장의 가격은 현재 환율에 따라 계산됩니다. 게시하기 전에 항상 각 시장의 가격을 검토합니다. 변경 내용이 저장되면 **가격 내보내기(xlsx)** 를 선택하여 가격 책정을 검토합니다.

시장을 제거하면 활성 배포를 사용하는 해당 시장의 고객은 새 배포를 만들거나 기존 배포를 강화할 수 없습니다. 기존 배포에는 영향을 주지 않습니다.

#### <a name="pricing"></a>가격 책정

**라이선스 모델**에 대해 **사용량 기반 월간 청구 플랜**을 선택하여 이 플랜에 대한 가격 책정을 구성하거나, **사용자 라이선스 필요**를 선택하여 고객이 기존 라이선스를 통해 이 플랜을 사용할 수 있도록 합니다.

사용량 기반 월간 청구 플랜의 경우 다음 세 가지 가격 책정 항목 옵션 중 하나를 사용합니다.

- **코어당**: 코어당 가격 책정(USD)을 제공합니다. Microsoft는 현재 환율을 사용하여 코어 크기별 가격 책정을 계산하고 현지 통화로 변환합니다.
- **코어 크기별**: 코어 크기별 가격 책정(USD)을 제공합니다. Microsoft는 현재 환율을 사용하여 가격 책정을 계산하고 현지 통화로 변환합니다.
- **지역/국가 및 코어 크기별**: 모든 시장에 대해 각 코어 크기에 대한 가격 책정을 제공합니다. 가격은 스프레드시트에서 가져올 수 있습니다.

> [!NOTE]
> 가격 책정 데이터를 내보낼 수 있도록 가격 책정 변경 내용을 저장합니다. 플랜의 시장 가격이 게시된 후에는 나중에 변경할 수 없습니다. 가격을 게시하기 직전에 가격이 적절한지 확인하려면 가격 책정 스프레드시트를 내보내고 각 시장의 가격을 검토합니다.

#### <a name="free-trial"></a>평가판

고객에게 1개월 또는 3개월 *평가판*을 제공할 수 있습니다.

#### <a name="visibility"></a>표시 유형

모든 사용자에게 표시되거나 미리 선택한 사용자에게만 표시되도록 각 플랜을 설계할 수 있습니다. Azure 구독 ID를 사용하여 제한된 대상 그룹의 멤버 자격을 할당합니다.

**공용**: 플랜이 모든 사용자에게 표시됩니다.

**프라이빗 대상**: 플랜이 미리 선택한 대상 그룹에만 표시되도록 합니다. 프라이빗 플랜으로 게시된 후에는 대상 그룹을 업데이트하거나 퍼블릭으로 변경할 수 있습니다. 퍼블릭 플랜으로 책정되면 퍼블릭으로 유지해야 하며, 프라이빗 플랜으로 다시 변경할 수 없습니다.

> [!NOTE]
> 프라이빗 또는 제한된 대상 그룹은 **미리 보기** 창에서 정의한 미리 보기 대상 그룹이 다릅니다. 미리 보기 대상 그룹은 Azure Marketplace에서 라이브로 게시되기 _전에_ 해당 제품에 액세스할 수 있습니다. 프라이빗 대상 그룹 선택은 특정 플랜에만 적용되지만, 미리 보기 대상 그룹은 유효성 검사를 위해 모든 프라이빗 및 퍼블릭 플랜을 볼 수 있습니다.

**제한된 대상 그룹(Azure 구독 ID)** : Azure 구독 ID를 사용하여 이 프라이빗 플랜에 액세스할 수 있는 대상 그룹을 할당합니다. 필요에 따라 할당한 각 Azure 구독 ID에 대한 설명을 포함시킵니다. CSV 스프레드시트를 가져오는 경우 수동으로 최대 10개의 구독 ID를 추가하거나 최대 20,000개의 ID를 추가할 수 있습니다. Azure 구독 ID는 GUID로 표시되며, 모든 문자는 소문자여야 합니다.

>[!Note]
>사설 제안은 클라우드 솔루션 공급자 프로그램 (CSP)의 대리점을 통해 설정 된 Azure 구독에서 지원 되지 않습니다.

#### <a name="hide-a-plan"></a>플랜 숨기기

가상 머신이 다른 솔루션 템플릿 또는 관리형 애플리케이션을 통해 참조될 때만 간접적으로 사용되어야 하는 경우 이 확인란을 선택하여 가상 머신을 게시하지만 해당 가상 머신을 직접 검색하는 고객에게는 숨깁니다.

> [!NOTE]
> 숨겨진 플랜은 미리 보기 링크를 지원하지 않습니다.

계속하기 전에 **초안 저장**을 선택합니다.

### <a name="technical-configuration"></a>기술 구성

이 플랜과 연결되는 이미지 및 기타 기술 속성을 제공합니다. 자세한 내용은 [Azure VM 기술 자산 만들기](create-azure-container-technical-assets.md)를 참조하세요.

> [!NOTE]
> **플랜 설정** 탭에서 다른 플랜의 패키지를 다시 사용하도록 이 플랜을 구성한 경우 **기술 구성** 탭이 표시되지 않습니다.

#### <a name="operating-system"></a>운영 체제

**운영 체제** 창에서 다음을 수행합니다.

- **운영 체제 제품군**에 대해 **Windows** 또는 **Linux** 운영 체제를 선택합니다.
- **릴리스** 또는 **공급업체**에 대해 Windows 릴리스 또는 Linux 공급업체를 선택합니다.
- **운영 체제 이름**에 대해 친숙한 운영 체제 이름을 입력합니다. 이 이름은 고객에게 표시됩니다.

#### <a name="recommended-vm-sizes"></a>권장되는 VM 크기

Azure Marketplace에 표시할 최대 6개의 추천 가상 머신 크기를 선택합니다.

#### <a name="open-ports"></a>포트 열기

배포된 가상 머신에서 퍼블릭 또는 프라이빗 포트를 엽니다.

#### <a name="storage-option-for-deployment"></a>배포용 스토리지 옵션

**디스크 배포 옵션**에 대해 고객이 가상 머신에 사용할 수 있는 디스크 배포 유형을 선택합니다. 배포를 **관리 디스크 배포**로만 제한하는 것이 좋습니다.

#### <a name="properties"></a>속성

**가속화된 네트워킹 지원**에 대해 VM에서 [가속화된 네트워킹](https://go.microsoft.com/fwlink/?linkid=2124513)을 지원하는지 여부를 선택합니다.

#### <a name="vm-images"></a>VM 이미지

가상 머신 이미지에 대한 디스크 버전과 SAS(공유 액세스 서명) URI를 제공합니다. 각 VM 이미지에 대해 최대 16개의 데이터 디스크를 추가합니다. 지정된 제출에서 플랜당 하나의 새 이미지 버전만 제공합니다. 이미지가 게시된 후에는 편집할 수 없지만 삭제할 수는 있습니다. 버전을 삭제하면 새 사용자와 기존 사용자가 모두 삭제된 버전의 새 인스턴스를 배포할 수 없습니다.

- **디스크 버전**: 제공하는 이미지의 버전입니다.
- **SAS URI**: 운영 체제 VHD가 저장된 Azure 스토리지 계정의 위치입니다. SAS URI를 가져오는 방법을 알아보려면 [VM 이미지에 대 한 공유 액세스 서명 URI 가져오기](get-sas-uri.md)를 참조 하세요.
- 데이터 디스크 이미지는 Azure 스토리지 계정에 저장된 VHD 공유 액세스 서명 URI이기도 합니다.
- 플랜에서 제출당 하나의 이미지만 추가합니다.

사용하는 운영 체제에 관계없이 솔루션에 필요한 최소 수의 데이터 디스크만 추가합니다. 배포 중에 고객은 이미지의 일부인 디스크를 제거할 수 없지만, 배포 중 또는 배포 후에 언제든지 디스크를 추가할 수 있습니다.

계속하기 전에 **초안 저장**을 선택하고, **플랜 개요**로 돌아갑니다.

## <a name="resell-through-csps"></a>CSP를 통한 재판매

[CSP(클라우드 솔루션 공급자)](https://azure.microsoft.com/offers/ms-azr-0145p/) 프로그램에서 파트너가 사용할 수 있도록 설정하여 제품의 연결 범위를 확장합니다. 모든 BYOL(사용자 라이선스 필요) 플랜은 자동으로 프로그램에 옵트인됩니다. 또한 비 BYOL 플랜을 옵트인하도록 선택할 수도 있습니다.

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="test-drive"></a>시험 사용

고객이 제품을 구입하기 전에 일정 기간 동안 제품을 사용해 볼 수 있는 데모 또는 *시험 사용*을 설정합니다. 고객을 위한 데모 환경을 만들려면 [상업용 마켓플레이스의 시험 사용 제품](test-drive.md)을 참조하세요.

시험 사용을 사용하도록 설정하려면 **제품 설정** 창에서 **시험 사용** 확인란을 선택합니다. 제품에서 시험 사용을 제거하려면 확인란의 선택을 취소합니다.

추가 시험 사용 리소스는 다음과 같습니다.

- [마케팅 모범 사례](../what-is-test-drive.md)
- [기술 모범 사례](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [시험 사용 개요](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF 파일(팝업 차단이 해제되어 있는지 확인)

계속하기 전에 **초안 저장**을 선택합니다.

## <a name="review-and-publish"></a>검토 및 게시

제품의 모든 필수 섹션이 완료되면 검토 및 게시를 위해 제출할 수 있습니다.

오른쪽 위에서 **검토 및 게시**를 선택합니다.

이 창에서 다음을 수행할 수 있습니다.

- 제품의 각 섹션에 대한 완료 상태를 확인합니다.

  - **시작되지 않음**: 섹션이 시작되지 않았으므로 완료해야 합니다.
  - **불완전**: 섹션에 수정해야 하거나 추가 정보를 제공해야 하는 오류가 있습니다. 불완전한 정보 업데이트에 대한 지침은 이 문서의 이전 섹션을 참조하세요.
  - **전체**: 섹션이 완료되었으며 오류가 없습니다. 제품을 제출하려면 제품의 모든 섹션이 완료되어야 합니다.
- **인증에 대한 참고 사항**을 인증 팀에 제공하여 애플리케이션이 제대로 테스트되었는지 확인합니다. 팀에서 애플리케이션을 이해하는 데 도움이 될 수 있는 테스트 지침과 보충 정보를 포함합니다.

게시할 제품을 제출하려면 **검토 및 게시**를 선택합니다.

Microsoft는 미리 보기 버전의 제품을 검토하고 승인할 수 있는 시기를 알려주는 이메일 메시지를 보냅니다. 제품을 퍼블릭 대상 그룹에 게시하려면(또는 프라이빗 제품인 경우 프라이빗 대상 그룹에 게시) 파트너 센터로 이동하여 제품의 **개요** 페이지로 이동한 다음, **라이브 상태로 전환**을 선택합니다. 게시가 진행 중인 경우 제품의 상태는 페이지 위쪽에 나타납니다.

### <a name="errors-and-review-feedback"></a>오류 및 검토 피드백

게시 프로세스의 **수동 유효성 검사** 단계는 제품 및 관련 기술 자산에 대한 광범위한 검토를 나타냅니다. 이 프로세스에서 제품의 오류를 발견하면 문제를 자세히 설명하는 인증 보고서를 받게 됩니다. 필요한 수정 작업을 수행하고 제품을 다시 게시하기만 하면 됩니다.

## <a name="offer-overview"></a>제품 개요

**제품 개요** 페이지에는 제품을 게시하는 데 필요한 완료됨 및 진행 중 단계 모두와 각 단계를 완료하는 데 걸리는 시간이 시각적으로 표시됩니다.

또한 이 페이지에는 상태에 따라 제품을 사용하는 데 도움이 되는 링크가 포함되어 있습니다.

- 제품이 초안인 경우: [초안 제품 삭제](update-existing-offer.md#delete-a-draft-offer)
- 제품이 라이브인 경우: [제품 판매 중지](update-existing-offer.md#stop-selling-an-offer-or-plan)
- 제품이 미리 보기에 있는 경우: [라이브 상태로 전환](publishing-status.md#publisher-approval)
- 게시자 로그아웃을 완료하지 않은 경우: [게시 취소](update-existing-offer.md#cancel-publishing)

## <a name="marketplace-examples"></a>마켓플레이스 예

Azure Marketplace에서 제공 정보를 표시 하는 방법의 예는 다음과 같습니다.

:::image type="content" source="media/example-azure-marketplace-virtual-machine-offer.png" alt-text="이 제품이 Azure Marketplace 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 크게 로고
2. 가격
3. 범주
4. 사용 약관
5. 개인 정보 취급 방침 주소 (링크)
6. Offer name
7. Description
8. 유용한 링크
9. 스크린샷/비디오

<br>다음은 Azure Marketplace 검색 결과에 제품 정보가 표시 되는 방법의 예입니다.

:::image type="content" source="media/example-azure-marketplace-virtual-machine-search-results.png" alt-text="이 제품이 Azure Marketplace 검색 결과에 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 작은 로고
2. Offer name
3. 검색 결과 요약
4. 평가판

<br>Azure Marketplace 계획 정보의 예는 다음과 같습니다.

:::image type="content" source="media/example-azure-marketplace-virtual-machine-plan-details.png" alt-text="Azure Marketplace 계획 정보를 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 계획 이름 및 요약
2. 권장 VM 크기
3. 계획 가격 책정

<br>다음은 Azure Portal에서 제공 정보를 표시 하는 방법의 예입니다.

:::image type="content" source="media/example-azure-portal-virtual-machine-offer.png" alt-text="이 제품이 Azure Portal 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. Name
2. Description
3. 유용한 링크
4. 스크린샷/비디오

<br>Azure Portal 검색 결과에 제품 정보가 표시 되는 방법의 예는 다음과 같습니다.

:::image type="content" source="media/example-azure-portal-virtual-machine-search-results.png" alt-text="이 제품이 Azure Portal 검색 결과에 표시 되는 방식을 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 작은 로고
2. Offer name
3. 검색 결과 요약

<br>Azure Portal 계획 정보의 예는 다음과 같습니다.

:::image type="content" source="media/example-azure-portal-virtual-machine-plan-details.png" alt-text="Azure Portal 계획 정보를 보여 줍니다.":::

#### <a name="call-out-descriptions"></a>호출 설명

1. 플랜 이름
2. 플랜 설명

## <a name="next-step"></a>다음 단계

- [상업용 마켓플레이스에서 기존 제품 업데이트](update-existing-offer.md)
