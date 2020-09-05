---
title: 포함 파일
description: 포함 파일
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854073"
---
| 리소스                                | 제한        |
|-----------------------------------------|------------------------------|
| VNet 주소 접두사                   | 600 VPN gateway 당          |
| BGP 경로 집계                    | 4000 VPN gateway 당        |
| 로컬 네트워크 게이트웨이 주소 접두사  | 1000-로컬 네트워크 게이트웨이 당               |
| S2S 연결                         | [게이트웨이 SKU에 따라 다릅니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| P2S 연결                         | [게이트웨이 SKU에 따라 다릅니다.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S 경로 제한-IKEv2                 | windows 용 Windows 25 이외의 경우 256 **/**           |
| P2S 경로 제한-OpenVPN               | 1000                         |
| 최대 flows                              | VpnGw2에 대 한 VpnGw1/AZ 512kb의 10만  **/**  -4/az|