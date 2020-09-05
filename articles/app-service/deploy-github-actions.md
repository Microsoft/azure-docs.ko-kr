---
title: GitHub Actions를 사용하여 CI/CD 구성
description: GitHub Actions를 사용하여 CI/CD 파이프라인에서 Azure App Service에 코드를 배포하는 방법을 알아봅니다. 빌드 작업을 사용자 지정하고 복잡한 배포를 실행합니다.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 264976fdfe514a8778c60fe9242ac555f268718d
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962573"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub Actions를 사용하여 App Service에 배포

[GitHub Actions](https://help.github.com/en/articles/about-github-actions)를 사용하면 자동화된 소프트웨어 개발 수명 주기 워크플로를 유연성 있게 빌드할 수 있습니다. GitHub용 Azure App Service 작업을 사용하면 GitHub Actions를 통해 [Azure App Service](overview.md)에 배포할 워크플로를 자동화할 수 있습니다.

> [!IMPORTANT]
> GitHub Actions는 현재 베타 버전입니다. 먼저 GitHub 계정을 사용하여 [미리 보기에 조인하려면 가입](https://github.com/features/actions)해야 합니다.
> 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

Azure App Service 워크플로의 경우 파일에는 다음 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의 합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**빌드** | 1. 환경을 설정 합니다. <br /> 2. 웹 앱을 빌드합니다. |
|**배포** | 1. 웹 앱을 배포 합니다. |

## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

# <a name="user-level-credentials"></a>[사용자 수준 자격 증명](#tab/userlevel)

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

위의 예제에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 아래와 같이 App Service 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명을 포함 하는 JSON 개체입니다. 나중에이 JSON 개체를 복사 합니다.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예제의 범위는 전체 리소스 그룹이 아니라 특정 App Service 앱으로 제한 됩니다.

# <a name="app-level-credentials"></a>[앱 수준 자격 증명](#tab/applevel)

앱에 대 한 게시 프로필을 사용 하 여 앱 수준 자격 증명을 사용할 수 있습니다. 포털에서 앱의 관리 페이지로 이동 합니다. **개요** 페이지에서 **게시 프로필 가져오기** 옵션을 클릭 합니다.

나중에 파일의 내용이 필요 합니다.

---

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

# <a name="user-level-credentials"></a>[사용자 수준 자격 증명](#tab/userlevel)

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가**합니다.

[사용자 수준 자격 증명](#generate-deployment-credentials)을 사용 하려면 Azure CLI 명령의 전체 JSON 출력을 암호의 값 필드에 붙여넣습니다. 암호에와 같은 이름을 지정 합니다 `AZURE_CREDENTIALS` .

나중에 워크플로 파일을 구성 하는 경우 `creds` Azure 로그인 동작의 입력에 대 한 암호를 사용 합니다. 예를 들어:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[앱 수준 자격 증명](#tab/applevel)

[GitHub](https://github.com/)에서 리포지토리를 찾아보고 **설정 > 비밀을 선택 하 > 새 비밀을 추가**합니다.

[앱 수준 자격 증명](#generate-deployment-credentials)을 사용 하려면 다운로드 한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다. 암호에와 같은 이름을 지정 합니다 `azureWebAppPublishProfile` .

나중에 워크플로 파일을 구성 하는 경우 `publish-profile` Azure 웹 앱 배포 작업의 입력에 대 한 암호를 사용 합니다. 예를 들어:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>환경 설정

환경 설정은 설정 작업 중 하나를 사용하여 수행할 수 있습니다.

|**언어**  |**설정 작업**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

다음 예제에서는 지원되는 다양한 언어의 환경을 설정하는 워크플로의 일부를 보여 줍니다.

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>웹앱 빌드

이 섹션은 언어에 따라 달라지며 Azure App Service에서 지원하는 언어의 경우 이 섹션은 각 언어의 표준 빌드 단계여야 합니다.

다음 예제에서는 지원되는 다양한 언어로 웹앱을 빌드하는 워크플로의 일부를 보여 줍니다.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>App Service에 배포

App Service 앱에 코드를 배포하려면 `azure/webapps-deploy@v2` 작업을 사용합니다. 이 작업에는 다음 4개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **app-name** | (필수) App Service 앱의 이름 | 
| **publish-profile** | (선택 사항) 웹 배포 비밀을 포함하는 게시 프로필 파일 콘텐츠 |
| **package** | (선택 사항) 패키지 또는 폴더 경로. *.zip, *.war, *.jar 또는 배포할 폴더 |
| **slot-name** | (선택 사항) 프로덕션 슬롯이 아닌 기존 슬롯 입력 |

# <a name="user-level-credentials"></a>[사용자 수준 자격 증명](#tab/userlevel)

다음은 Azure 서비스 주체를 사용하여 Node.js 앱을 빌드하고 Azure에 배포하는 샘플 워크플로입니다. `creds`입력 `AZURE_CREDENTIALS` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

# <a name="app-level-credentials"></a>[앱 수준 자격 증명](#tab/applevel)

앱의 게시 프로필을 사용 하 여 Node.js 앱을 빌드하고 Azure에 배포 하는 샘플 워크플로는 다음과 같습니다. `publish-profile`입력 `azureWebAppPublishProfile` 이 앞에서 만든 비밀을 참조 하는 방법을 확인 합니다.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="next-steps"></a>다음 단계

GitHub에서 다양한 리포지토리로 그룹화된 일련의 작업을 찾을 수 있습니다. 각 리포지토리에는 CI/CD에 GitHub를 사용하고 Azure에 앱을 배포하는 데 도움이 되는 문서 및 예제가 포함됩니다.

- [Azure에 배포할 작업 워크플로](https://github.com/Azure/actions-workflow-samples)

- [Azure 로그인](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [컨테이너용 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 로그인/로그아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로](https://github.com/actions/starter-workflows)