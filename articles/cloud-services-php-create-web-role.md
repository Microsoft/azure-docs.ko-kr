---
title: PHP용 Azure 웹 및 작업자 역할 만들기
description: Azure 클라우드 서비스에서 PHP 웹 및 작업자 역할을 만들고 PHP 런타임을 구성하는 방법을 설명하는 가이드입니다.
services: ''
documentationcenter: php
author: msangapu-msft
manager: gwallace
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 1bf0ea35c51b063a7720a1542a23a49fbcdbc557
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88892986"
---
# <a name="create-php-web-and-worker-roles"></a>PHP 웹 및 작업자 역할 만들기

## <a name="overview"></a>개요

이 가이드는 Windows 개발 환경에서 PHP 웹이나 작업자 역할을 만들고, 사용 가능한 "기본 제공" 버전에서 특정 PHP 버전을 선택하여 PHP 구성을 변경하고, 확장을 사용하고, 마지막으로 Azure에 배포하는 방법을 보여 줍니다. 또한 사용자 지정 구성 및 확장으로 제공하는 PHP 런타임을 사용하도록 웹 또는 작업자 역할을 구성하는 방법도 설명합니다.

Azure는 애플리케이션을 실행하기 위한 세 가지 컴퓨팅 모델인 Azure App Service, Azure Virtual Machines 및 Azure Cloud Services를 제공합니다. 이 세 모델은 모두 PHP를 지원합니다. 웹 및 작업자 역할을 포함하는 Cloud Services는 *PaaS(Platform as a Service)* 를 제공합니다. Cloud Services 안에서 웹 역할은 프런트 엔드 웹 애플리케이션을 호스팅할 전용 IIS(인터넷 정보 서비스) 웹 서버를 제공합니다. 작업자 역할은 비동기, 장기 실행 또는 영구 작업을 사용자 조작 또는 입력과 독립적으로 실행할 수 있습니다.

이러한 옵션에 대한 자세한 내용은 [Azure에서 제공하는 Compute 호스팅 옵션](cloud-services/cloud-services-choose-me.md)을 참조하세요.

## <a name="download-the-azure-sdk-for-php"></a>PHP용 Azure SDK 다운로드(영문)

