---
title: Java Spring Boot 앱에서 Azure App Configuration Key Vault 참조를 사용하기 위한 자습서 | Microsoft Docs
description: 이 자습서에서는 Java Spring Boot 앱에서 Azure App Configuration의 Key Vault 참조를 사용하는 방법을 알아봅니다.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 5977aced8354694a631cce05bf6d6b913ea79118
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121598"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>자습서: Java Spring 앱에서 Key Vault 참조 사용

이 자습서에서는 Azure Key Vault와 함께 Azure App Configuration 서비스를 사용하는 방법을 알아봅니다. App Configuration 및 Key Vault는 대부분의 애플리케이션 배포에서 함께 사용되는 보완 서비스입니다.

App Configuration을 사용하면 Key Vault에 저장된 값을 참조하는 키를 만들어 이러한 서비스를 함께 사용할 수 있습니다. App Configuration은 이러한 키를 만들 때 값 자체가 아니라 Key Vault 값의 URI를 저장합니다.

애플리케이션은 App Configuration에 저장된 다른 키와 마찬가지로 App Configuration 클라이언트 공급자를 사용하여 Key Vault 참조를 검색합니다. 이 경우 App Configuration에 저장된 값은 Key Vault의 값을 참조하는 URI입니다. Key Vault 값 또는 자격 증명이 아닙니다. 클라이언트 공급자는 이 키를 Key Vault 참조로 인식하므로 Key Vault를 사용하여 해당 값을 검색합니다.

애플리케이션은 App Configuration 및 Key Vault 모두에 대해 제대로 인증할 책임이 있습니다. 두 서비스는 직접 통신하지 않습니다.

