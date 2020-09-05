---
title: 공유 키 인증을 사용 하 여 REST API 작업 호출
titleSuffix: Discover how to call Azure Storage REST API operations with Shared Key authorization. Get detailed information about each step of the sample operation.
description: Azure Storage REST API를 사용 하 여 공유 키 권한 부여를 통해 Blob 저장소에 대 한 요청을 수행할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2019
ms.author: tamram
ms.reviewer: ozge
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: f569fdac19c4f765828d24f4d6615fdd7bafef8a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010905"
---
# <a name="call-rest-api-operations-with-shared-key-authorization"></a>공유 키 인증을 사용 하 여 REST API 작업 호출

이 문서에서는 인증 헤더를 구성 하는 방법을 비롯 하 여 Azure Storage REST Api를 호출 하는 방법을 보여 줍니다. REST에 대해 알지 못하는 개발자의 관점에서 작성 되었으며 REST 호출을 수행 하는 방법을 알 수 없습니다. REST 작업을 호출 하는 방법을 파악 한 후에는이 정보를 활용 하 여 다른 Azure Storage REST 작업을 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

샘플 응용 프로그램은 저장소 계정에 대 한 blob 컨테이너를 나열 합니다. 이 문서의 코드를 사용해 보려면 다음 항목이 필요합니다.

- **Azure 개발** 워크로드를 사용하여 [Visual Studio 2019](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)를 설치합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 범용 스토리지 계정. 스토리지 계정이 아직 없는 경우 [스토리지 계정 만들기](storage-account-create.md)를 참조하세요.

- 이 문서의 예제는 스토리지 계정의 컨테이너를 나열하는 방법을 보여 줍니다. 출력을 보려면 시작하기 전에 스토리지 계정의 Blob Storage에 컨테이너를 몇 개 추가합니다.

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

애플리케이션 예제는 C#으로 작성된 콘솔 애플리케이션입니다.

