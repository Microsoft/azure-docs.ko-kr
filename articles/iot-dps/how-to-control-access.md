---
title: IoT Device Provisioning Service의 보안 엔드포인트 | Microsoft Docs
description: 개념 - 백 엔드 앱용 IoT Device Provisioning 서비스에 대한 액세스를 제어하는 방법 보안 토큰에 대한 정보가 포함됩니다.
author: wesmc7777
manager: timlt
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: wesmc
ms.openlocfilehash: 7ff622ceac9c49eda7ba6bca1a8bb3aaabccb816
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60626663"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Azure IoT Hub Device Provisioning Service에 대한 액세스 제어

이 문서에서는 IoT Device Provisioning 서비스를 보호하는 옵션을 설명합니다. 프로비전 서비스는 *권한*을 사용하여 각 엔드포인트에 대한 액세스를 부여합니다. 사용 권한은 기능에 따라 서비스 인스턴스에 대한 액세스를 제한합니다.

이 문서에서는 다음을 설명합니다.

* 프로비전 서비스에 액세스하기 위해 백 엔드 앱에 부여할 수 있는 다양한 권한
* 권한을 확인하기 위해 사용되는 인증 프로세스 및 토큰.

### <a name="when-to-use"></a>사용하는 경우

프로비전 서비스 엔드포인트에 액세스하려면 적절한 권한이 있어야 합니다. 예를 들어 백 엔드 앱은 서비스에 보내는 모든 메시지와 함께 보안 자격 증명을 포함하는 토큰을 포함해야 합니다.

## <a name="access-control-and-permissions"></a>액세스 제어 및 권한

