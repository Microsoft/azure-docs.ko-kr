---
title: 마이크로서비스를 예측 가능하게 프로비전 및 배포 - Azure App Service
description: PowerShell 스크립팅과 JSON 리소스 그룹을 사용한 예측 가능한 방법으로 Azure App Service 내에서 마이크로 서비스로 구성된 애플리케이션을 배포하는 방법을 배워봅시다.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6d18222e15f62f12592362827b6dbc4a3d7dfbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766922"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Azure에서 마이크로 서비스를 예측 가능하게 프로비전 및 배포
이 자습서에서는 PowerShell 스크립팅과 JSON 리소스 그룹을 사용한 예측 가능한 방법으로 [Azure App Service](https://azure.microsoft.com/services/app-service/) 내에서 [마이크로 서비스](https://en.wikipedia.org/wiki/Microservices)로 구성된 애플리케이션의 프로비전 및 배포하는 방법을 보여줍니다. 

고도로 분리된 마이크로 서비스로 구성된 고확장성 애플리케이션을 프로비전 및 배포할 때 반복성과 예측 가능성이 관건입니다. [Azure App Service](https://azure.microsoft.com/services/app-service/)를 사용하면 웹앱, 모바일 백 엔드 및 API 앱을 포함한 마이크로 서비스를 만들 수 있습니다. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)는 데이터베이스와 소스 제어 설정과 같은 리소스 종속성과 함께 모든 마이크로 서비스를 하나의 유닛으로 관리할 수 있도록 해줍니다. 이제 JSON 템플릿과 간단한 PowerShell 스크립팅을 사용하여 이러한 애플리케이션을 배포할 수 있습니다. 

## <a name="what-you-will-do"></a>수행할 사항
자습서에서는 포함된 애플리케이션을 배포합니다.

* 두 가지 App Service 앱(즉, 두 개의 마이크로 서비스)
* 백 엔드 SQL Database
* 앱 설정, 연결 문자열 및 소스 제어
* Application insights, 경고, 자동 크기 조정 설정

## <a name="tools-you-will-use"></a>사용할 도구
이 자습서에서는 다음 도구를 사용합니다. 도구에 대한 포괄적인 설명이 아니기 때문에 종단간 시나리오를 그대로 유지하고 각각에 대해 간단히 소개하고 이에 대한 자세한 정보를 찾을 수 있는 곳을 알려드립니다. 

### <a name="azure-resource-manager-templates-json"></a>Azure 리소스 관리자 템플릿(JSON)
예를 들어 Azure App Service에서 앱을 만들 때마다 Azure Resource Manager는 구성 요소 리소스와 함께 전체 리소스 그룹을 만들기 위해 JSON 템플릿을 사용합니다. [Azure Marketplace](/azure/marketplace)의 복잡한 템플릿은 데이터베이스, 스토리지 계정, App Service 계획, 앱 자체, 경고 규칙, 앱 설정, 자동 크기 조정 설정 등을 포함할 수 있으며 이러한 템플릿은 PowerShell을 통해 제공됩니다. Azure 리소스 관리자 템플릿에 대한 자세한 내용은 [Azure 리소스 관리자 템플릿 작성하기](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Visual Studio용 Azure SDK 2.6
최신 SDK는 JSON 편집기에서 리소스 관리자 템플릿 지원에 향상된 기능을 포함합니다. 이것을 사용하여 신속하게 리소스 그룹 템플릿을 처음부터 만들고, 수정을 위한 기존 JSON 템플릿(예: 다운로드한 갤러리 템플릿)을 열고, 매개 변수 파일을 채우고, Azure 리소스 그룹 솔루션에서 직접 리소스 그룹을 배포할 수 있습니다.

자세한 내용은 [Visual Studio용 Azure SDK 2.6](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/)을 참조하세요.

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 또는 이후
Azure PowerShell 설치는 버전 0.8.0부터 Azure 모듈 외에도 Azure 리소스 관리자 모듈을 포함합니다. 이 새 모듈을 사용하면 리소스 그룹의 배포를 스크립트할 수 있습니다.

자세한 내용은 [Azure 리소스 관리자로 Azure PowerShell 사용](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure 리소스 탐색기
이 [미리 보기 도구](https://resources.azure.com)를 사용하면 구독 및 개별 리소스에서 모든 리소스 그룹의 JSON 정의를 탐색할 수 있습니다. 도구에서 리소스의 JSON 정의를 편집하고 리소스의 전체 계층을 삭제하며 새 리소스를 만들 수 있습니다.  이 도구에서 현재 사용 가능한 정보는 리소스, 올바른 값 등 특정 형식에 대해 설정해야 하는 속성을 보여주기 때문에 템플릿 작성에 매우 유용합니다. [Azure Portal](https://portal.azure.com/)에서 리소스 그룹을 만들 수도 있고, 그런 다음 리소스 그룹을 템플릿화할 수 있도록 탐색기 도구에서 JSON 정의를 검사합니다.

### <a name="deploy-to-azure-button"></a>Azure 단추에 배포
소스 제어용 GitHub를 사용하는 경우 [Azure 단추에 배포](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) 를 README.MD에 배치하여 턴키 배포 UI를 Azure에 사용합니다. 간단한 모든 앱에 이 작업을 수행할 수 있지만, azuredeploy.json 파일을 리포지토리 루트에 배치하면 전체 리소스 그룹을 배포하도록 이를 확장할 수 있습니다. 리소스 그룹을 만들려면 Azure 단추에 배포하 여 리소스 그룹 템플릿을 포함하는 이 JSON 파일을 사용합니다. 예를 들어 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 샘플을 참조하여 이 자습서에서 사용합니다.

## <a name="get-the-sample-resource-group-template"></a>샘플 리소스 그룹 템플릿 가져오기
이제 바로 살펴보겠습니다.

1. [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) App Service 샘플을 탐색합니다.
2. Readme.md에서 **Azure에 배포**를 클릭합니다.
3. [azure 배포](https://deploy.azure.com) 사이트로 이동하면 배포 매개 변수를 입력하라는 메시지가 표시됩니다. 대부분의 필드가 저장소 이름 및 일부 임의 문자열로 채워져 있는지 확인합니다. 원하는 모든 필드를 변경할 수 있지만 유일하게 입력해야 할 것은 SQL Server 관리 로그인 및 암호이며 그런 다음 **다음**을 클릭합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. 그런 다음 배포 프로세스를 시작하려면 **배포** 를 클릭합니다. 프로세스가 완료되면 배포된 애플리케이션을 탐색하기 위해 http://todoapp*XXXX*.azurewebsites.net 링크를 클릭합니다. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   UI는 앱이 이제 시작하기 때문에 먼저 탐색할 때 약간 느릴 수 있지만 완벽하게 작동하는 애플리케이션임을 직접 확인하세요.
5. 배포 페이지로 돌아와서 Azure Portal에서 새 애플리케이션을 보려면 **관리** 링크를 클릭합니다.
6. **필수** 드롭다운에서 리소스 그룹 링크를 클릭합니다. 앱이 GitHub 리포지토리의 **외부 프로젝트** 아래에 이미 연결되었습니다. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. 리소스 그룹 블레이드에서 리소스 그룹에 앱 두 개와 SQL Database 한 개가 이미 있는 것을 확인합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Azure 리소스 관리자의 자동화된 오케스트레이션이 설정한 모든 구성 요소, 종속성, 설정, 데이터베이스 및 지속적인 게시와 함께 완벽하게 배포된 두 가지 마이크로 서비스 애플리케이션에 대해 몇 분 동안 보았습니다. 이 모든 작업은 두 가지로 수행되었습니다.

* Azure 단추에 배포
* 리포지토리 루트에 azuredeploy.json

이 동일한 애플리케이션을 수십, 수백 또는 수천 번 배포할 수 있으며 매번 정확히 동일한 구성을 유지할 수 있습니다. 이 방법의 예측 가능성과 반복성으로 인해 쉽고 자신있게 대규모 애플리케이션을 배포할 수 있습니다.

## <a name="examine-or-edit-azuredeployjson"></a>AZUREDEPLOY.JSON 검사(또는 편집)
이제를 GitHub 리포지토리를 설정하는 방법에 대해 살펴보겠습니다. Azure.NET SDK에서 JSON 편집기를 사용할 수 있으므로 [Azure.NET SDK 2.6](https://azure.microsoft.com/downloads/)를 아직 설치하지 않았다면 지금 설치하십시오.

1. 즐겨 찾는 git 도구를 사용하여 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) 리포지토리를 복제합니다. 아래 스크린샷은 Visual Studio 2013의 팀 탐색기에서 이것을 보여줍니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. 리포지토리 루트로부터 Visual Studio에서 azuredeploy.json을 엽니다. JSON 개요 창이 보이지 않으면 Azure.NET SDK를 설치해야 합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

JSON 형식의 모든 세부 정보를 설명하지 않겠지만 [더 리소스](#resources) 섹션에 리소스 그룹 템플릿 언어 학습을 위한 링크가 있습니다. 여기에서 앱 배포에 대한 고유의 사용자 지정 템플릿을 만들기 시작하는데 도움이 되는 흥미로운 기능을 보여 주겠습니다.

### <a name="parameters"></a>매개 변수
이러한 대부분의 매개 변수가 **Azure에 배포** 단추로 입력하려는 것임을 확인하려면 매개 변수 섹션을 살펴보세요. **Azure에 배포** 버튼 뒤에 사이트는 azuredeploy.json에 정의된 매개 변수를 사용하는 입력 UI를 채웁니다. 이러한 매개 변수는 리소스 이름, 속성 값 등의 리소스 정의 전체에서 사용됩니다.

### <a name="resources"></a>리소스
리소스 노드에는 SQL Server 인스턴스, App Service 계획 및 앱 두 개를 포함하는 4개의 최상위 리소스가 정의된 것을 확인할 수 있습니다. 

#### <a name="app-service-plan"></a>App Service 계획
JSON에서 간단한 루트 수준 리소스부터 살펴보겠습니다. JSON 개요에서 해당하는 JSON 코드에 강조를 표시하기 위해 **[hostingPlanName]** 라고 명명된 App Service 계획을 클릭합니다. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

`type` 요소는 (오래 전 서버 팜을 호출했을) App Service 계획에 대한 문자열을 지정하고 JSON 파일에 정의된 매개 변수를 사용하여 다른 요소 및 속성을 채웁니다. 또한 이 리소스에는 모든 중첩된 리소스가 없습니다.

> [!NOTE]
> 또한 `apiVersion`의 값이 Azure에 JSON 리소스 정의를 사용할 REST API 버전을 알려주고 `{}` 내부에 형식을 지정하는 데 영향을 줄 수 있습니다. 
> 
> 

#### <a name="sql-server"></a>SQL Server
다음 JSON 개요에서 **SQLServer** 로 명명된 SQL Server 리소스를 클릭합니다.

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

강조 표시된 JSON 코드에 대해 다음에서 확인합니다.

* 매개 변수를 사용하면 만들어진 리소스는 다른 리소스와 일치하는 방식으로 확실하게 명명되고 구성됩니다.
* SQLServer 리소스에는 `type`에 각각 다른 값을 가진 두 중첩된 리소스가 있습니다.
* 데이터베이스 및 방화벽 규칙이 정의된 `“resources”: […]` 내부의 중첩된 리소스가 루트 수준의 SQLServer 리소스의 리소스 ID를 지정 하는 `dependsOn` 요소입니다. 이는 Azure 리소스 관리자에게 다음과 같은 사실을 알려줍니다. "이 리소스를 만들기 전에 다른 리소스가 이미 존재해야 합니다. 그 다른 리소스가 템플릿에 정의된 경우 그 하나를 먼저 만듭니다".
  
  > [!NOTE]
  > `resourceId()` 함수를 사용하는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿 함수](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid)를 참조하세요.
  > 
  > 
* `dependsOn` 요소의 효과로 Azure 리소스 관리자가 어떤 리소스를 동시에 만들 수 있고 어떤 리소스를 순차적으로 만들어야 하는지 알 수 있습니다. 

#### <a name="app-service-app"></a>App Service 앱
이제 더 복잡한 실제 앱 자체에 대해 알아보겠습니다. JSON 개요에서 [variables('apiSiteName')] 앱을 클릭하여 해당 JSON 코드를 강조 표시합니다. 상황이 훨씬 더 흥미로워집니다. 이런 목적으로 하나씩 기능에 대해 말하겠습니다.

##### <a name="root-resource"></a>루트 리소스
앱은 두 가지 리소스에 따라 달라집니다. 즉, Azure Resource Manager는 App Service 계획과 SQL Server 인스턴스가 둘 다 생성된 후에만 앱을 만듭니다.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>앱 설정
앱 설정은 중첩된 리소스로 정의됩니다.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

`config/appsettings`를 위한 `properties` 요소에서 `"<name>" : "<value>"` 형식에 두 개의 앱 설정이 있습니다.

* `PROJECT` 은 다중 프로젝트 Visual Studio 솔루션에서 어떤 프로젝트를 사용할지를 Azure 배포를 지시하는 [KUDU 설정](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) 입니다. 소스 제어가 어떻게 구성되는지 나중에 설명하겠지만 ToDoApp 코드가 다중 프로젝트 Visual Studio 솔루션에 존재하므로 이 설정이 필요합니다.
* `clientUrl` 은 애플리케이션 코드가 사용할 단순한 앱 설정입니다.

##### <a name="connection-strings"></a>연결 문자열
연결 문자열은 중첩된 리소스로 정의됩니다.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

`config/connectionstrings`를 위한 `properties` 요소에서 각 연결 문자열은 `"<name>" : {"value": "…", "type": "…"}`이라는 특정 형식을 가직 이름: 값 쌍으로 정의됩니다. `type` 요소에 대해 가능한 값은 `MySql`, `SQLServer`, `SQLAzure`, 및 `Custom`입니다.

> [!TIP]
> 연결 문자열 형식의 선언적 목록의 경우 Azure PowerShell에서 다음 명령을 실행합니다. \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>소스 제어
소스 제어 설정은 중첩된 리소스로 정의됩니다. JSON 파일을 처리하는 동안 Azure 리소스 관리자가 연속 게시를 구성하고(나중에 `IsManualIntegration`에서 caveat 참조) 자동으로 애플리케이션 코드의 배포를 시작하려면 이 리소스를 사용합니다.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` 및 `branch`는 매우 직관적이어야 하며 Git 리포지토리와 게시하려는 분기의 이름을 가리켜야 합니다. 다시, 입력 매개 변수에 의해 정의됩니다. 

`dependsOn` 요소에서 앱 리소스 자체 외에도 `sourcecontrols/web`은 `config/appsettings` 및 `config/connectionstrings`에 따라 달라집니다. 일단 `sourcecontrols/web` 가 구성되기 때문에 Azure 배포 프로세스는 애플리케이션 코드를 자동으로 배포, 빌드 및 시작하려 합니다. 따라서 이 종속성을 삽입하면 애플리케이션 코드를 실행하기 전에 애플리케이션이 필요한 앱 설정 및 연결 문자열에 액세스할 수 있는지 확인할 수 있습니다. 

> [!NOTE]
> 또한 `IsManualIntegration`은 `true`로 설정됩니다. 실제로 GitHub 리포지토리를 소유하지 않기 때문에 [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp)에서 연속 게시를 구성할 권한을 Azure에 실제로 부여할 수 없기 때문에 이 자습서에서는 이 속성이 필요합니다(즉, Azure에 자동 저장소 업데이트 밀어넣기). 이전에 [Azure portal](https://portal.azure.com/)에서 소유자의 GitHub 자격 증명을 사용하도록 구성한 경우에만 지정된 리포지토리에 기본값 `false`을 사용할 수 있습니다. 즉, 이전에 [Azure Portal](https://portal.azure.com/)에서 사용자 자격 증명을 사용하여 모든 앱용 GitHub 또는 BitBucket에 대한 원본 제어를 설정하는 경우 Azure에서는 해당 자격 증명을 기억하고 이후에 GitHub 또는 BitBucket에서 모든 앱을 배포할 때마다 사용합니다. 그러나 이 작업을 미리 수행하지 않은 경우 Azure Resource Manager가 리포지토리 소유자의 자격 증명으로 GitHub 또는 BitBucket에 로그인할 수 없기 때문에 앱의 소스 제어 설정을 구성할 때 JSON 템플릿 배포에 실패합니다.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>배포된 리소스 그룹과 JSON 템플릿 비교
[Azure Portal](https://portal.azure.com/)에서 앱의 모든 블레이드를 이용할 수 있지만, 더 이상은 아니더라도 그만큼 유용한 다른 도구도 있습니다. [Azure 리소스 탐색기](https://resources.azure.com) 미리 보기 도구로 이동하면 구독하는 모든 리소스 그룹이 Azure 백엔드에 실제로 존재하기에 그 JSON 표현을 제공합니다. Azure에서 리소스 그룹의 JSON 계층이 어떻게 그것을 만드는데 사용되는 템플릿 파일의 계층 구조와 일치하는지를 볼 수 있습니다.

예를 들어 [Azure 리소스 탐색기](https://resources.azure.com) 도구로 이동하고 탐색기에서 노드를 확장하는 경우 해당 리소스 형식에서 수집되는 리소스 그룹 및 루트 수준 리소스를 볼 수 있습니다.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

앱을 드릴다운하면 아래 스크린샷과 비슷한 앱 구성 정보를 볼 수 있습니다.

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

다시, 중첩된 리소스는 JSON 템플릿 파일의 리소스와 계층 구조가 매우 유사해야 하고 JSON 창에 제대로 적용된 앱 설정, 연결 문자열 등을 볼 수 있어야 합니다. 여기에 설정이 없는 경우 JSON 파일에 문제가 있을 수 있고 JSON 템플릿 파일의 문제를 해결하도록 돕습니다.

## <a name="deploy-the-resource-group-template-yourself"></a>리소스 그룹 템플릿을 직접 배포
**Azure에 배포** 단추는 유용하지만 GitHub에 azuredeploy.json를 이미 푸시한 경우에만 azuredeploy.json의 리소스 그룹 템플릿을 배포할 수 있습니다. 또한 Azure.NET SDK는 로컬 컴퓨터에서 직접 모든 JSON 템플릿 파일을 배포하는 도구를 제공 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
2. **Visual C#** > **클라우드** > **Azure 리소스 그룹**을 클릭한 후에 **확인**을 클릭합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. **Azure 템플릿 선택**에서 **빈 템플릿**을 선택하고 **확인**을 클릭합니다.
4. Azuredeploy.json을 새 프로젝트의 **템플릿** 폴더로 끌어옵니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. 솔루션 탐색기에서 복사한 azuredeploy.json를 엽니다.
6. 여기서는 설명을 위해 **리소스 추가**를 클릭하여 일부 표준 Application Insight 리소스를 JSON 파일에 추가해 보겠습니다. JSON 파일 배포에 관심이 있다면 배포 단계를 건너뜁니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. **Web Apps용 Application Insights**를 선택한 다음, 기존 App Service 계획 및 앱을 선택했는지 확인하고 **추가**를 클릭합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   이제 리소스 및 그 기능에 따라 App Service 계획 또는 앱에 종속된 여러 새 리소스를 볼 수 있습니다. 이러한 리소스는 기존의 정의를 사용할 수 없고 변경해야 합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. JSON 개요에서 JSON 코드에 강조를 표시하려면 **appInsights 자동 크기 조정** 을 클릭합니다. App Service 계획용 크기 조정 설정입니다.
9. 강조 표시된 JSON 코드에서 `location` 및 `enabled` 속성을 찾아 아래와 같이 설정합니다.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. JSON 개요에서 JSON 코드에 강조를 표시하려면 **CPUHigh appInsights** 을 클릭합니다. 이것은 경고입니다.
11. `location` 및 `isEnabled` 속성을 찾아 아래와 같이 설정합니다. 다른 3 개의 경고(자주색 전구)에 대해 동일한 작업을 수행합니다.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. 배포할 준비가 되었습니다. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **배포** > **New 배포ment**을 참조하십시오.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. 아직 수행하지 않은 경우 Azure 계정에 로그인합니다.
14. 구독에서 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들고 **azuredeploy.json**을 클릭한 다음 **매개 변수 편집**을 클릭합니다.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    이제 좋은 테이블에서 템플릿 파일에 정의된 모든 매개 변수를 편집할 수 있습니다. 기본값을 정의하는 매개 변수는 기본값이 이미 있고 허용된 값의 목록을 정의하는 매개 변수는 드롭다운으로 표시됩니다.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. 모든 빈 매개 변수를 입력하고 [repoUrl](https://github.com/azure-appservice-samples/ToDoApp.git) 에서 **ToDoApp용 GitHub 리포지토리 주소**를 사용합니다. 그런 다음 **저장**을 클릭합니다.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > 자동 크기 조정은 **표준** 계층 이상에서 제공되는 기능이며 계획 수준 경고는 **기본** 계층 이상에서 제공되는 기능입니다. 모든 새 App Insights 리소스를 켜서 확인하기 위해서는 **sku** 매개 변수를 **표준** 또는 **프리미엄**으로 설정해야 합니다.
    > 
    > 
16. **배포**을 참조하십시오. **암호 저장**을 선택한 경우, 암호가 **일반 텍스트에서** 매개 변수 파일에 저장됩니다. 그렇지 않은 경우 배포 프로세스 중에 데이터베이스 암호를 입력하라는 메시지가 표시됩니다.

끝났습니다. 이제 애플리케이션에 배포된 JSON에 추가된 도구를 새 경고 및 자동 크기 조정 설정을 보기 위해 [Azure Portal](https://portal.azure.com/) 및 [Azure Resource Explorer](https://resources.azure.com) 도구로 이동해야 합니다.

이 섹션의 단계에서 주로 다음을 수행합니다.

1. 템플릿 파일 만들기
2. 템플릿 파일에 맞는 매개 변수 파일 생성
3. 매개 변수 파일로 템플릿 파일 배포

마지막 단계는 PowerShell cmdlet이 쉽게 수행합니다. 애플리케이션을 배포하는 경우 Visual Studio의 역할을 참고하려면 Scripts\Deploy AzureResourceGroup.ps1를 엽니다. 여기에 코드가 많지만 매개 변수 파일로 템플릿 파일을 배포하기 위해 필요한 모든 관련 코드에 강조를 표시하겠습니다.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

마지막 cmdlet인 `New-AzureResourceGroup`은 실제로 작업을 수행합니다. 이로써 도구를 통해 예측 가능한 방식으로 클라우드 애플리케이션을 배포하는 것을 비교적 간단하게 시연해야 합니다. cmdlet를 실행할 때마다 동일한 템플릿에서 동일한 매개 변수 파일로 동일한 결과를 얻어야 합니다.

## <a name="summary"></a>요약
DevOps에서 반복성 및 예측 가능성은 마이크로 서비스로 구성하는 확장성이 뛰어난 애플리케이션의 모든 성공적인 배포의 열쇠입니다. 이 자습서에서는 Azure 리소스 관리자 템플릿을 사용하여 Azure에 단일 리소스 그룹으로 두 microservies 애플리케이션을 배포했습니다. 애플리케이션을 템플릿으로 변환하고 예측 가능한 방식으로 프로비전하고 배포하기 위해 필요한 정보를 제공했습니다. 

<a name="resources"></a>

## <a name="more-resources"></a>추가 리소스
* [Azure 리소스 관리자 템플릿 언어](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure 리소스 관리자 템플릿 함수](../azure-resource-manager/resource-group-template-functions.md)
* [Azure 리소스 관리자 템플릿으로 애플리케이션 배포](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure 리소스 관리자로 Azure PowerShell 사용](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Azure에서 리소스 그룹 배포 문제 해결](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>다음 단계

이 문서에서 배포된 리소스 유형에 대한 JSON 구문과 속성에 대해 알아보려면 다음을 참조하세요.

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)