[PHP용 Azure SDK](https://github.com/Azure/azure-sdk-for-php)는 여러 구성 요소로 구성됩니다. 이 문서에서는 이러한 구성 요소 중 두 가지인 Azure PowerShell 및 Azure 에뮬레이터를 사용합니다. 이러한 두 구성 요소는 Microsoft 웹 플랫폼 설치 관리자를 통해 설치할 수 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요.

## <a name="create-a-cloud-services-project"></a>Cloud Services 프로젝트 만들기

PHP 웹 또는 작업자 역할을 만드는 첫 번째 단계는 Azure 서비스 프로젝트를 만드는 것입니다. Azure 서비스 프로젝트는 웹 및 작업자 역할의 논리 컨테이너 역할을 하며 프로젝트의 [서비스 정의(.csdef)] 및 [서비스 구성(.cscfg)] 파일이 포함되어 있습니다.

새 Azure 서비스 프로젝트를 만들려면 관리자로 Azure PowerShell을 실행하고 다음 명령을 실행합니다.

```powershell
PS C:\>New-AzureServiceProject myProject
```

이 명령은 웹 및 작업자 역할을 추가할 수 있는 새 디렉터리(`myProject`)를 만듭니다.

## <a name="add-php-web-or-worker-roles"></a>PHP 웹 또는 작업자 역할 추가

PHP 웹 역할을 프로젝트에 추가하려면 프로젝트의 루트 디렉터리에서 다음 명령을 실행합니다.

```powershell
PS C:\myProject> Add-AzurePHPWebRole roleName
```

작업자 역할의 경우에는 다음 명령을 사용합니다.

```powershell
PS C:\myProject> Add-AzurePHPWorkerRole roleName
```

> [!NOTE]
> `roleName` 매개 변수는 선택 사항입니다. 생략되면 역할 이름이 자동으로 생성됩니다. 첫 번째로 만들어진 웹 역할은 `WebRole1`, 두 번째는 `WebRole2`이 되는 식입니다. 첫 번째로 만들어진 작업자 역할은 `WorkerRole1`, 두 번째는 `WorkerRole2`이 되는 식입니다.
>
>

## <a name="use-your-own-php-runtime"></a>고유 PHP 런타임 사용

기본 제공 PHP 런타임을 선택하여 위 설명대로 구성하는 대신 고유 PHP 런타임을 제공할 경우도 있습니다. 예를 들어, 개발 환경에서 사용하는 웹 또는 작업자 역할에서 동일한 PHP 런타임을 사용할 수 있습니다. 이렇게 하면 더 쉽게 애플리케이션이 프로덕션 환경에서 동작을 변경하지 않게 할 수 있습니다.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>고유 PHP 런타임을 사용하도록 웹 역할 구성

사용자가 제공하는 PHP 런타임을 사용하도록 웹 역할을 구성하려면 아래 단계를 따릅니다.

1. 이 항목의 앞부분에서 설명한 대로 Azure 서비스 프로젝트를 만들고 PHP 웹 역할을 추가합니다.
2. 웹 역할의 루트 디렉터리에 있는 `bin` 폴더에 `php` 폴더를 만든 후 PHP 런타임(모든 바이너리, 구성 파일, 하위 폴더 등)을 `php` 폴더에 추가합니다.
3. (선택 사항) PHP 런타임이 [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers]를 사용하면 웹 역할이 프로비전될 때 [SQL Server Native Client 2012][sql native client]를 설치하도록 웹 역할을 구성해야 합니다. 이렇게 하려면 [sqlncli.msi x64 설치 관리자]를 웹 역할의 루트 디렉터리에 있는 `bin` 폴더에 추가합니다. 다음 단계에 설명되어 있는 시작 스크립트는 역할이 프로비전될 때 설치 관리자를 자동으로 실행합니다. PHP 런타임이 Microsoft Drivers for PHP for SQL Server를 사용하지 않으면 다음 단계의 스크립트에서 다음 줄을 제거할 수 있습니다.

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. PHP 런타임을 사용하여  페이지에 대한 요청을 처리하도록 [IIS(인터넷 정보 서비스)`.php`][iis.net]를 구성하는 시작 작업을 정의합니다. 이렇게 하려면 텍스트 편집기에서 `setup_web.cmd` 파일(웹 역할 루트 디렉터리의 `bin` 파일에 있음)을 열고 그 내용을 다음 스크립트로 바꿉니다.

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. 애플리케이션 파일을 웹 역할의 루트 디렉터리에 추가합니다. 그러면 웹 서버의 루트 디렉터리가 됩니다.
6. 아래 [애플리케이션 게시](#publish-your-application) 섹션에 설명된 대로 애플리케이션을 게시합니다.

> [!NOTE]
> `download.ps1` 스크립트(웹 역할 루트 디렉터리의 `bin` 폴더에 있음)는 고유 PHP 런타임을 사용하기 위해 위에 설명된 단계를 따른 후에 삭제할 수 있습니다.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>고유 PHP 런타임을 사용하도록 작업자 역할 구성

제공하는 PHP 런타임을 사용하도록 작업자 역할을 구성하려면 다음 단계를 따르세요.

1. 이 항목의 앞부분에서 설명한 대로 Azure 서비스 프로젝트를 만들고 PHP 작업자 역할을 추가합니다.
2. 작업자 역할의 루트 디렉터리에 `php` 폴더를 만든 후 PHP 런타임(모든 바이너리, 구성 파일, 하위 폴더 등)을 `php` 폴더에 추가합니다.
3. (선택 사항) PHP 런타임이 [Microsoft Drivers for PHP for SQL Server][sqlsrv drivers]를 사용하면 작업자 역할이 프로비전될 때 [SQL Server Native Client 2012][sql native client]를 설치하도록 작업자 역할을 구성해야 합니다. 이렇게 하려면 [sqlncli.msi x64 설치 관리자]를 작업자 역할의 루트 디렉터리에 추가합니다. 다음 단계에 설명되어 있는 시작 스크립트는 역할이 프로비전될 때 설치 관리자를 자동으로 실행합니다. PHP 런타임이 Microsoft Drivers for PHP for SQL Server를 사용하지 않으면 다음 단계의 스크립트에서 다음 줄을 제거할 수 있습니다.

   ```console
   msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
   ```

4. 역할이 프로비전될 때 `php.exe` 실행 파일을 작업자 역할의 PATH 환경 변수에 추가하는 시작 작업을 정의합니다. 이렇게 하려면 텍스트 편집기에서 `setup_worker.cmd` 파일(작업자 역할의 루트 디렉터리에 있음)을 열고 그 내용을 다음 스크립트로 바꿉니다.

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. 애플리케이션 파일을 작업자 역할의 루트 디렉터리에 추가합니다.
6. 아래 [애플리케이션 게시](#publish-your-application) 섹션에 설명된 대로 애플리케이션을 게시합니다.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>컴퓨팅 및 스토리지 에뮬레이터에서 애플리케이션 실행

Azure 에뮬레이터는 클라우드에 배포하기 전에 Azure 애플리케이션을 테스트할 수 있는 로컬 환경을 제공합니다. 에뮬레이터와 Azure 환경 사이에는 약간의 차이가 있습니다. 이를 더 잘 이해 하려면 [개발 및 테스트에 Azure Storage 에뮬레이터 사용](storage/common/storage-use-emulator.md)을 참조 하세요.

컴퓨팅 에뮬레이터를 사용하려면 PHP를 로컬로 설치해야 합니다. 컴퓨팅 에뮬레이터는 로컬 PHP 설치를 사용하여 애플리케이션을 실행합니다.

에뮬레이터에서 프로젝트를 실행하려면 프로젝트의 루트 디렉터리에서 다음 명령을 실행합니다.

```powershell
PS C:\MyProject> Start-AzureEmulator
```

다음과 유사한 출력이 표시됩니다.

```output
Creating local package...
Starting Emulator...
Role is running at http://127.0.0.1:81
Started
```

웹 브라우저를 열고 출력에 표시된 로컬 주소(위 예제 출력의 `http://127.0.0.1:81`)로 이동하면 에뮬레이터에서 실행 중인 애플리케이션을 볼 수 있습니다.

에뮬레이터를 중지하려면 다음 명령을 실행합니다.

```powershell
PS C:\MyProject> Stop-AzureEmulator
```

## <a name="publish-your-application"></a>애플리케이션 게시

애플리케이션을 게시하려면 먼저 [Import-AzurePublishSettingsFile](/powershell/module/servicemanagement/azure.service/import-azurepublishsettingsfile) cmdlet을 사용하여 게시 설정을 가져와야 합니다. 그런 다음 [Publish-AzureServiceProject](/powershell/module/servicemanagement/azure.service/publish-azureserviceproject) cmdlet을 사용하여 애플리케이션을 게시합니다. 로그인에 대한 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/)을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)를 참조하세요.

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[서비스 정의 (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[서비스 구성 (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[sqlncli.msi x64 설치 관리자]: https://go.microsoft.com/fwlink/?LinkID=239648
