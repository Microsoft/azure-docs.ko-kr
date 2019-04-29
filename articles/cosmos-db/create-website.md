---
title: 템플릿을 사용하여 웹앱 배포 - Azure Cosmos DB
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 계정, Azure App Service Web Apps 및 샘플 웹 애플리케이션을 배포하는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: sngun
ms.openlocfilehash: 1a63de4642850e425cc785e0d89197178ceec1a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60889793"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-web-apps-using-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 및 Azure App Service Web Apps 배포
이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) 웹앱 및 샘플 웹 애플리케이션을 배포 및 통합하는 방법을 설명합니다.

Azure Resource Manager 템플릿을 사용하여 Azure 리소스의 배포 및 구성을 쉽게 자동화할 수 있습니다.  이 자습서에서는 웹 애플리케이션을 배포하고 자동으로 Azure Cosmos DB 계정 연결 정보를 구성하는 방법을 보여줍니다.

이 자습서를 완료하고 나면 다음을 알게 됩니다.  

* Azure Resource Manager 템플릿을 사용하여 Azure App Service에서 Azure Cosmos DB 계정 및 웹앱을 배포하고 통합하는 방법
* Azure Resource Manager 템플릿을 사용하여 App Service Web Apps 및 Webdeploy 애플리케이션에서 Azure Cosmos DB 계정 및 웹앱을 배포 및 통합하는 방법은?

<a id="Prerequisites"></a>

## <a name="prerequisites"></a>필수 조건
> [!TIP]
> 이 자습서에서는 Azure Resource Manager 템플릿 또는 JSON을 이전에 사용해 본 경험이 있다고 가정하지는 않지만, 참조된 템플릿 또는 배포 옵션을 수정하려면 이러한 각 영역에 대한 지식이 필요합니다.
> 
> 

이 자습서의 지침을 따르기 전에 Azure 구독이 있는지 확인하세요. Azure는 구독 기반 플랫폼입니다.  구독을 얻는 방법에 대한 자세한 내용은 [구매 옵션](https://azure.microsoft.com/pricing/purchase-options/), [구성원 제안](https://azure.microsoft.com/pricing/member-offers/) 또는 [평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a id="CreateDB"></a>1단계: 템플릿 파일 다운로드
먼저 이 자습서에 필요한 템플릿 파일을 다운로드합니다.

1. [Azure Cosmos DB 계정, Web Apps 만들기 및 데모 애플리케이션 배포 샘플](https://portalcontent.blob.core.windows.net/samples/DocDBWebsiteTodo.json) 템플릿을 로컬 폴더(예: C:\Azure Cosmos DBTemplates)에 다운로드합니다. 이 템플릿은 Azure Cosmos DB 계정, App Service 웹앱 및 웹 애플리케이션을 배포합니다.  또한 Azure Cosmos DB 계정에 연결되도록 웹 애플리케이션을 자동으로 구성합니다.
2. [Azure Cosmos DB 계정 및 Web Apps 만들기 샘플](https://portalcontent.blob.core.windows.net/samples/DocDBWebSite.json) 템플릿을 로컬 폴더(예: C:\Azure Cosmos DBTemplates)에 다운로드합니다. 이 템플릿은 Azure Cosmos DB 계정 및 App Service 웹앱을 배포하고 쉽게 Azure Cosmos DB 연결 정보를 노출하도록 사이트의 애플리케이션 설정을 수정하지만 웹 애플리케이션을 포함하지는 않습니다.  

<a id="Build"></a>

## <a name="step-2-deploy-the-azure-cosmos-db-account-app-service-web-app-and-demo-application-sample"></a>2단계: Azure Cosmos DB 계정, App Service 웹앱 및 데모 애플리케이션 샘플 배포
이제 첫 번째 템플릿을 배포합니다.

> [!TIP]
> 템플릿은 다음 템플릿에 입력된 웹앱 이름과 Azure Cosmos DB 계정 이름이 a) 유효한지, b) 사용 가능한지를 확인하지 않습니다.  배포를 제출하기 전에 지정하려는 이름의 가용성을 확인하는 것이 좋습니다.
> 
> 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 새로 만들기를 클릭하고 "템플릿 배포"를 검색합니다.
    ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment1.png)
