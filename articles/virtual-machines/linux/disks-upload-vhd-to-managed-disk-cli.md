---
title: Azure에 VHD 업로드 또는 지역 간에 디스크 복사-Azure CLI
description: 직접 업로드를 통해 Azure 관리 디스크에 VHD를 업로드 하 고 Azure CLI를 사용 하 여 여러 지역에 관리 되는 디스크를 복사 하는 방법을 알아봅니다.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: c7eb50caa4e7f0505809da64dd0309c6e0b8709f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691346"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Azure에 VHD를 업로드 하거나 관리 디스크를 다른 지역에 복사-Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>필수 구성 요소

- [AzCopy v10의 최신 버전](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)을 다운로드 합니다.
- [Azure CLI를 설치합니다](/cli/azure/install-azure-cli).
- 온-프레미스에서 VHD를 업로드 하려는 경우: [Azure에 대해 준비](../windows/prepare-for-upload-vhd-image.md)된 고정 크기 VHD는 로컬에 저장 됩니다.
- 또는 복사 작업을 수행 하려는 경우 Azure에서 관리 되는 디스크입니다.

## <a name="getting-started"></a>시작

GUI를 통해 디스크를 업로드 하는 것을 선호 하는 경우 Azure Storage 탐색기를 사용 하 여이 작업을 수행할 수 있습니다. 자세한 내용은 [Azure Storage 탐색기를 사용 하 여 Azure managed disks 관리](../disks-use-storage-explorer-managed-disks.md) 를 참조 하세요.

Azure에 VHD를 업로드 하려면이 업로드 프로세스에 대해 구성 된 빈 관리 디스크를 만들어야 합니다. 계정을 만들기 전에 이러한 디스크에 대해 알아야 하는 추가 정보가 있습니다.

이러한 종류의 관리 디스크에는 두 가지 고유한 상태가 있습니다.

- ReadToUpload는 디스크가 업로드를 받을 준비가 되었지만 SAS ( [보안 액세스 서명](../../storage/common/storage-sas-overview.md) )가 생성 되지 않았음을 의미 합니다.
- ActiveUpload-디스크가 업로드를 받을 준비가 되었으며 SAS가 생성 되었음을 의미 합니다.

> [!NOTE]
> 이러한 상태 중 하나에서 관리 디스크는 실제 디스크 유형에 상관 없이 [표준 HDD 가격](https://azure.microsoft.com/pricing/details/managed-disks/)으로 청구 됩니다. 예를 들어 P10는 S10로 청구 됩니다. 이는 `revoke-access` 디스크를 VM에 연결 하는 데 필요한 관리 디스크에서가 호출 될 때까지 적용 됩니다.

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

업로드할 빈 표준 HDD를 만들려면 먼저 업로드할 VHD의 파일 크기 (바이트)가 필요 합니다. 이를 얻기 위해 또는를 사용할 수 `wc -c <yourFileName>.vhd` 있습니다 `ls -al <yourFileName>.vhd` . 이 값은 **--upload size 바이트** 매개 변수를 지정할 때 사용 됩니다.

[디스크 만들기](/cli/azure/disk#az-disk-create) cmdlet에서-- **upload** 매개 변수와 **--upload-bytes** 매개 변수를 모두 지정 하 여 업로드할 빈 표준 HDD를 만듭니다.

`<yourdiskname>`을 `<yourresourcegroupname>` `<yourregion>` 선택한 값으로 바꿉니다. `--upload-size-bytes`매개 변수에는의 예제 값이 포함 되어 있으므로 해당 값을 `34359738880` 적절 한 값으로 바꿉니다.

> [!TIP]
> OS 디스크를 만드는 경우--hyper-v 세대 <yourGeneration> 를에 추가 `az disk create` 합니다.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

프리미엄 SSD 또는 표준 SSD를 업로드 하려면 **standard_lrs** **premium_LRS** 또는 **standardssd_lrs**으로 바꿉니다. 지금은 울트라 디스크가 지원 되지 않습니다.

업로드 프로세스를 위해 구성 된 빈 관리 디스크를 만들었으므로 이제 VHD를 업로드할 수 있습니다. 디스크에 VHD를 업로드 하려면 업로드할 대상으로 참조할 수 있도록 쓰기 가능한 SAS가 필요 합니다.

빈 관리 디스크의 쓰기 가능한 SAS를 생성 하려면 `<yourdiskname>` 및를 바꾼 `<yourresourcegroupname>` 후 다음 명령을 사용 합니다.

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

샘플 반환 값:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD 업로드

이제 빈 관리 디스크에 대 한 SAS가 있으므로이를 사용 하 여 업로드 명령의 대상으로 관리 디스크를 설정할 수 있습니다.

AzCopy v10를 사용 하 여 생성 한 SAS URI를 지정 하 여 로컬 VHD 파일을 관리 되는 디스크에 업로드 합니다.

이 업로드는 동일한 [표준 HDD](disks-types.md#standard-hdd)와 동일한 처리량을 갖습니다. 예를 들어 S4와 동일한 크기의 경우 최대 60 s p s/s의 처리량이 있습니다. 그러나 S70에 해당 하는 크기의 경우 최대 500 m b/s의 처리량이 있습니다.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

업로드가 완료 되 고 더 이상 디스크에 더 이상 데이터를 쓸 필요가 없으면 SAS를 해지 합니다. SAS를 해지 하면 관리 디스크의 상태가 변경 되 고 해당 디스크를 VM에 연결할 수 있습니다.

`<yourdiskname>`및 `<yourresourcegroupname>` 를 바꾼 후 다음 명령을 사용 하 여 디스크를 사용할 수 있도록 합니다.

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

또한 직접 업로드는 관리 디스크를 복사 하는 프로세스를 간소화 합니다. 동일한 지역 내에 복사 하거나 다른 지역에 복사할 수 있습니다.

다음 스크립트는 사용자를 위해이 작업을 수행 합니다 .이 프로세스는 앞에서 설명한 단계와 비슷하며 기존 디스크로 작업 하기 때문에 몇 가지 차이점이 있습니다.

> [!IMPORTANT]
> Azure에서 관리 디스크의 디스크 크기 (바이트)를 제공 하는 경우 512의 오프셋을 추가 해야 합니다. 이는 Azure에서 디스크 크기를 반환할 때 바닥글이 생략 되기 때문입니다. 이렇게 하지 않으면 복사가 실패 합니다. 다음 스크립트는 이미이를 위해이를 수행 합니다.

,, `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` , `<targetResourceGroupHere>` 및 `<yourTargetLocationHere>` (위치 값의 예: uswest2)를 값으로 바꾼 후 관리 디스크를 복사 하기 위해 다음 스크립트를 실행 합니다.

> [!TIP]
> OS 디스크를 만드는 경우--hyper-v 세대 <yourGeneration> 를에 추가 `az disk create` 합니다.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>다음 단계

이제 관리 디스크에 VHD를 성공적으로 업로드 했으므로 디스크를 [기존 vm에 데이터 디스크로](add-disk.md) 연결 하거나 [vm에 디스크를 OS 디스크로 연결](upload-vhd.md#create-the-vm)하 여 새 vm을 만들 수 있습니다. 
