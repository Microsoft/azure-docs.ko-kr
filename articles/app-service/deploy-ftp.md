---
title: FTP/S를 사용 하 여 콘텐츠 배포
description: FTP 또는 FTPS를 사용하여 Azure App Service에 앱을 배포하는 방법을 알아봅니다. 암호화 되지 않은 FTP를 사용 하지 않도록 설정 하 여 웹 사이트 보안 향상
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 7bc637b5719da3c5f5e5607436aa7da0721f5a9e
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680927"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>FTP/S를 사용하여 앱에 Azure App Service에 배포

이 문서는 FTP 또는 FTPS를 사용하여 웹앱, 모바일 앱 백 엔드 또는 API 앱을 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에 배포하는 방법을 보여 줍니다.

앱에 대한 FTP/S 엔드포인트는 이미 활성화되어 있습니다. FTP/S 배포를 사용하도록 설정하는 데 필요한 구성은 없습니다.

## <a name="open-ftp-dashboard"></a>FTP 대시보드 열기

1. [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색 하 고 선택 합니다.

    ![App services를 검색 합니다.](media/app-service-continuous-deployment/search-for-app-services.png)

2. 배포 하려는 웹 앱을 선택 합니다.

    ![앱을 선택 합니다.](media/app-service-continuous-deployment/select-your-app.png)

3. **배포 센터** > **FTP** > **대시보드**를 선택 합니다.

    ![FTP 대시보드 열기](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>FTP 연결 정보 가져오기

FTP 대시보드에서 **복사** 를 선택 하 여 FTPS 엔드포인트 및 앱 자격 증명을 복사 합니다.

![FTP 정보 복사](./media/app-service-deploy-ftp/ftp-dashboard.png)

각 앱에 고유하기 때문에 사용자 앱에 배포하려면 **앱 자격 증명**을 사용하는 것이 좋습니다. 단, **사용자 자격 증명**을 클릭하는 경우 구독에서 모든 App Service 앱에 대한 FTP/S 로그인에 사용할 수 있는 사용자 수준의 자격 증명을 설정할 수 있습니다.

> [!NOTE]
> 사용자 수준 자격 증명을 사용 하 여 FTP/FTPS 엔드포인트을 인증 하는 경우 사용자 이름은 다음 형식으로 requirers 됩니다. 
>
>`<app-name>\<user-name>`
>
> 사용자 수준 자격 증명은 특정 리소스가 아니라 사용자에 게 연결 되기 때문에 로그인 작업을 올바른 앱 엔드포인트으로 보내기 위해 사용자 이름이이 형식 이어야 합니다.
>

## <a name="deploy-files-to-azure"></a>Azure에 파일 배포

1. FTP 클라이언트(예: [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/) 또는 [WinSCP](https://winscp.net/index.php))에서 수집한 연결 정보를 사용하여 앱에 연결합니다.
2. 파일 및 해당 디렉터리 구조를 Azure의 [ **/site/wwwroot** 디렉터리](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure)(또는 WebJobs의 경우 **/site/wwwroot/App_Data/Jobs/** 디렉터리)에 복사합니다.
3. 앱의 URL을 찾아 앱이 제대로 실행하는지 확인합니다. 

> [!NOTE] 
> [Git 기반 배포](deploy-local-git.md)와 달리, FTP 배포에서는 다음과 같은 배포 자동화를 지원하지 않습니다. 
>
> - 종속성 복원(예: NuGet, NPM, PIP 및 Composer Automation)
> - .NET 이진 파일 컴파일
> - web.config 생성([Node.js 예제](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps) 참조)
> 
> 로컬 컴퓨터에서 이러한 필요한 파일을 수동으로 생성한 후 앱과 함께 배포합니다.
>

## <a name="enforce-ftps"></a>FTPS 적용

보안 향상을 위해 SSL을 통한 FTP만 허용해야 합니다. FTP 배포를 사용하지 않을 경우 FTP 및 FTPS를 둘 다 사용하지 않도록 설정할 수도 있습니다.

[Azure Portal](https://portal.azure.com)의 앱 리소스 페이지에서 왼쪽 탐색 영역에 있는 **구성** > **일반 설정** 을 선택 합니다.

암호화 되지 않은 FTP를 사용 하지 않도록 설정 하려면 **ftp 상태** **에서만 FTPS** 를 선택 합니다. FTP와 FTPS를 모두 사용 하지 않도록 설정 하려면 **사용 안 함**을 선택 합니다. 완료되면 **저장**을 클릭합니다. **FTPS만**사용 하는 경우에는 웹 앱의 **tls/SSL 설정** 블레이드로 이동 하 여 tls 1.2 이상을 적용 해야 합니다. TLS 1.0 및 1.1은 **FTPS만**으로 지원되지 않습니다.

![FTP/S 사용 안 함](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure)를 사용한 FTP 배포의 경우 [웹앱 만들기 및 FTP를 사용하여 파일 배포(Azure CLI)](./scripts/cli-deploy-ftp.md)를 참조하세요.

[Azure PowerShell](/cli/azure)을 사용한 FTP 배포의 경우 [FTP를 사용하여 웹앱에 파일 업로드(PowerShell)](./scripts/powershell-deploy-ftp.md)를 참조하세요.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>FTP 배포 문제 해결

- [FTP 배포 문제를 어떻게 해결할 수 있나요?](#how-can-i-troubleshoot-ftp-deployment)
- [FTP를 수행할 수 없으며 내 코드를 게시할 수 없습니다. 문제를 해결 하려면 어떻게 해야 하나요?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>FTP 배포 문제를 어떻게 해결할 수 있나요?

FTP 배포 문제를 해결하는 첫 번째 단계는 런타임 애플리케이션 문제에서 배포 문제를 격리하는 것입니다.

배포 문제가 발생하면 일반적으로 앱에 파일이 배포되지 않거나 잘못된 파일이 배포됩니다. FTP 배포를 조사하거나 대체 배포 경로(예: 소스 제어)를 선택하여 문제를 해결할 수 있습니다.

런타임 애플리케이션 문제가 발생하면 일반적으로 앱에 올바른 파일 집합이 배포되기는 하지만 앱이 올바르지 않게 동작합니다. 런타임의 코드 동작에 중점을 두고 구체적인 실패 경로를 조사하여 문제를 해결할 수 있습니다.

배포 또는 런타임 문제를 확인하려면 [배포 문제 및 런타임 문제](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)를 참조하세요.

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>FTP를 수행할 수 없으며 코드를 게시할 수 없습니다. 이 문제는 어떻게 해결할 수 있나요?
올바른 호스트 이름 및 [자격 증명](#open-ftp-dashboard)을 입력했는지 확인합니다. 또한 컴퓨터의 다음 FTP 포트가 방화벽에 의해 차단되지 않는지 확인합니다.

- FTP 제어 연결 포트: 21
- FTP 데이터 연결 포트: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>수동 모드를 통해 Azure App Service에서 FTP에 연결하려면 어떻게 해야 하나요?
Azure App Service는 활성 및 수동 모드를 통한 연결을 모두 지원합니다. 배포 컴퓨터는 일반적으로 방화벽 뒤에 있으므로(운영 체제에서 또는 가정용/회사 네트워크의 일부로) 수동 모드가 선호됩니다. [WinSCP 설명서의 예제](https://winscp.net/docs/ui_login_connection)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

고급 배포 시나리오에 대해서는 [Git를 사용하여 Azure에 배포](deploy-local-git.md)를 시도하세요. Azure로의 Git 기반 배포를 수행하면 버전 제어, 패키지 복원, MSBuild 등을 수행할 수 있습니다.

## <a name="more-resources"></a>추가 리소스

* [Azure App Service 배포 자격 증명](deploy-configure-credentials.md)
