---
title: 배포 FAQ - Azure App Service | Microsoft Docs
description: Azure App Service Web Apps 기능의 배포에 대한 질문과 대답을 확인합니다.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 9d38e3caf3d835eaf73df90282157eed76c5ea25
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61270050"
---
# <a name="deployment-faqs-for-web-apps-in-azure"></a>Azure의 Web Apps에 대한 배포 FAQ

이 문서에는 [Azure App Service의 Web Apps 기능](https://azure.microsoft.com/services/app-service/web/) 관련 배포 문제에 대한 FAQ(질문과 대답)가 있습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-am-just-getting-started-with-app-service-web-apps-how-do-i-publish-my-code"></a>App Service Web Apps를 시작하려고 합니다. 내 코드를 어떻게 게시할 수 있나요?

다음은 웹앱 코드를 게시할 수 있는 몇 가지 옵션입니다.

*   Visual Studio를 사용하여 배포. Visual Studio 솔루션이 있는 경우 웹 애플리케이션 프로젝트를 마우스 오른쪽 단추로 클릭하고 나서 **게시**를 선택합니다.
*   FTP 클라이언트를 사용하여 배포. Azure Portal에서 코드를 배포할 웹앱에 대한 게시 프로필을 다운로드합니다. 그다음에 동일한 게시 프로필 FTP 자격 증명을 사용하여 \site\wwwroot에 파일을 업로드합니다.

자세한 내용은 [App Service에 앱 배포](deploy-local-git.md)를 참조하세요.

## <a name="i-see-an-error-message-when-i-try-to-deploy-from-visual-studio-how-do-i-resolve-this"></a>Visual Studio에서 배포하려고 할 때 오류 메시지가 표시됩니다. 이 문제를 해결하려면 어떻게 해야 하나요?

다음과 같은 메시지가 나타나면 이전 버전의 SDK를 사용하는 것일 수 있습니다. "리소스 그룹 'YourResourceGroup'에서 'YourResourceName' 리소스를 배포하는 동안 오류가 발생했습니다. MissingRegistrationForLocation: 구독이 '미국 중부' 위치의 '구성 요소' 리소스 종류에 대해 등록되지 않았습니다. 이 리소스 형식에 액세스할 수 있는 권한을 가지려면 이 공급자에 대해 다시 등록하세요.” 

이 오류를 해결하려면 [최신 SDK](https://azure.microsoft.com/downloads/)로 업그레이드합니다. 이 메시지가 표시되고 최신 SDK가 있으면 지원 요청을 제출하세요.

## <a name="how-do-i-deploy-an-aspnet-application-from-visual-studio-to-app-service"></a>Visual Studio에서 App Service로 ASP.NET 애플리케이션을 배포하려면 어떻게 하나요?
<a id="deployasp"></a>

[Azure에서 ASP.NET 웹앱 만들기](app-service-web-get-started-dotnet.md) 자습서에서는 Visual Studio 2017을 사용하여 ASP.NET 웹 애플리케이션을 Azure App Service의 웹앱에 배포하는 방법을 보여줍니다.

## <a name="what-are-the-different-types-of-deployment-credentials"></a>배포 자격 증명 형식에는 무엇이 있나요?

App Service에서는 로컬 Git 배포 및 FTP/S 배포에 대한 두 가지 형식의 자격 증명을 지원합니다. 배포 자격 증명을 구성하는 방법에 대한 자세한 내용은 [App Service에 대한 배포 자격 증명 구성](deploy-configure-credentials.md)을 참조하세요.

## <a name="what-is-the-file-or-directory-structure-of-my-app-service-web-app"></a>내 App Service 웹앱의 파일 또는 디렉터리 구조는 무엇인가요?

App Service 앱의 파일 구조에 대한 자세한 내용은 [File structure in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(Azure의 파일 구조)를 참조하세요.

## <a name="how-do-i-resolve-ftp-error-550---there-is-not-enough-space-on-the-disk-when-i-try-to-ftp-my-files"></a>내 파일을 FTP하려고 할 때 “FTP 오류 550 - 디스크 공간이 부족합니다.” 메시지가 표시되면 어떻게 해결하나요?

이 메시지가 표시되면 웹앱에 대한 서비스 계획에서 디스크 할당량에 도달한 것일 수 있습니다. 필요한 디스크 공간에 따라 상위 서비스 계층으로 확장해야 할 수 있습니다. 가격 계획 및 리소스 제한에 대한 자세한 내용은 [App Service 가격](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.

## <a name="how-do-i-set-up-continuous-deployment-for-my-app-service-web-app"></a>내 App Service 웹앱에 대한 지속적인 배포를 설정하려면 어떻게 하나요?

Azure DevOps, OneDrive, GitHub, Bitbucket, Dropbox 및 기타 Git 리포지토리를 포함한 여러 리소스에서 지속적인 배포를 설정할 수 있습니다. 이러한 옵션은 포털에서 사용할 수 있습니다. [App Service에 대한 지속적인 배포](deploy-continuous-deployment.md)는 지속적인 배포를 설정하는 방법을 설명하는 유용한 자습서입니다.

## <a name="how-do-i-troubleshoot-issues-with-continuous-deployment-from-github-and-bitbucket"></a>GitHub 및 Bitbucket의 지속적인 배포 관련 문제를 해결하려면 어떻게 하나요?

GitHub 또는 Bitbucket의 지속적인 배포 관련 문제를 조사하는 방법에 대한 자세한 내용은 [Investigating continuous deployment](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)(지속적인 배포 조사)를 참조하세요.

## <a name="i-cant-ftp-to-my-site-and-publish-my-code-how-do-i-resolve-this"></a>내 사이트에 FTP하고 내 코드를 게시할 수 없습니다. 이 문제를 해결하려면 어떻게 해야 하나요?

FTP 문제를 해결하려면:

1. 올바른 호스트 이름 및 자격 증명을 입력하고 있는지 확인합니다. 다양한 자격 증명 형식 및 이러한 자격 증명을 사용하는 방법에 대한 자세한 내용은 [Deployment credentials](https://github.com/projectkudu/kudu/wiki/Deployment-credentials)(배포 자격 증명)를 참조하세요.
2. FTP 포트가 방화벽으로 차단되지 않았는지 확인합니다. 포트에는 다음 설정이 있어야 합니다.
    * FTP 제어 연결 포트: 21
    * FTP 데이터 연결 포트: 989, 10001-10300

## <a name="how-do-i-publish-my-code-to-app-service"></a>App Service에 내 코드를 게시하려면 어떻게 하나요?

Azure 빠른 시작은 선택한 배포 스택 및 방법을 사용하여 앱을 배포할 수 있도록 디자인되어 있습니다. Azure Portal에서 빠른 시작을 사용하려면 앱 서비스로 이동하고 **배포**에서 **빠른 시작**을 선택합니다.

## <a name="why-does-my-app-sometimes-restart-after-deployment-to-app-service"></a>App Service에 배포한 후 때때로 앱이 다시 시작되지 않는 이유는 무엇인가요?

애플리케이션 배포 후 다시 시작되는 경우를 알아보려면 [Deployment vs. runtime issues](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues#deployments-and-web-app-restarts")(배포 및 런타임 문제)를 참조하세요. 문서에서 설명하는 대로 App Service는 파일을 wwwroot 폴더에 배포합니다. 앱을 직접 다시 시작하지는 않습니다.

## <a name="how-do-i-integrate-azure-devops-code-with-app-service"></a>Azure DevOps 코드를 App Service와 통합하려면 어떻게 하나요?

Azure DevOps에서 지속적인 배포를 사용할 수 있는 다음과 같은 두 가지 옵션이 있습니다.

*   Git 프로젝트를 사용합니다. 배포 센터를 사용 하 여 App Service를 통해 연결 합니다.
*   TFVC(Team Foundation Version Control) 프로젝트를 사용합니다. App Service에 대한 빌드 에이전트를 사용하여 배포합니다.

이러한 두 가지 옵션에 대한 연속 코드 배포는 기존 개발자 워크플로 및 체크 인 절차에 따라 달라집니다. 자세한 내용은 다음 문서를 참조하세요. 

*   [Implement continuous deployment of your app to an Azure website](https://www.visualstudio.com/docs/release/examples/azure/azure-web-apps-from-build-and-release-hubs)(Azure 웹 사이트에 대한 앱 지속적인 배포 구현)
*   [Set up an Azure DevOps organization so it can deploy to a web app](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)(웹앱에 배포할 수 있도록 Azure DevOps 조직 설정)

## <a name="how-do-i-use-ftp-or-ftps-to-deploy-my-app-to-app-service"></a>FTP 또는 FTPS를 사용하여 App Service에 내 앱을 배포하려면 어떻게 하나요?

FTP 또는 FTPS를 사용하여 App Service에 웹앱을 배포하는 방법에 대한 자세한 내용은 [FTP/S를 사용하여 App Service에 앱 배포](deploy-ftp.md)를 참조하세요.
