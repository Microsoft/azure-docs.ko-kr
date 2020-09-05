---
title: 풀에서 가상 파일 시스템 탑재
description: Batch 풀에서 가상 파일 시스템을 탑재하는 방법을 알아봅니다.
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 08/13/2019
ms.openlocfilehash: df03275fdeea88df1a2f2b6e2cda55021497cdf7
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145487"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Batch 풀에서 가상 파일 시스템 탑재

Azure Batch에서는 이제 Windows 또는 Linux 컴퓨팅 노드의 Batch 풀에서 클라우드 스토리지 또는 외부 파일 시스템 탑재가 지원됩니다. 컴퓨팅 노드가 풀에 참가하면 가상 파일 시스템이 탑재되고 해당 노드에서 로컬 드라이브처럼 취급됩니다. Azure Files, Azure Blob 스토리지, [Avere vFXT 캐시](../avere-vfxt/avere-vfxt-overview.md)를 포함한 NFS(네트워크 파일 시스템) 또는 CIFS(Common Internet File System)와 같은 파일 시스템을 탑재합니다.

이 문서에서는 [.NET용 Batch 관리 라이브러리](/dotnet/api/overview/azure/batch)를 사용하여 컴퓨팅 노드 풀에서 가상 파일 시스템을 탑재하는 방법을 알아봅니다.

> [!NOTE]
> 가상 파일 시스템 탑재는 2019-08-19부터 생성되는 Batch 풀에서 지원됩니다. 2019-08-19 이전에 만든 Batch 풀에서는 이 기능이 지원되지 않습니다.
> 
> 컴퓨팅 노드에 파일 시스템을 탑재하기 위한 API는 [Batch .NET](/dotnet/api/microsoft.azure.batch) 라이브러리의 일부입니다.

## <a name="benefits-of-mounting-on-a-pool"></a>풀에 탑재 시 이점

태스크가 대량 데이터 세트에서 자신의 고유 데이터를 검색하도록 허용하지 않고 풀에 파일 시스템을 탑재하면 태스크가 더 쉽고 더 효율적인 방식으로 필요한 데이터에 액세스할 수 있습니다.

동영상 렌더링과 같이 여러 태스크에 공통 데이터 세트에 대한 액세스가 필요하다고 가정해보세요. 각 태스크는 장면 파일로부터 한 번에 하나 이상의 프레임을 렌더링합니다. 장면 파일이 포함된 드라이브를 탑재하면 컴퓨팅 노드가 더 쉽게 공유 데이터에 액세스할 수 있습니다. 또한 데이터에 동시에 액세스하는 컴퓨팅 노드 수에 따라 필요한 성능 및 규모(처리량 및 IOPS)를 기준으로 기본 파일 시스템을 독립적으로 선택하고 확장할 수 있습니다. 예를 들어 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)로 분산된 메모리 내 캐시를 사용하면 온-프레미스에 있는 소스 데이터에 액세스하여 수천 개의 동시 렌더링 노드가 있는 대규모 영화급 렌더링을 지원할 수 있습니다. 또는 이미 클라우드 기반 Blob 스토리지에 있는 데이터의 경우, [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)를 사용하여 이 데이터를 로컬 파일 시스템으로 탑재할 수 있습니다. Blobfuse는 Linux 노드에서만 사용할 수 있습니다. 하지만 [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/)는 비슷한 워크플로를 제공하며, Windows 및 Linux 모두에서 사용할 수 있습니다.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>풀에서 가상 파일 시스템 탑재  

풀에 가상 파일 시스템을 탑재하면 해당 풀의 모든 컴퓨팅 노드에서 파일 시스템을 사용할 수 있습니다. 파일 시스템은 컴퓨팅 노드가 풀에 참가할 때 또는 노드가 다시 시작되거나 이미지로 다시 설치될 때 구성됩니다.

풀에 파일 시스템을 탑재하려면 `MountConfiguration` 개체를 만듭니다. `AzureBlobFileSystemConfiguration`, `AzureFileShareConfiguration`, `NfsMountConfiguration` 또는 `CifsMountConfiguration`과 같이 가상 파일 시스템에 적합한 개체를 선택합니다.

