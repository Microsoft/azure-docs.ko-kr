---
title: 통신 보안 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
description: 위협 모델링 도구에 노출되는 위협 완화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 8534f30c17208e77adfa47ea41506a3a61d3548d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121326"
---
# <a name="security-frame-communication-security--mitigations"></a>보안 프레임: 통신 보안 | 완화 
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **Azure 이벤트 허브** | <ul><li>[SSL/TLS를 사용하여 이벤트 허브 통신 보안](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[서비스 계정 권한 확인 및 사용자 지정 서비스 또는 ASP.NET 페이지에서 CRM 보안을 준수하는지 확인](#priv-aspnet)</li></ul> |
| **Azure 데이터 팩터리** | <ul><li>[Azure Data Factory에 온-프레미스 SQL Server를 연결 하는 동안 데이터 관리 게이트웨이 사용 합니다.](#sqlserver-factory)</li></ul> |
| **Identity Server** | <ul><li>[Identity Server에 대한 모든 트래픽이 HTTPS 연결을 통과하는지 확인](#identity-https)</li></ul> |
| **웹 애플리케이션** | <ul><li>[X.509 인증서를 사용하여 SSL, TLS 및 DTLS 연결을 인증하는지 확인](#x509-ssltls)</li><li>[Azure App Service에서 사용자 지정 도메인에 대한 SSL 인증서 구성](#ssl-appservice)</li><li>[Azure App Service에 대한 모든 트래픽이 HTTPS 연결을 통과하도록 강제 적용](#appservice-https)</li><li>[HSTS(HTTP 엄격한 전송 보안)를 사용하도록 설정](#http-hsts)</li></ul> |
| **데이터베이스** | <ul><li>[SQL 서버 연결 암호화 및 인증서 유효성 검사 확인](#sqlserver-validation)</li><li>[SQL 서버에 암호화된 통신 강제 적용](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Azure Storage에 대한 통신이 HTTPS를 통과하는지 확인](#comm-storage)</li><li>[HTTPS를 사용할 수 없는 경우 Blob을 다운로드한 후 MD5 해시 유효성 검사](#md5-https)</li><li>[SMB 3.0 호환 클라이언트를 사용하여 Azure 파일 공유에 대한 전송 중 데이터 암호화 보장](#smb-shares)</li></ul> |
| **모바일 클라이언트** | <ul><li>[인증서 고정 구현](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[HTTPS 사용 설정 - 보안 전송 채널](#https-transport)</li><li>[WCF: 메시지 보안 보호 수준을 EncryptAndSign으로 설정](#message-protection)</li><li>[WCF: 최소 권한 계정을 사용하여 WCF 서비스 실행](#least-account-wcf)</li></ul> |
| **앱 API** | <ul><li>[Web API에 대한 모든 트래픽이 HTTPS 연결을 통과하도록 강제 적용](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Azure Cache for Redis에 대한 통신이 SSL을 통과하는지 확인](#redis-ssl)</li></ul> |
| **IoT 필드 게이트웨이** | <ul><li>[디바이스-필드 게이트웨이 통신 보안](#device-field)</li></ul> |
| **IoT 클라우드 게이트웨이** | <ul><li>[SSL/TLS를 사용하여 디바이스-클라우드 게이트웨이 통신 보안](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>SSL/TLS를 사용하여 이벤트 허브 통신 보안

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Event Hub | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Event Hubs 인증 및 보안 모델 개요](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **단계** | SSL/TLS를 사용하여 이벤트 허브에 대한 AMQP 또는 HTTP 연결 보안을 유지합니다. |

## <a id="priv-aspnet"></a>서비스 계정 권한 확인 및 사용자 지정 서비스 또는 ASP.NET 페이지에서 CRM 보안을 준수하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Dynamics CRM | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | 서비스 계정 권한을 확인하고 사용자 지정 서비스 또는 ASP.NET 페이지에서 CRM 보안을 준수하는지 확인합니다. |

## <a id="sqlserver-factory"></a>Azure Data Factory에 온-프레미스 SQL Server를 연결 하는 동안 데이터 관리 게이트웨이 사용 합니다.

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Data Factory | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 연결 된 서비스 형식-Azure 및 온-프레미스 |
| **참조**              |[온-프레미스 및 Azure Data Factory 간 데이터 이동](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [데이터 관리 게이트웨이](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **단계** | <p>DMG(데이터 관리 게이트웨이) 도구는 회사 네트워크 또는 방화벽 뒤에서 보호되는 데이터 원본에 연결해야 합니다.</p><ol><li>컴퓨터를 잠그면 DMG 도구가 격리되고 오작동하는 프로그램이 데이터 원본 컴퓨터를 손상시키거나 스누핑하지 않게 됩니다. (예: 최신 업데이트를 설치해야 하고, 필요한 최소 포트를 사용하고, 계정 프로비저닝을 제어하고, 감사를 사용하고, 디스크 암호화를 사용해야 합니다.)</li><li>데이터 게이트웨이 키는 빈번한 간격으로 또는 DMG 서비스 계정 암호가 갱신될 때마다 회전해야 합니다.</li><li>연결 서비스를 통한 데이터 전송은 암호화되어야 합니다.</li></ol> |

## <a id="identity-https"></a>Identity Server에 대한 모든 트래픽이 HTTPS 연결을 통과하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | ID 서버 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [IdentityServer3 - 키, 서명 및 암호화](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html)(영문), [IdentityServer3 - 배포](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html)(영문) |
| **단계** | 기본적으로 IdentityServer에서는 들어오는 모든 연결이 HTTPS를 통과해야 합니다. IdentityServer와의 통신은 반드시 보안 전송을 통해서만 수행되어야 합니다. 이 요구 사항을 완화할 수 있는 SSL 오프로딩과 같은 특정 배포 시나리오가 있습니다. 자세한 내용은 Identity Server 배포 페이지의 참조 부분을 참조하세요. |

## <a id="x509-ssltls"></a>X.509 인증서를 사용하여 SSL, TLS 및 DTLS 연결을 인증하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>SSL, TLS 또는 DTLS를 사용하는 애플리케이션은 연결하는 엔터티의 X.509 인증서를 완전히 확인해야 합니다. 여기에는 다음에 대한 인증서 확인이 포함됩니다.</p><ul><li>도메인 이름</li><li>유효 날짜(시작 날짜와 만료 날짜 모두)</li><li>해지 상태</li><li>사용 현황(예: 서버에 대한 서버 인증, 클라이언트에 대한 클라이언트 인증)</li><li>신뢰 체인 - 인증서는 플랫폼에서 신뢰할 수 있거나 관리자가 명시적으로 구성한 루트 인증 기관(CA)에 연결되어야 합니다.</li><li>인증서 공개 키의 길이는 2,048비트보다 커야 합니다.</li><li>해싱 알고리즘은 SHA256 이상이어야 합니다. |

## <a id="ssl-appservice"></a>Azure App Service에서 사용자 지정 도메인에 대한 SSL 인증서 구성

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | EnvironmentType - Azure |
| **참조**              | [Azure App Service에서 앱에 대한 HTTPS를 사용하도록 설정](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **단계** | 기본적으로 Azure는 *.azurewebsites.net 도메인에 대해 와일드카드 인증서를 사용하는 모든 앱에 대해 HTTPS를 이미 사용하도록 설정합니다. 그러나 모든 와일드카드 도메인과 마찬가지로 자체 인증서로 사용자 지정 도메인을 사용하는 것만큼 안전하지 않습니다([참조](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)). 배포된 앱에 액세스할 수 있는 사용자 지정 도메인에 대한 SSL을 사용하도록 설정하는 것이 좋습니다.|

## <a id="appservice-https"></a>Azure App Service에 대한 모든 트래픽이 HTTPS 연결을 통과하도록 강제 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | EnvironmentType - Azure |
| **참조**              | [Azure App Service에서 HTTPS 적용](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **단계** | <p>Azure는 *.azurewebsites.net 도메인에 대해 와일드카드 인증서를 사용하는 Azure 앱 서비스에 대해 HTTPS를 이미 사용하도록 설정하지만 HTTPS를 적용하지는 않습니다. 방문자는 여전히 HTTP를 사용하여 앱에 액세스할 수 있습니다. 이 경우 앱 보안을 손상시킬 수 있으므로 HTTPS를 명시적으로 적용해야 합니다. ASP.NET MVC 애플리케이션에서는 안전하지 않은 HTTP 요청을 HTTPS를 통해 다시 보내도록 하는 [RequireHttps 필터](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx)를 사용해야 합니다.</p><p>또는 Azure App Service에 포함된 URL 다시 쓰기 모듈을 사용하여 HTTPS를 적용할 수 있습니다. URL 다시 쓰기 모듈을 사용하면 개발자가 요청을 애플리케이션으로 전달하기 전에 들어오는 요청에 적용되는 규칙을 정의할 수 있습니다. URL 다시 쓰기 규칙은 애플리케이션 루트에 저장된 web.config 파일에 정의되어 있습니다.</p>|

### <a name="example"></a>예
다음 예제에는 들어오는 모든 트래픽이 강제로 HTTPS를 사용하도록 하는 기본 URL 다시 쓰기 규칙이 포함되어 있습니다.
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
이 규칙은 사용자가 HTTP를 사용하여 페이지를 요청할 때 HTTP 상태 코드 of 301(영구 리디렉션)을 반환하여 작동합니다. 301은 요청을 방문자가 요청한 것과 같은 URL로 리디렉션하지만 요청의 HTTP 부분을 HTTPS로 바꿉니다. 예를 들어 HTTP://contoso.com은 HTTPS://contoso.com으로 리디렉션됩니다. 

## <a id="http-hsts"></a>HSTS(HTTP 엄격한 전송 보안)를 사용하도록 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [OWASP HSTS(HTTP 엄격한 전송 보안) 참고 자료](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet)(영문) |
| **단계** | <p>HSTS는 특수 응답 헤더를 사용하여 웹 애플리케이션에서 지정하는 옵트인 보안 고급 기능입니다. 지원되는 브라우저에서 이 헤더를 받으면 브라우저는 HTTP를 통해 지정된 도메인으로 보내는 모든 통신을 차단하고 대신 HTTPS를 통해 모든 통신을 보냅니다. 또한 브라우저에서 프롬프트를 통해 HTTPS를 클릭하지 않도록 방지합니다.</p><p>HSTS를 구현하려면 코드 또는 구성에서 웹 사이트에 대해 다음 응답 헤더를 전역적으로 구성해야 합니다. Strict-Transport-Security: max-age=300; includeSubDomains HSTS는 다음과 같은 위협을 해결합니다.</p><ul><li>사용자가 책갈피를 설정하거나 수동으로 https://example.com을 입력하고 메시지 가로채기(man-in-the-middle) 공격자의 영향을 받을 수 있습니다. HSTS는 대상 도메인에 대한 HTTP 요청을 HTTPS로 자동으로 리디렉션합니다.</li><li>순전히 HTTPS로 의도된 웹 애플리케이션은 실수로 HTTP 링크를 포함하거나 HTTP를 통해 콘텐츠를 제공합니다. HSTS는 대상 도메인에 대한 HTTP 요청을 HTTPS로 자동으로 리디렉션합니다.</li><li>메시지 가로채기 공격자가 잘못된 인증서를 사용하여 희생자 사용자의 트래픽을 가로채려고 시도하고 사용자가 잘못된 인증서를 받아들이기를 기대합니다. HSTS는 사용자가 잘못된 인증서 메시지를 재정의하도록 허용하지 않습니다.</li></ul>|

## <a id="sqlserver-validation"></a>SQL 서버 연결 암호화 및 인증서 유효성 검사 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | SQL Azure  |
| **특성**              | SQL 버전 - V12 |
| **참조**              | [SQL Database의 보안 연결 문자열 작성에 대한 모범 사례](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **단계** | <p>SQL Database와 클라이언트 애플리케이션 간의 모든 통신은 항상 SSL(Secure Sockets Layer)을 사용하여 암호화됩니다. SQL Database는 암호화되지 않은 연결을 지원하지 않습니다. 애플리케이션 코드 또는 도구를 사용하여 인증서의 유효성을 검사하려면 명시적으로 암호화된 연결을 요청하고 서버 인증서를 신뢰하지 않습니다. 애플리케이션 코드 또는 도구가 암호화된 연결을 요청하지 않더라도 암호화된 연결을 받습니다.</p><p>그러나 서버 인증서의 유효성을 검사하지 않고 따라서 "메시지 가로채기" 공격에 노출되기 쉽습니다. ADO.NET 애플리케이션 코드를 사용하여 인증서의 유효성을 검사하려면 데이터베이스 연결 문자열에서 `Encrypt=True` 및 `TrustServerCertificate=False`를 설정합니다. SQL Server Management Studio를 통해 인증서의 유효성을 검사하려면 [서버에 연결] 대화 상자를 엽니다. [연결 속성] 탭에서 [연결 암호화]를 클릭합니다.</p>|

## <a id="encrypted-sqlserver"></a>SQL 서버에 암호화된 통신 강제 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 데이터베이스 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | OnPrem |
| **특성**              | SQL 버전 - MsSQL2016, SQL 버전 - MsSQL2012, SQL 버전 - MsSQL2014 |
| **참조**              | [데이터베이스 엔진에 암호화 연결 사용](https://msdn.microsoft.com/library/ms191192)  |
| **단계** | SSL 암호화를 사용하면 SQL Server의 인스턴스와 애플리케이션 간에 네트워크를 통해 전송되는 데이터의 보안이 강화됩니다. |

## <a id="comm-storage"></a>Azure Storage에 대한 통신이 HTTPS를 통과하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Storage 전송 수준 암호화 - HTTPS 사용](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **단계** | 전송 중인 Azure Storage 데이터의 보안을 유지하려면 REST API를 호출하거나 스토리지의 개체에 액세스할 때 항상 HTTPS 프로토콜을 사용합니다. 또한 Azure Storage 개체에 대한 액세스를 위임하는 데 사용할 수 있는 공유 액세스 서명에는 공유 액세스 서명을 사용할 때 HTTPS 프로토콜만 사용할 수 있도록 지정하여 SAS 토큰이 있는 링크를 보내는 사용자는 모두 적절한 프로토콜을 사용할 수 있도록 하는 옵션이 포함되어 있습니다.|

## <a id="md5-https"></a>HTTPS를 사용할 수 없는 경우 Blob을 다운로드한 후 MD5 해시 유효성 검사

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | StorageType - Blob |
| **참조**              | [Microsoft Azure Blob의 MD5 개요](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/)(영문) |
| **단계** | <p>Microsoft Azure Blob service는 애플리케이션 계층과 전송 계층 모두에서 데이터 무결성을 보장하는 메커니즘을 제공합니다. 어떤 이유로든 HTTPS 대신 HTTP를 사용해야 하고 블록 Blob을 사용하고 있는 경우, MD5 확인을 사용하면 전송되는 Blob의 무결성을 확인할 수 있습니다.</p><p>이렇게 하면 네트워크/전송 계층 오류로부터 보호되지만 항상 중간 공격을 막을 수 있는 것은 아닙니다. 전송 수준 보안을 제공하는 HTTPS를 사용할 수 있는 경우 MD5 확인을 사용하는 것은 중복된 작업으로 불필요합니다.</p>|

## <a id="smb-shares"></a>SMB 3.0 호환 클라이언트를 사용하여 Azure 파일 공유에 대한 전송 중 데이터 암호화 보장

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 모바일 클라이언트 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | StorageType - 파일 |
| **참조**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Windows 클라이언트에 대한 Azure File Storage SMB 지원](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **단계** | Azure File Storage는 REST API를 사용하는 경우 HTTPS를 지원하지만, VM에 연결된 SMB 파일 공유로 사용되는 경우가 더 일반적입니다. SMB 2.1은 암호화를 지원하지 않으므로 Azure의 동일한 지역 내에서만 연결이 허용됩니다. 그러나 SMB 3.0은 암호화를 지원하고, Windows Server 2012 R2, Windows 8, Windows 8.1 및 Windows 10에서 사용할 수 있으며 지역 간 액세스 및 데스크톱에 대한 액세스도 허용합니다. |

## <a id="cert-pinning"></a>인증서 고정 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Storage | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, Windows Phone |
| **특성**              | N/A  |
| **참조**              | [인증서 및 공개 키 고정](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net)(영문) |
| **단계** | <p>인증서 고정은 MITM(메시지 가로채기) 공격을 방어합니다. 고정은 예상되는 X509 인증서 또는 공개 키와 호스트를 연결하는 프로세스입니다. 호스트에 대한 인증서 또는 공개 키를 알고 있거나 확인하면 해당 인증서 또는 공개 키가 호스트에 연결되거나 '고정'됩니다. </p><p>따라서 악의적 사용자가 SSL MITM 공격을 시도하면 SSL 핸드셰이크 중에 공격자 서버의 키가 고정된 인증서 키와 다르고 요청이 무시되므로 ServicePointManager의 `ServerCertificateValidationCallback` 위임을 구현함으로써 MITM 인증서 고정을 방지할 수 있습니다.</p>|

### <a name="example"></a>예
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>HTTPS 사용 설정 - 보안 전송 채널

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **단계** | 애플리케이션 구성에서 중요한 정보에 대한 모든 액세스에 HTTPS가 사용되는지 확인해야 합니다.<ul><li>**설명:** 애플리케이션 중요한 정보를 처리하고 메시지 수준 암호화를 사용하지 않는 경우에는 암호화된 전송 채널을 통해서만 통신할 수 있어야 합니다.</li><li>**권장 사항:** HTTP 전송을 사용하지 않는 대신 HTTPS 전송을 사용하도록 설정합니다. 예를 들어 `<httpTransport/>`를 `<httpsTransport/>` 태그로 바꿉니다. 보안 채널을 통해서만 애플리케이션에 액세스할 수 있도록 하려면 네트워크 구성(방화벽)을 사용하면 안됩니다. 철학적 관점에서 애플리케이션은 보안을 위해 네트워크를 신뢰하지 않아야 합니다.</li></ul><p>실용적 관점에서 네트워크 보안 책임자는 진화하는 애플리케이션의 보안 요구 사항을 항상 추적하지는 않습니다.</p>|

## <a id="message-protection"></a>WCF: 메시지 보안 보호 수준을 EncryptAndSign으로 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **단계** | <ul><li>**설명:** 보호 수준을 "none"으로 설정하면 메시지 보호를 사용하지 않도록 설정됩니다. 적절한 수준의 설정을 통해 기밀성과 무결성을 보장합니다.</li><li>**권장 사항:**<ul><li>`Mode=None`인 경우 - 메시지 보호를 사용하지 않도록 설정</li><li>`Mode=Sign`인 경우 - 메시지를 서명하지만 암호화하지 않습니다. 데이터 무결성이 중요한 경우에 사용해야 합니다.</li><li>`Mode=EncryptAndSign`인 경우 - 메시지를 서명하고 암호화합니다.</li></ul></li></ul><p>기밀성에 대한 우려 없이 정보 무결성의 유효성을 검사하기만 하면 암호화를 해제하고 메시지에 서명하는 것이 좋습니다. 이렇게 하면 원래 보낸 사람의 유효성을 검사해야 하지만 중요한 데이터는 전송되지 않는 작업 또는 서비스 계약에 유용할 수 있습니다. 보호 수준을 낮출 때는 메시지에 PII(개인 식별이 가능한 정보)가 포함되지 않도록 주의합니다.</p>|

### <a name="example"></a>예
다음 예제에서는 메시지 서명만 수행하도록 서비스와 작업을 구성합니다. `ProtectionLevel.Sign`의 서비스 계약 예제: 다음은 서비스 계약 수준에서 ProtectionLevel.Sign을 사용하는 예입니다. 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>예
`ProtectionLevel.Sign`의 작업 계약 예제(세부적 제어의 경우): 다음은 OperationContract 수준에서 `ProtectionLevel.Sign`을 사용하는 예입니다.

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: 최소 권한 계정을 사용하여 WCF 서비스 실행

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **단계** | <ul><li>**설명:** 관리자 또는 권한이 높은 계정으로 WCF 서비스를 실행하면 안됩니다. 서비스가 손상된 경우 중대한 영향을 미칩니다.</li><li>**권장 사항:** 최소 권한 계정을 사용하여 WCF 서비스를 호스팅하면 애플리케이션의 공격에 대한 취약성이 줄어들고 공격을 받을 경우 잠재적 손상을 줄일 수 있습니다. 서비스 계정에 MSMQ, 이벤트 로그, 성능 카운터 및 파일 시스템과 같은 인프라 리소스에 대한 추가 액세스 권한이 필요한 경우 WCF 서비스가 성공적으로 실행될 수 있도록 이러한 리소스에 적절한 권한을 부여해야 합니다.</li></ul><p>서비스가 원래 호출자를 대신하여 특정 리소스에 액세스해야 하는 경우 가장 및 위임을 사용하여 다운스트림 권한 부여 확인을 위해 호출자의 ID를 전달합니다. 개발 시나리오에서는 권한이 제한된 특별한 기본 제공 계정인 로컬 네트워크 서비스 계정을 사용합니다. 프로덕션 시나리오에서는 최소 권한의 사용자 지정 도메인 서비스 계정을 만듭니다.</p>|

## <a id="webapi-https"></a>Web API에 대한 모든 트래픽이 HTTPS 연결을 통과하도록 강제 적용

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC5, MVC6 |
| **특성**              | N/A  |
| **참조**              | [Web API 컨트롤러에서 SSL 적용](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **단계** | 애플리케이션에 HTTPS와 HTTP 바인딩이 모두 있는 경우 클라이언트에서 HTTP를 계속 사용하여 사이트에 액세스할 수 있습니다. 이를 방지하려면 작업 필터를 사용하여 보호된 API에 대한 요청이 항상 HTTPS를 통과하도록 합니다.|

### <a name="example"></a>예 
다음 코드에서는 SSL을 확인하는 Web API 인증 필터를 보여 줍니다. 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
SSL이 필요한 모든 Web API 작업에 이 필터를 추가합니다. 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Azure Cache for Redis에 대한 통신이 SSL을 통과하는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Azure Cache for Redis | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [Azure Redis SSL 지원](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **단계** | Redis 서버는 기본적으로 SSL을 지원하지 않지만 Azure Cache for Redis는 지원합니다. Azure Cache for Redis에 연결 중이며 StackExchange.Redis처럼 클라이언트가 SSL을 지원하는 경우에는 SSL을 사용해야 합니다. 기본적으로 비SSL 포트는 새 Azure Cache for Redis 인스턴스에 대해 사용하지 않도록 설정됩니다. Redis 클라이언트에 대한 SSL 지원에 대한 종속성이 없으면 보안 기본값이 변경되지 않도록 합니다. |

Redis는 신뢰할 수 있는 환경 내에서 신뢰할 수 있는 클라이언트가 액세스할 수 있도록 설계되었습니다. 즉 일반적으로 Redis 인스턴스를 인터넷에 직접 노출하거나 일반적으로 신뢰할 수 없는 클라이언트를 Redis TCP 포트 또는 UNIX 소켓에 직접 액세스할 수 있는 환경에 직접 노출하지 않는 것이 좋습니다. 

## <a id="device-field"></a>디바이스-필드 게이트웨이 통신 보안

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 필드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | IP 기반 디바이스의 경우 통신 프로토콜은 일반적으로 전송 중인 데이터를 보호하기 위해 SSL/TLS 채널에 캡슐화될 수 있습니다. SSL/TLS를 지원하지 않는 다른 프로토콜의 경우 전송 계층 또는 메시지 계층에서 보안을 제공하는 보안 버전의 프로토콜이 있는지 조사합니다. |

## <a id="device-cloud"></a>SSL/TLS를 사용하여 디바이스-클라우드 게이트웨이 통신 보안

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | IoT 클라우드 게이트웨이 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [통신 프로토콜 선택](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **단계** | SSL/TLS를 사용하여 HTTP/AMQP 또는 MQTT 프로토콜 보안을 유지합니다. |
