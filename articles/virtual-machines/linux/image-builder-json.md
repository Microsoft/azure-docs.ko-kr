---
title: Azure Image Builder 템플릿 만들기(미리 보기)
description: Azure Image Builder에서 사용할 템플릿을 만드는 방법을 알아봅니다.
author: danielsollondon
ms.author: danis
ms.date: 08/13/2020
ms.topic: conceptual
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: 6ed95f87d2b2a5f811531a5ff258ebe97a9b892a
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869204"
---
# <a name="preview-create-an-azure-image-builder-template"></a>미리 보기: Azure Image Builder 템플릿 만들기 

Azure Image Builder는 .json 파일을 사용하여 Image Builder 서비스로 정보를 전달합니다. 이 문서에서는 사용자가 직접 빌드할 수 있도록 json 파일의 섹션을 설명합니다. 전체 .json 파일 예제를 보려면 [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)를 참조하세요.

기본 템플릿 형식은 다음과 같습니다.

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2020-02-14", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
                }
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>종류 및 API 버전

`type`는 리소스 종류로, `"Microsoft.VirtualMachineImages/imageTemplates"`이어야 합니다. `apiVersion`은 시간이 경과하면서 API 변경에 따라 달라지지만, 미리 보기에서는 `"2020-02-14"`여야 합니다.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2020-02-14",
```

## <a name="location"></a>위치

위치는 사용자 지정 이미지가 만들어질 지역입니다. Image Builder 미리 보기의 경우 다음 지역이 지원됩니다.

- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2
- 북유럽
- 서유럽


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
기본적으로 Image Builder는 "Standard_D1_v2" 빌드 VM을 사용합니다. 사용자가 이를 재정의할 수 있습니다. 예를 들어 GPU VM에 대한 이미지를 사용자 지정하려면 GPU VM 크기가 필요합니다. 이 구성 요소는 선택 사항입니다.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

기본적으로 Image Builder는 이미지 크기를 변경하지 않으며 원본 이미지의 크기를 사용합니다. OS 디스크의 크기를 늘릴 수 **만** 있습니다 (Win 및 Linux) .이는 선택 사항이 며 값 0은 원본 이미지와 동일한 크기를 유지 한다는 의미입니다. OS 디스크 크기는 원본 이미지의 크기 보다 작게 축소할 수 없습니다.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
VNET 속성을 지정하지 않으면 Image Builder에서 자체 VNET, 공용 IP 및 NSG를 만듭니다. 공용 IP는 서비스에서 빌드 VM과 통신하는 서비스에 사용됩니다. 그러나 공용 IP를 사용하지 않거나 Image Builder에 구성 서버(DSC, Chef, Puppet, Ansible), 파일 공유 등의 기존 VNET 리소스에 대한 액세스 권한을 부여하려는 경우 VNET을 지정할 수 있습니다. 자세한 내용은 [네트워킹 설명서](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder)를 검토하세요. 이 구성 요소는 선택 사항입니다.

```json
    "vnetConfig": {
        "subnetId": "/subscriptions/<subscriptionID>/resourceGroups/<vnetRgName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        }
    }
```
## <a name="tags"></a>태그들

생성된 이미지에 대해 지정할 수 있는 키/값 쌍입니다.

## <a name="depends-on-optional"></a>다음에 종속됨(선택 사항)

이 선택적 섹션을 사용하여 계속하기 전에 종속성이 완료되었는지 확인할 수 있습니다. 

```json
    "dependsOn": [],
```

자세한 내용은 [리소스 종속성 정의](../../azure-resource-manager/templates/define-resource-dependency.md#dependson)를 참조하세요.

## <a name="identity"></a>ID

필수-이미지 작성기에서 이미지를 읽고 쓸 수 있는 권한이 있어야 합니다. Azure Storage의 스크립트에서 읽기는 개별 리소스에 대 한 권한이 있는 Azure 사용자 할당 Id를 만들어야 합니다. 이미지 작성기 권한이 작동 하는 방법 및 관련 단계에 대 한 자세한 내용은 [설명서](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibPermissions.md#azure-vm-image-builder-permissions-explained-and-requirements)를 참조 하세요.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```


사용자 할당 Id에 대 한 이미지 작성기 지원:
* 단일 id만 지원 합니다.
* 사용자 지정 도메인 이름을 지원 하지 않습니다.

