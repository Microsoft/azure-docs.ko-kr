---
title: 템플릿을 사용 하 여 앱 배포
description: App Service 앱을 프로 비전 하 고 배포 하는 Azure Resource Manager 템플릿을 만드는 방법에 대 한 지침을 찾습니다.
author: tfitzmac
ms.topic: article
ms.date: 01/03/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: dfdfa9f69e00aa644c21fc96cb70e9fa460ca0c1
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211709"
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 웹앱을 배포하는 방법에 대한 지침

이 아티클에서는 Azure App Service 솔루션을 배포하는 Azure Resource Manager 템플릿을 만드는 방법에 대한 권장 사항을 제공합니다. 이러한 권장 사항은 일반적인 문제를 피하는 데 도움이 됩니다.

## <a name="define-dependencies"></a>종속성 정의

웹앱의 종속성을 정의하려면 웹앱 내에서 리소스가 상호 작용하는 원리를 이해해야 합니다. 잘못된 순서로 종속성을 지정하면 배포 오류가 발생하거나 배포가 중지되는 경합 상태가 생성될 수 있습니다.

> [!WARNING]
> 템플릿에 MSDeploy 사이트 확장을 포함하는 경우 구성 리소스가 MSDeploy 리소스에 종속되는 것으로 설정해야 합니다. 구성을 변경하면 사이트가 비동기적으로 다시 시작됩니다. 구성 리소스가 MSDeploy에 종속되도록 설정하면 사이트가 다시 시작되기 전에 MSDeploy가 완료됩니다. 이러한 종속성이 없으면 MSDeploy의 배포 프로세스 도중에 사이트가 다시 시작될 수 있습니다. 예제 템플릿을 보려면 [웹 배포 종속성이 포함된 WordPress 템플릿](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)을 참조하세요.

다음 이미지는 다양한 App Service 리소스의 종속성 순서를 보여줍니다.

![웹앱 종속성](media/web-sites-rm-template-guidance/web-dependencies.png)

다음 순서로 리소스를 배포합니다.

**계층 1**
* App Service 계획
* 데이터베이스 또는 스토리지 계정 등의 다른 관련 리소스

**계층 2**
* 웹앱--App Service 계획에 종속됩니다.
* 서비스 팜을 대상으로 하는 Azure Application Insights 인스턴스--App Service 계획에 종속됩니다.

**계층 3**
* 소스 제어--웹앱에 종속됩니다.
* MSDeploy 사이트 확장--웹앱에 종속됩니다.
* 웹 앱을 대상으로 하는 Azure 애플리케이션 Insights 인스턴스-웹 앱에 종속 됩니다.

**계층 4**
* App Service 인증서--있는 경우 소스 제어 또는 MSDeploy에 종속됩니다. 그렇지 않으면, 웹앱에 종속됩니다.
* 구성 설정(연결 문자열, 웹 구성 값, 앱 설정)--있는 경우 소스 제어 또는 MSDeploy에 종속됩니다. 그렇지 않으면, 웹앱에 종속됩니다.

**계층 5**
* 호스트 이름 바인딩--있는 경우 인증서에 종속됩니다. 그렇지 않으면, 상위 수준 리소스에 종속됩니다.
* 사이트 확장 - 있는 경우 구성 설정에 종속됩니다. 그렇지 않으면, 상위 수준 리소스에 종속됩니다.

일반적으로 솔루션에는 이러한 리소스와 계층 중 일부만 포함됩니다. 누락된 계층의 경우 하위 리소스를 그 다음 상위 계층에 매핑합니다.

다음 예제는 템플릿의 일부를 보여줍니다. 연결 문자열 구성 값은 MSDeploy 확장에 종속됩니다. MSDeploy 확장은 웹앱 및 데이터베이스에 따라 달라집니다. 

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

위의 코드를 사용하는 즉시 실행 가능한 샘플은 [템플릿: 간단한 Umbraco 웹앱 빌드](https://github.com/Azure/azure-quickstart-templates/tree/master/umbraco-webapp-simple)를 참조하세요.

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy 오류에 대한 정보 찾기

Resource Manager 템플릿에서 MSDeploy를 사용하는 경우 배포 오류 메시지를 이해하기 어려울 수 있습니다. 배포 실패 후 자세한 정보를 보려면 다음 단계를 시도해 보세요.

1. 사이트의 [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console)로 이동합니다.
2. D:\home\LogFiles\SiteExtensions\MSDeploy에서 폴더로 이동합니다.
3. appManagerStatus.xml 및 appManagerLog.xml 파일을 찾습니다. 첫 번째 파일은 상태를 기록합니다. 두 번째 파일은 오류에 대한 정보를 기록합니다. 오류가 명확 하지 않은 경우 [포럼](https://docs.microsoft.com/answers/topics/azure-webapps.html)에서 도움을 요청 하는 경우이를 포함할 수 있습니다.

## <a name="choose-a-unique-web-app-name"></a>고유한 웹앱 이름을 선택합니다.

웹앱 이름은 전역적으로 고유해야 합니다. 고유한 명명 규칙을 사용할 수도 있고, [uniqueString 함수](../azure-resource-manager/templates/template-functions-string.md#uniquestring)를 사용하여 고유 이름 생성을 지원할 수도 있습니다.

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="deploy-web-app-certificate-from-key-vault"></a>Key Vault에서 웹앱 인증서 배포

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

템플릿에 SSL 바인딩에 대한 [Microsoft.Web/certificates](/azure/templates/microsoft.web/certificates) 리소스가 포함되어 있고 인증서가 Key Vault에 저장되어 있는 경우, App Service ID가 인증서에 액세스할 수 있는지 확인해야 합니다.

전역 Azure에서 App Service 서비스 주체의 ID는 **abfa0a7c-a6b6-4736-8310-5855508787cd**입니다. App Service 서비스 주체에 Key Vault에 대한 액세스를 허용하려면 다음을 사용합니다.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
  -VaultName KEY_VAULT_NAME `
  -ServicePrincipalName abfa0a7c-a6b6-4736-8310-5855508787cd `
  -PermissionsToSecrets get `
  -PermissionsToCertificates get
```

Azure Government에서 App Service 서비스 주체의 ID는 **6a02c803-dafd-4136-b4c3-5a6f318b4714**입니다. 앞의 예제에서 해당 ID를 사용합니다.

Key Vault에서 **인증서** 및 **생성/가져오기**를 선택하여 인증서를 업로드합니다.

![인증서 가져오기](media/web-sites-rm-template-guidance/import-certificate.png)

템플릿에서 `keyVaultSecretName`에 대한 인증서의 이름을 입력합니다.

예제 템플릿을 보려면 [Deploy a Web App certificate from Key Vault secret and use it for creating SSL binding](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-certificate-from-key-vault)(Key Vault 비밀에서 웹앱 인증서 배포 및 SSL 바인딩 생성에 사용)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 템플릿으로 웹앱을 배포하는 방법에 대한 자습서를 보려면 [Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포](deploy-complex-application-predictably.md)를 참조하세요.
* 템플릿의 JSON 구문 및 리소스 종류의 속성에 대해 알아보려면 [Azure Resource Manager 템플릿 참조](/azure/templates/)를 확인하세요.
