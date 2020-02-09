---
title: Azure에서 음성, VoIP 및 SMS 메시징을 위해 Twilio 사용
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 Node.js로 작성되었습니다.
services: ''
documentationcenter: nodejs
author: georgewallace
ms.assetid: f558cbbd-13d2-416f-b9b1-33a99c426af9
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/25/2014
ms.author: gwallace
ms.openlocfilehash: 164bedffcf9a1aca9f1fa46dea254fb928abcf04
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637266"
---
# <a name="using-twilio-for-voice-voip-and-sms-messaging-in-azure"></a>Azure에서 음성, VoIP 및 SMS 메시징을 위해 Twilio 사용
이 가이드에서는 Azure에서 Twilio 및 node.js와 통신하는 앱을 빌드하는 방법을 보여 줍니다.

<a id="whatis"/>

## <a name="what-is-twilio"></a>Twilio 정의
Twilio는 개발자가 전화 통화를 걸고 받고, 문자 메시지를 보내고 받고, VoIP 호출을 브라우저 기반 및 네이티브 모바일 애플리케이션에 포함하는 작업을 쉽게 수행할 수 있게 해주는 API 플랫폼입니다. 깊이 있게 설명하기 전에 이러한 내용을 간략하게 살펴보겠습니다.

### <a name="receiving-calls-and-text-messages"></a>전화 및 문자 메시지 받기
Twilio를 통해 개발자는 호출 및 문자 메시지를 보내고 받는 데 사용할 수 있는 [프로그래밍 가능한 전화 번호를 구매할][purchase_phone] 수 있습니다. Twilio 번호가 인바운드 전화 또는 문자를 받으면 Twilio는 웹 애플리케이션에 HTTP POST 또는 GET 요청을 보내 전화 또는 문자를 처리하는 방법에 대한 지침을 요청합니다. 서버는 호출 또는 텍스트를 처리 하는 방법에 대한 지침을 포함 하는 간단한 XML 태그 집합인 [TwiML][twiml]를 사용 하 여 TWILIO의 HTTP 요청에 응답 합니다. 잠시 후에 TwiML 예제를 살펴보겠습니다.

### <a name="making-calls-and-sending-text-messages"></a>전화 걸기 및 문자 메시지 보내기
개발자는 Twilio 웹 서비스 API에 대한 HTTP 요청을 만들어 문자 메시지를 보내거나 아웃바운드 전화 통화를 시작할 수 있습니다. 아웃바운드 전화의 경우 개발자는 아웃바운드 전화가 연결된 후 해당 전화를 처리하는 방법에 대한 TwiML 지침을 반환하는 URL도 지정해야 합니다.

### <a name="embedding-voip-capabilities-in-ui-code-javascript-ios-or-android"></a>UI 코드(JavaScript, iOS 또는 Android)에 VoIP 기능 포함
Twilio는 모든 데스크톱 웹 브라우저, iOS 앱 또는 Android 앱을 VoIP 전화로 변환할 수 있는 클라이언트 쪽 SDK를 제공합니다. 이 문서에서는 브라우저에서 VoIP 호출을 사용하는 방법을 집중적으로 설명합니다. 브라우저에서 실행되는 *Twilio JavaScript SDK* 외에 서버 쪽 애플리케이션(node.js 애플리케이션)을 사용하여 "기능 토큰"을 JavaScript 클라이언트에 발급해야 합니다. [Twilio dev 블로그에서][voipnode]node.js를 사용 하 여 VoIP를 사용 하는 방법에 대해 자세히 알아볼 수 있습니다.

<a id="signup"/>

## <a name="sign-up-for-twilio-microsoft-discount"></a>Twilio 등록(Microsoft 할인)
Twilio services를 사용 하기 전에 먼저 [계정에 등록][signup]해야 합니다. Microsoft Azure 고객은 특별 할인을 받습니다. [여기에서 등록][signup]하세요.

<a id="azuresite"/>

## <a name="create-and-deploy-a-nodejs-azure-website"></a>node.js Azure 웹 사이트 만들기 및 배포
이제 Azure에서 실행되는 node.js 웹 사이트를 만들어야 합니다. [이 작업을 수행 하는 공식 설명서는 여기에][azure_new_site]있습니다. 높은 수준에서 봤을 때 다음과 같은 작업을 수행합니다.

