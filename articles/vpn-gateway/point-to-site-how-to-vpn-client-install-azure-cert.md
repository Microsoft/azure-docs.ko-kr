---
title: 지점 및 사이트 클라이언트 인증서를 설치 합니다. Azure | Microsoft Docs
description: P2S 인증서 인증용 Windows, Mac, Linux 클라이언트 인증서를 설치합니다.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: c278c1c85961fbeb0779cad98f8ac16d4961ba75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679994"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S 인증서 인증 연결용 클라이언트 인증서 설치

지점 및 사이트 간 Azure 인증서 인증을 사용하여 가상 네트워크에 연결하는 모든 클라이언트에는 클라이언트 인증서가 필요합니다. 이 문서에서는 P2S를 사용하여 VNet에 연결할 때 인증에 사용되는 클라이언트 인증서를 설치합니다.

## <a name="generate"></a>클라이언트 인증서 획득

연결하려는 클라이언트 운영 체제에 관계없이 항상 클라이언트 인증서가 있어야 합니다. 엔터프라이즈 CA 솔루션을 사용하여 생성된 루트 인증서 또는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성할 수 있습니다. 클라이언트 인증서를 생성하는 단계에 대해 알아보려면 [PowerShell](vpn-gateway-certificates-point-to-site.md), [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) 또는 [Linux](vpn-gateway-certificates-point-to-site-linux.md)지침을 참조하세요. 

## <a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN 클라이언트는 Resource Manager 배포 모델에서만 지원됩니다. 클래식 배포 모델에서는 지원되지 않습니다.
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="installlinux"></a>Linux

Linux 클라이언트 인증서는 클라이언트 구성의 일부로 클라이언트에 설치됩니다. [클라이언트 구성 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli) 지침을 참조하세요.

## <a name="next-steps"></a>다음 단계

[VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)에 대한 지점 및 사이트 간 구성 단계를 계속합니다.