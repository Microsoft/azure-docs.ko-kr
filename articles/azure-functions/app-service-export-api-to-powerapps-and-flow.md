---
title: Azure에서 호스트되는 API를 PowerApps 및 Microsoft Flow로 내보내기
description: App Service에 호스트되는 API를 PowerApps 및 Microsoft Flow로 내보내는 방법에 대한 개요
ms.topic: conceptual
ms.date: 12/15/2017
ms.reviewer: sunayv
ms.openlocfilehash: 632818bf82e41e6be0a96d30cc1c4fa631718a3b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233086"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Azure에서 호스트되는 API를 PowerApps 및 Microsoft Flow로 내보내기

[PowerApps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/)는 플랫폼 간에 데이터와 작업을 연결하는 사용자 지정 비즈니스 앱을 빌드하고 사용할 수 있는 서비스입니다. [Microsoft Flow](/learn/modules/get-started-with-flow/index)는 자주 사용하는 앱과 서비스 간의 워크플로 및 비즈니스 프로세스를 간단하게 자동화할 수 있습니다. PowerApps와 Microsoft Flow에는 Office 365, Dynamics 365, Salesforce 등 다양한 데이터 원본 커넥터가 기본적으로 제공됩니다. 경우에 따라 앱 및 흐름 빌더도 데이터 원본 그리고 조직에서 작성한 API에 연결하려 합니다.

마찬가지로 조직 내에서 자신의 API를 보다 광범위하게 노출하려는 개발자는 앱 및 흐름 빌더에 자신의 API를 제공하려 합니다. 이 토픽에서는 [Azure Functions](../azure-functions/functions-overview.md) 또는 [Azure App Service](../app-service/overview.md)를 통해 작성한 API를 노출하는 방법을 보여 줍니다. 내보낸 API는 *사용자 지정 커넥터*가 되며, 이 커넥터는 마치 기본 커넥터처럼 PowerApps 및 Microsoft Flow에 사용됩니다.

