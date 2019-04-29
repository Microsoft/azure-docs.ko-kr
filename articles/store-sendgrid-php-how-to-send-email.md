---
title: SendGrid 메일 서비스를 사용하는 방법(PHP) | Microsoft Docs
description: Azure에서 SendGrid 메일 서비스를 사용하여 메일을 보내는 방법을 알아봅니다. 코드 샘플은 PHP로 작성되었습니다.
documentationcenter: php
services: ''
manager: sendgrid
editor: mollybos
author: thinkingserious
ms.assetid: 51a9928a-4c9e-4b0a-aca8-9a408aeb6f47
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 10/30/2014
ms.author: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com
ms.openlocfilehash: db3333aa52782ceb949ef3f46a903b618f6e3f2f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60931231"
---
# <a name="how-to-use-the-sendgrid-email-service-from-php"></a>PHP에서 SendGrid 메일 서비스를 사용하는 방법

이 가이드에서는 Azure에서 SendGrid 전자 메일 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 샘플은 PHP로 작성되었습니다.
**전자 메일 작성**, **전자 메일 보내기**, **첨부 파일 추가** 등의 시나리오를 다룹니다. SendGrid 및 전자 메일 보내기에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid 전자 메일 서비스 정의
SendGrid는 사용자 지정 통합을 쉽게 만드는 유연한 API와 함께 신뢰할 만한 [트랜잭션 전자 메일 발송], 확장성 및 실시간 분석을 제공하는 [클라우드 기반 전자 메일 서비스]입니다. 일반적인 SendGrid 사용 시나리오는 다음과 같습니다.

* 고객에게 확인 메일 자동으로 보내기
* 월간 전자 전단 및 판촉 행사를 고객에게 보내기 위한 분산 목록 관리
* 차단된 전자 메일, 고객 응답 같은 항목의 실시간 메트릭 수집
* 경향을 식별하는 데 도움이 되도록 보고서 생성
* 고객 문의 전달
* 애플리케이션의 전자 메일 알림

