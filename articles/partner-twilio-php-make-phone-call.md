---
title: Twilio에서 전화를 거는 방법(PHP) | Microsoft Docs
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 샘플은 PHP 애플리케이션용입니다.
documentationcenter: php
services: ''
author: georgewallace
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: fb1623c4a409f1c6cba94bad56d773e166d2b182
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637320"
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Azure의 PHP 애플리케이션에서 Twilio를 사용하여 전화를 거는 방법
다음 예제는 Azure에 호스트된 PHP 웹 페이지에서 Twilio를 사용하여 전화를 거는 방법을 보여 줍니다. 결과 응용 프로그램은 다음 스크린샷에 표시 된 것 처럼 사용자에 게 전화 통화 값을 입력 하 라는 메시지를 표시 합니다.

![Twilio 및 PHP를 사용하는 Azure 통화 양식][twilio_php]

이 항목에서 코드를 사용하려면 다음을 수행해야 합니다.

1. [Twilio 콘솔][twilio_console]에서 Twilio 계정 및 인증 토큰을 획득 합니다. Twilio을 시작 하려면 가격 책정 [https://www.twilio.com/pricing][twilio_pricing]을 평가 하세요. 에서 [https://www.twilio.com/try-twilio][try_twilio]평가판 계정을 등록할 수 있습니다.
2. [PHP용 Twilio 라이브러리](https://github.com/twilio/twilio-php) 를 가져오거나 PEAR 패키지로 설치합니다. 자세한 내용은 [추가 정보 파일](https://github.com/twilio/twilio-php/blob/master/README.md)을 참조하세요.
3. PHP용 Azure SDK를 설치합니다. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>전화 걸기 웹 양식 만들기
다음 HTML 코드는 전화를 걸기 위해 사용자 데이터를 검색하는 웹 페이지(**callform.html**)를 빌드하는 방법을 보여 줍니다.

```html
<!DOCTYPE html>
<html>
<head>
  <title>Automated call form</title>
</head>
<body>
  <h1>Automated Call Form</h1>
  <p>Fill in all fields and click <b>Make this call</b>.</p>
  <form action="makecall.php" method="post">
    <table>
      <tr>
        <td>To:</td>
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>코드를 만들어 전화 걸기
다음 코드는 사용자가 **callform.html**에 의해 표시된 양식을 제출할 때 호출되는 **makecall.php**를 빌드하는 방법을 보여 줍니다. 아래 표시된 코드는 통화 메시지를 만들고 통화를 생성합니다. 또한 **$sid** 에 할당 된 자리 표시자 값 대신 [Twilio 콘솔][twilio_console] 에서 Twilio 계정 및 인증 토큰을 사용 하 고 아래 코드에서 **$token** 해야 합니다.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => https://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

전화 걸기와 함께, **makecall.php**는 몇몇 통화 메타데이터를 아래 이미지에 표시된 대로 표시합니다. 호출 메타 데이터에 대한 자세한 내용은 [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties]을 참조 하십시오.

![Twilio 및 PHP를 사용하는 Azure 통화 응답][twilio_php_response]

## <a name="run-the-application"></a>애플리케이션 실행
다음 단계에서는 [Git로 Azure Web Apps에 애플리케이션을 배포합니다](app-service/app-service-web-get-started-php.md)(일부 정보는 관련이 없을 수 있음). 

## <a name="next-steps"></a>다음 단계
이 코드는 Azure의 PHP에서 Twilio를 사용하는 기본 기능을 보여 줍니다. Azure를 프로덕션에 배포하기 전에 더 많은 오류 처리 또는 기타 기능을 추가할 수 있습니다. 예:

* 웹 양식을 사용하는 대신, Azure Storage Blob 또는 SQL Database를 사용하여 전화 번호 및 통화 텍스트를 저장할 수 있습니다. PHP에서 Azure storage blob을 사용 하는 방법에 대한 자세한 내용은 [Php 응용 프로그램에서 Azure Storage 사용][howto_blob_storage_php]을 참조 하세요. PHP에서 SQL Database를 사용 하는 방법에 대한 자세한 내용은 [Php 응용 프로그램에서 SQL Database 사용][howto_sql_azure_php]을 참조 하세요.
* **Makecall.php** 코드는 Twilio 제공 URL ([https://twimlets.com/message][twimlet_message_url])을 사용 하 여 TwiML (Twilio Markup Language) 응답을 제공 합니다 .이 응답은 Twilio를 사용 하 여 처리 하는 방법을 알려줍니다. 예를 들어 반환되는 TwiML에는 통화 수신자에게 말하는 텍스트에 나타나는 `<Say>` 동사가 포함될 수 있습니다. Twilio 제공 URL을 사용 하는 대신 사용자 고유의 서비스를 빌드하여 Twilio의 요청에 응답할 수 있습니다. 자세한 내용은 [PHP에서 음성 및 SMS 기능을 위해 Twilio를 사용 하는 방법][howto_twilio_voice_sms_php]을 참조 하세요. TwiML에 대한 자세한 내용은에서 확인할 [https://www.twilio.com/docs/api/twiml][twiml]수 있습니다 [https://www.twilio.com/docs/api/twiml/say][twilio_say]. 및 기타 `<Say>` Twilio 동사에 대한 자세한 내용은을 참조 하십시오.
* 에서 [https://www.twilio.com/docs/security][twilio_docs_security]Twilio 보안 지침을 읽습니다.

Twilio에 대한 자세한 내용은을 [https://www.twilio.com/docs][twilio_docs]참조 하십시오.

## <a name="see-also"></a>참고 항목
* [PHP에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: https://www.twilio.com/pricing
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_api]: https://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: https://twimlets.com/message
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api_service]: https://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: https://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: https://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: https://www.twilio.com/docs/security
[twilio_docs]: https://www.twilio.com/docs
[twilio_say]: https://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
