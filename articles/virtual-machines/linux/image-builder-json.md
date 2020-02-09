---
title: Azure 이미지 작성기 템플릿 만들기 (미리 보기)
description: Azure 이미지 작성기에서 사용할 템플릿을 만드는 방법에 대해 알아봅니다.
author: danis
ms.author: danis
ms.date: 01/23/2020
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 4ac2c1266933716697a658b1ba88efd8c2f05d34
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905751"
---
# <a name="preview-create-an-azure-image-builder-template"></a>미리 보기: Azure 이미지 작성기 템플릿 만들기 

Azure 이미지 작성기는 json 파일을 사용 하 여 이미지 작성기 서비스로 정보를 전달 합니다. 이 문서에서는 json 파일의 섹션을 설명 하므로 사용자가 직접 빌드할 수 있습니다. Full json 파일의 예를 보려면 [Azure 이미지 작성기 GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)를 참조 하세요.

기본 템플릿 형식은 다음과 같습니다.

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>"
            },
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>유형 및 API 버전

`type`는 리소스 유형으로 `"Microsoft.VirtualMachineImages/imageTemplates"`해야 합니다. `apiVersion` API가 변경 될 때 시간이 지남에 따라 변경 되지만 미리 보기를 위해 `"2019-05-01-preview"` 해야 합니다.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>위치

위치는 사용자 지정 이미지가 생성 될 지역입니다. 이미지 작성기 미리 보기의 경우 다음 지역이 지원 됩니다.

- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
기본적으로 이미지 작성기는 "Standard_D1_v2" 빌드 VM을 사용 합니다 .이를 재정의할 수 있습니다. 예를 들어 GPU VM에 대 한 이미지를 사용자 지정 하려는 경우 GPU VM 크기가 필요 합니다. 이 구성 요소는 선택 사항입니다.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

기본적으로 이미지 작성기는 이미지 크기를 변경 하지 않으며 원본 이미지의 크기를 사용 합니다. Os 디스크의 크기를 조정할 수 있습니다 (Win 및 Linux). 참고로, OS에 필요한 최소 필요한 공간 보다 너무 작게 이동 하지 마십시오. 이 값은 선택 사항이 며 값이 0 이면 원본 이미지와 크기가 동일 하 게 유지 됩니다. 이 구성 요소는 선택 사항입니다.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="tags"></a>태그

이는 생성 된 이미지에 대해 지정할 수 있는 키/값 쌍입니다.

## <a name="depends-on-optional"></a>다음에 종속 (옵션)

이 선택적 섹션을 사용 하 여 계속 하기 전에 종속성이 완료 되는지 확인할 수 있습니다. 

```json
    "dependsOn": [],
```

자세한 내용은 [리소스 종속성 정의](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson)를 참조 하세요.

## <a name="identity"></a>ID
기본적으로 이미지 작성기는 스크립트를 사용 하거나 GitHub 및 Azure storage와 같은 여러 위치에서 파일을 복사 하는 것을 지원 합니다. 이를 사용 하려면 공개적으로 액세스할 수 있어야 합니다.

사용자가 정의한 Azure 사용자 할당 관리 Id를 사용 하 여 Azure storage 계정에 최소 ' 저장소 Blob 데이터 판독기 '가 id에 부여 된 경우에만 이미지 작성기 액세스를 Azure Storage 수 있습니다. 즉, 저장소 blob을 외부에서 액세스할 수 없도록 하거나 SAS 토큰을 설정할 필요가 없습니다.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

전체 예제를 보려면 [Azure 사용자 할당 관리 id를 사용 하 여 Azure Storage의 파일에 액세스](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)를 참조 하세요.

사용자 할당 Id에 대 한 이미지 작성기 지원: • 단일 id만 지원 합니다. • 사용자 지정 도메인 이름을 지원 하지 않습니다.

자세한 내용은 [Azure 리소스에 대 한 관리 되는 id 란?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)을 참조 하세요.
이 기능을 배포 하는 방법에 대 한 자세한 내용은 [Azure CLI를 사용 하 여 AZURE VM에서 azure 리소스에 대 한 관리 Id 구성](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity)을 참조 하세요.

