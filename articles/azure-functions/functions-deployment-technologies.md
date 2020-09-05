---
title: Azure Functions의 배포 기술
description: Azure Functions 코드를 배포할 수 있는 다양 한 방법을 알아봅니다.
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 3865e6906b39633e14c86619770188f1c73fed8e
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88641962"
---
# <a name="deployment-technologies-in-azure-functions"></a>Azure Functions의 배포 기술

몇 가지 다른 기술을 사용 하 여 Azure Functions 프로젝트 코드를 Azure에 배포할 수 있습니다. 이 문서에서는 사용자에 게 제공 되는 배포 방법에 대해 간략하게 설명 하 고 다양 한 시나리오에서 사용 하기에 가장 좋은 방법에 대 한 권장 사항을 제공 합니다. 또한 기본 배포 기술에 대 한 주요 세부 정보 목록과 주요 정보를 제공 합니다. 

## <a name="deployment-methods"></a>배포 방법

Azure에 코드를 게시 하는 데 사용 하는 배포 기술은 일반적으로 앱을 게시 하는 방법에 따라 결정 됩니다. 적절 한 배포 방법은 특정 요구 사항 및 개발 주기의 시점에 따라 결정 됩니다. 예를 들어 개발 및 테스트 중에 Visual Studio Code와 같은 개발 도구에서 직접 배포할 수 있습니다. 앱이 프로덕션 환경에 있는 경우 추가 유효성 검사 및 테스트를 포함 하는 자동화 된 게시 파이프라인을 사용 하거나 소스 제어에서 지속적으로 게시할 가능성이 높습니다.  

다음 표에서는 함수 프로젝트에 사용할 수 있는 배포 방법에 대해 설명 합니다.

