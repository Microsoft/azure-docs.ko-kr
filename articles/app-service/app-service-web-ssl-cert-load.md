---
title: 애플리케이션 코드에서 클라이언트 SSL 인증서 사용 - Azure App Service | Microsoft Docs
description: 클라이언트 인증서를 사용하여 해당 인증서가 필요한 원격 리소스에 연결하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60851122"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Azure App Service의 애플리케이션 코드에서 SSL 인증서 사용

이 방법 가이드에서는 애플리케이션 코드의 App Service 앱에 업로드하거나 가져온 SSL 인증서 중 하나를 사용하는 방법을 보여줍니다. 사용 사례의 예제에서는 앱을 인증서 인증이 필요한 외부 서비스에 액세스합니다. 

코드에서 SSL 인증서를 사용하는 이 방법을 사용하면 App Service에서 SSL 기능을 사용하게 됩니다. 이 경우에 앱은 **기본** 계층 이상이어야 합니다. 대신 애플리케이션 디렉터리에 인증서 파일을 포함하고 직접 로드할 수 있습니다([대안: 인증서 파일 로드](#file) 참조). 그러나 이 대안을 사용하면 애플리케이션 코드 또는 개발자로부터 인증서의 개인 키를 숨길 수 없습니다. 또한 애플리케이션 코드가 오픈 소스 리포지토리에 있는 경우 리포지토리에서 개인 키로 인증서를 사용할 수 없습니다.

App Service에서 SSL 인증서를 관리할 수 있는 경우 인증서 및 애플리케이션 코드를 별도로 유지 관리하여 중요한 데이터를 보호할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 완료하려면 다음이 필요합니다.

- [App Service 앱 만들기](/azure/app-service/)
- [웹앱에 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
- 웹앱에 [SSL 인증서 업로드](app-service-web-tutorial-custom-ssl.md) 또는 [App Service Certificate 가져오기](web-sites-purchase-ssl-web-site.md)


## <a name="load-your-certificates"></a>인증서 로드

App Service에 업로드되거나 가져온 인증서를 사용하려면 먼저 애플리케이션 코드에 액세스할 수 있도록 합니다. `WEBSITE_LOAD_CERTIFICATES` 앱 설정에서 이 작업을 수행합니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 웹앱 페이지를 엽니다.

왼쪽 탐색 영역에서 **SSL 인증서**를 클릭합니다.

![업로드된 인증서](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

이 웹앱에 업로드하고 가져온 모든 SSL 인증서가 해당 지문과 함께 여기에 표시됩니다. 사용하려는 인증서의 지문을 복사합니다.

왼쪽 탐색 영역에서 **애플리케이션 설정**을 클릭합니다.

`WEBSITE_LOAD_CERTIFICATES`라는 앱 설정을 추가하고 인증서의 지문에 해당 값을 설정합니다. 여러 인증서에 액세스할 수 있도록 하려면 쉼표로 구분된 지문 값을 사용합니다. 모든 인증서에 액세스할 수 있도록 하려면 값을 `*`로 설정합니다. 이렇게 설정하면 인증서가 `CurrentUser\My` 저장소에 배치됩니다.

![앱 설정 구성](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

완료되면 **저장**을 클릭합니다.

이제 구성된 인증서를 코드에서 사용할 수 있습니다.

## <a name="use-certificate-in-c-code"></a>C# 코드에서 인증서 사용

인증서에 액세스할 수 있게 되면 인증서 지문이 C# 코드에서 액세스할 수 있습니다. 다음 코드는 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 지문으로 인증서를 로드합니다.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="alternative-load-certificate-as-a-file"></a>대안: 인증서 파일 로드

이 섹션에서는 애플리케이션 디렉터리에 있는 인증서 파일을 로드하는 방법을 보여줍니다. 

다음 C# 예제에서는 앱 리포지토리의 `certs` 디렉터리에서 `mycert.pfx`라는 인증서를 로드합니다.

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

