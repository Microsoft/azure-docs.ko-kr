---
title: PHP에서 Notification Hubs를 사용하는 방법
description: PHP 백 엔드에서 Azure Notification Hubs를 사용하는 방법에 대해 알아봅니다.
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 054edaf321d90015840fd84e1697fca742fd7e1e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872192"
---
# <a name="how-to-use-notification-hubs-from-php"></a>PHP에서 Notification Hubs를 사용하는 방법

[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

MSDN 항목 [Notification Hubs REST API](https://msdn.microsoft.com/library/dn223264.aspx)에 설명된 대로 Notification Hubs REST 인터페이스를 사용하여 Java/PHP/Ruby 백 엔드에서 모든 Notification Hubs 기능에 액세스할 수 있습니다.

이 항목에서는 다음 방법을 보여 줍니다.

* PHP에서 Notification Hubs 기능에 대한 REST 클라이언트를 빌드하는 방법
* 선택한 모바일 플랫폼에 대한 [시작 자습서](notification-hubs-ios-apple-push-notification-apns-get-started.md)에 따라 PHP에서 백 엔드 부분을 구현합니다.

## <a name="client-interface"></a>클라이언트 인터페이스

기본 클라이언트 인터페이스에서는 [.NET Notification Hubs SDK](https://msdn.microsoft.com/library/jj933431.aspx)에서 제공되는 것과 같은 메서드를 제공할 수 있습니다. 이 인터페이스를 사용하여 현재 이 사이트에서 사용할 수 있고 인터넷의 커뮤니티에서 제공되는 모든 자습서와 샘플을 직접 변환할 수 있습니다.

[PHP REST 래퍼 샘플]에서 사용할 수 있는 모든 코드를 찾을 수 있습니다.

예를 들어 클라이언트를 만들려면 다음을 수행합니다.

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

iOS 기본 알림을 보내려면 다음을 수행합니다.

    ```php
    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);
    ```

## <a name="implementation"></a>구현

아직 수행하지 않았으면 백 엔드를 구현해야 하는 [시작 자습서]의 마지막 섹션까지 수행합니다.
필요하면 [PHP REST 래퍼 샘플]에서 코드를 사용하고 직접 [자습서 완료](#complete-tutorial) 섹션으로 이동할 수도 있습니다.

전체 REST 래퍼를 구현하는 방법에 대한 자세한 내용은 [MSDN](https://msdn.microsoft.com/library/dn530746.aspx)을 참조하세요. 이 섹션에서는 Notification Hubs REST 엔드포인트에 액세스하는 데 필요한 기본 단계의 PHP 구현에 대해 설명합니다.

1. 연결 문자열 구문 분석
2. 인증 토큰 생성
3. HTTP 호출 수행

### <a name="parse-the-connection-string"></a>연결 문자열 구문 분석

연결 문자열을 구문 분석하는 생성자가 포함된 클라이언트를 구현하는 기본 클래스는 다음과 같습니다.

    ```php
    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }
    ```

### <a name="create-a-security-token"></a>보안 토큰 만들기

[SAS 보안 토큰을 만드는](https://docs.microsoft.com/previous-versions/azure/reference/dn495627(v=azure.100)#create-sas-security-token) 방법에 대한 정보는 Azure 설명서를 참조하세요.

`NotificationHub` 클래스에 `generateSasToken` 메서드를 추가하여 현재 요청의 URI 및 연결 문자열에서 추출된 자격 증명에 따라 토큰을 만듭니다.

    ```php
    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }
    ```

### <a name="send-a-notification"></a>알림 보내기

먼저 알림을 나타내는 클래스를 정의합니다.

    ```php
    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "fcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }
    ```

이 클래스는 기본 알림 본문(또는 템플릿 알림의 경우 속성 집합) 및 형식(기본 플랫폼 또는 템플릿)과 플랫폼 특정 속성(예: Apple 만료 속성 및 WNS 헤더)이 포함된 헤더 집합에 대한 컨테이너입니다.

모든 사용할 수 있는 옵션은 [Notification Hubs REST API 설명서](https://msdn.microsoft.com/library/dn495827.aspx) 및 특정 알림 플랫폼의 형식을 참조하세요.

이제 이 클래스를 사용하여 `NotificationHub` 클래스 내부에서 알림 보내기 메서드를 쓸 수 있습니다.

    ```php
    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "fcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notification: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 
    ```

위의 메서드는 알림을 보내기 위한 올바른 본문과 헤더가 있는 알림 허브의 `/messages` 엔드포인트로 HTTP POST 요청을 보냅니다.

## <a name="complete-tutorial"></a>자습서 완료

이제 PHP 백 엔드에서 알림을 보내 시작 자습서를 완료할 수 있습니다.

Notification Hubs 클라이언트를 초기화합니다( [시작 자습서]에 설명된 대로 연결 문자열 및 허브 이름 대체).

    ```php
    $hub = new NotificationHub("connection string", "hubname");
    ```

그리고 대상 모바일 플랫폼에 따라 보내기 코드를 추가합니다.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows 스토어 및 Windows Phone 8.1(비 Silverlight)

    ```php
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);
    ```

### <a name="ios"></a>iOS

    ```php
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);
    ```

### <a name="android"></a>Android

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("fcm", $message);
    $hub->sendNotification($notification, null);
    ```

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 및 8.1 Silverlight

    ```php
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);
    ```

### <a name="kindle-fire"></a>Kindle Fire

    ```php
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);
    ```

이제 PHP 코드를 실행하면 대상 디바이스에 나타나는 알림이 생성되어야 합니다.

## <a name="next-steps"></a>다음 단계

이 항목에서는 Notification Hubs에 대한 단순한 Java REST 클라이언트를 만드는 방법을 알아보았습니다. 여기에서 다음을 할 수 있습니다.

* 위의 모든 코드가 포함된 전체 [PHP REST 래퍼 샘플]을 다운로드합니다.
* [속보 자습서]에서 Notification Hubs 태그 지정 기능에 대해 계속 알아봅니다.
* [사용자에게 알림 자습서]에서 개별 사용자에게 알림을 게시하는 방법을 알아봅니다.

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조하세요.

[PHP REST 래퍼 샘플]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[시작 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
