---
title: TLS 상호 인증 구성 - Azure App Service
description: TLS에서 클라이언트 인증서 인증을 사용하도록 앱을 구성하는 방법을 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 5702362add6a50f2f4525afbd3649f083f34b6fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852451"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Azure App Service에 대 한 TLS 상호 인증 구성

다양한 유형의 인증을 사용하여 Azure App Service 앱에 대한 액세스를 제한할 수 있습니다. 작업을 수행 하는 한 가지 방법은 TLS/SSL을 통해 클라이언트 요청의 경우 클라이언트 인증서를 요청 하 고 인증서의 유효성을 검사 하는 경우 이 메커니즘은 TLS 상호 인증 또는 클라이언트 인증서 인증 이라고 합니다. 이 문서에는 클라이언트 인증서 인증을 사용 하도록 앱을 설정 하는 방법을 보여 줍니다.

> [!NOTE]
> HTTP를 통해 사이트에 액세스하고 HTTPS를 통해서는 액세스하지 않는 경우 클라이언트 인증서가 제공되지 않습니다. 따라서 응용 프로그램에 클라이언트 인증서가 필요한 경우 허용 하지 않아야 요청 응용 프로그램에 HTTP를 통해.
>

## <a name="enable-client-certificates"></a>클라이언트 인증서를 사용 하도록 설정

을 설정 하려면 앱 클라이언트 인증서를 요구 하도록 설정 해야 합니다 `clientCertEnabled` 앱 설정 `true`합니다. 설정의 설정 하려면에서 다음 명령을 실행 합니다 [Cloud Shell](https://shell.azure.com)합니다.

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="access-client-certificate"></a>액세스 클라이언트 인증서

App Service에서 SSL 종료 요청의 프런트 엔드 부하 분산 장치에서 발생합니다. 요청을 사용 하 여 앱 코드를 전달할 때 [사용 하도록 설정 하는 클라이언트 인증서](#enable-client-certificates)를 삽입 하는 App Service는 `X-ARR-ClientCert` 클라이언트 인증서를 사용 하 여 요청 헤더입니다. App Service 앱에 전달 이외의이 클라이언트 인증서를 사용 하 여 아무 것도 수행 하지 않습니다. 앱 코드는 클라이언트 인증서의 유효성을 검사 하는 일을 담당 합니다.

ASP.NET에 대 한 클라이언트 인증서가를 통해 사용할 수는 **HttpRequest.ClientCertificate** 속성입니다.

다른 응용 프로그램 스택 (Node.js, PHP 등)에 대 한 클라이언트 인증서에서 base64로 인코딩된 값을 통해 앱에서 사용할 수 있는를 `X-ARR-ClientCert` 요청 헤더입니다.

## <a name="aspnet-sample"></a>ASP.NET 샘플

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node.js 샘플

다음 Node.js 샘플 코드를 가져옵니다 합니다 `X-ARR-ClientCert` 사용 하 여 헤더 [노드 위조](https://github.com/digitalbazaar/forge) 인증서를 base64로 인코딩된 PEM 문자열로 변환 하 고 유효성을 검사 하려면:

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer((pki as any).certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```