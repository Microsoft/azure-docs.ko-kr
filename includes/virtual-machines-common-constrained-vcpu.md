---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 82cbffb257d85197848b8bca14231e5363d6d45c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729848"
---
SQL Server 또는 Oracle과 같은 일부 데이터베이스 워크로드는 메모리, 저장소 및 I/O 대역폭 요구량은 많지만 코어 수 요구량은 많지 않습니다. 대부분의 데이터베이스 워크로드는 CPU를 많이 사용하지 않습니다. Azure에서는 VM vCPU 수를 제한하여 소프트웨어 라이선스 비용을 줄이면서도 메모리, 저장소 및 I/O 대역폭을 그대로 유지할 수 있는 특정 VM 크기를 제공합니다.

vCPU 수를 원래 VM 크기의 절반이나 1/4로 제한할 수 있습니다. 이러한 새 VM 크기에는 더 쉽게 구분할 수 있도록 활성 vCPU 수를 지정하는 접미사가 붙습니다.

예를 들어 현재 VM 크기 Standard_GS5에는 32개의 vCPU, 448GB의 RAM, 64개 디스크(최대 256TB) 및 80,000 IOPS 또는 2GB/s의 I/O 대역폭이 함께 제공됩니다. 새 VM 크기 Standard_GS5-16 및 Standard_GS5-8에는 각각 16개 및 8개의 활성 vCPU가 함께 제공되고 메모리, 저장소 및 I/O 대역폭 등 나머지 사양은 Standard_GS5과 같습니다.

SQL Server 또는 Oracle에 대해 청구되는 라이선스 요금은 새 vCPU 개수로 제한되며 다른 제품은 새 vCPU 수를 기준으로 청구됩니다. 따라서 활성(청구 가능) vCPU에 대한 VM 사양의 비율이 50~75% 증가합니다. Azure에서만 사용할 수 있는 이 새 VM 크기를 통해 적은 코어당 라이선스 비용으로 CPU 활용도를 높일 수 있습니다. 현재 OS 라이선스를 포함하는 계산 비용은 원래 크기와 동일하게 유지됩니다. 자세한 내용은 [Azure VM sizes for more cost-effective database workloads](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/)(비용 효율적인 데이터베이스 워크로드를 위한 Azure VM 크기)를 참조하세요.


| 이름                | vCPU | 사양           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | M8ms와 동일    |
| Standard_M8-4ms     | 4    | M8ms와 동일    |
| Standard_M16-4ms    | 4    | M16ms와 동일   |
| Standard_M16-8ms    | 8    | M16ms와 동일   |
| Standard_M32-8ms    | 8    | M32ms와 동일   |
| Standard_M32-16ms   | 16   | M32ms와 동일   |
| Standard_M64-32ms   | 32   | M64ms와 동일   |
| Standard_M64-16ms   | 16   | M64ms와 동일   |
| Standard_M128-64ms  | 64   | M128ms와 동일  |
| Standard_M128-32ms  | 32   | M128ms와 동일  |
| Standard_E4-2s_v3   | 2    | E4s_v3과 동일  |
| Standard_E8-4s_v3   | 4    | E8s_v3과 동일  |
| Standard_E8-2s_v3   | 2    | E8s_v3과 동일  |
| Standard_E16-8s_v3  | 8    | E16s_v3와 동일 |
| Standard_E16-4s_v3  | 4    | E16s_v3와 동일 |
| Standard_E32-16_v3  | 16   | E32s_v3와 동일 |
| Standard_E32-8s_v3  | 8    | E32s_v3와 동일 |
| Standard_E64-32s_v3 | 32   | E64s_v3와 동일 |
| Standard_E64-16s_v3 | 16   | E64s_v3와 동일 |
| Standard_GS4-8      | 8    | GS4와 동일     |
| Standard_GS4-4      | 4    | GS4와 동일     |
| Standard_GS5-16     | 16   | GS5와 동일     |
| Standard_GS5-8      | 8    | GS5와 동일     |
| Standard_DS11-1_v2  | 1    | DS11_v2와 동일 |
| Standard_DS12-2_v2  | 2    | DS12_v2와 동일 |
| Standard_DS12-1_v2  | 1    | DS12_v2와 동일 |
| Standard_DS13-4_v2  | 4    | DS13_v2와 동일 |
| Standard_DS13-2_v2  | 2    | DS13_v2와 동일 |
| Standard_DS14-8_v2  | 8    | DS14_v2와 동일 |
| Standard_DS14-4_v2  | 4    | DS14_v2와 동일 |
