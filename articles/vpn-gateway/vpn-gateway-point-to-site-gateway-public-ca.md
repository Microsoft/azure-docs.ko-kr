---
title: P2S 게이트웨이에 대해 공용 CA 인증서로 전환 | Azure VPN Gateway | Microsoft Docs
description: 이 문서는 P2S 게이트웨이에 대해 새 공용 CA 인증서로 전환하는 데 도움이 됩니다.
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: conceptual
origin.date: 03/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.openlocfilehash: 29f2aeee53e07adfeafb8017c489c0b830f24b36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859650"
---
# <a name="transition-to-a-public-ca-gateway-certificate-for-p2s"></a>P2S용 공용 CA 게이트웨이 인증서로 전환

Azure VPN Gateway는 P2S 연결의 게이트웨이에 Azure 수준의 자체 서명된 인증서를 더 이상 발급하지 않습니다. 이제 발급된 인증서가 공용 CA(인증 기관)에 의해 서명됩니다. 그러나 이전 게이트웨이 중 일부는 자체 서명된 인증서를 계속 사용할 수 있습니다. 이러한 자체 서명된 인증서는 만료 날짜가 임박했으며 공용 CA 인증서로 전환해야 합니다.

>[!NOTE]
> * P2S 클라이언트 인증에 사용되는 자체 서명 인증서는 이 Azure 수준의 인증서 변경에 의해 영향을 받지 않습니다. 자체 서명된 인증서를 계속해서 발급하고 정상적으로 사용할 수 있습니다.
>

이 컨텍스트의 인증서는 Azure 수준의 추가 인증서입니다. 인증을 위해 자체 서명된 루트 인증서 및 클라이언트 인증서를 생성할 때 사용하는 인증서 체인이 아닙니다. 이러한 인증서는 영향을 받지 않으며 생성할 때 지정한 날짜에 만료됩니다.

이전에는 게이트웨이에 대한 자체 서명된 인증서(Azure에서 백그라운드로 발급됨)를 18개월마다 업데이트해야 했습니다. 그런 다음, VPN 클라이언트 구성 파일을 생성하여 모든 P2S 클라이언트에 다시 배포해야 했습니다. 공용 CA 인증서로 전환하면 이러한 제한 사항이 제거됩니다. 인증서 전환 외에도 이 변경으로 인해 플랫폼, 메트릭 및 안정성이 향상되었습니다.

이전 게이트웨이만 이 변경의 영향을 받습니다. 게이트웨이 인증서를 전환해야 하는 경우 Azure Portal에서 통신 또는 알림을 받게 됩니다. 이 문서의 단계를 사용하여 게이트웨이가 영향을 받는지 여부를 확인할 수 있습니다.

> [!IMPORTANT]
> 전환은 2019년 3월 12일 18:00 UTC에 시작되도록 예약되었습니다. 다른 기간을 원하는 경우 지원 사례를 만들 수 있습니다. 적어도 24시간 전에 요청을 하고 완료합니다.  다음 기간 중 하나를 요청할 수 있습니다.
>
> * 2월 25일 06:00 UTC
> * 2월 25일 18:00 UTC
> * 3월 1일 06:00 UTC
> * 3월 1일 18:00 UTC
>
> **나머지 게이트웨이는 모두 2019년 3월 12일 18:00 UTC부터 전환됩니다**.
>
> 게이트웨이 전환 프로세스가 완료되면 고객이 메일을 받게 됩니다.
> 

## <a name="1-verify-your-certificate"></a>1. 인증서 확인

### <a name="resource-manager"></a>Resource Manager

1. 이 업데이트의 영향을 받는지 확인합니다. [이 문서](point-to-site-vpn-client-configuration-azure-cert.md)의 단계를 사용하여 현재 VPN 클라이언트 구성을 다운로드합니다.

2. zip 파일을 열거나 추출하고 “일반” 폴더로 이동합니다. 일반 폴더에는 두 개의 파일이 표시되며, 둘 중 하나는 *VPNSettings.xml*입니다.
3. 임의의 xml 뷰어/편집기에서 *VPNSettings.xml*을 엽니다. xml 파일에서 다음 필드를 검색합니다.

   * `<ServerCertRootCn>DigiCert Global Root CA</ServerCertRootCn>`
   * `<ServerCertIssuerCn>DigiCert Global Root CA</ServerCertIssuerCn>`
4. *ServerCertRotCn* 및 *ServerCertIssuerCn*이 “DigiCert Global Root CA”인 경우에는 이 업데이트의 영향을 받지 않으며 이 문서의 단계를 진행할 필요가 없습니다. 그러나 다른 값이 표시되는 경우 게이트웨이 인증서가 업데이트에 포함되며 전환됩니다.

### <a name="classic"></a>클래식

1. 클라이언트 컴퓨터에서 경로로 이동 `%appdata%/Microsoft/Network/Connections/Cm/<gatewayID>`합니다. 게이트웨이 ID 폴더에서 인증서를 볼 수 있습니다.
2. 인증서의 일반 탭에서 발급 기관이 “DigiCert Global Root CA”인지 확인합니다. 이 발급 기관 이외의 CA가 있는 경우 게이트웨이 인증서가 업데이트에 포함되며 전환됩니다.

## <a name="2-check-certificate-transition-schedule"></a>2. 인증서 전환 일정 확인

인증서가 업데이트에 포함된 경우 게이트웨이 인증서가 전환됩니다. 전환 시간은 **중요** 참고 사항을 참조하세요. 업데이트 후에는 P2S 클라이언트가 이전 프로필을 사용하여 연결할 수 없습니다. 새 VPN 클라이언트 프로필을 생성하여 클라이언트에 설치해야 합니다.

## <a name="3-generate-vpn-client-configuration-profile"></a>3. VPN 클라이언트 구성 프로필 생성

인증서가 전환되고 나면, Azure Portal에서 새 VPN 프로필(VPN 클라이언트 구성 파일)을 다운로드합니다. 단계는 [VPN 클라이언트 구성 파일 만들기 및 설치](point-to-site-vpn-client-configuration-azure-cert.md)를 참조하세요. 새 클라이언트 인증서를 생성할 필요가 없습니다.

## <a name="4-deploy-vpn-client-profile"></a>4. VPN 클라이언트 프로필 배포

모든 지점 및 사이트 간 VPN 클라이언트에 새 프로필을 배포합니다. 지점 및 사이트 간 연결에 대한 새 VPN 프로필을 다운로드하여 클라이언트 디바이스에 배포할 때까지 VPN 클라이언트의 연결이 끊어집니다. VPN 클라이언트 컴퓨터에 이미 설치된 클라이언트 인증서는 다시 발급할 필요가 없습니다.

## <a name="5-connect-the-vpn-client"></a>5. VPN 클라이언트 연결

평소대로 VPN 클라이언트에서 Azure에 연결합니다.