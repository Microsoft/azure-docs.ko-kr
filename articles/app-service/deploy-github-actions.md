---
title: GitHub 작업을 사용 하 여 CI/CD 구성
description: GitHub 작업을 사용 하 여 CI/CD 파이프라인에서 Azure App Service 하는 코드를 배포 하는 방법을 알아봅니다. 빌드 작업을 사용자 지정 하 고 복잡 한 배포를 실행 합니다.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: e3d6e730846388c4b74cfa0b6361629e836b0517
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74670185"
---
# <a name="deploy-to-app-service-using-github-actions"></a>GitHub 작업을 사용 하 여 App Service에 배포

[GitHub 작업](https://help.github.com/en/articles/about-github-actions) 을 통해 자동화 된 소프트웨어 개발 수명 주기 워크플로를 유연 하 게 빌드할 수 있습니다. GitHub에 대한 Azure App Service 작업을 사용 하 여 GitHub 작업을 통해 [Azure App Service](overview.md) 에 배포할 워크플로를 자동화할 수 있습니다.

> [!IMPORTANT]
> GitHub 동작은 현재 베타 버전입니다. 먼저 GitHub 계정을 사용 하 여 [미리 보기에 참여 하려면 등록](https://github.com/features/actions) 해야 합니다.
> 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML (.yml) 파일에 의해 정의 됩니다. 이 정의는 워크플로를 구성 하는 다양 한 단계와 매개 변수를 포함 합니다.

Azure App Service 워크플로의 경우 파일에는 다음과 같은 세 개의 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 사용자 정의 <br /> 2. GitHub 암호 만들기 |
|**빌드** | 1. 환경 설정 <br /> 2. 웹 앱 빌드 |
|**배포** | 1. 웹 앱 배포 |

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](https://docs.microsoft.com/cli/azure/)에서 [az ad sp create-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/) 를 사용 하거나 **사용해 보기** 단추를 선택 하 여이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

이 예제에서는 리소스의 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 App Service 앱에 대한 액세스를 제공 하는 역할 할당 자격 증명입니다. GitHub에서 인증 하는 데 사용할 수 있는이 JSON 개체를 복사 합니다.

> [!NOTE]
> 인증을 위해 게시 프로필을 사용 하기로 결정 한 경우에는 서비스 주체를 만들 필요가 없습니다.

> [!IMPORTANT]
> 최소한의 액세스 권한을 부여 하는 것이 항상 좋은 방법입니다. 이전 예제의 범위는 전체 리소스 그룹이 아닌 특정 App Service 앱으로 제한 되기 때문입니다.

## <a name="configure-the-github-secret"></a>GitHub 암호 구성

또한 앱 수준 자격 증명 (예: 배포용 프로필 게시)을 사용할 수 있습니다. 암호를 구성 하는 단계를 수행 합니다.

1. **게시 프로필 가져오기** 옵션을 사용 하 여 포털에서 App Service 앱에 대한 게시 프로필을 다운로드 합니다.

2. [GitHub](https://github.com/)에서 리포지토리를 찾아보고, **설정 > 비밀을 선택 하 > 새 비밀을 추가 합니다** .

    ![secrets](media/app-service-github-actions/secrets.png)

3. 다운로드 한 게시 프로필 파일의 내용을 비밀의 값 필드에 붙여넣습니다.

4. 이제 분기의 워크플로 파일: `.github/workflows/workflow.yml` Azure 웹 앱 배포 작업의 입력 `publish-profile`에 대한 암호를 바꿉니다.
    
    ```yaml
        - uses: azure/webapps-deploy@v1
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. 정의 되 면 아래와 같이 비밀이 표시 됩니다.

    ![secrets](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>환경 설정

환경 설정은 설치 작업 중 하나를 사용 하 여 수행할 수 있습니다.

|**언어**  |**설치 작업**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

다음 예에서는 지원 되는 여러 언어에 대해 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

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

## <a name="build-the-web-app"></a>웹 앱 빌드

이 섹션은 언어와 Azure App Service에서 지 원하는 언어에 따라 달라 집니다 .이 섹션은 각 언어의 표준 빌드 단계 여야 합니다.

다음 예에서는 다양 한 언어에서 웹 앱을 빌드하는 워크플로의 일부를 보여 줍니다.

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

App Service 앱에 코드를 배포 하려면 `azure/webapps-deploy@v1 ` 작업을 사용 합니다. 이 작업에는 다음과 같은 4 개의 매개 변수가 있습니다.

| **매개 변수**  | **설명**  |
|---------|---------|
| **앱 이름** | 하다 App Service 앱의 이름 | 
| **게시-프로필** | 필드 웹 배포 암호를 사용 하 여 프로필 파일 콘텐츠 게시 |
| **package** | 필드 패키지 또는 폴더의 경로입니다. \* .zip, * war, * jar 또는 배포할 폴더 |
| **슬롯-이름** | 필드 프로덕션 슬롯이 아닌 기존 슬롯을 입력 하세요. |

### <a name="deploy-using-publish-profile"></a>게시 프로필을 사용 하 여 배포

다음은 게시 프로필을 사용 하 여 node.js 앱을 빌드하고 Azure에 배포 하는 샘플 워크플로입니다.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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
          uses: azure/webapps-deploy@v1
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Azure 서비스 주체를 사용 하 여 배포

Azure 서비스 주체를 사용 하 여 Azure에 node.js 앱을 빌드 및 배포 하는 샘플 워크플로는 다음과 같습니다.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout Github Action' 
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
    - uses: azure/webapps-deploy@v1
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>다음 단계

GitHub에서 다양 한 리포지토리로 그룹화 된 일련의 작업을 찾을 수 있습니다. 여기에는 CI/CD 용 GitHub를 사용 하 고 Azure에 앱을 배포 하는 데 도움이 되는 설명서와 예제가 포함 되어 있습니다.

- [Azure에 배포할 작업 워크플로](https://github.com/Azure/actions-workflow-samples)

- [Azure 로그인](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [컨테이너에 대한 Azure WebApp](https://github.com/Azure/webapps-container-deploy)

- [Docker 로그인/로그 아웃](https://github.com/Azure/docker-login)

- [워크플로를 트리거하는 이벤트](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s 배포](https://github.com/Azure/k8s-deploy)

- [시작 워크플로](https://github.com/actions/starter-workflows)
