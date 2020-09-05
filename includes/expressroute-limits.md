---
title: 포함 파일
description: 포함 파일
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 146a257a9b8f31e79283d71ede1e1c5c0355765c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147821"
---
| 리소스 | 제한 |
| --- | --- |
| 구독당 ExpressRoute 회로 |10 |
| Azure Resource Manager를 사용하는 구독당 지역별 ExpressRoute 회로 |10 |
| ExpressRoute 표준을 사용하는 Azure 개인 피어링에 보급되는 최대 경로 수 |4,000 |
| ExpressRoute 프리미엄 추가 기능을 사용하는 Azure 개인 피어링에 보급되는 최대 경로 수 |10000 |
| ExpressRoute 연결을 위한 VNet 주소 공간의 Azure 개인 피어링에서 보급되는 최대 경로 수 |200 |
| ExpressRoute 표준을 사용하는 Microsoft 피어링에 보급되는 최대 경로 수 |200 |
| ExpressRoute 프리미엄 추가 기능을 사용하는 Microsoft 피어링에 보급되는 최대 경로 수 |200 |
| 표준 성능/ErGw1AZ 게이트웨이에 연결된 최대 ExpressRoute 회로 수 |4 | 
| 고성능/ErGw2AZ 게이트웨이에 연결된 최대 ExpressRoute 회로 수 |8 | 
| 초고성능/ErGw3AZ 게이트웨이에 연결된 최대 ExpressRoute 회로 수 |16 | 
| 서로 다른 피어링 위치의 동일한 가상 네트워크에 연결된 ExpressRoute 회로의 최대 수(모든 SKU) |4 |
| ExpressRoute 회로당 허용되는 가상 네트워크 링크 수 |[ExpressRoute 회로당 가상 네트워크 수](#vnetpercircuit) 표를 참조하세요.  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a> ExpressRoute 회로당 가상 네트워크 수
| **회로 크기** | **표준에 대한 가상 네트워크 링크 수** | **프리미엄 추가 기능을 사용하는 가상 네트워크 링크 수** |
| --- | --- | --- |
| 50Mbps |10 |20 |
| 100Mbps |10 |25 |
| 200Mbps |10 |25 |
| 500Mbps |10 |40 |
| 1Gbps |10 |50 |
| 2Gbps |10 |60 |
| 5Gbps |10 |75 |
| 10Gbps |10 |100 |
| 40Gbps* |10 |100 |
| 100Gbps* |10 |100 |

**100Gbps ExpressRoute Direct만 해당*

> [!NOTE]
> Global Reach 연결은 ExpressRoute 회로당 가상 네트워크 연결 제한에 따라 계산됩니다. 예를 들어 10Gbps 프리미엄 회로는 5개의 Global Reach 연결과 95개의 ExpressRoute 게이트웨이에 대한 연결 또는 95개의 Global Reach 연결과 5개의 ExpressRoute 게이트웨이에 대한 연결 또는 회로에 대해 최대 100개의 연결 제한까지의 기타 조합을 허용합니다.
