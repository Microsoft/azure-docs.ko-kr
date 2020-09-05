---
title: SendGrid 메일 서비스를 사용하는 방법(.NET) | Microsoft Docs
description: Azure에서 SendGrid 메일 서비스를 사용 하 여 전자 메일을 보내는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 .NET API를 사용합니다.
services: ''
documentationcenter: .net
author: georgewallace
ms.author: gwallace
editor: ''
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.reviewer: dx@sendgrid.com
ms.custom: devx-track-dotnet
ms.openlocfilehash: c8ac20378cbae9334cedb59878311f2541b40bd3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020595"
---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Azure에서 SendGrid를 사용하여 전자 메일을 보내는 방법
## <a name="overview"></a>개요
이 가이드에서는 Azure에서 SendGrid 전자 메일 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 샘플은 C\#으로 작성되었으며 .NET 표준 1.3을 지원합니다. 전자 메일 작성, 전자 메일 발송, 첨부 파일 추가 및 다양한 메일 및 추적 설정 사용 등의 시나리오를 다룹니다. SendGrid 및 전자 메일 보내기에 대한 자세한 내용은 [다음 단계][Next steps] 섹션을 참조하세요.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 전자 메일 서비스 정의
SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한 [트랜잭션 전자 메일 배달], 확장성 및 실시간 분석을 제공하는 [클라우드 기반 전자 메일 서비스]입니다. 일반적인 SendGrid 사용 사례는 다음과 같습니다.

* 고객에게 영수증 또는 구매 확인을 자동으로 전송
* 고객에게 매달 전단지 및 홍보 메일을 보내는 메일 그룹 관리
* 차단된 메일과 같은 작업에 대한 실시간 메트릭 및 고객 참여 수집
* 고객 질문 전달
* 수신 메일 처리