## <a name="properties-source"></a>속성: 원본

`source` 섹션에는 이미지 작성기에서 사용 되는 원본 이미지에 대 한 정보가 포함 되어 있습니다.

API에는 이미지 빌드에 대 한 소스를 정의 하는 ' SourceType '가 필요 합니다. 현재 세 가지 유형이 있습니다.
- ISO-원본이 RHEL ISO 인 경우이를 사용 합니다.
- PlatformImage-원본 이미지가 Marketplace 이미지 임을 나타냅니다.
- ManagedImage-일반적인 관리 되는 이미지에서 시작할 때 사용 합니다.
- SharedImageVersion-공유 이미지 갤러리의 이미지 버전을 원본으로 사용 하는 경우에 사용 됩니다.

### <a name="iso-source"></a>ISO 원본

Azure 이미지 빌더는 미리 보기를 위해 게시 된 Red Hat Enterprise Linux 7.x 이진 DVD Iso 사용만 지원 합니다. 이미지 작성기는 다음을 지원 합니다.
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

`sourceURI` 및 `sha256Checksum` 값을 가져오려면 `https://access.redhat.com/downloads`로 이동 하 여 제품 **Red Hat Enterprise Linux**를 선택 하 고 지원 되는 버전을 선택 합니다. 

**Red Hat Enterprise Linux Server에 대 한 설치 관리자 및 이미지**목록에서 Red Hat Enterprise Linux 7.X 이진 DVD에 대 한 링크 및 체크섬을 복사 해야 합니다.

> [!NOTE]
> 링크의 액세스 토큰은 자주 새로 고쳐집니다. 따라서 템플릿을 제출할 때마다 RH 링크 주소가 변경 되었는지 확인 해야 합니다.
 
### <a name="platformimage-source"></a>PlatformImage 원본 
Azure 이미지 작성기는 Windows Server 및 클라이언트 및 Linux Azure Marketplace 이미지를 지원 합니다. 전체 목록은 [여기](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) 를 참조 하세요. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


여기에 나와 있는 속성은 AZ CLI를 사용 하 여 VM을 만드는 데 사용 되는 것과 같은 속성을 가져오기 위해 아래를 실행 합니다. 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> 버전은 ' 최신 ' 일 수 없습니다. 버전 번호를 가져오려면 위의 명령을 사용 해야 합니다. 

### <a name="managedimage-source"></a>ManagedImage 원본

원본 이미지를 일반화 된 VHD 또는 VM의 기존 관리 이미지로 설정 합니다. 원본 관리 이미지는 지원 되는 OS 여야 하며, Azure 이미지 작성기 템플릿과 동일한 지역에 있어야 합니다. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`는 관리 되는 이미지의 ResourceId 여야 합니다. `az image list` 사용 하 여 사용 가능한 이미지를 나열 합니다.


### <a name="sharedimageversion-source"></a>SharedImageVersion 원본
공유 이미지 갤러리에서 원본 이미지를 기존 이미지 버전으로 설정 합니다. 이미지 버전은 지원 되는 OS 여야 하 고 이미지는 Azure 이미지 작성기 템플릿과 동일한 지역에 복제 되어야 합니다. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`는 이미지 버전의 ResourceId 여야 합니다. [Az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list) 를 사용 하 여 이미지 버전을 나열 합니다.

## <a name="properties-buildtimeoutinminutes"></a>속성: buildTimeoutInMinutes

기본적으로 이미지 작성기는 240 분 동안 실행 됩니다. 그런 다음, 이미지 빌드가 완료 되었는지 여부에 관계 없이 시간 제한이 발생 하 고 중지 됩니다. 제한 시간에 도달 하면 다음과 유사한 오류가 표시 됩니다.

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

