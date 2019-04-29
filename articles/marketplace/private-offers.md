---
title: 전용 제품 | Azure
description: 앱 및 서비스 게시자용 Azure Marketplace의 전용 제품
services: Azure, Marketplace, Compute
documentationcenter: ''
author: qianw211
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211
ms.openlocfilehash: c3294aaa2b6c88e08028c8958d2ab3a7297aea74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735932"
---
# <a name="private-offers"></a>전용 제품

[Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/)의 전용 제품을 사용하면 게시자가 대상 고객에게만 표시되는 SKU를 만들 수 있습니다.

## <a name="unlock-enterprise-deals-with-private-offers"></a>전용 제품에서 엔터프라이즈 거래 잠금 해제

점점 더 많은 엔터프라이즈 고객이 온라인 마켓플레이스를 사용하여 클라우드 솔루션을 찾고, 사용하고, 구입합니다. 이제 전용 제품에서 게시자는 마켓플레이스를 사용하여 개별적으로 엔터프라이즈가 요구하는 기능을 포함한 사용자 지정 솔루션을 대상 고객과 공유할 수 있습니다.

- *가격 책정 협상*을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- *개별 사용 약관*을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- *특수 구성*을 통해 게시자가 Virtual Machines, Azure 애플리케이션 및 SaaS 앱 제품을 개별 고객의 요구 사항에 맞게 조정할 수 있습니다. 이 옵션을 사용하면 게시자가 보다 광범위하게 모든 고객에게 제공하기 전에 새 제품 기능에 대한 미리 보기 액세스를 제공할 수도 있습니다.

전용 제품을 사용하면 게시자가 사용자 지정 거래 및 구성을 협상하고 제공하는 데 필요한 유연성 및 컨트롤을 포함한 공용 마켓플레이스의 규모 및 글로벌 가용성을 활용할 수 있습니다. 이러한 기능을 통해 클라우드 마켓플레이스를 신속하게 엔터프라이즈에 도입하게 됩니다.  이제 엔터프라이즈는 기대하고 요구하는 방법으로 제품을 구입하고 판매할 수 있습니다.

전용 제품은 Virtual Machine, Azure 애플리케이션(솔루션 템플릿 또는 관리 애플리케이션으로 구현) 및 SaaS 앱에 사용할 수 있습니다. [클라우드 파트너 포털](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus)을 통해 공용 제품과 같은 전용 제품을 만들고 관리할 수 있습니다.  몇 분 안에 전용 제품에 대한 액세스 권한을 고객에게 부여하거나 해제할 수 있습니다.

## <a name="creating-private-offers-using-skus-and-plans"></a>SKU 및 계획을 사용하여 전용 제품 만들기

*공용 SKU 또는 계획을 포함한 새 제품 또는 기존 제품*의 경우 게시자는 새로운 SKU 또는 계획을 만들고 전용으로 표시하여 새로운 개인 변형을 쉽게 만들 수 있습니다.  [개인 SKU](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus) 및 계획은 제품의 구성 요소이며 대상 고객에게만 표시되고 구입 가능합니다. 개인 SKU 및 계획은 기본 이미지를 다시 사용하거나 공용 SKU 또는 계획에 이미 게시된 메타데이터를 제공할 수 있습니다. 이 옵션을 통해 게시자가 동일한 기본 이미지의 여러 버전을 게시하고 메타데이터를 제공하지 않고 공용 제품의 여러 개인 변형을 만들 수 있습니다. Virtual Machine 및 Azure 애플리케이션 전용 제품의 경우 개인 SKU가 공용 SKU가 포함된 기본 이미지를 공유하는 경우 제품의 기본 이미지를 변경하면 해당 기본 이미지를 사용하는 모든 공용 및 개인 SKU에 전파됩니다.

*개인 SKU 또는 계획을 포함하는 새 제품*의 경우 게시자는 해당 제품을 다른 제품으로 만든 다음, SKU 또는 계획을 전용으로 표시할 수 있습니다. 개인 SKU 또는 계획만 설치된 제품은 제품과 연결되지 않은 고객에 의해 [Azure Marketplace](https://azuremarketplace.microsoft.com) 또는 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)을 통해 검색되거나 액세스될 수 없습니다.

## <a name="targeting-customers-with-private-offers"></a>전용 제품을 사용하여 고객 대상 지정
새로운 전용 제품 및 기존 전용 제품의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. Virtual Machine 또는 Azure 애플리케이션 제품을 사용하는 게시자는 개별 Azure 구독 ID로 개인 SKU의 가용성을 제한하거나 최대 20,000개의 Azure 구독 ID의 CSV를 업로드할 수 있습니다. SaaS 앱 전용 제품을 사용하는 동안 게시자는 수동 또는 CSV 업로드 방법을 사용하여 전용 계획의 가용성을 제한하는 Azure 구독 ID 또는 테넌트 ID를 연결할 수 있습니다.

제품이 인증되고 게시되면 고객은 동기화 개인 구독 기능을 사용하여 몇 분 내에 SKU 또는 계획에서 업데이트되거나 제거될 수 있습니다. 이 기능을 사용하면 게시자가 제품을 다시 인증하거나 다시 게시하지 않고 개인 SKU 또는 계획을 나타내도록 고객의 목록을 쉽고 빠르게 업데이트할 수 있습니다.

## <a name="deploying-private-offers"></a>전용 제품 배포

전용 제품은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/)을 통해서만 검색될 수 있고 [Azure Marketplace](https://azuremarketplace.microsoft.com)를. Azure Portal에 로그인하면 고객은 전용 제품에 액세스할 Marketplace 검색 요소를 선택할 수 있습니다. 전용 제품은 검색 결과에 나타나고 다른 제품과 같이 명령줄 및 Azure Resource Manager 템플릿을 통해 배포될 수 있습니다.

![[전용 제품]](./media/marketplace-publishers-guide/private-offer.png)

또한 전용 제품은 검색 결과에 나타납니다. "전용" 배지만 조심하세요.

> [!Note]
> 클라우드 솔루션 공급자 (CSP) 프로그램의 대리점을 통해 설정 된 구독을 사용 하 여 전용 제품 지원 되지 않습니다.

## <a name="next-steps"></a>다음 단계

이러한 새 기능을 활용하려는 경우 [Azure Marketplace](https://azuremarketplace.microsoft.com/sell)에서 판매하기 시작할 수 있습니다.
