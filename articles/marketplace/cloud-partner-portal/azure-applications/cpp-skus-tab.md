---
title: Azure 애플리케이션 제품용 SKU 구성 | Microsoft Docs
description: Azure 관리 애플리케이션 및 Azure 솔루션 템플릿용 SKU를 구성하는 방법입니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 5d6ec0197699f603c79f414e015cdebcde6b9f60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60745079"
---
# <a name="azure-application-skus-tab"></a>Azure 애플리케이션 SKU 탭

이 문서에는 SKU 탭을 사용하여 Azure 애플리케이션용 SKU를 만드는 방법을 설명합니다. 

> [!IMPORTANT]
> SKU를 구성하는 단계는 관리되는 애플리케이션 제품과 솔루션 템플릿 제품에서 다릅니다. 이러한 차이는 이 문서에 설명되어 있습니다. 

## <a name="configure-azure-application-skus"></a>Azure 애플리케이션 SKU 구성

### <a name="create-a-new-sku"></a>새 SKU 만들기

새 SKU를 만들려면 다음 단계를 수행합니다.

1. **SKU** 탭을 선택합니다.
2. SKU에서 **+ 새 SKU**를 선택합니다.

    ![새 SKU 프롬프트](./media/azureapp-plus-sku.png)

3. 새 SKU 팝업 창에 **SKU ID**를 입력합니다. 이 ID는 50자로 제한되며 소문자 영숫자 문자, 대시 또는 밑줄만 사용해야 합니다. SKU ID는 대시로 끝날 수 없습니다.
4. SKU ID는 제품 URL, Resource Manager 템플릿(해당되는 경우) 및 청구 보고서에서 고객이 볼 수 있습니다. 제품이 게시된 후에는 이 ID를 수정할 수 없습니다.

### <a name="sku-details-for-a-solution-template"></a>솔루션 템플릿에 대한 SKU 세부 정보

다음 SKU 설정을 제공합니다.

- **제목** - SKU의 제목입니다. 이 제목은 이 항목의 갤러리에 나타납니다.
- **요약** - SKU에 대한 짧은 요약 설명입니다. 최대 길이는 100자입니다.
- **설명** - SKU에 대한 자세한 설명입니다.
- **SKU 형식** - "솔루션 템플릿" 및 "관리되는 애플리케이션"과 같은 값을 포함하는 드롭다운 목록입니다. 이 시나리오에서는 **솔루션 템플릿**을 선택합니다.
- **클라우드 가용성** - SKU의 위치입니다. 기본값은 **공용 Azure**입니다.
공용 Azure - 이 가상 머신은 Marketplace가 통합된 모든 공용 Azure 하위 지역의 고객에게 배포할 수 있습니다.
- **Azure Government 클라우드** - 이 가상 머신은 Azure Government 클라우드에 배포됩니다. [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)에 게시하기 전에 게시자가 작업 환경에서 솔루션이 예상한 대로 작동하는지 시험하고 유효성을 검사하는 것이 좋습니다. 준비하고 테스트하려면 [평가판 계정](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)을 요청하세요.

  >[!NOTE] 
  >Microsoft Azure Government는 미국 연방, 주, 지방 또는 부족의 고객 및 이러한 실체에 서비스를 제공할 자격이 있는 파트너에 대한 액세스를 제어할 수 있는 정부 커뮤니티 클라우드입니다.

