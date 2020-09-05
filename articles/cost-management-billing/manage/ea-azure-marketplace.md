---
title: Azure Marketplace
description: EA 고객이 Azure Marketplace를 사용하는 방법을 설명합니다.
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 91079310d4d566a65c6e8bc7f913ba92d04dece1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88689935"
---
# <a name="azure-marketplace"></a>Azure Marketplace

이 문서에서는 EA 고객 및 파트너가 마켓플레이스 요금을 보고 Azure Marketplace 구매를 사용하도록 설정하는 방법을 설명합니다.

## <a name="azure-marketplace-for-ea-customers"></a>EA 고객용 Azure Marketplace

직접 고객의 경우 Azure Marketplace 요금은 Azure Enterprise Portal에 표시됩니다. Azure Marketplace 구매 및 소비량은 분기별 또는 월간 주기 및 미지급에 대한 Azure 선불 외의 금액으로 청구됩니다.

간접 고객은 Azure Enterprise Portal의 **구독 관리** 페이지에서 Azure Marketplace 구독을 찾을 수 있지만, 가격은 숨겨집니다. 고객은 Azure Marketplace 요금에 대한 자세한 내용을 LSP(라이선스 솔루션 공급자)에 문의해야 합니다.

매월 또는 매년 반복되는 새로운 Azure Marketplace 구매에 대한 전체 요금은 Azure Marketplace 항목을 구매하는 기간 동안 청구됩니다. 이러한 항목은 그 다음 기간의 최초 구매와 동일한 날짜에 자동 갱신됩니다.

매월 반복되는 기존 요금은 각 달력 월의 1일에 계속 갱신됩니다. 연간 요금은 구매 갱신 날짜에 갱신됩니다.

Azure Marketplace에서 사용할 수 있는 일부 타사 재판매인 서비스는 이제 EA(기업계약) Azure 선불 잔액을 사용합니다. 이전에는 이러한 서비스가 EA Azure 선불과 별도로 청구되었으며 별도의 청구서가 발송되었습니다. EA Azure 선불을 Azure Marketplace의 이러한 서비스에 사용하면 고객 구매 및 결제 관리를 간소화할 수 있습니다. Azure 선불을 사용하는 서비스의 전체 목록은 [Azure 웹 사이트에서 2018년 3월 6일 업데이트](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/)를 참조하세요.

### <a name="partners"></a>파트너

LSP는 Azure Enterprise Portal의 가격표 페이지에서 Azure Marketplace 가격표를 다운로드할 수 있습니다. 오른쪽 위에서 **Marketplace 가격표** 링크를 선택합니다. Azure Marketplace 가격표에는 사용 가능한 모든 서비스 및 해당 가격이 표시됩니다.

가격표를 다운로드 하려면 다음을 수행합니다.

1. Azure Enterprise Portal에서 **보고서** > **가격표**로 차례로 이동합니다.
1. 오른쪽 위 모서리에서 사용자 이름 아래에 있는 Azure Marketplace 가격표에 대한 링크를 찾습니다.
1. 마우스 오른쪽 단추로 해당 링크를 클릭하고, **대상을 다른 이름으로 저장**을 선택합니다.
1. **저장** 창에서 문서 제목을 `AzureMarketplacePricelist.zip`으로 변경합니다. 그러면 파일이 .xlsx에서 .zip 파일로 변경됩니다.
1. 다운로드가 완료되면 국가별 가격표가 포함된 zip 파일이 제공됩니다.
1. LSP는 국가별 가격 책정에 대한 개별 국가 파일을 참조해야 합니다. LSP는 **알림** 탭을 사용하여 완전히 새롭거나 사용 중지된 SKU를 인식할 수 있습니다.
1. 가격 변경은 자주 발생하지 않습니다. LSP는 30일 전에 가격 인상 및 FX(외환) 변경에 대한 이메일 알림을 받습니다.
1. LSP는 등록별, ISV별, 분기별로 하나의 청구서를 받습니다.

### <a name="enabling-azure-marketplace-purchases"></a>Azure Marketplace 구매 사용

엔터프라이즈 관리자는 등록된 모든 Azure 구독에 대해 Azure Marketplace 구매를 사용하거나 사용하지 않도록 설정할 수 있습니다. 엔터프라이즈 관리자가 구매를 사용하지 않도록 설정하고 이미 Azure Marketplace 구독이 포함된 Azure 구독이 있는 경우 해당 Azure Marketplace 구독은 취소되거나 영향을 받지 않습니다.

고객이 Azure 구독을 Azure Enterprise Portal의 등록에 직접 연결하여 자신의 직접 Azure 구독을 Azure EA로 변환할 수 있지만, 이 경우 자식 구독을 자동으로 변환하지 않습니다.

Azure Marketplace 구매를 사용하도록 설정하려면 다음을 수행합니다.

1. 엔터프라이즈 관리자 권한으로 Azure Enterprise Portal에 로그인합니다.
1. **관리**로 이동합니다.
1. **등록 세부 정보** 아래에서 **Azure Marketplace** 품목 옆에 있는 연필 아이콘을 선택합니다.
1. **사용/사용 안 함** 또는 무료 **BYOL SKU만**을 적절하게 전환합니다.
1. **저장**을 선택합니다.