BuildTimeoutInMinutes 값을 지정 하지 않거나 0으로 설정 하면에서 기본값을 사용 합니다. 최대 960mins (16hrs)까지 값을 늘리거나 줄일 수 있습니다. Windows의 경우 60 분 미만으로 설정 하지 않는 것이 좋습니다. 시간 제한에 도달 하는 경우 [로그](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)를 검토 하 여 사용자 지정 단계가 사용자 입력과 같이 대기 중인지 확인 합니다. 

사용자 지정을 완료 하는 데 시간이 더 필요한 경우 약간의 오버 헤드를 고려 하 여이를 필요한 것으로 설정 합니다. 그러나 오류가 발생 하기 전에 시간이 초과 될 때까지 기다려야 할 수도 있으므로 너무 높게 설정 하지 마십시오. 


## <a name="properties-customize"></a>속성: 사용자 지정

이미지 작성기는 여러 ' 개인 지정자 '를 지원 합니다. 사용자 지정자는 스크립트를 실행 하거나 서버를 다시 부팅 하는 등 이미지를 사용자 지정 하는 데 사용 되는 함수입니다. 

`customize`사용 하는 경우: 
- 여러 사용자 지정자를 사용할 수 있지만 고유한 `name`있어야 합니다.
- 서식 지정자는 템플릿에 지정 된 순서 대로 실행 됩니다.
- 한 사용자 지정 자가 실패 하면 전체 사용자 지정 구성 요소가 실패 하 고 오류가 다시 보고 됩니다.
- 템플릿을 사용 하기 전에 스크립트를 철저히 테스트 하는 것이 좋습니다. 사용자 고유의 VM에서 스크립트를 디버깅 하는 것이 더 쉽습니다.
- 스크립트에 중요 한 데이터를 넣지 마세요. 
- [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)를 사용 하지 않는 경우 스크립트 위치는 공개적으로 액세스할 수 있어야 합니다.

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

 
사용자 지정 섹션은 배열입니다. Azure 이미지 작성기는이를 순차적인 순서 대로 실행 합니다. 모든 사용자에 게 오류가 발생 하면 빌드 프로세스가 실패 합니다. 
 
 
### <a name="shell-customizer"></a>셸 사용자 지정자

셸 사용자 지정자는 셸 스크립트 실행을 지원 합니다. 이러한 스크립트는 IB에 액세스 하기 위해 공개적으로 액세스할 수 있어야 합니다.

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
 
사용자 지정 속성:

- **유형** – Shell 
- **이름** -사용자 지정을 추적 하기 위한 이름 
- **scriptUri** -파일의 위치에 대 한 URI입니다. 
- 쉼표로 구분 된 셸 명령의 **인라인** 배열입니다.
- **sha256Checksum** -파일의 sha256 체크섬 값을 로컬로 생성 한 다음 이미지 작성기에서 체크섬 및 유효성 검사를 수행 합니다.
    * Sha256Checksum를 생성 하려면 Mac/Linux에서 터미널을 사용 하 여 다음을 실행 합니다. `sha256sum <fileName>`


슈퍼 사용자 권한으로 명령을 실행 하려면 `sudo`앞에와 야 합니다.

> [!NOTE]
> RHEL ISO 원본을 사용 하 여 셸 사용자 지정을 실행 하는 경우 사용자 지정이 발생 하기 전에 첫 번째 사용자 지정 셸에서 Red Hat 자격 서버 등록을 처리 하는지 확인 해야 합니다. 사용자 지정이 완료 되 면 스크립트는 자격 서버에서 등록을 취소 해야 합니다.

### <a name="windows-restart-customizer"></a>Windows 다시 시작 사용자 지정자 
다시 시작 사용자 지정자를 사용 하 여 Windows VM을 다시 시작 하 고 다시 온라인 상태가 될 때까지 기다릴 수 있습니다. 그러면 다시 부팅 해야 하는 소프트웨어를 설치할 수 있습니다.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0 /c", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS 지원: Windows
 
사용자 지정 속성:
- **유형**: windowsrestart
- **restartCommand** -restart를 실행 하는 명령입니다 (선택 사항). 기본값은 `'shutdown /r /f /t 0 /c \"packer restart\"'`입니다.
- **restartCheckCommand** – 다시 시작 성공 여부를 확인 하는 명령입니다 (선택 사항). 
- **restartTimeout** -크기 및 단위의 문자열로 지정 된 다시 시작 시간 제한입니다. 예를 들어 `5m` (5 분) 또는 `2h` (2 시간)입니다. 기본값은 ' 5m '입니다.