* Azure 계정 등록(아직 없는 경우)
* Azure 관리 콘솔을 사용하여 새 웹 사이트 만들기
* 소스 제어 지원 추가(git를 사용했다고 가정함)
* 간단한 node.js 웹 애플리케이션을 사용하여 파일 `server.js` 만들기
* 이 간단한 애플리케이션을 Azure에 배포

<a id="twiliomodule"/>

## <a name="configure-the-twilio-module"></a>Twilio 모듈 구성
이제 Twilio API를 사용하는 간단한 node.js 애플리케이션 작성을 시작합니다. 시작하기 전에 Twilio 계정 자격 증명을 구성해야 합니다.

### <a name="configuring-twilio-credentials-in-system-environment-variables"></a>시스템 환경 변수에서 Twilio 자격 증명 구성
Twilio 백 엔드에 대해 인증된 요청을 만들려면 Twilio 계정에 대해 사용자 이름 및 암호 집합 역할을 하는 계정 SID 및 인증 토큰이 필요합니다. Azure에서 노드 모듈에 사용하기 위해 이를 구성하는 가장 안전한 방법은 시스템 환경 변수를 사용하는 것입니다. 시스템 환경 변수는 Azure 관리 콘솔에서 직접 설정할 수 있습니다.

node.js 웹 사이트를 선택하고 "구성" 링크를 클릭합니다.  아래로 조금 스크롤하면 애플리케이션의 구성 속성을 설정할 수 있는 영역이 표시됩니다.  다음과 같이 Twilio 계정 자격증명을 입력합니다([Twilio콘솔에있음][twilio_console]). - `TWILIO_ACCOUNT_SID` 및 `TWILIO_AUTH_TOKEN`의 이름을 각각 지정합니다.

![Azure 관리 콘솔][azure-admin-console]

이러한 변수를 구성하고 나서 Azure 콘솔에서 애플리케이션을 다시 시작합니다.

### <a name="declaring-the-twilio-module-in-packagejson"></a>package.json에서 Twilio 모듈 선언
이제 [npm]을 통해 노드 모듈 종속성을 관리하기 위한 package.json을 만들어야 합니다. *Azure/node.js* 자습서에서 만든 `server.js` 파일과 같은 수준에서 `package.json`이라는 파일을 만듭니다.  이 파일 내에 다음을 배치합니다.

```json
{
  "name": "application-name",
  "version": "0.0.1",
  "private": true,
  "scripts": {
    "start": "node server"
  },
  "dependencies": {
    "body-parser": "^1.16.1",
    "ejs": "^2.5.5",
    "errorhandler": "^1.5.0",
    "express": "^4.14.1",
    "morgan": "^1.8.1",
    "twilio": "^2.11.1"
  }
}
```

이는 twilio 모듈을 종속성으로 선언 하 고 널리 사용 되는 [Express 웹 프레임 워크][express] 및 EJS 템플릿 엔진을 선언 합니다.  이제 모두 준비되었으니 코드를 작성하겠습니다.

<a id="makecall"/>

## <a name="make-an-outbound-call"></a>아웃바운드 전화 걸기
선택한 번호로 전화하는 간단한 양식을 만들어보겠습니다. `server.js`를 열고 다음 코드를 입력합니다. "CHANGE_ME"라고 표시된 곳에 Azure 웹 사이트의 이름을 배치합니다.

