---
title: 공유 액세스 서명을 사용 하 여 Azure Event Hubs에 대 한 액세스 인증
description: 이 문서에서는 공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대 한 액세스를 인증 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: bf1f42020237c0907ec7656735adfa46a21370f9
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008321"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>SAS (공유 액세스 서명)를 사용 하 여 Event Hubs 리소스에 대 한 액세스 인증
공유 액세스 서명 (SAS)은 공유 액세스 서명을 보유 한 클라이언트에 부여 하는 액세스 유형에 대 한 세부적인 제어를 제공 합니다. 다음은 SAS에서 설정할 수 있는 몇 가지 컨트롤입니다. 

- 시작 시간 및 만료 시간을 포함 하 여 SAS가 유효한 간격입니다.
- SAS에서 부여된 권한입니다. 예를 들어 Event Hubs 네임 스페이스에 대 한 SAS는 수신 권한을 부여할 수 있지만 send 권한은 부여할 수 없습니다.
- 유효한 자격 증명을 제공하는 클라이언트만 이벤트 허브로 데이터를 보낼 수 있습니다.
- 클라이언트는 다른 클라이언트를 가장할 수 없습니다.
- 악성 클라이언트는 이벤트 허브로 데이터를 보내지 못하도록 차단할 수 있습니다.

이 문서에서는 SAS를 사용 하 여 Event Hubs 리소스에 대 한 액세스를 인증 하는 방법을 다룹니다. SAS를 사용 하 여 Event Hubs 리소스에 대 한 액세스 **권한을 부여** 하는 방법을 알아보려면 [이 문서](authorize-access-shared-access-signature.md)를 참조 하세요. 

> [!NOTE]
> 가능 하면 공유 액세스 서명을 사용 하는 대신 보안 모범 사례로 Azure AD 자격 증명을 사용 하는 것이 좋습니다 .이는 더 쉽게 손상 될 수 있습니다. SAS (공유 액세스 서명)를 계속 사용 하 여 Event Hubs 리소스에 대 한 세분화 된 액세스 권한을 부여할 수 있지만, Azure AD는 SAS 토큰을 관리 하거나 손상 된 SAS를 해지 하는 것에 대해 걱정할 필요 없이 유사한 기능을 제공 합니다.
> 
> Azure Event Hubs의 Azure AD 통합에 대 한 자세한 내용은 [AZURE ad를 사용 하 여 Event Hubs에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)를 참조 하세요. 


## <a name="configuring-for-sas-authentication"></a>SAS 인증 구성
Event Hubs 네임 스페이스 또는 엔터티 (이벤트 허브의 Kafka 토픽)에서 EventHubs 공유 액세스 권한 부여 규칙을 구성할 수 있습니다. 소비자 그룹에 대 한 공유 액세스 권한 부여 규칙 구성은 현재 지원 되지 않지만 네임 스페이스 또는 엔터티에 구성 된 규칙을 사용 하 여 소비자 그룹에 대 한 액세스를 보호할 수 있습니다. 

다음 이미지는 샘플 엔터티에 권한 부여 규칙을 적용 하는 방법을 보여 줍니다. 

