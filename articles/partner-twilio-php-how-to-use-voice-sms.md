---
title: 음성 및 SMS에 Twilio를 사용하는 방법(PHP) | Microsoft Docs
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 PHP로 작성되었습니다.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 007f22e3-ac75-4868-8315-da000c2e0dd0
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 34057f1962338927a252011dccc56ed6a77bec47
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69636034"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-php"></a>PHP에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법
이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화 통화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 애플리케이션에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

## <a id="WhatIs"></a>Twilio 정의
Twilio는 개발자가 애플리케이션에 음성, VoIP 및 메시징을 포함할 수 있도록 하면서 비즈니스 통신의 미래를 이끌고 있습니다. 개발자는 클라우드 기반 글로벌 환경에 필요한 모든 인프라를 가상화하고, Twilio 통신 API 플랫폼을 통해 이를 공개합니다. 덕분에 애플리케이션을 간단히 빌드하고 확장할 수 있습니다. 종량제 가격의 유연성과 클라우드 안정성의 이점을 누리십시오.

**Twilio 음성**을 통해 애플리케이션에서 전화를 걸고 받을 수 있습니다. **Twilio SMS**를 사용하면 애플리케이션에서 문자 메시지를 보내고 받을 수 있습니다. **Twilio 클라이언트** 를 통해서는 전화, 태블릿 또는 브라우저에서 VoIP 통화를 하고 WebRTC를 지원할 수 있습니다.

