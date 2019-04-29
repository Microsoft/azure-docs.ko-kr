---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/02/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 857d29f407c9939143fbb8263be40dadb040efdc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60456462"
---
[!INCLUDE [P2S FAQ All](vpn-gateway-faq-p2s-all-include.md)]

### <a name="is-radius-authentication-supported-on-all-azure-vpn-gateway-skus"></a>모든 Azure VPN Gateway SKU에서 RADIUS 인증이 지원되나요?

RADIUS 인증은 VpnGw1, VpnGw2 및 VpnGw3 SKU에서 사용할 수 있습니다. 레거시 SKU를 사용하는 경우 RADIUS 인증은 표준 및 고성능 SKU에서 사용할 수 있습니다. 기본 게이트웨이 SKU에서 사용할 수 없습니다. 
 
### <a name="is-radius-authentication-supported-for-the-classic-deployment-model"></a>클래식 배포 모델에 RADIUS 인증이 지원되나요?
 
아니요. RADIUS 인증은 클래식 배포 모델에서 사용할 수 없습니다.
 
### <a name="are-3rd-party-radius-servers-supported"></a>타사 RADIUS 서버가 지원되나요?

네, 타사 RADIUS 서버가 지원됩니다.
 
### <a name="what-are-the-connectivity-requirements-to-ensure-that-the-azure-gateway-is-able-to-reach-an-on-premises-radius-server"></a>Azure 게이트웨이가 온-프레미스 RADIUS 서버에 연결할 수 있는지 확인하기 위한 연결 요구 사항은 무엇인가요?

적절한 경로로 구성된 온-프레미스 사이트에 대한 VPN 사이트 간 연결이 필요합니다.  
 
### <a name="can-traffic-to-an-on-premises-radius-server-from-the-azure-vpn-gateway-be-routed-over-an-expressroute-connection"></a>ExpressRoute 연결을 통해 Azure VPN Gateway에서 온-프레미스 RADIUS 서버로 트래픽을 라우팅할 수 있나요?

아니요. 사이트 간 연결을 통해서만 라우팅될 수 있습니다.
 
### <a name="is-there-a-change-in-the-number-of-sstp-connections-supported-with-radius-authentication-what-is-the-maximum-number-of-sstp-and-ikev2-connections-supported"></a>RADIUS 인증으로 지원되는 SSTP 연결 수가 변경되었나요? 지원되는 SSTP 및 IKEv2 연결의 최대 수는 무엇인가요?

RADIUS 인증을 사용하여 게이트웨이에서 지원되는 SSTP 연결의 최대 수는 변경되지 않았습니다. SSTP의 경우 128개가 남지만, IKEv2의 경우 게이트웨이 SKU에 따라 달라집니다. 지원되는 연결 수에 대한 자세한 내용은 [게이트웨이 SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)를 참조하세요.
 
### <a name="what-is-the-difference-between-doing-certificate-authentication-using-a-radius-server-vs-using-azure-native-certificate-authentication-by-uploading-a-trustedcertificate-to-azure"></a>(신뢰할 수 있는 인증서를 Azure에 업로드하여) RADIUS 서버 또는 Azure 네이티브 인증서 인증을 사용하여 인증서 인증을 수행하는 작업의 차이점은 무엇인가요?

RADIUS 인증서 인증에서 인증 요청은 실제 인증서 유효성 검사를 처리하는 RADIUS 서버로 전달됩니다. 이 옵션은 이미 RADIUS를 통해 인증서 인증 인프라와 통합하려는 경우에 유용합니다.
  
인증서 인증에 Azure를 사용하는 경우 Azure VPN Gateway는 인증서의 유효성 검사를 수행합니다. 게이트웨이에 인증서 공개 키를 업로드해야 합니다. 연결하도록 허용되지 않는 해지된 인증서 목록을 지정할 수도 있습니다.

### <a name="does-radius-authentication-work-with-both-ikev2-and-sstp-vpn"></a>RADIUS 인증은 IKEv2 및 SSTP VPN 모두에서 작동합니까?

예, RADIUS 인증은 IKEv2 및 SSTP VPN 모두에서 지원됩니다. 