![권한 부여 규칙 구성](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

이 예제에서 샘플 Event Hubs 네임 스페이스 (ExampleNamespace)에는 eh1 및 topic1 라는 두 개의 엔터티가 있습니다. 권한 부여 규칙은 엔터티 수준 뿐만 아니라 네임 스페이스 수준 에서도 정의 됩니다.  

관리자가 sendRuleNS 및 listenRuleNS 권한 부여 규칙은 이벤트 허브 인스턴스 eh1 및 항목 t1에 모두 적용 됩니다. ListenRule 및 sendRule-eh 권한 부여 규칙은 이벤트 허브 인스턴스에만 적용 되 고 sendRuleT authorization rule은 토픽 topic1에만 적용 됩니다. 

SendRuleNS 권한 부여 규칙을 사용 하는 경우 클라이언트 응용 프로그램은 eh1와 topic1 둘 다에 보낼 수 있습니다. SendRuleT 권한 부여 규칙을 사용 하는 경우 topic1에 대 한 세분화 된 액세스를 적용 하므로, 액세스에 대해이 규칙을 사용 하는 클라이언트 응용 프로그램은 이제 eh1에는 보낼 수 없지만 topic1에는 보낼 수 없습니다

## <a name="generate-a-shared-access-signature-token"></a>공유 액세스 서명 토큰 생성 
권한 부여 규칙 이름에 대한 액세스 권한이 있는 모든 클라이언트 및 해당 서명 키 중 하나는 SAS 토큰을 생성할 수 있습니다. 토큰은 다음 형식으로 문자열을 선별하여 생성됩니다.

- `se`  – 토큰 만료 인스턴트. 토큰이 만료 되 면 1 월 1970 (UNIX epoch) 1 월 1 일에 epoch 00:00:00 UTC 이후 초를 반영 하는 정수
- `skn` – 인증 규칙의 이름이 며,이는 SAS 키 이름입니다.
- `sr` – 액세스 되는 리소스의 URI입니다.
- `sig` 서명과.

서명 문자열은 리소스 URI (이전 섹션에서 설명 하는 범위)에 대해 계산 된 SHA-256 해시와 토큰 만료 인스턴트의 문자열 표현을 CRLF로 구분 하 여 계산 합니다.

해시 계산은 다음 의사 코드와 유사하며 256비트/32바이트 해시 값을 반환합니다. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

토큰은 수신자가 동일한 매개 변수를 사용하여 해시를 다시 계산할 수 있도록 발급자가 유효한 서명 키를 소유하는지 확인하여 해시되지 않은 값을 포함합니다.

리소스 URI은 액세스를 하려는 Service Bus 리소스의 전체 URI입니다. 예를 들면 http:// <namespace> . servicebus.windows.net/ <entityPath> 이 고, 그렇지 않으면 `sb://<namespace>.servicebus.windows.net/<entityPath>;` `http://contoso.servicebus.windows.net/eventhubs/eh1` 입니다.

URI는 %로 인코딩되어야 합니다.

이 URI로 또는 부모 계층 중 하나에 의해 지정된 엔터티에 서명에 사용된 공유 액세스 권한 부여 규칙을 구성해야 합니다. 예를 들어 이전 예에서 `http://contoso.servicebus.windows.net/eventhubs/eh1` 또는 `http://contoso.servicebus.windows.net`입니다.

SAS 토큰은 서명 문자열에 사용 된와 접두사로 추가 된 모든 리소스에 유효 <resourceURI> 합니다.

> [!NOTE]
> 공유 액세스 정책을 사용 하 여 Event Hubs에 대 한 액세스 토큰을 생성 합니다. 자세한 내용은 [공유 액세스 권한 부여 정책](authorize-access-shared-access-signature.md#shared-access-authorization-policies)을 참조 하세요.

### <a name="generating-a-signaturetoken-from-a-policy"></a>정책에서 서명 (토큰) 생성 
다음 섹션에서는 공유 액세스 서명 정책을 사용 하 여 SAS 토큰을 생성 하는 방법을 보여 줍니다.

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>SAS를 사용 하 여 Event Hubs 게시자 인증 
이벤트 게시자는 이벤트 허브에 대한 가상 엔드포인트를 정의합니다. 게시자는 메시지를 수신 하지 않고 이벤트 허브에 메시지를 보내는 데만 사용할 수 있습니다.

일반적으로 이벤트 허브에서는 클라이언트당 하나의 게시자를 사용합니다. 이벤트 허브의 게시자에게 전달되는 모든 메시지는 해당 이벤트 허브 내의 큐에 삽입됩니다. 게시자는 세분화 되는 액세스 제어를 지원 합니다.

각 Event Hubs 클라이언트는 클라이언트에 업로드되는 고유 토큰을 할당받습니다. 토큰은 각 고유 토큰이 서로 다른 고유 게시자에 대 한 액세스 권한을 부여 하도록 생성 됩니다. 토큰을 보유 하는 클라이언트는 한 게시자에만 보낼 수 있으며 다른 게시자에는 보낼 수 없습니다. 여러 클라이언트에서 동일한 토큰을 공유 하는 경우 각 클라이언트는 게시자를 공유 합니다.

모든 토큰은 SAS 키로 할당 됩니다. 일반적으로 모든 토큰은 동일한 키로 서명됩니다. 클라이언트는 키를 인식 하지 못하므로 클라이언트는 제조 토큰을 방지 합니다. 클라이언트는 만료 될 때까지 동일한 토큰에 대해 작동 합니다.

예를 들어 Event Hubs에 대 한 보내기/게시로 한정 된 권한 부여 규칙을 정의 하려면 전송 권한 부여 규칙을 정의 해야 합니다. 이 작업은 네임 스페이스 수준에서 수행 하거나 특정 엔터티 (event hubs 인스턴스 또는 토픽)에 더 세부적인 범위를 제공할 수 있습니다. 이러한 세부적인 액세스로 범위가 지정 된 클라이언트 또는 응용 프로그램을 Event Hubs 게시자 라고 합니다. 이렇게 하려면 다음 단계를 따르십시오.

1. 게시 하려는 엔터티에 대 한 SAS 키를 만들어 **송신** 범위를 할당 합니다. 자세한 내용은 [공유 액세스 권한 부여 정책](authorize-access-shared-access-signature.md#shared-access-authorization-policies)을 참조 하세요.
2. 1 단계에서 생성 된 키를 사용 하 여 특정 게시자에 대 한 만료 시간으로 SAS 토큰을 생성 합니다.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. 토큰을 게시자 클라이언트에 제공 합니다 .이 토큰은 해당 토큰에 대 한 액세스 권한을 부여 하는 엔터티와 게시자에만 보낼 수 있습니다.

    토큰이 만료 되 면 클라이언트는 엔터티에 대 한 보내기/게시 액세스 권한을 잃게 됩니다. 


> [!NOTE]
> 권장 되지는 않지만 이벤트 허브 또는 네임 스페이스에 대 한 액세스 권한을 부여 하는 토큰이 있는 장치를 사용할 수 있습니다. 이 토큰을 보유 하는 모든 장치는 해당 이벤트 허브에 직접 메시지를 보낼 수 있습니다. 또한 디바이스에서 해당 이벤트 허브로 보내는 것을 차단할 수 없습니다.
> 
> 항상 특정 하 고 세분화 된 범위를 제공 하는 것이 좋습니다.

> [!IMPORTANT]
> 토큰이 생성된 후에 각 클라이언트는 자체의 고유한 토큰과 함께 프로비전됩니다.
>
> 클라이언트는 이벤트 허브로 데이터를 보낼 때 해당 요청에 토큰을 태그 합니다. 공격자가 도청 및 토큰 가로채기를 하지 못하도록 방지하려면 클라이언트와 이벤트 허브 간의 통신은 암호화된 채널을 통해 이루어져야 합니다.
> 
> 토큰이 공격자에 의해 도난당한 경우 공격자가 토큰을 도난당한 클라이언트로 가장할 수 있습니다. 게시자를 나열 하면 다른 게시자를 사용 하는 새 토큰을 받을 때까지 해당 클라이언트를 사용할 수 없도록 렌더링 합니다.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>SAS를 사용 하 여 Event Hubs 소비자 인증 
Event Hubs 생산자에 의해 생성 된 데이터를 사용 하는 백 엔드 응용 프로그램을 인증 하려면 Event Hubs 토큰 인증을 사용 하려면 해당 클라이언트에 Event Hubs 네임 스페이스나 이벤트 허브 인스턴스 또는 토픽에 할당 된 **관리** 권한 또는 **수신** 권한이 있어야 합니다. 데이터는 소비자 그룹을 사용 하 여 Event Hubs에서 사용 됩니다. SAS 정책은 세부적인 범위를 제공 하지만이 범위는 소비자 수준이 아니라 엔터티 수준 에서만 정의 됩니다. 이는 네임 스페이스 수준이 나 이벤트 허브 인스턴스 또는 토픽 수준에서 정의 된 권한이 해당 엔터티의 소비자 그룹에 적용 됨을 의미 합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

- [SAS를 사용 하 여 권한 부여](authenticate-shared-access-signature.md)
- [RBAC (역할 기반 액세스 제어)를 사용 하 여 권한 부여](authenticate-shared-access-signature.md)
- [Event Hubs에 대 한 자세한 정보](event-hubs-about.md)

다음 관련 문서를 참조 하세요.

- [Azure Active Directory를 사용 하 여 응용 프로그램에서 Azure Event Hubs에 대 한 요청 인증](authenticate-application.md)
- [Event Hubs 리소스에 액세스 하기 위해 Azure Active Directory를 사용 하 여 관리 id 인증](authenticate-managed-identity.md)
- [Azure Active Directory를 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-azure-active-directory.md)
- [공유 액세스 서명을 사용 하 여 Event Hubs 리소스에 대 한 액세스 권한 부여](authorize-access-shared-access-signature.md)