```javascript
// Module dependencies
const express = require('express');
const path = require('path');
const http = require('http');
const twilio = require('twilio');
const logger = require('morgan');
const bodyParser = require('body-parser');
const errorHandler = require('errorhandler');
const accountSid = process.env.TWILIO_ACCOUNT_SID;
const authToken = process.env.TWILIO_AUTH_TOKEN;
// Create Express web application
const app = express();

// Express configuration
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'ejs');
app.use(logger('tiny'));
app.use(bodyParser.urlencoded({ extended: false }))
app.use(bodyParser.json())
app.use(express.static(path.join(__dirname, 'public')));

if (app.get('env') !== 'production') {
  app.use(errorHandler());
}

// Render an HTML user interface for the application's home page
app.get('/', (request, response) => response.render('index'));

// Handle the form POST to place a call
app.post('/call', (request, response) => {
  var client = twilio(accountSid, authToken);

  client.makeCall({
    // make a call to this number
    to:request.body.number,

    // Change to a Twilio number you bought - see:
    // https://www.twilio.com/console/phone-numbers/incoming
    from:'+15558675309',

    // A URL in our app which generates TwiML
    // Change "CHANGE_ME" to your app's name
    url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});

// Generate TwiML to handle an outbound call
app.post('/outbound_call', (request, response) => {
  var twiml = new twilio.TwimlResponse();

  // Say a message to the call's receiver
  twiml.say('hello - thanks for checking out Twilio and Azure', {
      voice:'woman'
  });

  response.set('Content-Type', 'text/xml');
  response.send(twiml.toString());
});

// Start server
app.listen(app.get('port'), function(){
  console.log(`Express server listening on port ${app.get('port')}`);
});
```

이제 `views`라는 디렉터리를 만들고 이 디렉터리 내에 다음 내용이 포함된 `index.ejs`라는 파일을 만듭니다.

```html
<!DOCTYPE html>
<html>
<head>
  <title>Twilio Test</title>
  <style>
    input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
  </style>
</head>
<body>
  <h1>Twilio Test</h1>
  <form action="/call" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input type="submit" value="Call the number above"/>
  </form>
</body>
</html>
```

이제 웹 사이트를 Azure에 배포하고 홈을 엽니다. 텍스트 필드에 전화 번호를 입력하고 Twilio 번호로부터 전화를 받을 수 있어야 합니다.

<a id="sendmessage"/>

## <a name="send-an-sms-message"></a>SMS 메시지 보내기
이제 사용자 인터페이스 및 양식 처리 논리를 설정하여 문자 메시지를 보내겠습니다. `server.js`를 열고 `app.post`에 대한 마지막 호출 뒤에 다음 코드를 추가합니다.

```javascript
app.post('/sms', (request, response) => {
  const client = twilio(accountSid, authToken);

  client.sendSms({
      // send a text to this number
      to:request.body.number,

      // A Twilio number you bought - see:
      // https://www.twilio.com/console/phone-numbers/incoming
      from:'+15558675309',

      // The body of the text message
      body: request.body.message

  }, () => {
      // Go back to the home page
      response.redirect('/');
  });
});
```

`views/index.ejs`에서 첫 번째 양식 아래에 다른 양식을 추가하여 번호 및 문자 메시지를 제출합니다.

```html
<form action="/sms" method="POST">
  <input placeholder="Enter a phone number" name="number"/>
  <br/>
  <input placeholder="Enter a message to send" name="message"/>
  <br/>
  <input type="submit" value="Send text to the number above"/>
</form>
```

애플리케이션을 Azure에 다시 배포하고 나면 이제 해당 양식을 제출하고 자신(또는 가까운 친구)에게 문자 메시지를 보낼 수 있어야 합니다.

<a id="nextsteps"/>

## <a name="next-steps"></a>다음 단계
이제 node.js 및 Twilio를 사용하여 통신하는 앱을 빌드하는 방법에 대한 기본 사항을 배웠습니다. 그러나 이러한 예제는 Twilio 및 node.js를 사용하여 수행할 수 있는 작업의 극히 일부에 불과합니다. Twilio와 node.js 사용에 대한 자세한 내용은 다음 리소스를 참조하십시오.

* [공식 모듈 문서][docs]
* [Node.js 응용 프로그램의 VoIP 자습서][voipnode]
* [Votr-node.js 및 CouchDB를 사용 하는 실시간 SMS 투표 응용 프로그램 (세 부분)][votr]
* [Node.js를 사용 하 여 브라우저에서의 쌍 프로그래밍][pair]

Azure에서 node.js와 Twilio 해킹을 즐기시기를 바랍니다.

[purchase_phone]: https://www.twilio.com/console/phone-numbers/search
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: https://ahoy.twilio.com/azure
[azure_new_site]: app-service/app-service-web-get-started-nodejs.md
[twilio_console]: https://www.twilio.com/console
[npm]: https://npmjs.org
[express]: https://expressjs.com
[voipnode]: https://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: https://www.twilio.com/docs/libraries/reference/twilio-node/
[votr]: https://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: https://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png
