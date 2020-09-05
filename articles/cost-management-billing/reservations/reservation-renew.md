---
title: Azure 예약 자동 갱신
description: 예약 할인을 계속 받기 위해 Azure 예약을 자동으로 갱신하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bf7945c56fc05c33ae3cfed1c67085b3b28f847f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690513"
---
# <a name="automatically-renew-reservations"></a>예약 자동 갱신

기존 예약이 만료되면 대체를 자동으로 구매하도록 예약을 갱신할 수 있습니다. 자동 갱신은 예약 할인을 계속 받을 수 있는 쉬운 방법을 제공합니다. 또한 예약 만료를 면밀하게 모니터링할 필요가 없습니다. 자동 갱신을 사용하면 수동으로 갱신하지 않아도 되기 때문에 절감 혜택 손실을 방지할 수 있습니다. 갱신 설정은 기본적으로 꺼져 있습니다. 기존 예약이 만료되기 전까지, 언제든지 갱신 설정을 사용하거나 사용하지 않도록 설정할 수 있습니다.

예약을 갱신하면 기존 예약이 만료될 때 새 예약이 생성됩니다. 기존 예약의 기간을 연장하지는 않습니다.

언제든지 자동으로 갱신하도록 옵트인하십시오. 갱신 가격은 기존 예약이 만료되기 30일 전에 제공됩니다. 예약이 만료되기 최소 31일 전에 갱신을 사용하도록 설정하면, 만료 30일 전에 갱신 비용이 기재된 이메일을 받게 됩니다. 예약 가격은 갱신 가격을 고정하는 시간과 갱신 시간 사이에 변경될 수 있습니다. 이런 경우에는 두 비용 중 낮은 비용이 갱신 비용입니다. 예약 수량을 변경할 수 있습니다. 이렇게 하면, 수량 변경 시 설정된 마켓 내 가격을 사용하도록 갱신이 업데이트됩니다.

갱신할 의무는 없으며 기존 예약이 만료되기 전에 언제든지 갱신을 옵트아웃할 수 있습니다.

## <a name="set-up-renewal"></a>갱신 설정

Azure Portal > **Reservations**로 이동합니다.

1. 예약을 선택합니다.
2. **갱신**을 클릭합니다.
3. **만료 시 자동으로 새 예약 구매**를 선택합니다.  
  ![예약 갱신을 보여주는 예](./media/reservation-renew/reservation-renewal.png)

## <a name="if-you-dont-renew"></a>계정하지 않는 경우

서비스는 계속 정상적으로 실행됩니다. 예약이 만료된 후 사용량에 대한 종량제 요금이 청구됩니다.

## <a name="required-renewal-permissions"></a>필요한 갱신 권한

예약을 갱신하려면 다음 조건이 필요합니다.

- 기존 예약의 소유자여야 합니다.
- 예약 범위가 단일 구독 또는 리소스 그룹으로 지정된 경우에는 구독의 소유자여야 합니다.
- 공유 범위가 있으면 구독의 소유자여야 합니다.

## <a name="default-renewal-settings"></a>기본 갱신 설정

기본적으로 갱신은 만료되는 예약의 모든 속성을 상속합니다. 예약 갱신 구매는 SKU, 지역, 범위, 청구 구독, 용어 및 수량이 동일합니다.

하지만, 갱신 예약 구매 수량을 업데이트하여 절감액을 최적화할 수 있습니다.

## <a name="when-the-new-reservation-is-purchased"></a>새로운 예약이 구매되는 시기

기존 예약이 만료되면 새로운 예약이 구매됩니다. 당사는 두 예약 사이에 지연이 발생하지 않도록 노력하고 있습니다. 예약이 연속되어야 비용을 예측할 수 있고 계속 할인을 받을 수 있습니다.

## <a name="changing-parent-reservation-after-setting-renewal"></a>갱신 설정 후 부모 예약 변경

만료되는 예약에 다음과 같은 변경 작업을 수행하면, 예약 갱신이 취소됩니다.

- 분할
- 병합
- 한 계정에서 다른 계정으로 예약 이전
- WebDirect 구독에서 EA(기업계약) 구독 또는 기타 구매 방법으로 예약 이전
- 등록 갱신

새 예약은 갱신 중에 만료되는 예약에서 범위 및 인스턴스 크기 유연성 설정을 상속합니다.

## <a name="new-reservation-permissions"></a>새 예약 권한

Azure는 만료되는 예약에서 새 예약으로 권한을 복사합니다. 또한 예약 구매의 구독 계정 관리자는 새 예약에 액세스할 수 있습니다.

## <a name="potential-renewal-problems"></a>잠재적인 갱신 문제

다음과 같은 경우 Azure에서 갱신이 처리되지 않을 수 있습니다.

- 지불액을 징수할 수 없음
- 갱신하는 동안 시스템 오류 발생
- 만료되는 SKU가 갱신 중 활성 상태가 아님
- EA는 다른 EA로 갱신됨

위의 조건 중 하나라도 발생하고 갱신이 비활성화되면 이메일 알림이 전송됩니다.

## <a name="renewal-notification"></a>갱신 알림

이메일은 구매 방법에 따라 다른 사람에게 전송됩니다.

- EA 고객 - EA 포털에 설정된 알림 연락처로 이메일이 전송됩니다.
- 종량제 요금을 사용하는 개별 구독 고객 - 계정 관리자로 설정된 사용자에게 이메일이 전송됩니다.
- 클라우드 솔루션 공급자 고객 - 파트너 알림 담당자에게 이메일이 전송됩니다.

## <a name="next-steps"></a>다음 단계
- Azure Reservations에 대한 자세한 내용은 [Azure Reservations란?](save-compute-costs-reservations.md)을 참조하세요.
