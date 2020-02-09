---
title: GitHub 작업을 사용 하 여 Azure Functions에서 코드 업데이트 수행
description: Github 작업을 사용 하 여 GitHub에서 Azure Functions 프로젝트를 빌드하고 배포 하는 워크플로를 정의 하는 방법을 알아봅니다.
author: ahmedelnably
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: aelnably
ms.openlocfilehash: c34847577b7e83228fafad431f541497be9a21ae
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75769152"
---
# <a name="continuous-delivery-by-using-github-action"></a>GitHub 작업을 사용 하 여 지속적인 배달

[GitHub 작업](https://github.com/features/actions) 을 통해 Azure의 함수 앱에 함수 코드를 자동으로 빌드 및 배포 하는 워크플로를 정의할 수 있습니다. 

GitHub 작업에서 [워크플로](https://help.github.com/articles/about-github-actions#workflow) 는 github 리포지토리에서 정의 하는 자동화 된 프로세스입니다. 이 프로세스는 github에서 함수 앱 프로젝트를 빌드하고 배포 하는 방법을 GitHub에 알려 줍니다. 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML (.yml) 파일에 의해 정의 됩니다. 이 정의는 워크플로를 구성 하는 다양 한 단계와 매개 변수를 포함 합니다. 

Azure Functions 워크플로의 경우 파일에는 다음과 같은 세 개의 섹션이 있습니다. 

| 섹션 | 작업 |
| ------- | ----- |
| **인증** | <ol><li>서비스 주체를 정의 합니다.</li><li>게시 프로필을 다운로드 합니다.</li><li>GitHub 비밀을 만듭니다.</li></ol>|
| **빌드** | <ol><li>환경을 설정 합니다.</li><li>함수 앱을 빌드합니다.</li></ol> |
| **배포** | <ol><li>함수 앱을 배포 합니다.</li></ol>|

> [!NOTE]
> 인증을 위해 게시 프로필을 사용 하기로 결정 한 경우에는 서비스 주체를 만들 필요가 없습니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](/cli/azure/)에서 [az ad sp create-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용 하 여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) 를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com) 를 사용 하거나 **사용해 보기** 단추를 선택 하 여이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

이 예제에서는 리소스의 자리 표시자를 구독 ID, 리소스 그룹 및 함수 앱 이름으로 바꿉니다. 출력은 함수 앱에 대한 액세스를 제공 하는 역할 할당 자격 증명입니다. GitHub에서 인증 하는 데 사용할 수 있는이 JSON 개체를 복사 합니다.

> [!IMPORTANT]
> 최소한의 액세스 권한을 부여 하는 것이 항상 좋은 방법입니다. 이전 예제의 범위가 전체 리소스 그룹이 아닌 특정 함수 앱으로 제한 되기 때문입니다.

## <a name="download-the-publishing-profile"></a>게시 프로필 다운로드

앱의 **개요** 페이지로 이동 하 고 **게시 프로필 가져오기**를 클릭 하 여 함수 앱의 게시 프로필을 다운로드할 수 있습니다.

   ![게시 프로필 다운로드](media/functions-how-to-github-actions/get-publish-profile.png)

파일의 내용을 복사 합니다.

## <a name="configure-the-github-secret"></a>GitHub 암호 구성

1. [GitHub](https://github.com)에서 리포지토리로 이동 하 고, **설정** > **비밀** > 선택 하 **여 새 비밀을 추가**합니다.

   ![비밀 추가](media/functions-how-to-github-actions/add-secret.png)

1. 새 비밀을 추가 합니다.

   * Azure CLI를 사용 하 여 만든 서비스 주체를 사용 하는 경우 **이름**에 `AZURE_CREDENTIALS`를 사용 합니다. 그런 다음 **값**에 대한 복사 된 JSON 개체 출력을 붙여넣고 **비밀 추가**를 선택 합니다.
   * 게시 프로필을 사용 하는 경우 **이름**에 `SCM_CREDENTIALS`를 사용 합니다. 그런 다음 **값**에 대한 게시 프로필의 파일 콘텐츠를 사용 하 고 **비밀 추가**를 선택 합니다.

이제 GitHub에서 Azure의 함수 앱에 인증할 수 있습니다.

## <a name="set-up-the-environment"></a>환경 설정 

환경 설정은 언어별 게시 설정 작업을 사용 하 여 수행 됩니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 `actions/setup-node` 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="pythontabpython"></a>[Python](#tab/python)

다음 예제에서는 `actions/setup-python` 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

다음 예제에서는 `actions/setup-dotnet` 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="javatabjava"></a>[Java](#tab/java)

다음 예제에서는 `actions/setup-java` 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다.

```yaml
    - name: 'Login via Azure CLI'
      uses: Azure/actions/login@master
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>함수 앱 빌드

이 섹션은 언어와 Azure Functions에서 지 원하는 언어에 따라 달라 집니다 .이 섹션은 각 언어의 표준 빌드 단계 여야 합니다.

다음 예제에서는 언어별 함수 앱을 빌드하는 워크플로의 일부를 보여 줍니다.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="javatabjava"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>함수 앱 배포

함수 앱에 코드를 배포 하려면 `Azure/functions-action` 작업을 사용 해야 합니다. 이 작업에는 두 개의 매개 변수가 있습니다.

|매개 변수 |설명  |
|---------|---------|
|**_앱 이름_** | 강제로 함수 앱의 이름입니다. |
|_**슬롯-이름**_ | 필드 배포 하려는 [배포 슬롯](functions-deployment-slots.md) 의 이름입니다. 슬롯은 함수 앱에 이미 정의 되어 있어야 합니다. |


다음 예에서는 `functions-action`버전 1을 사용 합니다.

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>다음 단계

전체 워크플로를 보려면 [Azure GitHub 작업 워크플로 샘플 리포지토리](https://aka.ms/functions-actions-samples) 에서 이름에 `functionapp` 있는 파일 중 하나를 참조 하세요. 이러한 샘플을 워크플로의 시작 지점으로 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub 동작에 대한 자세한 정보](https://help.github.com/en/articles/about-github-actions)
