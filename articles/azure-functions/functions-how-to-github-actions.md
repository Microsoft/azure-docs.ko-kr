---
title: GitHub 작업을 사용 하 여 Azure Functions에서 코드 업데이트 수행
description: Github 작업을 사용 하 여 GitHub에서 Azure Functions 프로젝트를 빌드하고 배포 하는 워크플로를 정의 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 02f5399e89900a438fb94f973c497a54dc05cfee
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210170"
---
# <a name="continuous-delivery-by-using-github-action"></a>GitHub 작업을 사용 하 여 지속적인 배달

[GitHub 작업](https://github.com/features/actions) 을 통해 Azure의 함수 앱에 함수 코드를 자동으로 빌드 및 배포 하는 워크플로를 정의할 수 있습니다. 

GitHub 작업에서 [워크플로](https://help.github.com/articles/about-github-actions#workflow) 는 github 리포지토리에서 정의 하는 자동화 된 프로세스입니다. 이 프로세스는 github에서 함수 앱 프로젝트를 빌드하고 배포 하는 방법을 GitHub에 알려 줍니다. 

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다. 

Azure Functions 워크플로의 경우 파일에는 다음과 같은 세 개의 섹션이 있습니다. 

| 섹션 | 작업 |
| ------- | ----- |
| **인증** | <ol><li>서비스 주체를 정의 합니다.</li><li>게시 프로필을 다운로드 합니다.</li><li>GitHub 비밀을 만듭니다.</li></ol>|
| **빌드** | <ol><li>환경을 설정 합니다.</li><li>함수 앱을 빌드합니다.</li></ol> |
| **배포** | <ol><li>함수 앱을 배포 합니다.</li></ol>|

> [!NOTE]
> 인증을 위해 게시 프로필을 사용 하기로 결정 한 경우에는 서비스 주체를 만들 필요가 없습니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com)을 사용하거나 **사용해 보세요** 단추를 선택하여 이 명령을 실행할 수 있습니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

이 예제에서는 리소스의 자리 표시자를 구독 ID, 리소스 그룹 및 함수 앱 이름으로 바꿉니다. 출력은 함수 앱에 대 한 액세스를 제공 하는 역할 할당 자격 증명입니다. GitHub에서 인증하는 데 사용할 수 있는 이 JSON 개체를 복사합니다.

> [!IMPORTANT]
> 항상 최소한의 액세스 권한을 부여하는 것이 좋습니다. 이전 예제의 범위가 전체 리소스 그룹이 아닌 특정 함수 앱으로 제한 되기 때문입니다.

## <a name="download-the-publishing-profile"></a>게시 프로필 다운로드

함수 앱의 게시 프로필을 다운로드 하려면 다음을 수행 합니다.

1. 함수 앱의 **개요** 페이지를 선택한 다음 **게시 프로필 가져오기**를 선택 합니다.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="게시 프로필 다운로드":::

1. 게시 설정 파일의 내용을 저장 하 고 복사 합니다.

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

1. [GitHub](https://github.com)에서 리포지토리로 이동 하 고 **설정**  >  **비밀**  >  **새 비밀 추가**를 선택 합니다.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="비밀 추가":::

1. 새 비밀을 추가 합니다.

   * Azure CLI를 사용 하 여 만든 서비스 주체를 사용 하는 경우 `AZURE_CREDENTIALS` **이름**에을 사용 합니다. 그런 다음 **값**에 대 한 복사 된 JSON 개체 출력을 붙여넣고 **비밀 추가**를 선택 합니다.
   * 게시 프로필을 사용 하는 경우 `SCM_CREDENTIALS` **이름**에을 사용 합니다. 그런 다음 **값**에 대 한 게시 프로필의 파일 콘텐츠를 사용 하 고 **비밀 추가**를 선택 합니다.

이제 GitHub에서 Azure의 함수 앱에 인증할 수 있습니다.

## <a name="set-up-the-environment"></a>환경 설정 

환경 설정은 언어별 게시 설정 작업을 사용 하 여 수행 됩니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예제에서는 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다 `actions/setup-node` .

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다 `actions/setup-python` .

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다 `actions/setup-dotnet` .

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 작업을 사용 하 여 환경을 설정 하는 워크플로의 일부를 보여 줍니다  `actions/setup-java` .

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="java"></a>[Java](#tab/java)

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

함수 앱에 코드를 배포 하려면 작업을 사용 해야 `Azure/functions-action` 합니다. 이 작업에는 두 개의 매개 변수가 있습니다.

|매개 변수 |설명  |
|---------|---------|
|**_앱 이름_** | 강제로 함수 앱의 이름입니다. |
|_**슬롯-이름**_ | 필드 배포 하려는 [배포 슬롯](functions-deployment-slots.md) 의 이름입니다. 슬롯은 함수 앱에 이미 정의 되어 있어야 합니다. |


다음 예에서는의 버전 1을 사용 합니다 `functions-action` .

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>다음 단계

전체 워크플로. yaml 파일을 보려면 이름에 있는 [Azure GitHub 작업 워크플로 샘플 리포지토리의](https://aka.ms/functions-actions-samples) 파일 중 하나를 참조 하세요. `functionapp` 이러한 샘플을 워크플로의 시작 지점으로 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [GitHub Actions에 대한 자세한 정보](https://help.github.com/en/articles/about-github-actions)