모든 탑재 구성 개체에는 다음과 같은 기본 매개 변수가 필요합니다. 일부 탑재 구성에는 사용 중인 파일 시스템별 매개 변수가 포함되며 이에 대해서는 코드 예제에서 더 자세히 다뤄집니다.

- **계정 이름 또는 원본**: 가상 파일 공유를 탑재하려면 스토리지 계정 또는 해당 원본의 이름이 필요합니다.
- **상대적 탑재 경로 또는 원본**: `AZ_BATCH_NODE_MOUNTS_DIR`을 통해 노드에서 액세스할 수 있는 표준 `fsmounts` 디렉터리를 기준으로 하는 컴퓨팅 노드에 탑재된 파일 시스템의 위치입니다. 정확한 위치는 노드에 사용된 운영 체제에 따라 달라집니다. 예를 들어 Ubuntu 노드에서 실제 위치는 `mnt\batch\tasks\fsmounts`로 매핑되고 CentOS 노드에서는 `mnt\resources\batch\tasks\fsmounts`로 매핑됩니다.
- **탑재 옵션 또는 blobfuse 옵션**: 이러한 옵션은 파일 시스템 탑재를 위한 특정 매개 변수에 대해 설명합니다.

`MountConfiguration` 개체가 생성된 다음, 풀을 만들 때 개체를 `MountConfigurationList` 속성에 할당합니다. 노드가 풀에 참가하거나 노드가 다시 시작 또는 이미지로 다시 설치될 때 파일 시스템이 탑재됩니다.