[권한](#device-provisioning-service-permissions)은 다음과 같은 방식으로 부여할 수 있습니다.

* **공유 액세스 권한 부여 정책**. 공유 액세스 정책은 모든 조합의 [권한](#device-provisioning-service-permissions)을 부여할 수 있습니다. [Azure Portal][lnk-management-portal]에서 또는 프로그래밍 방식으로 [Device Provisioning Service REST API][lnk-resource-provider-apis]를 사용하여 정책을 정의할 수 있습니다. 새로 만든 프로비전 서비스에는 다음과 같은 기본 정책이 있습니다.

* **provisioningserviceowner**: 모든 사용 권한이 있는 정책입니다.

> [!NOTE]
> 자세한 내용은 [사용 권한](#device-provisioning-service-permissions)을 참조하세요.

## <a name="authentication"></a>Authentication

Azure IoT Hub Device Provisioning Service는 공유 액세스 정책에 대한 토큰을 확인하여 엔드포인트에 대한 액세스를 부여합니다. 대칭 키와 같은 보안 자격 증명은 통신 중에 전송되지 않습니다.

> [!NOTE]
> Device Provisioning Service 리소스 공급자는 [Azure Resource Manager][lnk-azure-resource-manager]의 모든 공급자처럼 Azure 구독을 통해 보호됩니다.

보안 토큰을 생성 및 사용하는 방법에 대한 자세한 내용은 다음 섹션을 참조하세요.

HTTP는 유일하게 지원되는 프로토콜이며 **권한 부여** 요청 헤더에서 유효한 토큰을 포함하여 인증을 구현합니다.

#### <a name="example"></a>예
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> [Azure IoT Device Provisioning Service SDK][lnk-sdks]는 서비스에 연결할 때 토큰을 자동으로 생성합니다.

## <a name="security-tokens"></a>보안 토큰

Device Provisioning Service는 네트워크에서 키가 전송되는 것을 피하기 위해 보안 토큰을 사용하여 서비스를 인증합니다. 또한 보안 토큰은 유효 기간 및 범위가 제한됩니다. [Azure IoT Device Provisioning Service SDK][lnk-sdks]는 특별한 구성이 필요하지 않고 토큰을 자동으로 생성합니다. 일부 시나리오에서는 사용자가 보안 토큰을 직접 생성하고 사용해야 합니다. 이러한 시나리오에는 HTTP 화면의 직접 사용이 포함됩니다.

### <a name="security-token-structure"></a>보안 토큰 구조

보안 토큰을 사용하여 IoT Device Provisioning Service에서 특정 기능에 대한 시간 제한 액세스 권한을 부여합니다. 권한 부여를 얻어 프로비전 서비스에 연결하려면 서비스는 공유 액세스 또는 대칭 키 중 하나로 서명된 보안 토큰을 전송해야 합니다.

공유 액세스 키로 서명된 토큰은 공유 액세스 정책 권한과 관련된 모든 기능에 대한 액세스를 부여합니다. 

보안 토큰의 형식은 다음과 같습니다.

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

다음은 예상 값입니다.

| 값 | 설명 |
| --- | --- |
| {signature} |형식의 HMAC-SHA256 서명 문자열은 `{URL-encoded-resourceURI} + "\n" + expiry`입니다. **중요**: 키는 base64에서 디코딩되며 HMAC-SHA256 계산을 수행하는 데 키로 사용됩니다.|
| {expiry} |1970년 1월 1일 epoch 0시 UTC 이후의 초 수에 대한 UTF8 문자열입니다. |
| {URL-encoded-resourceURI} | 소문자 리소스 URI의 소문자 URL 인코딩. 이 토큰으로 액세스할 수 있는 엔드포인트의 URI 접두사(세그먼트별)이며 IoT Device Provisioning Service의 호스트 이름으로 시작합니다(프로토콜 없음). 예: `mydps.azure-devices-provisioning.net`. |
| {policyName} |이 토큰을 참조하는 공유 액세스 정책의 이름입니다. |

**접두사에 대한 참고**: 문자가 아니라 세그먼트에 의해 계산된 URI 접두사입니다. 예를 들어 `/a/b`는 `/a/b/c`에 대한 접두사이지만 `/a/bc`에 대한 접두사는 아닙니다.

다음 Node.js 코드 조각은 입력 `resourceUri, signingKey, policyName, expiresInMins`의 토큰을 계산하는 **generateSasToken**이라는 함수를 보여줍니다. 다음 섹션에서는 여러 토큰 사용 사례에 대해 서로 다른 입력을 초기화하는 방법을 자세히 설명합니다.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

비교를 위해 보안 토큰을 생성하는 동일한 Python 코드는 다음과 같습니다.

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> IoT Device Provisioning Service 머신에서 토큰의 유효 기간이 확인되므로 토큰을 생성하는 머신의 시계에서 편차가 최소여야 합니다.

### <a name="use-security-tokens-from-service-components"></a>서비스 구성 요소에서 보안 토큰 사용

앞에서 설명했듯이 서비스 구성 요소는 적절한 권한을 부여하는 공유 액세스 정책을 사용하여 보안 토큰을 생성할 수 있습니다.

다음은 엔드포인트에 노출된 서비스 기능입니다.

| 엔드포인트 | 기능 |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Device Provisioning Service에서 디바이스 등록 항목을 제공합니다. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |디바이스 등록 그룹을 관리하기 위한 작업을 제공합니다. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |디바이스 등록 상태를 검색 및 관리하기 위한 작업을 제공합니다. |


예를 들어 **enrollmentread**라는 미리 생성된 공유 액세스 정책을 사용하여 생성하는 서비스는 다음 매개 변수로 토큰을 만듭니다.

* 리소스 URI: `{mydps}.azure-devices-provisioning.net`,
* 서명 키: `enrollmentread` 정책의 키 중 하나,
* 정책 이름: `enrollmentread`,
* 임의의 만료 시간.backn

![포털에서 Device Provisioning 서비스 인스턴스에 대한 공유 액세스 정책 만들기][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

모든 등록 레코드에 대한 읽기 권한을 부여하는 결과는 다음과 같습니다.

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>참조 항목:

다음 참조 항목에서는 IoT Device Provisioning Service 액세스 제어에 대한 자세한 정보를 제공합니다.

### <a name="device-provisioning-service-permissions"></a>Device Provisioning Service 권한

다음 테이블에는 IoT Device Provisioning Service에 대한 액세스를 제어하는 데 사용할 수 있는 권한이 나열되어 있습니다.

| 사용 권한 | 메모 |
| --- | --- |
| **ServiceConfig** |서비스 구성을 변경하기 위한 액세스 권한을 부여합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용됩니다. |
| **EnrollmentRead** |디바이스 등록 및 등록 그룹에 대한 읽기 권한을 부여합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용됩니다. |
| **EnrollmentWrite** |디바이스 등록 및 등록 그룹에 대한 쓰기 권한을 부여합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용됩니다. |
| **RegistrationStatusRead** |디바이스 등록 상태에 대한 읽기 권한을 부여합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용됩니다. |
| **RegistrationStatusWrite**  |디바이스 등록 상태에 대한 삭제 권한을 부여합니다. <br/>이 사용 권한은 백 엔드 클라우드 서비스에서 사용됩니다. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/resource-group-overview.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iot-dps/
