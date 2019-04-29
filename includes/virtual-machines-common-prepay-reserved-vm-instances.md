---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/13/2019
ms.openlocfilehash: d9b9aae8bea323e5aac74a2e317b82d4cb43568f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406885"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제

가상 머신에 대해 선불 결제하고 Azure Reserved VM(Virtual Machine) Instances를 사용하여 비용을 절감합니다. 자세한 내용은 [Azure Reserved VM Instances 제품](https://azure.microsoft.com/pricing/reserved-vm-instances/)을 참조하세요.

[Azure Portal](https://portal.azure.com)에서 예약 VM 인스턴스를 구입할 수 있습니다. 인스턴스를 구입하려면:

- 사용자가 하나 이상의 Enterprise 또는 종량제 구독에 대해 소유자 역할이어야 합니다.
- Enterprise 구독의 경우 [EA 포털](https://ea.azure.com)에서 **예약 인스턴스 추가**를 활성화해야 합니다. 이 설정을 비활성화하려면 구독의 EA 관리자여야 합니다.
- CSP(클라우드 솔루션 공급자) 프로그램의 경우 관리자 에이전트 또는 판매 에이전트는 예약 구매를 할 수 있습니다.

예약 할인은 예약 범위 및 특성과 일치하는 실행 중인 가상 머신 수에 자동으로 적용됩니다. [Azure Portal](https://portal.azure.com), PowerShell, CLI 또는 API를 통해 예약의 범위를 업데이트할 수 있습니다.

## <a name="determine-the-right-vm-size-before-you-buy"></a>구매하기 전에 적절한 VM 크기를 결정합니다.

예약을 구입 하기 전에 해야 하는 VM의 크기를 결정 해야 합니다. 다음 섹션에서는 적절 한 VM 크기를 결정 하는 데 도움이 됩니다.

### <a name="use-reservation-recommendations"></a>사용 하 여 예약 권장 사항

예약 권장 사항 구입 해야 하는 예약을 결정 하는 데 사용할 수 있습니다.

- 구매 권장 사항 및 권장 되는 수량을 구입한 경우 Azure portal에서 VM 예약된 인스턴스를 표시 합니다.
- Azure Advisor는 개별 구독에 대 한 구매 권장 사항을 제공합니다.  
- 공유 범위와 단일 구독 범위에 대 한 구매 권장 사항을 가져오려면 Api를 사용할 수 있습니다. 자세한 내용은 [예약 인스턴스 구매 권장 사항 Api 기업 고객을 위한](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)합니다.
- EA 고객의 경우에 대 한 권장 사항을 공유에 대 한 구매 및 단일 구독 범위에서 사용할 수는 [Azure 소비 Insights Power BI 콘텐츠 팩](/power-bi/service-connect-to-azure-consumption-insights)합니다.

### <a name="classic-vms-and-cloud-services"></a>클래식 Vm 및 cloud services

가상 머신 예약 인스턴스는 자동으로 모두 클래식 Vm에 적용 하 고 클라우드 서비스 인스턴스 크기 유연성을 사용 하는 경우. 클래식 Vm 또는 cloud services에 대 한 모든 특수 Sku 없습니다. 동일한 VM Sku에 적용 합니다.

예를 들어, 클래식 Vm 또는 클라우드 서비스를 Azure Resource Manager 기반 Vm을 변환할 수 있습니다. 이 예제에서는 예약 할인 일치 Vm에 자동으로 적용 됩니다. 하지 않아도 됩니다 *exchange* 기존 예약 된 인스턴스-자동으로 적용 됩니다.

### <a name="analyze-your-usage-information"></a>사용 정보를 분석 합니다.
구입 해야 하는 예약을 결정 하는 데 사용 정보를 분석 해야 합니다.

사용 현황 데이터 사용 현황 파일 및 Api에서 제공 됩니다. 사용 하 여 함께 구매 하는 예약 확인. 높은 사용량 매일 예약 구매 수량을 결정할 수 있는 VM 인스턴스를 확인 해야 합니다.

방지 합니다 `Meter` subcategory 및 `Product` 사용 현황 데이터의 필드입니다. Premium storage를 사용 하는 VM 크기를 구분 하지 있습니다. 이러한 필드를 사용 하 여 예약 구매에 대 한 VM 크기를 결정 하는 경우 잘못 된 크기를 구입할 수 있습니다. 그런 다음 원하는 예약 할인을 얻을 수 없습니다. 대신 참조는 `AdditionalInfo` 사용 현황 파일에 올바른 VM 크기를 결정 하는 사용량 API 필드입니다.

### <a name="purchase-restriction-considerations"></a>구매 제한 고려 사항

Reserved VM Instances는 몇 가지 예외를 사용 하 여 대부분의 VM 크기에 대해 사용할 수 있습니다. 예약 할인은 같은 Vm에 대 한 적용 되지 않습니다.

- **VM 시리즈** -A 시리즈, Av2 시리즈 또는 G 시리즈입니다.

- **미리 보기에서 Vm** -모든 VM 시리즈 또는 크기는 미리 보기 상태입니다.

- **클라우드** -예약 구매 독일 또는 중국 지역에서 사용할 수 없습니다.

- **할당량이 부족** -단일 구독에 범위가 지정 된 예약 vCPU 할당량을 새 RI에 대 한 구독에서 사용할 수 있어야 합니다. 예를 들어 대상 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU인 경우 11개의 Standard_D1 인스턴스에 대해 예약을 구입할 수 없습니다. 예약에 대한 할당량 확인에는 구독에 이미 배포된 VM이 포함됩니다. 예를 들어, 구독에서 D 시리즈에 대한 할당량 한도가 10개 vCPU이고 두 개의 standard_D1 인스턴스가 배포된 경우 이 구독에서 10개의 standard_D1 인스턴스에 대해 예약을 구입할 수 있습니다. 할 수 있습니다 [인용 증가 요청 만들기](../articles/azure-supportability/resource-manager-core-quotas-request.md) 이 문제를 해결 합니다.

- **용량 제한** -드문 경우, Azure 한도 지역의 낮은 용량으로 인해 VM의 하위 집합에 대 한 새로운 예약 구매 크기입니다.

## <a name="buy-a-reserved-vm-instance"></a>예약 VM 인스턴스 구입

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **모든 서비스** > **예약**을 선택합니다.
3. **추가**를 선택하여 새 예약을 구입합니다.
4. 필수 필드를 입력합니다. 사용자가 선택한 특성과 일치하는 VM 인스턴스를 실행하면 예약 할인을 받을 수 있습니다. 할인을 받을 실제 VM 인스턴스 수는 선택한 범위 및 수량에 따라 달라집니다.

    | 필드      | 설명|
    |------------|--------------|
    |name        |이 예약의 이름입니다.|
    |구독|예약에 대해 비용을 지불하는 데 사용하는 구독입니다. 구독 시 지불 방법은 예약에 대해 선불로 비용이 청구됩니다. 구독 유형은 기업계약(제안 번호: MS-AZR-0017P 또는 MS-AZR-0148P) 또는 종량제(제안 번호: MS-AZR-0003P 또는 MS-AZR-0023P)여야 합니다. Enterprise 구독에 대한 요금은 등록의 금액 약정 잔액에서 차감되거나 초과 비용으로 청구됩니다. 종량제 구독에 대한 요금은 신용 카드 또는 구독 시 선택한 청구서 결제 방법으로 청구됩니다.|    
    |범위       |예약 범위에는 하나 또는 여러 개의 구독(공유 범위)이 포함될 수 있습니다. 다음을 선택하는 경우: <ul><li>단일 구독 - 예약 할인이 이 구독의 VM에 적용됩니다. </li><li>공유 - 예약 할인이 청구 컨텍스트 내의 모든 구독에서 실행 중인 VM에 적용됩니다. 기업 고객의 공유 범위는 등록이며 등록 내의 모든 구독을 포함합니다. 종량제 고객의 공유 범위는 계정 관리자가 만든 모든 종량제 구독입니다.</li></ul>|
    |지역    |예약 범위에 해당하는 Azure 지역입니다.|    
    |VM 크기     |VM 인스턴스의 크기입니다.|
    |다음에 맞게 최적화     |VM 인스턴스 크기 유연성은 동일한 [VM 크기 그룹](https://aka.ms/RIVMGroups)의 다른 VM에 예약 할인을 적용합니다. 용량 우선 순위는 배포를 위해 데이터 센터 용량에서 우선됩니다. 그러면 필요할 때 VM 인스턴스를 시작하는 기능을 더욱 신뢰할 수 있습니다. 용량 우선 순위는 예약 범위가 단일 구독일 때에 사용할 수 있습니다. |
    |용어        |1년 또는 3년입니다.|
    |수량    |예약 내에서 구매하는 인스턴스의 수입니다. 수량은 청구 할인을 받을 수 있는 실행 중인 VM 인스턴스의 수입니다. 예를 들어 미국 동부 지역에서 10개의 Standard_D2 VM을 실행 중인 경우 실행 중인 모든 컴퓨터에 대한 혜택을 극대화하려면 수량을 10으로 지정할 수 있습니다. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="change-a-reservation-after-purchase"></a>예약 구매 후 변경

예약 구매 후에 다음과 같은 유형의 변경 내용 만들 수 있습니다.

- 예약 범위를 업데이트 합니다.
- 인스턴스 크기 유연성 (있는 경우)
- 소유권

또한 더 작은 청크 및 이미 예약을 분할 하는 병합에 예약을 분할할 수 있습니다. 변경 내용은 없습니다 새 상업용 트랜잭션이 하거나 예약의 종료 날짜를 변경 합니다.

직접 구매 후 다음과 같은 유형의 변경 내용 만들 수 없습니다.

- 기존 예약을 지역
- SKU
- 수량
- 기간

그러나 수 있습니다 *exchange* 예약을 변경 하려는 경우.

## <a name="cancellations-and-exchanges"></a>취소 및 교환

예약을 취소해야 하는 경우 12%의 조기 종료 수수료가 있을 수 있습니다. 환불은 구매 가격 또는 예약의 현재 가격 중 최저 가격을 기준으로 합니다. 환불은 연간 $50,000로 제한됩니다. 받는 환불은 12%의 조기 종료 수수료를 뺀 나머지 비례 배분한 잔액입니다. 취소를 요청하려면 Azure Portal의 예약으로 이동하고 **환불**을 선택하여 지원 요청을 만듭니다.

예약 VM 인스턴스를 다른 지역, VM 크기 그룹 또는 기간으로 변경해야 하는 경우 같거나 더 큰 값인 다른 예약에 대해 교환할 수 있습니다. 새 예약에 대한 기간 시작일은 교환된 예약에서 수행되지 않습니다. 새 예약을 만들 때 1년 또는 3년의 기간이 시작됩니다. 교환을 요청하려면 Azure Portal의 예약으로 이동하고, **교환**을 선택하여 지원 요청을 만듭니다.

Exchange 또는 환불 예약 하는 방법에 대 한 자세한 내용은 참조 하세요. [예약 교환 및 환불](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md)합니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의하세요.

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](../articles/billing/billing-manage-reserved-vm-instance.md)를 참조하세요.
- Azure Reservations에 대한 자세한 내용은 다음 문서를 참조하세요.
    - [Azure Reservations란?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Azure에서 Reservations 관리](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [예약 할인이 적용되는 방식 이해](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [종량제 구독의 예약 사용량 이해](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [엔터프라이즈 등록에서 예약 사용량 이해](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [예약에 포함되지 않는 Windows 소프트웨어 비용](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [파트너 센터 CSP(클라우드 솔루션 공급자) 프로그램의 Azure 예약](https://docs.microsoft.com/partner-center/azure-reservations)