### <a name="linux-restart"></a>Linux 다시 시작  
Linux 다시 시작 사용자 지정자는 없지만 드라이버를 설치 하는 경우 또는 다시 시작 해야 하는 구성 요소를 설치 하는 경우 셸 사용자 지정자를 사용 하 여 설치 하 고 다시 시작을 호출할 수 있으며, 빌드 VM에 대해 20min의 SSH 시간 제한이 있습니다.

### <a name="powershell-customizer"></a>PowerShell 사용자 지정자 
셸 사용자 지정자는 PowerShell 스크립트 및 인라인 명령 실행을 지원 합니다. IB에서 액세스할 수 있도록 스크립트를 공개적으로 액세스할 수 있어야 합니다.

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
             "valid_exit_codes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS 지원: Windows 및 Linux

사용자 지정 속성:

- **유형** – PowerShell
- **scriptUri** -PowerShell 스크립트 파일의 위치에 대 한 URI입니다. 
- **인라인** – 실행할 인라인 명령이 며 쉼표로 구분 됩니다.
- **valid_exit_codes** – 스크립트/인라인 명령에서 반환 될 수 있는 유효한 코드 (선택 사항)-스크립트/인라인 명령의 오류를 보고 하지 않습니다.
- **runelevated** – 선택적, 부울, 상승 된 권한으로 명령 및 스크립트를 실행 하기 위한 지원입니다.
- **sha256Checksum** -파일의 sha256 체크섬 값을 로컬로 생성 한 다음 이미지 작성기에서 체크섬 및 유효성 검사를 수행 합니다.
    * [Windows에서](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6) PowerShell을 사용 하 여 sha256Checksum을 생성 하려면


### <a name="file-customizer"></a>파일 사용자 지정자

이미지 작성기는 파일 사용자 지정자를 사용 하 여 GitHub 또는 Azure storage에서 파일을 다운로드할 수 있습니다. 빌드 아티팩트에 의존 하는 이미지 빌드 파이프라인이 있는 경우 빌드 공유에서 다운로드 하도록 파일 사용자 지정자를 설정 하 고 아티팩트를 이미지로 이동할 수 있습니다.  

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

파일 사용자 지정자 속성:

- **Sourceuri** -액세스 가능한 저장소 엔드포인트으로, GitHub 또는 Azure 저장소 일 수 있습니다. 전체 디렉터리가 아닌 하나의 파일만 다운로드할 수 있습니다. 디렉터리를 다운로드 해야 하는 경우에는 압축 된 파일을 사용 하 고 Shell 또는 PowerShell 사용자 지정자를 사용 하 여 압축을 풉니다. 
- **destination** – 전체 대상 경로 및 파일 이름입니다. 참조 된 경로와 하위 디렉터리가 있어야 합니다. 셸 또는 PowerShell을 사용 하 여 미리 설정 합니다. 스크립트 사용자 지정자를 사용 하 여 경로를 만들 수 있습니다. 

Windows 디렉터리 및 Linux 경로에서 지원 되지만 다음과 같은 몇 가지 차이점이 있습니다. 
- Linux OS – 이미지 작성기에서 쓸 수 있는 유일한 경로는/tmp.입니다.
- Windows – 경로 제한이 없지만 경로가 있어야 합니다.
 
 
파일을 다운로드 하는 동안 오류가 발생 하거나 지정 된 디렉터리에 파일을 저장 하는 동안 오류가 발생 하면 사용자 지정 단계가 실패 하 고이는 사용자 지정 .log에 있습니다.

> [!NOTE]
> 파일 사용자 지정자는 < 20MB 작은 파일 다운로드에만 적합 합니다. 대용량 파일 다운로드의 경우 스크립트 또는 인라인 명령을 사용 합니다. 예를 들어, Linux `wget` 또는 `curl`, Windows, `Invoke-WebRequest`와 같은 파일을 다운로드 하는 코드를 사용 합니다.

