---
title: Twilio에서 전화를 거는 방법(.NET) | Microsoft Docs
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 .NET으로 작성되었습니다.
services: ''
documentationcenter: .net
author: mimckitt
editor: ''
ms.assetid: 789185ad-69dc-4e9e-a936-42e0a25315c8
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/04/2016
ms.author: mimckitt
ms.custom: devx-track-dotnet
ms.openlocfilehash: 90e7bafab3fb9658cd9e11d0417f84269adcfa13
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022346"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-web-role-on-azure"></a>Azure의 웹 역할에서 Twilio를 사용하여 전화를 거는 방법
이 가이드에서는 Azure에 호스트된 웹 페이지에서 Twilio를 사용하여 전화를 거는 방법을 설명합니다. 결과적으로 애플리케이션은 다음 스크린샷에 표시된 대로 지정된 번호와 메시지를 사용하여 호출하라는 메시지를 사용자에게 표시합니다.

![Twilio 및 ASP.NET을 사용하는 Azure 통화 양식][twilio_dotnet_basic_form]

## <a name="prerequisites"></a><a name="twilio-prereqs"></a>필수 조건
이 항목에서 코드를 사용하려면 다음을 수행해야 합니다.

1. [Twilio 콘솔][twilio_console]에서 Twilio 계정 및 인증 토큰을 가져옵니다. Twilio을 시작 하려면에서 등록 [https://www.twilio.com/try-twilio][try_twilio] 합니다. 가격 책정은에서 평가할 수 있습니다 [https://www.twilio.com/pricing][twilio_pricing] . Twilio에서 제공 하는 API에 대 한 자세한 내용은을 참조 하십시오 [https://www.twilio.com/voice/api][twilio_api] .
2. *Twilio .NET 라이브러리*를 웹 역할에 추가합니다. 이 항목의 뒷부분에 나오는 **웹 역할 프로젝트에 Twilio 라이브러리를 추가 하려면**을 참조 하세요.

[Azure에서 기본적인 웹 역할][azure_webroles_get_started] 만들기에 익숙해져야 합니다.

## <a name="how-to-create-a-web-form-for-making-a-call"></a><a name="howtocreateform"></a>방법: 전화 걸기 웹 양식 만들기
<a id="use_nuget"></a>웹 역할 프로젝트에 Twilio 라이브러리 추가하려면

1. Visual Studio에서 솔루션을 엽니다.
2. **참조**를 마우스 오른쪽 단추로 클릭합니다.
3. **NuGet 패키지 관리**를 클릭 합니다.
4. **온라인**을 클릭합니다.
5. 온라인 검색 상자에 *twilio*를 입력합니다.
6. Twilio 패키지에서 **설치** 를 클릭합니다.

다음 코드는 전화를 걸기 위해 웹 양식을 만들고 사용자 데이터를 검색하는 방법을 보여 줍니다. 이 예제에서는 **TwilioCloud** 라는 ASP.NET 웹 역할을 만듭니다.

```aspx
<%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
    AutoEventWireup="true" CodeBehind="Default.aspx.cs"
    Inherits="WebRole1._Default" %>

<asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
</asp:Content>
<asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
    <div>
        <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
        </asp:BulletedList>
    </div>
    <div>
        <p>Fill in all fields and click <b>Make this call</b>.</p>
        <div>
            To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
            Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
            <asp:Button ID="callpage" runat="server" Text="Make this call"
                onclick="callpage_Click" />
        </div>
    </div>
</asp:Content>
```

## <a name="how-to-create-the-code-to-make-the-call"></a><a id="howtocreatecode"></a>방법: 코드를 만들어 전화 걸기
사용자가 양식을 다 작성하면 호출되는 다음 코드는 통화 메시지를 만들고 통화를 생성합니다. 이 예제에서는 코드가 양식 단추의 onclick 이벤트 처리기에서 실행됩니다. (아래 코드에서 `accountSID` 및 `authToken`에 할당된 자리 표시자 값 대신 Twilio 계정 및 인증 토큰을 사용하십시오.)

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Web.UI;
using System.Web.UI.WebControls;
using Twilio;
using Twilio.Http;
using Twilio.Types;
using Twilio.Rest.Api.V2010;

namespace WebRole1
{
    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {

        }

        protected void callpage_Click(object sender, EventArgs e)
        {
            // Call processing happens here.

            // Use your account SID and authentication token instead of
            // the placeholders shown here.
            var accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
            var authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

            // Instantiate an instance of the Twilio client.
            TwilioClient.Init(accountSID, authToken);

            // Retrieve the account, used later to retrieve the
            var account = AccountResource.Fetch(accountSID);

            this.varDisplay.Items.Clear();

            if (this.toNumber.Text == "" || this.message.Text == "")
            {
                this.varDisplay.Items.Add(
                        "You must enter a phone number and a message.");
            }
            else
            {
                // Retrieve the values entered by the user.
                var to = PhoneNumber(this.toNumber.Text);
                var from = new PhoneNumber("+14155992671");
                var myMessage = this.message.Text;

                // Create a URL using the Twilio message and the user-entered
                // text. You must replace spaces in the user's text with '%20'
                // to make the text suitable for a URL.
                var url = $"https://twimlets.com/message?Message%5B0%5D={myMessage.Replace(" ", "%20")}";
                var twimlUri = new Uri(url);

                // Display the endpoint, API version, and the URL for the message.
                this.varDisplay.Items.Add($"Using Twilio endpoint {
                }");
                this.varDisplay.Items.Add($"Twilioclient API Version is {apiVersion}");
                this.varDisplay.Items.Add($"The URL is {url}");

                // Place the call.
                var call = CallResource.create(to, from, url: twimlUri);
                this.varDisplay.Items.Add("Call status: " + call.Status);
            }
        }
    }
}
```

전화가 걸리고 Twilio 엔드포인트, API 버전 및 통화 상태가 표시됩니다. 다음 스크린샷은 샘플 실행의 결과를 보여 줍니다.

![Twilio 및 ASP.NET을 사용하는 Azure 통화 응답][twilio_dotnet_basic_form_output]

TwiML에 대 한 자세한 내용은에서 찾을 수 있습니다 [https://www.twilio.com/docs/api/twiml][twiml] . 및 기타 Twilio 동사에 대 한 자세한 내용은 &lt; &gt; 에서 찾을 수 있습니다 [https://www.twilio.com/docs/api/twiml/say][twilio_say] .

## <a name="next-steps"></a><a id="nextsteps"></a>다음 단계
이 코드는 Azure에서 ASP.NET 웹 역할의 Twilio를 사용하는 기본 기능을 보여 줍니다. Azure를 프로덕션에 배포하기 전에 더 많은 오류 처리 또는 기타 기능을 추가할 수 있습니다. 예를 들어:

* 웹 양식을 사용하는 대신, Azure Blob 스토리지 또는 Azure SQL Database 인스턴스를 사용하여 전화 번호 및 통화 텍스트를 저장할 수 있습니다. Azure에서 Blob 사용에 대한 자세한 내용은 [.NET에서 Azure Blob Storage 서비스를 사용하는 방법][howto_blob_storage_dotnet]을 참조하세요. SQL Database 사용에 대한 자세한 내용은 [.NET 애플리케이션에서 Azure SQL Database를 사용하는 방법][howto_sql_azure_dotnet]을 참조하세요.
* 양식에서 값을 하드 코딩하는 대신, `RoleEnvironment.getConfigurationSettings`를 사용하여 배포 구성 설정에서 Twilio 계정 ID 및 인증 토큰을 검색할 수 있습니다. `RoleEnvironment` 클래스에 대한 자세한 내용은 [Microsoft.WindowsAzure.ServiceRuntime Namespace][azure_runtime_ref_dotnet]를 참조하세요.
* 에서 Twilio 보안 지침을 읽습니다 [https://www.twilio.com/docs/security][twilio_docs_security] .
* Twilio at에 대해 자세히 알아보세요 [https://www.twilio.com/docs][twilio_docs] .

## <a name="see-also"></a><a name="seealso"></a>참고 항목
* [Azure에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: https://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
[azure_webroles_get_started]: https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-get-started
