---
title: 풀에 가상 파일 시스템 탑재-Azure Batch | Microsoft Docs
description: Batch 풀에 가상 파일 시스템을 탑재 하는 방법에 대해 알아봅니다.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: a22117505dff35f9b92e3dd3c91dc8540557b218
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023041"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Batch 풀에 가상 파일 시스템 탑재

이제 Azure Batch은 클라우드 저장소 또는 Batch 풀의 Linux 계산 노드 또는 Windows의 외부 파일 시스템을 탑재할 수 있도록 지원 합니다. 계산 노드가 풀에 연결 되 면 가상 파일 시스템을 탑재 하 고 해당 노드의 로컬 드라이브로 처리 합니다. [Avere vFXT cache](../avere-vfxt/avere-vfxt-overview.md)또는 CIFS (Common Internet file system)를 포함 하 여 Azure Files, Azure Blob STORAGE, NFS (네트워크 파일 시스템) 등의 파일 시스템을 탑재할 수 있습니다.

이 문서에서는 [.net 용 Batch 관리 라이브러리](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)를 사용 하 여 계산 노드 풀에 가상 파일 시스템을 탑재 하는 방법에 대해 알아봅니다.

> [!NOTE]
> 가상 파일 시스템 탑재는 2019-08-19 이후에 생성 된 배치 풀에서 지원 됩니다. 2019-08-19 이전에 만든 Batch 풀은이 기능을 지원 하지 않습니다.
> 
> 계산 노드의 파일 시스템을 탑재 하는 Api는 [Batch .net](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) 라이브러리의 일부입니다.

## <a name="benefits-of-mounting-on-a-pool"></a>풀에 탑재의 이점

태스크가 대량 데이터 집합에서 자신의 데이터를 검색 하도록 하는 대신 파일 시스템을 풀에 탑재 하면 작업에서 필요한 데이터에 보다 쉽고 효율적으로 액세스할 수 있습니다.

동영상 렌더링과 같은 공통 데이터 집합에 액세스 해야 하는 여러 작업이 있는 시나리오를 고려해 보세요. 각 작업은 장면 파일에서 한 번에 하나 이상의 프레임을 렌더링 합니다. 장면 파일이 포함 된 드라이브를 탑재 하 여 계산 노드가 공유 데이터에 액세스 하는 것이 더 쉽습니다. 또한 데이터에 동시에 액세스 하는 계산 노드 수에 필요한 성능 및 규모 (처리량 및 IOPS)에 따라 기본 파일 시스템을 독립적으로 선택 하 고 확장할 수 있습니다. 예를 들어 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distributed 메모리 내 캐시를 사용 하 여 수천 개의 동시 렌더링 노드가 있는 대규모의 그림 크기 조정 렌더링을 지원 하 고 온-프레미스에 있는 원본 데이터에 액세스할 수 있습니다. 또는 이미 클라우드 기반 Blob storage에 있는 데이터의 경우 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) 를 사용 하 여이 데이터를 로컬 파일 시스템으로 탑재할 수 있습니다. Blobfuse는 Linux 노드에서만 사용할 수 있지만 [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) 유사한 워크플로를 제공 하 고 Windows 및 Linux에서 모두 사용할 수 있습니다.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>풀에 가상 파일 시스템 탑재  

풀에 가상 파일 시스템을 탑재 하면 풀의 모든 계산 노드에서 파일 시스템을 사용할 수 있습니다. 파일 시스템은 계산 노드가 풀에 조인 되거나 노드가 다시 시작 되거나 이미지로 다시 설치 때 구성 됩니다.

풀에 파일 시스템을 탑재 하려면 `MountConfiguration` 개체를 만듭니다. `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration`또는 `CifsMountConfiguration`가상 파일 시스템에 맞는 개체를 선택 합니다.

