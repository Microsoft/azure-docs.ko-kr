---
title: Azure-SSIS Integration Runtime에 대 한 설정 사용자 지정
description: 이 문서에서는 Azure-SSIS Integration Runtime에 대 한 사용자 지정 설치 인터페이스를 사용 하 여 추가 구성 요소를 설치 하거나 설정을 변경 하는 방법을 설명 합니다.
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
ms.date: 02/14/2020
ms.openlocfilehash: 9c084564fec3faf59317fe9e05f3e850a38454d6
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251977"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime에 대 한 설정 사용자 지정

Azure SQL Server Integration Services Integration Runtime (Azure-SSIS IR)의 사용자 지정 설정은 Azure-SSIS IR를 설치 하거나 재구성 하는 동안 사용자 고유의 단계를 추가 하기 위한 인터페이스를 제공 합니다. 

사용자 지정 설치 프로그램을 사용 하 여 기본 운영 구성 또는 환경을로 변경할 수 있습니다. 예를 들어 추가 Windows 서비스를 시작 하거나 파일 공유에 대 한 액세스 자격 증명을 유지할 수 있습니다. 또는 Azure-SSIS IR의 각 노드에 어셈블리, 드라이버, 확장 등의 추가 구성 요소를 설치할 수 있습니다.

다음 두 가지 방법 중 하나로 Azure-SSIS IR에서 사용자 지정을 수행할 수 있습니다. 
* **스크립트 없이 사용자 지정 설치**: 몇 가지 일반적인 시스템 구성 및 Windows 명령을 실행 하거나 스크립트를 사용 하지 않고 자주 사용 하거나 권장 되는 추가 구성 요소를 설치 합니다.
* **스크립트를 사용한 표준 사용자 지정 설정**: 스크립트 및 관련 파일을 준비 하 고 Azure 저장소 계정의 blob 컨테이너에 모두 업로드 합니다. 그런 다음 Azure-SSIS IR를 설정 하거나 다시 구성할 때 컨테이너에 대해 SAS (공유 액세스 서명) URI (Uniform Resource Identifier)를 제공 합니다. 그러면 Azure-SSIS IR의 각 노드가 컨테이너에서 스크립트 및 관련 파일을 다운로드 하 고 승격 된 권한으로 사용자 지정 설치를 실행 합니다. 사용자 지정 설치가 완료 되 면 각 노드는 실행의 표준 출력과 컨테이너에 기타 로그를 업로드 합니다.

Express 및 표준 사용자 지정 설치를 사용 하 여 사용이 허가 되지 않은 무료 구성 요소와 유료 라이선스 구성 요소를 모두 설치할 수 있습니다. ISV (독립 소프트웨어 공급 업체) 인 경우 [Azure-SSIS IR에 대 한 유료 또는 라이선스 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)을 참조 하세요.

> [!IMPORTANT]
> Azure-SSIS IR의 v2 시리즈 노드가 사용자 지정 설치에 적합 하지 않으므로 v3 시리즈 노드를 대신 사용 합니다. 이미 v2 시리즈 노드를 사용 하 고 있는 경우 가능한 한 빨리 v3 시리즈 노드로 전환 합니다.

## <a name="current-limitations"></a>현재 제한 사항

표준 사용자 지정에는 다음 제한 사항이 적용 됩니다.

- 스크립트에서 *gacutil.exe* 를 사용 하 여 GAC (전역 어셈블리 캐시)에 어셈블리를 설치 하려면 사용자 지정 설정의 일부로 *gacutil.exe* 를 제공 해야 합니다. 또는 나중에 "지침" 섹션에서 설명 하는 *공개 미리 보기* 컨테이너에 제공 된 복사본을 사용할 수 있습니다.