| 배포 &nbsp; 유형 | 메서드 | 최적 ... |
| -- | -- | -- |
| 도구 기반 | &bull;&nbsp;[Visual &nbsp; Studio &nbsp; Code &nbsp; 게시](functions-develop-vs-code.md#publish-to-azure)<br/>&bull;&nbsp;[Visual Studio 게시](functions-develop-vs.md#publish-to-azure)<br/>&bull;&nbsp;[핵심 도구 게시](functions-run-local.md#publish) | 개발 및 기타 ad hock 배포 중 배포. 배포는 도구를 통해 로컬로 관리 됩니다. | 
| App Service 관리| &bull;&nbsp;[배포 &nbsp; 센터 &nbsp; (CI/CD)](functions-continuous-deployment.md)<br/>&bull;&nbsp;[컨테이너 &nbsp; 배포](functions-create-function-linux-custom-image.md#enable-continuous-deployment-to-azure) |  원본 제어 또는 컨테이너 레지스트리에서 CI/CD (연속 배포) 배포는 Kudu (App Service 플랫폼)에 의해 관리 됩니다.|
| 외부 파이프라인|&bull;&nbsp;[DevOps 파이프라인](functions-how-to-azure-devops.md)<br/>&bull;&nbsp;[GitHub 작업](functions-how-to-github-actions.md) | 추가 유효성 검사, 테스트 및 기타 작업을 포함 하는 프로덕션 및 DevOps 파이프라인은 자동화 된 배포의 일부로 실행 됩니다. 배포는 파이프라인을 통해 관리 됩니다. |

특정 함수 배포는 해당 컨텍스트를 기반으로 하는 최고의 기술을 사용 하지만 대부분의 배포 방법은 [zip 배포](#zip-deploy)를 기반으로 합니다.

## <a name="deployment-technology-availability"></a>배포 기술 가용성

Azure Functions는 Windows 및 Linux에서 플랫폼 간 로컬 개발 및 호스팅을 지원 합니다. 현재 세 가지 호스팅 요금제를 사용할 수 있습니다.

+ [Consumption](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [전용 (App Service)](functions-scale.md#app-service-plan)

각 계획 마다 동작이 다릅니다. Azure Functions의 각 버전에 대 한 모든 배포 기술이 제공 되는 것은 아닙니다. 다음 차트는 운영 체제 및 호스팅 계획의 각 조합에 대해 지원 되는 배포 기술을 보여 줍니다.

| 배포 기술 | Windows 사용량 | Windows Premium | Windows 전용  | Linux 소비 | Linux 프리미엄 | Linux 전용 |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| 외부 패키지 URL<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip 배포 |✔|✔|✔|✔|✔|✔|
| Docker 컨테이너 | | | | |✔|✔|
| 웹 배포 |✔|✔|✔| | | |
| 원본 제어 |✔|✔|✔| |✔|✔|
| 로컬 Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| 클라우드 동기화<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| 포털 편집 |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> [수동 트리거를 동기화](#trigger-syncing)해야 하는 배포 기술입니다.
<sup>2</sup> 포털 편집은 프리미엄 및 전용 요금제를 사용 하는 Linux의 함수에 대 한 HTTP 및 타이머 트리거에 대해서만 사용할 수 있습니다.

## <a name="key-concepts"></a>주요 개념

몇 가지 주요 개념은 Azure Functions에서 배포가 작동 하는 방식을 이해 하는 데 중요 합니다.

### <a name="trigger-syncing"></a>동기화 트리거

트리거를 변경 하는 경우 함수 인프라에서 변경 사항을 알고 있어야 합니다. 여러 배포 기술에 대 한 동기화가 자동으로 수행 됩니다. 그러나 일부 경우에는 트리거를 수동으로 동기화 해야 합니다. 외부 패키지 URL, 로컬 Git, 클라우드 동기화 또는 FTP를 참조 하 여 업데이트를 배포할 때 수동으로 트리거를 동기화 해야 합니다. 다음 세 가지 방법 중 하나로 트리거를 동기화 할 수 있습니다.

* Azure Portal에서 함수 앱을 다시 시작 합니다.
* `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` [마스터 키](functions-bindings-http-webhook-trigger.md#authorization-keys)를 사용 하 여 HTTP POST 요청을 보냅니다.
* 에 HTTP POST 요청을 보냅니다 `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01` . 자리 표시자를 구독 ID, 리소스 그룹 이름 및 함수 앱의 이름으로 바꿉니다.

### <a name="remote-build"></a>원격 빌드

Zip 배포 후에 수신 하는 코드에서 자동으로 빌드를 수행할 수 Azure Functions. 이러한 빌드는 응용 프로그램이 Windows 또는 Linux에서 실행 되는지 여부에 따라 약간 다르게 동작 합니다. 앱이 이전에 [패키지 모드에서](run-functions-from-deployment-package.md) 실행 되도록 설정 된 경우 원격 빌드가 수행 되지 않습니다. 원격 빌드를 사용 하는 방법을 알아보려면 [zip 배포](#zip-deploy)로 이동 합니다.

> [!NOTE]
> 원격 빌드에 문제가 있는 경우 기능을 사용할 수 있게 되기 전에 앱이 생성 되었기 때문일 수 있습니다 (2019 년 8 월 1 일). 새 함수 앱을 만들거나를 실행 `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` 하 여 함수 앱을 업데이트 해 보세요. 이 명령을 실행 하는 데 두 번의 시도가 성공할 수 있습니다.

#### <a name="remote-build-on-windows"></a>Windows에서 원격 빌드

Windows에서 실행 되는 모든 함수 앱에는 작은 관리 앱 인 SCM (또는 [Kudu](https://github.com/projectkudu/kudu)) 사이트가 있습니다. 이 사이트는 Azure Functions에 대 한 대부분의 배포 및 빌드 논리를 처리 합니다.

앱을 Windows에 배포할 때 `dotnet restore` (c #) 또는 (JavaScript)와 같은 언어별 명령이 `npm install` 실행 됩니다.

#### <a name="remote-build-on-linux"></a>Linux에서 원격 빌드

Linux에서 원격 빌드를 사용 하도록 설정 하려면 다음 [응용 프로그램 설정을](functions-how-to-use-azure-function-app-settings.md#settings) 설정 해야 합니다.

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

기본적으로 [Azure Functions Core Tools](functions-run-local.md) 및 [Azure Functions Visual Studio Code 확장](functions-create-first-function-vs-code.md#publish-the-project-to-azure) 은 모두 Linux에 배포할 때 원격 빌드를 수행 합니다. 이로 인해 두 도구 모두 Azure에서 이러한 설정을 자동으로 만듭니다.

앱이 Linux에서 원격으로 빌드되면 [배포 패키지에서 실행](run-functions-from-deployment-package.md)됩니다.

##### <a name="consumption-plan"></a>소비 계획

소비 계획에서 실행 되는 Linux 함수 앱은 배포 옵션을 제한 하는 SCM/Kudu 사이트를 포함 하지 않습니다. 그러나 소비 계획에서 실행 되는 Linux의 함수 앱은 원격 빌드를 지원 합니다.

##### <a name="dedicated-and-premium-plans"></a>전용 및 프리미엄 계획

[전용 (App Service) 요금제](functions-scale.md#app-service-plan) 및 [Premium 요금제](functions-scale.md#premium-plan) 에서 Linux에서 실행 되는 함수 앱에는 제한 된 SCM/Kudu 사이트도 있습니다.

## <a name="deployment-technology-details"></a>배포 기술 세부 정보

Azure Functions에서 사용할 수 있는 배포 방법은 다음과 같습니다.

### <a name="external-package-url"></a>외부 패키지 URL

외부 패키지 URL을 사용 하 여 함수 앱이 포함 된 원격 패키지 (.zip) 파일을 참조할 수 있습니다. 파일이 제공 된 URL에서 다운로드 되 고 앱이 [패키지 모드에서 실행](run-functions-from-deployment-package.md) 될 때 실행 됩니다.

>__사용 방법:__ `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정에를 추가 합니다. 이 설정의 값은 URL (실행 하려는 특정 패키지 파일의 위치) 이어야 합니다. [포털에서](functions-how-to-use-azure-function-app-settings.md#settings) 또는 [Azure CLI를 사용 하](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)여 설정을 추가할 수 있습니다.
>
>Azure Blob storage를 사용 하는 경우 [공유 액세스 서명 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 이 포함 된 개인 컨테이너를 사용 하 여 패키지에 대 한 액세스 기능을 제공 합니다. 응용 프로그램이 다시 시작 될 때마다 콘텐츠의 복사본을 페치합니다. 참조는 응용 프로그램의 수명 동안 유효 해야 합니다.

>__사용 시기:__ 외부 패키지 URL은 사용자가 [원격 빌드](#remote-build) 를 수행 하지 않으려는 경우 사용 계획에서 Linux에서 실행 되는 Azure Functions에 대해 유일 하 게 지원 되는 배포 방법입니다. 함수 앱이 참조 하는 패키지 파일을 업데이트할 때 [수동으로 트리거를 동기화](#trigger-syncing) 하 여 응용 프로그램의 변경 내용을 Azure에 전달 해야 합니다.

### <a name="zip-deploy"></a>Zip 배포

Zip 배포를 사용 하 여 함수 앱을 포함 하는 .zip 파일을 Azure에 푸시합니다. 필요에 따라 [패키지에서 실행](run-functions-from-deployment-package.md)을 시작 하도록 앱을 설정 하거나 [원격 빌드가](#remote-build) 발생 하도록 지정할 수 있습니다.

>__사용 방법:__ 즐겨 사용 하는 클라이언트 도구를 사용 하 여 배포 합니다. [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)또는 [Azure Functions Core Tools](functions-run-local.md#project-file-deployment)를 사용 하 여 명령줄에서 배포 합니다. 기본적으로 이러한 도구는 zip 배포를 사용 하 고 [패키지에서 실행](run-functions-from-deployment-package.md)합니다. 핵심 도구와 Visual Studio Code 확장은 모두 Linux에 배포할 때 [원격 빌드](#remote-build) 를 사용 하도록 설정 합니다. .Zip 파일을 함수 앱에 수동으로 배포 하려면 [.zip 파일이 나 URL에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)의 지침을 따르세요.

>Zip 배포를 사용 하 여 배포 하는 경우 앱이 [패키지에서 실행](run-functions-from-deployment-package.md)되도록 설정할 수 있습니다. 패키지에서 실행 하려면 `WEBSITE_RUN_FROM_PACKAGE` 응용 프로그램 설정 값을로 설정 `1` 합니다. Zip 배포를 권장 합니다. 응용 프로그램에 대 한 로드 시간이 빨라지고, VS Code, Visual Studio 및 Azure CLI에 대 한 기본값입니다.

>__사용 시기:__ Zip 배포는 Azure Functions에 권장 되는 배포 기술입니다.

### <a name="docker-container"></a>Docker 컨테이너

함수 앱을 포함 하는 Linux 컨테이너 이미지를 배포할 수 있습니다.

>__사용 방법:__ 프리미엄 또는 전용 계획에서 Linux 함수 앱을 만들고 실행할 컨테이너 이미지를 지정 합니다. 다음 두 가지 방법으로 수행할 수 있습니다.
>
>* Azure Portal에서 Azure App Service 계획에 Linux 함수 앱을 만듭니다. **게시**에서 **Docker 이미지**를 선택 하 고 컨테이너를 구성 합니다. 이미지가 호스트 되는 위치를 입력 합니다.
>* Azure CLI를 사용 하 여 App Service 계획에서 Linux 함수 앱을 만듭니다. 방법을 알아보려면 [사용자 지정 이미지를 사용 하 여 Linux에서 함수 만들기](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function)를 참조 하세요.
>
>사용자 지정 컨테이너를 사용 하 여 기존 앱에 배포 하려면 [Azure Functions Core Tools](functions-run-local.md)에서 [`func deploy`](functions-run-local.md#publish) 명령을 사용 합니다.

>__사용 시기:__ 함수 앱이 실행 되는 Linux 환경을 보다 세부적으로 제어 해야 하는 경우 Docker 컨테이너 옵션을 사용 합니다. 이 배포 메커니즘은 Linux에서 실행 되는 함수에만 사용할 수 있습니다.

### <a name="web-deploy-msdeploy"></a>웹 배포 (Msdeploy.exe)

Azure의 Windows에서 실행 되는 함수 앱을 포함 하 여 패키지를 웹 배포 하 고 Windows 응용 프로그램을 모든 IIS 서버에 배포 합니다.

>__사용 방법:__ [Azure Functions Visual Studio tools를](functions-create-your-first-function-visual-studio.md)사용 합니다. **패키지 파일에서 실행 (권장)** 확인란의 선택을 취소 합니다.
>
>[웹 배포 3.6](https://www.iis.net/downloads/microsoft/web-deploy) 를 다운로드 하 고 직접 호출할 수도 있습니다 `MSDeploy.exe` .

>__사용 시기:__ 웹 배포 지원 되며 문제가 없지만 기본 메커니즘은 [패키지에서 실행이 활성화 된 zip 배포](#zip-deploy)입니다. 자세히 알아보려면 [Visual Studio 개발 가이드](functions-develop-vs.md#publish-to-azure)를 참조 하세요.

### <a name="source-control"></a>원본 제어

소스 제어를 사용 하 여 함수 앱을 Git 리포지토리에 연결 합니다. 해당 리포지토리의 코드 업데이트가 배포를 트리거합니다. 자세한 내용은 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments)를 참조 하세요.

>__사용 방법:__ 포털의 기능 영역에 있는 Deployment Center를 사용 하 여 원본 제어에서 게시를 설정 합니다. 자세한 내용은 [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)를 참조하세요.

>__사용 시기:__ 원본 제어를 사용 하는 것은 해당 함수 앱에서 공동 작업 하는 팀의 모범 사례입니다. 원본 제어는 보다 정교한 배포 파이프라인을 가능 하 게 하는 좋은 배포 옵션입니다.

### <a name="local-git"></a>로컬 Git

로컬 Git를 사용 하 여 Git를 통해 로컬 컴퓨터에서 Azure Functions로 코드를 푸시할 수 있습니다.

>__사용 방법:__ [로컬 Git 배포](../app-service/deploy-local-git.md)의 지침에 따라 Azure App Service 합니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 사용 하는 것이 좋습니다. 로컬 Git에서 게시 하는 경우 [수동으로 트리거를 동기화](#trigger-syncing)해야 합니다.

### <a name="cloud-sync"></a>클라우드 동기화

클라우드 동기화를 사용 하 여 Dropbox 및 OneDrive의 콘텐츠를 Azure Functions와 동기화 합니다.

>__사용 방법:__ [클라우드 폴더에서 콘텐츠 동기화](../app-service/deploy-content-sync.md)의 지침을 따릅니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 권장 합니다. 클라우드 동기화를 사용 하 여 게시 하는 경우 [수동으로 트리거를 동기화](#trigger-syncing)해야 합니다.

### <a name="ftp"></a>FTP

FTP를 사용 하 여 파일을 Azure Functions로 직접 전송할 수 있습니다.

>__사용 방법:__ [FTP/s를 사용 하 여 콘텐츠 배포](../app-service/deploy-ftp.md)의 지침을 따릅니다.

>__사용 시기:__ 일반적으로 다른 배포 방법을 권장 합니다. FTP를 사용 하 여 게시 하는 경우 [수동으로 트리거를 동기화](#trigger-syncing)해야 합니다.

### <a name="portal-editing"></a>포털 편집

포털 기반 편집기에서 함수 앱에 있는 파일 (기본적으로 변경 내용을 저장할 때마다 배포)을 직접 편집할 수 있습니다.

>__사용 방법:__ Azure Portal에서 함수를 편집 하려면 [포털에서 함수를 만들어야](functions-create-first-azure-function.md)합니다. 단일 원인을 보존 하기 위해 다른 배포 방법을 사용 하면 함수를 읽기 전용으로 설정 하 고 계속 해 서 포털을 편집할 수 없습니다. Azure Portal에서 파일을 편집할 수 있는 상태로 돌아가려면 편집 모드를 수동으로 다시 설정 하 `Read/Write` 고 배포 관련 응용 프로그램 설정 (예:)을 제거 하면 `WEBSITE_RUN_FROM_PACKAGE` 됩니다.

>__사용 시기:__ 포털은 Azure Functions를 시작 하는 좋은 방법입니다. 보다 강력한 개발 작업을 수행 하려면 다음 클라이언트 도구 중 하나를 사용 하는 것이 좋습니다.
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (명령줄)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

다음 표에서는 포털 편집을 지 원하는 운영 체제 및 언어를 보여 줍니다.

| 언어 | Windows 사용량 | Windows Premium | Windows 전용 | Linux 소비 | Linux 프리미엄 | Linux 전용 |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# 스크립트 |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript(Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python(미리 보기) | | | | | | |
| PowerShell(미리 보기) |✔|✔|✔| | | |
| TypeScript (Node.js) | | | | | | |

<sup>*</sup> 포털 편집은 프리미엄 및 전용 요금제를 사용 하는 Linux의 함수에 대 한 HTTP 및 타이머 트리거에 대해서만 사용할 수 있습니다.

## <a name="deployment-behaviors"></a>배포 동작

배포를 수행 하는 경우 모든 기존 실행이 완료 되거나 제한 시간이 초과 될 수 있으며, 그 후에는 새 코드가 로드 되어 요청 처리를 시작 합니다.

이 전환에 대해 더 많은 제어가 필요한 경우 배포 슬롯을 사용 해야 합니다.

## <a name="deployment-slots"></a>배포 슬롯

함수 앱을 Azure에 배포 하는 경우 프로덕션에 직접 배포 하는 대신 별도의 배포 슬롯에 배포할 수 있습니다. 배포 슬롯에 대 한 자세한 내용은 [Azure Functions 배포 슬롯](../app-service/deploy-staging-slots.md) 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

함수 앱을 배포 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)
+ [Azure DevOps를 사용한 지속적인 업데이트](functions-how-to-azure-devops.md)
+ [Azure Functions에 대 한 Zip 배포](deployment-zip-push.md)
+ [패키지에서 Azure Functions 실행](run-functions-from-deployment-package.md)
+ [Azure Functions에서 함수 앱에 대 한 리소스 배포 자동화](functions-infrastructure-as-code.md)
