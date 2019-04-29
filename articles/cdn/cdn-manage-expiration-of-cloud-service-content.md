---
title: Azure CDN에서 웹 콘텐츠의 만료 관리 | Microsoft Docs
description: Azure CDN에서 Azure Web Apps/Cloud Services, ASP.NET 또는 IIS 콘텐츠의 만료를 관리하는 방법을 알아봅니다.
services: cdn
documentationcenter: .NET
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2018
ms.author: magattus
ms.openlocfilehash: c21ae227d74442be5701dd906180392b1e0fdf8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60636708"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>Azure CDN에서 웹 콘텐츠의 만료 관리
> [!div class="op_single_selector"]
> * [Azure 웹 콘텐츠](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

TTL(time-to-live)이 경과할 때까지 원본 웹 서버에서 공개적으로 액세스할 수 있는 파일은 Azure CDN(콘텐츠 전송 네트워크)에 캐시될 수 있습니다. TTL은 원본 서버의 HTTP 응답에 있는 `Cache-Control` 헤더를 기반으로 결정됩니다. 이 문서에서는 Microsoft Azure App Service, Azure Cloud Services, ASP.NET 애플리케이션 및 IIS(인터넷 정보 서비스) 사이트에 대한 웹앱 기능의 `Cache-Control` 헤더를 설정하는 방법을 설명하며, 이 헤더들은 모두 비슷하게 구성됩니다. 구성 파일을 사용하거나 프로그래밍 방식으로 `Cache-Control` 헤더를 설정할 수 있습니다. 

[CDN 캐시 규칙](cdn-caching-rules.md)을 설정하여 Azure Portal에서 캐시 설정을 제어할 수도 있습니다. 하나 이상의 캐싱 규칙을 만들고 캐싱 동작을 **재정의** 또는 **캐시 무시**로 설정하는 경우 이 문서에 설명된 원본 제공 캐싱 설정이 무시됩니다. 일반적인 캐싱 개념에 대한 자세한 내용은 [캐싱 동작 방식](cdn-how-caching-works.md)을 참조하세요.

> [!TIP]
> 파일에 TTL을 설정하지 않을 수도 있습니다. 이 경우 Azure CDN은 사용자가 Azure Portal에서 캐싱 규칙을 설정하지 않은 한, 7일의 기본 TTL을 자동으로 적용합니다. 이 기본 TTL은 일반 웹 배달 최적화에만 적용됩니다. 대용량 파일 최적화의 경우 기본 TTL은 1일이고 미디어 스트리밍 최적화의 경우 기본 TTL은 1년입니다.
> 
> 파일과 다른 리소스에 대한 액세스 속도를 높이기 위해 Azure CDN이 작동하는 방법에 대한 자세한 내용은 [Azure CDN(Content Delivery Network) 개요](cdn-overview.md)를 참조하세요.
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN 캐싱 규칙을 사용하여 Cache-Control 헤더 설정
웹 서버 `Cache-Control` 헤더를 설정하기 위한 기본 방법은 Azure Portal에서 캐싱 규칙을 사용하는 것입니다. CDN 캐싱 규칙에 대한 자세한 내용은 [캐싱 규칙을 사용하여 Azure CDN 캐싱 동작 제어](cdn-caching-rules.md)를 참조하세요.

> [!NOTE] 
> 캐싱 규칙은 **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필에만 사용할 수 있습니다. **Verizon의 Azure CDN 프리미엄** 프로필은 유사한 기능을 위해 **관리** 포털에서 [Azure CDN 규칙 엔진](cdn-rules-engine.md)을 사용해야 합니다.

**CDN 캐싱 규칙 페이지로 이동하려면**:

1. Azure Portal에서 CDN 프로필을 선택한 다음, 웹 서버용 엔드포인트를 선택합니다.

1. 설정 아래의 왼쪽 창에서 **캐싱 규칙**을 선택합니다.

   ![CDN 캐싱 규칙 단추](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   **캐싱 규칙** 페이지가 나타납니다.

   ![CDN 캐싱 페이지](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**전역 캐싱 규칙을 사용하여 웹 서버의 Cache-Control 헤더를 설정하려면:**

1. **전역 캐싱 규칙**에서 **쿼리 문자열 캐시 동작**을 **쿼리 문자열 무시**로 설정하고 **캐싱 동작**을 **재정의**로 설정합니다.
      
1. **캐시 만료 기간**은 **초** 상자에 3600초를 입력하거나 **시간** 상자에 1시간을 입력합니다. 

   ![CDN 전역 캐싱 규칙 예](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   해당 전역 캐싱 규칙은 1시간의 캐시 기간을 설정하고 엔드포인트에 대한 모든 요청에 영향을 줍니다. 엔드포인트에서 지정한 원본 서버가 보낸 `Cache-Control` 또는 `Expires` HTTP 헤더를 재정의합니다.   

1. **저장**을 선택합니다.

**사용자 지정 캐싱 규칙을 사용하여 웹 서버 파일의 Cache-Control 헤더를 설정하려면:**

1. **사용자 지정 캐싱 규칙**에 따라 일치 조건 두 개를 만듭니다.

     a. 첫 번째 일치 조건의 경우 **일치 조건**을 **경로**로 설정하고 **일치 값**으로 `/webfolder1/*`을 입력합니다. **캐싱 동작**을 **재정의**로 설정하고 **시간** 상자에 4시간을 입력합니다.

     b. 두 번째 일치 조건의 경우 **일치 조건**을 **경로**로 설정하고 **일치 값**으로 `/webfolder1/file1.txt`를 입력합니다. **캐싱 동작**을 **재정의**로 설정하고 **시간** 상자에 2시간을 입력합니다.

    ![CDN 사용자 지정 캐싱 규칙 예](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    첫 번째 사용자 지정 캐싱 규칙은 엔드포인트에서 지정한 원본 서버의 `/webfolder1`폴더 내 모든 파일에 대해 4시간의 캐시 기간을 설정합니다. 두 번째 규칙은 `file1.txt` 파일을 위한 첫 번째 규칙을 재정의하고 이에 대해 2시간의 캐시 기간을 설정합니다.

1. **저장**을 선택합니다.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>구성 파일을 사용하여 Cache-Control 헤더 설정
이미지, 스타일 시트 등 정적 콘텐츠에 대해서는 웹 애플리케이션의 **applicationHost.config** 또는 **Web.config** 구성 파일을 수정함으로써 업데이트 빈도를 제어할 수 있습니다. 콘텐츠에 대한 `Cache-Control` 헤더를 설정하려면 각 파일의 `<system.webServer>/<staticContent>/<clientCache>` 요소를 사용합니다.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config 파일 사용
**ApplicationHost.config** 파일은 IIS 구성 시스템의 루트 파일입니다. **ApplicationHost.config** 파일의 구성 설정은 사이트의 모든 애플리케이션에 영향을 미치지만 웹 애플리케이션에 대해 존재하는 **Web.config** 파일의 설정에 의해 재정의됩니다.

### <a name="using-webconfig-files"></a>Web.config 파일 사용
**Web.config** 파일을 사용하여 전체 웹 애플리케이션 또는 웹 애플리케이션에서 특정 디렉터리가 작동하는 방식을 사용자 지정할 수 있습니다. 일반적으로 웹 애플리케이션의 루트 폴더에 하나 이상의 **Web.config** 파일이 있습니다. 특정 폴더에 있는 각 **Web.config** 파일의 구성 설정은 다른 **Web.config** 파일에 의해 하위 폴더 수준에서 재정의된 경우를 제외하고는 해당 폴더 및 그 하위 폴더의 모든 항목에 영향을 줍니다. 

예를 들어 3일 동안 웹 애플리케이션의 모든 정적 콘텐츠를 캐시하도록 웹 애플리케이션 루트 폴더의 **Web.config** 파일에 `<clientCache>` 요소를 설정할 수 있습니다. 또한 가변 콘텐츠(예: `\frequent`)가 더 많이 포함된 하위 폴더에 **Web.config** 파일을 추가하고 6시간 동안 하위 폴더의 콘텐츠를 캐시하도록 `<clientCache>` 요소를 설정할 수도 있습니다. 그 결과 전체 웹 사이트의 콘텐츠는 3일 동안 캐시되지만 `\frequent` 디렉터리의 콘텐츠는 6시간 동안만 캐시됩니다.  

다음 XML 구성 파일 예제에서는 최대 기간을 3일로 지정하도록 `<clientCache>` 요소를 설정하는 방법을 보여 줍니다.  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

**cacheControlMaxAge** 특성을 사용하려면 **cacheControlMode** 특성의 값을 `UseMaxAge`로 설정해야 합니다. 이 설정으로 인해 HTTP 헤더 및 지시문, `Cache-Control: max-age=<nnn>`이 응답에 추가됩니다. **cacheControlMaxAge** 특성에 대한 시간 범위 값의 형식은 `<days>.<hours>:<min>:<sec>`입니다. 해당 값은 초로 변환되고 `Cache-Control` `max-age` 지시문의 값으로 사용됩니다. 에 대 한 자세한 내용은 합니다 `<clientCache>` 요소를 참조 하세요 [클라이언트 캐시 \<clientCache >](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)합니다.  

## <a name="setting-cache-control-headers-programmatically"></a>프로그래밍 방식으로 Cache-Control 헤더 설정
ASP.NET 애플리케이션의 경우 .NET API의 **HttpResponse.Cache** 속성을 설정하면 CDN 캐싱 동작을 프로그래밍 방식으로 제어합니다. **HttpResponse.Cache** 속성에 대한 자세한 내용은 [HttpResponse.Cache 속성](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 및 [HttpCachePolicy 클래스](/dotnet/api/system.web.httpcachepolicy)를 참조하세요.  

ASP.NET에서 프로그래밍 방식으로 애플리케이션 콘텐츠를 캐시하려면 다음 단계를 따릅니다.
   1. `HttpCacheability`를 `Public`으로 설정하여 콘텐츠를 캐시 가능하게 표시합니다. 
   1. 다음 `HttpCachePolicy` 메서드 중 하나를 호출하여 캐시 유효성 검사기를 설정합니다.
      - `Last-Modified` 헤더의 타임스탬프 값을 설정하려면 `SetLastModified`를 호출합니다.
      - `ETag` 헤더의 값을 설정하려면 `SetETag`를 호출합니다.
   1. 필요에 따라 `SetExpires`를 호출하여 캐시 만료 시간을 지정하여 `Expires` 헤더의 값을 설정합니다. 그렇지 않은 경우 이 문서의 앞 부분에서 설명한 기본 캐시 추론이 적용됩니다.

예를 들어 1시간 동안의 콘텐츠를 캐시하려면 다음 C# 코드를 추가합니다.  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>Cache-Control 헤더 테스트
웹 콘텐츠의 TTL 설정을 쉽게 확인할 수 있습니다. 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)를 사용하여 웹 콘텐츠에 `Cache-Control` 응답 헤더가 포함되어 있는지 테스트합니다. **wget**, [Postman](https://www.getpostman.com/) 또는 [Fiddler](https://www.telerik.com/fiddler)와 같은 도구를 사용하여 응답 헤더를 검사할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
* [**clientCache** 요소에 대한 자세한 내용](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [**HttpResponse.Cache** 속성에 대한 자세한 내용](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [**HttpCachePolicy 클래스**에 대한 자세한 내용](/dotnet/api/system.web.httpcachepolicy)  
* [캐싱 개념에 대해 알아보기](cdn-how-caching-works.md)