모든 탑재 구성 개체에는 다음 기본 매개 변수가 필요 합니다. 일부 탑재 구성에는 사용 되는 파일 시스템에 해당 하는 매개 변수가 있습니다 .이에 대해서는 코드 예제에 자세히 설명 되어 있습니다.

- **계정 이름 또는 원본**: 가상 파일 공유를 탑재 하려면 저장소 계정 또는 해당 원본의 이름이 필요 합니다.
- **상대 탑재 경로 또는 원본**: `AZ_BATCH_NODE_MOUNTS_DIR`를 통해 노드에서 액세스할 수 있는 표준 `fsmounts` 디렉터리를 기준으로 계산 노드에 탑재 된 파일 시스템의 위치입니다. 정확한 위치는 노드에서 사용 되는 운영 체제에 따라 달라 집니다. 예를 들어 Ubuntu 노드의 물리적 위치는 `mnt\batch\tasks\fsmounts`에 매핑되고 CentOS 노드에는 `mnt\resources\batch\tasks\fsmounts`매핑됩니다.
- **탑재 옵션 또는 blobfuse 옵션**: 이러한 옵션은 파일 시스템을 탑재 하기 위한 특정 매개 변수를 설명 합니다.

`MountConfiguration` 개체가 만들어지면 풀을 만들 때 개체를 `MountConfigurationList` 속성에 할당 합니다. 노드가 풀에 조인 되거나 노드가 다시 시작 되거나 이미지로 다시 설치 될 때 파일 시스템이 탑재 됩니다.

