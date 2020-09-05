---
title: Azure에 대한 예약 소프트웨어 비용
description: Azure Reserve VM Instances 비용에 포함되지 않는 소프트웨어 미터에 대해 알아봅니다.
author: yashesvi
ms.reviewer: yashar
tags: billing
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: e302a8459d3092a5543efda7494c68d6660df39d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690819"
---
# <a name="software-costs-not-included-with-azure-reserved-vm-instances"></a>Azure Reserved VM Instances에 포함되지 않는 Windows 소프트웨어 비용

가상 머신 예약 인스턴스 및 SQL 예약 용량 할인은 소프트웨어 비용이 아닌 인프라 비용에만 적용됩니다. Windows VM을 사용하고 예약 가상 머신 인스턴스에 Azure Hybrid Benefit이 없는 경우, 다음 섹션에 나열된 소프트웨어 미터에 대한 요금이 청구됩니다. SQL PaaS 배포의 경우 Azure Hybrid Benefit을 선택하지 않을 때 별도의 미터를 사용하여 IP 비용이 계속 청구됩니다.

## <a name="windows-software-meters-not-included-in-reservation-cost"></a>Windows 소프트웨어 요금제는 예약 요금에 포함되지 않습니다.

| MeterId | 사용량 파일의 MeterName | VM별 사용 |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Reservation-Windows Svr Burst(1 코어) | B 시리즈 |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Reservation-Windows Svr Burst(2 코어) | B 시리즈 |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Reservation-Windows Svr Burst(4 코어) | B 시리즈 |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Reservation-Windows Svr Burst(8 코어) | B 시리즈 |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Reservation-Windows Svr(1 코어) | B 시리즈를 제외한 모두 |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Reservation-Windows Svr(2 코어) | B 시리즈를 제외한 모두 |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Reservation-Windows Svr(4 코어) | B 시리즈를 제외한 모두 |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Reservation-Windows Svr(6 코어) | B 시리즈를 제외한 모두 |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Reservation-Windows Svr(8 코어) | B 시리즈를 제외한 모두 |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Reservation-Windows Svr(12 코어) | B 시리즈를 제외한 모두 |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Reservation-Windows Svr(16 코어) | B 시리즈를 제외한 모두 |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Reservation-Windows Svr(20 코어) | B 시리즈를 제외한 모두 |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Reservation-Windows Svr(24 코어) | B 시리즈를 제외한 모두 |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Reservation-Windows Svr(32 코어) | B 시리즈를 제외한 모두 |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Reservation-Windows Svr(40 코어) | B 시리즈를 제외한 모두 |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Reservation-Windows Svr(64 코어) | B 시리즈를 제외한 모두 |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Reservation-Windows Svr(72 코어) | B 시리즈를 제외한 모두 |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Reservation-Windows Svr(128 코어) | B 시리즈를 제외한 모두 |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Reservation-Windows Svr(256 코어) | B 시리즈를 제외한 모두 |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Reservation-Windows Svr(96 코어) | B 시리즈를 제외한 모두 |

## <a name="cloud-services-software-meters-not-included-in-reservation-cost"></a>클라우드 서비스 소프트웨어 미터는 예약 비용에 포함되지 않음

| MeterId | 사용량 파일의 MeterName |
| ------- | ------------------------|
|ac9d47ff-ff68-4afc-a145-0c321cf8d0d5|Cloud Services 1 vCPU 라이선스|
|e0434559-19ee-4132-9c46-05ad4044f3f7|Cloud Services 2 vCPU 라이선스|
|6ecc834e-39b3-48b3-8d10-cc5626bacb66|Cloud Services 4 vCPU 라이선스|
|13103090-ca72-4825-ab12-7f16c4931d95|Cloud Services 8 vCPU 라이선스|
|ecd2bb6e-45a5-49aa-a58b-3947ba21c364|Cloud Services 16 vCPU 라이선스|
|de2c7f1d-06dc-4b16-bc8b-c2ec5f4c8aee|Cloud Services 20 vCPU 라이선스|
|ca1af837-4b35-47f5-8d14-b1988149c4ca|Cloud Services 32 vCPU 라이선스|
|dc72ee45-2ab7-4698-b435-e2cf10d1f9f6|Cloud Services 64 vCPU 라이선스|
|7a803026-244c-4659-834c-11e6b2d6b76f|Cloud Services 80 vCPU 라이선스|

## <a name="get-rates-for-azure-meters"></a>Azure 미터에 대한 요금 가져오기

Azure RateCard API를 통해 이러한 미터 각각의 비용을 알아볼 수 있습니다. Azure 미터에 대한 요금을 알아보는 방법에 대한 정보는 [Azure 구독에서 사용되는 리소스에 대한 가격 및 메타데이터 정보 가져오기](/previous-versions/azure/reference/mt219004(v=azure.100))를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure용 예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure용 예약 관리](manage-reserved-vm-instance.md)
- [예약 할인이 적용되는 방식 이해](../manage/understand-vm-reservation-charges.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