[git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-rest-api-with-auth.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. Visual Studio 솔루션을 열려면 storage-dotnet-rest-api-with-auth 폴더를 찾아 열고, StorageRestApiAuth.sln을 두 번 클릭합니다.

## <a name="about-rest"></a>REST 정보

REST는 *Representational State Transfer*을 나타냅니다. 구체적인 정의는 [Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer)를 확인하세요.

REST는 HTTP/HTTPS와 같은 인터넷 프로토콜을 통해 서비스와 상호 작용할 수 있게 해 주는 아키텍처입니다. REST는 서버 또는 클라이언트에서 실행 되는 소프트웨어에 독립적입니다. REST API은 HTTP/HTTPS를 지 원하는 모든 플랫폼에서 호출할 수 있습니다. Mac, Windows, Linux, Android 휴대폰 또는 태블릿, iPhone, iPod, 웹 사이트에서 실행되는 애플리케이션을 작성할 수 있으며 이 모든 플랫폼에 동일한 REST API를 사용할 수 있습니다.

REST API에 대 한 호출은 클라이언트에서 수행 하는 요청과 서비스에서 반환 하는 응답으로 구성 됩니다. 요청에서 호출할 작업에 대 한 정보, 작업할 리소스, 쿼리 매개 변수 및 헤더, 호출 된 작업에 따라 데이터 페이로드가 포함 된 URL을 보냅니다. 서비스의 응답에는 상태 코드, 응답 헤더 집합 및 호출 된 작업에 따라 데이터 페이로드가 포함 됩니다.

## <a name="about-the-sample-application"></a>샘플 애플리케이션 정보

샘플 애플리케이션은 스토리지 계정의 컨테이너를 나열합니다. REST API 설명서의 정보와 실제 코드의 상관 관계를 이해하고 나면 다른 REST 호출은 더 쉽게 이해할 수 있습니다.

[BLOB 서비스 REST API](/rest/api/storageservices/Blob-Service-REST-API)를 보시면, Blob Storage에서 수행할 수 있는 모든 작업이 있습니다. 스토리지 클라이언트 라이브러리는 REST API를 감싸고 이는 래퍼로, REST API를 직접 사용하지 않고도 간단하게 스토리지에 액세스할 수 있게 해줍니다. 하지만 위에서 언급했듯이, 가끔 스토리지 클라이언트 라이브러리 대신 REST API를 사용하는 경우가 있습니다.

## <a name="list-containers-operation"></a>컨테이너 작업 나열

[Listcontainers](/rest/api/storageservices/List-Containers2) 작업에 대 한 참조를 검토 합니다. 이 정보는 요청 및 응답에서 일부 필드가 제공 되는 위치를 이해 하는 데 도움이 됩니다.

**요청 메서드**: GET. 이 동사는 요청 개체의 속성으로 지정되는 HTTP 메서드입니다. 호출하는 API에 따라 이 동사의 다른 값으로 HEAD, PUT 및 DELETE가 포함됩니다.

**요청 URI**: `https://myaccount.blob.core.windows.net/?comp=list` .요청 URI는 blob storage 계정 끝점 `https://myaccount.blob.core.windows.net` 및 리소스 문자열에서 생성 됩니다 `/?comp=list` .

[URI 매개 변수](/rest/api/storageservices/List-Containers2#uri-parameters): ListContainers를 호출할 때 사용할 수 있는 추가 쿼리 매개 변수가 있습니다. 이러한 매개 변수 중 일부는 필터링에 사용되는 호출 *timeout*(초) 및 *prefix*입니다.

또 다른 유용한 매개 변수는 *maxresults:* 입니다. 사용 가능한 컨테이너가 이 값보다 많으면 응답 본문에 다음 요청에서 반환할 그 다음 컨테이너를 나타내는 *NextMarker* 요소가 포함됩니다. 이 기능을 사용하려면 다음 요청을 만들 때 *NextMarker* 값을 URI에 *marker* 매개 변수로 입력합니다. 이 기능을 사용하면 결과 페이징과 비슷한 동작이 발생합니다.

추가 매개 변수를 사용하려면 다음 예제와 같이 값을 사용하여 리소스 문자열에 매개 변수를 추가합니다.

```
/?comp=list&timeout=60&maxresults=100
```

[요청 헤더](/rest/api/storageservices/List-Containers2#request-headers)**:** 이 섹션에는 필수 및 선택적 요청 헤더가 나열됩니다. *Authorization* 헤더, *x-ms-date*(요청의 UTC 시간 포함) 및 *x-ms-version*(사용할 REST API 버전 지정)의 세 가지 헤더가 필요합니다. 헤더에 *x-ms-client-request-id*를 포함하는 것은 선택 사항입니다. 이 필드의 값을 원하는 대로 설정할 수 있으며, 로깅이 사용되면 스토리지 분석 로그에 기록됩니다.

[요청 본문](/rest/api/storageservices/List-Containers2#request-body)**:** ListContainers에 대한 요청 본문이 없습니다. 요청 본문은 BLOB을 업로드할 때 모든 PUT 작업에 사용되며, 적용하려는 저장된 액세스 정책 XML 목록을 보낼 수 있는 SetContainerAccessPolicy에도 사용됩니다. 저장된 액세스 정책은 [SAS(공유 액세스 서명) 사용](storage-sas-overview.md) 문서에서 다룹니다.

[응답 상태 코드](/rest/api/storageservices/List-Containers2#status-code)**:** 개발자가 알아야 하는 상태 코드를 알려줍니다. 이 예제에서는 HTTP 상태 코드 200이 정상입니다. HTTP 상태 코드의 전체 목록은 [상태 코드 정의](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)를 참조하세요. Storage REST API에 대한 오류 코드를 보려면 [공통 REST API 오류 코드](/rest/api/storageservices/common-rest-api-error-codes)를 참조하세요.

[응답 헤더](/rest/api/storageservices/List-Containers2#response-headers)**:** *내용 유형을*포함 합니다. 전달 된 요청 ID 인 *x-y (요청 id)* 입니다. 사용 되는 Blob service 버전을 나타내는 *x m s-버전* 그리고 UTC *날짜*이며 요청이 수행 된 시간을 알려 줍니다.

[응답 본문](/rest/api/storageservices/List-Containers2#response-body):이 필드는 요청한 데이터를 제공하는 XML 구조입니다. 이 예제에서 응답은 컨테이너 및 해당 속성의 목록입니다.

## <a name="creating-the-rest-request"></a>REST 요청 만들기

프로덕션 환경에서 실행 하는 경우 보안을 위해 항상 HTTP 대신 HTTPS를 사용 합니다. 이 연습에서는 목적을 달성하기 위해 HTTP를 사용할 것이므로 요청 및 응답 데이터를 볼 수 있습니다. 실제 REST 호출에서 요청 및 응답 정보를 보려면 [Fiddler](https://www.telerik.com/fiddler) 또는 유사한 애플리케이션을 다운로드합니다. Visual Studio 솔루션에서 저장소 계정 이름과 키는 클래스에 하드 코딩 됩니다. ListContainersAsyncREST 메서드는 저장소 계정 이름과 저장소 계정 키를 REST 요청의 다양 한 구성 요소를 만드는 데 사용 되는 메서드에 전달 합니다. 실제 애플리케이션에서 스토리지 계정 이름과 키는 구성 파일이나 환경 변수에 상주하거나 Azure Key Vault에서 검색할 수 있습니다.

샘플 프로젝트에서 권한 부여 헤더를 만드는 코드는 별도의 클래스에 있습니다. 그 이유는 전체 클래스를 사용 하 여 솔루션에 추가 하 고 "있는 그대로" 사용할 수 있다는 것입니다. 인증 헤더 코드는 Azure Storage에 대한 대부분의 REST API 호출에 사용할 수 있습니다.

HttpRequestMessage 개체인 요청을 만들려면 Program.cs에서 ListContainersAsyncREST로 이동합니다. 요청을 작성하는 단계:

- 서비스 호출에 사용할 URI를 만듭니다.
- HttpRequestMessage 개체를 만들고 페이로드를 설정합니다. 아직 아무 것도 전달되지 않기 때문에 페이로드 ListContainersAsyncREST에 대한 페이로드는 null입니다.
- x-ms-date 및 x-ms-version에 대한 요청 헤더를 추가합니다.
- 인증 헤더를 가져와서 추가합니다.

필요한 몇 가지 기본 정보:

- ListContainers의 경우 **메서드**는 `GET`입니다. 이 값은 요청을 인스턴스화할 때 설정됩니다.
- **리소스**는 URI에서 호출되는 API를 나타내는 쿼리 부분이며, 값은 `/?comp=list`입니다. 앞서 언급했듯이, 리소스는 [ListContainers API](/rest/api/storageservices/List-Containers2)에 대한 정보를 표시하는 참조 설명서 페이지에 있습니다.
- URI는 해당 스토리지 계정에 대한 Blob service 엔드포인트를 만들고 리소스를 연결하여 구성됩니다. **요청 URI**의 값은 결국 `http://contosorest.blob.core.windows.net/?comp=list`입니다.
- ListContainers의 경우 **requestBody**가 null이고 추가 **헤더**는 없습니다.

여러 API가 *ifMatch*처럼 다른 매개 변수를 전달할 수 있습니다. 예를 들어 PutBlob을 호출할 때 IfMatch를 사용할 수 있습니다. 이 경우 ifMatch를 eTag로 설정하면 개발자가 입력한 eTag가 BLOB의 현재 eTag와 일치하면 BLOB만 업데이트합니다. 다른 사람이 eTag를 검색한 후 BLOB을 업데이트하면 변경 내용이 재정의되지 않습니다.

먼저 `uri` 및 `payload`를 설정합니다.

```csharp
// Construct the URI. It will look like this:
//   https://myaccount.blob.core.windows.net/resource
String uri = string.Format("http://{0}.blob.core.windows.net?comp=list", storageAccountName);

// Provide the appropriate payload, in this case null.
//   we're not passing anything in.
Byte[] requestPayload = null;
```

다음으로 요청을 인스턴스화하고, 메서드를 `GET`으로 설정하고, URI를 입력합니다.

```csharp
// Instantiate the request message with a null payload.
using (var httpRequestMessage = new HttpRequestMessage(HttpMethod.Get, uri)
{ Content = (requestPayload == null) ? null : new ByteArrayContent(requestPayload) })
{
```

및에 대 한 요청 헤더를 추가 `x-ms-date` `x-ms-version` 합니다. 코드의 이 위치는 호출에 필요한 추가 요청 헤더를 추가하는 위치이기도 합니다. 이 예에는 추가 헤더가 없습니다. 추가 헤더를 전달 하는 API의 예로는 컨테이너 ACL 설정 작업이 있습니다. 이 API 호출은 "x-y-공용-액세스" 라는 헤더와 액세스 수준에 대 한 값을 추가 합니다.

```csharp
// Add the request headers for x-ms-date and x-ms-version.
DateTime now = DateTime.UtcNow;
httpRequestMessage.Headers.Add("x-ms-date", now.ToString("R", CultureInfo.InvariantCulture));
httpRequestMessage.Headers.Add("x-ms-version", "2017-07-29");
// If you need any additional headers, add them here before creating
//   the authorization header.
```

인증 헤더를 만드는 메서드를 호출하고 요청 헤더에 추가합니다. 인증 헤더를 만드는 방법은 문서 뒷부분에서 살펴보겠습니다. 메서드 이름은 GetAuthorizationHeader이고, 이 코드 조각에서 볼 수 있습니다.

```csharp
// Get the authorization header and add it.
httpRequestMessage.Headers.Authorization = AzureStorageAuthenticationHelper.GetAuthorizationHeader(
    storageAccountName, storageAccountKey, now, httpRequestMessage);
```

이 시점에 `httpRequestMessage`에는 인증 헤더를 완전히 갖춘 REST 요청이 포함되어 있습니다.

## <a name="send-the-request"></a>요청 보내기

이제 요청을 생성 했으므로 SendAsync 메서드를 호출 하 여 Azure Storage로 보낼 수 있습니다. 응답 상태 코드의 값이 200 인지 확인 합니다. 즉, 작업이 성공 했음을 의미 합니다. 그런 다음 응답을 구문 분석 합니다. 이 예에서는 컨테이너의 XML 목록을 가져옵니다. 요청을 만들고 실행한 다음, 컨테이너 목록에 대한 응답을 검사하려면 GetRESTRequest 메서드를 호출하는 코드를 살펴보겠습니다.

```csharp
    // Send the request.
    using (HttpResponseMessage httpResponseMessage =
      await new HttpClient().SendAsync(httpRequestMessage, cancellationToken))
    {
        // If successful (status code = 200),
        //   parse the XML response for the container names.
        if (httpResponseMessage.StatusCode == HttpStatusCode.OK)
        {
            String xmlString = await httpResponseMessage.Content.ReadAsStringAsync();
            XElement x = XElement.Parse(xmlString);
            foreach (XElement container in x.Element("Containers").Elements("Container"))
            {
                Console.WriteLine("Container name = {0}", container.Element("Name").Value);
            }
        }
    }
}
```

SendAsync 호출을 만들 때 [Fiddler](https://www.telerik.com/fiddler) 같은 네트워크 감지기를 실행하면 요청 및 응답 정보를 볼 수 있습니다. 살펴보겠습니다. 스토리지 계정의 이름은 *contosorest*입니다.

**요청:**

```
GET /?comp=list HTTP/1.1
```

**요청 헤더:**

```
x-ms-date: Thu, 16 Nov 2017 23:34:04 GMT
x-ms-version: 2014-02-14
Authorization: SharedKey contosorest:1dVlYJWWJAOSHTCPGiwdX1rOS8B4fenYP/VrU0LfzQk=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**실행 후 반환된 상태 코드 및 응답 헤더:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 3e889876-001e-0039-6a3a-5f4396000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 00:23:42 GMT
Content-Length: 1511
```

**응답 본문 (XML):** 컨테이너 나열 작업의 경우 컨테이너 및 해당 속성의 목록이 표시 됩니다.

```xml  
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
  ServiceEndpoint="http://contosorest.blob.core.windows.net/">
  <Containers>
    <Container>
      <Name>container-1</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:39:48 GMT</Last-Modified>
        <Etag>"0x8D46CBD5A7C301D"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-2</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:40:50 GMT</Last-Modified>
        <Etag>"0x8D46CBD7F49E9BD"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-3</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:10 GMT</Last-Modified>
        <Etag>"0x8D46CBD8B243D68"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
      </Properties>
    </Container>
    <Container>
      <Name>container-4</Name>
      <Properties>
        <Last-Modified>Thu, 16 Mar 2017 22:41:25 GMT</Last-Modified>
        <Etag>"0x8D46CBD93FED46F"</Etag>
        <LeaseStatus>unlocked</LeaseStatus>
        <LeaseState>available</LeaseState>
        </Properties>
      </Container>
      <Container>
        <Name>container-5</Name>
        <Properties>
          <Last-Modified>Thu, 16 Mar 2017 22:41:39 GMT</Last-Modified>
          <Etag>"0x8D46CBD9C762815"</Etag>
          <LeaseStatus>unlocked</LeaseStatus>
          <LeaseState>available</LeaseState>
        </Properties>
      </Container>
  </Containers>
  <NextMarker />
</EnumerationResults>
```

요청을 만들고, 서비스를 호출하고, 결과를 구문 분석하는 방법을 이해했으니, 지금부터는 인증 헤더를 만드는 방법을 살펴보겠습니다. 인증 헤더를 만드는 작업은 복잡하지만, 다행히도 일단 코드가 정상적으로 작동한다면 그 코드는 모든 Storage Service REST API에 대해 작동합니다.

## <a name="creating-the-authorization-header"></a>인증 헤더 만들기

> [!TIP]
> 이제 Azure Storage는 blob 및 큐에 대 한 Azure Active Directory (Azure AD) 통합을 지원 합니다. Azure AD는 Azure Storage에 대한 요청에 권한을 부여하는 훨씬 간단한 환경을 제공합니다. Azure AD를 사용 하 여 REST 작업에 권한을 부여 하는 방법에 대 한 자세한 내용은 [Azure Active Directory 권한 부여](/rest/api/storageservices/authorize-with-azure-active-directory)를 참조 하세요. Azure Storage와의 Azure AD 통합에 대 한 개요는 [Azure Active Directory를 사용 하 여 Azure Storage에 대 한 액세스 인증](storage-auth-aad.md)을 참조 하세요.

[Azure Storage 요청에 권한을 부여](/rest/api/storageservices/authorize-requests-to-azure-storage)하는 방법 (코드 없음)에 대해 설명 하는 문서가 있습니다.

이 문서에서 필요한 내용만 추리고 코드를 살펴보겠습니다.

먼저 공유 키 인증을 사용 합니다. 인증 헤더 형식은 다음과 같습니다.

```  
Authorization="SharedKey <storage account name>:<signature>"  
```

서명 필드는 요청에서 생성되어 SHA256 알고리즘을 사용하여 계산된 후 Base64 인코딩을 사용하여 인코딩되는 해시 기반 메시지 인증 코드(HMAC)입니다. 무슨 말인지 이해하셨나요? (여기서는 아직 *정형화* 된 단어를 아직 듣지 않았습니다.)

이 코드 조각은 공유 키 서명 문자열의 형식을 보여줍니다.

```csharp  
StringToSign = VERB + "\n" +  
               Content-Encoding + "\n" +  
               Content-Language + "\n" +  
               Content-Length + "\n" +  
               Content-MD5 + "\n" +  
               Content-Type + "\n" +  
               Date + "\n" +  
               If-Modified-Since + "\n" +  
               If-Match + "\n" +  
               If-None-Match + "\n" +  
               If-Unmodified-Since + "\n" +  
               Range + "\n" +  
               CanonicalizedHeaders +  
               CanonicalizedResource;  
```

대부분의 이러한 필드는 거의 사용되지 않습니다. Blob Storage의 경우 VERB, md5, 콘텐츠 길이, 정식화 헤더 및 정식화 리소스를 지정합니다. 다른 항목은 비워 두어도 됩니다(하지만 비어 있다는 것을 알 수 있도록 `\n` 삽입).

CanonicalizedHeaders 및 CanonicalizedResource란 무엇인가요? 좋은 질문입니다. 정식화의 의미가 무엇일까요? Microsoft Word에서는 이 단어를 인지하지도 못합니다. 다음은 [정규화에 대 한 정보](https://en.wikipedia.org/wiki/Canonicalization)를 제공 합니다. *컴퓨터 과학에서 정형화 (경우에 따라 표준화 또는 정규화)는 둘 이상의 표현이 있는 데이터를 "표준", "보통" 또는 정규 형식으로 변환 하는 프로세스입니다* . 다시 말해서, 항목 목록(예: 정식화 헤더인 경우에는 헤더)을 가져와서 필요한 형식으로 표준화하는 것입니다. 기본적으로 Microsoft에서 형식을 결정하면 개발자가 그에 맞춰야 합니다.

인증 헤더를 만드는 데 필요한 두 정식화 필드부터 알아보겠습니다.

### <a name="canonicalized-headers"></a>정식화 헤더

이 값을 만들려면 "x-ms-"로 시작하는 헤더를 검색하고 정렬한 다음 `[key:value\n]` 인스턴스 문자열로 포맷하고, 하나의 문자열로 연결합니다. 이 예제의 정식화 헤더는 다음과 같습니다.

```
x-ms-date:Fri, 17 Nov 2017 00:44:48 GMT\nx-ms-version:2017-07-29\n
```

다음은 해당 출력을 만드는 데 사용된 코드입니다.

```csharp
private static string GetCanonicalizedHeaders(HttpRequestMessage httpRequestMessage)
{
    var headers = from kvp in httpRequestMessage.Headers
        where kvp.Key.StartsWith("x-ms-", StringComparison.OrdinalIgnoreCase)
        orderby kvp.Key
        select new { Key = kvp.Key.ToLowerInvariant(), kvp.Value };

    StringBuilder headersBuilder = new StringBuilder();

    // Create the string in the right format; this is what makes the headers "canonicalized" --
    //   it means put in a standard format. https://en.wikipedia.org/wiki/Canonicalization
    foreach (var kvp in headers)
    {
        headersBuilder.Append(kvp.Key);
        char separator = ':';

        // Get the value for each header, strip out \r\n if found, then append it with the key.
        foreach (string headerValue in kvp.Value)
        {
            string trimmedValue = headerValue.TrimStart().Replace("\r\n", string.Empty);
            headersBuilder.Append(separator).Append(trimmedValue);

            // Set this to a comma; this will only be used
            // if there are multiple values for one of the headers.
            separator = ',';
        }

        headersBuilder.Append("\n");
    }

    return headersBuilder.ToString();
}
```

### <a name="canonicalized-resource"></a>정형화 리소스

서명 문자열의 이 부분은 요청의 대상이 되는 스토리지 계정을 나타냅니다. 요청 URI는 `<http://contosorest.blob.core.windows.net/?comp=list>`이며, 실제 계정 이름(이 예에서는 `contosorest`)을 사용합니다. 이 예에서는 다음과 같은 결과가 반환되었습니다.

```
/contosorest/\ncomp:list
```

쿼리 매개 변수가 있는 경우이 예제에도 해당 매개 변수가 포함 됩니다. 다음은 값이 여러 개인 추가 쿼리 매개 변수 및 쿼리 매개 변수를 처리하는 코드입니다. 모든 REST Api에 대해 작동 하도록이 코드를 작성 하 고 있음을 명심 하세요. ListContainers 메서드가 모두 필요 하지 않은 경우에도 모든 가능성을 포함 하려고 합니다.

```csharp
private static string GetCanonicalizedResource(Uri address, string storageAccountName)
{
    // The absolute path will be "/" because for we're getting a list of containers.
    StringBuilder sb = new StringBuilder("/").Append(storageAccountName).Append(address.AbsolutePath);

    // Address.Query is the resource, such as "?comp=list".
    // This ends up with a NameValueCollection with 1 entry having key=comp, value=list.
    // It will have more entries if you have more query parameters.
    NameValueCollection values = HttpUtility.ParseQueryString(address.Query);

    foreach (var item in values.AllKeys.OrderBy(k => k))
    {
        sb.Append('\n').Append(item.ToLower()).Append(':').Append(values[item]);
    }

    return sb.ToString();
}
```

정식화 문자열을 설정했으니, 이번에는 인증 헤더 자체를 만드는 방법을 살펴보겠습니다. 먼저 이 문서의 앞부분에서 표시한 StringToSign 형식으로 메시지 서명 문자열을 만듭니다. 이 개념은 코드에 주석을 사용하여 쉽게 설명할 수 있으며, 여기 이것은 인증 헤더를 반환하는 최종 메서드입니다.

```csharp
internal static AuthenticationHeaderValue GetAuthorizationHeader(
    string storageAccountName, string storageAccountKey, DateTime now,
    HttpRequestMessage httpRequestMessage, string ifMatch = "", string md5 = "")
{
    // This is the raw representation of the message signature.
    HttpMethod method = httpRequestMessage.Method;
    String MessageSignature = String.Format("{0}\n\n\n{1}\n{5}\n\n\n\n{2}\n\n\n\n{3}{4}",
                method.ToString(),
                (method == HttpMethod.Get || method == HttpMethod.Head) ? String.Empty
                  : httpRequestMessage.Content.Headers.ContentLength.ToString(),
                ifMatch,
                GetCanonicalizedHeaders(httpRequestMessage),
                GetCanonicalizedResource(httpRequestMessage.RequestUri, storageAccountName),
                md5);

    // Now turn it into a byte array.
    byte[] SignatureBytes = Encoding.UTF8.GetBytes(MessageSignature);

    // Create the HMACSHA256 version of the storage key.
    HMACSHA256 SHA256 = new HMACSHA256(Convert.FromBase64String(storageAccountKey));

    // Compute the hash of the SignatureBytes and convert it to a base64 string.
    string signature = Convert.ToBase64String(SHA256.ComputeHash(SignatureBytes));

    // This is the actual header that will be added to the list of request headers.
    AuthenticationHeaderValue authHV = new AuthenticationHeaderValue("SharedKey",
        storageAccountName + ":" + signature);
    return authHV;
}
```

이 코드를 실행 하는 경우 결과 MessageSignature은 다음 예제와 같습니다.

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 01:07:37 GMT\nx-ms-version:2017-07-29\n/contosorest/\ncomp:list
```

다음은 AuthorizationHeader에 대한 최종 값입니다.

```
SharedKey contosorest:Ms5sfwkA8nqTRw7Uury4MPHqM6Rj2nfgbYNvUKOa67w=
```

AuthorizationHeader는 응답을 게시하기 전에 요청 헤더에 배치되는 마지막 헤더입니다.

여기에는 저장소 서비스 REST Api를 호출 하는 요청을 만들 수 있는 클래스를 포함 하는 데 필요한 모든 것이 포함 되어 있습니다.

## <a name="example-list-blobs"></a>예: blob 나열

컨테이너 *컨테이너-1*에 대 한 blob 목록 작업을 호출 하는 코드를 변경 하는 방법을 살펴보겠습니다. 이 코드는 컨테이너를 나열 하는 코드와 거의 동일 하며, URI 및 응답을 구문 분석 하는 방법만 다릅니다.

[ListBlobs](/rest/api/storageservices/List-Blobs)에 대한 참조 설명서를 보시면 메서드는 *GET*이고 RequestURI는 다음과 같습니다.

```
https://myaccount.blob.core.windows.net/container-1?restype=container&comp=list
```

ListContainersAsyncREST에서 URI를 설정하는 코드를 ListBlobs에 대한 API로 변경합니다. 컨테이너 이름은 **container-1**입니다.

```csharp
String uri =
    string.Format("http://{0}.blob.core.windows.net/container-1?restype=container&comp=list",
      storageAccountName);

```

그런 다음 응답을 처리할 때 컨테이너 대신 BLOB을 검색하도록 코드를 변경합니다.

```csharp
foreach (XElement container in x.Element("Blobs").Elements("Blob"))
{
    Console.WriteLine("Blob name = {0}", container.Element("Name").Value);
}
```

이 샘플을 실행하면 다음과 같은 결과가 반환됩니다.

**정형화 헤더:**

```
x-ms-date:Fri, 17 Nov 2017 05:16:48 GMT\nx-ms-version:2017-07-29\n
```

**정형화 리소스:**

```
/contosorest/container-1\ncomp:list\nrestype:container
```

**메시지 서명:**

```
GET\n\n\n\n\n\n\n\n\n\n\n\nx-ms-date:Fri, 17 Nov 2017 05:16:48 GMT
  \nx-ms-version:2017-07-29\n/contosorest/container-1\ncomp:list\nrestype:container
```

**권한 부여 헤더:**

```
SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
```

다음 값은 [Fiddler](https://www.telerik.com/fiddler)에서 얻습니다.

**요청:**

```
GET http://contosorest.blob.core.windows.net/container-1?restype=container&comp=list HTTP/1.1
```

**요청 헤더:**

```
x-ms-date: Fri, 17 Nov 2017 05:16:48 GMT
x-ms-version: 2017-07-29
Authorization: SharedKey contosorest:uzvWZN1WUIv2LYC6e3En10/7EIQJ5X9KtFQqrZkxi6s=
Host: contosorest.blob.core.windows.net
Connection: Keep-Alive
```

**실행 후 반환된 상태 코드 및 응답 헤더:**

```
HTTP/1.1 200 OK
Content-Type: application/xml
Server: Windows-Azure-Blob/1.0 Microsoft-HTTPAPI/2.0
x-ms-request-id: 7e9316da-001e-0037-4063-5faf9d000000
x-ms-version: 2017-07-29
Date: Fri, 17 Nov 2017 05:20:21 GMT
Content-Length: 1135
```

**응답 본문(XML):** 이 XML 응답은 BLOB 및 해당 속성의 목록을 보여 줍니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<EnumerationResults
    ServiceEndpoint="http://contosorest.blob.core.windows.net/" ContainerName="container-1">
    <Blobs>
        <Blob>
            <Name>DogInCatTree.png</Name>
            <Properties><Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
            <Etag>0x8D52D5C4A4C96B0</Etag>
            <Content-Length>419416</Content-Length>
            <Content-Type>image/png</Content-Type>
            <Content-Encoding />
            <Content-Language />
            <Content-MD5 />
            <Cache-Control />
            <Content-Disposition />
            <BlobType>BlockBlob</BlobType>
            <LeaseStatus>unlocked</LeaseStatus>
            <LeaseState>available</LeaseState>
            <ServerEncrypted>true</ServerEncrypted>
            </Properties>
        </Blob>
        <Blob>
            <Name>GuyEyeingOreos.png</Name>
            <Properties>
                <Last-Modified>Fri, 17 Nov 2017 01:41:14 GMT</Last-Modified>
                <Etag>0x8D52D5C4A25A6F6</Etag>
                <Content-Length>167464</Content-Length>
                <Content-Type>image/png</Content-Type>
                <Content-Encoding />
                <Content-Language />
                <Content-MD5 />
                <Cache-Control />
                <Content-Disposition />
                <BlobType>BlockBlob</BlobType>
                <LeaseStatus>unlocked</LeaseStatus>
                <LeaseState>available</LeaseState>
                <ServerEncrypted>true</ServerEncrypted>
            </Properties>
            </Blob>
        </Blobs>
    <NextMarker />
</EnumerationResults>
```

## <a name="summary"></a>요약

이 문서에서는 REST API blob 저장소에 대 한 요청을 수행 하는 방법을 알아보았습니다. 요청을 사용 하 여 컨테이너의 목록이 나 컨테이너의 blob 목록을 검색할 수 있습니다. REST API 호출에 대 한 권한 부여 서명과 REST 요청에서 사용 하는 방법을 배웠습니다. 마지막으로 응답을 검사 하는 방법을 배웠습니다.

## <a name="next-steps"></a>다음 단계

- [Blob Service REST API](/rest/api/storageservices/blob-service-rest-api)
- [File Service REST API](/rest/api/storageservices/file-service-rest-api)
- [Queue Service REST API](/rest/api/storageservices/queue-service-rest-api)
- [Table Service REST API](/rest/api/storageservices/table-service-rest-api)