파일 시스템이 탑재 되 면 탑재 된 파일 시스템의 위치 및 로그 파일을 가리키는 환경 변수 `AZ_BATCH_NODE_MOUNTS_DIR` 만들어집니다 .이 변수는 문제 해결 및 디버깅에 유용 합니다. 로그 파일은 [탑재 오류 진단](#diagnose-mount-errors) 섹션에 자세히 설명 되어 있습니다.  

> [!IMPORTANT]
> 풀에 탑재 된 파일 시스템의 최대 수는 10 개입니다. 자세한 내용은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md#other-limits) 을 참조 하세요.

## <a name="examples"></a>예시

다음 코드 예제에서는 계산 노드 풀에 다양 한 파일 공유를 탑재 하는 방법을 보여 줍니다.

### <a name="azure-files-share"></a>Azure Files 공유

Azure Files은 표준 Azure 클라우드 파일 시스템 제품입니다. 탑재 구성 코드 샘플에서 매개 변수를 가져오는 방법에 대해 자세히 알아보려면 [Azure Files 공유 사용](../storage/files/storage-how-to-use-files-windows.md)을 참조 하세요.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 파일 시스템

또 다른 옵션은 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)를 통해 Azure Blob storage를 사용 하는 것입니다. Blob 파일 시스템을 탑재 하려면 저장소 계정에 대한 `AccountKey` 또는 `SasKey` 필요 합니다. 이러한 키를 가져오는 방법에 대한 자세한 내용은 [저장소 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md)또는 [SAS (공유 액세스 서명) 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조 하세요. Blobfuse를 사용 하는 방법에 대한 자세한 내용은 blobfuse [문제 해결 FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)를 참조 하세요. Blobfuse 탑재 된 디렉터리에 대한 기본 액세스 권한을 얻으려면 **관리자 권한**으로 작업을 실행 합니다. Blobfuse는 사용자 공간에서 디렉터리를 탑재 하 고 풀을 만들 때 루트로 탑재 됩니다. Linux에서는 모든 **관리자** 작업이 루트입니다. 퓨즈 모듈의 모든 옵션은 [퓨즈 참조 페이지](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)에 설명 되어 있습니다.

문제 해결 가이드 외에도 blobfuse 리포지토리의 GitHub 문제는 현재 blobfuse 문제 및 해결 방법을 확인 하는 데 유용한 방법입니다. 자세한 내용은 [blobfuse 문제](https://github.com/Azure/azure-storage-fuse/issues)를 참조 하세요.

> [!NOTE]
> Blobfuse는 현재 Debian에서 지원 되지 않습니다. 자세한 내용은 [지원 되는 sku](#supported-skus) 를 참조 하세요.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>네트워크 파일 시스템

NFS (네트워크 파일 시스템)도 풀 노드에 탑재할 수 있으므로 기존 파일 시스템을 Azure Batch 노드에서 쉽게 액세스할 수 있습니다. 이는 클라우드에 배포 된 단일 NFS 서버 또는 가상 네트워크를 통해 액세스 하는 온-프레미스 NFS 서버 일 수 있습니다. 또는 온-프레미스 저장소에 원활한 연결을 제공 하 고, 주문형 데이터를 캐시로 읽고, 클라우드 기반 계산 노드에 고성능 및 규모를 제공 하는 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) distributed 메모리 내 캐시 솔루션을 활용할 수 있습니다.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>일반 인터넷 파일 시스템

CIFS (Common Internet File system)를 풀 노드에 탑재할 수도 있으므로 기존 파일 시스템을 Azure Batch 노드에서 쉽게 액세스할 수 있습니다. CIFS는 네트워크 서버 파일 및 서비스를 요청 하는 데 사용할 수 있는 개방형 플랫폼 간 메커니즘을 제공 하는 파일 공유 프로토콜입니다. CIFS는 인터넷 및 인트라넷 파일 공유에 대한 Microsoft SMB (서버 메시지 블록) 프로토콜의 향상 된 버전을 기반으로 하며, Windows 노드에 외부 파일 시스템을 탑재 하는 데 사용 됩니다. SMB에 대해 자세히 알아보려면 [파일 서버 및 smb](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)를 참조 하세요.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>탑재 오류 진단

탑재 구성에 실패 하면 풀의 계산 노드가 실패 하 고 노드 상태를 사용할 수 없게 됩니다. 탑재 구성 오류를 진단 하려면 오류에 대한 자세한 내용은 [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 속성을 검사 합니다.

디버그할 로그 파일을 가져오려면 [Outputfiles](batch-task-output-files.md) 를 사용 하 여 `*.log` 파일을 업로드 합니다. `*.log` 파일에는 `AZ_BATCH_NODE_MOUNTS_DIR` 위치의 파일 시스템 탑재에 대한 정보가 포함 되어 있습니다. 탑재 로그 파일은 각 탑재에 대한 `<type>-<mountDirOrDrive>.log` 형식입니다. 예를 들어 `test` 이라는 탑재 디렉터리에 탑재 된 `cifs` 탑재 로그 파일은 `cifs-test.log`됩니다.

## <a name="supported-skus"></a>지원되는 SKU

| 게시자 | 제품 | SKU | Azure Files 공유 | Blobfuse | NFS 탑재 | CIFS 탑재 |
|---|---|---|---|---|---|---|
| batch | rendering-centos73 | 렌더링 | :heavy_check_mark: <br>참고: CentOS 7.7와 호환 됩니다.</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8, 9 | :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>참고: CentOS 7.4와 호환 됩니다. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>참고: A_8 또는 9 저장소를 지원 합니다.</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>다음 단계

- [Windows](../storage/files/storage-how-to-use-files-windows.md) 또는 [Linux](../storage/files/storage-how-to-use-files-linux.md)를 사용 하 여 Azure Files 공유를 탑재 하는 방법에 대해 자세히 알아보세요.
- [Blobfuse](https://github.com/Azure/azure-storage-fuse) 가상 파일 시스템을 사용 하 고 탑재 하는 방법에 대해 알아봅니다.
- NFS 및 해당 응용 프로그램에 대해 알아보려면 [네트워크 파일 시스템 개요](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) 를 참조 하세요.
- CIFS에 대해 자세히 알아보려면 [MICROSOFT SMB 프로토콜 및 cifs 프로토콜 개요](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) 를 참조 하세요.