2. 템플릿 배포 항목을 선택하고 **만들기** ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment2.png)을 클릭합니다.
3. **템플릿 편집**을 클릭하고 DocDBWebsiteTodo.json 템플릿 파일의 내용을 붙여 넣고 **저장**을 클릭합니다.
   ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment3.png)
4. **매개 변수 편집**을 클릭하고 각 필수 매개 변수 값을 제공하고 **확인**을 클릭합니다.  매개 변수는 다음과 같습니다.
   
   1. SITENAME: App Service 웹앱 이름을 지정하고 웹앱에 액세스하는 데 사용할 URL을 만드는 데 사용됩니다(예: "mydemodocdbwebsite"를 지정할 경우 웹앱에 액세스하는 데 사용되는 URL은 mydemodocdbwebsite.azurewebsites.net이 됨).
   2. HOSTINGPLANNAME: 만들려는 App Service 호스팅 계획의 이름을 지정합니다.
   3. LOCATION: Azure Cosmos DB 및 웹앱 리소스를 만들 Azure 위치를 지정합니다.
   4. DATABASEACCOUNTNAME: 만들 Azure Cosmos DB 계정의 이름을 지정합니다.   
      
      ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment4.png)
5. 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들도록 이름을 제공하며 리소스 그룹에 대한 위치를 선택합니다.

    ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment5.png)
6. **약관 검토**, **구매**를 클릭한 다음 **만들기**를 클릭하여 배포를 시작합니다.  **대시보드에 고정** 을 선택하여 결과 배포를 Azure 포털 홈 페이지에서 쉽게 볼 수 있도록 합니다.
   ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment6.png)
7. 배포가 완료되면 리소스 그룹 창이 열립니다.
   ![리소스 그룹 창 스크린샷](./media/create-website/TemplateDeployment7.png)  
8. 애플리케이션을 사용하려면 웹앱 URL로 이동합니다(위의 예제에서 URL은 http://mydemodocdbwebapp.azurewebsites.net) 이 됨).  다음과 같은 웹 애플리케이션이 표시됩니다.
   
   ![샘플 Todo 애플리케이션](./media/create-website/image2.png)
9. 계속해서 웹앱에서 몇 가지 작업을 만든 다음, Azure Portal의 리소스 그룹 창으로 돌아옵니다. 리소스 목록에서 Azure Cosmos DB 계정 리소스를 클릭한 다음 **데이터 탐색기**를 클릭합니다.
10. 기본 쿼리 "SELECT * FROM c"를 실행하고 결과를 검사합니다.  쿼리가 위의 7단계에서 만든 todo 항목의 JSON 표현을 검색했음을 알 수 있습니다.  자유롭게 쿼리를 실험합니다. 예를 들어 SELECT * FROM c WHERE c.isComplete = true를 실행하면 완료로 표시된 모든 todo 항목이 반환됩니다.
11. 자유롭게 Azure Cosmos DB 포털 환경을 탐색하거나 샘플 Todo 애플리케이션을 수정합니다.  준비가 되면 다른 템플릿을 배포합니다.

<a id="Build"></a> 

## <a name="step-3-deploy-the-document-account-and-web-app-sample"></a>3단계: 문서 계정 및 웹앱 샘플 배포
이제 두 번째 템플릿을 배포합니다.  이 템플릿은 계정 엔드포인트 및 마스터 키와 같은 Azure Cosmos DB 연결 정보를 애플리케이션 설정 또는 사용자 지정 연결 문자열로 웹앱에 주입하는 방법을 보여주는 데 유용합니다. 예를 들어 Azure Cosmos DB 계정을 사용하여 배포하는 웹 애플리케이션이 있어서 배포하는 동안 연결 정보가 자동으로 채워졌을 수도 있습니다.

> [!TIP]
> 템플릿은 아래에 입력된 웹앱 이름과 Azure Cosmos DB 계정 이름이 a) 유효한지, b) 사용 가능한지를 확인하지 않습니다.  배포를 제출하기 전에 지정하려는 이름의 가용성을 확인하는 것이 좋습니다.
> 
> 

