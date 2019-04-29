---
title: 음성 및 SMS에 Twilio를 사용하는 방법(Python) | Microsoft 문서
description: Azure에서 Twilio API 서비스를 사용하여 전화를 걸고 SMS 메시지를 보내는 방법에 대해 알아봅니다. 코드 샘플은 Python으로 작성되었습니다.
services: ''
documentationcenter: python
author: devinrader
manager: twilio
editor: ''
ms.assetid: 561bc75b-4ac4-40ba-bcba-48e901f27cc3
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/19/2015
ms.author: MicrosoftHelp@twilio.com
ms.openlocfilehash: e6cfd9e72dc1a38e4ed0c11320336ccc4b44a2c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457671"
---
# <a name="how-to-use-twilio-for-voice-and-sms-capabilities-in-python"></a>Python에서 음성 및 SMS 기능을 위해 Twilio를 사용하는 방법
이 가이드에서는 Azure에서 Twilio API 서비스로 일반 프로그래밍 작업을 수행하는 방법을 보여 줍니다. 이 문서의 시나리오에서는 전화 통화를 걸고 SMS(Short Message Service) 메시지를 보냅니다. 애플리케이션에서 음성 및 SMS 사용 방법과 Twilio에 대한 자세한 내용은 [다음 단계](#NextSteps) 섹션을 참조하십시오.

## <a id="WhatIs"></a>Twilio 정의
Twilio는 개발자가 애플리케이션에 음성, VoIP 및 메시징을 포함할 수 있도록 하면서 비즈니스 통신의 미래를 이끌고 있습니다. 개발자는 클라우드 기반 글로벌 환경에 필요한 모든 인프라를 가상화하고, Twilio 통신 API 플랫폼을 통해 이를 공개합니다. 덕분에 애플리케이션을 간단히 빌드하고 확장할 수 있습니다. 종량제 가격의 유연성과 클라우드 안정성의 이점을 누리십시오.

**Twilio 음성**을 통해 애플리케이션에서 전화를 걸고 받을 수 있습니다.
**Twilio SMS**를 사용하면 애플리케이션에서 문자 메시지를 보내고 받을 수 있습니다.
**Twilio 클라이언트** 를 통해서는 전화, 태블릿 또는 브라우저에서 VoIP 통화를 하고 WebRTC를 지원할 수 있습니다.

## <a id="Pricing"></a>Twilio 가격 책정 및 특별 제공
Azure 고객은 Twilio 계정을 업그레이드할 때 [특별 제공][special_offer] 10달러의 Twilio 크레딧을 받습니다. 이 Twilio 크레딧은 모든 Twilio 사용량에 적용될 수 있습니다. 10달러의 크레딧은 전화 번호 및 메시지 또는 통화 대상의 위치에 따라 SMS 메시지를 1,000개 보내거나 최대 1000분간 인바운드 음성을 받을 수 있는 금액입니다. 이 [Twilio 크레딧][special_offer]을 충전하고 시작하세요.

Twilio는 종량제 서비스입니다. 설정 수수료는 없으며 언제든 계정을 종료할 수 있습니다. [Twilio 가격 책정][twilio_pricing]에서 자세한 내용을 볼 수 있습니다.

## <a id="Concepts"></a>개념
Twilio API는 애플리케이션에 대한 음성 및 SMS 기능을 제공하는 RESTful API입니다. 클라이언트 라이브러리는 다양한 언어로 사용할 수 있습니다. 목록에 대해서는 [Twilio API 라이브러리][twilio_libraries](영문)를 참조하십시오.

Twilio API의 핵심 요소는 Twilio 동사와 TwiML(Twilio Markup Language)입니다.

### <a id="Verbs"></a>Twilio 동사
API는 Twilio 동사를 활용합니다. 예를 들어 **&lt;Say&gt;** 동사는 Twilio에 통화 메시지를 음성으로 전달하도록 지시합니다.

다음은 Twilio 동사의 목록입니다. 기타 동사 및 기능에 대해서는 [Twilio Markup Language 설명서][twiml]에서 알아보세요.

* **&lt;전화&gt;**: 발신자를 다른 전화에 연결합니다.
* **&lt;수집&gt;**: 전화 키패드에 입력된 숫자를 수집합니다.
* **&lt;전화 끊기&gt;**: 통화를 끝냅니다.
* **&lt;일시 중시&gt;**: 지정된 시간(초) 동안 무음으로 대기합니다.
* **&lt;재생&gt;**: 오디오 파일을 재생합니다.
* **&lt;큐&gt;**: 호출자 큐에 추가합니다.
* **&lt;녹음&gt;**: 발신자의 음성을 녹음하고 녹음이 포함된 파일의 URL을 반환합니다.
* **&lt;리디렉션&gt;**: 통화 또는 SMS에 대한 제어를 다른 URL의 TwiML로 전송합니다.
* **&lt;거부&gt;**: 요금을 청구하지 않고 Twilio 번호로 걸려 오는 전화를 거부합니다.
* **&lt;통화&gt;**: 통화 시 텍스트를 음성으로 변환합니다.
* **&lt;Sms&gt;**: SMS 메시지를 보냅니다.

### <a id="TwiML"></a>TwiML
TwiML은 Twilio에 통화 또는 SMS 처리 방법을 알려 주는 Twilio 동사를 사용하는 XML 기반 명령 집합입니다.

다음 예제 TwiML은 **Hello World** 텍스트를 음성으로 변환합니다.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
      <Say>Hello World</Say>
    </Response>

애플리케이션에서 Twilio API를 호출할 때 API 매개 변수 중 하나는 TwiML 응답을 반환하는 URL입니다. 개발을 위해서 Twilio 제공 URL을 사용하여 애플리케이션에 사용되는 TwiML 응답을 제공할 수 있습니다. 또한 TwiML 응답을 생성하는 고유한 URL을 호스트할 수도 있고, `TwiMLResponse` 개체를 사용할 수도 있습니다.

Twilio 동사, 특성 및 TwiML에 대한 자세한 내용은 [TwiML][twiml](영문)을 참조하십시오. Twilio API에 대한 자세한 내용은 [Twilio API][twilio_api](영문)를 참조하십시오.

## <a id="CreateAccount"></a>Twilio 계정 만들기
Twilio 계정을 사용할 준비가 되었다면 [Twilio 체험][try_twilio]에서 등록하세요. 무료 계정으로 시작했다가 나중에 계정을 업그레이드할 수 있습니다.

Twilio 계정을 등록하면 계정 SID 및 인증 토큰을 받게 됩니다. 둘 다 Twilio API 통화를 하는 데 필요합니다. 계정에 대한 무단 액세스를 방지하려면 인증 토큰을 안전하게 유지하십시오. 계정 SID 및 인증 토큰은 [Twilio 콘솔][twilio_console]의 **ACCOUNT SID** 및 **AUTH TOKEN** 필드에서 각각 확인할 수 있습니다.

## <a id="create_app"></a>Python 애플리케이션 만들기
Twilio 서비스를 사용하고 Azure에서 실행되고 있는 Python 애플리케이션은 Twilio 서비스를 사용하는 다른 Python 애플리케이션과 차이가 없습니다. Twilio 서비스가 REST 기반이고 여러 가지 방법으로 Python에서 호출될 수 있기는 하지만, 이 문서에서는 [GitHub의 Python용 Twilio 라이브러리][twilio_python]와 Twilio 서비스를 사용하는 방법을 집중적으로 설명합니다. Python용 Twilio 라이브러리를 사용하는 방법에 대한 자세한 내용은 [https://www.twilio.com/docs/libraries/python][twilio_lib_docs]를 참조하세요.

먼저, 새 Python 웹 애플리케이션의 호스트 역할을 할 [새 Azure Linux VM을 설정][azure_vm_setup]합니다. Virtual Machine이 실행되면 아래 설명된 대로 공개 포트에 애플리케이션을 표시해야 합니다.

### <a name="add-an-incoming-rule"></a>수신 규칙 추가
  1. [네트워크 보안 그룹][azure_nsg] 페이지로 이동합니다.
  2. Virtual Machine과 일치하는 네트워크 보안 그룹을 선택합니다.
  3. **포트 80**에 대한 **발신 규칙**을 추가합니다. 모든 주소에서의 수신을 허용해야 합니다.

### <a name="set-the-dns-name-label"></a>DNS 이름 레이블 설정
  1. [공용 IP 주소][azure_ips] 페이지로 이동합니다.
  2. Virtual Machine과 일치하는 공용 IP를 선택합니다.
  3. **구성** 섹션에서 **DNS 이름 레이블**을 설정합니다. 이 예제의 경우 *your-domain-label*.centralus.cloudapp.azure.com 같이 표시됩니다.

SSH를 통해 Virtual Machine에 연결할 수 있으면 선택한 웹 프레임워크를 설치할 수 있습니다(Python에서 가장 잘 알려진 두 개의 웹 프레임워크는 [Flask](http://flask.pocoo.org/) 및 [Django](https://www.djangoproject.com)임). `pip install` 명령을 실행하여 둘 중 하나를 설치할 수 있습니다.

포트 80에서만 트래픽을 허용하도록 Virtual Machine을 구성했다는 점에 유의하세요. 따라서 이 포트를 사용하도록 애플리케이션을 구성해야 합니다.

## <a id="configure_app"></a>Twilio 라이브러리를 사용하도록 애플리케이션 구성
다음과 같은 두 가지 방법으로 Python용 Twilio 라이브러리를 사용하도록 애플리케이션을 구성할 수 있습니다.

* Python용 Twilio 라이브러리를 Pip 패키지로 설치합니다. 다음 명령을 사용하여 설치할 수 있습니다.
   
        $ pip install twilio

    또는

* GitHub([https://github.com/twilio/twilio-python][twilio_python])에서 Python용 Twilio 라이브러리를 다운로드하고 다음과 같이 설치합니다.

        $ python setup.py install

Python용 Twilio 라이브러리를 설치한 후 Python 파일로 라이브러리를 `import`할 수 있습니다.

        import twilio

자세한 내용은 [twilio_github_readme](https://github.com/twilio/twilio-python/blob/master/README.rst)를 참조하세요.

## <a id="howto_make_call"></a>방법: 발신 전화 걸기
다음은 발신 전화를 거는 방법을 보여 줍니다. 또한 이 코드는 Twilio 제공 사이트를 사용하여 TwiML(Twilio Markup Language) 응답을 반환합니다. **from_number** 및 **to_number** 전화 번호의 값을 바꾸고 코드를 실행하기 전에 Twilio 계정에 대한 **from_number** 전화 번호를 확인해야 합니다.

    from urllib.parse import urlencode

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    # The number of the phone initiating the call.
    # This should either be a Twilio number or a number that you've verified
    from_number = "NNNNNNNNNNN"

    # The number of the phone receiving call.
    to_number = "NNNNNNNNNNN"

    # Use the Twilio-provided site for the TwiML response.
    url = "https://twimlets.com/message?"

    # The phone message text.
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url + urlencode({'Message': message}))
    print(call.sid)

언급한 대로 이 코드는 Twilio 제공 사이트를 사용하여 TwiML 응답을 반환합니다. 고유한 사이트를 대신 사용하여 TwiML 응답을 제공할 수 있습니다. 자세한 내용은 [고유한 웹 사이트에서 TwiML 응답을 제공하는 방법](#howto_provide_twiml_responses)을 참조하십시오.

## <a id="howto_send_sms"></a>방법: SMS 메시지 보내기
다음은 `TwilioRestClient` 클래스를 사용하여 SMS 메시지를 보내는 방법을 보여 줍니다. 평가판 계정이 SMS 메시지를 보낼 **from_number** 번호는 Twilio에서 자동으로 입력됩니다. 코드를 실행하기 전에 Twilio 계정에 대한 **to_number** 번호를 확인해야 합니다.

    # Import the Twilio Python Client.
    from twilio.rest import TwilioRestClient

    # Set your account ID and authentication token.
    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"

    from_number = "NNNNNNNNNNN"  # With trial account, texts can only be sent from your Twilio number.
    to_number = "NNNNNNNNNNN"
    message = "Hello world."

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Send the SMS message.
    message = client.messages.create(to=to_number,
                                     from_=from_number,
                                     body=message)

## <a id="howto_provide_twiml_responses"></a>방법: 고유한 웹 사이트에서 TwiML 응답 제공
애플리케이션에서 Twilio API 호출을 시작하면 Twilio에서 TwiML 응답을 반환해야 하는 URL로 요청을 보냅니다. 위의 예제에서는 Twilio 제공 URL [https://twimlets.com/message][twimlet_message_url]을 사용합니다. TwiML은 Twilio에서 사용되도록 설계되었지만 브라우저에서도 TwiML을 볼 수 있습니다. 예를 들어, [https://twimlets.com/message][twimlet_message_url]을 클릭하여 빈 `<Response>` 요소를 확인합니다. 또 다른 예로 [https://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world]를 클릭하여 `<Say>` 요소를 포함하는 `<Response>` 요소를 확인합니다.

Twilio 제공 URL을 사용하지 않고 HTTP 응답을 반환하는 고유한 사이트를 만들 수 있습니다. XML 응답을 반환하는 모든 언어로 사이트를 만들 수 있습니다. 이 항목에서는 TwiML을 만들기 위해 Python를 사용 중이라고 가정합니다.

다음 예제에서는 호출 시 **Hello World**라고 말하는 TwiML 응답이 출력됩니다.

Flask 사용:

    from flask import Response
    @app.route("/")
    def hello():
        xml = '<Response><Say>Hello world.</Say></Response>'
        return Response(xml, mimetype='text/xml')

Django 사용:

    from django.http import HttpResponse
    def hello(request):
        xml = '<Response><Say>Hello world.</Say></Response>'
        return HttpResponse(xml, content_type='text/xml')

위의 예제와 같이 TwiML 응답은 단지 XML 문서입니다. Python용 Twilio 라이브러리에는 TwiML을 자동으로 생성하는 클래스가 포함되어 있습니다. 아래 예제에서는 위에 표시된 것과 동일한 응답을 생성하지만 Python용 Twilio 라이브러리의 `twiml` 모듈을 사용합니다.

    from twilio import twiml

    response = twiml.Response()
    response.say("Hello world.")
    print(str(response))

TwiML에 대한 자세한 내용은 [https://www.twilio.com/docs/api/twiml][twiml_reference]를 참조하세요.

Python 애플리케이션이 TwiML 응답을 제공하도록 설정된 경우 `client.calls.create` 메서드에 전달되는 URL로 Python 애플리케이션의 URL을 사용합니다. 예를 들어 **MyTwiML**이라는 웹 애플리케이션이 Azure 호스티드 서비스에 배포되어 있으면 다음 예제에 표시된 것처럼 URL을 웹후크로 사용할 수 있습니다.

    from twilio.rest import TwilioRestClient

    account_sid = "your_twilio_account_sid"
    auth_token = "your_twilio_authentication_token"
    from_number = "NNNNNNNNNNN"
    to_number = "NNNNNNNNNNN"
    url = "http://your-domain-label.centralus.cloudapp.azure.com/MyTwiML/"

    # Initialize the Twilio client.
    client = TwilioRestClient(account_sid, auth_token)

    # Make the call.
    call = client.calls.create(to=to_number,
                               from_=from_number,
                               url=url)
    print(call.sid)

## <a id="AdditionalServices"></a>방법: 추가 Twilio 서비스 사용
여기에서 보여 준 예뿐만 아니라 Twilio는 Azure 애플리케이션에서 Twilio 기능을 활용할 수 있는 웹 기반 API를 제공합니다. 자세한 내용은 [Twilio API 설명서][twilio_api]를 참조하세요.

## <a id="NextSteps"></a>다음 단계
Twilio 서비스의 기본 사항을 배웠으며 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [Twilio 보안 지침][twilio_security_guidelines]
* [Twilio 사용 방법 가이드 및 예제 코드][twilio_howtos]
* [Twilio 빠른 시작 자습서][twilio_quickstarts]
* [Twilio on GitHub][twilio_on_github]
* [Twilio 지원 문의][twilio_support]

[special_offer]: https://ahoy.twilio.com/azure
[twilio_python]: https://github.com/twilio/twilio-python
[twilio_lib_docs]: https://www.twilio.com/docs/libraries/python
[twilio_github_readme]: https://github.com/twilio/twilio-python/blob/master/README.md

[twimlet_message_url]: https://twimlets.com/message
[twimlet_message_url_hello_world]: https://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: https://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: https://www.twilio.com/docs/api/twiml
[twilio_api]: https://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_console]:  https://www.twilio.com/console
[twilio_security_guidelines]: https://www.twilio.com/docs/security
[twilio_howtos]: https://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: https://www.twilio.com/help/contact
[twilio_quickstarts]: https://www.twilio.com/docs/quickstart
