---
title: TLS 상호 인증 구성
description: TLS에서 클라이언트 인증서를 인증 하는 방법에 대해 알아봅니다. Azure App Service를 사용 하면 인증을 위해 응용 프로그램 코드에서 클라이언트 인증서를 사용할 수 있습니다.
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 10/01/2019
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 145b999d7bf8597c06d6e3d4a36d01b182c8ae68
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213647"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Azure App Service에 대 한 TLS 상호 인증 구성

다양한 유형의 인증을 사용하여 Azure App Service 앱에 대한 액세스를 제한할 수 있습니다. 이 작업을 수행 하는 한 가지 방법은 클라이언트 요청이 TLS/SSL을 통해 수행 되는 경우 클라이언트 인증서를 요청 하 고 인증서의 유효성을 검사 하는 것입니다. 이 메커니즘을 TLS 상호 인증 또는 클라이언트 인증서 인증 이라고 합니다. 이 문서에서는 클라이언트 인증서 인증을 사용 하도록 앱을 설정 하는 방법을 보여 줍니다.

> [!NOTE]
> HTTP를 통해 사이트에 액세스하고 HTTPS를 통해서는 액세스하지 않는 경우 클라이언트 인증서가 제공되지 않습니다. 따라서 응용 프로그램에 클라이언트 인증서가 필요한 경우 HTTP를 통해 응용 프로그램에 대 한 요청을 허용 하지 않아야 합니다.
>

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="enable-client-certificates"></a>클라이언트 인증서 사용하도록 설정

클라이언트 인증서를 요구 하도록 앱을 설정 하려면 Azure Portal에서 **구성**일반 설정을 선택 **하 여 들어오는** 인증서 요구를 전환  >  **General Settings** 하거나 `clientCertEnabled` 앱에 대 한 설정을로 설정 해야 `true` 합니다. 설정을 설정 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>인증 요청에서 경로 제외

응용 프로그램에 대해 상호 인증을 사용 하도록 설정 하면 앱의 루트에 있는 모든 경로에 액세스 하기 위한 클라이언트 인증서가 필요 합니다. 익명 액세스를 위해 특정 경로를 열어 둘 수 있도록 허용 하려면 응용 프로그램 구성의 일부로 제외 경로를 정의할 수 있습니다.

제외 경로는 **구성**  >  **일반 설정** 을 선택 하 고 제외 경로를 정의 하 여 구성할 수 있습니다. 이 예제에서 응용 프로그램의 경로 아래에 있는 모든 항목 `/public` 은 클라이언트 인증서를 요청 하지 않습니다.

![인증서 제외 경로][exclusion-paths]


## <a name="access-client-certificate"></a>클라이언트 인증서 액세스

App Service에서 요청에 대 한 TLS 종료는 프런트 엔드 부하 분산 장치에서 발생 합니다. [클라이언트 인증서를 사용](#enable-client-certificates)하 여 응용 프로그램 코드에 요청을 전달 하는 경우 App Service는 `X-ARR-ClientCert` 클라이언트 인증서를 사용 하 여 요청 헤더를 삽입 합니다. App Service는이 클라이언트 인증서를 사용 하 여 앱에 전달 하는 것 외에 다른 작업을 수행 하지 않습니다. 응용 프로그램 코드는 클라이언트 인증서의 유효성을 검사 해야 합니다.

ASP.NET의 경우 **HttpRequest** 속성을 통해 클라이언트 인증서를 사용할 수 있습니다.

다른 응용 프로그램 스택 (Node.js, PHP 등)의 경우 요청 헤더에 있는 base64 인코딩 값을 통해 앱에서 클라이언트 인증서를 사용할 수 있습니다. `X-ARR-ClientCert`

## <a name="aspnet-sample"></a>ASP.NET 샘플

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
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

다음 Node.js 샘플 코드는 헤더를 가져오고 `X-ARR-ClientCert` [노드-대장간](https://github.com/digitalbazaar/forge) 을 사용 하 여 b a s e 64로 인코딩된 PEM 문자열을 인증서 개체로 변환 하 고 유효성을 검사 합니다.

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
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
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

## <a name="java-sample"></a>Java 샘플

다음 Java 클래스는에서 인스턴스로 인증서를 인코딩합니다 `X-ARR-ClientCert` `X509Certificate` . `certificateIsValid()` 인증서의 지문이 생성자에 지정 된 지문과 일치 하 고 인증서가 만료 되지 않았는지 확인 합니다.


```java
import java.io.ByteArrayInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.cert.*;
import java.security.MessageDigest;

import sun.security.provider.X509Factory;

import javax.xml.bind.DatatypeConverter;
import java.util.Base64;
import java.util.Date;

public class ClientCertValidator { 

    private String thumbprint;
    private X509Certificate certificate;

    /**
     * Constructor.
     * @param certificate The certificate from the "X-ARR-ClientCert" HTTP header
     * @param thumbprint The thumbprint to check against
     * @throws CertificateException If the certificate factory cannot be created.
     */
    public ClientCertValidator(String certificate, String thumbprint) throws CertificateException {
        certificate = certificate
                .replaceAll(X509Factory.BEGIN_CERT, "")
                .replaceAll(X509Factory.END_CERT, "");
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        byte [] base64Bytes = Base64.getDecoder().decode(certificate);
        X509Certificate X509cert =  (X509Certificate) cf.generateCertificate(new ByteArrayInputStream(base64Bytes));

        this.setCertificate(X509cert);
        this.setThumbprint(thumbprint);
    }

    /**
     * Check that the certificate's thumbprint matches the one given in the constructor, and that the
     * certificate has not expired.
     * @return True if the certificate's thumbprint matches and has not expired. False otherwise.
     */
    public boolean certificateIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        return certificateHasNotExpired() && thumbprintIsValid();
    }

    /**
     * Check certificate's timestamp.
     * @return Returns true if the certificate has not expired. Returns false if it has expired.
     */
    private boolean certificateHasNotExpired() {
        Date currentTime = new java.util.Date();
        try {
            this.getCertificate().checkValidity(currentTime);
        } catch (CertificateExpiredException | CertificateNotYetValidException e) {
            return false;
        }
        return true;
    }

    /**
     * Check the certificate's thumbprint matches the given one.
     * @return Returns true if the thumbprints match. False otherwise.
     */
    private boolean thumbprintIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        byte[] der = this.getCertificate().getEncoded();
        md.update(der);
        byte[] digest = md.digest();
        String digestHex = DatatypeConverter.printHexBinary(digest);
        return digestHex.toLowerCase().equals(this.getThumbprint().toLowerCase());
    }

    // Getters and setters

    public void setThumbprint(String thumbprint) {
        this.thumbprint = thumbprint;
    }

    public String getThumbprint() {
        return this.thumbprint;
    }

    public X509Certificate getCertificate() {
        return certificate;
    }

    public void setCertificate(X509Certificate certificate) {
        this.certificate = certificate;
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png
