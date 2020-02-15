---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 3aeb0369ee4a04dbbe89a4d0684b2a6c97378d13
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2020
ms.locfileid: "77123258"
---
Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다.  이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다.  고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.

격리 크기를 사용하면 가상 머신이 특정 서버 인스턴스에서 하나만 실행되도록 보장합니다.  현재 격리 가상 머신 제품에는 다음이 포함됩니다.
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

[여기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)에서 사용 가능한 각 격리 된 크기에 대해 자세히 알아볼 수 있습니다.

## <a name="retiring-d15_v2ds15_v2-isolation-on-may-15-2020"></a>2020 년 5 월 15 일 D15_v2/DS15_v2 격리 사용 중지
**2020 년 2 월 10 일 업데이트: "격리" 사용 중지 시간 표시 막대가 5 월 15 2020 일에 연장 되었습니다.**

Azure 전용 호스트는 이제 GA로, 단일 테 넌 트 물리적 서버에서 조직의 Linux 및 Windows 가상 머신을 실행할 수 있습니다. 격리 된 Azure Vm을 Azure 전용 호스트로 완전히 바꿀 예정입니다. **2020 년 5 월 15 일** 이후 D15_v2/DS15_v2 Azure vm은 더 이상 하드웨어로 격리 되지 않습니다.

## <a name="how-does-this-affect-me"></a>이는 어떻게 영향을 미칩니까?
2020 년 5 월 15 일 이후 D15_v2/DS15_v2 Azure virtual machines에 대 한 격리 보장은 더 이상 제공 되지 않습니다. 

## <a name="what-actions-should-i-take"></a>어떤 작업을 수행 해야 하나요?
하드웨어 격리가 필요 하지 않은 경우에는 수행 해야 하는 작업이 없습니다. 

2020 년 5 월 15 일 이전에 격리 해야 하는 경우 다음 중 하나를 수행 해야 합니다.