파일 사용자 지정자의 파일은 [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage)를 사용 하 여 Azure Storage에서 다운로드할 수 있습니다.

### <a name="generalize"></a>일반화 
기본적으로 Azure 이미지 작성기는 각 이미지 사용자 지정 단계가 끝날 때 ' 프로 비전 해제 ' 코드를 실행 하 여 이미지를 ' 일반화 ' 합니다. 일반화는 이미지를 설정 하 여 여러 Vm을 만드는 데 사용할 수 있는 프로세스입니다. Windows Vm의 경우 Azure 이미지 작성기는 Sysprep을 사용 합니다. Linux의 경우 Azure 이미지 작성기는 ' waagent-프로 비전 해제 '를 실행 합니다. 

이미지 작성기 사용자가 일반화 하는 명령은 모든 상황에 적합 하지 않을 수 있으므로 필요한 경우 Azure 이미지 작성기를 사용 하 여이 명령을 사용자 지정할 수 있습니다. 

기존 사용자 지정을 마이그레이션하는 경우 다른 Sysprep/waagent 명령을 사용 하는 경우 이미지 작성기 일반 명령을 사용 하 고 VM을 만들 수 없는 경우 사용자 고유의 Sysprep 또는 waagent 명령을 사용 합니다.

Azure 이미지 작성기에서 Windows 사용자 지정 이미지를 만들고이 이미지에서 VM을 만든 경우 VM 만들기가 실패 하거나 성공적으로 완료 되지 않는 것을 알 수 있습니다. Windows Server Sysprep 설명서를 검토 하거나 다음을 사용 하 여 지원 요청을 실행 해야 합니다. 올바른 Sysprep 사용에 대 한 문제를 해결 하 고 advise 할 수 있는 Windows Server Sysprep 고객 서비스 지원 팀입니다.


#### <a name="default-sysprep-command"></a>기본 Sysprep 명령
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>기본 Linux 프로 비전 해제 명령

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>명령 재정의
명령을 재정의 하려면 PowerShell 또는 Shell 스크립트 provisioners를 사용 하 여 정확한 파일 이름으로 명령 파일을 만들고 올바른 디렉터리에 배치 합니다.

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

이미지 작성기는 이러한 명령을 읽어 AIB 로그 (' 사용자 지정. 로그 ')에 기록 합니다. 로그를 수집 하는 방법에 대 한 [문제 해결](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) 을 참조 하세요.
 
## <a name="properties-distribute"></a>속성: 배포

Azure 이미지 작성기는 다음과 같은 세 가지 배포 대상을 지원 합니다. 

- **managedImage** -관리 되는 이미지입니다.
- **sharedImage** -공유 이미지 갤러리.
- 저장소 계정의 **vhd** -vhd

동일한 구성에서 두 대상 유형에 이미지를 배포할 수 있습니다. [예](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80)를 참조 하세요.

배포할 대상이 둘 이상 있을 수 있으므로 이미지 작성기는 `runOutputName`을 쿼리하여 액세스할 수 있는 모든 배포 대상의 상태를 유지 관리 합니다.  `runOutputName`은 해당 배포에 대 한 정보를 게시 하는 배포를 쿼리할 수 있는 개체입니다. 예를 들어 VHD의 위치 또는 이미지 버전이 복제 된 지역 또는 SIG 이미지 버전이 생성 된 영역을 쿼리할 수 있습니다. 모든 배포 대상의 속성입니다. `runOutputName`은 각 배포 대상에 대해 고유 해야 합니다. 다음은 공유 이미지 갤러리 배포를 쿼리 하는 예제입니다.

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
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

### <a name="distribute-managedimage"></a>배포: managedImage

