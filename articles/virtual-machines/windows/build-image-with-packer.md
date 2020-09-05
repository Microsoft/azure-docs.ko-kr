---
title: PowerShell-패키지를 사용 하 여 VM 이미지를 만드는 방법
description: Azure에서 패키지를 사용 하 여 가상 머신의 이미지를 만드는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: 16f2bc2cc22fa38ece78b4a07298235abd7d629d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587092"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: Azure에서 패키지를 사용 하 여 가상 머신 이미지를 만드는 방법
Azure의 각 VM(가상 머신)은 Windows 배포판 및 OS 버전을 정의하는 이미지에서 만들어집니다. 이미지는 사전 설치된 애플리케이션 및 구성을 포함할 수 있습니다. Azure Marketplace는 가장 일반적인 OS 및 애플리케이션 환경에 대한 다양한 자사 및 타사 이미지를 제공하거나 사용자 요구에 맞게 사용자 지정 이미지를 만들 수 있습니다. 이 문서에는 오픈 소스 도구 [Packer](https://www.packer.io/)를 사용하여 Azure에서 사용자 지정 이미지를 정의하고 빌드하는 방법을 자세히 설명합니다.

이 문서는 [1.6.1 버전을](https://www.packer.io/docs/install) 사용 하 여 8/5/2020에서 마지막으로 테스트 되었습니다.

> [!NOTE]
> 이제 Azure에는 고유한 사용자 지정 이미지를 정의하고 만드는 데 필요한 Azure Image Builder(미리 보기) 서비스가 있습니다. Azure Image Builder는 Packer를 기반으로 빌드되므로 기존 Packer 셸 프로비저닝 프로그램 스크립트도 사용할 수 있습니다. Azure Image Builder를 시작하려면 [Azure Image Builder를 사용하여 Windows VM 만들기](image-builder.md)를 참조하세요.

## <a name="create-azure-resource-group"></a>Azure 리소스 그룹 만들기
빌드 프로세스 동안 Packer는 원본 VM을 빌드하므로 임시 Azure 리소스를 만듭니다. 이미지로 사용하기 위해 해당 원본 VM을 캡처하려면 리소스 그룹을 정의해야 합니다. Packer 빌드 프로세스의 출력은 이 리소스 그룹에 저장됩니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 리소스 그룹을 만듭니다. 다음 예에서는 *e미국* 위치에 *myPackerGroup* 이라는 리소스 그룹을 만듭니다.

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure 자격 증명 만들기
Packer는 서비스 사용자를 사용하여 Azure를 인증합니다. Azure 서비스 사용자는 앱, 서비스 및 Packer와 같은 자동화 도구를 사용할 수 있는 보안 ID입니다. 서비스 주체가 Azure에서 수행할 수 있는 작업에 대한 사용 권한은 사용자가 제어하고 정의합니다.

[AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)를 사용 하 여 서비스 주체를 만듭니다. `-DisplayName` 값은 고유해야 합니다. 필요에 따라 사용자의 값으로 바꿉니다.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

그런 다음 암호와 애플리케이션 ID를 출력합니다.

```powershell
$plainPassword
$sp.ApplicationId
```


Azure를 인증하려면 [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)을 사용하여 Azure 테넌트 및 구독 ID를 가져와야 합니다.

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Packer 템플릿 정의
이미지를 작성하려면 JSON 파일로 템플릿을 만듭니다. 템플릿에서 실제 빌드 프로세스를 통해 수행하는 작성기와 프로비저너를 정의합니다. Packer에는 이전 단계에서 만든 서비스 사용자 자격 증명과 같은 Azure 리소스를 정의하도록 허용하는 [Azure용 작성기](https://www.packer.io/docs/builders/azure.html)가 있습니다.

*windows.json*이라는 파일을 만들고 다음 콘텐츠를 붙여 넣습니다. 다음에 대해 사용자 고유의 값을 입력합니다.

| 매개 변수                           | 얻을 수 있는 위치 |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | `$sp.applicationId`를 사용하여 서비스 사용자 ID 보기 |
| *client_secret*                     | `$plainPassword`를 사용하여 자동 생성된 암호 보기 |
| *tenant_id*                         | `$sub.TenantId` 명령의 출력 |
| *subscription_id*                   | `$sub.SubscriptionId` 명령의 출력 |
| *managed_image_resource_group_name* | 첫 번째 단계에서 만든 리소스 그룹의 이름 |
| *managed_image_name*                | 만들어진 관리되는 디스크 이미지의 이름 |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

이 템플릿은 Windows Server 2016 VM을 빌드하고 IIS를 설치한 다음 Sysprep을 사용하여 VM을 일반화합니다. IIS 설치는 PowerShell 프로비저너를 사용하여 추가 명령을 실행하는 방법을 보여줍니다. 그런 다음, 최종 Packer 이미지에는 필요한 소프트웨어 설치 및 구성이 포함됩니다.

Windows 게스트 에이전트는 Sysprep 프로세스에 참여 합니다. 에이전트가 완전히 설치 되어 있어야 VM을 sysprep 된 수 있습니다. 이를 확인 하려면 sysprep.exe를 실행 하기 전에 모든 에이전트 서비스가 실행 중 이어야 합니다. 위의 JSON 코드 조각은 PowerShell provisioner에서이 작업을 수행 하는 한 가지 방법을 보여 줍니다. 이 코드 조각은 기본 인 에이전트를 설치 하도록 VM이 구성 된 경우에만 필요 합니다.


## <a name="build-packer-image"></a>Packer 이미지 작성
로컬 컴퓨터에 Packer를 아직 설치하지 않은 경우 [Packer 설치 지침을 따릅니다](https://learn.hashicorp.com/packer/getting-started/install).

명령 프롬프트를 열고 다음과 같이 Packer 템플릿 파일을 지정하여 이미지를 빌드합니다.

```
./packer build windows.json
```

이전 명령에서 출력의 예는 다음과 같습니다.

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Packer가 VM을 빌드하고 프로비저너를 실행하고 배포를 정리하는 데 몇 분 정도 걸립니다.


## <a name="create-a-vm-from-the-packer-image"></a>Packer 이미지에서 VM 만들기
이제 [New-AzVM](/powershell/module/az.compute/new-azvm)을 사용하여 이미지에서 VM을 만들 수 있습니다. 아직 없는 경우 지원 네트워크 리소스가 만들어집니다. 메시지가 표시되면 VM에 만들어질 관리 사용자 이름 및 암호를 입력합니다. 다음 예제에서는 *myPackerImage*에서 *myVM*이라는 VM을 만듭니다.

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Packer 이미지와 다른 리소스 그룹 또는 지역에서 VM을 만들려는 경우 이미지 이름 대신 이미지 ID를 지정합니다. [Get-AzImage](/powershell/module/az.compute/get-azimage)를 사용하여 이미지 ID를 가져올 수 있습니다.

Packer 이미지에서 VM을 만드는 데 몇 분이 걸립니다.


## <a name="test-vm-and-webserver"></a>VM 및 웹 서버 테스트
[Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 VM의 공용 IP 주소를 가져옵니다. 다음 예제에서는 앞서 만든 *myPublicIP*의 IP 주소를 가져옵니다.

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

실제로 Packer 프로비저너에서 IIS 설치를 포함하는 VM을 확인하려면 웹 브라우저에 공용 IP 주소를 입력합니다.

![IIS 기본 사이트](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>다음 단계
[Azure Image Builder](image-builder.md)에서 기존 Packer 프로비저닝 프로그램 스크립트를 사용할 수도 있습니다.
