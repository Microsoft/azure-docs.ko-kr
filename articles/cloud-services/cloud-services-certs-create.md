---
title: Cloud Services 및 관리 인증서 | Microsoft Docs
description: Cloud services에 대 한 인증서를 만들고 배포 하 고 Azure에서 관리 API를 사용 하 여 인증 하는 방법에 대해 알아봅니다.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 08ce69856dd36b6029297109fcb8610b856c8b98
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142369"
---
# <a name="certificates-overview-for-azure-cloud-services"></a>Azure Cloud Services 인증서 개요
인증서는 Azure에서 클라우드 서비스([서비스 인증서](#what-are-service-certificates))와 관리 API([관리 인증서](#what-are-management-certificates))를 통한 인증에 사용됩니다. 이 토픽에서는 두 가지 인증서 형식에 대한 일반적인 개요와 인증서를 [만들고](#create) Azure에 배포하는 방법을 살펴봅니다.

Azure에서 사용되는 인증서는 x.509 v3 인증서이며 다른 신뢰할 수 있는 인증서에 의해 서명되거나 자체 서명될 수 있습니다. 자체 서명된 인증서는 해당 작성자에 의해 서명되므로 기본적으로 신뢰할 수 없습니다. 대부분의 브라우저는 이러한 문제를 무시할 수 있습니다. Cloud Services를 개발하고 테스트하는 경우에만 자체 서명된 인증서를 사용해야 합니다. 

Azure에서 사용 하는 인증서에는 공개 키가 포함 될 수 있습니다. 인증서에는 지문이 포함되어 있어 모호하지 않은 방식의 식별 수단을 제공합니다. 이 지 문은 Azure [구성 파일](cloud-services-configure-ssl-certificate-portal.md) 에서 클라우드 서비스가 사용 해야 하는 인증서를 식별 하는 데 사용 됩니다. 

>[!Note]
>Azure Cloud Services는 AES256-SHA256 암호화 인증서를 수락하지 않습니다.

## <a name="what-are-service-certificates"></a>서비스 인증서란 무엇인가요?
서비스 인증서는 클라우드 서비스에 첨부되며 서비스와의 보안 통신이 가능하게 해 줍니다. 예를 들어 웹 역할을 배포한 경우 노출된 HTTPS 엔드포인트를 인증할 수 있는 인증서를 제공하려고 할 것입니다. 서비스 정의에 있는 서비스 인증서는 자동으로 해당 역할 인스턴스를 실행하는 가상 머신에 배포됩니다. 

Azure Portal을 사용하거나 클래식 배포 모델을 사용하여 서비스 인증서를 Azure Portal에 업로드할 수 있습니다. 서비스 인증서는 특정 클라우드 서비스와 연관됩니다. 서비스 정의 파일에서 배포에 할당됩니다.

서비스 인증서는 서비스와 별도로 관리할 수 있으며 다른 개인이 관리할 수도 있습니다. 예를 들어 개발자는 IT 관리자가 이전에 Azure로 업로드한 인증서를 참조하는 서비스 패키지를 업로드할 수 있습니다. IT 관리자는 새 서비스 패키지를 업로드할 필요 없이 서비스 구성을 변경하는 해당 인증서를 관리하고 갱신할 수 있습니다. 새 서비스 패키지 없이 업데이트가 가능한 이유는 인증서의 논리적 이름과 저장소 이름 및 위치는 서비스 정의 파일에 있고 인증서 지문은 서비스 구성 파일에 지정되어 있기 때문입니다. 인증서를 업데이트하려면 새 인증서를 업로드하고 서비스 구성 파일의 지문 값을 변경하기만 하면 됩니다.

>[!Note]
>[CLOUD SERVICES FAQ-구성 및 관리](cloud-services-configuration-and-management-faq.md) 문서에는 인증서에 대 한 몇 가지 유용한 정보가 있습니다.

## <a name="what-are-management-certificates"></a>관리 인증서란 무엇인가요?
관리 인증서를 사용하면 클래식 배포 모델로 인증할 수 있습니다. Visual Studio 또는 Azure SDK와 같은 많은 프로그램 및 도구에서 이러한 인증서를 사용하여 다양한 Azure 서비스의 구성 및 배포를 자동화합니다. 클라우드 서비스와는 실제로 관련이 없습니다. 

> [!WARNING]
> 주의가 필요합니다! 이러한 형식의 인증서를 사용하면 해당 인증서로 인증된 사람은 누구나 연결된 구독을 관리할 수 있습니다. 
> 
> 

### <a name="limitations"></a>제한 사항
관리 인증서는 구독당 100개로 제한됩니다. 특정 서비스 관리자의 사용자 ID에서 모든 구독에 대한 관리 인증서가 100개로 제한되기도 합니다. 계정 관리자의 사용자 ID가 이미 관리 인증서 100개를 추가하는 데 사용되었으나 인증서가 더 필요한 경우 공동 관리자를 추가하여 인증서를 더 추가할 수 있습니다. 

<a name="create"></a>
## <a name="create-a-new-self-signed-certificate"></a>자체 서명된 새로운 인증서 만들기
어떠한 도구든 다음 설정을 준수하는 경우 자체 서명된 인증서를 만드는 데 사용할 수 있습니다.

* X.509 인증서여야 합니다.
* 공개 키를 포함 합니다.
* 키 교환용으로 만들어졌어야 합니다(.pfx 파일).
* 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인과 일치해야 합니다.

    > Cloudapp.net (또는 Azure 관련) 도메인에 대 한 TLS/SSL 인증서를 가져올 수 없습니다. 인증서의 주체 이름은 응용 프로그램에 액세스 하는 데 사용 되는 사용자 지정 도메인 이름과 일치 해야 합니다. 예를 들어, **contoso.cloudapp.net**이 아니라**contoso.net**입니다.

* 최소한 2048비트 암호화를 사용해야 합니다.
* **서비스 인증서에만 해당**: 클라이언트 쪽 인증서는 *개인* 인증서 저장소에 있어야 합니다.

Windows에서는 두 가지 방법, `makecert.exe` 유틸리티 또는 IIS를 사용하여 쉽게 인증서를 만들 수 있습니다.

### <a name="makecertexe"></a>Makecert.exe
이 유틸리티는 사용되지 않으므로 여기에 더 이상 설명되지 않습니다. 자세한 내용은 [이 MSDN 문서](/windows/desktop/SecCrypto/makecert)를 참조하세요.

### <a name="powershell"></a>PowerShell
```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 2048 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```

> [!NOTE]
> 도메인 대신에 IP 주소가 들어 있는 인증서를 사용하려는 경우 -DnsName 매개 변수에서 IP 주소를 사용 합니다.


[관리 포털에서 인증서](../azure-api-management-certs.md)를 사용하려는 경우 **.cer** 파일로 내보냅니다.

```powershell
Export-Certificate -Type CERT -Cert $cert -FilePath .\my-cert-file.cer
```

### <a name="internet-information-services-iis"></a>IIS(인터넷 정보 서비스)
인터넷에는 IIS를 사용하여 이 작업을 수행하는 방법을 다룬 내용이 많이 있습니다. [여기](https://www.sslshopper.com/article-how-to-create-a-self-signed-certificate-in-iis-7.html) 에서 볼 수 있습니다. 

### <a name="linux"></a>Linux
[이](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 문서에서는 SSH로 인증서를 만드는 방법을 설명합니다.

## <a name="next-steps"></a>다음 단계
[서비스 인증서를 Azure Portal에 업로드](cloud-services-configure-ssl-certificate-portal.md)합니다.

[관리 API 인증서](../azure-api-management-certs.md)를 Azure Portal에 업로드합니다.