- **비공개 SKU인가요?** - 이 SKU를 선택한 고객 그룹에서만 사용할 수 있는 경우 예를 선택합니다.

    ![솔루션 템플릿에 대한 SKU 세부 정보 양식](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>관리되는 애플리케이션에 대한 SKU 세부 정보

다음 화면 캡처에는 관리되는 애플리케이션에 대한 SKU 세부 정보 양식이 표시됩니다.

   ![관리되는 애플리케이션에 대한 SKU 세부 정보 양식](./media/azureapp-sku-details-managedapplication.png)

다음 SKU 설정을 구성합니다.

- **제목** - SKU의 제목입니다. 이 제목은 이 항목의 갤러리에 나타납니다.
- **요약** - SKU에 대한 짧은 요약 설명입니다. 최대 길이는 100자입니다.
- **설명** - SKU에 대한 자세한 설명입니다.
- **SKU 형식** - "솔루션 템플릿" 및 "관리되는 애플리케이션"과 같은 값을 포함하는 드롭다운 목록입니다. 이 시나리오에서는 **관리되는 애플리케이션**을 선택합니다.
- **클라우드 가용성** - SKU의 위치입니다. 기본값은 **공용 Azure**입니다.
- **공용 Azure** - 이 가상 머신은 Marketplace가 통합된 모든 공용 Azure 하위 지역의 고객에게 배포할 수 있습니다.
- **Azure Government 클라우드** - 이 가상 머신은 Azure Government 클라우드에 배포됩니다. [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)에 게시하기 전에 게시자가 작업 환경에서 솔루션이 예상한 대로 작동하는지 시험하고 유효성을 검사하는 것이 좋습니다. 준비하고 테스트하려면 [평가판 계정](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/)을 요청하세요.

  >[!NOTE] 
  >Microsoft Azure Government는 미국 연방, 주, 지방 또는 부족의 고객 및 이러한 실체에 서비스를 제공할 자격이 있는 파트너에 대한 액세스를 제어할 수 있는 정부 커뮤니티 클라우드입니다.

- **비공개 SKU인가요?** - 이 SKU를 선택한 고객 그룹에서만 사용할 수 있는 경우 예를 선택합니다.
- **국가/지역 가용성** – **지역 선택**을 사용하여 사용할 수 있는 국가/지역 목록을 표시합니다. 각 국가/지역을 확인하고 **확인**을 선택하여 선택한 내용을 저장합니다. 

   ![국가 및 지역 가용성 목록](./media/azure-app-select-country-region.png)

- **이전 가격 책정** – 월당 USD 단위로 SKU 가격을 입력합니다. 가격은 구성 당시의 환율을 사용하여 현지 통화로 설정합니다. 이러한 설정은 최종적으로 저장되므로 유효한지 검사합니다. 각 국가/지역의 가격을 개별적으로 설정하거나 보려면 가격 책정 스프레드시트를 내보내고 사용자 지정 가격으로 가져오세요.

  >[!NOTE]
  >가격 책정 변경 내용을 저장하여 가격 책정 데이터를 내보내거나 가져올 수 있도록 합니다.

- **간소화된 통화 가격 책정** – 월당 USD 단위로 SKU 가격을 입력합니다. 이전 가격 책정 설정과 동일해야 합니다. 자세한 내용은 [간소화된 통화 가격 책정](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer)을 참조하세요.

### <a name="package-details-for-solution-template"></a>솔루션 템플릿에 대한 패키지 세부 정보

다음 패키지 세부 정보를 제공합니다.

- **버전** - 업로드할 패키지의 버전입니다. 버전 태그는 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다.
- **패키지 파일(.zip)** - 이 패키지는 .zip 파일에 저장된 다음 파일을 포함합니다.
  - MainTemplate.json - 솔루션/응용 프로그램을 배포하고 솔루션에 대해 정의된 리소스를 만드는 데 사용되는 배포 템플릿 파일입니다. 자세한 내용은 [배포 템플릿 파일을 작성하는 방법](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)을 참조하세요.
  - createUIDefinition.json - 이 파일은 Azure Portal에서 이 솔루션/애플리케이션을 프로비전하기 위한 사용자 인터페이스를 생성하는 데 사용됩니다. 자세한 내용은 [관리되는 애플리케이션에 대한 Azure Portal 사용자 인터페이스 만들기](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)를 참조하세요.

  >[!IMPORTANT] 
  >이 패키지는 이 애플리케이션을 프로비전하는 데 필요한 중첩된 템플릿 또는 스크립트를 포함해야 합니다. MainTemplate.json 파일 및 createUIDefinition.json 파일은 루트 폴더에 있어야 합니다.

   ![솔루션 템플릿에 대한 패키지 세부 정보](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>관리되는 애플리케이션에 대한 패키지 세부 정보

다음 패키지 세부 정보를 제공합니다.

- **버전** - 업로드할 패키지의 버전입니다. 버전 태그는 X.Y.Z 형식이며, X, Y 및 Z는 정수여야 합니다.
- **패키지 파일(.zip)** - 이 패키지는 .zip 파일에 저장된 다음 파일을 포함합니다.
  - applianceMainTemplate.json - 솔루션/애플리케이션을 배포하고 정의된 리소스를 만드는 데 사용되는 배포 템플릿 파일입니다. 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)를 참조하세요. 
  - applianceCreateUIDefinition.json - 이 파일은 Azure Portal에서 이 솔루션/애플리케이션을 프로비전하기 위한 사용자 인터페이스를 생성하는 데 사용됩니다. 자세한 내용은 [관리되는 애플리케이션에 대한 Azure Portal 사용자 인터페이스 만들기](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)를 참조하세요.
  - mainTemplate.json - Microsoft.Solution/appliances 리소스만 포함하는 템플릿 파일입니다. 자세한 내용은 [Azure Resource Manager 템플릿의 구조 및 구문 이해](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)를 참조하세요. <br>
이 리소스의 다음 주요 속성을 기록해 둡니다.
    - "kind" - Marketplace 관리되는 애플리케이션의 경우 값은 "Marketplace"여야 합니다.
    - "ManagedResourceGroupId" - applianceMainTemplate.json에 정의된 모든 리소스가 배포되는 고객 구독에 있는 리소스 그룹입니다.
    - "PublisherPackageId" - 패키지를 고유하게 식별하는 문자열입니다. 이 값은 [publisherId].[OfferId]-preview[SKUID].[PackageVersion]과 같이 연결하는 방식으로 생성해야 합니다.

  >[!IMPORTANT] 
  >이 패키지는 이 애플리케이션을 프로비전하는 데 필요한 중첩된 템플릿 또는 스크립트를 포함해야 합니다. 파일  MainTemplate.json, applianceMainTemplate.json 및 applianceCreateUIDefinition.json은 루트 폴더에 있어야 합니다.

- **테넌트 ID** - 조직의 Azure Active Directory 테넌트 ID입니다.
- **JIT 액세스 사용** – 이 제품을 사용하여 고객 배포에 대한 Just-In-Time 관리 액세스 권한을 사용하도록 설정하려면 **예**를 선택합니다.

  >[!NOTE] 
  >JIT를 사용하는 경우 JIT 액세스를 지원하도록 CreateUiDefinition.json 파일을 업데이트해야 합니다.

   ![관리되는 애플리케이션에 대한 패키지 세부 정보](./media/azureapp-sku-pkgdetails-managedapplication.png)

관리되는 애플리케이션에 대해 권한 부여 및 정책 설정을 구성해야 합니다.

#### <a name="authorization"></a>권한 부여

관리되는 리소스 그룹에 대해 권한을 부여하려는 사용자, 그룹 또는 애플리케이션의 Azure Active Directory 식별자를 추가합니다. 부여된 사용 권한은 역할 정의 ID로 표시됩니다. 소유자, 참가자 또는 사용자 지정 역할 수 있습니다.

#### <a name="policy-settings"></a>정책 설정

관리되는 앱이 준수하는 정책을 추가합니다. Azure 리소스 정책에 대한 자세한 내용은 [Azure Policy란?](../../../governance/policy/overview.md)을 참조하세요.


   ![관리되는 애플리케이션에 대한 권한 부여 및 정책 설정](./media/azureapp-sku-details-managedapp-auth-policy.png)

**새 권한 부여를 만들려면**

1. **권한 부여** 아래에서 **+ 새 권한 부여**를 선택합니다.
2. **보안 주체 ID**에 대해 관리되는 리소스 그룹에 대해 권한을 부여하려는 사용자, 그룹 또는 애플리케이션의 Azure Active Directory 식별자를 추가합니다. 부여된 사용 권한은 역할 정의로 표시됩니다.
3. **역할 정의**에 대해 드롭다운 목록에서 옵션  소유자 또는 참가자 중 하나를 선택합니다. 자세한 내용은 [Azure 리소스에 대한 기본 제공 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)을 참조하세요.

>[!NOTE] 
>여러 권한 부여를 추가할 수 있습니다. 그러나 Active Directory 사용자 그룹을 만들고 "PrincipalId"에 해당 ID를 지정하는 것이 좋습니다. 이렇게 하면 사용자 그룹에 더 많은 사용자를 추가할 수 있으며 SKU를 업데이트하지 않아도 됩니다.

**새 정책을 만들려면**

1. **정책 설정** 아래에서 **+ 새 정책**을 선택합니다.
2. **정책 이름**에 정책의 새 이름을 입력합니다. 이름의 최대 길이는 50자입니다.
3. **정책**에 대해 드롭다운 목록의 옵션 중 하나를 선택합니다. 데이터 공급자가 애플리케이션이 데이터를 사용할 때 사용하도록 설정하려는 정책을 선택합니다. 자세한 내용은 [Azure Policy 샘플](https://docs.microsoft.com/azure/governance/policy/samples/index)을 참조하세요.

    ![관리되는 애플리케이션에 대한 정책 설정](./media/azureapp-sku-policy-settings.png)

4. **정책 SKU**에서 정책 SKU 유형으로 무료 또는 표준을 선택합니다. 표준 SKU는 감사 정책에 필요합니다.

## <a name="next-steps"></a>다음 단계

[Marketplace 탭](./cpp-marketplace-tab.md)