파일 시스템이 탑재될 때는 로그 파일은 물론 탑재된 파일 시스템의 위치를 나타내어 문제 해결과 디버깅에 도움이 되는 환경 변수 `AZ_BATCH_NODE_MOUNTS_DIR`이 생성됩니다. 로그 파일에 대해서는 [탑재 오류 진단](#diagnose-mount-errors) 섹션에서 더 자세히 설명합니다.  

> [!IMPORTANT]
> 풀에 탑재되는 파일 시스템의 최대 수는 10입니다. 자세한 내용 및 기타 제한 사항은 [Batch 서비스 할당량 및 제한](batch-quota-limit.md#other-limits)을 참조하세요.

## <a name="examples"></a>예

다음 코드 예시는 컴퓨팅 노드 풀에 다양한 파일 공유를 탑재하는 방법을 보여줍니다.

### <a name="azure-files-share"></a>Azure Files 공유

Azure Files는 표준 Azure 클라우드 파일 시스템 제품입니다. 탑재 구성 코드 샘플에서 매개 변수를 가져오는 방법을 자세히 알아보려면 [Azure Files 공유 사용](../storage/files/storage-how-to-use-files-windows.md)을 참조하세요.

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
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob 파일 시스템

또 다른 옵션은 [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)를 통해 Azure Blob 스토리지를 사용하는 것입니다. Blob 파일 시스템 탑재를 위해서는 해당 스토리지 계정에 대한 `AccountKey` 또는 `SasKey`가 필요합니다. 이러한 키를 가져오는 방법은 [스토리지 계정 액세스 키 관리](../storage/common/storage-account-keys-manage.md) 또는 [SAS(공유 액세스 서명) 사용](../storage/common/storage-sas-overview.md)을 참조하세요. blobfuse 사용에 대한 자세한 내용은 blobfuse [문제 해결 FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)를 참조하세요. blobfuse에 탑재된 디렉터리에 대한 기본 액세스 권한을 얻으려면 작업을 **관리자**로 실행합니다. Blobfuse는 사용자 공간에 디렉터리를 탑재하고, 풀 생성 시 루트로 탑재됩니다. Linux에서 모든 **관리자** 태스크는 루트입니다. FUSE 모듈의 모든 옵션은 [FUSE 참조 페이지](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)에 설명되어 있습니다.

문제 해결 가이드 외에도 blobfuse 저장소의 GitHub 문제 설명은 현재의 blobfuse 문제 및 해결 방법을 확인하는 데 유용합니다. 자세한 내용은 [blobfuse 문제](https://github.com/Azure/azure-storage-fuse/issues)를 참조하세요.

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

NFS(네트워크 파일 시스템)는 풀 노드에도 탑재될 수 있으며, Azure Batch 노드가 기존 파일 시스템에 쉽게 액세스할 수 있게 해줍니다. 이것은 클라우드에 배포되는 단일 NFS 서버이거나 가상 네트워크를 통해 액세스되는 온-프레미스 NFS 서버일 수 있습니다. 또는 온-프레미스 스토리지에 대한 효율적인 연결 기능을 제공하는 [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md)로 분산된 메모리 내 캐시 솔루션을 활용해서 요청에 따라 데이터를 해당 캐시로 읽어내고, 클라우드 기반 컴퓨팅 노드에 고성능 및 확장 기능을 제공할 수 있습니다.

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

### <a name="common-internet-file-system"></a>Common Internet File System

CIFS(Common Internet File System)도 풀 노드에 탑재할 수 있으며, Azure Batch 노드에서 기존 파일 시스템을 쉽게 액세스하도록 허용합니다. CIFS는 네트워크 서버 파일 및 서비스를 요청하기 위해 개방형의 플랫폼 범용 메커니즘을 제공하는 파일 공유 프로토콜입니다. CIFS는 인터넷 및 인트라넷 파일 공유를 위한 Microsoft의 SMB(서버 메시지 블록) 프로토콜의 고급 버전을 기반으로 하며, Windows 노드에 외부 파일 시스템을 탑재하기 위해 사용됩니다. SMB에 대해 자세히 알아보려면 [파일 서버 및 SMB](/windows-server/storage/file-server/file-server-smb-overview)를 참조하세요.

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

탑재 구성이 실패하면 풀의 컴퓨팅 노드가 실패하고 노드 상태가 사용 불가로 전환됩니다. 탑재 구성 오류를 진단하려면 [`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) 속성에서 자세한 오류 정보를 조사합니다.

디버그를 위해 로그 파일을 가져오려면 [OutputFiles](batch-task-output-files.md)를 사용하여 `*.log` 파일을 업로드합니다. `*.log` 파일에는 `AZ_BATCH_NODE_MOUNTS_DIR` 위치에 있는 파일 시스템 탑재에 대한 정보가 포함됩니다. 탑재 로그 파일에는 각 탑재에 대해 `<type>-<mountDirOrDrive>.log` 형식이 사용됩니다. 예를 들어 `test`라는 탑재 디렉터리에서 `cifs` 탑재에는 `cifs-test.log`라는 탑재 로그 파일이 사용됩니다.

## <a name="supported-skus"></a>지원되는 SKU

| 게시자 | 제안 | SKU | Azure Files 공유 | Blobfuse | NFS 탑재 | CIFS 탑재 |
|---|---|---|---|---|---|---|
| 일괄 처리 | rendering-centos73 | 렌더링 | :heavy_check_mark: <br>참고: CentOS 7.7과 호환 가능</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-ads | linux-data-science-vm | linuxdsvm | :heavy_check_mark: <br>참고: CentOS 7.4와 호환 가능 </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | centos-container-rdma | 7.4 | :heavy_check_mark: <br>참고: A_8 또는 9 스토리지 지원</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-batch | ubuntu-server-container | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>다음 단계

- [Windows](../storage/files/storage-how-to-use-files-windows.md) 또는 [Linux](../storage/files/storage-how-to-use-files-linux.md)에서 Azure Files 공유 탑재에 대해 자세히 알아보세요.
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 가상 파일 시스템 사용 및 탑재에 대해 자세히 알아보세요.
- [네트워크 파일 시스템 개요](/windows-server/storage/nfs/nfs-overview)를 보고 NFS 및 해당 애플리케이션에 대해 자세히 알아보세요.
- [Microsoft SMB 프로토콜 및 CIFS 프로토콜 개요](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)를 보고 CIFS에 대해 자세히 알아보세요.