> [!IMPORTANT]
> 이 문서에 표시 된 API 정의 기능은 Azure Functions 런타임 및 App Services 앱 [의 버전 1. x](functions-versions.md#creating-1x-apps) 에만 지원 됩니다. 버전 2.x의 함수는 OpenAPI 정의를 만들고 유지 관리 하기 위해 API Management와 통합 됩니다. 자세한 내용은 [Azure API Management를 사용 하 여 함수에 대한 OpenAPI 정의 만들기](functions-openapi-definition.md)를 참조 하세요. 

## <a name="create-and-export-an-api-definition"></a>API 정의를 만들고 내보내기
API를 내보내기 전에 먼저 OpenAPI 정의(이전에는 [Swagger](https://swagger.io/) 파일)를 사용하여 API를 설명해야 합니다. 이 정의에는 API에서 사용할 수 있는 작업 및 API에 대한 요청 및 응답 데이터가 구성되는 방식에 대한 정보가 포함됩니다. PowerApps 및 Microsoft Flow는 모든 OpenAPI 2.0 정의를 위한 사용자 지정 커넥터를 만들 수 있습니다. Azure Functions 및 Azure App Service는 OpenAPI 정의를 만들고 호스트하고 관리할 수 있는 기본 지원을 제공합니다. 자세한 내용은 [Azure App Service에서 CORS를 통해 RESTful API 호스팅](../app-service/app-service-web-tutorial-rest-api.md)을 참조하세요.

> [!NOTE]
> OpenAPI 정의를 사용하지 않고 PowerApps 및 Microsoft Flow UI에서 사용자 지정 커넥터를 빌드할 수도 있습니다. 자세한 내용은 [사용자 지정 커넥터 등록 및 사용(PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) 및 [사용자 지정 커넥터 등록 및 사용(Microsoft Flow)](/power-automate/developer/register-custom-api)을 참조하세요.

API 정의를 내보내려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure Functions 또는 또 다른 App Service 애플리케이션으로 이동합니다.

    Azure Functions를 사용하는 경우 함수 앱을 선택하고 **플랫폼 기능**, **API 정의**를 차례로 선택합니다.

    ![Azure Functions API 정의](media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png)

    Azure App Service를 사용하는 경우 설정 목록에서 **API 정의**를 선택합니다.

    ![App Service API 정의](media/app-service-export-api-to-powerapps-and-flow/api-definition-app.png)

2. **PowerApps + Microsoft Flow로 내보내기** 단추를 사용할 수 있어야 합니다. 사용할 수 없으면 OpenAPI 정의부터 만들어야 합니다. 이 단추를 클릭하면 내보내기 프로세스가 시작됩니다.

    ![PowerApps + Microsoft Flow로 내보내기 단추](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. **내보내기 모드**를 선택합니다.

    **기본**을 사용하면 Azure Portal 내에서 사용자 지정 커넥터를 만들 수 있습니다. 이렇게 하려면 PowerApps 또는 Microsoft Flow에 로그인해야 하며 대상 환경에서 커넥터를 만들 수 있는 권한이 필요합니다. 이 모드는 이러한 두 가지 요구 사항을 충족할 수 있는 경우 권장되는 방법입니다. 이 모드를 사용하는 경우 아래의 [기본 내보내기 사용](#express) 지침을 따릅니다.

    **수동**을 사용하면 API 정의를 내보낸 후 PowerApps 또는 Microsoft Flow 포털을 사용하여 가져올 수 있습니다. 이 모드는 Azure 사용자와 커넥터를 만들 수 있는 권한이 있는 사용자가 다른 사람이거나 다른 Azure 테넌트에서 커넥터를 만들어야 하는 경우 권장되는 방법입니다. 이 모드를 사용하는 경우 아래의 [수동 내보내기 사용](#manual) 지침을 따릅니다.

    ![내보내기 모드](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> 사용자 지정 커넥터는 API 정의 *복사*를 사용하므로 사용자가 애플리케이션 및 해당 API 정의를 변경하더라도 PowerApps 및 Microsoft Flow가 그 사실을 즉시 알 수 없습니다. 변경 작업을 수행하는 경우 새 버전에 대해 내보내기 단계를 반복합니다.

<a name="express"></a>
## <a name="use-express-export"></a>기본 내보내기 사용

**기본** 모드에서 내보내기를 완료하려면 다음 단계를 수행합니다.

1. 내보내려는 PowerApps 또는 Microsoft Flow 테넌트에 로그인되었는지 확인합니다. 

2. 표에 지정된 대로 설정을 사용합니다.

    |설정|설명|
    |--------|------------|
    |**환경**|사용자 지정 커넥터를 저장해야 하는 환경을 선택합니다. 자세한 내용은 [환경 개요](https://powerapps.microsoft.com/tutorials/environments-overview/)를 참조하세요.|
    |**사용자 지정 API 이름**|PowerApps 및 Microsoft Flow 빌더의 커넥터 목록에 표시될 이름을 입력합니다.|
    |**보안 구성 준비**|필요한 경우 사용자에게 API에 대한 액세스 권한을 부여하는 데 필요한 보안 구성 세부 정보를 제공합니다. 이 예제에서는 API 키를 보여 줍니다. 자세한 내용은 아래의 [인증 유형 지정](#auth)을 참조하세요.|
 
    ![PowerApps 및 Microsoft Flow로 기본 내보내기](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. **확인**을 클릭합니다. 이제 사용자 지정 커넥터가 빌드되고 지정한 환경에 추가됩니다.

<a name="manual"></a>
## <a name="use-manual-export"></a>수동 내보내기 사용

**수동** 모드에서 내보내기를 완료하려면 다음 단계를 수행합니다.

1. **다운로드**를 클릭하고 파일을 저장하거나 복사 단추를 클릭하고 URL을 저장합니다. 가져오는 동안 다운로드 파일 또는 URL을 사용할 것입니다.
 
    ![PowerApps 및 Microsoft Flow로 수동 내보내기](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. API 정의에 보안 정의가 포함된 경우 2단계에서 보안 정의가 호출됩니다. 가져오는 동안 PowerApps 및 Microsoft Flow가 보안 정의를 감지하고 보안 정보를 요청합니다. 다음 섹션에서 사용할 각 정의와 관련된 자격 증명을 수집합니다. 자세한 내용은 아래의 [인증 유형 지정](#auth)을 참조하세요.

    ![수동 내보내기의 보안](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    이 예제에서는 OpenAPI 정의에 포함된 API 키 보안 정의를 보여 줍니다.

API 정의를 내보냈으니, 이제 PowerApps 및 Microsoft Flow로 가져와서 사용자 지정 커넥터를 만들어야 합니다. 사용자 지정 커넥터는 두 서비스 간에 공유되므로 정의를 한 번만 가져오면 됩니다.

PowerApps 및 Microsoft Flow로 API 정의를 가져오려면 다음 단계를 수행합니다.

1. [powerapps.com](https://web.powerapps.com) 또는 [flow.microsoft.com](https://flow.microsoft.com)으로 이동합니다.

2. 오른쪽 위에서 기어 아이콘을 클릭한 다음, **사용자 지정 커넥터**를 클릭합니다.

   ![서비스의 기어 아이콘](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. **사용자 지정 커넥터 만들기**, **OpenAPI 정의 가져오기**를 차례로 클릭합니다.

   ![사용자 지정 커넥터 만들기](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. 사용자 지정 커넥터에 대한 이름을 입력한 다음, 내보낸 OpenAPI 정의로 이동하고, **계속**을 클릭합니다.

   ![OpenAPI 정의 업로드](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. **일반** 탭에서 OpenAPI 정의에서 제공되는 정보를 검토합니다.

5. **보안** 탭에서 인증 세부 정보를 입력하라는 메시지가 나오면 인증 유형에 적합한 값을 입력합니다. **계속**을 클릭합니다.

    ![보안 탭](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    이 예제에서는 API 키 인증에 대한 필수 필드를 보여 줍니다. 필드는 인증 유형에 따라 달라집니다.

6. **정의** 탭에서 OpenAPI 파일에 정의된 모든 작업이 자동으로 채워집니다. 필요한 모든 작업이 정의되면 다음 단계로 이동할 수 있습니다. 그렇지 않은 경우 여기에서 작업을 추가하고 수정할 수 있습니다.

    ![정의 탭](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    이 예제에는 `CalculateCosts`라는 작업 하나가 있습니다. **설명** 같은 메타데이터는 전부 OpenAPI 파일에서 옵니다.

7. 페이지 위쪽의 **커넥터 만들기**를 클릭합니다.

이제 PowerApps 및 Microsoft Flow에서 사용자 지정 커넥터에 연결할 수 있습니다. PowerApps 및 Microsoft Flow 포털에서 커넥터를 만드는 방법에 대한 자세한 내용은 [사용자 지정 커넥터 등록(PowerApps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) 및 [사용자 지정 커넥터 등록(Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector)을 참조하세요.

<a name="auth"></a>
## <a name="specify-authentication-type"></a>인증 유형 지정

PowerApps 및 Microsoft Flow는 사용자 지정 커넥터에 대한 인증을 제공하는 ID 공급자 컬렉션을 지원합니다. API에서 인증을 요구하는 경우 다음 예제처럼 OpenAPI 문서에서 _보안 정의_로 캡처되는지 확인합니다.

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
내보내는 동안 PowerApps 및 Microsoft Flow가 사용자를 인증할 수 있는 구성 값을 제공합니다.

이 섹션에서는 **기본** 모드에서 지원되는 인증 유형인 API 키, Azure Active Directory 및 제네릭 OAuth 2.0을 설명합니다. PowerApps 및 Microsoft Flow는 기본 인증뿐 아니라 Dropbox, Facebook, SalesForce 같은 특정 서비스에 대해 OAuth 2.0을 지원합니다.

### <a name="api-key"></a>API 키
API 키를 사용하는 경우 커넥터 사용자가 연결을 만들 때 키를 제공하라는 메시지가 표시됩니다. 사용자가 필요한 키를 쉽게 알 수 있는 API 키 이름을 지정하는 것이 좋습니다. 이전 예제에서는 사람들이 API 키에 대한 정보를 어디서 가져와야 하는지 쉽게 알 수 있도록 `API Key (contact meganb@contoso.com)`라는 이름을 사용했습니다. Azure Functions의 경우 이 키는 일반적으로 호스트 키 중 하나이며, 함수 앱 내의 여러 함수를 포괄합니다.

### <a name="azure-active-directory-azure-ad"></a>Azure AD(Azure Active Directory)
Azure AD를 사용하는 경우 두 개의 Azure AD 애플리케이션 등록이 필요합니다. 하나는 API 자체에 대한 것이고 다른 하나는 사용자 지정 커넥터에 대한 것입니다.

- API에 대한 등록을 구성하려면 [App Service 인증/권한 부여](../app-service/configure-authentication-provider-aad.md) 기능을 사용합니다.

- 커넥터에 대한 등록을 구성하려면 [Azure AD 애플리케이션 추가](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)의 단계를 따릅니다. 등록에는 API 및 `https://msmanaged-na.consent.azure-apim.net/redirect`의 회신 URL에 대한 위임된 액세스가 필요합니다. 

자세한 내용은 [PowerApps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) 및 [Microsoft Flow](https://docs.microsoft.com/connectors/custom-connectors/azure-active-directory-authentication)에 대한 Azure AD 등록 예제를 참조하세요. 이러한 예제에서는 Azure Resource Manager를 API로 사용합니다. 다음 단계를 수행하는 경우 API를 대체하세요.

다음과 같은 구성 값이 필요합니다.
- **클라이언트 ID** - 커넥터 Azure AD 등록의 클라이언트 ID
- **클라이언트 암호** - 커넥터 Azure AD 등록의 클라이언트 암호
- **로그인 URL** - Azure AD의 기준 URL. Azure에서는 일반적으로 `https://login.windows.net`입니다.
- **테넌트 ID** - 로그인에 사용할 테넌트의 ID. "common" 또는 커넥터를 만드는 테넌트의 ID여야 합니다.
- **리소스 URL** - API에 대한 Azure AD 등록의 리소스 URL

> [!IMPORTANT]
> 다른 사람이 수동 흐름의 일부로 PowerApps 및 Microsoft Flow로 API 정의를 가져오는 경우 API의 리소스 URL뿐 아니라 *커넥터 등록*의 클라이언트 ID 및 클라이언트 암호를 해당 사용자에게 제공해야 합니다. 이러한 비밀은 안전하게 관리해야 합니다. **API 보안 자격 증명 자체를 공유하지 마세요.**

### <a name="generic-oauth-20"></a>제네릭 OAuth 2.0
제네릭 OAuth 2.0을 사용하는 경우 모든 OAuth 2.0 공급자와 통합할 수 있습니다. 이 옵션을 사용하면 기본적으로 지원되지 않는 사용자 지정 공급자를 작업할 수 있습니다.

다음과 같은 구성 값이 필요합니다.
- **클라이언트 ID** - OAuth 2.0 클라이언트 ID
- **클라이언트 암호** - OAuth 2.0 클라이언트 암호
- **권한 부여 URL** - OAuth 2.0 권한 부여 URL
- **토큰 URL** - OAuth 2.0 토큰 URL
- **새로 고침 URL** - OAuth 2.0 새로 고침 URL


