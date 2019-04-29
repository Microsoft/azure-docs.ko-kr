---
title: Microsoft Azure용 가상 머신 필수 조건 | Microsoft Docs
description: Azure Marketplace에 VM 제안을 게시하는 데 필요한 필수 조건에 대한 목록입니다.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844043"
---
# <a name="virtual-machine-prerequisites"></a>가상 머신 필수 조건

이 문서에서는 두 기술 및 비즈니스 요구 하기 전에 충족 해야 하는 VM 제품을 게시할 수는 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/)합니다.  아직 수행 하는 경우 검토 합니다 [가상 머신 제품 게시 가이드](../../marketplace-virtual-machines.md)합니다.


## <a name="technical-requirements"></a>기술적인 요구 사항

VM(가상 머신) 솔루션을 게시하기 위한 기술적인 필수 조건은 간단합니다.

- 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 [Microsoft Azure 사이트](https://azure.microsoft.com)에 등록할 수 있습니다.  
- Windows 또는 Linux VM 개발을 지원하도록 구성된 환경이 있어야 합니다.  자세한 내용은 다음과 같은 관련 VM 설명서 사이트를 참조하세요.
    - [Linux VM 설명서](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Windows VM 설명서](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>비즈니스 요구 사항

비즈니스 요구 사항에는 절차, 계약 및 법적 의무가 포함됩니다. 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- 등록된 Cloud Marketplace 게시자여야 합니다.  아직 등록되지 않은 경우 [Cloud Marketplace 게시자 되기](../../become-publisher.md) 문서의 단계를 수행합니다.

    > [!NOTE]
    > 동일한 Microsoft 개발자 센터 등록 계정을 사용하여 [Cloud 파트너 포털](https://cloudpartner.azure.com)에 로그온해야 합니다.
    > Azure Marketplace 제품에 대해 Microsoft 계정이 하나만 있으면 됩니다. 개별 서비스 또는 제안에만 한정될 필요가 없습니다.
    
- 회사(또는 해당 자회사)는 Azure Marketplace에서 지원하는 판매 국가에 있어야 합니다.  이러한 국가의 최신 목록은 [Microsoft Azure Marketplace 참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)을 참조하세요.
- 제품은 Azure Marketplace에서 지원하는 청구 모델과 호환되는 방식으로 허가를 받아야 합니다.  자세한 내용은 [Azure Marketplace의 청구 옵션](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace)을 참조하세요. 
- 상업적이고 합리적인 방식으로 고객이 이용할 수 있는 기술 지원을 담당합니다. 이러한 지원은 체험, 유료 또는 커뮤니티 접근 방식을 통해 제공될 수 있습니다.
- 소프트웨어 및 타사 소프트웨어 종속성에 대해 사용 허가를 받을 책임이 있습니다.
- Azure Marketplace 및 Azure Portal에 등록할 제안에 대한 조건을 충족하는 콘텐츠를 제공해야 합니다. <!-- TD: Meaning/links? -->
- [Microsoft Azure Marketplace 참가 정책](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) 및 게시자 계약의 약관에 동의해야 합니다.
- 준수 해야 합니다 [Microsoft Azure 웹 사이트 사용 약관](https://azure.microsoft.com/support/legal/website-terms-of-use/)를 [Microsoft 개인정보취급방침](https://privacy.microsoft.com/privacystatement), 및 [Microsoft Azure Certified 프로그램 계약](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/)합니다.


## <a name="next-steps"></a>다음 단계

이러한 필수 구성이 요소를 충족 하면 있습니다 [VM 제품 만들기](./cpp-create-offer.md)합니다.
