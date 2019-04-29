---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 05820cc5f7b7d61d83f73ea5b62b05f8712e0997
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771217"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Reserved VM Instances에서 가상 머신 크기 유연성

인스턴스 크기 유연성에 최적화된 예약 가상 머신 인스턴스에서 사용자가 구입한 예약은 동일한 크기 시리즈 그룹에서 VM(가상 머신) 크기에 적용할 수 있습니다. 예를 들어 Standard_DS5_v2와 같은 DSv2 시리즈 표에 나열된 VM 크기에 대한 예약을 구입하는 경우 예약 할인은 동일한 표에 나와 있는 다른 네 개의 크기에 적용할 수 있습니다.

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

하지만 DSv2 시리즈 상위 메모리 테이블(예: Standard_DS11_v2, Standard_DS12_v2 등)의 VM 크기와 같이, 다른 테이블에 나열된 VM 크기에는 해당 예약 할인이 적용되지 않습니다.

크기 계열 그룹 내에서 예약 할인을 적용하는 VM 수는 예약을 구입하는 경우 선택한 VM 크기에 따라 달라집니다. 또한 실행하는 VM의 크기에 따라서도 달라집니다. 다음 표에 나열된 비율 열은 해당 그룹의 각 VM 크기에 대한 상대 공간을 비교합니다. 예약 할인을 실행하고 있는 VM에 적용하는 방법을 계산하기 위해 비율 값을 사용합니다.

## <a name="examples"></a>예

다음 예제에서는 DSv2 시리즈 테이블의 크기 및 비율을 사용합니다.

 크기의 비율 또는 해당 계열에서 다른 크기에 비해 상대적 공간은 8 Standard_DS4_v2 예약 VM 인스턴스를 구입 합니다.

- 시나리오 1: 1의 비율로 8대의 Standard_DS1_v2 크기 VM을 실행합니다. 예약 할인은 8대의 모든 해당 VM에 적용됩니다.
- 시나리오 2: 각각 2의 비율로 2대의 Standard_DS2_v2 크기 VM을 실행합니다. 또한 4의 비율로 Standard_DS3_v2 크기 VM을 실행합니다. 총 공간은 2+2+4=8입니다. 따라서 예약 할인은 3대의 모든 해당 VM에 적용됩니다.
- 시나리오 3: 16의 비율로 한 대의 Standard_DS5_v2를 실행합니다. 예약 할인은 해당 VM의 계산 비용 절반에 적용됩니다.

다음 섹션에서는 인스턴스 크기 유연성에 최적화된 예약 VM 인스턴스를 구매하는 경우 동일한 크기 시리즈 그룹에 있는 크기를 보여줍니다.

## <a name="b-series"></a>B 시리즈

| 크기 | 비율|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

자세한 내용은 [B 시리즈 버스트 가능 가상 머신 크기](../articles/virtual-machines/windows/b-series-burstable.md)를 참조하세요.

## <a name="b-series-high-memory"></a>B 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

자세한 내용은 [B 시리즈 버스트 가능 가상 머신 크기](../articles/virtual-machines/windows/b-series-burstable.md)를 참조하세요.

## <a name="d-series"></a>D 시리즈

| 크기 | 비율|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="d-series-high-memory"></a>D 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="ds-series"></a>Ds 시리즈

| 크기 | 비율|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="ds-series-high-memory"></a>Ds 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="dsv2-series"></a>DSv2 시리즈

| 크기 | 비율|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="dsv2-series-high-memory"></a>DSv2 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="dsv3-series"></a>DSv3 시리즈

| 크기 | 비율|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

자세한 내용은 [범용 가상 머신 크기](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1)를 참조하세요.

## <a name="dv2-series"></a>Dv2 시리즈

| 크기 | 비율|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="dv2-series-high-memory"></a>Dv2 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="dv3-series"></a>Dv3 시리즈

| 크기 | 비율|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

자세한 내용은 [범용 가상 머신 크기](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)를 참조하세요.

## <a name="esv3-series"></a>ESv3 시리즈

| 크기 | 비율|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28.8|
|Standard_E64-16s_v3|28.8|
|Standard_E64-32s_v3|28.8|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#esv3-series)를 참조하세요.

## <a name="ev3-series"></a>Ev3 시리즈

| 크기 | 비율|
|---|---|
| Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#ev3-series)를 참조하세요.

## <a name="f-series"></a>F 시리즈

| 크기 | 비율|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="fs-series"></a>FS 시리즈

| 크기 | 비율|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

자세한 내용은 [이전 세대의 가상 머신 크기](../articles/virtual-machines/windows/sizes-previous-gen.md)를 참조하세요.

## <a name="fsv2-series"></a>Fsv2 시리즈

| 크기 | 비율|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

자세한 내용은 [컴퓨팅 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1)를 참조하세요.

## <a name="h-series"></a>H 시리즈

| 크기 | 비율|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

자세한 내용은 [고성능 계산 VM 크기](../articles/virtual-machines/windows/sizes-hpc.md)를 참조하세요.

## <a name="h-series-high-memory"></a>H 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

자세한 내용은 [고성능 계산 VM 크기](../articles/virtual-machines/windows/sizes-hpc.md)를 참조하세요.

## <a name="ls-series"></a>Ls 시리즈

| 크기 | 비율|
|---|---|
| Standard_L4s|1|
|Standard_L8s|2|
|Standard_L16s|4|
|Standard_L32s|8|

자세한 내용은 [저장소 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-storage.md)를 참조하세요.

## <a name="m-series"></a>M 시리즈

| 크기 | 비율|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#m-series)를 참조하세요.

## <a name="m-series-fractional"></a>M 시리즈 소수 자릿수

| 크기 | 비율|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#m-series)를 참조하세요.

## <a name="m-series-fractional-high-memory"></a>M 시리즈 소수 자릿수 상위 메모리

| 크기 | 비율|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#m-series)를 참조하세요.

## <a name="m-series-fractional-large"></a>M 시리즈 큰 소수 자릿수

| 크기 | 비율|
|---|---|
| Standard_M32ls|1|
|Standard_M64ls|2|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#m-series)를 참조하세요.

## <a name="m-series-high-memory"></a>M 시리즈 상위 메모리

| 크기 | 비율|
|---|---|
| Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

자세한 내용은 [메모리 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-memory.md#m-series)를 참조하세요.

## <a name="nc-series"></a>NC 시리즈

| 크기 | 비율|
|---|---|
| Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

자세한 내용은 [GPU 최적화 가상 머신 크기](../articles/virtual-machines/windows/sizes-gpu.md)를 참조하세요.

## <a name="ncv2-series"></a>NCv2 시리즈

| 크기 | 비율|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

자세한 내용은 [GPU 최적화 가상 머신 크기](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series)를 참조하세요.

## <a name="ncv3-series"></a>NCv3 시리즈

| 크기 | 비율|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

자세한 내용은 [GPU 최적화 가상 머신 크기](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series)를 참조하세요.

## <a name="nd-series"></a>ND 시리즈

| 크기 | 비율|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

자세한 내용은 [GPU 최적화 가상 머신 크기](../articles/virtual-machines/windows//sizes-gpu.md#nd-series)를 참조하세요.

## <a name="nv-series"></a>NV 시리즈

| 크기 | 비율|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

자세한 내용은 [GPU 최적화 가상 머신 크기](../articles/virtual-machines/windows//sizes-gpu.md#nv-series)를 참조하세요.