> [!NOTE]
> BYOL(사용자 라이선스 필요) 및 무료 전용 옵션은 Azure Marketplace SKU의 구매 및 취득을 BYOL 및 무료 SKU로 제한합니다.

### <a name="services-billed-hourly-for-azure-ea"></a>Azure EA에 대한 시간당 서비스 요금이 청구되는 서비스

MOSP의 월별 요율 대신 기업계약에 따른 시간당 요금이 청구되는 서비스는 다음과 같습니다.

- 애플리케이션 전달 네트워크
- 웹 애플리케이션 방화벽

### <a name="azure-remoteapp"></a>Azure RemoteApp

기업계약을 체결한 경우 기업계약 가격 수준에 따라 Azure RemoteApp에 대한 요금을 지불합니다. 추가 요금이 없습니다. 표준 가격에는 초기 40시간이 포함됩니다. 무제한 가격에는 초기 80시간이 포함됩니다. 80시간을 초과하면 RemoteApp에서 사용량 내보내기를 중지합니다.

## <a name="azure-marketplace-faq"></a>Azure Marketplace FAQ

이 섹션에서는 Azure 선불이 Azure Marketplace의 일부 타사 재판매인 서비스에 적용되는 방법에 대해 설명합니다.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>Azure Marketplace 서비스와 Azure EA 선불로 변경된 사항은 무엇인가요?

2018년 3월 1일부터 일부 타사 재판매인 서비스는 Azure EA 선불을 사용합니다. 이전에는 Azure RI(예약 VM 인스턴스)를 제외한 서비스에 대한 요금이 Azure EA 선불 이외의 금액으로 청구되었으며, 청구서가 별도로 발부되었습니다.

타사 게시 Azure Marketplace 서비스 중 일부를 포함하도록 Azure 선불 사용을 확장했습니다. Azure EA 선불을 Azure Marketplace의 이러한 서비스에 사용하면 구매 및 결제 관리를 간소화할 수 있습니다.

### <a name="why-did-we-make-this-change"></a>왜 이렇게 변경했나요?

고객은 선불 Azure 선불을 활용할 수 있는 추가적인 방법을 지속적으로 찾고 있습니다. 이 변경은 고객이 자주 요청했으며, 대부분의 Azure Marketplace 고객에게 영향을 주었습니다.

### <a name="how-do-you-benefit"></a>혜택을 받으려면 어떻게 하나요?

더 간단한 청구 환경을 받을 수 있고, Azure EA 선불을 더 효율적으로 사용할 수 있습니다. 이러한 서비스는 Azure 선불에 포함되어 있으므로 Azure EA 선불이 더 유용합니다.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Azure EA 선불을 사용하는 Azure Marketplace 서비스는 무엇이며, 어떻게 알 수 있나요?

Azure EA 선불을 사용하는 서비스를 구입하면 Azure Marketplace에 대한 고지 사항이 표시됩니다. Red Hat, SUSE, Autodesk 및 Oracle에서 게시한 일부 서비스가 지원됩니다. 현재 다른 당사자가 게시한 비슷한 이름의 서비스는 Azure 선불에서 공제되지 않습니다. 전체 목록은 이 FAQ의 마지막 부분에 제공됩니다.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>Azure EA 선불이 실행되는 경우는 어떻게 되나요?

모든 Azure 선불을 소진하여 초과분으로 이동하는 경우 이러한 서비스와 관련된 요금은 다른 소비 서비스와 함께 다음 초과분 청구서에 표시됩니다. 2018년 3월 1일 변경 전에 이러한 요금은 다른 Azure Marketplace 서비스에서 청구되었습니다.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>모든 Azure Marketplace에서 Azure EA 선불을 사용하지 않는 이유는 무엇인가요?

Azure EA 선불과 관련하여 최상의 고객 환경을 제공하기 위해 열심히 노력하고 있습니다. 이 변경에 따라 많은 고객과 Azure Marketplace의 총 지출 중 상당 부분이 해결되었습니다. 나중에 다른 서비스가 추가될 수 있습니다.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>간접 등록 및 파트너에는 어떤 영향이 있을까요?

간접 등록 고객 또는 파트너에게는 아무 영향을 주지 않습니다. 이러한 서비스는 다른 사용 서비스와 동일한 파트너 가격 인상 기능을 따릅니다. 요금이 다른 청구서에 표시되고 요금 지불은 고객의 Azure EA 선불에서 나온다는 것이 유일한 변경입니다.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Azure EA 선불을 사용하는 Azure Marketplace 서비스의 목록이 있나요?

특정 Azure Marketplace 제품은 Azure 선불 자금을 사용할 수 있습니다. 이 프로그램에 참여하는 제품의 전체 목록은 [Azure 선불을 사용하는 타사 서비스](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [가격 책정](ea-pricing-overview.md)에 대한 자세한 정보를 참조하세요.