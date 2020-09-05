---
title: 개요
description: Azure App Service로 웹 애플리케이션을 개발 및 호스팅하는 방법에 대해 알아보세요.
ms.assetid: 94af2caf-a2ec-4415-a097-f60694b860b3
ms.topic: overview
ms.date: 07/06/2020
ms.custom: devx-track-dotnet, mvc, seodec18
ms.openlocfilehash: 1b72224441741990a1fc94400dfe718ea9d1b0b3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961485"
---
# <a name="app-service-overview"></a>App Service 개요

*Azure App Service*는 웹 애플리케이션, REST API 및 모바일 백 엔드를 호스트하는 HTTP 기반 서비스입니다. .NET, .NET Core, Java, Ruby, Node.js, PHP 또는 Python 등 원하는 언어로 개발할 수 있습니다. Windows 및 [Linux](#app-service-on-linux) 기반 환경에서 애플리케이션을 쉽게 실행하고 확장할 수 있습니다.

App Service는 보안, 부하 분산, 자동 크기 조정 및 자동화된 관리와 같이 Microsoft Azure의 강력한 기능을 애플리케이션에 추가합니다. 또한 Azure DevOps, GitHub, Docker 허브 및 기타 원본, 패키지 관리, 스테이징 환경, 사용자 지정 도메인 및 TLS/SSL 인증서의 지속적인 배포와 같은 DevOps 기능도 활용할 수 있습니다. 

App Service를 사용하면 Azure 컴퓨팅 리소스에 대한 비용을 지불하게 됩니다. 사용할 컴퓨팅 리소스는 앱을 실행하는 _App Service 계획_에 따라 결정됩니다. 자세한 내용은 [Azure App Service 계획 개요](overview-hosting-plans.md)를 참조하세요.

## <a name="why-use-app-service"></a>App Service를 사용하는 이유는 무엇인가요?

App Service의 주요 기능은 다음과 같습니다.

* **여러 언어 및 프레임워크** - App Service는 ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP 또는 Python에 대한 고급 지원을 제공합니다. [PowerShell 및 기타 스크립트 또는 실행 파일](webjobs-create.md)을 백그라운드 서비스로 실행할 수도 있습니다.
* **관리형 프로덕션 환경** - App Service는 [OS 및 언어 프레임워크를 자동으로 패치하고 유지 관리](overview-patch-os-runtime.md)합니다. 뛰어난 앱을 작성하고 Azure에서 플랫폼에 대해 걱정할 수 있습니다.
* **컨테이너화 및 Docker** - 앱을 Dockerize하고 App Service에서 사용자 지정 Windows 또는 Linux 컨테이너를 호스트합니다. Docker Compose를 사용하여 다중 컨테이너 앱을 실행합니다. Docker 기술을 App Service로 직접 마이그레이션합니다.
* **DevOps 최적화** - Azure DevOps, GitHub, BitBucket, Docker 허브 또는 Azure Container Registry를 사용하여 [연속 통합 및 배포](deploy-continuous-deployment.md)를 설정합니다. [테스트 및 스테이징 환경](deploy-staging-slots.md)을 통해 업데이트를 승격합니다. [Azure PowerShell](/powershell/azure/) 또는 [플랫폼 간 CLI(명령줄 인터페이스)](/cli/azure/install-azure-cli)를 사용하여 App Service에서 앱을 관리합니다.
* **고가용성을 가진 글로벌 규모 조정** - 수동 또는 자동으로 규모를 [강화](manage-scale-up.md) 또는 [확장](../azure-monitor/platform/autoscale-get-started.md)합니다. Microsoft의 글로벌 데이터 센터 인프라의 모든 위치에서 앱을 호스팅하고 App Service [SLA](https://azure.microsoft.com/support/legal/sla/app-service/) 를 사용하면 고가용성이 보장됩니다.
* **SaaS 플랫폼 및 온-프레미스 데이터에 연결** - 엔터프라이즈 시스템(예: SAP), SaaS 서비스(예: Salesforce) 및 인터넷 서비스(예: Facebook)를 위해 50개 이상의 [커넥터](../connectors/apis-list.md)에서 선택합니다. [하이브리드 연결](app-service-hybrid-connections.md) 및 [Azure Virtual Networks](web-sites-integrate-with-vnet.md)를 사용하여 온-프레미스 데이터에 액세스합니다.
* **보안 및 규정 준수** - App Service는 [ISO, SOC 및 PCI 규격](https://www.microsoft.com/en-us/trustcenter)입니다. [Azure Active Directory](configure-authentication-provider-aad.md), [Google](configure-authentication-provider-google.md), [Facebook](configure-authentication-provider-facebook.md), [Twitter](configure-authentication-provider-twitter.md) 또는 [Microsoft 계정](configure-authentication-provider-microsoft.md)으로 사용자를 인증합니다. [IP 주소 제한](app-service-ip-restrictions.md)을 만들고 [서비스 ID를 관리](overview-managed-identity.md)합니다.
* **애플리케이션 템플릿** - [Azure Marketplace](https://azure.microsoft.com/marketplace/)(예: WordPress, Joomla 및 Drupal)의 광범위한 애플리케이션 템플릿 목록에서 선택합니다.
* **Visual Studio와 Visual Studio Code 통합** - Visual Studio 및 Visual Studio Code의 전용 도구는 생성, 배포, 디버깅 작업을 간소화합니다.
* **API 및 모바일 기능** - App Service는 RESTful API 시나리오에 대한 턴키 방식 CORS 지원을 제공하며, 인증, 오프라인 데이터 동기화, 푸시 알림 등을 활성화하여 모바일 앱 시나리오를 간소화합니다.
* **서버리스 코드** - 인프라를 명시적으로 프로비전하거나 관리하지 않고도 요청 시에 코드 조각이나 스크립트를 실행하고, 코드에서 실제로 사용하는 컴퓨팅 시간에 대해서만 비용을 지불합니다([Azure Functions](../azure-functions/index.yml) 참조).

Azure는 App Service 뿐만 아니라 웹 사이트와 웹 애플리케이션 호스팅에 사용할 수 있는 다른 서비스를 제공합니다. 대부분의 시나리오의 경우 App Service를 사용하는 것이 좋습니다.  마이크로 서비스 아키텍처의 경우 [Azure Spring-Cloud Service](../spring-cloud/index.yml) 또는 [Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric)을 사용하는 것이 좋습니다.  코드가 실행되는 VM을 보다 정밀하게 제어해야 하는 경우 [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)를 사용하는 것이 좋습니다. 이러한 Azure 서비스 중에서 하나를 선택하는 방법에 대한 자세한 내용은 [Azure App Service, Virtual Machines, 서비스 패브릭 및 Cloud Services 비교](/azure/architecture/guide/technology-choices/compute-decision-tree)를 참조하세요.

## <a name="app-service-on-linux"></a>Linux의 App Service

App Service는 지원되는 애플리케이션 스택에 대해 기본적으로 Linux에서 웹앱을 호스트할 수도 있습니다. 사용자 지정 Linux 컨테이너(컨테이너용 웹앱이라고도 함)를 실행할 수도 있습니다.

### <a name="built-in-languages-and-frameworks"></a>기본 제공 언어 및 프레임워크

Linux의 App Service는 다양한 언어별 기본 제공 이미지를 지원합니다. 코드를 배포하기만 하면 됩니다. 지원되는 언어는 다음과 같습니다. Node.js, Java(JRE 8 & JRE 11), PHP, Python, .NET Core 및 Ruby. [`az webapp list-runtimes --linux`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes)를 실행하여 최신 언어 및 지원되는 버전을 확인합니다. 애플리케이션에 필요한 런타임이 기본 제공 이미지에서 지원되지 않는 경우 사용자 지정 컨테이너를 사용하여 배포할 수 있습니다.

### <a name="limitations"></a>제한 사항

- Linux의 App Service는 [공유](https://azure.microsoft.com/pricing/details/app-service/plans/) 가격 책정 계층에서 지원되지 않습니다. 
- 동일한 App Service 계획에서 Windows 및 Linux 앱을 혼합할 수 없습니다.  
- 동일한 리소스 그룹 내에서 동일한 지역에 있는 Windows 및 Linux 앱을 혼합할 수 없습니다.
- Azure Portal에는 현재 Linux 앱에서 작동하는 기능만 표시됩니다. 기능이 활성화되면 포털에서 활성화됩니다.
- 기본 제공 이미지에 배포된 경우 코드 및 콘텐츠에는 Azure Storage에서 지원하는 웹 콘텐츠용 스토리지 볼륨이 할당됩니다. 이 볼륨의 디스크 대기 시간은 컨테이너 파일 시스템의 대기 시간보다 높고 더 많은 변수가 있습니다. 콘텐츠 파일에 대한 많은 읽기 전용 액세스를 필요로 하는 앱은 콘텐츠 볼륨 대신 파일 시스템에 파일을 배치하는 사용자 지정 컨테이너 옵션의 이점을 누릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

첫 번째 웹앱을 만듭니다.

> [!div class="nextstepaction"]
> [ASP.NET Core(Windows 또는 Linux)](quickstart-dotnetcore.md)

> [!div class="nextstepaction"]
> [ASP.NET(Windows)](quickstart-dotnet-framework.md)

> [!div class="nextstepaction"]
> [PHP(Windows 또는 Linux)](quickstart-php.md)

> [!div class="nextstepaction"]
> [Ruby(Linux)](quickstart-ruby.md)

> [!div class="nextstepaction"]
> [Node.js(Windows 또는 Linux)](quickstart-nodejs.md)

> [!div class="nextstepaction"]
> [Java(Windows 또는 Linux)](quickstart-java.md)

> [!div class="nextstepaction"]
> [Python(Linux)](quickstart-python.md)

> [!div class="nextstepaction"]
> [HTML(Windows 또는 Linux)](quickstart-html.md)

> [!div class="nextstepaction"]
> [사용자 지정 컨테이너(Windows 또는 Linux)](tutorial-custom-container.md)