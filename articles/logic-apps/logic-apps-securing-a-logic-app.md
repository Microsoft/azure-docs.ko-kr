---
title: 액세스 및 데이터 보안
description: Azure Logic Apps의 입력, 출력, 요청 기반 트리거, 실행 기록, 관리 작업 및 기타 리소스에 대한 보안 액세스
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 08/27/2020
ms.openlocfilehash: 442b5acf3a6786b9fcaf0a96015a6df31215653c
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231421"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Azure Logic Apps에서 액세스 및 데이터 보호

Azure Logic Apps은 [Azure Storage](../storage/index.yml) 를 사용 하 여 [미사용 데이터](../security/fundamentals/encryption-atrest.md)를 저장 하 고 자동으로 암호화 합니다. 이 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다. 기본적으로 Azure Storage는 Microsoft 관리 키를 사용 하 여 데이터를 암호화 합니다. 자세한 내용은 [미사용 데이터에 대한 Azure Storage 암호화](../storage/common/storage-service-encryption.md)를 참조하세요.

Azure Logic Apps에서 중요 한 데이터 액세스 및 보호를 추가로 제어 하려면 다음 영역에서 추가 보안을 설정할 수 있습니다.

* [요청 기반 트리거에 대 한 인바운드 호출에 대 한 액세스](#secure-inbound-requests)
* [논리 앱 작업에 대한 액세스](#secure-operations)
* [실행 기록 입력 및 출력에 대한 액세스](#secure-run-history)
* [매개 변수 입력에 대한 액세스](#secure-action-parameters)
* [다른 서비스 및 시스템에 대 한 아웃 바운드 호출에 대 한 액세스](#secure-outbound-requests)
* [특정 커넥터에 대 한 연결 만들기 차단](#block-connections)
* [논리 앱에 대 한 격리 지침](#isolation-logic-apps)
* [Azure Logic Apps에 대 한 Azure 보안 기준](../logic-apps/security-baseline.md)

Azure의 보안에 대 한 자세한 내용은 다음 항목을 참조 하세요.

* [Azure 암호화 개요](../security/fundamentals/encryption-overview.md)
* [Azure 미사용 데이터 암호화](../security/fundamentals/encryption-atrest.md)
* [Azure Security Benchmark](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>요청 기반 트리거에 대 한 인바운드 호출에 대 한 액세스

[요청 트리거 또는](../connectors/connectors-native-reqres.md) [HTTP Webhook](../connectors/connectors-native-webhook.md) 트리거와 같은 요청 기반 트리거를 통해 논리 앱이 수신 하는 인바운드 호출은 암호화를 지원 하 고 [TLS (전송 계층 보안) 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security)를 사용 하 여 이전에 SSL(Secure Sockets Layer) (SSL)로 알려져 있습니다. Logic Apps 요청 트리거에 대 한 인바운드 호출 또는 HTTP Webhook 트리거 또는 동작에 대 한 콜백을 받을 때이 버전을 적용 합니다. TLS 핸드셰이크 오류가 발생하는 경우 TLS 1.2를 사용해야 합니다. 자세한 내용은 [TLS 1.0 문제 해결](/security/solving-tls1-problem)을 참조하세요.

인바운드 호출은 다음과 같은 암호 그룹을 지원 합니다.

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

인증 된 클라이언트만 논리 앱을 호출할 수 있도록 논리 앱에 대 한 인바운드 호출을 수신 하는 트리거에 대 한 액세스를 제한할 수 있는 추가 방법은 다음과 같습니다.

* [SAS(공유 액세스 서명) 생성](#sas)
* [Azure AD OAuth(Azure Active Directory 공개 인증) 사용](#enable-oauth)
* [Azure API Management을 사용 하 여 논리 앱 노출](#azure-api-management)
* [인바운드 IP 주소 제한](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>SAS(공유 액세스 서명) 생성

논리 앱의 모든 요청 엔드포인트에는 엔드포인트 URL에 [SAS(공유 액세스 서명)](/rest/api/storageservices/constructing-a-service-sas)가 있으며, 형식은 다음과 같습니다.

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

다음 표에 설명된 대로 각 URL에는 `sp`, `sv`, `sig` 쿼리 매개 변수가 포함됩니다.

| 쿼리 매개 변수 | Description |
|-----------------|-------------|
| `sp` | 사용이 허용된 HTTP 메서드에 대한 권한을 지정합니다. |
| `sv` | 서명을 생성하는 데 사용할 SAS 버전을 지정합니다. |
| `sig` | 트리거에 대한 액세스를 인증하는 데 사용할 서명을 지정합니다. 서명은 모든 URL 경로 및 속성에 관한 비밀 액세스 키를 포함한 SHA256 알고리즘을 사용하여 생성됩니다. 절대 노출되거나 공개되지 않는, 이 키는 암호화되고 논리 앱에 저장됩니다. 논리 앱은 암호 키로 만들어진 유효한 서명을 포함하는 트리거에 권한을 부여합니다. |
|||

SAS를 사용하여 액세스를 보호하는 방법에 대한 자세한 내용은 이 항목에서 다음 섹션을 참조하세요.

* [액세스 키 다시 생성](#access-keys)
* [만료 콜백 URL 만들기](#expiring-urls)
* [주 또는 보조 키를 사용하여 URL 만들기](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>액세스 키 다시 생성

언제든지 새 보안 액세스 키를 생성하려면 REST API 또는 Azure Portal을 사용합니다. 이전 키를 사용하는 이전에 생성된 모든 URL은 무효화되고 논리 앱을 트리거하기 위한 권한이 더 이상 부여되지 않습니다. 다시 생성한 후 검색한 URL은 새 액세스 키로 서명됩니다.

1. [Azure Portal](https://portal.azure.com)에서 다시 생성하려는 키가 있는 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **액세스 키**를 선택합니다.

1. 다시 생성하려는 키를 선택하고 프로세스를 마칩니다.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>만료 콜백 URL 만들기

요청 기반 트리거에 대한 엔드포인트 URL을 다른 상대방과 공유하는 경우 특정 키를 사용하고 만료 날짜가 있는 콜백 URL을 생성할 수 있습니다. 이렇게 하면 특정 시간 범위에 따라 키를 원활하게 롤링하거나 논리 앱을 트리거하는 액세스를 제한할 수 있습니다. URL의 만료 날짜를 지정하려면 [Logic Apps REST API](/rest/api/logic/workflowtriggers)를 사용합니다. 예:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

본문에 JSON 날짜 문자열을 사용하여 `NotAfter` 속성을 포함합니다. 이 속성은 `NotAfter` 날짜 및 시간까지만 유효한 콜백 URL을 반환합니다.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>기본 또는 보조 비밀 키로 URL 만들기

요청 기반 트리거의 콜백 URL을 생성하거나 나열하면, URL에 서명하는 데 사용할 키를 지정할 수 있습니다. 특정 키로 서명된 URL을 생성하려면 [Logic Apps REST API](/rest/api/logic/workflowtriggers)를 사용합니다. 예:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

본문에서는 속성 `KeyType`을 `Primary` 또는 `Secondary`로서 포함합니다. 이 속성은 지정된 보안 키로 서명된 URL을 반환합니다.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Azure AD OAuth(Azure Active Directory 공개 인증) 사용

논리 앱이 [요청 트리거로](../connectors/connectors-native-reqres.md)시작 하는 경우 요청 트리거에 대 한 인바운드 호출에 대 한 권한 부여 정책을 정의 하거나 추가 하 여 [Azure Active Directory 오픈 인증 (Azure AD OAuth)](../active-directory/develop/index.yml) 을 사용 하도록 설정할 수 있습니다.

이 인증을 사용하도록 설정하기 전에 다음 사항을 검토하십시오.

* 요청 트리거에 대 한 인바운드 호출은 인증 토큰을 사용 하 여 Azure AD OAuth, 인증 토큰을 사용 하는 Azure AD OAuth 또는 [SAS (공유 액세스 서명) URL](#sas) 을 사용 하 여 두 가지 스키마를 모두 사용할 수 없습니다.

  한 스키마를 사용 하면 다른 체계가 비활성화 되지 않지만 서비스에서 선택할 체계를 알 수 없기 때문에 두 가지를 동시에 사용 하면 오류가 발생 합니다. 또한 요청 트리거에 대해서만 지원 되는 OAuth 인증 토큰에 대해 [전달자 유형](../active-directory/develop/active-directory-v2-protocols.md#tokens) 인증 스키마만 지원 됩니다. 인증 토큰은 `Bearer-type` 권한 부여 헤더에를 지정 해야 합니다.

* 논리 앱은 최대 권한 부여 정책 수로 제한됩니다. 각 권한 부여 정책에도 최대 [클레임](../active-directory/develop/developer-glossary.md#claim) 수가 있습니다. 자세한 내용은 [Azure Logic Apps에 대한 제한 및 구성](../logic-apps/logic-apps-limits-and-config.md#authentication-limits)을 참조하세요.

* 권한 부여 정책에는 또는 (OAuth **Issuer** V2)로 시작 하는 값이 `https://sts.windows.net/` `https://login.microsoftonline.com/` Azure AD 발급자 ID 인 발급자 클레임이 적어도 하나 이상 포함 되어야 합니다. 액세스 토큰에 대 한 자세한 내용은 [Microsoft id 플랫폼 액세스 토큰](../active-directory/develop/access-tokens.md)을 참조 하세요.

논리 앱이 OAuth 인증 토큰을 포함 하는 인바운드 요청을 수신 하는 경우 Azure Logic Apps는 토큰의 클레임을 각 권한 부여 정책의 클레임과 비교 합니다. 토큰의 클레임과 하나 이상의 정책에 있는 모든 클레임 사이에 일치하는 항목이 있으면 인바운드 요청에 대한 권한 부여가 성공합니다. 토큰은 권한 부여 정책에 지정된 수보다 많은 클레임을 가질 수 있습니다.

예를 들어 논리 앱에 **발급자** 와 **대상**이라는 두 가지 클레임 유형이 필요한 권한 부여 정책이 있다고 가정 합니다. 다음 샘플 디코딩 [액세스 토큰](../active-directory/develop/access-tokens.md)에는 이러한 클레임 유형이 모두 포함됩니다.

```json
{
   "aud": "https://management.core.windows.net/",
   "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
   "iat": 1582056988,
   "nbf": 1582056988,
   "exp": 1582060888,
   "_claim_names": {
      "groups": "src1"
   },
   "_claim_sources": {
      "src1": {
         "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
    }
   },
   "acr": "1",
   "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
   "amr": [
      "rsa",
      "mfa"
   ],
   "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
   "appidacr": "2",
   "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
   "family_name": "Sophia Owen",
   "given_name": "Sophia Owen (Fabrikam)",
   "ipaddr": "167.220.2.46",
   "name": "sophiaowen",
   "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
   "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
   "puid": "1003000000098FE48CE",
   "scp": "user_impersonation",
   "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
   "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
   "unique_name": "SophiaOwen@fabrikam.com",
   "upn": "SophiaOwen@fabrikam.com",
   "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
   "ver": "1.0"
}
```

<a name="define-authorization-policy-portal"></a>

#### <a name="define-authorization-policy-in-azure-portal"></a>Azure Portal에서 권한 부여 정책 정의

Azure Portal에서 논리 앱에 대해 Azure AD OAuth를 사용 하도록 설정 하려면 다음 단계를 수행 하 여 논리 앱에 하나 이상의 권한 부여 정책을 추가 합니다.

1. [Azure Portal](https://portal.microsoft.com)의 Logic App Designer에서 논리 앱을 찾아서 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **권한 부여**를 선택합니다. 권한 부여 창이 열리면 **정책 추가**를 선택합니다.

   !["권한 부여" > "정책 추가" 선택](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. 요청 트리거에 대한 각 인바운드 호출을 통해 제공되는 인증 토큰에서 논리 앱에 필요한 [클레임 유형](../active-directory/develop/developer-glossary.md#claim) 및 값을 지정하여 권한 부여 정책에 대한 정보를 제공합니다.

   ![권한 부여 정책에 대한 정보 제공](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | 속성 | 필수 | Description |
   |----------|----------|-------------|
   | **정책 이름** | 예 | 권한 부여 정책에 사용하려는 이름입니다. |
   | **클레임** | 예 | 논리 앱이 인바운드 호출에서 받는 클레임 유형 및 값입니다. 사용 가능한 클레임 유형은 다음과 같습니다. <p><p>- **발급자** <br>- **대상 그룹** <br>- **제목** <br>- **JWT ID**(JSON Web Token ID) <p><p>최소한 **클레임** 목록에는 또는로 시작 하는 값이 있는 **발급자** 클레임 `https://sts.windows.net/` 또는 `https://login.microsoftonline.com/` Azure AD 발급자 ID가 포함 되어야 합니다. 이러한 클레임 유형에 대한 자세한 내용은 [Azure AD 보안 토큰의 클레임](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens)을 참조하세요. 자체 클레임 유형 및 값을 지정할 수도 있습니다. |
   |||

1. 또 다른 클레임을 추가하려면 다음 옵션 중에서 선택합니다.

   * 다른 클레임 유형을 추가하려면 **표준 클레임 추가**를 선택하고 클레임 유형을 선택한 후 클레임 값을 지정합니다.

   * 자체 클레임을 추가하려면 **사용자 지정 클레임 추가**를 선택하고 사용자 지정 클레임 값을 지정합니다.

1. 권한 부여 정책을 더 추가하려면 **정책 추가**를 선택합니다. 이전 단계를 반복하여 정책을 설정합니다.

1. 완료되면 **저장**을 선택합니다.

<a name="define-authorization-policy-template"></a>

#### <a name="define-authorization-policy-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 권한 부여 정책 정의

논리 앱을 배포 하기 위해 ARM 템플릿에서 Azure AD OAuth를 사용 하도록 설정 하려면 `properties` [논리 앱의 리소스 정의](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)에 대 한 섹션에서 `accessControl` 개체를 포함 하는 개체 (없는 경우)를 추가 `triggers` 합니다. 개체에서 `triggers` `openAuthenticationPolicies` 다음 구문을 따라 하나 이상의 권한 부여 정책을 정의 하는 개체를 추가 합니다.

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "values": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

섹션에 대 한 자세한 내용은 `accessControl` [Azure Resource Manager 템플릿에서 인바운드 IP 범위 제한](#restrict-inbound-ip-template) 및 [Microsoft 논리 워크플로 템플릿 참조](/azure/templates/microsoft.logic/2019-05-01/workflows)를 참조 하세요.

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Azure API Management을 사용 하 여 논리 앱 노출

논리 앱에 더 많은 [인증 프로토콜](../active-directory/develop/authentication-vs-authorization.md) 을 추가 하려면 [Azure API Management](../api-management/api-management-key-concepts.md) 서비스를 사용 하는 것이 좋습니다. 이 서비스는 논리 앱을 API로 노출하는 데 유용하며 모든 엔드포인트에 대한 풍부한 모니터링, 보안, 정책 및 설명서를 제공합니다. API Management는 논리 앱의 퍼블릭 또는 프라이빗 엔드포인트를 노출할 수 있습니다. 이 끝점에 대 한 액세스 권한을 부여 하려면 Azure AD OAuth, [클라이언트 인증서](#client-certificate-authentication)또는 다른 보안 표준을 사용 하 여 해당 끝점에 대 한 액세스 권한을 부여할 수 있습니다. API Management는 요청을 받으면 논리 앱에 요청을 보내고 필요한 변환 또는 제한을 수행합니다. 논리 앱만 API Management 호출 하도록 하려면 [논리 앱의 인바운드 IP 주소를 제한할](#restrict-inbound-ip)수 있습니다.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>인바운드 IP 주소 제한

SAS(공유 액세스 서명)와 마찬가지로 논리 앱을 호출할 수 있는 특정 클라이언트를 구체적으로 제한할 수 있습니다. 예를 들어 [Azure API Management](../api-management/api-management-key-concepts.md)를 사용 하 여 요청 끝점을 관리 하는 경우 사용자가 [만든 API Management 서비스 인스턴스에](../api-management/get-started-create-service-instance.md)대 한 IP 주소의 요청만 수락 하도록 논리 앱을 제한할 수 있습니다.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Azure Portal에서 인바운드 IP 범위 제한

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세스 제어 구성** > **허용된 인바운드 IP 주소**에서 **특정 IP 범위**를 선택합니다.

1. **트리거에 대한 IP 범위**에서 트리거가 허용하는 IP 주소 범위를 지정합니다.

   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다.

논리 앱이 중첩된 논리 앱으로만 트리거되도록 하려면 **허용된 인바운드 IP 주소** 목록에서 **다른 Logic Apps 전용**을 선택합니다. 이 옵션은 논리 앱 리소스에 빈 배열을 씁니다. 이렇게 하면 Logic Apps 서비스(부모 논리 앱)의 호출만 중첩된 논리 앱을 트리거할 수 있습니다.

> [!NOTE]
> IP 주소에 관계 없이 [Logic Apps REST API: 워크플로 트리거-실행](/rest/api/logic/workflowtriggers/run) 요청 또는 API Management를 사용 하 여 요청 기반 트리거를 포함 하는 논리 앱을 계속 실행할 수 있습니다. 하지만 이 시나리오에는 Azure REST API에 대한 [인증](../active-directory/develop/authentication-vs-authorization.md)이 여전히 필요합니다. 모든 이벤트는 Azure 감사 로그에 나타납니다. 그에 따라 액세스 제어 정책을 설정했는지 확인하십시오.

<a name="restrict-inbound-ip-template"></a>

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 인바운드 IP 범위 제한

[Resource Manager 템플릿을 사용하여 논리 앱 배포를 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)하는 경우 `accessControl` 섹션을 사용하고 논리 앱의 리소스 정의에 `triggers` 및 `actions` 섹션을 포함하여 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식으로 IP 범위를 지정할 수 있습니다. 예를 들면 다음과 같습니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>논리 앱 작업에 대한 액세스

특정 사용자나 그룹만 논리 앱 관리, 편집 및 보기와 같은 특정 작업을 실행하도록 허용할 수 있습니다. 사용자의 권한을 제어 하려면 azure 구독에서 구성원에 사용자 지정 또는 기본 제공 역할을 할당할 수 있도록 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md) 를 사용 합니다.

* [논리 앱 참가자](../role-based-access-control/built-in-roles.md#logic-app-contributor): 논리 앱을 관리할 수 있지만 앱에 대한 액세스는 변경할 수 없습니다.

* [논리 앱 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator): 논리 앱을 읽고 사용하거나 사용하지 않도록 설정할 수는 있지만 편집하거나 업데이트할 수 없습니다.

다른 사람이 논리 앱을 변경하거나 삭제하지 못하게 하려면 [Azure 리소스 잠금](../azure-resource-manager/management/lock-resources.md)을 사용할 수 있습니다. 이 기능은 다른 사람이 프로덕션 리소스를 변경하거나 삭제하지 못하도록 합니다.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>실행 기록 데이터에 대한 액세스

논리 앱을 실행하는 동안 모든 데이터는 TLS(전송 계층 보안)를 사용하여 [미사용 상태에서](../security/fundamentals/encryption-atrest.md) [전송 중에 암호화](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)됩니다. 논리 앱 실행이 끝나면 해당 실행 기록을 볼 수 있습니다. 여기에는 실행된 단계와 함께 각 작업의 상태, 지속 시간, 입력 및 출력이 포함됩니다. 이렇게 풍부한 정보는 논리 앱이 어떻게 실행되었는지, 문제가 발생하면 어디부터 문제 해결을 시작해야 하는지에 대한 인사이트를 제공합니다.

논리 앱의 실행 기록을 볼 때는 Logic Apps가 액세스를 인증한 다음, 각 실행의 요청과 응답에 대한 입력 및 출력 링크를 제공합니다. 단, 암호, 비밀, 키 또는 기타 중요한 정보를 처리하는 작업의 경우 다른 사용자가 해당 데이터를 보거나 액세스하지 못하도록 하는 것이 좋습니다. 예를 들어, [Azure Key Vault](../key-vault/general/overview.md)에서 논리 앱이 HTTP 작업을 인증할 때 사용할 비밀을 가져오는 경우, 이 비밀을 시야에서 숨기는 것이 좋습니다.

논리 앱의 실행 기록에서 입력과 출력에 대한 액세스를 제어하기 위해 다음 옵션을 사용할 수 있습니다.

* [IP 주소 범위를 통해 액세스를 제한합니다](#restrict-ip).

  이 옵션은 특정 IP 주소 범위의 요청을 기반으로 실행 기록에 대한 액세스를 보호하는 데 유용합니다.

* [난독 처리를 사용하여 실행 기록의 데이터를 보호합니다](#obfuscate).

  많은 트리거와 작업에서 논리 앱의 실행 기록에 포함된 입력, 출력 또는 둘 다를 보호할 수 있습니다.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>IP 주소 범위를 통한 액세스 제한

논리 앱의 실행 기록에 포함된 입력 및 출력에 대한 액세스 권한을 제한하여 특정 IP 주소 범위의 요청만 해당 데이터를 볼 수 있도록 할 수 있습니다. 예를 들어, 입력 및 출력에 아무도 액세스하지 못하게 하려면 `0.0.0.0-0.0.0.0`와 같은 IP 주소 범위를 지정합니다. 이러한 제한은 관리자 권한이 있는 사용자만 제거할 수 있기 때문에 논리 앱 데이터에 대한 “Just-In-Time” 액세스 가능성을 제공합니다. 제한할 IP 범위는 Azure Portal을 사용하거나 논리 앱 배포에 사용하는 Azure Resource Manager 템플릿에서 지정할 수 있습니다.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Azure Portal에서 IP 범위 제한

1. Azure Portal의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세스 제어 구성** > **허용된 인바운드 IP 주소**에서 **특정 IP 범위**를 선택합니다.

1. **콘텐츠의 IP 범위**에서 입력 및 출력의 콘텐츠에 액세스할 수 있는 IP 주소 범위를 지정합니다.

   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다.

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 IP 범위 제한

[Resource Manager 템플릿을 사용하여 논리 앱 배포를 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)하는 경우 논리 앱의 리소스 정의에서 `accessControl` 섹션에 `contents` 섹션을 사용하여 IP 범위를 지정할 수 있습니다.

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>난독 처리를 사용하여 실행 기록의 데이터 보호

많은 트리거와 작업에는 논리 앱의 실행 기록에서 입력, 출력 또는 둘 다를 보호하는 설정이 있습니다. 이러한 설정을 사용하여 데이터를 보호하기 전에 [고려 사항을 검토하세요](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>디자이너의 보안 입력 및 출력

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

   ![Logic Apps 디자이너에 열린 논리 앱](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. 중요한 데이터를 보호하려는 트리거 또는 작업에서 줄임표( **...** ) 단추를 선택한 다음, **설정**을 선택합니다.

   ![트리거 또는 작업 설정 열기](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. **보안 입력**, **보안 출력** 중 하나 또는 둘 다를 켭니다. 완료되면 **완료**를 선택합니다.

   !["보안 입력" 또는 "보안 출력" 켜기](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   이제 작업 또는 트리거의 제목 표시줄에 자물쇠 아이콘이 보입니다.

   ![작업 또는 트리거 제목 표시줄에 자물쇠 아이콘이 보임](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   이전 작업의 보안 출력을 나타내는 토큰에도 자물쇠 아이콘이 표시됩니다. 예를 들어, 작업에 사용할 동적 컨텐츠 목록에서 이러한 출력을 선택하면 해당 토큰에 자물쇠 아이콘이 표시됩니다.

   ![보안 출력을 위한 토큰 선택](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. 논리 앱이 실행된 후 해당 실행 기록을 볼 수 있습니다.

   1. 논리 앱의 **개요** 창에서 보려는 실행을 선택합니다.

   1. **논리 앱 실행** 창에서 검토하려는 작업을 펼칩니다.

      입력과 출력을 모두 가리도록 선택하면 해당 값이 숨김 상태로 표시됩니다.

      ![실행 기록에 숨겨진 입력 및 출력](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>코드 보기의 보안 입력 및 출력

기본 트리거 또는 작업 정의에서 다음 값 중 하나 또는 둘 다를 사용하여 `runtimeConfiguration.secureData.properties` 배열을 추가하거나 업데이트합니다.

* `"inputs"`: 실행 기록의 입력을 보호합니다.
* `"outputs"`: 실행 기록의 출력을 보호합니다.

이 설정을 사용하여 데이터를 보호하는 경우 [고려할 사항](#obfuscation-considerations)이 있습니다.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-securing-inputs-and-outputs"></a>입력 및 출력 보안 시 고려 사항

* 트리거 또는 작업에서 입력 또는 출력을 가리면 Logic Apps는 보안 데이터를 Azure Log Analytics로 보내지 않습니다. 또한 모니터링을 위해 해당 트리거 또는 작업에 [추적된 속성](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)을 추가할 수 없습니다.

* [워크플로 기록을 처리하는 Logic Apps API](/rest/api/logic/)는 보안 출력을 반환하지 않습니다.

* 입력을 가리는 동작에서 출력을 보호하거나 출력을 명시적으로 가리려면 해당 동작에서 **보안 출력**을 수동으로 켭니다.

* 실행 기록이 해당 데이터를 가릴 필요가 있는 다운스트림 작업에서 **보안 입력** 또는 **보안 출력**을 켜야 합니다.

  **보안 출력 설정**

  트리거 또는 작업에서 **보안 출력**을 수동으로 켜면 Logic Apps는 해당 출력을 실행 기록에서 숨깁니다. 다운스트림 작업의 입력으로 이렇게 보안 출력이 명시적으로 사용되면 Logic Apps는 실행 기록에서 해당 작업의 입력을 숨기지만 해당 작업의 **보안 입력** 설정은 활성화되지 않습니다.

  ![다운스트림 및 입력으로 사용되는 보안 출력이 대부분의 작업에 미치는 영향](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  작성, JSON 구문 분석, 응답 작업에는 **보안 입력** 설정만 있으며, 켜지면, 설정으로 인해 이러한 작업의 출력도 숨겨집니다. 이러한 작업의 입력으로 업스트림 보안 출력을 명시적으로 사용하면 Logic Apps는 이러한 작업의 입력 및 출력을 숨기지만 해당 작업의 **보안 입력** 설정은 활성화되지 않습니다. 다운스트림 작업의 입력으로 작성, JSON 구문 분석, 응답 작업의 숨겨진 출력이 명시적으로 사용되면 Logic App은 이러한 다운스트림 작업의 입력 또는 출력을 숨기지 않습니다.

  ![다운스트림에 입력으로 사용되는 보안 출력이 특정 작업에 미치는 영향](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **보안 입력 설정**

  트리거 또는 작업에서 **보안 입력**을 수동으로 켜면 Logic Apps는 해당 입력을 실행 기록에서 숨깁니다. 다운스트림 작업의 입력으로 트리거 또는 작업에 보이는 출력이 명시적으로 사용되면 Logic Apps는 실행 기록에서 다운스트림 작업의 입력을 숨기지만 작업의 **보안 입력**은 활성화되지 않으며 작업의 출력을 숨기지 않습니다.

  ![보안 입력 및 다운스트림이 대부분의 작업에 미치는 영향](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  작성, JSON 구문 분석, 응답 작업에 보안 입력이 포함된 작업이나 트리거에 보이는 출력이 명시적으로 사용되면 Logic Apps는 해당 작업의 입력 및 출력을 숨기지만 해당 작업의 **보안 입력** 설정은 활성화되지 않습니다. 다운스트림 작업의 입력으로 작성, JSON 구문 분석, 응답 작업의 숨겨진 출력이 명시적으로 사용되면 Logic App은 이러한 다운스트림 작업의 입력 또는 출력을 숨기지 않습니다.

  ![보안입력 및 다운스트림이 특정 작업에 미치는 영향](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>매개 변수 입력에 대한 액세스

여러 환경에 배포하는 경우에는 워크플로 정의에서 환경에 따라 달라지는 값을 매개 변수화하는 것이 좋습니다. 이렇게 하면 [Azure Resource Manager 템플릿](../azure-resource-manager/templates/overview.md)을 사용하여 논리 앱을 배포하기 때문에 하드 코딩된 데이터를 피할 수 있고, 보안 매개 변수를 정의하여 이 데이터를 별도의 입력으로 전달하기 때문에([매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)을 사용하여 [템플릿의 매개 변수](../azure-resource-manager/templates/template-parameters.md)를 통해) 중요한 데이터를 보호할 수 있습니다.

예를 들어 Azure AD OAuth([Azure Active Directory 공개 인증](#azure-active-directory-oauth-authentication))를 사용하여 HTTP 작업을 인증하는 경우 인증에 사용되는 클라이언트 ID와 클라이언트 암호를 수신하는 매개 변수를 정의하고 가릴 수 있습니다. 논리 앱에서 이러한 매개 변수를 정의하려면 논리 앱의 워크플로 정의에서 `parameters` 섹션을 사용하고 배포용 Resource Manager 템플릿을 사용합니다. 논리 앱을 편집하거나 실행 기록을 볼 때 나타내지 않을 매개 변수 값에 보안을 설정하려면 `securestring` 또는 `secureobject` 유형을 사용하여 매개 변수를 정의하고 필요에 따라 인코딩을 사용합니다. 이 형식의 매개 변수는 리소스 정의와 함께 반환되지 않으며, 배포 후 리소스를 볼 때 액세스할 수 없습니다. 런타임 중에 해당 매개 변수 값에 액세스하려면 워크플로 정의 내에 `@parameters('<parameter-name>')` 식을 사용합니다. 이 식은 런타임 시에만 계산되며 [워크플로 정의 언어](../logic-apps/logic-apps-workflow-definition-language.md)에 설명되어 있습니다.

> [!NOTE]
> 요청 헤더 또는 본문에서 매개 변수를 사용하면 논리 앱의 실행 기록과 나가는 HTTP 요청을 볼 때 해당 매개 변수가 표시될 수 있습니다. 콘텐츠 액세스 정책도 적절히 설정해야 합니다.
> [난독 처리](#obfuscate)를 사용하여 실행 기록에서 입력 및 출력을 숨길 수도 있습니다. 권한 부여 헤더는 입력 또는 출력을 통해 볼 수 없습니다. 따라서 권한 부여 헤더에 비밀이 사용되면 해당 비밀을 검색할 수 없습니다.

자세한 내용은 이 항목에서 다음 섹션을 참조하세요.

* [워크플로 정의의 보안 매개 변수](#secure-parameters-workflow)
* [난독 처리를 사용하여 실행 기록의 데이터 보호](#obfuscate)

[Resource Manager 템플릿을 사용하여 논리 앱 배포를 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)하는 경우 배포 시 `securestring` 및 `secureobject` 유형을 사용하여 평가되는 보안 [템플릿 매개 변수](../azure-resource-manager/templates/template-parameters.md)를 정의할 수 있습니다. 템플릿 매개 변수를 정의하려면 템플릿의 최상위 `parameters` 섹션을 사용합니다. 이 섹션은 워크플로 정의의 `parameters` 섹션과 다르며 별개입니다. 템플릿 매개 변수에 대한 값을 제공하려면 별도의 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)을 사용합니다.

예를 들어 비밀을 사용하는 경우에는 배포 시 [Azure Key Vault](../key-vault/general/overview.md)에서 해당 비밀을 검색하는 보안 템플릿 매개 변수를 정의하고 사용할 수 있습니다. 그런 다음, 매개 변수 파일에서 키 자격 증명 모음 및 비밀을 참조하면 됩니다. 자세한 내용은 다음 항목을 참조하세요.

* [배포 시 Azure Key Vault를 사용하여 중요한 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)
* 이 항목의 뒷부분에 나오는 [Azure Resource Manager 템플릿의 보안 매개 변수](#secure-parameters-deployment-template)

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>워크플로 정의의 보안 매개 변수

논리 앱 워크플로 정의의 중요한 정보를 보호하려면 논리 앱을 저장한 후 정보가 보이지 않도록 보호되는 매개 변수를 사용합니다. 예를 들어, HTTP에 사용자 이름과 암호를 사용하는 기본 인증이 필요하다고 가정합니다. 워크플로 정의에서 `parameters` 섹션은 `securestring` 유형을 사용하여 `basicAuthPasswordParam` 및 `basicAuthUsernameParam` 매개 변수를 정의합니다. 그런 다음, 작업 정의가 `authentication` 섹션에서 이러한 매개 변수를 참조합니다.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 보안 매개 변수

논리 앱용 [Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)에는 `parameters` 섹션이 여러 개 있습니다. 암호, 키, 비밀 및 기타 중요한 정보를 보호하려면 `securestring` 또는 `secureobject` 유형을 사용하여 템플릿 수준과 워크플로 정의 수준에서 보안 매개 변수를 정의합니다. 그런 다음, [Azure Key Vault](../key-vault/general/overview.md)에 해당 값을 저장하고 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)을 사용하여 키 자격 증명 모음과 비밀을 참조할 수 있습니다. 그러면 템플릿이 배포 시 해당 정보를 검색합니다. 자세한 내용은 [배포 시 Azure Key Vault를 사용하여 중요한 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)을 참조하세요.

이러한 `parameters` 섹션에 대한 자세한 내용은 다음과 같습니다.

* 템플릿의 최상위 수준에서 `parameters` 섹션은 템플릿이 배포 시 사용하는 값에 대한 매개 변수를 정의합니다. 예를 들어 이러한 값에는 특정 배포 환경에 대한 연결 문자열이 포함될 수 있습니다. 그런 다음, 값을 별도의 [매개 변수 파일](../azure-resource-manager/templates/parameter-files.md)에 저장하면 해당 값을 더 쉽게 변경할 수 있습니다.

* 논리 앱의 리소스 정의 내에 있지만 워크플로 정의 외부에 있는 `parameters` 섹션은 워크플로 정의의 매개 변수에 대한 값을 지정합니다. 이 섹션에서는 템플릿 매개 변수를 참조하는 템플릿 식을 사용하여 해당 값을 할당할 수 있습니다. 이러한 식은 배포 시 평가됩니다.

* 워크플로 정의 내에서 `parameters` 섹션은 논리 앱이 런타임 시 사용하는 매개 변수를 정의합니다. 그런 다음, 런타임 시 평가되는 워크플로 정의 식을 사용하여 논리 앱의 워크플로 내에서 해당 매개 변수를 참조할 수 있습니다.

다음 예제 템플릿에는 `securestring` 형식을 사용하는 여러 보안 매개 변수 정의가 포함됩니다.

| 매개 변수 이름 | Description |
|----------------|-------------|
| `TemplatePasswordParam` | 비밀을 수신한 후 워크플로 정의의 `basicAuthPasswordParam` 매개 변수로 전달되는 템플릿 매개 변수 |
| `TemplateUsernameParam` | 사용자 이름을 수신한 후 워크플로 정의의 `basicAuthUserNameParam` 매개 변수로 전달되는 템플릿 매개 변수 |
| `basicAuthPasswordParam` | HTTP 작업에서 기본 인증을 위한 암호를 수신하는 워크플로 정의 매개 변수 |
| `basicAuthUserNameParam` | HTTP 작업에서 기본 인증을 위한 사용자 이름을 수신하는 워크플로 정의 매개 변수 |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>다른 서비스 및 시스템에 대 한 아웃 바운드 호출에 대 한 액세스

대상 끝점의 기능을 기반으로 [http 트리거 또는 http 동작](../connectors/connectors-native-http.md)에서 전송 하는 아웃 바운드 호출은 암호화를 지원 하 고 [TLS (Transport Layer Security) 1.0, 1.1 또는 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security)(이전에는 SSL(Secure Sockets Layer) (SSL)로 알려짐)를 사용 하 여 보호 됩니다. Logic Apps은 지원 되는 가장 높은 버전을 사용 하 여 대상 끝점과 협상 합니다. 예를 들어 대상 끝점이 1.2을 지 원하는 경우 HTTP 트리거 또는 작업은 1.2를 먼저 사용 합니다. 그렇지 않으면 커넥터에서 지원 되는 가장 높은 다음 버전을 사용 합니다.

TLS/SSL 자체 서명 인증서에 대 한 정보는 다음과 같습니다.

* 전역 다중 테 넌 트 Azure 환경에서 논리 앱의 경우 HTTP 커넥터는 자체 서명 된 TLS/SSL 인증서를 허용 하지 않습니다. 논리 앱이 서버에 대 한 HTTP 호출을 수행 하 고 TLS/SSL 자체 서명 된 인증서를 제공 하는 경우 HTTP 호출은 오류가 발생 하 고 실패 `TrustFailure` 합니다.

* [ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)의 논리 앱에 대해 HTTP 커넥터는 TLS/SSL 핸드셰이크에 자체 서명 된 인증서를 허용 합니다. 그러나 Logic Apps REST API를 사용 하 여 기존 ISE 또는 새 ISE에 대해 [자체 서명 된 인증서 지원을 사용 하도록 설정](../logic-apps/create-integration-service-environment-rest-api.md#request-body) 하 고 위치에 공용 인증서를 설치 해야 합니다 `TrustedRoot` .

다음은 논리 앱에서 전송 된 호출을 처리 하는 끝점을 보호 하는 데 도움이 되는 방법입니다.

* [아웃 바운드 요청에 인증을 추가](#add-authentication-outbound)합니다.

  HTTP 트리거 또는 작업을 사용 하 여 아웃 바운드 호출을 보내는 경우 논리 앱에서 보낸 요청에 인증을 추가할 수 있습니다. 예를 들어 다음 인증 유형을 선택할 수 있습니다.

  * [기본 인증](#basic-authentication)

  * [클라이언트 인증서 인증](#client-certificate-authentication)

  * [Active Directory OAuth 인증](#azure-active-directory-oauth-authentication)

  * [관리 ID 인증](#managed-identity-authentication)

* 논리 앱 IP 주소의 액세스를 제한합니다.

  논리 앱이 엔드포인트로 보내는 모든 호출은 논리 앱의 지역을 기반으로 지정된 특정 IP 주소에서 시작됩니다. 이러한 IP 주소의 요청만 수락하는 필터링을 추가할 수 있습니다. 이러한 IP 주소를 알아보려면 [Azure Logic Apps에 대한 제한 및 구성](logic-apps-limits-and-config.md#configuration)을 참조하세요.

* 온-프레미스 시스템에 대한 연결 보안을 강화합니다.

  Azure Logic Apps는 이러한 서비스와의 통합을 제공하기 때문에 보안과 안정성이 강화된 온-프레미스 통신을 제공할 수 있습니다.

  * 온-프레미스 데이터 게이트웨이

    Azure Logic Apps의 많은 관리형 커넥터를 사용하면 온-프레미스 시스템(예: 파일 시스템, SQL, SharePoint, DB2)에 대한 보안 연결이 편리합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널에서 온-프레미스 원본의 데이터를 보냅니다. 모든 트래픽은 게이트웨이 에이전트의 보안 아웃바운드 트래픽으로 발생합니다. [온-프레미스 데이터 게이트웨이 작동 방식](logic-apps-gateway-install.md#gateway-cloud-service)을 알아보세요.

  * Azure API Management를 통해 연결

    [Azure API Management](../api-management/api-management-key-concepts.md) 는 사이트 간 가상 사설망 및 온-프레미스 시스템과의 통신을 위한 [express](../expressroute/expressroute-introduction.md) 경로 통합과 같은 온-프레미스 연결 옵션을 제공 합니다. 온-프레미스 시스템에 대 한 액세스를 제공 하는 API가 있고 [API Management 서비스 인스턴스](../api-management/get-started-create-service-instance.md)를 만들어 해당 api를 노출 한 경우 논리 앱 디자이너에서 기본 제공 API Management 트리거 또는 동작을 선택 하 여 논리 앱의 워크플로에서 해당 api를 호출할 수 있습니다.

    > [!NOTE]
    > 커넥터는 보고 연결할 권한이 있지만 소비 기반 API Management 서비스는 표시 하지 않는 API Management 서비스만 표시 합니다.

    1. 논리 앱 디자이너에서 `api management` 검색 상자에를 입력 합니다. 트리거 또는 작업을 추가 하 고 있는지 여부에 따라 단계를 선택 합니다.<p>

       * 워크플로의 첫 번째 단계인 트리거를 추가 하는 경우 **Azure API Management 트리거 선택**을 선택 합니다.

       * 작업을 추가 하는 경우 **Azure API Management 작업 선택**을 선택 합니다.

       다음 예에서는 트리거를 추가 합니다.

       ![Azure API Management 트리거 추가](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. 이전에 만든 API Management 서비스 인스턴스를 선택 합니다.

       ![API Management 서비스 인스턴스 선택](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. 사용할 API 호출을 선택 합니다.

       ![기존 API 선택](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>아웃바운드 호출에 대한 인증 추가

HTTP 및 HTTPS 엔드포인트는 다양한 종류의 인증을 지원합니다. 이러한 끝점에 대 한 아웃 바운드 호출 또는 요청을 보내는 데 사용 하는 일부 트리거 및 작업에서 인증 유형을 지정할 수 있습니다. 논리 앱 디자이너에서 인증 유형 선택을 지 원하는 트리거 및 작업에는 **authentication** 속성이 있습니다. 그러나이 속성은 기본적으로 항상 표시 되지 않을 수도 있습니다. 이러한 경우 트리거 또는 작업에서 **새 매개 변수 추가** 목록을 열고 **인증**을 선택 합니다.

> [!IMPORTANT]
> 논리 앱이 처리 하는 중요 한 정보를 보호 하려면 보안 매개 변수를 사용 하 고 필요에 따라 데이터를 인코딩합니다.
> 매개 변수 사용 및 보안에 대한 자세한 내용은 [매개 변수 입력에 대한 액세스](#secure-action-parameters)를 참조하세요.

다음 표에서는 사용자가 인증 유형을 선택할 수 있는 트리거와 작업에서 사용할 수 있는 인증 유형을 식별 합니다.

| 인증 유형 | 지원 되는 트리거 및 작업 |
|---------------------|--------------------------------|
| [기본](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [클라이언트 인증서](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Active Directory OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [원시](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [관리 ID](#managed-identity-authentication) | Azure API Management, Azure 앱 Services, Azure Functions, HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>기본 인증

[기본](../active-directory-b2c/secure-rest-api.md) 옵션을 사용할 수 있는 경우 다음 속성 값을 지정합니다.

| 속성(디자이너) | 속성(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | Basic | 사용할 인증 유형 |
| **사용자 이름** | `username` | 예 | <*사용자-이름*>| 대상 서비스 엔드포인트에 대한 액세스를 인증하는 사용자 이름입니다. |
| **암호** | `password` | 예 | <*암호*> | 대상 서비스 엔드포인트에 대한 액세스를 인증하는 암호입니다. |
||||||

[보안 매개 변수](#secure-action-parameters)를 사용하여 중요한 정보를 처리하고 보안을 유지하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)에서), 식을 사용하여 런타임 시 해당 매개 변수 값에 액세스할 수 있습니다. 다음 예제 HTTP 작업 정의는 인증 `type`을 `Basic`으로 지정하고 [parameters() 함수](../logic-apps/workflow-definition-language-functions-reference.md#parameters)를 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

### <a name="client-certificate-authentication"></a>클라이언트 인증서 인증

[클라이언트 인증서](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) 옵션을 사용할 수 있는 경우 다음 속성 값을 지정합니다.

| 속성(디자이너) | 속성(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | **클라이언트 인증서** <br>또는 <br>`ClientCertificate` | 사용할 인증 유형입니다. [Azure API Management](../api-management/api-management-howto-mutual-certificates.md)를 사용 하 여 인증서를 관리할 수 있습니다. <p></p>**참고**: 사용자 지정 커넥터는 인바운드 및 아웃 바운드 호출에 대해 인증서 기반 인증을 지원 하지 않습니다. |
| **Pfx** | `pfx` | 예 | <*encoded-pfx-file-content*> | PFX(개인 정보 교환) 파일의 base64로 인코딩된 콘텐츠 <p><p>PFX 파일을 base64 인코딩 형식으로 변환하려면 다음 단계에 따라 PowerShell을 사용합니다. <p>1. 인증서 콘텐츠를 변수에 저장합니다. <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. `ToBase64String()` 함수를 사용하여 인증서 콘텐츠를 변환하고 해당 콘텐츠를 텍스트 파일에 저장합니다. <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **암호** | `password`| 예 | <*password-for-pfx-file*> | PFX 파일에 액세스하기 위한 암호 |
|||||

[보안 매개 변수](#secure-action-parameters)를 사용하여 중요한 정보를 처리하고 보안을 유지하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)에서), 식을 사용하여 런타임 시 해당 매개 변수 값에 액세스할 수 있습니다. 다음 예제 HTTP 작업 정의는 인증 `type`을 `ClientCertificate`으로 지정하고 [parameters() 함수](../logic-apps/workflow-definition-language-functions-reference.md#parameters)를 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

클라이언트 인증서 인증을 사용하여 서비스를 보호하는 방법에 대한 자세한 내용은 다음 항목을 참조하세요.

* [Azure API Management에서 클라이언트 인증서 인증을 사용하여 API에 대한 보안 강화](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Azure API Management에서 클라이언트 인증서 인증을 사용하여 백엔드 서비스에 대한 보안 강화](../api-management/api-management-howto-mutual-certificates.md)
* [클라이언트 인증서를 사용하여 RESTfuL 서비스에 대한 보안 강화](../active-directory-b2c/secure-rest-api.md)
* [애플리케이션 인증을 위한 인증서 자격 증명](../active-directory/develop/active-directory-certificate-credentials.md)
* [Azure App Service의 코드에서 TLS/SSL 인증서 사용](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Azure Active Directory 공개 인증

요청 트리거에서 논리 앱에 대 한 [AZURE ad 권한 부여 정책을 설정한](#enable-oauth) 후에 들어오는 호출을 인증 하는 데 [Azure Active Directory Open AUTHENTICATION (azure ad OAuth)](../active-directory/develop/index.yml)을 사용할 수 있습니다. 선택할 수 있는 **Active Directory OAuth** 인증 유형을 제공하는 다른 모든 트리거 및 작업의 경우 다음 속성 값을 지정합니다.

| 속성(디자이너) | 속성(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | **Active Directory OAuth** <br>또는 <br>`ActiveDirectoryOAuth` | 사용할 인증 유형입니다. Logic Apps는 현재 [OAuth 2.0 프로토콜](../active-directory/develop/v2-overview.md)을 따릅니다. |
| **권한** | `authority` | 예 | <*URL-for-authority-token-issuer*> | 인증 토큰을 제공하는 기관의 URL입니다. 이 값은 기본적으로 `https://login.windows.net`입니다. |
| **테넌트** | `tenant` | 예 | <*tenant-ID*> | Azure AD 테넌트의 테넌트 ID |
| **대상** | `audience` | 예 | <*resource-to-authorize*> | 권한 부여에 사용할 리소스(예: `https://management.core.windows.net/`) |
| **클라이언트 ID** | `clientId` | 예 | <*client-ID*> | 권한 부여를 요청하는 앱에 대한 클라이언트 ID |
| **자격 증명 유형** | `credentialType` | 예 | 인증서 <br>또는 <br>비밀 | 클라이언트가 권한 부여를 요청하는 데 사용하는 자격 증명 유형입니다. 이 속성과 값은 논리 앱의 기본 정의에는 나타나지 않지만 선택한 자격 증명 유형에 대해 나타나는 속성을 결정합니다. |
| **비밀** | `secret` | 예, "Secret" 자격 증명 유형에만 해당 | <*client-secret*> | 권한 부여를 요청하는 클라이언트 비밀 |
| **Pfx** | `pfx` | 예, "Certificate" 자격 증명 유형에만 해당 | <*encoded-pfx-file-content*> | PFX(개인 정보 교환) 파일의 base64로 인코딩된 콘텐츠 |
| **암호** | `password` | 예, "Certificate" 자격 증명 유형에만 해당 | <*password-for-pfx-file*> | PFX 파일에 액세스하기 위한 암호 |
|||||

[보안 매개 변수](#secure-action-parameters)를 사용하여 중요한 정보를 처리하고 보안을 유지하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)에서), 식을 사용하여 런타임 시 해당 매개 변수 값에 액세스할 수 있습니다. 다음 예제 HTTP 작업 정의는 인증 `type`을 `ActiveDirectoryOAuth`로 지정하며 자격 증명 유형을 `Secret`으로 지정하고 [parameters() 함수](../logic-apps/workflow-definition-language-functions-reference.md#parameters)를 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

### <a name="raw-authentication"></a>원시 인증

**원시** 옵션을 사용할 수 있으면 [OAuth 2.0 프로토콜](https://oauth.net/2/)을 따르지 않는 [인증 체계](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml)를 사용해야 하는 경우 이 인증 유형을 사용할 수 있습니다. 이 유형을 사용하면 나가는 요청과 함께 보내는 인증 헤더 값을 수동으로 만들고 트리거 또는 작업에서 이 헤더 값을 지정합니다.

예를 들어 [OAuth 1.0 프로토콜](https://tools.ietf.org/html/rfc5849)을 따르는 HTTPS 요청에 대한 샘플 헤더는 다음과 같습니다.

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

원시 인증을 지원하는 트리거 또는 작업에서 다음 속성 값을 지정합니다.

| 속성(디자이너) | 속성(JSON) | 필수 | 값 | Description |
|---------------------|-----------------|----------|-------|-------------|
| **인증** | `type` | 예 | Raw | 사용할 인증 유형 |
| **값** | `value` | 예 | <*authorization-header-value*> | 인증에 사용할 인증 헤더 값 |
||||||

[보안 매개 변수](#secure-action-parameters)를 사용하여 중요한 정보를 처리하고 보안을 유지하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)에서), 식을 사용하여 런타임 시 해당 매개 변수 값에 액세스할 수 있습니다. 다음 예제 HTTP 작업 정의는 인증 `type`을 `Raw`로 지정하고 [parameters() 함수](../logic-apps/workflow-definition-language-functions-reference.md#parameters)를 사용하여 매개 변수 값을 가져옵니다.

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

### <a name="managed-identity-authentication"></a>관리 ID 인증

[관리 id](../active-directory/managed-identities-azure-resources/overview.md) 옵션을 [특정 트리거 또는 동작](#add-authentication-outbound)에서 사용할 수 있는 경우 논리 앱은 로그인 하지 않고 Azure Active Directory (Azure AD)로 보호 되는 다른 리소스에 대 한 액세스를 인증 하기 위해 시스템이 할당 한 id 또는 수동으로 만들어진 *단일* 사용자 할당 id를 사용할 수 있습니다. 이 ID는 Azure에서 관리되며, 비밀을 제공하거나 순환할 필요가 없기 때문에 자격 증명을 보호하는 데 유용합니다. [Azure AD 인증에 관리 ID를 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)에 대해 자세히 알아보세요.

1. 논리 앱이 관리 ID를 사용할 수 있으려면 그 전에 [Azure Logic Apps에서 관리 ID를 사용하여 Azure 리소스에 대한 액세스 인증](../logic-apps/create-managed-service-identity.md)의 단계를 따르세요. 이 단계는 논리 앱에서 관리 ID를 사용하도록 설정하고 대상 Azure 리소스에 대한 해당 ID의 액세스 권한을 설정합니다.

1. Azure 함수가 관리 ID를 사용할 수 있으려면 먼저 [Azure functions에 대한 인증이 가능하도록 설정](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)합니다.

1. 관리 ID를 사용하려는 트리거 또는 작업에서 다음 속성 값을 지정합니다.

   | 속성(디자이너) | 속성(JSON) | 필수 | 값 | Description |
   |---------------------|-----------------|----------|-------|-------------|
   | **인증** | `type` | 예 | **관리 ID** <br>또는 <br>`ManagedServiceIdentity` | 사용할 인증 유형 |
   | **관리 ID** | `identity` | 예 | * **시스템 할당 관리 ID** <br>또는 <br>`SystemAssigned` <p><p>* <*user-assigned-identity-name*> | 사용할 관리 ID |
   | **대상** | `audience` | 예 | <*target-resource-ID*> | 액세스하려는 대상 리소스의 리소스 ID입니다. <p>예를 들어 `https://storage.azure.com/`은 인증을 위한 [액세스 토큰](../active-directory/develop/access-tokens.md)을 모든 스토리지 계정에 유효하게 만듭니다. 하지만 특정 스토리지 계정에 대해 `https://fabrikamstorageaccount.blob.core.windows.net`과 같은 루트 서비스 URL을 지정할 수도 있습니다. <p>**참고**: **대상 그룹** 속성은 일부 트리거나 작업에서 숨겨질 수 있습니다. 이 속성을 표시하려면 트리거 또는 작업에서 **새 매개 변수 추가** 목록을 열고 **대상 그룹**을 선택합니다. <p><p>**중요**: 이 대상 리소스 ID는 필수 후행 슬래시를 포함하여 Azure AD에 필요한 값과 정확히 일치해야 합니다. 따라서 모든 Azure Blob Storage 계정에 대한 `https://storage.azure.com/` 리소스 ID에는 후행 슬래시가 필요합니다. 하지만 특정 스토리지 계정에 대한 리소스 ID에는 슬래시가 필요하지 않습니다. 이러한 리소스 ID를 알아보려면 [Azure AD를 지원하는 Azure 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)를 참조하세요. |
   |||||

   [보안 매개 변수](#secure-action-parameters)를 사용하여 중요한 정보를 처리하고 보안을 유지하는 경우(예: [배포 자동화를 위한 Azure Resource Manager 템플릿](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)에서), 식을 사용하여 런타임 시 해당 매개 변수 값에 액세스할 수 있습니다. 다음 예제 HTTP 작업 정의는 인증 `type`을 `ManagedServiceIdentity`로 지정하고 [parameters() 함수](../logic-apps/workflow-definition-language-functions-reference.md#parameters)를 사용하여 매개 변수 값을 가져옵니다.

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>연결 만들기 차단

조직에서 Azure Logic Apps의 커넥터를 사용 하 여 특정 리소스에 대 한 연결을 허용 하지 않는 경우 [Azure Policy](../governance/policy/overview.md)를 사용 하 여 논리 앱 워크플로에서 특정 커넥터에 대 한 [연결을 만드는 기능을 차단할](../logic-apps/block-connections-connectors.md) 수 있습니다. 자세한 내용은 [Azure Logic Apps에서 특정 커넥터에 의해 생성 된 연결 차단](../logic-apps/block-connections-connectors.md)을 참조 하세요.

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>논리 앱에 대 한 격리 지침

Azure Logic Apps를 사용 하 여 [Azure Government 영향 수준 5 격리 지침](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) 에 설명 된 지역에서 모든 영향 수준을 지원 하 고, [보안 클라우드 컴퓨팅 보안 요구 사항 가이드 (Srg)의 미국 부서](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html)에서 [Azure Government](../azure-government/documentation-government-welcome.md) 수 있습니다. 이러한 요구 사항을 충족 하기 위해 Logic Apps는 전용 리소스가 있는 환경에서 워크플로를 만들고 실행할 수 있는 기능을 지원 하 여 논리 앱에서 다른 Azure 테 넌 트의 성능 영향을 줄이고 다른 테 넌 트와 컴퓨팅 리소스를 공유 하지 않도록 합니다.

* 사용자 고유의 코드를 실행 하거나 XML 변환을 수행 하려면 각각 [인라인 코드 기능](../logic-apps/logic-apps-add-run-inline-code.md) 을 사용 하거나 [맵으로 사용할 어셈블리](../logic-apps/logic-apps-enterprise-integration-maps.md)를 제공 하는 대신 [Azure function을 만들고 호출](../logic-apps/logic-apps-azure-functions.md)합니다. 또한 격리 요구 사항을 준수 하도록 함수 앱에 대 한 호스팅 환경을 설정 합니다.

  예를 들어, 영향 수준 5 요구 사항을 충족 하려면 **격리** 된 가격 책정 계층을 사용 하는 [App Service Environment (ASE)](../app-service/environment/intro.md) 와 함께 [ **격리** 된 가격 책정 계층](../app-service/overview-hosting-plans.md) 을 사용 하 여 [App Service 계획](../azure-functions/functions-scale.md#app-service-plan) 으로 함수 앱을 만듭니다. 이 환경에서 함수 앱은 전용 Azure virtual machines 및 전용 Azure 가상 네트워크에서 실행 되며, 앱에 대 한 계산 격리를 기반으로 하는 네트워크 격리와 최대 스케일 아웃 기능을 제공 합니다. 자세한 내용은 [Azure Government 영향 수준 5 격리 지침-Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions)을 참조 하세요.

  자세한 내용은 다음 항목을 참조하세요.<p>

  * [Azure App Service 계획](../app-service/overview-hosting-plans.md)
  * [Azure Functions 네트워킹 옵션](../azure-functions/functions-networking-options.md)
  * [가상 컴퓨터용 Azure 전용 호스트](../virtual-machines/windows/dedicated-hosts.md)
  * [Azure의 가상 머신 격리](../virtual-machines/isolation.md)
  * [가상 네트워크에 전용 Azure 서비스 배포](../virtual-network/virtual-network-for-azure-services.md)

* 전용 리소스에서 실행 되 고 Azure 가상 네트워크로 보호 되는 리소스에 액세스할 수 있는 논리 앱을 만들려면 [ISE (통합 서비스 환경)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)를 만들 수 있습니다.

  * 일부 Azure virtual network는 azure에서 호스트 되는 Azure Storage, Azure Cosmos DB, Azure SQL Database, 파트너 서비스 또는 고객 서비스와 같은 Azure PaaS 서비스에 대 한 액세스를 제공 하기 위해 개인 끝점 ([Azure 개인 링크](../private-link/private-link-overview.md))을 사용 합니다. 논리 앱에서 개인 끝점을 사용 하는 가상 네트워크에 액세스 해야 하는 경우에는 ISE 내에서 해당 논리 앱을 만들고, 배포 하 고, 실행 해야 합니다.

  * Azure Storage에서 사용 하는 암호화 키에 대 한 더 많은 제어를 위해 [Azure Key Vault](../key-vault/general/overview.md)를 사용 하 여 사용자 고유의 키를 설정, 사용 및 관리할 수 있습니다. 이 기능은 BYOK ("Bring Your Own Key") 라고도 하며, 키를 "고객 관리 키" 라고 합니다. 자세한 내용은 [Azure Logic Apps에서 integration service environment (ISEs)에 대해 미사용 데이터를 암호화 하도록 고객 관리 키 설정](../logic-apps/customer-managed-keys-integration-service-environment.md)을 참조 하세요.

자세한 내용은 다음 항목을 참조하세요.

* [Azure 공용 클라우드에서 격리](../security/fundamentals/isolation-choices.md)
* [Azure의 매우 중요 한 IaaS 앱에 대 한 보안](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>다음 단계

* [Azure Logic Apps에 대 한 Azure 보안 기준](../logic-apps/security-baseline.md)
* [Azure Logic Apps의 배포 자동화](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [논리 앱 모니터링](../logic-apps/monitor-logic-apps-log-analytics.md)
