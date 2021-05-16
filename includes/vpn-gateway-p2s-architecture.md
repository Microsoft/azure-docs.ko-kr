---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/30/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e3ad8fcb6cd5cf68b02ac522d0e5ef5a18ba8a3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93145001"
---
지점 및 사이트 간 네이티브 Azure 인증서 인증 연결은 이 연습에서 구성하는 다음 항목을 사용합니다.

* RouteBased VPN 게이트웨이입니다.
* Azure에 업로드된 루트 인증서에 대한 공개 키(.cer 파일)입니다. 인증서가 업로드되면 신뢰할 수 있는 인증서로 간주되며 인증에 사용됩니다.
* 루트 인증서에서 생성된 클라이언트 인증서. 클라이언트 인증서는 VNet에 연결할 각 클라이언트 컴퓨터에 설치됩니다. 클라이언트 인증에 사용됩니다.
* VPN 클라이언트 구성. VPN 클라이언트는 VPN 클라이언트 구성 파일을 사용하여 구성됩니다. 이러한 파일에는 클라이언트를 VNet에 연결하는 데 필요한 정보가 포함됩니다. 파일은 운영 체제에 기본적으로 제공된 기존의 VPN 클라이언트를 구성합니다. 연결되는 각 클라이언트는 구성 파일에서 설정을 사용하여 구성해야 합니다.