자세한 내용은 [https://sendgrid.com][https://sendgrid.com]을 참조하세요.

## <a name="create-a-sendgrid-account"></a>SendGrid 계정 만들기

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="using-sendgrid-from-your-php-application"></a>PHP 애플리케이션에서 SendGrid 사용

Azure PHP 애플리케이션에서 SendGrid를 사용하기 위해 특별한 구성이 필요하지는 않습니다. SendGrid는 서비스이므로, 온-프레미스 애플리케이션에서 액세스하는 것과 동일한 방법으로 클라우드 애플리케이션에서 액세스할 수 있습니다.

## <a name="how-to-send-an-email"></a>방법: 이메일 보내기

SendGrid에서 제공하는 SMTP 또는 웹 API를 사용하여 전자 메일을 보낼 수 있습니다.

### <a name="smtp-api"></a>SMTP API

SendGrid SMTP API를 사용하여 메일을 보내려면 PHP 애플리케이션에서 메일을 보내기 위한 구성 요소 기반 라이브러리인 *Swift Mailer*를 사용합니다. [Swift Mailer 라이브러리](https://swiftmailer.symfony.com/) v5.3.0을 다운로드할 수 있습니다([Composer]를 사용하여 Swift Mailer 설치). 라이브러리를 사용하여 이메일을 보내려면 `Swift\_SmtpTransport`, `Swift\_Mailer` 및 `Swift\_Message` 클래스 인스턴스를 만들고, 적절한 속성을 설정하고, `Swift\_Mailer::send` 메서드를 호출해야 합니다.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
 $html = "<html>
       <head></head>
       <body>
           <p>Hi!<br>
               How are you?<br>
           </p>
       </body>
       </html>";
 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');
 // Email recipients
 $to = array(
       'john@contoso.com'=>'Destination 1 Name',
       'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
     // This will let us know how many users received this message
     echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
     echo "Something went wrong - ";
     print_r($failures);
 }
```

### <a name="web-api"></a>Web API
PHP의 [curl 함수][curl function] 를 사용하여 SendGrid 웹 API를 사용하여 전자 메일을 보냅니다.

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $params = array(
      'api_user' => $user,
      'api_key' => $pass,
      'to' => 'john@contoso.com',
      'subject' => 'testing from curl',
      'html' => 'testing body',
      'text' => 'testing body',
      'from' => 'anna@contoso.com',
   );

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

SendGrid의 웹 API는 REST API와 매우 유사하지만 대부분의 호출에서 GET 및 POST 동사를 상호 교환 가능한 방식으로 사용할 수 있으므로 진정한 의미에서 RESTful API는 아닙니다.

## <a name="how-to-add-an-attachment"></a>방법: 첨부 파일 추가

### <a name="smtp-api"></a>SMTP API

SMTP API를 사용하여 첨부 파일을 보내는 프로세스에는 Swift Mailer를 사용하여 메일을 보내기 위한 예제 스크립트에 대한 추가 코드 줄이 포함됩니다.

```php
<?php
 include_once "vendor/autoload.php";
 /*
  * Create the body of the message (a plain-text and an HTML version).
  * $text is your plain-text email
  * $html is your html version of the email
  * If the receiver is able to view html emails then only the html
  * email will be displayed
  */
 $text = "Hi!\nHow are you?\n";
  $html = "<html>
      <head></head>
      <body>
         <p>Hi!<br>
            How are you?<br>
         </p>
      </body>
      </html>";

 // This is your From email address
 $from = array('someone@example.com' => 'Name To Appear');

 // Email recipients
 $to = array(
      'john@contoso.com'=>'Destination 1 Name',
      'anna@contoso.com'=>'Destination 2 Name'
 );
 // Email subject
 $subject = 'Example PHP Email';

 // Login credentials
 $username = 'yoursendgridusername';
 $password = 'yourpassword';

 // Setup Swift mailer parameters
 $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
 $transport->setUsername($username);
 $transport->setPassword($password);
 $swift = Swift_Mailer::newInstance($transport);

 // Create a message (subject)
 $message = new Swift_Message($subject);

 // attach the body of the email
 $message->setFrom($from);
 $message->setBody($html, 'text/html');
 $message->setTo($to);
 $message->addPart($text, 'text/plain');
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));

 // send message
 if ($recipients = $swift->send($message, $failures))
 {
      // This will let us know how many users received this message
      echo 'Message sent out to '.$recipients.' users';
 }
 // something went wrong =(
 else
 {
      echo "Something went wrong - ";
      print_r($failures);
 }
```

추가 코드 줄은 다음과 같습니다.

```php
 $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));
```

이 코드 줄은 `Swift\_Message` 개체에서 첨부 메서드를 호출하고 `Swift\_Attachment` 클래스의 정적 메서드 `fromPath`를 사용하여 파일을 가져와서 메시지에 첨부합니다.

### <a name="web-api"></a>Web API

웹 API를 사용한 첨부 파일 보내기는 웹 API를 사용하여 메일 보내기와 매우 유사합니다. 그러나 다음 예제에서 매개 변수 배열은 이 요소를 포함해야 합니다.

```php
    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
```

#### <a name="example"></a>예

```php
<?php

 $url = 'https://api.sendgrid.com/';
 $user = 'USERNAME';
 $pass = 'PASSWORD';

 $fileName = 'myfile';
 $filePath = dirname(__FILE__);

 $params = array(
     'api_user' => $user,
     'api_key' => $pass,
     'to' =>'john@contoso.com',
     'subject' => 'test of file sends',
     'html' => '<p> the HTML </p>',
     'text' => 'the plain text',
     'from' => 'anna@contoso.com',
     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
 );

 print_r($params);

 $request = $url.'api/mail.send.json';

 // Generate curl request
 $session = curl_init($request);

 // Tell curl to use HTTP POST
 curl_setopt ($session, CURLOPT_POST, true);

 // Tell curl that this is the body of the POST
 curl_setopt ($session, CURLOPT_POSTFIELDS, $params);

 // Tell curl not to return headers, but do return the response
 curl_setopt($session, CURLOPT_HEADER, false);
 curl_setopt($session, CURLOPT_RETURNTRANSFER, true);

 // obtain response
 $response = curl_exec($session);
 curl_close($session);

 // print everything out
 print_r($response);
