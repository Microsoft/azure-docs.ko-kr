---
title: 음성 및 SMS에 Twilio를 사용하는 방법(Java) | Microsoft Docs
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 Java로 작성되었습니다.
services: ''
documentationcenter: java
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: f3508965-5527-4255-9d51-5d5f926f4d43
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 386b4b8440c74f6599e7147996b5843ea0f67e68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623955"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-java"></a>Java에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법
이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화 통화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 애플리케이션에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

## <a id="WhatIs"></a>Twilio 정의
Twilio는 기존 웹 언어와 기술을 사용하여 음성 및 SMS 애플리케이션을 빌드할 수 있게 해주는 전화 통신 웹 서비스 API입니다. Twilio는 타사 서비스입니다(Azure 기능 또는 Microsoft 제품 아님).

**Twilio 음성**을 통해 애플리케이션에서 전화를 걸고 받을 수 있습니다. **Twilio SMS**를 사용하면 애플리케이션에서 SMS 메시지를 작성하고 받을 수 있습니다. **Twilio 클라이언트**를 사용하면 애플리케이션에서 모바일 연결을 비롯한 기존 인터넷 연결을 통해 음성 통신을 사용할 수 있습니다.

## <a id="Pricing"></a>Twilio 가격 책정 및 특별 제공
Twilio 가격 책정 정보는 [Twilio 가격 책정][twilio_pricing]에서 확인할 수 있습니다. Azure 고객은 [특별 제공][special_offer](문자 1000통 또는 인바운드 통화 1000분의 무료 크레딧)을 받습니다. 이 제공에 등록하거나 추가 정보를 얻으려면 [https://ahoy.twilio.com/azure][special_offer]를 방문하세요.

## <a id="Concepts"></a>개념
Twilio API는 애플리케이션에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][twilio_libraries](영문)를 참조하십시오.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <a id="Verbs"></a>Twilio 동사
API는 Twilio 동사를 활용합니다. 예를 들어 **&lt;Say&gt;** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다.

* **&lt;전화&gt;**: 발신자를 다른 전화에 연결합니다.
* **&lt;수집&gt;**: 전화 키패드에 입력된 숫자를 수집합니다.
* **&lt;전화 끊기&gt;**: 통화를 끝냅니다.
* **&lt;재생&gt;**: 오디오 파일을 재생합니다.
* **&lt;큐&gt;**: 호출자 큐에 추가합니다.
* **&lt;일시 중시&gt;**: 지정된 시간(초) 동안 무음으로 대기합니다.
* **&lt;녹음&gt;**: 발신자의 음성을 녹음하고 녹음이 포함된 파일의 URL을 반환합니다.
* **&lt;리디렉션&gt;**: 통화 또는 SMS에 대한 제어를 다른 URL의 TwiML로 전송합니다.
* **&lt;거부&gt;**: 요금을 청구하지 않고 Twilio 번호로 걸려 오는 전화를 거부합니다.
* **&lt;통화&gt;**: 통화 시 텍스트를 음성으로 변환합니다.
* **&lt;Sms&gt;**: SMS 메시지를 보냅니다.

### <a id="TwiML"></a>TwiML
TwiML은 Twilio에 통화 또는 SMS 처리 방법을 알려 주는 Twilio 동사를 사용하는 XML 기반 명령 집합입니다.

다음 예제 TwiML은 **Hello World!** 텍스트를 음성으로 변환합니다.

```xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World!</Say>
    </Response>
```

애플리케이션에서 Twilio API를 호출할 때 API 매개 변수 중 하나는 TwiML 응답을 반환하는 URL입니다. 개발을 위해서 Twilio 제공 URL을 사용하여 애플리케이션에 사용되는 TwiML 응답을 제공할 수 있습니다. 또한 TwiML 응답을 생성하는 고유한 URL을 호스트할 수도 있고, **TwiMLResponse** 개체를 사용할 수도 있습니다.

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][twiml](영문)을 참조하십시오. Twilio API에 대한 자세한 내용은 [Twilio API][twilio_api](영문)를 참조하십시오.