1. [Azure Portal](https://portal.azure.com)에서 새로 만들기를 클릭하고 "템플릿 배포"를 검색합니다.
    ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment1.png)
2. 템플릿 배포 항목을 선택하고 **만들기** ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment2.png)을 클릭합니다.
3. **템플릿 편집**을 클릭하고 DocDBWebSite.json 템플릿 파일의 내용을 붙여 넣고 **저장**을 클릭합니다.
   ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment3.png)
4. **매개 변수 편집**을 클릭하고 각 필수 매개 변수 값을 제공하고 **확인**을 클릭합니다.  매개 변수는 다음과 같습니다.
   
   1. SITENAME: App Service 웹앱 이름을 지정하고 웹앱에 액세스하는 데 사용할 URL을 만드는 데 사용됩니다(예: "mydemodocdbwebsite"를 지정할 경우 웹앱에 액세스하는 데 사용되는 URL은 mydemodocdbwebsite.azurewebsites.net이 됨).
   2. HOSTINGPLANNAME: 만들려는 App Service 호스팅 계획의 이름을 지정합니다.
   3. LOCATION: Azure Cosmos DB 및 웹앱 리소스를 만들 Azure 위치를 지정합니다.
   4. DATABASEACCOUNTNAME: 만들 Azure Cosmos DB 계정의 이름을 지정합니다.   
      
      ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment4.png)
5. 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만들도록 이름을 제공하며 리소스 그룹에 대한 위치를 선택합니다.

    ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment5.png)
6. **약관 검토**, **구매**를 클릭한 다음 **만들기**를 클릭하여 배포를 시작합니다.  **대시보드에 고정** 을 선택하여 결과 배포를 Azure 포털 홈 페이지에서 쉽게 볼 수 있도록 합니다.
   ![템플릿 배포 UI의 스크린샷](./media/create-website/TemplateDeployment6.png)
7. 배포가 완료되면 리소스 그룹 창이 열립니다.
   ![리소스 그룹 창 스크린샷](./media/create-website/TemplateDeployment7.png)  
8. 리소스 목록에서 웹앱 리소스를 클릭한 다음, **애플리케이션 설정**![리소스 그룹의 스크린샷](./media/create-website/TemplateDeployment9.png)을 클릭합니다.  
9. 애플리케이션 설정이 Azure Cosmos DB 엔드포인트 및 각 Azure Cosmos DB 마스터 키에 대해 어떻게 제시되어 있는지 확인합니다.

    ![애플리케이션 설정의 스크린샷](./media/create-website/TemplateDeployment10.png)  
10. 자유롭게 Azure Portal을 계속 탐색하거나 Azure Cosmos DB [샘플](https://go.microsoft.com/fwlink/?LinkID=402386) 중 하나에 따라 고유한 Azure Cosmos DB 애플리케이션을 만듭니다.

<a name="NextSteps"></a>

## <a name="next-steps"></a>다음 단계
축하합니다! Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB, App Service 웹앱 및 샘플 웹 애플리케이션을 배포했습니다.

* Azure Cosmos DB에 대해 자세히 알아보려면 [여기](https://azure.microsoft.com/services/cosmos-db/)를 클릭하세요.
* Azure App Service Web Apps에 대해 자세히 알아보려면 [여기](https://go.microsoft.com/fwlink/?LinkId=325362)를 클릭하세요.
* Azure 리소스 관리자 템플릿에 대해 자세히 알아보려면 [여기](https://msdn.microsoft.com/library/azure/dn790549.aspx)를 클릭하세요.

## <a name="whats-changed"></a>변경된 내용
* 웹 사이트에서 App Service로의 변경에 대한 지침은 다음을 참조하세요. [Azure App Service와 이 서비스가 기존 Azure 서비스에 미치는 영향](https://go.microsoft.com/fwlink/?LinkId=529714)

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure App Service를 시작하려면 [App Service 체험](https://go.microsoft.com/fwlink/?LinkId=523751)으로 이동합니다. App Service에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