자세한 내용은 [https://sendgrid.com](https://sendgrid.com) 또는 SendGrid의 [c # 라이브러리][sendgrid-csharp] GitHub 리포지토리를 참조 하세요.

## <a name="create-a-sendgrid-account"></a>SendGrid 계정 만들기
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>SendGrid.NET 클래스 라이브러리 참조
[SendGrid NuGet 패키지](https://www.nuget.org/packages/Sendgrid) 는 SendGrid API를 가져오고 애플리케이션에서 종속성을 모두 갖도록 구성하는 가장 쉬운 방법입니다. NuGet은 Microsoft Visual Studio 2015 이상에 포함된 Visual Studio 확장 프로그램으로서 라이브러리 및 도구를 쉽게 설치하고 업데이트할 수 있습니다.

> [!NOTE]
> Visual Studio 2015 이전 버전의 Visual Studio를 실행 하는 경우 NuGet을 설치 하려면를 방문 하 [https://www.nuget.org](https://www.nuget.org) 여 **nuget 설치** 단추를 클릭 합니다.
>
>

애플리케이션에서 SendGrid NuGet 패키지를 설치하려면 다음을 수행합니다.

1. **새 프로젝트**를 클릭하고 **템플릿**을 선택합니다.

   ![새 프로젝트 만들기][create-new-project]
2. **솔루션 탐색기**에서 **참조**를 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.

   ![SendGrid NuGet 패키지][SendGrid-NuGet-package]
3. **SendGrid**를 검색하고 결과 목록에서 **SendGrid** 항목을 선택합니다.
4. 이 문서에서 설명하는 개체 모델 및 API를 사용하려면 버전 드롭다운에서 안정적인 최신 버전의 Nuget 패키지를 선택합니다.

   ![SendGrid 패키지][sendgrid-package]
5. **설치** 를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.

SendGrid의 .NET 클래스 라이브러리는 **SendGrid**라고 합니다. 다음 네임스페이스가 포함되어 있습니다.

* SendGrid의 API와 통신 하기 위한 **SendGrid** 입니다.
* **SendGrid.Helpers.Mail**은 도우미 메서드가 전자 메일을 보내는 방법을 지정하는 SendGridMessage 개체를 쉽게 만들 수 있습니다.

프로그래밍 방식으로 SendGrid 전자 메일 서비스에 액세스하려는 C# 파일의 맨 위에 다음과 같은 코드 네임스페이스 선언을 추가합니다.

```csharp
using SendGrid;
using SendGrid.Helpers.Mail;
```

## <a name="how-to-create-an-email"></a>방법: 전자 메일 만들기
**SendGridMessage** 개체를 사용하여 메일 메시지를 만듭니다. 일단 메시지 개체가 만들어지면 메일 보낸 사람, 메일 받는 사람, 메일 제목 및 본문을 포함하여 속성과 메서드를 설정할 수 있습니다.

다음 예에서는 완전히 채워진 전자 메일 개체를 만드는 방법을 보여 줍니다.

```csharp
var msg = new SendGridMessage();

msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

var recipients = new List<EmailAddress>
{
    new EmailAddress("jeff@example.com", "Jeff Smith"),
    new EmailAddress("anna@example.com", "Anna Lidman"),
    new EmailAddress("peter@example.com", "Peter Saddow")
};
msg.AddTos(recipients);

msg.SetSubject("Testing the SendGrid C# Library");

msg.AddContent(MimeType.Text, "Hello World plain text!");
msg.AddContent(MimeType.Html, "<p>Hello World!</p>");
```

**SendGrid** 형식에서 지원하는 모든 속성 및 메서드에 대한 자세한 내용은 GitHub에서 [sendgrid-csharp][sendgrid-csharp]을 참조하세요.

## <a name="how-to-send-an-email"></a>방법: 전자 메일 보내기
전자 메일 메시지를 만든 후에 SendGrid의 API를 사용하여 해당 메시지를 보낼 수 있습니다. 또는 [.NET의 기본 제공 라이브러리][NET-library]를 사용할 수도 있습니다.

메일을 보내려면 SendGrid API 키를 제공해야 합니다. API 키를 구성하는 방법에 대한 세부 정보가 필요한 경우 SendGrid의 API 키 [설명서][documentation]를 방문하세요.

응용 프로그램 설정을 클릭 하 고 앱 설정 아래에 키/값 쌍을 추가 하 여 Azure Portal 통해 이러한 자격 증명을 저장할 수 있습니다.

 ![Azure 앱 설정][azure_app_settings]

그러면 다음과 같이 자격 증명에 액세스할 수 있습니다.

```csharp
var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
var client = new SendGridClient(apiKey);
```

다음 예제에서는 콘솔 애플리케이션에서 SendGrid Web API를 사용하여 이메일 메시지를 보내는 방법을 보여 줍니다.

```csharp
using System;
using System.Threading.Tasks;
using SendGrid;
using SendGrid.Helpers.Mail;

namespace Example
{
    internal class Example
    {
        private static void Main()
        {
            Execute().Wait();
        }

        static async Task Execute()
        {
            var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
            var client = new SendGridClient(apiKey);
            var msg = new SendGridMessage()
            {
                From = new EmailAddress("test@example.com", "DX Team"),
                Subject = "Hello World from the SendGrid CSharp SDK!",
                PlainTextContent = "Hello, Email!",
                HtmlContent = "<strong>Hello, Email!</strong>"
            };
            msg.AddTo(new EmailAddress("test@example.com", "Test User"));
            var response = await client.SendEmailAsync(msg);
        }
    }
}
```

## <a name="how-to-send-email-from-asp-net-core-api-using-mailhelper-class"></a>방법: MailHelper 클래스를 사용하여 ASP.NET Core API에서 이메일 보내기

아래 예제를 사용하면 `SendGrid.Helpers.Mail` 네임스페이스의 `MailHelper` 클래스를 사용하여 ASP.NET Core API에서 여러 사용자에게 단일 이메일을 보낼 수 있습니다. 이 예에서는 ASP.NET Core 1.0을 사용합니다. 

이 예에서는 API 키가 `appsettings.json` 파일에 저장됩니다. 위의 예에 나와 있는 것처럼 Azure Portal에서 이 파일을 재정의할 수 있습니다.

`appsettings.json` 파일의 내용은 다음과 같아야 합니다.

```csharp
{
   "Logging": {
   "IncludeScopes": false,
   "LogLevel": {
   "Default": "Debug",
   "System": "Information",
   "Microsoft": "Information"
     }
   },
 "SENDGRID_API_KEY": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
}
```

먼저 .NET Core API 프로젝트의 `Startup.cs` 파일에 아래 코드를 추가해야 합니다. 그래야 API 컨트롤러에서 종속성 주입을 사용하여 `appsettings.json` 파일에서 `SENDGRID_API_KEY`에 액세스할 수 있습니다. `IConfiguration` 인터페이스는 아래의 `ConfigureServices` 메서드에 추가한 후 컨트롤러의 생성자에서 주입할 수 있습니다. 필요한 코드를 추가한 후의 `Startup.cs` 파일 내용은 다음과 같습니다.

```csharp
    public IConfigurationRoot Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        // Add mvc here
        services.AddMvc();
        services.AddSingleton<IConfiguration>(Configuration);
    }
```

컨트롤러에서 `IConfiguration` 인터페이스를 주입한 후에는 `MailHelper` 클래스의 `CreateSingleEmailToMultipleRecipients` 메서드를 사용하여 여러 명의 받는 사람에게 단일 이메일을 보낼 수 있습니다. 이 메서드는 `showAllRecipients`라는 추가 부울 매개 변수 하나를 허용합니다. 이 매개 변수를 사용하면 이메일 받는 사람이 이메일 머리글의 받는 사람 섹션에서 다른 받는 사람의 이메일 주소를 볼 수 있는지를 제어할 수 있습니다. 컨트롤러의 샘플 코드는 다음과 같습니다. 

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using SendGrid;
using SendGrid.Helpers.Mail;
using Microsoft.Extensions.Configuration;

namespace SendgridMailApp.Controllers
{
    [Route("api/[controller]")]
    public class NotificationController : Controller
    {
       private readonly IConfiguration _configuration;

       public NotificationController(IConfiguration configuration)
       {
         _configuration = configuration;
       }      
    
       [Route("SendNotification")]
       public async Task PostMessage()
       {
          var apiKey = _configuration.GetSection("SENDGRID_API_KEY").Value;
          var client = new SendGridClient(apiKey);
          var from = new EmailAddress("test1@example.com", "Example User 1");
          List<EmailAddress> tos = new List<EmailAddress>
          {
              new EmailAddress("test2@example.com", "Example User 2"),
              new EmailAddress("test3@example.com", "Example User 3"),
              new EmailAddress("test4@example.com","Example User 4")
          };
        
          var subject = "Hello world email from Sendgrid ";
          var htmlContent = "<strong>Hello world with HTML content</strong>";
          var displayRecipients = false; // set this to true if you want recipients to see each others mail id 
          var msg = MailHelper.CreateSingleEmailToMultipleRecipients(from, tos, subject, "", htmlContent, false);
          var response = await client.SendEmailAsync(msg);
      }
   }
}
```

## <a name="how-to-add-an-attachment"></a>방법: 첨부 파일 추가
**AddAttachment** 메서드를 호출하고 최소한 첨부할 파일 이름과 Base64 인코딩 콘텐츠를 지정하여 첨부 파일을 메시지에 추가할 수 있습니다. 첨부할 파일마다 이 메서드를 한 번씩 호출하고 **AddAttachments** 메서드를 사용하여 여러 개의 첨부 파일을 포함할 수 있습니다. 다음 예에서는 메시지에 첨부 파일을 추가하는 방법을 보여 줍니다.

```csharp
var banner2 = new Attachment()
{
    Content = Convert.ToBase64String(raw_content),
    Type = "image/png",
    Filename = "banner2.png",
    Disposition = "inline",
    ContentId = "Banner 2"
};
msg.AddAttachment(banner2);
```

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>방법: 메일 설정을 사용하여 바닥글, 추적 및 분석을 사용하도록 설정
SendGrid는 메일 설정 및 추적 설정을 사용하여 추가 메일 기능을 제공합니다. 클릭 추적, Google 분석, 구독 추적 등과 같은 특정 기능을 사용할 수 있도록 전자 메일 메시지에 이러한 설정을 추가할 수 있습니다. 앱의 전체 목록은 [설정 설명서][settings-documentation]를 참조하세요.

**SendGridMessage** 클래스의 일부로 구현되는 메서드를 사용하여 앱을 **SendGrid** 메일 메시지에 적용할 수 있습니다. 다음 예에서는 바닥글 및 클릭 추적 필터를 보여 줍니다.

다음 예에서는 바닥글 및 클릭 추적 필터를 보여 줍니다.

### <a name="footer-settings"></a>바닥글 설정

```csharp
msg.SetFooterSetting(
                     true,
                     "Some Footer HTML",
                     "<strong>Some Footer Text</strong>");
```

### <a name="click-tracking"></a>클릭 추적

```csharp
msg.SetClickTracking(true);
```

## <a name="how-to-use-additional-sendgrid-services"></a>방법: 추가 SendGrid 서비스 사용
SendGrid는 Azure 애플리케이션 내에서 추가 기능을 활용하는 데 사용할 수 있는 여러 API 및 웹후크를 제공합니다. 자세한 내용은 [SendGrid API 참조][SendGrid API documentation]를 참조하세요.

## <a name="next-steps"></a>다음 단계
SendGrid 전자 메일 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* SendGrid C\# 라이브러리 리포지토리: [sendgrid-csharp][sendgrid-csharp]
* SendGrid API 설명서: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[클라우드 기반 전자 메일 서비스]: https://sendgrid.com/solutions
[트랜잭션 전자 메일 발송]: https://sendgrid.com/use-cases/transactional-email