- 스크립트에서 하위 폴더를 참조 하려는 경우 *msiexec.exe* 는 루트 폴더를 참조 하는 `.\` 표기법을 지원 하지 않습니다. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`대신 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`와 같은 명령을 사용 합니다.

- Windows에서 자동으로 만드는 관리 공유 또는 숨겨진 네트워크 공유는 현재 Azure-SSIS IR에서 지원 되지 않습니다.

- IBM iSeries Access ODBC 드라이버는 Azure-SSIS IR에서 지원 되지 않습니다. 사용자 지정 설치 중에 설치 오류가 표시 될 수 있습니다. 필요한 경우 IBM 지원에 문의 하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR 사용자 지정 하려면 다음 항목이 필요 합니다.

- [Azure 구독](https://azure.microsoft.com/)

- [Azure-SSIS IR 프로 비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure Storage 계정](https://azure.microsoft.com/services/storage/) Express 사용자 지정에는 필요 하지 않습니다. 표준 사용자 지정 설치의 경우 사용자 지정 설치 스크립트와 관련 파일을 blob 컨테이너에 업로드 하 고 저장 합니다. 사용자 지정 설정 프로세스에서는 실행 로그도 같은 Blob 컨테이너에 업로드합니다.

## <a name="instructions"></a>Instructions

1. PowerShell을 사용 하 여 Azure-SSIS IR를 설정 하거나 다시 구성 하려면 [Azure PowerShell](/powershell/azure/install-az-ps)를 다운로드 하 여 설치 합니다. Express 사용자 지정의 경우 4 단계로 건너뜁니다.

1. 사용자 지정 설정 스크립트 및 관련 파일(예: .bat, .cmd, .exe, .dll, .msi, 또는 .ps1 파일)을 준비합니다.

   * 사용자 지정 설치의 진입점인 *main. .cmd*라는 스크립트 파일이 있어야 합니다.  
   * 스크립트가 자동으로 실행 될 수 있도록 하려면 먼저 로컬 컴퓨터에서 테스트 하는 것이 좋습니다.  
   * 다른 도구 (예: *msiexec.exe*)에서 생성 된 추가 로그를 컨테이너에 업로드 하려면 스크립트에서 미리 정의 된 환경 변수 `CUSTOM_SETUP_SCRIPT_LOG_DIR`를 로그 폴더로 지정 합니다 (예: *msiexec/i xxx .msi/quiet/lv% CUSTOM_SETUP_SCRIPT_LOG_DIR% \ install .log*).

1. [Azure Storage 탐색기](https://storageexplorer.com/)를 다운로드 하 고 설치 하 고 엽니다. 이렇게 하려면 다음을 수행합니다.

   a. **(로컬 및 연결 됨)** 에서 **저장소 계정**을 마우스 오른쪽 단추로 클릭 하 고 **Azure storage에 연결**을 선택 합니다.

      ![Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. **저장소 계정 이름 및 키 사용**을 선택 하 고 **다음**을 선택 합니다.

      ![스토리지 계정 이름 및 키 사용](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   다. Azure storage 계정 이름 및 키를 입력 하 고 **다음**을 선택한 다음, **연결**을 선택 합니다.

      ![저장소 계정 이름 및 키 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 연결 된 Azure storage 계정에서 **Blob 컨테이너**를 마우스 오른쪽 단추로 클릭 하 고 **blob 컨테이너 만들기**를 선택 하 고 새 컨테이너의 이름을로 선택 합니다.

      ![Blob 컨테이너 만들기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 새 컨테이너를 선택 하 고 사용자 지정 설치 스크립트 및 관련 파일을 업로드 합니다. 컨테이너가 아닌 컨테이너의 최상위 수준에서 *기본 .cmd* 를 업로드 해야 합니다. 또한 컨테이너에 필요한 사용자 지정 설치 파일만 포함 하 여 나중에 Azure-SSIS IR 다운로드 하는 데 시간이 오래 걸리지 않도록 합니다. 사용자 지정 설치의 최대 기간은 현재 45 분 이내에 설정 되어 있습니다. 여기에는 컨테이너에서 모든 파일을 다운로드 하 여 Azure-SSIS IR에 설치 하는 시간이 포함 됩니다. 설치 하는 데 시간이 더 필요한 경우 지원 티켓을 발생 시킵니다.

      ![Blob 컨테이너에 파일 업로드](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 컨테이너를 마우스 오른쪽 단추로 클릭 하 고 **공유 액세스 서명 가져오기**를 선택 합니다.

      ![컨테이너에 대한 공유 액세스 서명 가져오기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 충분 한 만료 시간을 사용 하 고 읽기/쓰기/목록 권한을 사용 하 여 컨테이너에 대 한 SAS URI를 만듭니다. Azure-SSIS IR 노드가 이미지로 다시 설치 되거나 다시 시작 될 때마다 사용자 지정 설치 스크립트 및 관련 파일을 다운로드 하 고 실행 하려면 SAS URI가 필요 합니다. 설정 실행 로그를 업로드하려면 쓰기 권한이 필요합니다.

      > [!IMPORTANT]
      > 이 기간 동안 정기적으로 Azure-SSIS IR를 중지 하 고 시작 하는 경우, 특히 SAS URI가 만료 되지 않고 사용자 지정 설치 리소스를 만들기에서 삭제까지 Azure-SSIS IR의 전체 수명 주기 동안 항상 사용할 수 있는지 확인 합니다.

      ![컨테이너에 대한 공유 액세스 서명 생성](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 컨테이너의 SAS URI를 복사하여 저장합니다.

      ![공유 액세스 서명 복사 및 저장](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. 데이터 팩터리 UI를 사용 하 여 Azure-SSIS IR를 설정 하거나 다시 구성할 때 **Integration Runtime 설정** 창의 **고급 설정** 섹션에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자** 지정 확인란을 선택 하 여 사용자 지정 설치를 추가 하거나 제거할 수 있습니다. 

   표준 사용자 지정 설치를 추가 하려면 **사용자 지정 설치 컨테이너 SAS uri** 상자에 컨테이너의 sas uri를 입력 합니다. 
   
   Express 사용자 지정 설치를 추가 하려면 **새로 만들기** 를 선택 하 여 **빠른 사용자 지정 설정 추가** 창을 연 다음 **express 사용자 지정 설치 유형** 드롭다운 목록에서 유형을 선택 합니다.

   * **Cmdkey 실행 명령** 유형을 선택 하는 경우 대상 컴퓨터 이름 또는 도메인 이름, 계정 이름 또는 사용자 이름, **/add**, **/user**및 **/Pass** 상자에 계정 키 또는 암호를 입력 하 여 Azure-SSIS IR에 대 한 파일 공유 또는 Azure Files 공유에 대 한 액세스 자격 증명을 유지할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 명령을 실행 하는 것과 유사 합니다.
   
   * 환경 변수 형식 **추가** 를 선택 하는 경우 **변수 이름** 및 **변수 값** 상자에 환경 변수 이름과 값을 입력 하 여 Azure-SSIS IR에서 실행 되는 패키지에 사용할 Windows 환경 변수를 추가할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) 명령을 실행 하는 것과 유사 합니다.

   * **사용 허가를 받은 구성 요소** 유형을 선택 하는 경우 **구성 요소 이름** 드롭다운 목록의 ISV 파트너에서 통합 된 구성 요소를 선택할 수 있습니다.

     * **Sentryone의 작업 팩터리** 구성 요소를 선택 하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력 하 여 Azure-SSIS IR의 구성 요소에 대 한 [작업 팩터리](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 제품군을 설치할 수 있습니다. 현재 통합 버전은 **2019.4.3**입니다.

     * Oh22's HEDDA를 선택 하는 경우  **IO** 구성 요소 HEDDA를 설치할 수 있습니다 [. ](https://hedda.io/ssis-component/)서비스를 구매한 후 Azure-SSIS IR에서 oh22의 IO 데이터 품질/정리 구성 요소 현재 통합 버전은 **1.0.13**입니다.

     * **Oh22's SQLPhonetics.NET** 구성 요소를 선택 하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력 하 여 Azure-SSIS IR oh22에서 [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) 데이터 품질/일치 구성 요소를 설치할 수 있습니다. 현재 통합 버전은 **1.0.43**입니다.

     * **KingswaySoft의 Ssis 통합 도구 키트** 구성 요소를 선택 하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력 하 여 Azure-SSIS IR KINGSWAYSOFT의 CRM/ERP/marketing/공동 작업 앱에 대 한 커넥터의 [ssis integration toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) 제품군을 설치할 수 있습니다 (예:). 현재 통합 버전은 **2019.2**입니다.

     * **KingswaySoft의 Ssis 생산성 팩** 구성 요소를 선택 하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력 하 여 KingswaySoft의 구성 요소에 Azure-SSIS IR 대 한 [ssis 생산성 팩](https://www.kingswaysoft.com/products/ssis-productivity-pack) 제품군을 설치할 수 있습니다. 현재 통합 버전은 **10.0**입니다.

   추가 된 빠른 사용자 지정 설치가 **고급 설정** 섹션에 표시 됩니다. 제거 하려면 해당 확인란을 선택한 다음 **삭제**를 선택 합니다.

   ![사용자 지정 설정으로 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. PowerShell을 사용 하 여 Azure-SSIS IR를 설정 하거나 다시 구성 하는 경우 Azure-SSIS IR를 시작 하기 전에 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet을 실행 하 여 사용자 지정 설치 프로그램을 추가 하거나 제거할 수 있습니다.
   
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
   
   표준 사용자 지정 설치를 완료 하 고 Azure-SSIS IR를 시작 하면 저장소 컨테이너의 *기본 .cmd .log* 폴더에 있는 *기본 .cmd* 및 기타 실행 로그의 표준 출력을 찾을 수 있습니다.

1. 표준 사용자 지정 프로그램의 일부 샘플을 보려면 Azure Storage 탐색기를 사용 하 여 공개 미리 보기 컨테이너에 연결 합니다.

   a. **(로컬 및 연결)** 에서 **스토리지 계정**을 마우스 오른쪽 단추로 클릭하고 **Azure Storage에 연결**을 선택한 다음, **연결 문자열 또는 공유 액세스 서명 URI**를 선택하고 **다음**을 선택합니다.

      ![공유 액세스 서명을 사용하여 Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. **SAS Uri 사용** 을 선택 하 고 **URI** 상자에 다음 SAS uri를 입력 합니다.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![컨테이너에 공유 액세스 서명 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   다. **다음**을 선택 하 고 **연결**을 선택 합니다.

   d. 왼쪽 창에서 연결 된 **publicpreview** 컨테이너를 선택 하 고 *customsetupscript* 폴더를 두 번 클릭 합니다. 이 폴더에는 다음 항목이 있습니다.

      * Azure-SSIS IR의 각 노드에 기본 작업을 설치 하는 사용자 지정 설정이 포함 된 *샘플* 폴더입니다. 이 작업은 몇 초 동안 대기하면서 아무 것도 수행하지 않습니다. 이 폴더에는 컨테이너에 전체 콘텐츠 (*gacutil.exe*, *gacutil.exe*및 *1033 \ gacutlrc*)를 그대로 복사할 수 있는 *gacutil.exe* 폴더도 포함 되어 있습니다.

      * 실제 사용자 시나리오의 여러 사용자 지정 설정 샘플이 포함 된 *Userscenarios* 폴더.

        ![공개 미리 보기 컨테이너의 콘텐츠](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. *Userscenarios* 폴더를 두 번 클릭 하 여 다음 항목을 찾습니다.

      * Azure-SSIS IR의 각 노드에서 사용자 지정 구성 요소에 필요할 수 있는 .NET Framework의 이전 버전을 설치 하는 사용자 지정 설정이 포함 된 *.NET FRAMEWORK 3.5* 폴더.

      * *Bcp* 폴더는 Azure-SSIS IR의 각 노드에*bcp*(대량 복사 프로그램)를 비롯 한 SQL Server 명령줄 유틸리티 (*MsSqlCmdLnUtils*)를 설치 하는 사용자 지정 설정을 포함 합니다.

      * *Excel* 폴더-스크립트 태스크에서 Azure-SSIS IR의 각 노드에 EXCEL 파일을 동적으로 읽고 쓰는 C# 데 사용할 수 있는 어셈블리 및 라이브러리를 설치 하기 위한 사용자 지정 설치 스크립트 (*기본 .cmd*)를 포함 합니다. 
      
        먼저, [*excel*](https://www.nuget.org/packages/ExcelDataReader/) [*을 다운로드 한 다음,* ](https://www.nuget.org/packages/DocumentFormat.OpenXml/) *기본 .cmd* 와 함께 모두 컨테이너에 업로드 합니다. 또는 표준 Excel 연결 관리자, Excel 원본 및 Excel 대상만 사용 하려는 경우에는 필요한 액세스 재배포 가능 항목이 이미 Azure-SSIS IR에 미리 설치 되어 있으므로 사용자 지정 설정이 필요 하지 않습니다.
      
      * 사용자 지정 설치 스크립트 (*기본 .cmd*)를 포함 하는 *mysql odbc* 폴더로, Azure-SSIS IR의 각 노드에 mysql odbc 드라이버를 설치 합니다. 이 설정을 사용 하면 ODBC 연결 관리자, 원본 및 대상을 사용 하 여 MySQL 서버에 연결할 수 있습니다. 
     
        먼저, [최신 64 비트 및 32 비트 버전의 MYSQL ODBC 드라이버 설치 관리자](https://dev.mysql.com/downloads/connector/odbc/) (예: *mysql-connector-odbc-8.0.13-winx64 .msi* 및 *mysql-connector-odbc-8.0.13*)를 다운로드 한 다음, 모든를 *기본 .cmd* 와 함께 컨테이너에 업로드 합니다.

      * Azure-SSIS IR Enterprise Edition의 각 노드에 Oracle 커넥터 및 OCI 드라이버를 설치 하기 위한 사용자 지정 설치 스크립트 (*기본 .cmd*) 및 자동 설치 구성*파일 (client.msi*)이 포함 된 *oracle ENTERPRISE* 폴더 이 설정을 사용 하면 oracle 연결 관리자, 원본 및 대상을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 microsoft [다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=55179) [와 Oracle의](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)최신 oracle 클라이언트 (예: *AttunitySSISOraAdaptersSetup64 및*)에서 microsoft Connector v 5.0 For Oracle (*Attyour Yssisoraadaptersadapter및* )을 다운로드 한 다음 *winx64_12102_client*, *기본 .cmd* 와 *클라이언트* 를 모두 컨테이너에 업로드 합니다. TNS를 사용 하 여 Oracle에 연결 하는 경우에는 ora를 다운로드 하 고, 편집 하 고, 컨테이너에 업로드 해야 *합니다.* 설치 하는 동안 oracle 설치 폴더에 복사할 수 있습니다.

      * Oracle *STANDARD ADO.NET* 폴더-사용자 지정 설치 스크립트 (*기본 .cmd*)를 포함 하 여 Azure-SSIS IR의 각 노드에 oracle ODP.NET 드라이버를 설치 합니다. 이 설정을 사용 하면 ADO.NET 연결 관리자, 원본 및 대상을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 [최신 Oracle ODP.NET 드라이버](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) (예: *ODP NET_Managed_ODAC122cR1. .zip*)를 다운로드 한 다음, *기본 .cmd* 와 함께 컨테이너에 업로드 합니다.
       
      * Oracle *표준 odbc* 폴더로, oracle odbc 드라이버를 설치 하 고 Azure-SSIS IR의 각 노드에서 DSN (데이터 원본 이름)을 구성 하는 사용자 지정 설치 스크립트 (*기본 .cmd*)를 포함 합니다. 이 설정을 사용 하면 odbc 연결 관리자, 원본 및 대상 또는 ODBC 데이터 원본 유형을 사용 하는 파워 쿼리 연결 관리자 및 원본을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 최신 Oracle 인스턴트 클라이언트 (기본 패키지 또는 기본 Lite 패키지)와 ODBC 패키지를 다운로드 한 다음, 모든를 *기본 .cmd* 와 함께 컨테이너에 업로드 합니다.
        * [64 비트 패키지 다운로드](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (기본 패키지: *instantclient-basic-windows. x64-18.3.0.0.0* 기본 Lite 패키지: *instantclient-basiclite-windows. x64-18.3.0.0.0* ODBC 패키지: *instantclient-odbc-windows. x64-18.3.0.0.0* 
        * [32 비트 패키지 다운로드](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (기본 패키지: *instantclient-basic-nt-18.3.0.0.0 dbru .zip*; 기본 Lite 패키지: *instantclient-basiclite-nt-18.3.0.0.0 dbru .zip*; ODBC 패키지: *instantclient-odbc-nt-18.3.0.0.0 dbru .zip*)

      * Oracle *표준 oledb* 폴더로, Azure-SSIS IR의 각 노드에 oracle OLEDB 드라이버를 설치 하는 사용자 지정 설치 스크립트 (*기본 .cmd*)가 포함 되어 있습니다. 이렇게 설정 하면 OLEDB 연결 관리자, 원본 및 대상을 사용 하 여 Oracle 서버에 연결할 수 있습니다. 
     
        먼저 [최신 ORACLE OLEDB 드라이버](https://www.oracle.com/partners/campaign/index-090165.html) (예: *ODAC122010Xcopy_x64 .zip*)를 다운로드 한 다음, *기본 .cmd* 와 함께 컨테이너에 업로드 합니다.

      * Azure-SSIS IR의 각 노드에 PostgreSQL ODBC 드라이버를 설치 하는 사용자 지정 설치*스크립트 (* POSTGRESQL)가 포함 된 *odbc* 폴더. 이 설정을 사용 하면 ODBC 연결 관리자, 원본 및 대상을 사용 하 여 PostgreSQL 서버에 연결할 수 있습니다. 
     
        먼저 PostgreSQL ODBC 드라이버 설치 관리자 (예: *psqlodbc_x64 .msi* 및 *psqlodbc_x86*) [의 최신 64 비트 및 32 비트 버전을 다운로드](https://www.postgresql.org/ftp/odbc/versions/msi/) 한 다음, 모든를 *기본 .cmd* 와 함께 컨테이너에 업로드 합니다.

      * Azure-SSIS IR Enterprise Edition의 각 노드에 SAP .NET 커넥터 어셈블리 (librfc32.dll)를 설치 하는 사용자 지정 설치 스크립트 ( *)가*포함 된 *SAP BW* 폴더 이 설정을 통해 SAP BW (Business Warehouse) 연결 관리자, 원본 및 대상을 사용 하 여 SAP BW 서버에 연결할 수 있습니다. 
      
        먼저 librfc32.dll와 함께 SAP 설치 폴더에서 64 비트 또는 32 비트 버전의 *를 컨테이너* 에 업로드 합니다. 그런 다음 스크립트는 설치 하는 동안 SAP 어셈블리를 *%windir%\SysWow64* 또는 *%windir%\System32* 폴더에 복사 합니다.

      * Azure-SSIS IR의 각 노드에 Azure PowerShell를 설치 하기 위한 사용자 지정 설정이 포함 된 *저장소* 폴더입니다. 이렇게 설치 하면 PowerShell 스크립트를 실행 하는 SSIS 패키지를 배포 하 고 실행 하 [여 Azure storage 계정을 조작할](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)수 있습니다. 
      
        *기본 .cmd*, 샘플 *azurepowershell .msi* (또는 최신 버전 사용) 및 *저장소.* p s 1을 컨테이너에 복사 합니다. *Package.dtsx* 를 패키지의 템플릿으로 사용 합니다. 패키지 템플릿은 *storage.* p s 1을 수정 가능한 PowerShell 스크립트로 다운로드 하는 [Azure Blob 다운로드 작업과](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)각 노드에서 스크립트를 실행 하는 [프로세스 실행 태스크](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)를 결합 합니다.

      * *TERADATA* 폴더-사용자 지정 설치 스크립트 (*기본 .cmd*), 연결 된 파일 (*install .cmd*) 및 설치 관리자 패키지 ( *.msi*)를 포함 합니다. 이러한 파일은 Azure-SSIS IR Enterprise Edition의 각 노드에 Teradata 커넥터, Teradata Parallel Transporter (TPT) API 및 ODBC 드라이버를 설치 합니다. 이 설정을 사용 하면 Teradata 연결 관리자, 원본 및 대상을 사용 하 여 Teradata 서버에 연결할 수 있습니다. 
      
        먼저 [Teradata Tools 및 유틸리티 4.x zip 파일](http://partnerintelligence.teradata.com) (TeradataToolsAndUtilitiesBase__windows_indep 예: *15.10.22.00*)을 다운로드 한 후 앞에서 설명한 *.cmd* 및 *.msi* 파일과 함께 컨테이너에 업로드 합니다.

      * 사용자 지정 설치 스크립트 (*기본 .cmd*) 및 PowerShell 파일 (*install_openjdk.* p s 1)을 포함 하는 *줄루어 openjdk* 폴더로, Azure-SSIS IR의 각 노드에는 줄루어 openjdk를 설치 합니다. 이 설정을 사용 하면 Azure Data Lake Store 및 유연한 파일 커넥터를 사용 하 여 ORC 및 Parquet 파일을 처리할 수 있습니다. 자세한 내용은 [Integration Services 용 Azure 기능 팩](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)을 참조 하세요. 
      
        먼저 [최신 줄루어 OpenJDK](https://www.azul.com/downloads/zulu/zulu-windows/) (예: *줄루어 8.33.0.1-jdk 8.0.192-win_x64 .zip*)를 다운로드 한 다음, *기본 .cmd* 및 *install_openjdk* 를 컨테이너에 업로드 합니다.

        ![사용자 시나리오 폴더의 폴더](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 이러한 사용자 지정 설치 샘플을 시도 하려면 선택한 폴더의 콘텐츠를 컨테이너에 복사 합니다.
   
      Data Factory UI를 사용 하 여 Azure-SSIS IR를 설정 하거나 다시 구성 하는 경우 **고급 설정** 섹션에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자 지정** 확인란을 선택 하 고 **사용자 지정 설치 컨테이너 sas URI** 상자에 컨테이너의 SAS uri를 입력 합니다.
   
      PowerShell을 사용 하 여 Azure-SSIS IR를 설정 하거나 다시 구성 하는 경우 `SetupScriptContainerSasUri` 매개 변수의 값으로 컨테이너의 SAS URI를 사용 하 여 `Set-AzDataFactoryV2IntegrationRuntime` cmdlet을 실행 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS Integration Runtime Enterprise Edition 설정](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS Integration Runtime에 대 한 유료 또는 사용이 허가 된 사용자 지정 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)
