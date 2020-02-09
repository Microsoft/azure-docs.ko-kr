---
title: 인증 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 다양 한 리소스 및 워크플로에 대 한 인증을 설정 하 고 구성 하는 방법에 대해 알아봅니다. 개발 또는 테스트 목적으로 간단한 UI 기반 인증에서 서비스 주체 인증을 전체 Azure Active Directory 하는 여러 가지 방법으로 서비스 내에서 인증을 구성 하 고 사용할 수 있습니다.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: ce85c45d80a776af84a0987cfbc3f496c2bbb72b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893952"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Azure Machine Learning 리소스 및 워크플로에 대 한 인증 설정
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning의 다양 한 리소스 및 워크플로에 대 한 인증을 설정 하 고 구성 하는 방법에 대해 알아봅니다. 개발 또는 테스트를 위한 간단한 UI 기반 인증에서 서비스 주체 인증을 전체 Azure Active Directory 하는 방법에 이르기까지 다양 한 방법으로 서비스를 인증할 수 있습니다. 또한이 문서에서는 웹 서비스 인증의 작동 방식에 대 한 차이점을 설명 하 고 Azure Machine Learning REST API을 인증 하는 방법을 설명 합니다.

이 방법에서는 다음 작업을 수행 하는 방법을 보여 줍니다.

* 테스트/개발에 대화형 UI 인증 사용
* 서비스 주체 인증 설정
* 작업 영역에 대 한 인증
* Azure Machine Learning REST API에 대 한 OAuth 2.0 전달자 형식 토큰을 가져옵니다.
* 웹 서비스 인증 이해

