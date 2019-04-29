---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60761619"
---
| **공급업체** | **디바이스 패밀리** | **펌웨어 버전** |
| --- | --- | --- |
|시스코 | ISR| IOS 15.1(미리 보기)|
|시스코 | ASA | ASA(*) RouteBased(IKEv2- BGP 없음), 9.8 미만 ASA |
|시스코 | ASA | ASA RouteBased(IKEv2- BGP 없음), 9.8 이상 ASA  |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x RouteBased BGP |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) 필수: NarrowAzureTrafficSelectors(UsePolicyBasedTrafficSelectors 옵션 사용 설정) 및 CustomAzurePolicies(IKE/IPsec)
>