이 자습서에서는 코드로 Key Vault 참조를 구현하는 방법을 보여 줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 Java Spring 앱 만들기](./quickstart-java-spring-app.md)를 완료하세요.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. 예를 들어 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 운영 체제에서 사용할 수 있는 크로스 플랫폼 코드 편집기입니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Key Vault에 저장된 값을 참조하는 App Configuration 키 만들기
> * Java Spring 애플리케이션에서 이 키 값에 액세스합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* 버전 8 이상이 설치된 지원되는 [JDK(Java Development Kit)](https://docs.microsoft.com/java/azure/jdk)
* [Apache Maven](https://maven.apache.org/download.cgi) 버전 3.0 이상

## <a name="create-a-vault"></a>자격 증명 모음 만들기

1. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기** 옵션을 선택합니다.

    ![Key Vault 만들기가 완료된 후 출력](./media/quickstarts/search-services.png)
1. 검색 상자에 **Key Vault**를 입력합니다.
1. 결과 목록의 왼쪽에서 **Key Vault**를 선택합니다.
1. **Key Vault**에서 **추가**를 선택합니다.
1. 오른쪽에 있는 **Key Vault 만들기**에서 다음 정보를 제공합니다.
    * **구독**을 선택하여 구독을 선택합니다.
    * **리소스 그룹**에서 **새로 만들기**를 선택하고 리소스 그룹 이름을 입력합니다.
    * **Key Vault 이름**에는 고유한 이름이 필요합니다. 이 자습서에서는 **Contoso-vault2**를 입력합니다.
    * **지역** 드롭다운 목록에서 위치를 선택합니다.
1. 다른 **Key Vault 만들기** 옵션은 기본값 그대로 둡니다.
1. **만들기**를 선택합니다.

이때 사용자의 Azure 계정은 이 새 자격 증명 모음에 액세스할 권한이 있는 유일한 계정입니다.

![Key Vault 만들기가 완료된 후 출력](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Key Vault에 비밀 추가

자격 증명 모음에 비밀을 추가하려면 몇 가지 추가 단계를 수행해야 합니다. 이 경우 Key Vault 검색을 테스트하는 데 사용할 수 있는 메시지를 추가합니다. 메시지는 **Message**라고 하며 여기에 "Hello from Key Vault" 값을 저장합니다.

1. Key Vault 속성 페이지에서 **비밀**을 선택합니다.
1. **생성/가져오기**를 선택합니다.
1. **비밀 만들기** 창에서 다음 값을 입력합니다.
    * **업로드 옵션**: **수동**을 입력합니다.
    * **Name**: **Message**를 입력합니다.
    * **값**: **Hello from Key Vault**를 입력합니다.
1. 다른 **비밀 만들기** 속성은 기본값 그대로 둡니다.
1. **만들기**를 선택합니다.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration에 Key Vault 참조 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택한 다음, 빠른 시작에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기**를 선택합니다.

1. **+ 만들기** > **Key Vault 참조**를 선택하고 다음 값을 지정합니다.
    * **키**: **/application/config.keyvaultmessage**를 선택합니다.
    * **레이블**: 이 값은 빈 상태로 둡니다.
    * **구독**, **리소스 그룹** 및 **Key Vault**: 이전 섹션에서 만든 Key Vault의 값에 해당하는 값을 입력합니다.
    * **비밀**: 이전 섹션에서 만든 **Message**라는 비밀을 선택합니다.

## <a name="connect-to-key-vault"></a>Key Vault에 연결

1. 이 자습서에서는 Key Vault에 대한 인증에 서비스 주체를 사용합니다. 서비스 주체를 만들려면 Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) 명령을 사용합니다.

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    이 작업을 수행하면 일련의 키/값 쌍이 반환됩니다.

    ```json
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. 다음 명령을 실행하여 서비스 주체가 키 자격 증명 모음에 액세스하도록 허용합니다.

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. 다음 명령을 실행하여 개체 ID를 가져온 다음, App Configuration에 추가합니다.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. 환경 변수 **AZURE_CLIENT_ID**, **AZURE_CLIENT_SECRET** 및 **AZURE_TENANT_ID**를 만듭니다. 이전 단계에서 표시된 서비스 주체에 대한 값을 사용합니다. 명령줄에서 다음 명령을 실행하고 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Key Vault 자격 증명은 애플리케이션 내에서만 사용됩니다.  애플리케이션은 App Configuration 서비스를 제외하고 이러한 자격 증명을 사용하여 Key Vault에서 직접 인증합니다.  Key Vault는 키를 공유하거나 노출하지 않고 애플리케이션 및 App Configuration 서비스 모두에 대한 인증을 제공합니다.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Key Vault 참조를 사용하도록 코드 업데이트

1. **APP_CONFIGURATION_ENDPOINT**라는 환경 변수를 만듭니다. 해당 값을 App Configuration 저장소의 엔드포인트로 설정합니다. Azure Portal의 **액세스 키** 블레이드에서 엔드포인트를 찾을 수 있습니다. 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다. 


1. *리소스* 폴더에서 *bootstrap.properties*를 엽니다. 이 파일을 업데이트하여 **APP_CONFIGURATION_ENDPOINT** 값을 사용합니다. 이 파일에서 연결 문자열에 대한 참조를 제거합니다. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

1. *MessageProperties.java*를 엽니다. 다음과 같이 *keyVaultMessage*라는 새 변수를 추가합니다.

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. *HelloController.java*를 엽니다. *getMessage* 메서드를 업데이트하여 Key Vault에서 검색한 메시지를 포함시킵니다.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. *AzureCredentials.java*라는 새 파일을 만들고 아래 코드를 추가합니다.

    ```java
    package com.example.demo;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. *AppConfiguration.java*라는 새 파일을 만듭니다. 아래 코드를 추가합니다.

    ```java
    package com.example.demo;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. 리소스 META-INF 디렉터리에서 *spring.factories*라는 새 파일을 만들고 아래 코드를 추가합니다.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Maven을 사용하여 Spring Boot 애플리케이션을 빌드하고 실행합니다. 예를 들어 다음과 같습니다.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. 애플리케이션이 실행되면 *curl*을 사용하여 애플리케이션을 테스트합니다. 예를 들어 다음과 같습니다.

      ```shell
      curl -X GET http://localhost:8080/
      ```

    App Configuration 저장소에 입력한 메시지가 표시됩니다. Key Vault에서 입력한 메시지도 표시됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Key Vault에 저장된 값을 참조하는 App Configuration 키를 만들었습니다. Java Spring 애플리케이션에서 기능 플래그를 사용하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./quickstart-feature-flag-spring-boot.md)
