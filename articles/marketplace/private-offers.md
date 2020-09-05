---
title: Microsoft 상업적 marketplace의 개인 제품
description: 앱 및 서비스 게시자를 위한 Microsoft 상업적 marketplace의 개인 제품입니다.
ms.subservice: partnercenter-marketplace-publisher
ms.service: marketplace
ms.topic: article
author: vikrambmsft
ms.author: vikramb
ms.date: 07/01/2020
ms.openlocfilehash: 2808f0d3b99257d52b2d400424f256f980005863
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607197"
---
# <a name="private-offers-in-the-microsoft-commercial-marketplace"></a>Microsoft 상업적 marketplace의 개인 제품

의 개인 제공 [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) 게시자는 대상 고객 에게만 표시 되는 계획을 만들 수 있습니다. 이 문서에서는 개인 제품의 옵션 및 이점에 대해 설명 합니다.

## <a name="unlock-enterprise-deals-with-private-offers"></a>Enterprise에서 개인 제품으로 거래

점점 더 많은 엔터프라이즈 고객이 온라인 마켓플레이스를 사용하여 클라우드 솔루션을 찾고, 사용하고, 구입합니다. 이제 프라이빗 제품에서 게시자는 마켓플레이스를 사용하여 개별적으로 엔터프라이즈가 요구하는 기능을 포함한 사용자 지정 솔루션을 대상 고객과 공유할 수 있습니다.

- *가격 책정 협상*을 통해 게시자가 할인을 확장하고 공개적으로 사용 가능한 제품의 가격 책정을 목록에서 지울 수 있습니다.
- *프라이빗 사용 약관*을 사용하면 게시자가 사용 약관을 특정 고객에게 맞게 조정할 수 있습니다.
- *특수 구성*을 통해 게시자가 Virtual Machines, Azure 애플리케이션 및 SaaS 앱 제품을 개별 고객의 요구 사항에 맞게 조정할 수 있습니다. 이 옵션을 사용하면 게시자가 보다 광범위하게 모든 고객에게 제공하기 전에 새 제품 기능에 대한 미리 보기 액세스를 제공할 수도 있습니다.

프라이빗 제품을 사용하면 게시자가 사용자 지정 거래 및 구성을 협상하고 제공하는 데 필요한 유연성 및 컨트롤을 포함한 공용 마켓플레이스의 규모 및 글로벌 가용성을 활용할 수 있습니다. 이러한 기능을 통해 클라우드 마켓플레이스를 신속하게 엔터프라이즈에 도입하게 됩니다. 이제 엔터프라이즈는 기대하고 요구하는 방법으로 제품을 구입하고 판매할 수 있습니다.

프라이빗 제품은 Virtual Machine, Azure 애플리케이션(솔루션 템플릿 또는 관리 애플리케이션으로 구현) 및 SaaS 앱에 사용할 수 있습니다.

<!--- Like public offers, private offers can be created and managed via the [Cloud Partner Portal](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md). Customers can be granted or revoked access to private offers in minutes.
--->

## <a name="creating-private-offers-using-plans"></a>요금제를 사용 하 여 개인 제안 만들기

요금제를 사용 하는 *신규 또는 기존*제안의 경우, 게시자는 새 계획 (이전의 sku)을 만들어 개인 변형으로 표시 하 여 새 개인 변형을 쉽게 만들 수 있습니다. 각 제품에는 최대 45 개의 개인 계획이 있을 수 있습니다.

<!--- [Private SKUs](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) --->

비공개 요금제는 제품의 구성 요소 이며 대상 고객 에게만 표시 되 고 구입 가능 됩니다. 비공개 요금제는 공개 계획에 이미 게시 된 기본 이미지 및/또는 제안 메타 데이터를 다시 사용할 수 있습니다. 이 옵션을 통해 게시자가 동일한 기본 이미지의 여러 버전을 게시하고 메타데이터를 제공하지 않고 공용 제품의 여러 프라이빗 변형을 만들 수 있습니다. 가상 컴퓨터 및 Azure 응용 프로그램의 경우에만 비공개 요금제에서 공개 요금제를 사용 하 여 기본 이미지를 공유 하는 경우 제품의 기본 이미지에 대 한 모든 변경 내용이 해당 기본 이미지를 사용 하 여 모든 공용 및 개인 계획에 전파 됩니다.

*비공개 요금제만 포함 된 새 제품*의 경우, 게시자는 해당 제품을 다른 제품으로 만든 다음 해당 계획을 비공개로 표시할 수 있습니다. 비공개 요금제만 있는 제품은 Microsoft 상용 marketplace 또는 제품과 연결 되지 않은 고객의 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 을 통해 검색 되거나 액세스할 수 없게 됩니다.

>[!NOTE]
>비공개 계획만 포함 된 제안은 공용 Azure Marketplace 또는 AppSource에 표시 되지 않습니다.

## <a name="targeting-customers-with-private-offers"></a>비공개 제품을 사용 하 여 고객 대상 지정

새로운 프라이빗 제품 및 기존 전용 제품의 경우 게시자는 구독 식별자를 사용하여 고객을 대상으로 지정할 수 있습니다. 가상 컴퓨터 또는 Azure 애플리케이션 제안을 사용 하는 게시자는 개인 요금제의 가용성을 개별 Azure 구독 ID로 제한 하거나 최대 2만 Azure 구독 id의 CSV를 업로드할 수 있습니다. SaaS 앱 개인 제안을 사용 하는 동안 게시자는 수동 또는 CSV 업로드 방법을 사용 하 여 테 넌 트 ID를 연결 하 여 개인 계획의 가용성을 제한할 수 있습니다.

제품이 인증 되 고 게시 되 면 개인 구독 동기화 기능을 사용 하 여 몇 분 내에 고객이 업데이트 하거나 제거할 수 있습니다. 이 기능을 통해 게시자는 제품을 인증 하거나 게시 하지 않고도 비공개 요금제가 제공 되는 고객의 목록을 빠르고 쉽게 업데이트할 수 있습니다.

## <a name="deploying-private-offers"></a>개인 제품 배포

개인 제공은 [Azure Portal](https://azure.microsoft.com/features/azure-portal/) 통해서만 검색할 수 있으며 [Microsoft AppSource](https://appsource.microsoft.com/) 또는 [Azure Marketplace](https://azuremarketplace.microsoft.com)에는 표시 되지 않습니다. 다른 상업용 marketplace online 스토어에 게시 하는 방법에 대 한 자세한 내용은 [게시 옵션 결정](./determine-your-listing-type.md)을 참조 하세요.

Azure Portal에 로그인 하면 고객이 Marketplace 탐색 요소를 선택 하 여 개인 제안에 액세스할 수 있습니다. 개인 제안은 검색 결과에 표시 되 고 다른 모든 제품 처럼 명령줄 및 Azure Resource Manager 템플릿을 통해 배포할 수 있습니다.

![[프라이빗 제품]](./media/marketplace-publishers-guide/private-offer.png)

또한 프라이빗 제품은 검색 결과에 나타납니다. **비공개** 배지만 찾습니다.

>[!Note]
>CSP (클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정 된 구독은 개인 제공을 지원 하지 않습니다.


<!---
## Next steps

To start using private offers, follow the steps in the [Private SKUs and Plans](./cloud-partner-portal-orig/cloud-partner-portal-azure-private-skus.md) guide.
--->