```

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>방법: 필터를 사용하여 바닥글, 추적 및 분석을 사용하도록 설정

SendGrid는 *필터*사용을 통해 추가 전자 메일 기능을 제공합니다. 클릭 추적, Google 분석, 구독 추적 등을 사용하도록 설정하는 것과 같이 특정 기능을 사용하도록 설정하기 위해 전자 메일 메시지에 추가할 수 있는 설정입니다.

필터는 filters 속성을 사용하여 메시지에 적용할 수 있습니다. 각 필터는 필터별 설정을 포함하는 해시에 의해 지정됩니다. 다음 예제에서는 바닥글 필터를 사용하도록 설정하고 메일 메시지의 맨 아래에 추가할 텍스트 메시지를 지정합니다. 이 예제의 경우 [sendgrid-php 라이브러리]를 사용합니다.

라이브러리를 설치하려면 [Composer]를 사용합니다.

```bash
php composer.phar require sendgrid/sendgrid 2.1.1
```

### <a name="example"></a>예  

```php
<?php
 /*
  * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
  */
 include "vendor/autoload.php";

 $email = new SendGrid\Email();
 // The list of addresses this message will be sent to
 // [This list is used for sending multiple emails using just ONE request to SendGrid]
 $toList = array('john@contoso.com', 'anna@contoso.com');

 // Specify the names of the recipients
 $nameList = array('Name 1', 'Name 2');

 // Used as an example of variable substitution
 $timeList = array('4 PM', '5 PM');

 // Set all of the above variables
 $email->setTos($toList);
 $email->addSubstitution('-name-', $nameList);
 $email->addSubstitution('-time-', $timeList);

 // Specify that this is an initial contact message
 $email->addCategory("initial");

 // You can optionally setup individual filters here, in this example, we have
 // enabled the footer filter
 $email->addFilter('footer', 'enable', 1);
 $email->addFilter('footer', "text/plain", "Thank you for your business");
 $email->addFilter('footer', "text/html", "Thank you for your business");

 // The subject of your email
 $subject = 'Example SendGrid Email';

 // Where is this message coming from. For example, this message can be from
 // support@yourcompany.com, info@yourcompany.com
 $from = 'someone@example.com';

 // If you do not specify a sender list above, you can specify the user here. If
 // a sender list IS specified above, this email address becomes irrelevant.
 $to = 'john@contoso.com';

 # Create the body of the message (a plain-text and an HTML version).
 # text is your plain-text email
 # html is your html version of the email
 # if the receiver is able to view html emails then only the html
 # email will be displayed

 /*
  * Note the variable substitution here =)
  */
 $text = "
 Hello -name-,
 Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
 Regards,
 Fred";

 $html = "
 <html>
 <head></head>
 <body>
 <p>Hello -name-,<br>
 Thank you for your interest in our products. We have set up an appointment
 to call you at -time- EST to discuss your needs in more detail.

 Regards,

 Fred<br>
 </p>
 </body>
 </html>";

 // set subject
 $email->setSubject($subject);

 // attach the body of the email
 $email->setFrom($from);
 $email->setHtml($html);
 $email->addTo($to);
 $email->setText($text);

 // Your SendGrid account credentials
 $username = 'sendgridusername@yourdomain.com';
 $password = 'example';

 // Create SendGrid object
 $sendgrid = new SendGrid($username, $password);

 // send message
 $response = $sendgrid->send($email);

 print_r($response);
 ```

## <a name="next-steps"></a>다음 단계

SendGrid 전자 메일 서비스에 관한 기본적인 사항들을 익혔으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* SendGrid 설명서: <https://sendgrid.com/docs>
* SendGrid PHP 라이브러리: <https://github.com/sendgrid/sendgrid-php>
* Azure 고객을 위한 SendGrid 특별 제공: <https://sendgrid.com/windowsazure.html>

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조하세요.

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
[special offer]: https://www.sendgrid.com/windowsazure.html
[Packaging and Deploying PHP Applications for Azure]: https://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
[http://swiftmailer.org/download]: http://swiftmailer.org/download
[curl function]: https://php.net/curl
[클라우드 기반 전자 메일 서비스]: https://sendgrid.com/email-solutions
[트랜잭션 전자 메일 발송]: https://sendgrid.com/transactional-email
[sendgrid-php 라이브러리]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
[Composer]: https://getcomposer.org/download/