자세히 알아보려면 [Azure 리소스에 대한 관리 ID란?](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
이 기능을 배포하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity)을 참조하세요.

## <a name="properties-source"></a>속성: source

이미지 작성기는 현재 HyperV 1 이미지 및 Vm만 지원 하며,이 섹션에는 `source` 이미지 작성기에서 사용 되는 원본 이미지에 대 한 정보가 포함 되어 있습니다.

API에는 이미지 빌드에 대한 소스를 정의하는 'SourceType'이 필요합니다. 현재 세 가지 유형이 있습니다.
- PlatformImage - 원본 이미지가 Marketplace 이미지 임을 나타냅니다.
- ManagedImage - 일반 관리형 이미지에서 시작할 때 사용합니다.
- SharedImageVersion - Shared Image Gallery의 이미지 버전을 원본으로 사용하는 경우에 사용됩니다.


> [!NOTE]
> 기존 Windows 사용자 지정 이미지를 사용 하는 경우 단일 Windows 이미지에서 Sysprep 명령을 최대 8 번까지 실행할 수 있습니다. 자세한 내용은 [sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep) 설명서를 참조 하십시오.

### <a name="platformimage-source"></a>PlatformImage 원본 
Azure Image Builder는 Windows Server 및 클라이언트 그리고 Linux Azure Marketplace 이미지를 지원합니다. 전체 목록은 [여기](../windows/image-builder-overview.md#os-support)를 참조하세요. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


여기에 나와 있는 속성은 AZ CLI를 사용하여 VM을 만드는 데 사용되는 것과 동일합니다. 속성을 가져오려면 아래 명령을 실행합니다. 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

버전에서 '최신'을 사용할 수 있습니다. 버전은 템플릿이 제출될 때가 아니라 이미지 빌드가 수행될 때 평가됩니다. Shared Image Gallery 대상과 함께 이 기능을 사용하는 경우 템플릿을 다시 전송하지 않고 주기적으로 이미지 빌드를 다시 실행하여 가장 최근 이미지에서 이미지를 다시 만들 수 있습니다.

#### <a name="support-for-market-place-plan-information"></a>시장 출시 계획 정보 지원
계획 정보를 지정할 수도 있습니다. 예를 들면 다음과 같습니다.
```json
    "source": {
        "type": "PlatformImage",
        "publisher": "RedHat",
        "offer": "rhel-byos",
        "sku": "rhel-lvm75",
        "version": "latest",
        "planInfo": {
            "planName": "rhel-lvm75",
            "planProduct": "rhel-byos",
            "planPublisher": "redhat"
       }
```
### <a name="managedimage-source"></a>ManagedImage 원본

원본 이미지를 일반화된 VHD 또는 VM의 기존 관리형 이미지로 설정합니다.

> [!NOTE]
> 원본 관리 이미지는 지원 되는 OS 여야 하 고 이미지는 Azure 이미지 작성기 템플릿과 동일한 지역 이어야 합니다. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`는 관리형 이미지의 ResourceId여야 합니다. `az image list`를 사용하여 사용 가능한 이미지를 나열합니다.


### <a name="sharedimageversion-source"></a>SharedImageVersion 원본
Shared Image Gallery에서 원본 이미지를 기존 이미지 버전으로 설정합니다.

> [!NOTE]
> 원본 관리 이미지는 지원 되는 OS 여야 하 고 이미지는 Azure 이미지 작성기 템플릿과 동일한 지역 이어야 합니다. 그렇지 않은 경우 이미지 작성기 템플릿 영역에 이미지 버전을 복제 하세요.


```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`는 이미지 버전의 ResourceId여야 합니다. [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list)를 사용하여 이미지 버전을 나열합니다.


## <a name="properties-buildtimeoutinminutes"></a>속성: buildTimeoutInMinutes

기본적으로 Image Builder는 240분 동안 실행됩니다. 그런 다음, 이미지 빌드가 완료되었는지 여부에 관계없이 시간 초과가 발생하고 Image Builder가 중지됩니다. 시간 제한에 도달하면 다음과 같은 오류가 표시됩니다.

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

buildTimeoutInMinutes 값을 지정하지 않거나 0으로 설정하면 기본값이 사용됩니다. 최대 960분(16시간)까지 값을 늘리거나 줄일 수 있습니다. Windows의 경우 60분 미만으로 설정하지 않는 것이 좋습니다. 시간 제한에 도달하면 [로그](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)를 검토하여 사용자 지정 단계가 사용자 입력 등을 대기 중인지 확인합니다. 

사용자 지정을 완료하는 데 시간이 더 필요한 것으로 판단되는 경우 약간의 오버헤드를 고려하여 필요한 값을 설정합니다. 그러나 오류가 발생하기 전에 시간이 초과될 때까지 기다려야 할 수도 있으므로 너무 높게 설정하지 마세요. 


## <a name="properties-customize"></a>속성: customize

Image Builder는 여러 '사용자 지정자'를 지원합니다. 사용자 지정자는 스크립트를 실행하거나 서버를 다시 부팅하는 등 이미지를 사용자 지정하는 데 사용되는 함수입니다. 

`customize`를 사용하는 경우: 
- 여러 사용자 지정자를 사용할 수 있지만 각각 고유한 `name`이 있어야 합니다.
- 사용자 지정자는 템플릿에 지정된 순서대로 실행됩니다.
- 한 사용자 지정자가 실패하면 전체 사용자 지정 구성 요소가 실패하고 오류가 보고됩니다.
- 템플릿에서 사용하기 전에 스크립트를 철저히 테스트하는 것이 좋습니다. 사용자 VM에서 스크립트를 디버깅하는 것이 더 쉽습니다.
- 스크립트에 중요한 데이터를 넣지 마세요. 
- [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)를 사용하지 않는 경우 스크립트 위치는 공개적으로 액세스할 수 있어야 합니다.

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
사용자 지정 섹션은 배열입니다. Azure Image Builder는 이를 정렬된 순서로 실행합니다. 한 사용자 지정자라도 오류가 발생하면 빌드 프로세스가 실패합니다. 

> [!NOTE]
> 인라인 명령은 이미지 템플릿 정의와 지원 사례를 지원할 때 Microsoft 지원에서 볼 수 있습니다. 중요 한 정보가 있는 경우 액세스를 요구 하는 Azure Storage의 스크립트로 이동 해야 합니다.
 
### <a name="shell-customizer"></a>셸 사용자 지정자

셸 사용자 지정자는 셸 스크립트 실행을 지원합니다. 이러한 스크립트는 IB가 액세스하도록 공개적으로 액세스할 수 있어야 합니다.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS 지원: Linux 
 
사용자 지정 속성은 다음과 같습니다.

- **type** – 셸 
- **name** - 사용자 지정을 추적하기 위한 이름 
- **scriptUri** - 파일 위치에 대한 URI 
- **inline** - 쉼표로 구분된 셸 명령의 배열
- **sha256Checksum** - 파일의 sha256 체크섬 값. 이 값을 로컬로 생성하면 Image Builder가 체크섬 및 유효성 검사를 수행합니다.
    * sha256Checksum을 생성하려면 Mac/Linux에서 터미널을 사용하여 다음을 실행합니다. `sha256sum <fileName>`


슈퍼 사용자 권한으로 명령을 실행하려면 `sudo`를 접두사로 사용해야 합니다.

> [!NOTE]
> 인라인 명령은 이미지 템플릿 정의의 일부로 저장 되며, 이미지 정의를 덤프할 때 볼 수 있으며, 문제 해결을 위한 지원 사례의 경우 Microsoft 지원에도 표시 됩니다. 중요 한 명령 또는 값이 있는 경우 스크립트로 이동 하 고 사용자 id를 사용 하 여 Azure Storage에 인증 하는 것이 좋습니다.

### <a name="windows-restart-customizer"></a>Windows 다시 시작 사용자 지정자 
다시 시작 사용자 지정자를 사용하여 Windows VM을 다시 시작하고 다시 온라인 상태가 될 때까지 기다릴 수 있습니다. 그러면 다시 부팅해야 하는 소프트웨어를 설치할 수 있습니다.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS 지원: Windows
 
사용자 지정 속성은 다음과 같습니다.
- **형식**: WindowsRestart
- **restartCommand** - 다시 시작을 실행하는 명령입니다(선택 사항). 기본값은 `'shutdown /r /f /t 0 /c \"packer restart\"'`입니다.
- **restartCheckCommand** – 다시 시작이 성공 했는지 확인하는 명령입니다(선택 사항). 
- **restartTimeout** - 크기 및 단위 문자열로 지정된 다시 시작 시간 제한입니다. 예를 들어 `5m`(5분) 또는 `2h`(2시간)입니다. 기본값은 '5m'입니다.

### <a name="linux-restart"></a>Linux 다시 시작  
Linux 다시 시작 사용자 지정자는 없지만 드라이버를 설치하는 경우 또는 다시 시작해야 하는 구성 요소를 설치하는 경우 셸 사용자 지정자를 사용하여 설치하고 다시 시작을 호출할 수 있습니다. 빌드 VM에 대해 20분 SSH 시간 제한이 있습니다.

### <a name="powershell-customizer"></a>PowerShell 사용자 지정자 
셸 사용자 지정자는 PowerShell 스크립트 및 인라인 명령 실행을 지원합니다. IB가 액세스하도록 스크립트를 공개적으로 액세스할 수 있어야 합니다.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS 지원: Windows 및 Linux

사용자 지정 속성은 다음과 같습니다.

- **type** – PowerShell.
- **scriptUri** - PowerShell 스크립트 파일의 위치에 대한 URI입니다. 
- **inline** - 실행할 인라인 명령이며 쉼표로 구분됩니다.
- **validExitCodes** – 선택 사항. 스크립트/인라인 명령에서 반환될 수 있는 유효한 코드입니다. 이를 지정하면 스크립트/인라인 명령의 오류를 보고하지 않습니다.
- **runElevated** – 선택 사항, 부울. 상승된 권한으로 명령 및 스크립트를 실행하기 위한 지원입니다.
- **sha256Checksum** - 파일의 sha256 체크섬 값. 이 값을 로컬로 생성하면 Image Builder가 체크섬 및 유효성 검사를 수행합니다.
    * sha256Checksum을 생성하려면 Windows에서 PowerShell을 사용하여 [Get-Hash](/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)를 실행합니다.


### <a name="file-customizer"></a>파일 사용자 지정자

Image Builder는 파일 사용자 지정자를 사용하여 GitHub 또는 Azure 스토리지에서 파일을 다운로드할 수 있습니다. 빌드 아티팩트에 의존하는 이미지 빌드 파이프라인이 있는 경우 빌드 공유에서 다운로드하도록 파일 사용자 지정자를 설정하고 아티팩트를 이미지로 이동할 수 있습니다.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
         }
     ]
```

OS 지원: Linux 및 Windows 

파일 사용자 지정자 속성은 다음과 같습니다.

- **sourceUri** - 액세스할 수 있는 스토리지 엔드포인트로, GitHub 또는 Azure 스토리지일 수 있습니다. 전체 디렉터리가 아닌 하나의 파일만 다운로드할 수 있습니다. 디렉터리를 다운로드해야 하는 경우에는 압축된 파일을 사용하고 셸 또는 PowerShell 사용자 지정자를 사용하여 압축을 풉니다. 
- **destination** – 전체 대상 경로 및 파일 이름입니다. 참조된 경로와 하위 디렉터리가 있어야 합니다. 셸 또는 PowerShell을 사용하여 미리 해당 값을 설정합니다. 스크립트 사용자 지정자를 사용하여 경로를 만들 수 있습니다. 

이 속성은 Windows 디렉터리 및 Linux 경로에서 지원되지만 다음과 같은 몇 가지 차이점이 있습니다. 
- Linux OS – Image Builder가 쓸 수 있는 유일한 경로는 /tmp입니다.
- Windows – 경로 제한이 없지만 해당 경로가 존재해야 합니다.
 
 
파일을 다운로드하는 동안 오류가 발생하거나 지정된 디렉터리에 파일을 저장하는 동안 오류가 발생하면 사용자 지정 단계가 실패하고 이는 customization.log에 기록됩니다.

> [!NOTE]
> 파일 사용자 지정자는 20MB 이하 소용량 파일 다운로드에만 적합합니다. 대용량 파일 다운로드의 경우 스크립트 또는 인라인 명령을 사용합니다. 예를 들어, Linux의 `wget` 또는 `curl`, Windows의 `Invoke-WebRequest` 같은 코드를 사용하여 파일을 다운로드합니다.

[MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)를 사용하여 Azure Storage에서 파일 사용자 지정자의 파일을 다운로드할 수 있습니다.

### <a name="windows-update-customizer"></a>Windows 업데이트 사용자 지정자
이 사용자 지정자는 Packer(Packer 커뮤니티에서 관리하는 오픈 소스 프로젝트)용 [커뮤니티 Windows 업데이트 프로비저닝 프로그램](https://packer.io/docs/provisioners/community-supported.html)을 기반으로 합니다. Microsoft는 Image Builder 서비스에서 이 프로비저닝 프로그램을 테스트 및 유효성 검사를 하고, 관련 문제에 대한 조사를 지원하고, 문제를 해결하기 위해 노력하지만, 이 오픈 소스 프로젝트를 공식적으로 지원하지는 않습니다. 이 Windows 업데이트 프로비저닝 프로그램에 대한 자세한 설명서 및 도움말은 프로젝트 리포지토리를 참조하세요.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

사용자 지정 속성은 다음과 같습니다.
- **type** – WindowsUpdate.
- **searchCriteria** - 선택 사항. 설치되는 업데이트 유형(권장, 중요 등)을 정의합니다. BrowseOnly=0 및 IsInstalled=0(권장)이 기본값입니다.
- **filters** – 선택 사항. 업데이트를 포함하거나 제외하도록 필터를 지정할 수 있습니다.
- **updateLimit** – 선택 사항. 설치할 수 있는 업데이트 수를 정의합니다. 기본값은 1000입니다.
 
> [!NOTE]
> 처리 중인 Windows를 다시 시작 하거나 응용 프로그램 설치를 계속 실행 하는 경우 Windows 업데이트 사용자 지정 자가 실패할 수 있습니다. 일반적으로 사용자 지정. 로그에서이 오류가 표시 될 수 `System.Runtime.InteropServices.COMException (0x80240016): Exception from HRESULT: 0x80240016` 있습니다. Windows를 다시 시작할 때 추가 하는 것이 좋습니다 https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/start-sleep?view=powershell-7) . 또는 Windows 업데이트 실행 하기 전에 인라인 명령이 나 스크립트를 사용 하 여 응용 프로그램에서 설치를 완료 하는 데 충분 한 시간을 허용 하는 것이 좋습니다.

### <a name="generalize"></a>일반화 
기본적으로 Azure Image Builder는 각 이미지 사용자 지정 단계가 끝날 때 '프로비전 해제' 코드를 실행하여 이미지를 '일반화'합니다. 일반화는 여러 VM을 만드는 데 다시 사용할 수 있도록 이미지를 설정하는 프로세스입니다. Windows VM의 경우 Azure Image Builder는 Sysprep을 사용합니다. Linux의 경우 Azure Image Builder는 'waagent -deprovision'을 실행합니다. 

Azure Image Builder가 일반화에 사용하는 명령은 모든 상황에 적합하지 않을 수 있으므로, 필요한 경우 이 명령을 사용자 지정할 수 있습니다. 

기존 사용자 지정을 마이그레이션하고 다른 Sysprep/waagent 명령을 사용하는 경우 Image Builder 일반 명령을 사용할 수 있습니다. VM 만들기가 실패하는 경우 자체 Sysprep 또는 waagent 명령을 사용하세요.

Azure Image Builder에서 성공적으로 Windows 사용자 지정 이미지를 만들고 이 이미지에서 VM을 만들지만 VM 만들기가 실패하거나 성공적으로 완료되지 않을 경우 Windows Server Sysprep 설명서를 검토하거나 Sysprep을 올바르게 사용하도록 문제 해결 및 지원을 제공할 수 있는 Windows Server Sysprep 고객 서비스 지원 팀에게 지원 요청을 제기해야 합니다.


#### <a name="default-sysprep-command"></a>기본 Sysprep 명령
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```
#### <a name="default-linux-deprovision-command"></a>기본 Linux 프로비전 해제 명령

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>명령 재정의
명령을 재정의하려면 PowerShell 또는 셸 스크립트 프로비저닝 프로그램을 사용하여 정확한 파일 이름으로 명령 파일을 만들고 올바른 디렉터리에 배치합니다.

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder는 이러한 명령을 읽어 AIB 로그 ‘customization.log’에 기록합니다. 로그를 수집하는 방법은 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs)을 참조하세요.
 
## <a name="properties-distribute"></a>속성: distribute

Azure Image Builder는 다음과 같은 세 가지 배포 대상을 지원합니다. 

- **managedImage** - 관리형 이미지입니다.
- **sharedImage**- Shared Image Gallery입니다.
- **VHD** - 스토리지 계정의 VHD입니다.

동일한 구성에서 두 대상 유형 모두에 이미지를 배포할 수 있습니다.

> [!NOTE]
> 기본 AIB sysprep 명령은 "/mode: vm"을 포함 하지 않습니다. 그러나 HyperV 역할을 설치 하는 이미지를 만들 때이 작업이 필요할 수 있습니다. 이 명령 인수를 추가 해야 하는 경우 sysprep 명령을 재정의 해야 합니다.

배포할 대상이 둘 이상 있을 수 있으므로 Image Builder는 `runOutputName`을 쿼리하여 액세스할 수 있는 모든 배포 대상의 상태를 유지 관리합니다.  `runOutputName`은 배포 후 해당 배포에 대한 정보를 위해 쿼리할 수 있는 개체입니다. 예를 들어 VHD의 위치 또는 이미지 버전이 복제된 지역 또는 생성된 SIG 이미지 버전을 쿼리할 수 있습니다. 이는 모든 배포 대상의 속성입니다. `runOutputName`은 각 배포 대상에 고유해야 합니다. 다음은 Shared Image Gallery 배포를 쿼리하는 예제입니다.

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2020-02-14
```

출력:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

### <a name="distribute-managedimage"></a>Distribute: managedImage

이미지 출력은 관리형 이미지 리소스입니다.

```json
{
       "type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
            "<name>": "<value>"
        }
}
```
 
배포 속성은 다음과 같습니다.
- **type** – managedImage 
- **imageId** – 대상 이미지의 리소스 ID입니다. 형식이 필요 합니다./Subscriptions/ \<subscriptionId> /Sggg/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
- **location** - 관리형 이미지의 위치입니다.  
- **runOutputName** – 분포를 식별하는 고유 이름입니다.  
- **artifactTags** - 선택 사항. 사용자 지정 키 값 쌍 태그입니다.
 
 
> [!NOTE]
> 대상 리소스 그룹이 존재해야 합니다.
> 다른 지역에 이미지를 배포하려는 경우 배포 시간이 늘어납니다. 

### <a name="distribute-sharedimage"></a>Distribute: sharedImage 
Azure Shared Image Gallery는 이미지 영역 복제, 버전 관리, 사용자 지정 이미지 공유를 관리하는 데 사용할 수 있는 새로운 이미지 관리 서비스입니다. Azure Image Builder는 이 서비스를 사용한 배포를 지원하므로 공유 이미지 갤러리에서 지원하는 지역에 이미지를 배포할 수 있습니다. 
 
Shared Image Gallery의 구성 요소는 다음과 같습니다. 
 
- 갤러리 - 여러 공유 이미지의 컨테이너입니다. 갤러리는 한 지역에 배포됩니다.
- 이미지 정의 - 이미지에 대한 개념적 그룹화입니다. 
- 이미지 버전 - VM 또는 확장 집합을 배포하는 데 사용되는 이미지 형식입니다. 이미지 버전은 VM을 배포해야 하는 다른 지역으로 복제할 수 있습니다.
 
이미지 갤러리에 배포하려면 먼저 갤러리 및 이미지 정의를 만들어야 합니다. [공유 이미지](../shared-images-cli.md)를 참조하세요. 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

공유 이미지 갤러리의 배포 속성은 다음과 같습니다.

- **type** - sharedImage  
- **galleryImageId** – 공유 이미지 갤러리의 ID 이며, 다음 두 가지 형식으로 지정할 수 있습니다.
    * 자동 버전 관리-이미지 빌더는 단조 버전 번호를 생성 합니다 .이는 동일한 템플릿에서 이미지를 다시 작성 하려는 경우에 유용 합니다. 형식: `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>`
    * 명시적 버전 관리-이미지 작성기에서 사용 하려는 버전 번호를 전달할 수 있습니다. 형식은 다음과 같습니다. `/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<sharedImageGalName>/images/<imageDefName>/versions/<version e.g. 1.1.1>`

- **runOutputName** – 분포를 식별하는 고유 이름입니다.  
- **artifactTags** - 선택 사항. 사용자 지정 키 값 쌍 태그입니다.
- **replicationRegions** - 복제용 지역의 배열입니다. 지역 중 하나는 갤러리가 배포된 지역이어야 합니다. 영역을 추가 하는 것은 복제가 완료 될 때까지 빌드가 완료 되지 않으므로 빌드 시간이 증가 한다는 것을 의미 합니다.
- **excludeFromLatest** (선택 사항)이 옵션을 사용 하면 사용자가 만든 이미지 버전을 SIG 정의에서 최신 버전으로 표시할 수 있습니다. 기본값은 ' f a l s e '입니다.
- **Storageaccounttype** (옵션) AIB는 만들 이미지 버전에 대해 다음과 같은 유형의 저장소를 지정할 수 있도록 지원 합니다.
    * "Standard_LRS"
    * "Standard_ZRS"


> [!NOTE]
> 이미지 템플릿이 `image definition` 동일한 위치에 있지 않으면 이미지를 만드는 데 추가 시간이 표시 됩니다. 이미지 작성기에는 현재 `location` 이미지 버전 리소스에 대 한 매개 변수가 없으므로 부모에서 가져옵니다 `image definition` . 예를 들어 이미지 정의가 westus에 있고 이미지 버전이 eastus에 복제 되 게 하려면 blob을 westus에 복사 하 고,이에서 westus의 이미지 버전 리소스를 만든 다음 eastus에 복제 합니다. 추가 복제 시간을 방지 하려면 `image definition` 및 이미지 템플릿이 동일한 위치에 있어야 합니다.


### <a name="distribute-vhd"></a>배포: VHD  
VHD로 출력할 수 있습니다. 그런 다음 VHD를 복사하여 Azure MarketPlace에 게시하거나 Azure Stack과 함께 사용할 수 있습니다.  

```json
{ 
    "type": "VHD",
    "runOutputName": "<VHD name>",
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
    }
}
```
 
OS 지원: Windows 및 Linux

VHD 배포 매개 변수:

- **type** - VHD.
- **runOutputName** – 분포를 식별하는 고유 이름입니다.  
- **tags** - 선택 사항. 사용자 지정 키 값 쌍 태그입니다.
 
Azure Image Builder에서는 사용자가 스토리지 계정 위치를 지정할 수 없지만, `runOutputs`의 상태를 쿼리하여 위치를 가져올 수 있습니다.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD를 만든 후에는 가능한 한 빨리 다른 위치로 복사합니다. VHD는 이미지 템플릿이 Azure Image Builder 서비스로 제출될 때 생성된 임시 리소스 그룹에서 스토리지 계정에 저장됩니다. 이미지 템플릿을 삭제하면 VHD가 손실됩니다. 

## <a name="image-template-operations"></a>이미지 템플릿 작업

### <a name="starting-an-image-build"></a>이미지 빌드 시작
빌드를 시작 하려면 이미지 템플릿 리소스에서 ' 실행 '을 호출 해야 합니다 `run` . 명령의 예는 다음과 같습니다.

```PowerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Run -Force
```


```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

### <a name="cancelling-an-image-build"></a>이미지 빌드 취소
잘못 된 것으로 생각 되는 이미지 빌드를 실행 하 고 사용자 입력을 기다리거나 성공적으로 완료 되지 않을 경우 빌드를 취소할 수 있습니다.

언제 든 지 빌드를 취소할 수 있습니다. 배포 단계가 시작 된 경우에도 취소할 수 있지만 완료 하지 못할 수 있는 이미지를 모두 정리 해야 합니다. 취소 명령이 취소를 완료할 때까지 기다리지 않습니다 `lastrunstatus.runstate` . 다음 상태 [명령을](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#get-statuserror-of-the-template-submission-or-template-build-status)사용 하 여 진행률 취소를 모니터링 하세요.


명령의 예는 `cancel` 다음과 같습니다.

```powerShell
Invoke-AzResourceAction -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14" -Action Cancel -Force
```

```bash
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Cancel 
```

## <a name="next-steps"></a>다음 단계

[Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder)에 다양한 시나리오에 대한 샘플 .json 파일이 있습니다.
