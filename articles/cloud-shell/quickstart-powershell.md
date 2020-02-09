---
title: Azure Cloud Shell 빠른 시작-PowerShell
description: Azure Cloud Shell를 사용 하 여 브라우저에서 PowerShell을 사용 하는 방법을 알아봅니다.
author: maertendmsft
ms.author: damaerte
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/18/2018
ms.openlocfilehash: 72261989b7cee9d2251eb18b36431ec807b0e874
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791609"
---
# <a name="quickstart-for-powershell-in-azure-cloud-shell"></a>Azure Cloud Shell의 PowerShell에 대한 빠른 시작

이 문서는 [Azure Portal](https://portal.azure.com/)에서 Azure Cloud Shell의 PowerShell을 사용하는 방법을 자세히 설명합니다.

> [!NOTE]
> [Azure Cloud Shell의 Bash](quickstart.md) 빠른 시작도 사용할 수 있습니다.

## <a name="start-cloud-shell"></a>Cloud Shell 시작

1. Azure Portal의 위쪽 탐색 모음에서 **Azure Cloud Shell** 버튼을 클릭합니다.

   ![](media/quickstart-powershell/shell-icon.png)

2. 드롭다운에서 PowerShell 환경을 선택하면 Azure 드라이브 `(Azure:)`에 들어가게 됩니다.

   ![](media/quickstart-powershell/environment-ps.png)

## <a name="run-powershell-commands"></a>PowerShell 명령 실행

Azure Cloud Shell에서 다음과 같은 일반 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
PS Azure:\> Get-Date

# Expected Output
Friday, July 27, 2018 7:08:48 AM

PS Azure:\> Get-AzVM -Status

# Expected Output
ResourceGroupName       Name       Location                VmSize   OsType     ProvisioningState  PowerState
-----------------       ----       --------                ------   ------     -----------------  ----------
MyResourceGroup2        Demo        westus         Standard_DS1_v2  Windows    Succeeded           running
MyResourceGroup         MyVM1       eastus            Standard_DS1  Windows    Succeeded           running
MyResourceGroup         MyVM2       eastus   Standard_DS2_v2_Promo  Windows    Succeeded           deallocated
```

## <a name="navigate-azure-resources"></a>Azure 리소스 이동

 1. `Azure` 드라이브에서 모든 구독 나열

    ```azurepowershell-interactive
    PS Azure:\> dir
    ```

 2. 기본 구독에 `cd`

    ```azurepowershell-interactive
    PS Azure:\> cd MySubscriptionName
    PS Azure:\MySubscriptionName>
    ```

 3. 현재 구독에서 모든 Azure 리소스 보기

    Azure 리소스 뷰를 여러 개 나열하려면 `dir`을 입력합니다.

    ```azurepowershell-interactive
    PS Azure:\MySubscriptionName> dir

        Directory: azure:\MySubscriptionName

    Mode Name
    ---- ----
    +    AllResources
    +    ResourceGroups
    +    StorageAccounts
    +    VirtualMachines
    +    WebApps
    ```

### <a name="allresources-view"></a>AllResources 보기

Azure 리소스를 보려면 `AllResources` 디렉터리 아래에 `dir`을 입력 합니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir AllResources
```

### <a name="explore-resource-groups"></a>리소스 그룹 탐색

 `ResourceGroups` 디렉터리로 가서 특정 리소스 그룹 안에서 가상 컴퓨터를 찾을 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> cd ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines

PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines> dir


    Directory: Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup1\Microsoft.Compute\virtualMachines


VMName    Location   ProvisioningState VMSize          OS            SKU             OSVersion AdminUserName  NetworkInterfaceName
------    --------   ----------------- ------          --            ---             --------- -------------  --------------------
TestVm1   westus     Succeeded         Standard_DS2_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo371
TestVm2   westus     Succeeded         Standard_DS1_v2 WindowsServer 2016-Datacenter Latest    AdminUser      demo271
```

> [!NOTE]
> `dir`을 두 번째 입력할 때, Cloud Shell이 훨씬 더 빠르게 항목을 표시할 수 있음을 알 수 있습니다.
> 이는 자식 항목이 향상된 사용자 환경을 위해 메모리에 캐시되기 때문입니다.
그렇지만, 새로운 데이터를 가져오기 위해 언제나 `dir -Force`을 사용할 수 있습니다.

### <a name="navigate-storage-resources"></a>스토리지 리소스 이동

`StorageAccounts` 디렉터리에 들어가면 모든 스토리지 리소스를 쉽게 탐색할 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> dir

    Directory: Azure:\MySubscriptionNameStorageAccounts\MyStorageAccountName\Files

Name          ConnectionString
----          ----------------
MyFileShare1  \\MyStorageAccountName.file.core.windows.net\MyFileShare1;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare2  \\MyStorageAccountName.file.core.windows.net\MyFileShare2;AccountName=MyStorageAccountName AccountKey=<key>
MyFileShare3  \\MyStorageAccountName.file.core.windows.net\MyFileShare3;AccountName=MyStorageAccountName AccountKey=<key>
```

연결 문자열과 함께 다음 명령을 사용하여 Azure Files 공유를 탑재할 수 있습니다.

```azurepowershell-interactive
net use <DesiredDriveLetter>: \\<MyStorageAccountName>.file.core.windows.net\<MyFileShareName> <AccountKey> /user:Azure\<MyStorageAccountName>
```

자세한 내용은 [Azure Files 공유를 탑재 하 고 Windows에서 공유에 액세스][azmount]를 참조 하세요.

또한 Azure Files 공유에서 디렉터리를 다음과 같이 탐색할 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files> cd .\MyFileShare1\
PS Azure:\MySubscriptionName\StorageAccounts\MyStorageAccountName\Files\MyFileShare1> dir

Mode  Name
----  ----
+     TestFolder
.     hello.ps1
```

### <a name="interact-with-virtual-machines"></a>가상 머신과 상호 작용

`VirtualMachines` 디렉터리를 통해 현재 구독에서 모든 가상 머신을 찾을 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName\VirtualMachines> dir

    Directory: Azure:\MySubscriptionName\VirtualMachines


Name       ResourceGroupName  Location  VmSize          OsType              NIC ProvisioningState  PowerState
----       -----------------  --------  ------          ------              --- -----------------  ----------
TestVm1    MyResourceGroup1   westus    Standard_DS2_v2 Windows       my2008r213         Succeeded     stopped
TestVm2    MyResourceGroup1   westus    Standard_DS1_v2 Windows          jpstest         Succeeded deallocated
TestVm10   MyResourceGroup2   eastus    Standard_DS1_v2 Windows           mytest         Succeeded     running
```

#### <a name="invoke-powershell-script-across-remote-vms"></a>PowerShell 스크립트를 원격 VM에 걸쳐 호출

 > [!WARNING]
 > [Azure VM의 원격 관리 문제 해결](troubleshooting.md#troubleshooting-remote-management-of-azure-vms)을 참조하세요.

  MyVM1이란 VM이 있다고 가정하고 `Invoke-AzVMCommand`를 사용하여 원격 컴퓨터에 PowerShell 스크립트 블록을 호출합니다.

  ```azurepowershell-interactive
  Enable-AzVMPSRemoting -Name MyVM1 -ResourceGroupname MyResourceGroup
  Invoke-AzVMCommand -Name MyVM1 -ResourceGroupName MyResourceGroup -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)
  ```

  또한 먼저 VirtualMachines 디렉터리로 이동하고 다음과 같이 `Invoke-AzVMCommand`를 실행할 수 있습니다.

  ```azurepowershell-interactive
  PS Azure:\> cd MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines
  PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Invoke-AzVMCommand -Scriptblock {Get-ComputerInfo} -Credential (Get-Credential)

  # You will see output similar to the following:

  PSComputerName                                          : 65.52.28.207
  RunspaceId                                              : 2c2b60da-f9b9-4f42-a282-93316cb06fe1
  WindowsBuildLabEx                                       : 14393.1066.amd64fre.rs1_release_sec.170327-1835
  WindowsCurrentVersion                                   : 6.3
  WindowsEditionId                                        : ServerDatacenter
  WindowsInstallationType                                 : Server
  WindowsInstallDateFromRegistry                          : 5/18/2017 11:26:08 PM
  WindowsProductId                                        : 00376-40000-00000-AA947
  WindowsProductName                                      : Windows Server 2016 Datacenter
  WindowsRegisteredOrganization                           :
   ...
  ```

#### <a name="interactively-log-on-to-a-remote-vm"></a>원격 VM에 대화형으로 로그온

`Enter-AzVM`을 사용하여 Azure에서 실행하는 VM에 대화형으로 로그인할 수 있습니다.

  ```azurepowershell-interactive
  PS Azure:\> Enter-AzVM -Name MyVM1 -ResourceGroupName MyResourceGroup -Credential (Get-Credential)
  ```

또는 먼저 `VirtualMachines` 디렉터리로 이동하고 다음과 같이 `Enter-AzVM`를 실행할 수 있습니다.

  ```azurepowershell-interactive
 PS Azure:\MySubscriptionName\ResourceGroups\MyResourceGroup\Microsoft.Compute\virtualMachines> Get-Item MyVM1 | Enter-AzVM -Credential (Get-Credential)
 ```

### <a name="discover-webapps"></a>WebApps 검색

`WebApps` 디렉터리에 들어가면 웹앱 리소스를 쉽게 탐색할 수 있습니다.

```azurepowershell-interactive
PS Azure:\MySubscriptionName> dir .\WebApps\

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Stopped  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US

# You can use Azure cmdlets to Start/Stop your web apps
PS Azure:\MySubscriptionName\WebApps> Start-AzWebApp -Name mywebapp1 -ResourceGroupName MyResourceGroup1

Name           State    ResourceGroup        EnabledHostNames                   Location
----           -----    -------------        ----------------                   --------
mywebapp1      Running  MyResourceGroup1     {mywebapp1.azurewebsites.net ...   West US

# Refresh the current state with -Force
PS Azure:\MySubscriptionName\WebApps> dir -Force

    Directory: Azure:\MySubscriptionName\WebApps

Name            State    ResourceGroup      EnabledHostNames                  Location
----            -----    -------------      ----------------                  --------
mywebapp1       Running  MyResourceGroup1   {mywebapp1.azurewebsites.net...   West US
mywebapp2       Running  MyResourceGroup2   {mywebapp2.azurewebsites.net...   West Europe
mywebapp3       Running  MyResourceGroup3   {mywebapp3.azurewebsites.net...   South Central US
```

## <a name="ssh"></a>SSH

SSH를 사용하여 서버 또는 VM을 인증하려면 Cloud Shell에서 공개-프라이빗 키 쌍을 생성하고 퍼블릭 키를 원격 컴퓨터의 `authorized_keys`(예: `/home/user/.ssh/authorized_keys`)에 게시합니다.

> [!NOTE]
> Cloud Shell에서 `ssh-keygen`을 사용하여 SSH 프라이빗-공개 키를 만들고 이 키를 `$env:USERPROFILE\.ssh`에 게시할 수 있습니다.

### <a name="using-ssh"></a>SSH 사용

Azure PowerShell cmdlet을 사용 하 여 새 VM 구성을 만들려면 [여기](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-powershell) 의 지침을 따르세요.
`New-AzVM`을 호출하여 배포를 시작하기 전에 SSH 공개 키를 VM 구성에 추가합니다.
새로 만든 VM에는 공개 키가 `~\.ssh\authorized_keys` 위치에 포함되므로 자격 증명이 없는 SSH 세션을 VM에 사용할 수 있습니다.

```azurepowershell-interactive
# Create VM config object - $vmConfig using instructions on linked page above

# Generate SSH keys in Cloud Shell
ssh-keygen -t rsa -b 2048 -f $HOME\.ssh\id_rsa 

# Ensure VM config is updated with SSH keys
$sshPublicKey = Get-Content "$HOME\.ssh\id_rsa.pub"
Add-AzVMSshPublicKey -VM $vmConfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

# Create a virtual machine
New-AzVM -ResourceGroupName <yourResourceGroup> -Location <vmLocation> -VM $vmConfig

# SSH to the VM
ssh azureuser@MyVM.Domain.Com
```

## <a name="list-available-commands"></a>사용할 수 있는 명령 목록

`Azure` 드라이브에서 상황별 Azure 명령을 가져오려면 `Get-AzCommand`를 입력합니다.

또는 사용 가능한 Azure 명령을 알아보려면 언제나 `Get-Command *az* -Module Az.*`을 사용할 수 있습니다.

## <a name="install-custom-modules"></a>사용자 지정 모듈 설치

`Install-Module`를 실행 하 여 [PowerShell 갤러리][gallery]모듈을 설치할 수 있습니다.

## <a name="get-help"></a>Get-Help

Azure Cloud Shell에서 PowerShell에 대한 정보를 가져오려면 `Get-Help`을 입력합니다.

```azurepowershell-interactive
Get-Help
```

특정 명령에 대해 여전히 `Get-Help`를 실행한 후 뒤 이어 cmdlet을 실행할 수 있습니다.

```azurepowershell-interactive
Get-Help Get-AzVM
```

## <a name="use-azure-files-to-store-your-data"></a>Azure Files를 사용하여 데이터 저장

예를 들어 `helloworld.ps1`이란 스크립트를 만들어 `clouddrive`에 저장하고 셸 세션 전반에 걸쳐 사용할 수 있습니다.

```azurepowershell-interactive
cd $HOME\clouddrive
# Create a new file in clouddrive directory
New-Item helloworld.ps1
# Open the new file for editing
code .\helloworld.ps1
# Add the content, such as 'Hello World!'
.\helloworld.ps1
Hello World!
```

다음에 Azure Cloud Shell의 PowerShell을 사용할 때 `helloworld.ps1` 파일은 Azure Files 공유를 탑재한 `$HOME\clouddrive` 디렉터리에 존재할 것입니다.

## <a name="use-custom-profile"></a>사용자 지정 프로필 사용

PowerShell 프로필 `profile.ps1`(또는 `Microsoft.PowerShell_profile.ps1`)을 만들어 PowerShell 환경을 사용자 지정할 수 있습니다.
그것을 `$profile.CurrentUserAllHosts`(또는 `$profile.CurrentUserAllHosts`)에 저장하여 Cloud Shell 세션의 모든 PowerShell에 로드될 수 있게 합니다.

프로필을 만드는 방법에 대한 자세한 내용은 [프로필 정보][profile]를 참조 하세요.

## <a name="use-git"></a>Git 사용

Cloud Shell에서 Git 리포지토리를 복제 하려면 [개인용 액세스 토큰][githubtoken] 을 만들고 사용자 이름으로 사용 해야 합니다. 토큰이 있다면 다음과 같이 리포지토리를 복제합니다.

```azurepowershell-interactive
  git clone https://<your-access-token>@github.com/username/repo.git
```

## <a name="exit-the-shell"></a>셸 종료

세션을 종료하려면 `exit`을 입력합니다.

[bashqs]:quickstart.md
[gallery]:https://www.powershellgallery.com/
[customex]:https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript
[profile]: https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/about/about_profiles
[azmount]: https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows
[githubtoken]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