## <a id="Pricing"></a>Twilio 가격 책정 및 특별 제공
Azure 고객은 Twilio 계정을 업그레이드할 때 [특별 제공](https://www.twilio.com/azure)(10달러의 Twilio 크레딧)을 받습니다. 이 Twilio 크레딧은 모든 Twilio 사용량에 적용될 수 있습니다. 10달러의 크레딧은 전화 번호 및 메시지 또는 통화 대상의 위치에 따라 SMS 메시지를 1,000개 보내거나 최대 1000분간 인바운드 음성을 받을 수 있는 금액입니다. [https://ahoy.twilio.com/azure](https://ahoy.twilio.com/azure)에서 이 Twilio 크레딧을 충전하고 시작하세요.

Twilio는 종량제 서비스입니다. 설정 수수료는 없으며 언제든 계정을 종료할 수 있습니다. [Twilio 가격 책정][twilio_pricing](영문)에서 자세한 내용을 볼 수 있습니다.

## <a id="Concepts"></a>개념
Twilio API는 애플리케이션에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 여러 언어로 제공 됩니다. 목록은 [TWILIO API 라이브러리][twilio_libraries]를 참조 하세요.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <a id="Verbs"></a>Twilio 동사
API는 Twilio 동사를 활용합니다. 예를 들어 **&lt;Say&gt;** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다. 기타 동사 및 기능에 대해서는 [Twilio Markup Language 설명서](https://www.twilio.com/docs/api/twiml)(영문)에서 알아보십시오.

* **&lt;전화&gt;** : 발신자를 다른 전화에 연결합니다.
* **&lt;수집&gt;** : 전화 키패드에 입력된 숫자를 수집합니다.
* **&lt;전화 끊기&gt;** : 통화를 끝냅니다.
* **&lt;재생&gt;** : 오디오 파일을 재생합니다.
* **&lt;일시 중시&gt;** : 지정된 시간(초) 동안 무음으로 대기합니다.
* **&lt;녹음&gt;** : 발신자의 음성을 녹음하고 녹음이 포함된 파일의 URL을 반환합니다.
* **&lt;리디렉션&gt;** : 통화 또는 SMS에 대한 제어를 다른 URL의 TwiML로 전송합니다.
* **&lt;거부&gt;** : 요금을 청구하지 않고 Twilio 번호로 걸려 오는 전화를 거부합니다.
* **&lt;통화&gt;** : 통화 시 텍스트를 음성으로 변환합니다.
* **&lt;Sms&gt;** : SMS 메시지를 보냅니다.

### <a id="TwiML"></a>TwiML
TwiML은 Twilio에 통화 또는 SMS 처리 방법을 알려 주는 Twilio 동사를 사용하는 XML 기반 명령 집합입니다.

다음 예제 TwiML은 **Hello World** 텍스트를 음성으로 변환합니다.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

애플리케이션에서 Twilio API를 호출할 때 API 매개 변수 중 하나는 TwiML 응답을 반환하는 URL입니다. 개발을 위해서 Twilio 제공 URL을 사용하여 애플리케이션에 사용되는 TwiML 응답을 제공할 수 있습니다. 또한 TwiML 응답을 생성하는 고유한 URL을 호스트할 수도 있고, **TwiMLResponse** 개체를 사용할 수도 있습니다.

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][twiml]를 참조 하세요. Twilio API에 대한 자세한 내용은 [TWILIO api][twilio_api]를 참조 하세요.

## <a id="CreateAccount"></a>Twilio 계정 만들기
Twilio 계정을 가져올 준비가 되 면 [Twilio 체험][try_twilio]에서 등록 합니다. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 ID 및 인증 토큰을 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하십시오. 계정 ID 및 인증 토큰은 [Twilio 계정 페이지][twilio_account]의 **Account SID** 및 **AUTH token**필드에서 각각 볼 수 있습니다.

## <a id="create_app"></a>HP 애플리케이션 만들기
Twilio 서비스를 사용하고 Azure에서 실행되고 있는 PHP 애플리케이션은 Twilio 서비스를 사용하는 다른 PHP 애플리케이션과 차이가 없습니다. Twilio services는 REST 기반이 고 여러 가지 방법으로 PHP에서 호출 될 수 있지만,이 문서에서는 [GitHub의 php 용 Twilio library][twilio_php]를 사용 하 여 Twilio services를 사용 하는 방법에 중점을 둡니다. PHP 용 Twilio 라이브러리를 사용 하는 방법에 대한 [https://www.twilio.com/docs/libraries/php][twilio_lib_docs]자세한 내용은을 참조 하십시오.

Twilio/PHP 응용 프로그램을 빌드하고 Azure에 배포 하는 방법에 대한 자세한 지침은 [azure의 PHP 응용 프로그램에서 Twilio를 사용 하 여 전화를 거는 방법][howto_phonecall_php]에서 확인할 수 있습니다.

## <a id="configure_app"></a>Twilio 라이브러리를 사용하도록 애플리케이션 구성
다음과 같은 두 가지 방법으로 PHP용 Twilio 라이브러리를 사용하도록 애플리케이션을 구성할 수 있습니다.

1. GitHub ([https://github.com/twilio/twilio-php][twilio_php])에서 PHP 용 Twilio 라이브러리를 다운로드 하 고 응용 프로그램에 **Services** 디렉터리를 추가 합니다.
   
    -또는-
2. PHP용 Twilio 라이브러리를 PEAR 패키지로 설치합니다. 다음 명령을 사용하여 설치할 수 있습니다.
   
        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

PHP용 Twilio 라이브러리를 설치하고 나면 PHP 파일의 맨 위에 **require_once** 문을 추가하여 라이브러리를 참조하도록 할 수 있습니다.

        require_once 'Services/Twilio.php';

자세한 내용은 [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]를 참조하세요.

## <a id="howto_make_call"></a>방법: 발신 전화 걸기
다음은 **Services_Twilio** 클래스를 사용하여 발신 전화를 거는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **From** 및 **To** 전화 번호의 값을 바꾸고, 코드를 실행하기 전에 Twilio 계정의 **From** 번호를 확인하십시오.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the call.
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "https://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 고유한 사이트를 대신 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [고유한 웹 사이트에서 TwiML 응답을 제공하는 방법](#howto_provide_twiml_responses)을 참조하십시오.

* **참고**: SSL 인증서 유효성 검사 오류를 해결 하려면 다음을 참조 하세요.[http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation] 

## <a id="howto_send_sms"></a>방법: SMS 메시지 보내기
다음은 **Services_Twilio** 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 평가판 계정이 SMS 메시지를 보낼 **From** 번호는 자동으로 입력됩니다. 코드를 실행하기 전에 Twilio 계정에 대한 **To** 번호를 확인해야 합니다.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->$client->account->messages->sendMessage($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공
애플리케이션에서 Twilio API 호출을 시작하면 Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 위의 예제에서는 Twilio 제공 URL [https://twimlets.com/message][twimlet_message_url]을 사용 합니다. TwiML은 Twilio에서 사용되도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어, [https://twimlets.com/message][twimlet_message_url] 빈 `<Response>` 요소를 보려면를 클릭 하 고, `<Say>` 요소를 [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] 포함 하는 `<Response>` 요소를 보려면 클릭 합니다.

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 사이트를 만들 수 있습니다. XML 응답을 반환하는 모든 언어로 사이트를 만들 수 있습니다. 이 항목에서는 TwiML을 만들기 위해 PHP를 사용 중이라고 가정합니다.

다음 PHP 페이지에서는 호출 시 **Hello World** 라고 말하는 TwiML 응답이 생성됩니다.

    <?php    
        header("content-type: text/xml");    
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>    
        <Say>Hello world.</Say>
    </Response>

위의 예제와 같이 TwiML 응답은 단지 XML 문서입니다. PHP용 Twilio 라이브러리에는 TwiML을 자동으로 생성하는 클래스가 포함되어 있습니다. 아래의 예제에서는 위에 표시된 것과 동일한 응답을 생성하지만 PHP용 Twilio 라이브러리의 **Services\_Twilio\_Twiml** 클래스를 사용합니다.

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

TwiML에 대한 자세한 내용은 [https://www.twilio.com/docs/api/twiml][twiml_reference]을 참조하세요. 

PHP 페이지가 TwiML 응답을 제공하도록 설정된 경우 `Services_Twilio->account->calls->create` 메서드에 전달되는 URL로 PHP 페이지의 URL을 사용합니다. 예를 들어 **MyTwiML**이라는 웹 애플리케이션이 Azure 호스티드 서비스에 배포되어 있고 PHP 페이지의 이름이 **mytwiml.php**인 경우 다음 예와 같이 URL을 **Services_Twilio-&gt;account-&gt;calls-&gt;create**에 전달할 수 있습니다.

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
              $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

PHP를 사용 하 여 Azure에서 Twilio를 사용 하는 방법에 대한 자세한 내용은 [azure의 Php 응용 프로그램에서 Twilio를 사용 하 여 전화를 거는 방법][howto_phonecall_php]을 참조 하세요.

## <a id="AdditionalServices"></a>방법: 추가 Twilio 서비스 사용
여기에서 보여 준 예뿐만 아니라 Twilio는 Azure 애플리케이션에서 Twilio 기능을 활용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [TWILIO API 설명서][twilio_api_documentation]를 참조 하세요.

## <a id="NextSteps"></a>다음 단계
Twilio 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [Twilio 보안 지침][twilio_security_guidelines]
* [Twilio 방법의 및 예제 코드][twilio_howtos]
* [Twilio 빠른 시작 자습서][twilio_quickstarts] 
* [GitHub의 Twilio][twilio_on_github]
* [Twilio 지원에 문의][twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: https://www.twilio.com/docs/api/errors
[twilio_api_service]: https://api.twilio.com
[howto_phonecall_php]: partner-twilio-php-make-phone-call.md
[twilio_voice_request]: https://www.twilio.com/docs/api/twiml/twilio_request
[twilio_sms_request]: https://www.twilio.com/docs/api/twiml/sms/twilio_request
[misc_role_config_settings]: https://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing
[special_offer]: https://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: https://www.twilio.com/api
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
