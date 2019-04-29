---
title: 예외 관리 - Microsoft 위협 모델링 도구 - Azure | Microsoft Docs
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
ms.openlocfilehash: 7d881454eb857080f1178f228a1f7bec36cae178
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610703"
---
# <a name="security-frame-exception-management--mitigations"></a>보안 프레임: 예외 관리 | 완화 방법 
| 제품/서비스 | 문서 |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - 구성 파일에 serviceDebug 노드를 포함하지 않음](#servicedebug)</li><li>[WCF - 구성 파일에 serviceMetadata 노드를 포함하지 않음](#servicemetadata)</li></ul> |
| **앱 API** | <ul><li>[ASP.NET Web API에서 적절 한 예외 처리가 수행 되었는지 확인](#exception)</li></ul> |
| **웹 애플리케이션** | <ul><li>[오류 메시지에 보안 정보를 노출 하지 마십시오](#messages)</li><li>[기본 오류 처리 페이지 구현](#default)</li><li>[IIS에서 배포 방법을 일반 정품으로 설정](#deployment)</li><li>[안전한 예외 실패](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - 구성 파일에 serviceDebug 노드를 포함하지 않음

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반, .NET Framework 3 |
| **특성**              | N/A  |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **단계** | WCF(Windows Communication Framework) 서비스는 디버깅 정보를 노출하도록 구성할 수 있습니다. 디버그 정보를 프로덕션 환경에서는 사용하지 않아야 합니다. `<serviceDebug>` 태그는 WCF 서비스에 대해 디버그 정보 기능이 사용되는지 여부를 정의합니다. 특성 includeExceptionDetailInFaults를 true로 설정하면 애플리케이션에서 예외 정보가 클라이언트로 반환됩니다. 공격자는 디버깅 출력에서 얻은 추가 정보를 활용하여 프레임워크, 데이터베이스 또는 애플리케이션이 사용하는 기타 리소스를 대상으로 공격을 수행할 수 있습니다. |

### <a name="example"></a>예
다음 구성 파일에는 `<serviceDebug>` 태그가 포함됩니다. 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
서비스에서 디버깅 정보를 사용하지 않도록 설정합니다. 이 작업은 애플리케이션 구성 파일에서 `<serviceDebug>` 태그를 제거하여 수행할 수 있습니다. 

## <a id="servicemetadata"></a>WCF - 구성 파일에 serviceMetadata 노드를 포함하지 않음

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | WCF | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | 일반, .NET Framework 3 |
| **참조**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify, 영국](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **단계** | 서비스에 대한 정보를 공개적으로 노출하면 공격자는 서비스를 악용하는 방법에 대한 중요한 아이디어를 얻을 수 있습니다. `<serviceMetadata>` 태그는 메타데이터 게시 기능을 사용하도록 설정합니다. 서비스 메타데이터에는 공개적으로 액세스할 수 없게 해야 하는 중요한 정보가 포함될 수 있습니다. 적어도, 신뢰할 수 있는 사용자만 메타데이터에 액세스하도록 허용하고 불필요한 정보가 노출되지 않도록 해야 합니다. 더 나아가 메타데이터를 게시하는 기능은 완전히 사용하지 않도록 설정해야 합니다. 안전한 WCF 구성에는 `<serviceMetadata>` 태그가 포함되지 않습니다. |

## <a id="exception"></a>ASP.NET Web API에서 적절한 예외 처리가 수행되었는지 확인

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | Web API | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | MVC 5, MVC 6 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET Web API에서 예외 처리](https://www.asp.net/web-api/overview/error-handling/exception-handling), [ASP.NET Web API의 모델 유효성 검사](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **단계** | 기본적으로 ASP.NET Web API에서 확인할 수 없는 대부분의 예외는 `500, Internal Server Error` 상태 코드의 HTTP 응답으로 변환됩니다.|

### <a name="example"></a>예
API에서 반환되는 상태 코드를 제어하려면 아래와 같이 `HttpResponseException`을 사용할 수 있습니다. 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>예
예외 응답을 추가적으로 제어하려면 아래와 같이 `HttpResponseMessage` 클래스를 사용할 수 있습니다. 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
`HttpResponseException` 형식이 아닌 처리되지 않은 예외를 catch하려면 예외 필터를 사용할 수 있습니다. 예외 필터는 `System.Web.Http.Filters.IExceptionFilter` 인터페이스를 구현합니다. 예외 필터를 작성하는 가장 간단한 방법은 `System.Web.Http.Filters.ExceptionFilterAttribute` 클래스에서 파생하고 OnException 메서드를 재정의하는 것입니다. 

### <a name="example"></a>예
다음은 `NotImplementedException`을 HTTP 상태 코드 `501, Not Implemented`로 변환하는 필터입니다. 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

다음과 같은 여러 가지 방법으로 Web API 예외 필터를 등록할 수 있습니다.
- 작업 기준
- 컨트롤러 기준
- 전역적으로

### <a name="example"></a>예
특정 작업에 필터를 적용하려면 필터를 작업에 특성으로 추가합니다. 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>예
`controller`의 모든 작업에 필터를 적용하려면 필터를 `controller` 클래스에 특성으로 추가합니다. 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>예
모든 Web API 컨트롤러에는 전역적으로 필터를 적용하려면 필터의 인스턴스를 `GlobalConfiguration.Configuration.Filters` 컬렉션에 추가합니다. 이 컬렉션의 예외 필터는 모든 Web API 컨트롤러 작업에 적용됩니다. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>예
모델 유효성 검사를 위해 모델 상태를 아래와 같이 CreateErrorResponse 메서드에 전달할 수 있습니다. 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

ASP.NET Web API의 모델 유효성 검사 및 예외 처리에 대 한 자세한 내용은 참조 섹션에는 링크를 확인 합니다. 

## <a id="messages"></a>오류 메시지에 보안 정보를 노출하지 않음

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | N/A  |
| **단계** | <p>일반 오류 메시지는 중요한 애플리케이션 데이터를 포함하지 않고 사용자에게 직접 제공됩니다. 중요한 데이터의 예는 다음과 같습니다.</p><ul><li>서버 이름</li><li>연결 문자열</li><li>사용자 이름</li><li>암호</li><li>SQL 프로시저</li><li>동적 SQL 오류의 세부 정보</li><li>스택 추적 및 코드 줄</li><li>메모리에 저장된 변수</li><li>드라이브 및 폴더 위치</li><li>애플리케이션 설치 지점</li><li>호스트 구성 설정</li><li>기타 내부 애플리케이션 정보</li></ul><p>애플리케이션 내의 모든 오류를 트래핑하고 일반 오류 메시지를 제공할 뿐 아니라 IIS 내에서 사용자 지정 오류를 사용하도록 설정하면 정보가 공개되지 않도록 하는 데 도움이 됩니다. 다른 오류 처리 아키텍처 중에서 SQL Server Database 및 .NET 예외 처리는 사용자 애플리케이션을 프로파일링하는 악의적인 사용자에게 특히 유용할 수 있습니다. .NET 예외 클래스에서 파생된 클래스의 내용을 직접 표시하지 않도록 하고, 예기치 않은 예외가 사용자에게 실수로 직접 표시되지 않도록 적절한 예외 처리를 진행합니다.</p><ul><li>예외/오류 메시지에서 직접 확인되는 특정 세부 정보를 추상화하는 일반적인 오류 메시지를 사용자에게 제공합니다.</li><li>.NET 예외 클래스의 내용을 사용자에게 직접 표시하지 않습니다.</li><li>모든 오류 메시지를 트래핑하고, 해당하는 경우 애플리케이션 클라이언트에 전송된 일반 오류 메시지를 통해 사용자에게 알립니다.</li><li>예외 클래스의 내용, 특히 `.ToString()`의 반환 값 또는 Message 또는 StackTrace 속성 값을 사용자에게 직접 공개하지 않습니다. 이 정보를 안전하게 기록하고 사용자에게는 좀 더 무해한 메시지를 표시합니다.</li></ul>|

## <a id="default"></a>기본 오류 처리 페이지 구현

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [ASP.NET 오류 페이지 설정 대화 상자 편집](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **단계** | <p>ASP.NET 애플리케이션이 실패하고 HTTP/1.x 500 내부 서버 오류가 발생하거나 기능 구성(예: 요청 필터링)으로 인해 페이지가 표시되지 않을 경우 오류 메시지가 생성됩니다. 관리자는 애플리케이션이 클라이언트에 자세한 오류 메시지를 표시할지 또는 localhost에만 자세한 오류 메시지를 표시할지를 선택할 수 있습니다. web.config의 `<customErrors>` 태그는 다음 세 가지 모드로 제공됩니다.</p><ul><li>**On:** 사용자 지정 오류를 사용할 수 있는지를 지정 합니다. defaultRedirect 특성을 지정하지 않으면 사용자에게 일반 오류가 표시됩니다. 원격 클라이언트 및 로컬 호스트에 사용자 지정 오류가 표시됩니다.</li><li>**Off:** 사용자 지정 오류가 비활성화 되는지 지정 합니다. 원격 클라이언트 및 로컬 호스트에 자세한 ASP.NET 오류가 표시됩니다.</li><li>**RemoteOnly:** 사용자 지정 오류가 원격 클라이언트에만 표시 되는 로컬 호스트에 ASP.NET 오류가 표시 되는 지정 합니다. 이것이 기본값입니다.</li></ul><p>애플리케이션/사이트에 대한 `web.config` 파일을 열고 태그에 `<customErrors mode="RemoteOnly" />` 또는 `<customErrors mode="On" />`이 정의되어 있는지 확인합니다.</p>|

## <a id="deployment"></a>IIS에서 배포 방법을 일반 정품으로 설정

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 배포 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [배포 요소(ASP.NET 설정 스키마)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **단계** | <p>`<deployment retail>` 스위치는 프로덕션 IIS 서버에서 사용되도록 고안되었습니다. 이 스위치는 애플리케이션이 페이지에서 추적 출력을 생성하는 기능을 사용하지 않도록 설정하고, 최종 사용자에게 자세한 오류 메시지를 표시하는 기능을 사용하지 않도록 설정하고, 디버그 스위치를 사용하지 않도록 설정하여 성능을 최대화하고 보안 정보 노출을 최소화하도록 도와줍니다.</p><p>종종 활성 개발 중에 실패한 요청 추적 및 디버그와 같은 개발자 중심의 스위치 및 옵션이 사용됩니다. 프로덕션 서버의 배포 방법을 일반 정품으로 설정하는 것이 좋습니다. machine.config 파일을 열고 `<deployment retail="true" />`을 true 상태로 유지합니다.</p>|

## <a id="fail"></a>안전한 예외 실패

| 제목                   | 세부 정보      |
| ----------------------- | ------------ |
| **구성 요소**               | 웹 애플리케이션 | 
| **SDL 단계**               | 빌드 |  
| **적용 가능한 기술** | 일반 |
| **특성**              | N/A  |
| **참조**              | [안전하게 실패](https://www.owasp.org/index.php/Fail_securely) |
| **단계** | 애플리케이션은 안전하게 실패해야 합니다. 수행되는 특정 결정에 따라 부울 값을 반환하는 모든 메서드는 신중하게 예외 블록을 만들어야 합니다. 부주의하게 예외 블록이 작성되면 보안 문제로 인한 논리적 오류가 많이 발생합니다.|

### <a name="example"></a>예
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
예외가 발생하면 항상 위 메서드는 True를 반환합니다. 최종 사용자가 잘못된 형식의 URL을 제공할 경우 브라우저는 이를 유지하지만 `Uri()` 생성자는 그렇지 않을 경우 예외가 throw되며 사용자는 유효하지만 형식이 잘못된 URL을 사용하게 됩니다. 
