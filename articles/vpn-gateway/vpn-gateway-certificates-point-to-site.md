---
title: '지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기: PowerShell: Azure | Microsoft Docs'
description: Windows 10 또는 Windows Server 2016의 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고, 공개 키를 내보내고, 클라이언트 인증서를 생성합니다.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764019"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>PowerShell을 사용하여 지점 및 사이트 간 연결에 대한 인증서 생성 및 내보내기

지점 및 사이트 간 연결은 인증서를 사용하여 인증을 합니다. 이 아티클에서는 Windows 10 또는 Windows Server 2016에서 PowerShell을 사용하여 자체 서명된 루트 인증서를 만들고 클라이언트 인증서를 생성하는 방법을 보여 줍니다. 다른 인증서 지침을 찾는 경우 [인증서 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 또는 [인증서 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)를 참조하세요.

Windows 10 또는 Windows Server 2016을 실행하는 컴퓨터에서 이 문서의 단계를 수행해야 합니다. 인증서를 생성하는 데 사용하는 PowerShell cmdlet은 운영 체제의 일부이며 다른 Windows 버전에서는 작동하지 않습니다. Windows 10 또는 Windows Server 2016 컴퓨터는 인증서 생성에만 필요합니다. 인증서를 생성한 후에는 지원되는 모든 클라이언트 운영 체제에 업로드하거나 설치할 수 있습니다. 

Windows 10 또는 Windows Server 2016 컴퓨터에 액세스할 수 없는 경우 [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)를 사용하여 인증서를 생성할 수 있습니다. 두 방법 중 하나를 사용하여 생성하는 인증서는 [지원되는](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) 모든 클라이언트 운영 체제에 설치할 수 있습니다.

## <a name="rootcert"></a>1. 자체 서명된 루트 인증서 만들기

New-SelfSignedCertificate cmdlet을 사용하여 자체 서명된 루트 인증서를 만듭니다. 추가 매개 변수 정보는 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)를 참조하세요.

1. Windows 10 또는 Windows Server 2016을 실행하는 컴퓨터에서 상승된 권한으로 Windows PowerShell 콘솔을 엽니다. 이 예제는 Azure Cloud Shell "시도"에서는 작동하지 않습니다. 이 예제는 로컬에서 실행해야 합니다.
2. 다음 예제를 사용하여 자체 서명된 루트 인증서를 만듭니다. 다음 예제에서는 'Certificates-Current User\Personal\Certificates'에 자동으로 설치된 'P2SRootCert'라는 자체 서명된 루트 인증서를 만듭니다. *certmgr.msc* 또는 *사용자 인증서 관리*를 열어 인증서를 볼 수 있습니다.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. 클라이언트 인증서 생성

지점 및 사이트 간을 사용하여 VNet에 연결하는 각 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성한 후 클라이언트 인증서를 내보내고 설치합니다. 클라이언트 인증서가 설치되어 있지 않으면 인증이 실패합니다. 

다음 단계는 자체 서명된 루트 인증서에서 클라이언트 인증서를 생성하는 과정을 안내합니다. 동일한 루트 인증서에서 여러 클라이언트 인증서를 생성할 수 있습니다. 다음 단계를 사용하여 클라이언트 인증서를 생성하는 경우 클라이언트 인증서가 인증서를 생성하는 데 사용하는 컴퓨터에 자동으로 설치됩니다. 다른 클라이언트 컴퓨터에 클라이언트 인증서를 설치하려는 경우 인증서를 내보낼 수 있습니다.

예제에서는 New-SelfSignedCertificate cmdlet을 사용하여 1년 후에 만료되는 클라이언트 인증서를 생성합니다. 클라이언트 인증서에 대해 다른 만료 값을 설정하는 등의 추가 매개 변수 정보는 [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)를 참조하세요.

### <a name="example-1"></a>예 1

