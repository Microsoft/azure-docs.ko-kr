---
title: 소프트웨어 요금제 할인 - Azure | Microsoft Docs
description: 가상 머신의 소프트웨어에 소프트웨어 요금제 할인이 적용되는 방법을 알아봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: banders
ms.openlocfilehash: 3b936cb15114d81d1dff1eb052a9bafe01adeff7
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681688"
---
# <a name="azure-software-plan-discount"></a>Azure 소프트웨어 요금제 할인

SUSE 및 RedHat에 대한 Azure 소프트웨어 요금제는 배포된 VM에 적용되는 예약입니다. 소프트웨어 요금제 할인은 예약과 일치하는 배포된 VM의 소프트웨어 사용량에 적용됩니다.

VM을 종료하면 사용 가능한 경우 일치하는 다른 VM에 할인이 자동으로 적용됩니다. 소프트웨어 요금제는 VM에서 소프트웨어를 실행하는 비용을 포함합니다. 컴퓨팅, 스토리지 및 네트워킹 등의 기타 요금은 별도로 청구됩니다.

적절한 요금제를 구입하려면 VM 사용량과 해당 VM에 있는 vCPU 수를 이해해야 합니다. 다음 섹션은 사용량 데이터에 따라 구입할 요금제를 식별하는 데 도움이 됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>구매하기 전에 RedHat VM 사용량 검토

사용량 데이터에서 제품 이름을 가져오고 동일한 유형 및 크기의 RedHat 요금제를 구입합니다.

예를 들어 사용량에 제품 **Red Hat Enterprise Linux - 1-4 vCPU VM 라이선스**가 있는 경우 **1-4 vCPU VM**에 대한 **Red Hat Enterprise Linux**를 구매해야 합니다.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>구매하기 전에 SUSE VM 사용량 검토

사용량 데이터에서 제품 이름을 가져오고 동일한 유형 및 크기의 SUSE 요금제를 구입합니다.

예를 들어 사용량이 제품 **SUSE Linux Enterprise Server 우선 순위 - 2-4 vCPU VM 지원**에 대한 것인 경우 **2-4 vCPU**에 대한 **SUSE Linux Enterprise Server 우선 순위**를 구매해야 합니다.

![구매할 제품을 선택하는 예제](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>SUSE 요금제의 다른 VM 크기에 할인이 적용됩니다.

Reserved VM Instances와 같이 SUSE 요금제 구매는 인스턴스 크기 유연성을 제공합니다. 즉, 다른 vCPU 수를 사용하는 VM을 배포할 때도 할인이 적용됩니다. 소프트웨어 요금제 내의 다른 VM 크기에도 할인이 적용됩니다.

할인 금액은 다음 표에 나열된 비율에 따라 달라집니다. 이 비율은 해당 그룹의 각 미터법에 대한 상대적 공간을 비교합니다. 이 비율은 VM vCPU에 따라 달라집니다. SUSE Linux 요금제 할인을 받는 VM 인스턴스 수를 계산할 때 이 비율 값을 사용합니다.

예를 들어, 3~4개의 vCPU가 있는 VM에 대해 SUSE Linux Enterprise Server for HPC 우선 순위용 요금제를 구입하는 경우 해당 예약에 대한 비율은 2입니다. 이 할인은 다음에 대한 SUSE 소프트웨어 비용을 포함합니다.

- 1~2개의 vCPU가 있는 배포된 VM 2개
- 3~4개의 vCPU가 있는 배포된 VM 1개
- 또는 5개 이상의 vCPU가 있는 VM의 0.77 또는 약 77%

5개 이상의 vCPU에 대한 비율은 2.6입니다. 따라서 5개 이상의 vCPU가 있는 SUSE에 대한 예약은 소프트웨어 비용에서 약 77%에 해당하는 일부만 부과합니다.

다음 표에는 예약을 구입할 수 있는 소프트웨어 요금제, 해당 관련 사용량 미터 및 각각의 비율이 나와 있습니다.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC 우선 순위

Azure Portal Marketplace 이름:

- SLES 12 SP3 for HPC(우선 순위)

|SUSE VM | MeterId| 비율| 예제 VM 크기|
| -------| ------------------------| --- |--- |
|SLES for HPC 1-2 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC 3-4 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC 5+ vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Azure Portal Marketplace 이름:

- SLES 12 SP3 for HPC

|SUSE VM | MeterId | 비율|예제 VM 크기|
| ------- | --- | ------------------------| --- |
|SLES for HPC 1-2 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC 3-4 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES for HPC 5+ vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP 우선 순위

Azure Portal Marketplace 이름:

- SLES for SAP 15(우선 순위)
- SLES for SAP 12 SP3(우선 순위)
- SLES for SAP 12 SP2(우선 순위)

|SUSE VM | MeterId | 비율|예제 VM 크기|
| ------- |------------------------| --- | --- |
|SLES for SAP 우선 순위 1-2 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP 우선 순위 3-4 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP 우선 순위 5+ vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Azure Portal Marketplace 이름:

- SLES 15
- SLES 15(표준)
- SLES 12 SP3(표준)

|SUSE VM | MeterId | 비율|예제 VM 크기|
| ------- |------------------------| --- |--- |
|SLES 1-2 코어 vCPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 코어 vCPU |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5+ vCPU |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [Azure 예약에서 SUSE 소프트웨어 요금제에 대한 선불](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure Reservations 관리](manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
