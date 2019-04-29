---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 072864d565e2edbddd4b7df851ad0e30daf7e5fa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60387964"
---
Microsoft Azure 클라우드 서비스 관련 문제를 진단하려면 문제가 발생할 때 가상 머신에서 서비스의 로그 파일을 수집해야 합니다. 필요에 따라 AzureLogCollector 확장을 사용하여 VM에 원격으로 로그온하지 않고 웹 역할 및 작업자 역할 둘 다로 하나 이상의 클라우드 서비스 VM에서 일회성 로그 수집을 수행하고 수집한 파일을 Azure 저장소 계정으로 보낼 수 있습니다.

> [!NOTE]
> 대부분의 로깅된 정보에 대한 설명은 http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.asp에서 확인할 수 있습니다.
> 
> 

수집할 파일의 유형에 따라 달라지는 두 가지 모드의 컬렉션이 있습니다.

* **Azure 게스트 에이전트만 로그(GA)**. 이 컬렉션 모드는 Azure 게스트 에이전트 및 기타 Azure 구성 요소와 관련된 모든 로그를 포함합니다.
* **모든 로그(전체)**. 이 컬렉션 모드는 GA 모드의 모든 파일과 다음 정보를 수집합니다.
  
  * 시스템 및 애플리케이션 이벤트 로그
  * HTTP 오류 로그
  * IIS 로그
  * 설정 로그
  * 기타 시스템 로그

두 컬렉션 모드에서 다음 구조의 컬렉션을 사용하여 추가 데이터 수집 폴더를 지정할 수 있습니다.

* **이름**: 수집된 파일이 포함된 Zip 파일 내의 하위 폴더의 이름으로 사용되는 컬렉션의 이름입니다.
* **Location**: 수집될 파일이 배치될 가상 머신의 폴더 경로입니다.
* **SearchPattern**: 수집할 파일의 이름 패턴입니다. 기본값은 “\*”입니다.
* **재귀**: 수집되는 파일이 지정된 위치에 재귀적으로 배치됩니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* 생성된 zip 파일을 저장하려면 확장에 대한 저장소 계정이 있어야 합니다.
* Azure PowerShell. 참조 [Azure PowerShell 설치](/powershell/azure/install-az-ps)] 설치 지침에 대 한 합니다.

## <a name="add-the-extension"></a>확장 추가
[Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) cmdlet 또는 [서비스 관리 REST API](https://msdn.microsoft.com/library/ee460799.aspx)를 사용하여 AzureLogCollector 확장을 추가할 수 있습니다.

Cloud Services의 경우 기존 Azure Powershell cmdlet인 **Set-azureserviceextension**을 사용하여 클라우드 서비스 역할 인스턴스에서 확장을 사용하도록 설정할 수 있습니다. 이 확장이 cmdlet을 통해 사용하도록 설정될 때마다 선택한 역할의 선택한 역할 인스턴스에서 로그 수집이 트리거됩니다.

Virtual Machines의 경우 기존 Azure Powershell cmdlet인 **Set-AzureVMExtension**을 사용하여 Virtual Machines에서 확장을 사용하도록 설정할 수 있습니다. 이 확장이 cmdlet을 통해 사용하도록 설정될 때마다 각 인스턴스에서 로그 수집이 트리거됩니다.

내부적으로 이 확장은 JSON 기반 PublicConfiguration 및 PrivateConfiguration을 사용합니다. 다음은 공용 및 개인 구성에 대한 샘플 JSON의 레이아웃입니다.

### <a name="publicconfiguration"></a>PublicConfiguration

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> 이 확장은 **privateConfiguration**이 필요하지 않습니다. **–PrivateConfiguration** 인수에 대해 비어 있는 구조를 제공할 수 있습니다.
> 
> 

다음 두 개의 단계 중 하나를 따라 각 VM에 컬렉션을 트리거하여 지정된 Azure 계정에 수집된 파일을 실행 및 전송하는 선택한 역할의 클라우드 서비스 또는 Virtual Machine의 하나 이상의 인스턴스에 AzureLogCollector를 추가할 수 있습니다.

## <a name="adding-as-a-service-extension"></a>서비스 확장으로 추가
1. 다음 지침을 따라 구독에 Azure PowerShell을 연결합니다.
2. AzureLogCollector 확장을 추가하고 사용하려는 서비스 이름, 슬롯, 역할 및 역할 인스턴스를 지정합니다.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. 수집할 파일에 대해 추가 데이터 폴더를 지정합니다(이 단계는 선택 사항).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > 고정된 드라이브를 사용하지 않으므로 토큰 `%roleroot%`을(를) 사용하여 역할 루트 드라이브를 지정할 수 있습니다.
   > 
   > 
4. 업로드될 수집된 파일에 Azure 저장소 계정 이름 및 키를 제공합니다.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. 클라우드 서비스에 대해 AzureLogCollector 확장을 사용하려면 다음과 같이 SetAzureServiceLogCollector.ps1(이 문서 끝에 포함됨)을 호출합니다. 실행이 완료되면 `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

다음은 스크립트에 전달된 매개 변수의 정의입니다. (아래에 복사됩니다.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: 클라우드 서비스 이름입니다.
* **역할**: "WebRole1" 또는 "WorkerRole1"과 같은 역할 목록입니다.
* **인스턴스**: 쉼표로 구분된 역할 인스턴스의 이름 목록입니다. 모든 역할 인스턴스에 대해 와일드카드 문자열(“*”)을 사용합니다.
* **슬롯**: 슬롯 이름입니다. “프로덕션” 또는 “스테이징”입니다.
* **모드**: 수집 모드입니다. "Full" 또는 "GA"입니다.
* **StorageAccountName**: 수집된 데이터를 저장하기 위한 Azure 스토리지 계정의 이름입니다.
* **StorageAccountKey**: Azure Storage 계정 키 이름입니다.
* **AdditionalDataLocationList**: 다음 구조의 목록:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>VM 확장으로 추가
다음 지침을 따라 구독에 Azure PowerShell을 연결합니다.

1. 서비스 이름, VM 및 컬렉션 모드를 지정합니다.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. 업로드될 수집된 파일에 Azure 저장소 계정 이름 및 키를 제공합니다.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. 클라우드 서비스에 대해 AzureLogCollector 확장을 사용하려면 다음과 같이 SetAzureVMLogCollector.ps1(이 문서 끝에 포함됨)을 호출합니다. 실행이 완료되면 `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

다음은 스크립트에 전달된 매개 변수의 정의입니다. (아래에 복사됩니다.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **ServiceName**: 클라우드 서비스 이름입니다.
* **VMName**: VM의 이름입니다.
* **모드**: 수집 모드입니다. "Full" 또는 "GA"입니다.
* **StorageAccountName**: 수집된 데이터를 저장하기 위한 Azure 스토리지 계정의 이름입니다.
* **StorageAccountKey**: Azure Storage 계정 키 이름입니다.
* **AdditionalDataLocationList**: 다음 구조의 목록:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>확장 PowerShell 스크립트 파일
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>다음 단계
이제 간단한 위치에서 로그를 검사하거나 복사할 수 있습니다.

