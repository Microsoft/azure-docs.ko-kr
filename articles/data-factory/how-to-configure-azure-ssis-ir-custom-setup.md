---
title: Azure-SSIS 통합 런타임을 위한 사용자 지정 설치
description: 이 문서에서는 추가 구성 요소나 변경 설정을 설치하려면 Azure SSIS 통합 런타임에 사용자 지정 설정 인터페이스를 사용하는 방법을 설명함
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/01/2020
ms.openlocfilehash: e85ef22542fc162648dbfc637892cf7e580c6aac
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964552"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임을 위한 사용자 지정 설치

Azure-SSIS IR(Integration Runtime)에 대한 사용자 지정 설정 인터페이스는 Azure-SSIS IR의 프로비전 또는 재구성 중에 자체 설정 단계를 추가하는 인터페이스를 제공합니다. 

사용자 지정 설정을 사용하면 기본 운영 구성이나 환경을 변경하거나(예: 추가 Windows 서비스 시작 또는 파일 공유에 대한 액세스 자격 증명 유지) 추가 구성 요소(예: 어셈블리, 드라이버, 확장)를 Azure-SSIS IR의 각 노드에 설치할 수 있습니다.

Azure-SSIS IR에서 사용자 지정 설정 작업을 수행 하는 두 가지 방법이 있습니다. 스크립트를 사용 하지 않고 사용자 지정 설정 및 스크립트를 사용 하는 표준 사용자 지정 설정입니다.

Express 사용자 지정 설치를 사용 하면 몇 가지 일반적인 시스템 구성/w i n s 명령을 실행 하거나 스크립트를 사용 하지 않고 널리 사용 되는/권장 추가 구성 요소를 설치할 수 있습니다.  

표준 사용자 지정 프로그램을 사용 하는 경우 스크립트 및 관련 파일을 준비 하 고 모두 Azure Storage 계정의 blob 컨테이너에 업로드 해야 합니다. 그런 다음 Azure-SSIS IR를 프로 비전 하거나 다시 구성할 때 컨테이너에 대한 SAS (공유 액세스 서명) URI (Uniform Resource Identifier)를 제공 합니다. 그러면 Azure-SSIS IR의 각 노드가 컨테이너에서 스크립트 및 관련 파일을 다운로드 하 고 상승 된 권한으로 사용자 지정 설치를 실행 합니다. 사용자 지정 설치가 완료 되 면 각 노드는 실행의 표준 출력과 기타 로그를 컨테이너에 업로드 합니다.

Express/standard 사용자 지정 설치를 사용 하 여 무료/사용 허가 및 유료/라이선스 구성 요소를 모두 설치할 수 있습니다. ISV 인 경우 [Azure-SSIS IR에 대해 유료 또는 라이선스 구성 요소를 개발 하는 방법](how-to-develop-azure-ssis-ir-licensed-components.md)에 대한 설명서를 참조 하세요.

> [!IMPORTANT]
> Azure-SSIS IR의 v2 시리즈 노드는 사용자 지정 설치에 적합하지 않으므로 v3 시리즈 노드를 대신 사용합니다.  이미 v2 시리즈 노드를 사용하고 있으면 가능한 한 빨리 v3 시리즈 노드를 사용하도록 전환하세요.

## <a name="current-limitations"></a>현재 제한 사항

표준 사용자 지정에는 다음 제한 사항이 적용 됩니다.

- 스크립트에서 `gacutil.exe`를 사용 하 여 GAC (전역 어셈블리 캐시)에 어셈블리를 설치 하려면 사용자 지정 설치의 일부로 `gacutil.exe`를 제공 하거나 아래의 공개 미리 보기 컨테이너에 제공 된 복사본을 사용 해야 합니다.