• 워크 로드를 Azure 전용 호스트로 [마이그레이션합니다](https://azure.microsoft.com/blog/introducing-azure-dedicated-host) .

• D15i_v2에 대 한 [액세스를 요청](https://aka.ms/D15iRequestAccess) 하 고 Azure VM을 DS15i_v2 하 여 동일한 가격의 성능을 얻을 수 있습니다. 이 옵션은 종 량 제 및 1 년 예약 인스턴스 시나리오에만 사용할 수 있습니다.    

• 워크 로드를 다른 Azure 격리 가상 머신으로 [마이그레이션합니다](https://azure.microsoft.com/blog/resize-virtual-machines/) . 

자세한 내용은 다음을 참조 하세요.

## <a name="timeline"></a>타임라인
| Date | 작업 | 
| --- | --- |
| 2019 년 11 월 18 일 | D/DS15i_v2의 가용성 (PAYG, 1 년 RI) |
| 5 월 14 일, 2020  | D/DS15i_v2 1 년 RI의 최근 구매 날짜 | 
| 5 월 15 일, 2020   | D/DS15_v2 격리 보장 제거 | 
| 5 월 15 일, 2021  | D/DS15i_v2 사용 중지 (2019 년 11 월 18 일 이전에 3 년 DS15_v2 RI를 구매한 사용자를 제외한 모든 고객)| 
| 2022 년 11 월 17 일  | 3 년 RIs가 완료 될 때 D/DS15i_v2 사용 중지 (2019 년 11 월 18 일 이전에 3 년 RI의 D/DS15_v2를 구매한 고객의 경우) | 

## <a name="faq"></a>FAQ
### <a name="q-is-the-size-dds15_v2-going-to-get-retired"></a>Q: 크기 D/DS15_v2 사용 중지 될 예정 인가요?
**A**: 아니요, "격리" 기능만 사용 중지 될 예정입니다. 격리가 필요 하지 않은 경우에는 아무 작업도 수행할 필요가 없습니다.

### <a name="q-is-the-size-dds15i_v2-going-to-get-retired"></a>Q: 크기 D/DS15i_v2 사용 중지 될 예정 인가요?
**A**: 예, 크기는 5 월 15 일 (2021) 까지만 사용할 수 있습니다. 2019 년 11 월 18 일 이전에 D/DS15_v2에 3 년 RIs를 구매한 고객의 경우는 2022 17 년 11 월 17 일까 야 D/DS15i_v2에 액세스할 수 있습니다.

### <a name="q-why-am-i-not-seeing-the-new-dds15i_v2-sizes-in-the-portal"></a>Q: 포털에서 새로운 D/DS15i_v2 크기가 표시 되지 않는 이유는 무엇 인가요?
**A**: 현재 d/DS15_v2 고객 이며 새로운 d/DS15i_v2 크기를 사용 하려는 경우이 [폼](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUMkZUWEFPNjFPNVgyMkhZS05FSzlPTzRIOS4u) 을 채웁니다.

### <a name="q-why-i-am-not-seeing-any-quota-for-the-new-dds15i_v2-sizes"></a>Q: 새 D/DS15i_v2 크기에 대 한 할당량이 표시 되지 않는 이유는 무엇 인가요?
**A**: 현재 d/DS15_v2 고객 이며 새로운 d/DS15i_v2 크기를 사용 하려는 경우이 [폼](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0FTPNXHdWpJlO27GE-bHitUNU1XUkhZWkNXQUFMNEJWUk9VWkRRVUJPMy4u) 을 채웁니다.

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q: 다른 격리 된 크기는 언제 사용 중지 될 예정 인가요?
**A**: 크기의 공식 서비스 해제를 사전에 12 개월 전에 미리 알림을 제공 합니다.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q: vm이 격리 되지 않은 하드웨어에 있는 경우 가동 중지 시간이 있나요?
**A**: 격리가 필요 하지 않은 경우 아무 작업도 수행할 필요가 없으며 가동 중지 시간이 표시 되지 않습니다.

### <a name="q-are-there-any-cost-changes-for-moving-to-a-non-isolated-virtual-machine"></a>Q: 격리 되지 않은 가상 머신으로 이동 하기 위한 비용 변화가 있나요?
**A**: 아니요 

### <a name="q-i-already-purchased-1--or-3-year-reserved-instance-for-d15_v2-or-ds15_v2-how-will-the-discount-be-applied-to-my-vm-usage"></a>Q: D15_v2 또는 Ds15_v2에 대해 1 년 또는 3 년 예약 인스턴스를 이미 구입 했습니다. 내 VM 사용에 할인이 적용 되는 방법
**A**: 2019 년 11 월 18 일 이전에 구매한 RIs는 새 격리 된 VM 시리즈에 대 한 검사를 자동으로 확장 합니다. 

| RI |  인스턴스 크기 유연성 | 혜택 자격 |   
| --- | --- | --- |
|   D15_v2  |   꺼짐     |   D15_v2 및 D15i_v2 |    
|   D15_v2  |   설정  |   D15_v2 시리즈 및 D15i_v2는 모두 RI 혜택을 받습니다. |    
|   D14_v2  |   설정  |   D15_v2 시리즈 및 D15i_v2는 모두 RI 혜택을 받습니다. |    
 
Dsv2 시리즈의 경우에도 마찬가지입니다.
 
### <a name="q-i-want-to-purchase-additional-reserved-instances-for-dv2-which-one-should-i-choose"></a>Q: Dv2에 대 한 추가 예약 인스턴스를 구매 하려고 합니다. 어떤 것을 선택 해야 하나요?
**A**: 2019 년 11 월 18 일 이후에 구매한 모든 RIs는 다음 동작을 수행 합니다. 

| RI |  인스턴스 크기 유연성 | 혜택 자격 |   
| --- | --- | --- |
| D15_v2 |  꺼짐 |   D15_v2만  
| D15_v2 |  설정 |    D15_v2 시리즈는 RI 혜택을 받습니다. 새 D15i_v2는이 RI 유형의 RI 혜택에 적합 하지 않습니다. | 
| D15i_v2 |     꺼짐 | D15i_v2만 |  
| D15i_v2 |     설정  | D15i_v2만 | 
 
인스턴스 크기 유연성을 사용 하 여 D2_v2, D4_v2, D15_v2 등의 다른 크기에 적용할 수 없습니다. 마찬가지로 Dsv2 시리즈의 경우입니다.  
 
### <a name="q-can-i-buy-a-new-3-year-ri-for-d15i_v2-and-ds15i_v2"></a>Q: D15i_v2 및 DS15i_v2에 대해 새로운 3 년 RI를 구입할 수 있습니까?
**A**: 아쉽게도 1 년 RI만 새 구매에 사용할 수 있습니다.
 
### <a name="q-can-i-move-my-existing-d15_v2ds15_v2-reserve-instance-to-an-isolated-size-reserved-instance"></a>Q: 기존 D15_v2/DS15_v2 예약 인스턴스를 분리 된 크기의 예약 된 인스턴스로 이동할 수 있나요?
**A**:이 작업은 격리 된 크기와 격리 되지 않은 크기 모두에 적용 되므로이 작업은 필요 하지 않습니다. 그러나 Azure는 기존 D15_v2/DS15_v2 예약 된 인스턴스를 D15i_v2/DS15i_v2로 변경 하는 기능을 지원 합니다. 다른 모든 Dv2/Dsv2 예약 인스턴스의 경우 기존 예약 인스턴스를 사용 하거나 격리 된 크기에 대해 새 예약 인스턴스를 구입 합니다.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q: 실버 또는 골드 내구성 계층에 의존 하는 Azure Service Fabric 고객입니다. 이 변경 내용이 영향을 미칩니까?
**A**: 아니요. 이 변경 후에도 Service Fabric의 [내구성 계층](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-capacity#the-durability-characteristics-of-the-cluster) 에서 제공 하는 보장은 계속 작동 합니다. 다른 이유로 인해 물리적 하드웨어 격리가 필요한 경우에도 위에서 설명한 작업 중 하나를 수행 해야 할 수 있습니다. 