## <a id="CreateAccount"></a>Twilio 계정 만들기
Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][try_twilio](영문)에서 등록하십시오. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 ID 및 인증 토큰을 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하십시오. 계정 ID 및 인증 토큰은 [Twilio 콘솔][twilio_console]의 **ACCOUNT SID** 및 **AUTH TOKEN** 필드에서 각각 확인할 수 있습니다.

## <a id="create_app"></a>Java 애플리케이션 만들기
1. Twilio JAR을 다운로드하여 Java 빌드 경로 및 WAR 배포 어셈블리에 추가합니다. [https://github.com/twilio/twilio-java][twilio_java]에서 GitHub 원본을 다운로드한 후 고유한 JAR을 만들거나 종속성 포함 여부에 관계없이 미리 빌드된 JAR을 다운로드할 수 있습니다.
2. JDK의 **cacerts** keystore에 MD5 지문이 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4인 Equifax Secure Certificate Authority 인증서가 포함되어 있는지 확인합니다(일련 번호는 35:DE:F4:CF이고 SHA1 지문은 D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A임). 이 인증서는 Twilio API를 사용할 때 호출되는 [https://api.twilio.com][twilio_api_service] 서비스에 대한 CA(인증 기관) 인증서입니다. JDK의 **cacerts** keystore에 올바른 CA 인증서가 포함되어 있는지 확인하는 방법에 대한 자세한 내용은 [Java CA 인증서 저장소에 인증서 추가][add_ca_cert]를 참조하세요.

Java용 Twilio 클라이언트 라이브러리 사용에 대한 자세한 지침은 [Azure의 Java 애플리케이션에서 Twilio를 사용하여 전화를 거는 방법][howto_phonecall_java]을 참조하세요.

## <a id="configure_app"></a>Twilio 라이브러리를 사용하도록 애플리케이션 구성
코드 내에서 애플리케이션에 사용할 Twilio 패키지 또는 클래스의 원본 파일 맨 위에 **import** 문을 추가할 수 있습니다.

Java 원본 파일의 경우

```java
    import com.twilio.*;
    import com.twilio.rest.api.*;
    import com.twilio.type.*;
    import com.twilio.twiml.*;
```

JSP(Java Server Page) 원본 파일의 경우

```java
    import="com.twilio.*"
    import="com.twilio.rest.api.*"
    import="com.twilio.type.*"
    import="com.twilio.twiml.*"
 ```
 
사용할 Twilio 패키지 또는 클래스에 따라 **import** 문이 달라질 수 있습니다.

## <a id="howto_make_call"></a>방법: 발신 전화 걸기
다음은 **Call** 클래스를 사용하여 발신 전화를 거는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **from** 및 **to** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **from** 번호를 확인하세요.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    URI uri = new URI("https://twimlets.com/message" +
            "?Message%5B0%5D=Hello%20World%21");

    // Declare To and From numbers
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

**Call.creator** 메서드에 전달된 매개 변수에 대한 자세한 내용은 [https://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]를 참조하세요.

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 고유한 사이트를 대신 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [Azure의 Java 애플리케이션에서 TwiML 응답을 제공하는 방법](#howto_provide_twiml_responses)을 참조하십시오.

## <a id="howto_send_sms"></a>방법: SMS 메시지 보내기
다음은 **Message** 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 체험 계정이 SMS 메시지를 보낼 **from** 번호 **4155992671**은 Twilio에서 자동으로 입력됩니다. 코드를 실행하기 전에 Twilio 계정에 대한 **to** 번호를 확인해야 합니다.

```java
    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account_SID";
    String authToken = "your_twilio_authentication_token";

    // Initialize the Twilio client.
    Twilio.init(accountSID, authToken);

    // Declare To and From numbers and the Body of the SMS message
    PhoneNumber to = new PhoneNumber("+14159352345"); // Replace with a valid phone number for your account.
    PhoneNumber from = new PhoneNumber("+14158141829"); // Replace with a valid phone number for your account.
    String body = "Where's Wallace?";

    // Create a Message creator passing From, To and Body values
    // then send the SMS message by calling the create() method
    Message sms = Message.creator(to, from, body).create();
```

**Message.creator** 메서드에 전달된 매개 변수에 대한 자세한 내용은 [https://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]를 참조하세요.

## <a id="howto_provide_twiml_responses"></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공
애플리케이션에서 Twilio API 호출을 시작하면(예: **CallCreator.create** 메서드를 통해) Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 위의 예제에서는 Twilio 제공 URL [https://twimlets.com/message][twimlet_message_url]을 사용합니다. TwiML은 웹 서비스에서 사용되도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어 [https://twimlets.com/message][twimlet_message_url]를 클릭하여 빈 **&lt;Response&gt;** 요소를 확인합니다. 다른 예로 [https://twimlets.com/message?Message%5B0%5D=Hello%20World%21][twimlet_message_url_hello_world]를 클릭하여 **&lt;Say&gt;** 요소를 포함하는 **&lt;Response&gt;** 요소를 확인합니다.)

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 URL 사이트를 만들 수 있습니다. HTTP 응답을 반환하는 모든 언어로 사이트를 만들 수 있습니다. 이 항목에서는 JSP 페이지에서 URL을 호스팅한다고 가정합니다.

다음 JSP 페이지에서는 호출 시 **Hello World!** 라고 말하는 TwiML 응답이 생성됩니다.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello World!</Say>
    </Response>
```

다음 JSP 페이지에서는 일부 텍스트를 말하고 여러 일시 중지를 포함하며 Twilio API 버전 및 Azure 역할 이름에 대한 정보를 말하는 TwiML 응답이 생성됩니다.

```xml
    <%@ page contentType="text/xml" %>
    <Response>
        <Say>Hello from Azure!</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>
```

**ApiVersion** 매개 변수는 Twilio 음성 요청(SMS 요청 아님)에 사용할 수 있습니다. Twilio 음성 및 SMS 요청에 대한 사용 가능한 요청 매개 변수를 확인하려면 각각 <https://www.twilio.com/docs/api/twiml/twilio_request> 및 <https://www.twilio.com/docs/api/twiml/sms/twilio_request>를 참조하세요. **RoleName** 환경 변수는 Azure 배포의 일부로 사용할 수 있습니다. **System.getenv**에서 선택될 수 있도록 사용자 지정 환경 변수를 추가하려면 [기타 역할 구성 설정][misc_role_config_settings]의 환경 변수 섹션을 참조하세요.

JSP 페이지가 TwiML 응답을 제공하도록 설정된 경우 **Call.creator** 메서드에 전달되는 URL로 JSP 페이지의 URL을 사용합니다. 예를 들어 MyTwiML이라는 웹 애플리케이션이 Azure 호스티드 서비스에 배포되어 있고 JSP 페이지의 이름이 mytwiml.jsp인 경우 다음과 같이 URL을 **Call.creator**에 전달할 수 있습니다.

```java
    // Declare To and From numbers and the URL of your JSP page
    PhoneNumber to = new PhoneNumber("NNNNNNNNNN");
    PhoneNumber from = new PhoneNumber("NNNNNNNNNN");
    URI uri = new URI("http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    // Create a Call creator passing From, To and URL values
    // then make the call by executing the create() method
    Call.creator(to, from, uri).create();
```

TwiML로 응답하는 또 다른 옵션은 **com.twilio.twiml** 패키지에 포함된 **VoiceResponse** 클래스를 사용하는 것입니다.

Azure에서 Java와 함께 Twilio를 사용하는 방법에 대한 자세한 내용은 [Azure의 Java 웹 애플리케이션에서 Twilio를 사용하여 전화를 거는 방법][howto_phonecall_java]을 참조하세요.

## <a id="AdditionalServices"></a>방법: 추가 Twilio 서비스 사용
여기에서 보여 준 예뿐만 아니라 Twilio는 Azure 애플리케이션에서 Twilio 기능을 활용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [Twilio API 설명서][twilio_api_documentation]를 참조하세요.

## <a id="NextSteps"></a>다음 단계
Twilio 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [Twilio 보안 지침][twilio_security_guidelines]
* [Twilio 사용 방법 및 예제 코드][twilio_howtos]
* [Twilio 빠른 시작 자습서][twilio_quickstarts]
* [Twilio on GitHub][twilio_on_github]
* [Twilio 지원 문의][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World%21
[twilio_rest_making_calls]: https://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: https://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/docs
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
