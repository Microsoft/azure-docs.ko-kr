---
title: '지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기: MakeCert: Azure | Microsoft Docs'
description: MakeCert를 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
services: vpn-gateway
documentationcenter: na
author: WenJason
ms.service: vpn-gateway
ms.topic: article
origin.date: 09/05/2018
ms.date: 10/01/2018
ms.author: v-jay
ms.openlocfilehash: 973c0aa3bd187e963f15adbe34955d6bc9fa612d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768109"
---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>MakeCert를 사용하여 지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 문서에서는 MakeCert를 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [인증서 - PowerShell](vpn-gateway-certificates-point-to-site.md) 또는 [인증서 - Linux](vpn-gateway-certificates-point-to-site-linux.md)를 참조하세요.

[Windows 10 PowerShell 단계](vpn-gateway-certificates-point-to-site.md)를 사용하여 인증서를 만드는 것이 좋지만 하나의 선택적 방법으로 이러한 MakeCert 지침을 제공합니다. 두 방법 중 하나를 사용하여 생성하는 인증서는 [지원되는 모든 클라이언트 운영 체제](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)에 설치할 수 있습니다. 그러나 MakeCert에는 다음과 같은 제한이 있습니다.

* MakeCert는 더 이상 사용되지 않습니다. 즉, 언제든지 이 도구가 제거될 수 있습니다. MakeCert를 더 이상 사용할 수 없는 경우 MakeCert를 사용하여 이미 생성한 인증서는 영향을 받지 않습니다. MakeCert는 메커니즘 유효성 검사에 사용되지 않으며 인증서를 생성하는 데에만 사용됩니다.

## <a name="rootcert"></a>자체 서명된 루트 인증서 만들기

다음 단계에서는 MakeCert를 사용하여 자체 서명된 인증서를 만드는 방법을 보여 줍니다. 이러한 단계는 배포 모델에 한정되지 않습니다. 리소스 관리자와 클래식에 대해 모두 유효합니다.

1. [MakeCert](https://msdn.microsoft.com/library/windows/desktop/aa386968(v=vs.85).aspx)를 다운로드 및 설치합니다.
2. 설치가 끝나면 일반적으로이 경로 아래에서 makecert.exe 유틸리티를 찾을 수 있습니다. 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>'. 하지만 다른 위치에 설치되었을 수도 있습니다. 관리자 권한으로 명령 프롬프트를 열고 MakeCert 유틸리티의 위치로 이동합니다. 적절한 위치에 대해 조정하여 다음 예제를 사용할 수 있습니다.

   ```cmd
   cd C:\Program Files (x86)\Windows Kits\10\bin\x64
   ```
3. 사용자 컴퓨터의 개인 인증서 저장소에 인증서를 만들고 설치합니다. 다음 예제에서는 P2S를 구성할 때 Azure에 업로드하는 해당 *.cer* 파일을 만듭니다. 'P2SRootCert' 및 'P2SRootCert.cer'을 인증서에 사용하려는 이름으로 바꿉니다. 인증서는 'Certificates - Current User\Personal\Certificates'에 있습니다.

   ```cmd
   makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha256 -len 2048 -ss My
   ```

## <a name="cer"></a>공개 키(.cer) 내보내기

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

exported.cer 파일을 Azure에 업로드해야 합니다. 자세한 내용은 [지점 및 사이트 간 연결 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile)을 참조하세요. 신뢰할 수 있는 루트 인증서를 추가하려면 문서의 [이 섹션](vpn-gateway-howto-point-to-site-resource-manager-portal.md#add)을 참조하세요.

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>자체 서명된 인증서 및 개인 키를 내보낸 다음 저장(선택 사항)

자체 서명된 루트 인증서를 내보낸 다음 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에서 해당 인증서를 설치하고 더 많은 클라이언트 인증서를 생성하거나 다른 .cer 파일을 내보낼 수 있습니다. 자체 서명된 루트 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientexport)에서 설명하는 것과 같은 단계를 사용합니다.

## <a name="create-and-install-client-certificates"></a>클라이언트 인증서 만들기 및 설치

자체 서명된 인증서를 클라이언트 컴퓨터에 직접 설치하지는 않으며, 자체 서명된 인증서에서 클라이언트 인증서를 생성해야 합니다. 그런 다음 클라이언트 인증서를 클라이언트 컴퓨터로 내보낸 후 설치해야 합니다. 이러한 단계는 배포 모델에 관계없이 적용됩니다. 리소스 관리자와 클래식에 대해 모두 유효합니다.

### <a name="clientcert"></a>클라이언트 인증서 생성

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치합니다. 클라이언트 인증서가 설치되어 있지 않으면 인증이 실패합니다. 

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 과정을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 다음 단계를 사용하여 클라이언트 인증서를 생성하는 경우 클라이언트 인증서가 인증서를 생성하는 데 사용하는 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 인증서를 내보낼 수 있습니다.
 
1. 자체 서명된 인증서를 만드는 데 사용한 동일한 컴퓨터에서 관리자로 명령 프롬프트를 엽니다.
2. 샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다.
   * *"P2SRootCert"* 는 클라이언트 인증서를 생성 중인 자체 서명된 루트의 이름으로 변경합니다. 루트 인증서의 이름을 사용하고 있는지 확인합니다. 이 경우에 'CN=' 값은 자체 서명된 루트를 만들 때 지정한 값입니다.
   * *P2SChildCert*는 생성하는 클라이언트 인증서에 사용할 이름으로 변경합니다.

   다음 예제를 수정하지 않고 실행하면 루트 인증서 P2SRootCert에서 생성된 클라이언트 인증서 P2SChildcert가 개인 인증서 저장소에 저장됩니다.

   ```cmd
   makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha256
   ```

### <a name="clientexport"></a>클라이언트 인증서 내보내기

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>내보낸 클라이언트 인증서 설치

클라이언트 인증서를 설치하려면 [클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

지점 및 사이트 간 구성을 계속합니다. 

* **Resource Manager** 배포 모델 단계는 [네이티브 Azure 인증서 인증을 사용하여 P2S 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요.
* **클래식** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 VPN 연결 구성(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 참조하세요.

P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.