자체 서명된 루트 인증서를 만든 후에 PowerShell 콘솔을 닫지 않은 경우 이 예제를 사용합니다. 이 예제는 이전 섹션에서 계속되며 선언된 ‘$cert’ 변수를 사용합니다. 자체 서명된 루트 인증서를 만든 후 PowerShell 콘솔을 종료했거나 새 PowerShell 콘솔 세션에서 추가 클라이언트 인증서를 생성하려는 경우 [예제 2](#ex2)의 단계를 사용합니다.

샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다.  자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>예 2

추가 클라이언트 인증서를 만들거나 자체 서명된 루트 인증서를 만드는 데 사용한 것과 동일한 PowerShell 세션을 사용하지 않을 경우 다음 단계를 사용합니다.

1. 컴퓨터에 설치되어 있는 자체 서명된 루트 인증서를 식별합니다. 이 cmdlet은 컴퓨터에 설치된 인증서 목록을 반환합니다.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. 반환된 목록에서 주체 이름을 찾은 다음 텍스트 파일에 옆에 있는 지문을 복사합니다. 다음 예제에는 두 개의 인증서가 있습니다. CN 이름은 자식 인증서를 생성하려는 자체 서명된 루트 인증서의 이름입니다. 이 경우에는 'P2SRootCert'입니다.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. 이전 단계의 지문을 사용하여 루트 인증서에 대한 변수를 선언합니다. THUMBPRINT을 자식 인증서를 생성하려는 루트 인증서의 지문으로 바꿉니다.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   예를 들어 이전 단계의 P2SRootCert에 대한 지문을 사용할 경우 변수는 다음과 같습니다.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. 샘플을 수정 및 실행하여 클라이언트 인증서를 생성합니다. 다음 예제를 수정하지 않고 실행할 경우 결과적으로 'P2SChildCert'라는 클라이언트 인증서가 만들어집니다. 자식 인증서에 다른 이름을 지정하려는 경우 CN 값을 수정합니다. 이 예제를 실행하는 경우는 TextExtension을 변경하지 마세요. 생성하는 클라이언트 인증서는 컴퓨터의 'Certificates - Current User\Personal\Certificates'에 자동으로 설치됩니다.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. 루트 인증서 공개 키(.cer) 내보내기

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>자체 서명된 루트 인증서 및 개인 키를 내보내고 저장(선택 사항)

자체 서명된 루트 인증서를 내보내고 백업으로 안전하게 저장할 수 있습니다. 필요한 경우 나중에 다른 컴퓨터에 설치하고 더 많은 클라이언트 인증서를 생성할 수 있습니다. 자체 서명된 루트 인증서를 .pfx로 내보내려면 루트 인증서를 선택하고 [클라이언트 인증서 내보내기](#clientexport)에서 설명하는 것과 같은 단계를 사용합니다.

## <a name="clientexport"></a>4. 클라이언트 인증서 내보내기

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. 내보낸 클라이언트 인증서 설치

P2S 연결을 통해 VNet에 연결하는 각 클라이언트는 클라이언트 인증서를 로컬로 설치해야 합니다.

클라이언트 인증서를 설치하려면 [지점 및 사이트 간 연결에 클라이언트 인증서 설치](point-to-site-how-to-vpn-client-install-azure-cert.md)를 참조하세요.

## <a name="install"></a>6. P2S 구성 단계 계속

지점 및 사이트 간 구성을 계속합니다.

* **Resource Manager** 배포 모델 단계는 [네이티브 Azure 인증서 인증을 사용하여 P2S 구성](vpn-gateway-howto-point-to-site-resource-manager-portal.md)을 참조하세요. 
* **클래식** 배포 모델 단계의 경우 [VNet에 지점 및 사이트 간 VPN 연결 구성(클래식)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 참조하세요.
* P2S 문제 해결 정보는 [Azure 지점 및 사이트 간 연결 문제 해결](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)을 참조하세요.
