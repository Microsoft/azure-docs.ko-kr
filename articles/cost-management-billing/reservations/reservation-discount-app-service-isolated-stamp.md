---
title: Azure App Service에 대한 예약 할인
description: Azure App Service 격리 스탬프에 예약 할인이 적용되는 방법을 알아봅니다. 할인은 해당 지역의 인지세에 자동으로 적용됩니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: f7d3134652d75cb4ce40b3e9366d3083f5d760df
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682623"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Azure App Service 격리 스탬프에 예약 할인이 적용되는 방법

App Service 격리 인지세 예약 용량을 구입하면, 지역의 인지세에 예약 할인이 자동으로 적용됩니다. 예약 할인은 격리 인지세 미터에서 내보낸 사용량에 적용됩니다. 작업자, 추가 프런트 엔드 및 스탬프와 연결된 기타 리소스는 일반 요금으로 계속 청구됩니다.

## <a name="reservation-discount-application"></a>예약 할인 애플리케이션

App Service 격리 인지세 할인은 시간 단위로 격리 스탬프를 실행하는 데 적용됩니다. 한 시간 동안 배포된 스탬프가 없으면 해당 시간 동안 예약된 용량이 낭비됩니다. 이월되지 않습니다.

구매 후에는 구매한 예약이 특정 지역에서 실행되는 격리 스탬프와 일치합니다. 해당 스탬프를 종료하면 예약 할인은 해당 지역에서 실행 중인 다른 스탬프에 자동으로 적용됩니다. 스탬프가 없으면 예약은 해당 지역에서 생성된 다음 스탬프에 적용됩니다.

스탬프가 한 시간 동안 실행되지 않으면, 같은 시간 동안 같은 지역에 있는 일치하는 다른 스탬프에 예약이 자동으로 적용됩니다.

## <a name="choose-a-stamp-type---windows-or-linux"></a>스탬프 유형 선택 - Windows 또는 Linux

비어 있는 격리 스탬프는 기본적으로 Windows 스탬프 미터를 내보냅니다. 예를 들어 작업자가 배포되지 않은 경우, Windows 작업자가 배포될 때 계속해서 미터를 내보냅니다. Linux 작업자를 배포하는 경우 미터가 Linux 스탬프 미터로 변경됩니다. Linux 및 Windows 작업자가 모두 배포되면 스탬프가 Windows 미터를 내보냅니다.

그 결과, 스탬프 미터는 스탬프 수명 기간 동안 Windows와 Linux 사이에서 변경될 수 있습니다. 한편, 예약은 운영 체제마다 다릅니다. 스탬프에 배포할 작업자를 지원하는 예약을 구매해야 합니다. Windows 전용 스탬프와 혼합 스탬프는 Windows 예약을 사용합니다. Linux 작업자만 있는 스탬프는 Linux 예약을 사용합니다.

Linux 예약을 구매해야 하는 유일한 경우는 스탬프에 Linux 작업자_만_ 포함하려는 경우입니다.

## <a name="discount-examples"></a>할인 예제

다음 예제에서는 배포에 따라 격리 인지세 예약 인스턴스 할인이 적용되는 방법을 보여줍니다.

- **예제 1**: App Service 격리 스탬프가 없는 지역에서 격리된 예약 스탬프 용량 인스턴스를 하나 구매합니다. 지역에 새 스탬프를 배포하고 이 스탬프에 예약된 요금을 지불합니다.
- **예제 2**: App Service 격리 스탬프가 이미 배포된 지역에서 격리된 예약 스탬프 용량 인스턴스를 하나 구매합니다. 배포된 스탬프에 예약된 요금을 받기 시작합니다.
- **예제 3**: App Service 격리 스탬프가 이미 배포된 지역에서 격리된 예약 스탬프 용량 인스턴스를 하나 구매합니다. 배포된 스탬프에 대해 예약된 요금을 받기 시작합니다. 나중에 스탬프를 삭제하고 새 스탬프를 배포합니다. 새 스탬프에 예약된 요금을 받습니다. 배포된 스탬프가 없는 기간 동안 할인은 이월되지 않습니다.
- **예제 4**: 한 지역에서 격리된 Linux 예약 스탬프 용량 인스턴스를 하나 구입한 다음, 이 지역에 새 스탬프를 배포합니다. 스탬프가 작업자 없이 처음 배포되면 Windows 스탬프 미터를 내보냅니다. 할인을 받지 못합니다. 첫 번째 Linux 작업자가 스탬프를 배포하면 Linux 스탬프 미터를 내보내고 예약 할인이 적용됩니다. Windows 작업자가 나중에 스탬프에 배포되면 스탬프 미터가 Windows로 되돌아갑니다. 격리된 Linux 예약 스탬프 예약에 대해 더 이상 할인을 받지 않습니다.

## <a name="next-steps"></a>다음 단계

- 예약을 관리하는 방법을 알아보려면 [Azure Reservations 관리](manage-reserved-vm-instance.md)를 참조하세요.
- 비용 절감을 위해 사전 구매 App Service 격리 스탬프 예약 용량에 대해 자세히 알아보려면 [예약된 용량으로 Azure App Service 격리 인지세를 선불로 결제](prepay-app-service-isolated-stamp.md)를 참조하세요.
- Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.
  - [Azure 예약이란?](save-compute-costs-reservations.md)
  - [Azure에서 Reservations 관리](manage-reserved-vm-instance.md)
  - [종량제 요금을 사용하는 구독에 대한 예약 사용량 이해](understand-reserved-instance-usage.md)
  - [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
