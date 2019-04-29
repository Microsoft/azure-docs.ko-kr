---
title: '지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기: Linux: CLI: Azure | Microsoft Docs'
description: Linux(strongSwan) CLI를 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 01/31/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: b673be47d4951adab08f04efc56410095f549356
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766177"
---
# <a name="generate-and-export-certificates"></a>인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 문서에서는 Linux CLI 및 strongSwan을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [Powershell](vpn-gateway-certificates-point-to-site.md) 또는 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 문서를 참조하세요. CLI 대신 GUI를 사용하여 strongSwan을 설치하는 방법에 대한 자세한 내용은 [클라이언트 구성](point-to-site-vpn-client-configuration-azure-cert.md#install) 문서의 단계를 참조하세요.

## <a name="generate-and-export"></a>생성 및 내보내기
[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)에 대한 지점 및 사이트 간 구성을 계속합니다.