- 스크립트에서 하위 폴더를 참조하려는 경우 `msiexec.exe`는 루프 폴더를 참조하도록 `.\` 표기법을 지원하지 않습니다. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` 대신 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`와 같은 명령을 사용합니다.

- Windows에서 자동으로 만든 숨겨진 네트워크 공유와 같은 관리 공유는 현재 Azure-SSIS IR에서 지원 되지 않습니다.

- IBM iSeries Access ODBC 드라이버는 Azure-SSIS IR에서 지원 되지 않습니다. 사용자 지정 설치 중에 설치 오류가 표시 될 수 있습니다. 지원에 대해서는 IBM 지원에 문의 하세요.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure SSIS IR을 사용자 지정하려면 다음 항목이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/)

- [Azure-SSIS IR 프로 비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure Storage 계정](https://azure.microsoft.com/services/storage/) Express 사용자 지정에는 필요 하지 않습니다. 표준 사용자 지정 설치의 경우 사용자 지정 설치 스크립트와 관련 파일을 blob 컨테이너에 업로드 하 고 저장 합니다. 사용자 지정 설정 프로세스에서는 실행 로그도 같은 Blob 컨테이너에 업로드합니다.

## <a name="instructions"></a>Instructions

1. PowerShell을 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하려면 [Azure PowerShell](/powershell/azure/install-az-ps)를 다운로드 하 여 설치 합니다. Express 사용자 지정의 경우 4 단계로 건너뜁니다.

1. 사용자 지정 설정 스크립트 및 관련 파일(예: .bat, .cmd, .exe, .dll, .msi, 또는 .ps1 파일)을 준비합니다.

   1. 사용자 지정 설정의 진입점 역할을 하는 이름이 `main.cmd`인 스크립트 파일이 필요합니다.

   1. 스크립트를 자동으로 실행할 수 있는지 확인 해야 합니다. 먼저 로컬 컴퓨터에서 스크립트를 테스트 하는 것이 좋습니다.

   1. 다른 도구에서 생성한 추가 로그(예: `msiexec.exe`)를 컨테이너에 업로드하려는 경우 미리 정의된 환경 변수 `CUSTOM_SETUP_SCRIPT_LOG_DIR`을 스크립트에 로그 폴더로 지정합니다(예:  `msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`).

1. [Azure Storage Explorer](https://storageexplorer.com/)를 다운로드 및 설치하고 시작합니다.

   1. **(로컬 및 연결)** 에서 **스토리지 계정**을 마우스 오른쪽 단추로 선택하고 **Azure Storage에 연결**을 선택합니다.

      ![Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. **스토리지 계정 이름과 키 사용**, **다음**을 차례로 선택합니다.

      ![스토리지 계정 이름 및 키 사용](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. Azure Storage 계정 이름과 키를 입력하고 **다음**을 선택하고 **연결**을 선택합니다.

      ![저장소 계정 이름 및 키 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. 연결된 Azure Storage 계정에서 **Blob 컨테이너**를 마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기**를 선택한 다음, 새 컨테이너의 이름을 지정합니다.

      ![Blob 컨테이너 만들기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. 새 컨테이너를 선택하고 사용자 지정 설정 스크립트와 관련 파일을 업로드합니다. 폴더가 아니라 컨테이너의 최상위 수준에 `main.cmd`를 업로드해야 합니다. 또한 컨테이너에 필요한 사용자 지정 설치 파일만 포함되어 있는지 확인합니다. 그러면 나중에 Azure-SSIS IR에 다운로드하는 데 시간이 오래 걸리지 않습니다. 사용자 지정 설치에 대한 최대 기간은 현재 45 분 전에 분에 설정 되어 있으며 컨테이너에서 모든 파일을 다운로드 하 여 Azure-SSIS IR에 설치 하는 시간을 포함 합니다. 더 긴 기간이 필요한 경우 지원 티켓을 제기 하세요.

      ![Blob 컨테이너에 파일 업로드](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. 컨테이너를 마우스 오른쪽 단추로 클릭하고 **공유 액세스 서명 가져오기**를 선택합니다.

      ![컨테이너에 대한 공유 액세스 서명 가져오기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. 충분히 긴 만료 시간을 지정하고 읽기 + 쓰기 + 나열 권한이 있는 컨테이너에 대한 SAS URI를 만듭니다. Azure-SSIS IR 노드의 이미지를 다시 만들고/다시 시작할 때마다 사용자 지정 설정 스크립트 및 관련 파일을 다운로드하여 실행하기 위해 SAS URI가 필요합니다. 설정 실행 로그를 업로드하려면 쓰기 권한이 필요합니다.

      > [!IMPORTANT]
      > SAS URI가 만료 되지 않고 사용자 지정 설치 리소스는 Azure-SSIS IR의 전체 수명 주기 동안, 특히이 기간 동안 정기적으로 Azure-SSIS IR를 중지 하 고 시작 하는 경우에는 사용자 지정 설치 리소스를 항상 사용할 수 있는지 확인 하세요.

      ![컨테이너에 대한 공유 액세스 서명 생성](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. 컨테이너의 SAS URI를 복사하여 저장합니다.

      ![공유 액세스 서명 복사 및 저장](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Data Factory UI를 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하는 경우 Integration Runtime 설정 패널의 **고급 설정** 섹션에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자** 지정 확인란을 선택 하 여 사용자 지정 설치를 추가/제거할 수 있습니다. 

   표준 사용자 지정 설치를 추가 하려면 **사용자 지정 설치 컨테이너 SAS uri** 필드에 컨테이너의 sas uri를 입력 합니다. 
   
   Express 사용자 지정 설치를 추가 하려면 **새로 만들기** 를 선택 하 여 **빠른 사용자 지정 설정 추가** 패널을 열고 **express 사용자 지정 설치 유형** 드롭다운 메뉴에서 유형을 선택 합니다.

   1. **Cmdkey 실행 명령** 유형을 선택 하는 경우 **/Pass** **필드에 대상**컴퓨터/도메인 이름, 계정 이름/사용자 이름 및 계정 키/암호를 입력 하 여 Azure-SSIS IR에 대한 파일 공유/Azure Files에 대한 액세스 자격 증명을 유지할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 명령을 실행 하는 것과 유사 합니다.
   
   1. 환경 변수 형식 **추가** 를 선택 하는 경우 **변수 이름** 및 **변수 값** 필드에 각각 환경 변수 이름과 값을 입력 하 여 Azure-SSIS IR에서 실행 되는 패키지에 사용할 Windows 환경 변수를 추가할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) 명령을 실행 하는 것과 유사 합니다.

   1. **사용 허가를 받은 구성 요소** 유형을 선택 하는 경우 **구성 요소 이름** 드롭다운 메뉴의 ISV 파트너에서 통합 된 구성 요소를 선택할 수 있습니다.

      1. **Sentryone의 작업 팩터리** 구성 요소를 선택 하는 경우 **라이선스 키** 필드에서 구매한 제품 라이선스 키를 입력 하 여 Azure-SSIS IR의 [작업 팩터리](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 제품군을 사용자의 컴퓨터에 설치할 수 있습니다. 현재 통합 버전은 **2019.4.3**입니다.

      1. Oh22's HEDDA를 선택 하는 경우  **IO** 구성 요소 HEDDA를 설치할 수 있습니다 [. ](https://hedda.io/ssis-component/)서비스를 구매한 후 Azure-SSIS IR에서 oh22의 IO 데이터 품질/정리 구성 요소 현재 통합 버전은 **1.0.13**입니다.

      1. **Oh22's SQLPhonetics.NET** 구성 요소를 선택 하는 경우 **라이선스 키** 필드에서 구매한 제품 라이선스 키를 입력 하 여 Azure-SSIS IR oh22에서 [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) 데이터 품질/일치 구성 요소를 설치할 수 있습니다. 현재 통합 버전은 **1.0.43**입니다.
   
   추가 된 빠른 사용자 지정 설치가 **고급 설정** 섹션에 표시 됩니다. 이러한 항목을 제거 하려면 해당 확인란을 선택한 다음 **삭제**를 선택 하면 됩니다.

   ![사용자 지정 설정으로 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. PowerShell을 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하는 경우 Azure-SSIS IR를 시작 하기 전에 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet을 실행 하 여 사용자 지정 프로그램을 추가/제거할 수 있습니다.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   표준 사용자 지정 설치를 완료 하 고 Azure-SSIS IR를 시작 하면 저장소 컨테이너의 `main.cmd.log` 폴더에서 `main.cmd` 및 기타 실행 로그의 표준 출력을 찾을 수 있습니다.

1. 표준 사용자 지정의 일부 샘플을 보려면 Azure Storage 탐색기를 사용 하 여 공개 미리 보기 컨테이너에 연결 합니다.

   1. **(로컬 및 연결)** 에서 **스토리지 계정**을 마우스 오른쪽 단추로 클릭하고 **Azure Storage에 연결**을 선택한 다음, **연결 문자열 또는 공유 액세스 서명 URI**를 선택하고 **다음**을 선택합니다.

      ![공유 액세스 서명을 사용하여 Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. **SAS URI 사용**을 선택하고 공개 미리 보기 컨테이너에 다음 SAS URI를 입력합니다. **다음**을 선택 하 고 **연결**을 선택 합니다.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![컨테이너에 공유 액세스 서명 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. 연결된 공개 미리 보기 컨테이너를 선택하고 `CustomSetupScript` 폴더를 두 번 클릭합니다. 이 폴더에는 다음 항목이 있습니다.

      1. `Sample` 폴더: Azure-SSIS IR의 각 노드에 기본 작업을 설치하는 사용자 지정 설정을 포함합니다. 이 작업은 몇 초 동안 대기하면서 아무 것도 수행하지 않습니다. 또한 폴더에는 `gacutil` 폴더와 컨테이너에 있는 그대로 복사할 수 있는 전체 콘텐츠(`gacutil.exe`, `gacutil.exe.config` 및 `1033\gacutlrc.dll`)가 포함됩니다.

      1. 실제 사용자 시나리오의 여러 사용자 지정 설정 예제를 포함 하는 `UserScenarios` 폴더

      ![공개 미리 보기 컨테이너의 콘텐츠](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. `UserScenarios` 폴더를 두 번 클릭 하 여 다음 항목을 찾습니다.

      1. `.NET FRAMEWORK 3.5` 폴더: Azure SSIS IR의 각 노드에서 사용자 지정 구성 요소에 필요할 수 있는 이전 버전의 .NET Framework를 설치하기 위한 사용자 지정 설치를 포함합니다.

      1. `BCP` 폴더: 대량 복사 프로그램(`bcp`) 등, Azure-SSIS IR의 각 노드에 SQL Server 명령줄 유틸리티를 설치하기 위한 사용자 지정 설정을 포함합니다(`MsSqlCmdLnUtils.msi`).

      1. `EXCEL` 폴더-Azure-SSIS IR의 각 노드에서 Excel 파일을 동적으로 읽고 쓰기 위해 C# 스크립트 태스크에서 사용할 수 있는 어셈블리/라이브러리를 설치 하기 위한 사용자 지정 설치 스크립트 (`main.cmd`)가 포함 되어 있습니다. 먼저 [여기에서 `ExcelDataReader.dll`](https://www.nuget.org/packages/ExcelDataReader/) 를 다운로드 하 고 [여기](https://www.nuget.org/packages/DocumentFormat.OpenXml/)에서 `DocumentFormat.OpenXml.dll` 하 고 컨테이너에 `main.cmd`와 함께 모두 업로드 합니다. 또는 표준 Excel 연결 관리자/원본/대상만 사용 하려는 경우에는 필요한 액세스 재배포 가능 패키지가 이미 Azure-SSIS IR에 미리 설치 되어 있으므로 사용자 지정 설정이 필요 하지 않습니다.
      
      1. Azure-SSIS IR의 각 노드에 MySQL ODBC 드라이버를 설치 하는 사용자 지정 설치 스크립트 (`main.cmd`)가 포함 된 `MYSQL ODBC` 폴더 이 설정을 사용 하면 ODBC 연결 관리자/원본/대상을 사용 하 여 MySQL server에 연결할 수 있습니다. 먼저 mysql ODBC 드라이버 설치 관리자의 최신 64 비트 및 32 비트 버전을 다운로드 합니다 (예: `mysql-connector-odbc-8.0.13-winx64.msi` 및 `mysql-connector-odbc-8.0.13-win32.msi` [mysql](https://dev.mysql.com/downloads/connector/odbc/)) 다운로드 한 다음 `main.cmd`와 함께 모두 컨테이너에 업로드 합니다.

      1. `ORACLE ENTERPRISE` 폴더: Azure-SSIS IR Enterprise Edition의 각 노드에 Oracle 커넥터 및 OCI 드라이버를 설치하기 위한 사용자 지정 설정 스크립트(`main.cmd`) 및 자동 설치 구성 파일(`client.rsp`)을 포함합니다. 이 설정을 사용 하면 oracle 연결 관리자/원본/대상을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 먼저, [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=55179)에서 Oracle용 Microsoft Connectors v5.0(`AttunitySSISOraAdaptersSetup.msi` 및 `AttunitySSISOraAdaptersSetup64.msi`), [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)에서 최신 Oracle 클라이언트(예: `winx64_12102_client.zip`)를 다운로드한 다음, `main.cmd` 및 `client.rsp`를 사용하여 모두 컨테이너에 업로드합니다. TNS를 사용하여 Oracle에 연결할 경우, 설정 중에 Oracle 설치 폴더에 복사될 수 있게 `tnsnames.ora`도 다운로드하여 편집하고 컨테이너에 업로드해야 합니다.

      1. `ORACLE STANDARD ADO.NET` 폴더: Azure-SSIS IR의 각 노드에 Oracle ODP.NET 드라이버를 설치하는 사용자 지정 설정 스크립트(`main.cmd`)를 포함합니다. 이 설정을 사용 하면 ADO.NET 연결 관리자/원본/대상을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 먼저 최신 Oracle ODP.NET 드라이버 (예: [oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)에서 `ODP.NET_Managed_ODAC122cR1.zip`)를 다운로드 한 다음 `main.cmd`와 함께 컨테이너에 업로드 합니다.
       
      1. `ORACLE STANDARD ODBC` 폴더: Azure-SSIS IR의 각 노드에 Oracle ODBC 드라이버를 설치하고 DSN을 구성하는 사용자 지정 설치 스크립트(`main.cmd`)가 포함되어 있습니다. 이 설치를 통해 ODBC 연결 관리자/원본/대상 또는 ODBC 데이터 원본 종류가 있는 파워 쿼리 연결 관리자/원본을 사용하여 Oracle 서버에 연결할 수 있습니다. 먼저 최신 Oracle 인스턴트 클라이언트 (기본 패키지 또는 기본 Lite 패키지)와 ODBC 패키지 (예:)를 다운로드 합니다. [여기](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (기본 패키지: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`, 기본 lite 패키지: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`, odbc 패키지: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`) 또는 여기에서 32 비트 [패키지 (기본](https://www.oracle.com/technetwork/topics/winsoft-085727.html) 패키지: `instantclient-basic-nt-18.3.0.0.0dbru.zip`, 기본 lite 패키지: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`, odbc 패키지: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`)의 64 비트 패키지를 컨테이너에 `main.cmd` 함께 업로드 합니다.

      1. `ORACLE STANDARD OLEDB` 폴더-Azure-SSIS IR의 각 노드에 Oracle OLEDB 드라이버를 설치 하기 위한 사용자 지정 설치 스크립트 (`main.cmd`)가 포함 되어 있습니다. 이 설정을 사용 하면 OLEDB 연결 관리자/원본/대상을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 먼저 최신 Oracle OLEDB 드라이버 (예: [oracle](https://www.oracle.com/partners/campaign/index-090165.html)에서 `ODAC122010Xcopy_x64.zip`)를 다운로드 한 다음 `main.cmd`와 함께 컨테이너에 업로드 합니다.

      1. `POSTGRESQL ODBC` 폴더-Azure-SSIS IR의 각 노드에 PostgreSQL ODBC 드라이버를 설치 하기 위한 사용자 지정 설치 스크립트 (`main.cmd`)가 포함 되어 있습니다. 이 설정을 사용 하면 ODBC 연결 관리자/원본/대상을 사용 하 여 PostgreSQL 서버에 연결할 수 있습니다. 먼저 PostgreSQL ODBC 드라이버 설치 관리자 (예: `psqlodbc_x64.msi` 및 `psqlodbc_x86.msi`)의 최신 64 비트 및 32 비트 [버전을 다운로드](https://www.postgresql.org/ftp/odbc/versions/msi/)한 다음, `main.cmd`와 함께 모두 컨테이너에 업로드 합니다.

      1. `SAP BW` 폴더-Azure-SSIS IR Enterprise Edition의 각 노드에 SAP .NET 커넥터 어셈블리 (`librfc32.dll`)를 설치 하기 위한 사용자 지정 설치 스크립트 (`main.cmd`)가 포함 되어 있습니다. 이 설정을 사용 하면 SAP BW 연결 관리자/원본/대상을 사용 하 여 SAP BW 서버에 연결할 수 있습니다. 먼저 `librfc32.dll`의 64 비트 또는 32 비트 버전을 SAP 설치 폴더에서 `main.cmd`와 함께 컨테이너에 업로드 합니다. 그러면 설정 중에 스크립트나 SAP 어셈블리를 `%windir%\SysWow64` 또는 `%windir%\System32` 폴더에 복사합니다.

      1. `STORAGE` 폴더: Azure-SSIS IR의 각 노드에 Azure PowerShell을 설치하는 사용자 지정 설정을 포함합니다. 이 설정을 통해 [PowerShell 스크립트를 실행하여 Azure Storgae 계정을 조작하는](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell) SSIS 패키지를 배포 및 실행할 수 있습니다. `main.cmd`, 샘플 `AzurePowerShell.msi` (또는 최신 버전 사용)를 복사 하 고 컨테이너에 `storage.ps1` 합니다. 패키지에 대해 PowerShell.dtsx를 템플릿으로 사용합니다. 패키지 템플릿은 수정 가능한 PowerShell 스크립트로 `storage.ps1`을 다운로드하는 [Azure Blob 다운로드 작업](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)과, 각 노드에서 스크립트를 실행하는 [프로세스 실행 작업](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)을 결합합니다.

      1. `TERADATA` 폴더: 사용자 지정 설정 스크립트(`main.cmd`), 관련 파일(`install.cmd`) 및 설치 관리자 패키지(`.msi`)를 포함합니다. 이러한 파일은 Azure-SSIS IR Enterprise Edition의 각 노드에 Teradata 커넥터, TPT API 및 ODBC 드라이버를 설치 합니다. 이 설정을 사용 하면 teradata 연결 관리자/원본/대상을 사용 하 여 Teradata 서버에 연결할 수 있습니다. 먼저 teradata에서 Teradata Tools 및 유틸리티 4.x zip 파일 (예: `TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip`[)을 다운로드](http://partnerintelligence.teradata.com)한 다음 위의 `.cmd`와 함께 업로드 하 고 파일을 컨테이너에 `.msi` 합니다.

      1. `ZULU OPENJDK` 폴더-Azure-SSIS IR의 각 노드에 줄루어 OpenJDK를 설치 하기 위한 사용자 지정 설치 스크립트 (`main.cmd`) 및 PowerShell 파일 (`install_openjdk.ps1`)이 포함 되어 있습니다. 이 설정을 사용 하면 Azure Data Lake Store/유연한 파일 커넥터를 사용 하 여 ORC/Parquet 파일을 처리할 수 있습니다. 자세한 내용은 [여기](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) 를 참조 하세요. 먼저 최신 줄루어 OpenJDK (예: `zulu8.33.0.1-jdk8.0.192-win_x64.zip` [)를 다운로드](https://www.azul.com/downloads/zulu/zulu-windows/)한 다음 `main.cmd`와 함께 업로드 하 고 컨테이너에 `install_openjdk.ps1` 합니다.

      ![사용자 시나리오 폴더의 폴더](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. 이러한 사용자 지정 설치 샘플을 시도 하려면 선택한 폴더의 콘텐츠를 복사 하 여 컨테이너에 붙여 넣습니다.
   
      Data Factory UI를 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하는 경우 **고급 설정** 섹션에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자** 지정 확인란을 선택 하 고 **사용자 지정 설치 컨테이너 sas URI** 필드에 컨테이너의 SAS uri를 입력 합니다.
   
      PowerShell을 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하는 경우 `SetupScriptContainerSasUri` 매개 변수의 값으로 컨테이너의 SAS URI를 사용 하 여 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet을 실행 합니다.

## <a name="next-steps"></a>다음 단계

-   [Azure-SSIS Integration Runtime Enterprise 버전](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Azure-SSIS Integration Runtime에 대한 유료 또는 라이선스 사용자 지정 구성 요소를 개발하는 방법](how-to-develop-azure-ssis-ir-licensed-components.md)