이미지 출력은 관리 되는 이미지 리소스입니다.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
배포 속성:
- **유형** – managedImage 
- **imageId** – 대상 이미지의 리소스 ID입니다. 예상 형식:/Subscriptions/\<subscriptionId >/Wsourcegs/\<destinationResourceGroupName >/Providers/Microsoft.Compute/images/\<imageName >
- **location** -관리 되는 이미지의 위치입니다.  
- **runOutputName** – 분포를 식별 하는 고유 이름입니다.  
- **artifacttags** -선택적 사용자 지정 키 값 쌍 태그입니다.
 
 
> [!NOTE]
> 대상 리소스 그룹이 있어야 합니다.
> 다른 지역에 이미지를 배포 하려는 경우 배포 시간이 늘어납니다. 

### <a name="distribute-sharedimage"></a>배포: sharedImage 
Azure 공유 이미지 갤러리는 이미지 영역 복제, 버전 관리 및 사용자 지정 이미지 공유를 관리 하는 데 사용할 수 있는 새로운 이미지 관리 서비스입니다. Azure 이미지 작성기는이 서비스와의 배포를 지원 하므로 공유 이미지 갤러리에서 지 원하는 지역에 이미지를 배포할 수 있습니다. 
 
공유 이미지 갤러리는 다음과 같은 요소로 구성 됩니다. 
 
- Gallery-여러 공유 이미지에 대 한 컨테이너입니다. 갤러리는 한 지역에 배포 됩니다.
- 이미지 정의-이미지에 대 한 개념적 그룹화입니다. 
- 이미지 버전-VM 또는 확장 집합을 배포 하는 데 사용 되는 이미지 형식입니다. 이미지 버전은 Vm을 배포 해야 하는 다른 지역으로 복제할 수 있습니다.
 
이미지 갤러리에 배포 하려면 먼저 갤러리 및 이미지 정의를 만들어야 합니다. [공유 이미지](shared-images.md)를 참조 하세요. 

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

공유 이미지 갤러리에 대 한 속성 배포:

- **type** - sharedImage  
- **galleryImageId** – 공유 이미지 갤러리의 ID입니다. 형식은 다음과 같습니다./subscriptions/\<subscriptionId >/Hsourceg/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName >/images/\<imageGalleryName >.
- **runOutputName** – 분포를 식별 하는 고유 이름입니다.  
- **artifacttags** -선택적 사용자 지정 키 값 쌍 태그입니다.
- **replicationRegions** -복제용 지역 배열입니다. 영역 중 하나는 갤러리가 배포 된 지역 이어야 합니다.
 
> [!NOTE]
> 갤러리에 대 한 다른 지역에서 Azure 이미지 작성기를 사용할 수 있지만 Azure 이미지 작성기 서비스는 데이터 센터 간에 이미지를 전송 해야 하므로 더 오래 걸립니다. 이미지 작성기는 단조 정수를 기준으로 이미지의 버전을 자동으로 지정 하므로 현재는 지정할 수 없습니다. 

### <a name="distribute-vhd"></a>배포: VHD  
VHD로 출력할 수 있습니다. 그런 다음 VHD를 복사 하 여 Azure MarketPlace에 게시 하거나 Azure Stack와 함께 사용할 수 있습니다.  

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

VHD 매개 변수 배포:

- **유형** -VHD.
- **runOutputName** – 분포를 식별 하는 고유 이름입니다.  
- **태그** -선택적으로 사용자 지정 키 값 쌍 태그입니다.
 
Azure 이미지 작성기에서는 사용자가 저장소 계정 위치를 지정할 수 없지만, `runOutputs` 상태를 쿼리하여 위치를 가져올 수 있습니다.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> VHD를 만든 후에는 가능한 한 빨리 다른 위치로 복사 합니다. VHD는 이미지 템플릿이 Azure 이미지 작성기 서비스로 전송 될 때 생성 된 임시 리소스 그룹의 저장소 계정에 저장 됩니다. 이미지 템플릿을 삭제 하면 VHD가 손실 됩니다. 
 
## <a name="next-steps"></a>다음 단계

[Azure 이미지 작성기 GitHub](https://github.com/danielsollondon/azvmimagebuilder)에는 다양 한 시나리오에 대 한 샘플 json 파일이 있습니다.
 