Azure Machine Learning 내에서 보안 및 인증에 대 한 일반적인 개요는 [개념 문서](concept-enterprise-security.md) 를 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)을 만듭니다.
* Azure Machine Learning SDK를 설치 하거나 SDK가 이미 설치 된 [Azure Machine Learning 노트북 VM](concept-azure-machine-learning-architecture.md#compute-instance) 을 사용 하도록 [개발 환경을 구성](how-to-configure-environment.md) 합니다.

## <a name="interactive-authentication"></a>대화형 인증

이 서비스에 대 한 설명서의 대부분의 예제에서는 간단한 테스트 및 시연 방법으로 Jupyter 노트북의 대화형 인증을 사용 합니다. 이는 빌드 하는 항목을 테스트 하는 간단한 방법입니다. UI 기반 인증 흐름을 자동으로 확인 하는 두 개의 함수 호출이 있습니다.

`from_config()` 함수를 호출 하면 프롬프트가 표시 됩니다.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

`from_config()` 함수는 작업 영역 연결 정보를 포함 하는 JSON 파일을 찾습니다. 또한 `Workspace` 생성자를 사용 하 여 연결 정보를 명시적으로 지정할 수 있으며,이 경우 대화형 인증도 표시 됩니다. 두 호출은 모두 동일 합니다.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

여러 테넌트에 액세스할 수 있는 경우 클래스를 가져오고 대상 테넌트를 명시적으로 정의 해야 합니다. `InteractiveLoginAuthentication`에 대 한 생성자를 호출 하면 위의 호출과 비슷하게 로그인 하 라는 메시지가 표시 됩니다.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

대화형 인증은 테스트 및 학습에 유용 하지만 자동화 되거나 헤드리스 워크플로를 만드는 데 도움이 되지 않습니다. SDK를 사용 하는 자동화 된 프로세스의 경우 서비스 주체 인증을 설정 하는 것이 가장 좋은 방법입니다.

## <a name="set-up-service-principal-authentication"></a>서비스 주체 인증 설정

이 프로세스는 특정 사용자 로그인에서 분리 된 인증을 사용 하도록 설정 하 여 자동화 된 워크플로에서 Azure Machine Learning Python SDK에 인증할 수 있도록 하는 데 필요 합니다. 서비스 주체 인증을 사용 하 여 [REST API에 인증할](#azure-machine-learning-rest-api-auth)수도 있습니다.

서비스 주체 인증을 설정 하려면 먼저 Azure Active Directory에서 앱 등록을 만든 다음 앱 역할 기반 액세스 권한을 ML 작업 영역에 부여 합니다. 이 설치를 완료 하는 가장 쉬운 방법은 Azure Portal [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 하는 것입니다. 포털에 로그인 한 후 이름 옆에 있는 페이지의 오른쪽 위에 있는 `>_` 아이콘을 클릭 하 여 셸을 엽니다.

Azure 계정에서 cloud shell을 사용 하지 않은 경우 기록 되는 모든 파일을 저장 하기 위한 저장소 계정 리소스를 만들어야 합니다. 일반적으로이 저장소 계정에는 무시할 수 있는 월별 비용이 발생 합니다. 또한 다음 명령을 사용 하 여 이전에 사용 하지 않은 경우 machine learning 확장을 설치 합니다.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> 다음 단계를 수행 하려면 구독에 대 한 관리자 여야 합니다.

그런 다음, 다음 명령을 실행 하 여 서비스 주체를 만듭니다. 이름 (이 경우에는 **ml-auth)** 을 지정 합니다.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

출력은 다음과 비슷한 JSON이 됩니다. 이 문서의 다른 단계에 필요 하므로 `clientId`, `clientSecret`및 `tenantId` 필드를 기록해 둡니다.

```json
{
    "clientId": "your-client-id",
    "clientSecret": "your-client-secret",
    "subscriptionId": "your-sub-id",
    "tenantId": "your-tenant-id",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com",
    "activeDirectoryGraphResourceId": "https://graph.windows.net",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net"
}
```

이제 다음 명령을 실행 하 여 위에서 만든 서비스 사용자에 대 한 세부 정보를 가져옵니다. 위의 `clientId` 값을 `--id` 매개 변수에 대 한 입력으로 사용 합니다.

```azurecli-interactive
az ad sp show --id your-client-id
```

다음은 명령의 JSON 출력에 대 한 간단한 예제입니다. 다음 단계에 대 한 값이 필요 하므로 `objectId` 필드를 기록해 둡니다.

```json
{
    "accountEnabled": "True",
    "addIns": [],
    "appDisplayName": "ml-auth",
    ...
    ...
    ...
    "objectId": "your-sp-object-id",
    "objectType": "ServicePrincipal"
}
```

다음으로, 다음 명령을 사용 하 여 machine learning 작업 영역에 대 한 서비스 주체 액세스 권한을 할당 합니다. 작업 영역 이름과 `-w` 및 `-g` 매개 변수에 대 한 리소스 그룹 이름이 각각 필요 합니다. `--user` 매개 변수의 경우 이전 단계의 `objectId` 값을 사용 합니다. `--role` 매개 변수를 사용 하 여 서비스 주체에 대 한 액세스 역할을 설정할 수 있으며, 일반적으로 **소유자** 또는 **참가자**중 하나를 사용 합니다. 둘 다 계산 클러스터 및 데이터 저장소와 같은 기존 리소스에 대 한 쓰기 권한이 있지만 **소유자** 만 이러한 리소스를 프로 비전 할 수 있습니다. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

이 호출은 출력을 생성 하지 않지만 작업 영역에 대 한 서비스 주체 인증을 설정 했습니다.

## <a name="authenticate-to-your-workspace"></a>작업 영역에 대 한 인증

이제 서비스 주체 인증을 사용 하도록 설정 했으므로 사용자로 물리적으로 로그인 하지 않고도 SDK에서 작업 영역에 인증할 수 있습니다. `ServicePrincipalAuthentication` 클래스 생성자를 사용 하 고 이전 단계에서 가져온 값을 매개 변수로 사용 합니다. `tenant_id` 매개 변수는 위의 `tenantId`에 매핑되고 `service_principal_id` `clientId`에 매핑되고 `service_principal_password` `clientSecret`에 매핑됩니다.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

이제 `sp` 변수는 SDK에서 직접 사용 하는 인증 개체를 포함 합니다. 일반적으로 다음 코드와 같이 환경 변수에서 위의 사용 된 id/암호를 저장 하는 것이 좋습니다.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

Python에서 실행 되 고 SDK를 주로 사용 하는 자동화 된 워크플로의 경우 대부분의 경우 인증에이 개체를 그대로 사용할 수 있습니다. 다음 코드는 방금 만든 인증 개체를 사용 하 여 작업 영역에 인증 합니다.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API 인증

위의 단계에서 만든 서비스 주체를 사용 하 여 Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/)에 인증할 수도 있습니다. 자동화 된 워크플로에서 헤드리스 인증에 대 한 서비스 간 호출을 허용 하는 Azure Active Directory [클라이언트 자격 증명 부여 흐름](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)을 사용 합니다. 예제는 Python 및 node.js 둘 다에서 [ADAL 라이브러리](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) 를 사용 하 여 구현 되지만 openid connect Connect 1.0을 지 원하는 오픈 소스 라이브러리를 사용할 수도 있습니다. 

> [!NOTE]
> MSAL는 ADAL 보다 최신 라이브러리 이지만 MSAL와 클라이언트 자격 증명을 사용 하 여 서비스 간 인증을 수행할 수 없습니다 .이는 주로 특정 사용자에 연결 된 대화형/UI 인증을 위한 클라이언트 쪽 라이브러리 이기 때문입니다. REST API를 사용 하 여 자동화 된 워크플로를 빌드하려면 아래와 같이 ADAL을 사용 하는 것이 좋습니다.

### <a name="nodejs"></a>Node.js

Node.js를 사용 하 여 인증 토큰을 생성 하려면 다음 단계를 사용 합니다. 사용자 환경에서 `npm install adal-node`를 실행 합니다. 그런 다음, 위의 단계에서 만든 서비스 사용자의 `tenantId`, `clientId`및 `clientSecret`를 다음 스크립트에서 일치 하는 변수의 값으로 사용 합니다.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

`tokenResponse` 변수는 토큰 및 관련 메타 데이터 (예: 만료 시간)를 포함 하는 개체입니다. 토큰은 1 시간 동안 유효 하며, 동일한 호출을 다시 실행 하 여 새 토큰을 검색 함으로써 새로 고칠 수 있습니다. 다음은 샘플 응답입니다.

```javascript
{ 
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id' 
}
```

`accessToken` 속성을 사용 하 여 인증 토큰을 가져옵니다. 토큰을 사용 하 여 API 호출을 수행 하는 방법에 대 한 예제는 [REST API 설명서](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) 를 참조 하세요.

### <a name="python"></a>Python 

다음 단계를 사용 하 여 Python을 사용 하 여 인증 토큰을 생성 합니다. 사용자 환경에서 `pip install adal`를 실행 합니다. 그런 다음 위의 단계에서 만든 서비스 사용자의 `tenantId`, `clientId`및 `clientSecret`를 다음 스크립트의 적절 한 변수에 대 한 값으로 사용 합니다.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

`token_response` 변수는 토큰 및 관련 메타 데이터 (예: 만료 시간)를 포함 하는 사전입니다. 토큰은 1 시간 동안 유효 하며, 동일한 호출을 다시 실행 하 여 새 토큰을 검색 함으로써 새로 고칠 수 있습니다. 다음은 샘플 응답입니다.

```python
{
    'tokenType': 'Bearer', 
    'expiresIn': 3599, 
    'expiresOn': '2019-12-17 19:47:15.150205', 
    'resource': 'https://management.azure.com/', 
    'accessToken': 'random-oauth-token', 
    'isMRRT': True, 
    '_clientId': 'your-client-id', 
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

`token_response["accessToken"]`를 사용 하 여 인증 토큰을 가져옵니다. 토큰을 사용 하 여 API 호출을 수행 하는 방법에 대 한 예제는 [REST API 설명서](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) 를 참조 하세요.

## <a name="web-service-authentication"></a>웹 서비스 인증

Azure Machine Learning의 웹 서비스는 위에서 설명한 것과 다른 인증 패턴을 사용 합니다. 배포 된 웹 서비스를 인증 하는 가장 쉬운 방법은 **키 기반 인증**을 사용 하는 것입니다 .이 인증을 사용 하면 새로 고칠 필요가 없는 정적 전달자 유형 인증 키가 생성 됩니다. 배포 된 웹 서비스에 대해서만 인증 해야 하는 경우 위에 표시 된 대로 서비스 주체 인증을 설정 하지 않아도 됩니다.

Azure Kubernetes Service에 배포 된 웹 서비스에는 기본적으로 키 기반 인증이 *사용* 됩니다. 배포 된 서비스 Azure Container Instances 기본적으로 키 기반 인증을 *사용 하지 않도록* 설정 되어 있지만 ACI 웹 서비스를 만들 때 `auth_enabled=True`를 설정 하 여 사용 하도록 설정할 수 있습니다. 다음은 키 기반 인증을 사용 하도록 설정 된 ACI 배포 구성을 만드는 예입니다.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enable=True)
```

그런 다음 `Model` 클래스를 사용 하 여 배포에서 사용자 지정 ACI 구성을 사용할 수 있습니다.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

인증 키를 페치 하려면 `aci_service.get_keys()`를 사용 합니다. 키를 다시 생성 하려면 `regen_key()` 함수를 사용 하 고 **기본** 또는 **보조**를 전달 합니다.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

웹 서비스는 토큰 기반 인증도 지원 하지만 Azure Kubernetes 서비스 배포에 대해서만 지원 합니다. 인증에 대 한 자세한 내용은 웹 서비스를 사용 하 [는 방법을](how-to-consume-web-service.md) 참조 하세요.

### <a name="token-based-web-service-authentication"></a>토큰 기반 웹 서비스 인증

웹 서비스에 대 한 토큰 인증을 사용 하도록 설정 하는 경우 사용자는 웹 서비스에 액세스 하는 Azure Machine Learning JSON Web Token를 제공 해야 합니다. 토큰은 지정 된 시간 프레임 후에 만료 되므로 계속 호출 하려면 새로 고쳐야 합니다.

* 토큰 인증은 Azure Kubernetes Service에 배포할 때 **기본적으로 사용 하지 않도록 설정** 됩니다.
* Azure Container Instances에 배포 하는 경우에는 토큰 인증이 **지원 되지 않습니다** .

토큰 인증을 제어 하려면 배포를 만들거나 업데이트할 때 `token_auth_enabled` 매개 변수를 사용 합니다.

토큰 인증을 사용 하는 경우 `get_token` 메서드를 사용 하 여 JWT (JSON Web Token) 및 해당 토큰의 만료 시간을 검색할 수 있습니다.

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 토큰의 `refresh_by` 시간 이후에 새 토큰을 요청 해야 합니다. Python SDK 외부에서 토큰을 새로 고쳐야 하는 경우 한 가지 옵션은 앞에서 설명한 대로 서비스 주체 인증과 함께 REST API를 사용 하 여 주기적으로 `service.get_token()` 호출 하는 것입니다.
>
> Azure Kubernetes Service 클러스터와 동일한 지역에 Azure Machine Learning 작업 영역을 만드는 것이 좋습니다. 
>
> 토큰을 사용 하 여 인증 하기 위해 웹 서비스는 Azure Machine Learning 작업 영역이 생성 되는 영역에 대 한 호출을 수행 합니다. 작업 영역을 사용할 수 없는 경우에는 클러스터가 작업 영역에서 다른 지역에 있는 경우에도 웹 서비스에 대 한 토큰을 가져올 수 없습니다. 결과적으로 작업 영역을 다시 사용할 수 있을 때까지 Azure AD 인증를 사용할 수 없습니다. 
>
> 또한 클러스터 지역과 작업 영역 영역 간의 거리가 클수록 토큰을 인출 하는 데 시간이 오래 걸립니다.

## <a name="next-steps"></a>다음 단계

* [이미지 분류 모델을 학습 하 고 배포](tutorial-train-models-with-aml.md)합니다.
* [웹 서비스로 배포 된 Azure Machine Learning 모델을 사용](how-to-consume-web-service.md)합니다.
