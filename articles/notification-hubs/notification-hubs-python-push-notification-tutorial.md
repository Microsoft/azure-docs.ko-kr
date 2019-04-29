---
title: Python과 함께 Notification Hubs를 사용하는 방법
description: Python 백 엔드에서 Azure Notification Hubs를 사용하는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 5640dd4a-a91e-4aa0-a833-93615bde49b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: python
ms.devlang: php
ms.topic: article
ms.author: jowargo
ms.date: 01/04/2019
ms.openlocfilehash: 43a691ff9025cdb39786f965be6a2fca1b33bd3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458455"
---
# <a name="how-to-use-notification-hubs-from-python"></a>Python에서 Notification Hubs를 사용하는 방법

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

MSDN 문서 [Notification Hubs REST API](https://msdn.microsoft.com/library/dn223264.aspx)에 설명된 대로 Notification Hubs REST 인터페이스를 사용하여 Java/PHP/Python/Ruby 백 엔드에서 모든 Notification Hubs 기능에 액세스할 수 있습니다.

> [!NOTE]
> 이는 Python에서 알림 보내기를 구현하기 위한 샘플 참조 구현이며 공식적으로 지원되는 알림 허브 Python SDK가 아닙니다. 샘플은 Python 3.4를 사용하여 만들어졌습니다.

이 문서에서는 다음 방법을 안내합니다.

- Python에서 Notification Hubs 기능에 대한 REST 클라이언트를 빌드하는 방법
- Python 인터페이스를 사용하여 알림 허브 REST API에 알림을 보냅니다.
- 디버그/교육 용도로 HTTP REST 요청/응답의 덤프를 가져옵니다.

선택한 모바일 플랫폼에 대한 [시작 자습서](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) 에 따라 Python에서 백 엔드 부분을 구현할 수 있습니다.

> [!NOTE]
> 샘플 범위는 알림 보내기로만 제한되며 등록 관리는 수행하지 않습니다.

## <a name="client-interface"></a>클라이언트 인터페이스

기본 클라이언트 인터페이스에서는 [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx)에서 제공되는 것과 같은 메서드를 제공할 수 있습니다. 따라서 이 인터페이스를 통해 현재 이 사이트에서 사용 가능하며 인터넷 커뮤니티에서 제공한 모든 자습서 및 샘플을 직접 변환할 수 있습니다.

[Python REST 래퍼 샘플]에서 사용 가능한 모든 코드를 찾을 수 있습니다.

예를 들어 클라이언트를 만들려면 다음을 수행합니다.

```python
isDebug = True
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

Windows 알림 메시지를 보내려면

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Hello world!</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

## <a name="implementation"></a>구현

아직 수행하지 않았으면 백 엔드를 구현해야 하는 [시작 자습서]의 마지막 섹션까지 수행합니다.

전체 REST 래퍼를 구현하는 방법에 대한 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/dn530746.aspx)을 참조하세요. 이 섹션에서는 Notification Hubs REST 엔드포인트에 액세스하고 알림을 보내는 데 필요한 기본 단계의 Python 구현에 대해 설명합니다.

1. 연결 문자열 구문 분석
2. 인증 토큰 생성
3. HTTP REST API를 사용하여 알림 보내기

### <a name="parse-the-connection-string"></a>연결 문자열 구문 분석

연결 문자열을 구문 분석하는 생성자가 포함된 클라이언트를 구현하는 기본 클래스는 다음과 같습니다.

```python
class NotificationHub:
    API_VERSION = "?api-version=2013-10"
    DEBUG_SEND = "&test"

    def __init__(self, connection_string=None, hub_name=None, debug=0):
        self.HubName = hub_name
        self.Debug = debug

        # Parse connection string
        parts = connection_string.split(';')
        if len(parts) != 3:
            raise Exception("Invalid ConnectionString.")

        for part in parts:
            if part.startswith('Endpoint'):
                self.Endpoint = 'https' + part[11:]
            if part.startswith('SharedAccessKeyName'):
                self.SasKeyName = part[20:]
            if part.startswith('SharedAccessKey'):
                self.SasKeyValue = part[16:]
```

### <a name="create-security-token"></a>보안 토큰 만들기

보안 토큰 만들기에 대한 자세한 내용은 [여기](https://msdn.microsoft.com/library/dn495627.aspx)를 참조하세요.
`NotificationHub` 클래스에 다음 메서드를 추가하여 현재 요청의 URI 및 연결 문자열에서 추출된 자격 증명에 따라 토큰을 만듭니다.

```python
@staticmethod
def get_expiry():
    # By default returns an expiration of 5 minutes (=300 seconds) from now
    return int(round(time.time() + 300))

@staticmethod
def encode_base64(data):
    return base64.b64encode(data)

def sign_string(self, to_sign):
    key = self.SasKeyValue.encode('utf-8')
    to_sign = to_sign.encode('utf-8')
    signed_hmac_sha256 = hmac.HMAC(key, to_sign, hashlib.sha256)
    digest = signed_hmac_sha256.digest()
    encoded_digest = self.encode_base64(digest)
    return encoded_digest

def generate_sas_token(self):
    target_uri = self.Endpoint + self.HubName
    my_uri = urllib.parse.quote(target_uri, '').lower()
    expiry = str(self.get_expiry())
    to_sign = my_uri + '\n' + expiry
    signature = urllib.parse.quote(self.sign_string(to_sign))
    auth_format = 'SharedAccessSignature sig={0}&se={1}&skn={2}&sr={3}'
    sas_token = auth_format.format(signature, expiry, self.SasKeyName, my_uri)
    return sas_token
```

### <a name="send-a-notification-using-http-rest-api"></a>HTTP REST API를 사용하여 알림 보내기

먼저 알림을 나타내는 클래스를 정의합니다.

```python
class Notification:
    def __init__(self, notification_format=None, payload=None, debug=0):
        valid_formats = ['template', 'apple', 'fcm', 'windows', 'windowsphone', "adm", "baidu"]
        if not any(x in notification_format for x in valid_formats):
            raise Exception(
                "Invalid Notification format. " +
                "Must be one of the following - 'template', 'apple', 'fcm', 'windows', 'windowsphone', 'adm', 'baidu'")

        self.format = notification_format
        self.payload = payload

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry
        # in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        self.headers = None
```

이 클래스는 기본 알림 본문(또는 템플릿 알림의 속성 집합), 형식(기본 플랫폼 또는 템플릿)이 포함된 헤더 집합 및 플랫폼 특정 속성(예: Apple 만료 속성 및 WNS 헤더)에 대한 컨테이너입니다.

모든 사용할 수 있는 옵션은 [Notification Hubs REST API 설명서](https://msdn.microsoft.com/library/dn495827.aspx) 및 특정 알림 플랫폼의 형식을 참조하세요.

이제 이 클래스를 사용하여 `NotificationHub` 클래스 내부에 알림 보내기 메서드를 작성합니다.

```python
def make_http_request(self, url, payload, headers):
    parsed_url = urllib.parse.urlparse(url)
    connection = http.client.HTTPSConnection(parsed_url.hostname, parsed_url.port)

    if self.Debug > 0:
        connection.set_debuglevel(self.Debug)
        # adding this querystring parameter gets detailed information about the PNS send notification outcome
        url += self.DEBUG_SEND
        print("--- REQUEST ---")
        print("URI: " + url)
        print("Headers: " + json.dumps(headers, sort_keys=True, indent=4, separators=(' ', ': ')))
        print("--- END REQUEST ---\n")

    connection.request('POST', url, payload, headers)
    response = connection.getresponse()

    if self.Debug > 0:
        # print out detailed response information for debugging purpose
        print("\n\n--- RESPONSE ---")
        print(str(response.status) + " " + response.reason)
        print(response.msg)
        print(response.read())
        print("--- END RESPONSE ---")

    elif response.status != 201:
        # Successful outcome of send message is HTTP 201 - Created
        raise Exception(
            "Error sending notification. Received HTTP code " + str(response.status) + " " + response.reason)

    connection.close()

def send_notification(self, notification, tag_or_tag_expression=None):
    url = self.Endpoint + self.HubName + '/messages' + self.API_VERSION

    json_platforms = ['template', 'apple', 'fcm', 'adm', 'baidu']

    if any(x in notification.format for x in json_platforms):
        content_type = "application/json"
        payload_to_send = json.dumps(notification.payload)
    else:
        content_type = "application/xml"
        payload_to_send = notification.payload

    headers = {
        'Content-type': content_type,
        'Authorization': self.generate_sas_token(),
        'ServiceBusNotification-Format': notification.format
    }

    if isinstance(tag_or_tag_expression, set):
        tag_list = ' || '.join(tag_or_tag_expression)
    else:
        tag_list = tag_or_tag_expression

    # add the tags/tag expressions to the headers collection
    if tag_list != "":
        headers.update({'ServiceBusNotification-Tags': tag_list})

    # add any custom headers to the headers collection that the user may have added
    if notification.headers is not None:
        headers.update(notification.headers)

    self.make_http_request(url, payload_to_send, headers)

def send_apple_notification(self, payload, tags=""):
    nh = Notification("apple", payload)
    self.send_notification(nh, tags)

def send_fcm_notification(self, payload, tags=""):
    nh = Notification("fcm", payload)
    self.send_notification(nh, tags)

def send_adm_notification(self, payload, tags=""):
    nh = Notification("adm", payload)
    self.send_notification(nh, tags)

def send_baidu_notification(self, payload, tags=""):
    nh = Notification("baidu", payload)
    self.send_notification(nh, tags)

def send_mpns_notification(self, payload, tags=""):
    nh = Notification("windowsphone", payload)

    if "<wp:Toast>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'toast', 'X-NotificationClass': '2'}
    elif "<wp:Tile>" in payload:
        nh.headers = {'X-WindowsPhone-Target': 'tile', 'X-NotificationClass': '1'}

    self.send_notification(nh, tags)

def send_windows_notification(self, payload, tags=""):
    nh = Notification("windows", payload)

    if "<toast>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/toast'}
    elif "<tile>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/tile'}
    elif "<badge>" in payload:
        nh.headers = {'X-WNS-Type': 'wns/badge'}

    self.send_notification(nh, tags)

def send_template_notification(self, properties, tags=""):
    nh = Notification("template", properties)
    self.send_notification(nh, tags)
```

이러한 메서드는 알림을 보내기 위한 올바른 본문과 헤더가 있는 알림 허브의 /messages 엔드포인트로 HTTP POST 요청을 보냅니다.

### <a name="using-debug-property-to-enable-detailed-logging"></a>디버그 속성을 통해 자세한 로깅 사용

알림 허브를 초기화하는 동안 디버그 속성을 사용하면 HTTP 요청 및 응답 덤프에 대한 자세한 로깅 정보 및 자세한 알림 메시지 전송 결과가 기록됩니다.
[Notification Hubs TestSend 속성](https://docs.microsoft.com/previous-versions/azure/reference/dn495827(v=azure.100))은 알림 보내기 결과에 대한 자세한 정보를 반환합니다.
이 속성을 사용하려면 다음 코드를 사용하여 초기화합니다.

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName", isDebug)
```

결과적으로 Notification Hub 보내기 요청 HTTP URL에 "test" 쿼리 문자열이 추가됩니다.

## <a name="complete-tutorial"></a>자습서 완료

이제 Python 백 엔드에서 알림을 보내 시작 자습서를 완료할 수 있습니다.

Notification Hubs 클라이언트를 초기화합니다( [시작 자습서]에 설명된 대로 연결 문자열 및 허브 이름 대체).

```python
hub = NotificationHub("myConnectionString", "myNotificationHubName")
```

그리고 대상 모바일 플랫폼에 따라 보내기 코드를 추가합니다. 또한 이 샘플은 플랫폼에 따라 알림 보내기를 사용하도록 설정하는 상위 수준의 메서드를 추가합니다(예: send_windows_notification(Windows), send_apple_notification(Apple) 등).

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows 스토어 및 Windows Phone 8.1(비 Silverlight)

```python
wns_payload = """<toast><visual><binding template=\"ToastText01\"><text id=\"1\">Test</text></binding></visual></toast>"""
hub.send_windows_notification(wns_payload)
```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 및 8.1 Silverlight

```python
hub.send_mpns_notification(toast)
```

### <a name="ios"></a>iOS

```python
alert_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_apple_notification(alert_payload)
```

### <a name="android"></a>Android

```python
fcm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_fcm_notification(fcm_payload)
```

### <a name="kindle-fire"></a>Kindle Fire

```python
adm_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_adm_notification(adm_payload)
```

### <a name="baidu"></a>Baidu

```python
baidu_payload = {
    'data':
        {
            'msg': 'Hello!'
        }
}
hub.send_baidu_notification(baidu_payload)
```

Python 코드를 실행하면 대상 디바이스에 나타나는 알림이 생성됩니다.

## <a name="examples"></a>예

### <a name="enabling-the-debug-property"></a>`debug` 속성 사용

NotificationHub를 초기화하는 동안 디버그 플래그를 사용하도록 설정하면, 다음과 같이 자세한 HTTP 요청 및 응답 덤프 요청에 전달된 HTTP 헤더뿐만 아니라 Notification Hub에서 받은 HTTP 응답을 이해할 수 있는 NotificationOutcome도 표시됩니다.

![][1]

예를 들어 메시지가 푸시 알림 서비스로 전송되면

- 자세한 알림 허브 결과가 표시됩니다.
    ```xml
    <Outcome>The Notification was successfully sent to the Push Notification System</Outcome>
    ```
- 푸시 알림의 대상을 찾을 수 없는 경우 등록에 불일치 태그가 있어서 알림을 전달할 등록을 찾을 수 없음을 나타내는 다음 출력이 응답으로 표시되기를 원할 것입니다.
    ```xml
    '<NotificationOutcome xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect" xmlns:i="https://www.w3.org/2001/XMLSchema-instance"><Success>0</Success><Failure>0</Failure><Results i:nil="true"/></NotificationOutcome>'
    ```

### <a name="broadcast-toast-notification-to-windows"></a>Windows로 보내는 브로드캐스트 알림 메시지

Windows 클라이언트로 브로드캐스트 알림 메시지를 보낼 때 전송되는 헤더를 확인합니다.

```python
hub.send_windows_notification(wns_payload)
```

![][2]

### <a name="send-notification-specifying-a-tag-or-tag-expression"></a>태그(또는 태그 식)를 지정하여 알림 보내기

HTTP 요청에 추가되는 태그 HTTP 헤더를 확인합니다. 아래 예제에서는 '스포츠' 페이로드가 있는 등록에만 알림을 보냅니다.

```python
hub.send_windows_notification(wns_payload, "sports")
```

![][3]

### <a name="send-notification-specifying-multiple-tags"></a>여러 태그를 지정하여 알림 보내기

여러 태그를 보낼 때 태그 HTTP 헤더가 어떻게 변경되는지 확인합니다.

```python
tags = {'sports', 'politics'}
hub.send_windows_notification(wns_payload, tags)
```

![][4]

### <a name="templated-notification"></a>템플릿 기반 알림

형식 HTTP 헤더가 변경되고 페이로드 본문이 HTTP 요청 본문의 일부로 전송됩니다.

**클라이언트 쪽 - 등록된 템플릿:**

```python
var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(greeting_en)</text></binding></visual></toast>";
```

**서버 쪽 - 페이로드 보내기:**

```python
template_payload = {'greeting_en': 'Hello', 'greeting_fr': 'Salut'}
hub.send_template_notification(template_payload)
```

![][5]

## <a name="next-steps"></a>다음 단계

이 문서에서는 Notification Hubs에 대한 Python REST 클라이언트를 만드는 방법을 알아보았습니다. 여기에서 다음을 할 수 있습니다.

- 이 문서의 모든 코드가 포함된 전체 [Python REST 래퍼 샘플]을 다운로드합니다.
-  [속보 자습서]
-  [지역화 뉴스 자습서]

<!-- URLs -->
[Python REST 래퍼 샘플]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-python
[시작 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[속보 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[지역화 뉴스 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/

<!-- Images. -->
[1]: ./media/notification-hubs-python-backend-how-to/DetailedLoggingInfo.png
[2]: ./media/notification-hubs-python-backend-how-to/BroadcastScenario.png
[3]: ./media/notification-hubs-python-backend-how-to/SendWithOneTag.png
[4]: ./media/notification-hubs-python-backend-how-to/SendWithMultipleTags.png
[5]: ./media/notification-hubs-python-backend-how-to/TemplatedNotification.png
