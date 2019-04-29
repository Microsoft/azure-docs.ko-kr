---
title: 렌더링을 위한 스토리지 및 데이터 이동 - Azure Batch
description: 워크로드를 렌더링하기 위한 저장소 및 데이터 이동 옵션
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 5a0d4dc82995e63697cc673bc54695c9c6d586df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773998"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>자산 및 출력 파일을 렌더링하기 위한 저장소 및 데이터 이동 옵션

풀 VM에서 렌더링하는 애플리케이션에 장면 및 자산 파일을 사용할 수 있도록 하는 옵션이 있습니다.

* [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * 장면 및 자산 파일은 로컬 파일 시스템에서 Blob Storage로 업로드됩니다. 애플리케이션이 작업에 의해 실행되면 필수 파일이 Blob Storage에서 VM으로 복사되므로 렌더링하는 애플리케이션에서 액세스할 수 있습니다. 출력 파일은 렌더링하는 애플리케이션에 의해 VM 디스크에 기록된 다음, Blob Storage로 복사됩니다.  필요한 경우 Blob Storage에서 로컬 파일 시스템으로 출력 파일을 다운로드할 수 있습니다.
  * Azure Blob Storage는 소규모 프로젝트에 대한 간단하고 비용 효율적인 옵션입니다.  각 풀 VM에 모든 자산에 파일이 필요하므로 자산 파일의 크기와 수가 증가되면 파일 전송이 최대한 효율적으로 수행되도록 주의해야 합니다.  
* [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)를 사용하는 파일 시스템인 Azure Storage입니다.
  * Linux VM의 경우 blobfuse 가상 파일 시스템 드라이버를 사용할 때 저장소 계정을 노출하고 파일 시스템으로 사용할 수 있습니다.
  * 이 옵션은 아주 비용 효과적인 장점이 있습니다. 파일 시스템에 대한 VM이 필요하지 않고, VM의 blobfuse 캐싱을 통해 여러 작업 및 태스크에 대해 동일한 파일을 반복적으로 다운로드하지 않도록 방지합니다.  파일이 Blob이면 데이터 이동도 간단하며 표준 API 및 도구(예: azcopy)를 사용하여 온-프레미스 파일 시스템과 Azure Storage 간에 파일을 복사할 수 있습니다.
* 파일 시스템 또는 파일 공유:
  * VM 운영 체제 및 성능/크기 조정 요구 사항에 따라 옵션에는 [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)가 포함되어 NFS에서 연결된 디스크와 VM을 사용하거나, GlusterFS와 같은 분산 파일 시스템에서 여러 VM을 사용하거나, 타사 제품을 사용합니다.
  * [Avere 시스템](https://www.averesystems.com/)은 이제 Microsoft의 일부이며, 가까운 장래에 대규모의 고성능 렌더링에 적합한 솔루션을 포함합니다.  Avere 솔루션을 사용하면 Azure 기반 NFS 또는 SMB 캐시를 Blob Storage 또는 온-프레미스 NAS 디바이스와 함께 작동하도록 만들 수 있습니다.
  * 파일 시스템에서 파일을 읽고 파일 시스템에 직접 복사하거나, 파일 시스템과 풀 VM 간에 복사할 수 있습니다.
  * 공유 파일 시스템을 사용하면 필수 항목에 액세스하는 렌더링 작업에서 함께 프로젝트와 작업 간에 공유된 다수의 자산을 활용할 수 있습니다.

## <a name="using-azure-blob-storage"></a>Azure Blob Storage 사용

Blob Storage 계정 또는 범용 v2 스토리지 계정을 사용해야 합니다.  이러한 두 저장소 계정 형식은 [이 블로그 게시물](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)에 설명된 대로 범용 v1 저장소 계정에 비해 훨씬 높은 한도로 구성할 수 있습니다.  항목이 구성되면 특히 다수의 풀 VM이 저장소 계정에 액세스하는 경우 높은 제한을 통해 훨씬 더 나은 성능 및 확장성을 활용할 수 있습니다.

### <a name="copying-files-between-client-and-blob-storage"></a>클라이언트와 Blob Storage 간에 파일 복사

Azure Storage 간에 파일을 복사하려면 Storage Blob API, [Azure Storage Data Movement Library](https://github.com/Azure/azure-storage-net-data-movement), [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 또는 [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)용 azcopy 명령줄 도구, [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) 및 [Azure Batch Explorer](https://azure.github.io/BatchExplorer/)를 비롯한 다양한 매커니즘을 사용하면 됩니다.

예를 들어 azcopy를 사용하여 다음과 같이 폴더의 모든 자산을 전송할 수 있습니다.


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

수정된 파일만을 복사하려면 /XO 매개 변수를 사용할 수 있습니다.

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>입력 자산 파일을 Blob Storage에서 Batch 풀 VM에 복사

작업 자산의 크기에 따라 가장 좋은 방법을 결정하여 파일을 복사하는 몇 가지 다른 방법이 있습니다.
가장 간단한 방법은 각 작업의 풀 VM에 모든 자산 파일을 복사하는 것입니다.

* 작업에 고유한 파일이 있지만 작업의 모든 태스크에 대해 필수적인 경우 모든 파일을 복사하도록 [작업 준비 태스크](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask)를 지정할 수 있습니다.  작업 준비 태스크는 첫 번째 태스크가 VM에서 실행되지만 후속 작업 태스크에서 다시 실행되지 않는 경우 한 번 실행됩니다.
* 작업이 완료되면 작업당 파일을 제거하도록 [작업 해제 태스크](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask)를 지정해야 합니다. 그러면 VM 디스크를 모든 작업 자산 파일에서 채우지 않도록 방지합니다.
* 동일한 자산을 사용하는 여러 작업이 있을 때 하위 집합만 업데이트된 경우에도 각 작업에 대해 자산을 증분 변경하여 모든 자산 파일을 복사합니다.  그러면 대규모 자산 파일이 많은 경우 비효율적입니다.

작업 간에 자산 파일을 다시 사용할 때 작업 간에 증분 변경하여 VM의 공유 폴더에 자산을 저장하고 변경된 파일을 동기화하는 작업이 약간 더 복잡하지만 더 효율적인 방법입니다.

* 작업 준비 태스크는 AZ_BATCH_NODE_SHARED_DIR 환경 변수로 지정된 VM 공유 폴더에 대해 /XO 매개 변수를 포함한 azcopy를 사용하여 복사를 수행합니다.  그러면 각 VM에 변경된 파일만 복사합니다.
* 풀 VM의 임시 드라이브에 맞을 수 있도록 모든 자산의 크기를 지정해야 합니다.

Azure Batch는 저장소 계정과 Batch 풀 VM 간에 파일을 복사하는 지원을 기본 제공합니다.  태스크 [리소스 파일](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile)은 저장소에서 풀 VM으로 파일을 복사하고 작업 준비 태스크에 대해 지정될 수 있습니다.  그러나 파일이 수백 개인 경우 제한에 도달하여 작업이 실패할 수 있습니다.  자산 수가 많은 경우 작업 준비 태스크에서 azcopy 명령줄을 사용하는 것이 좋습니다. 여기서는 제한 없이 와일드 카드를 사용할 수 있습니다.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Batch 풀 VM에서 Blob Storage로 출력 파일 복사

[출력 파일](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile)을 사용하여 풀 VM에서 저장소에 파일을 복사할 수 있습니다.  작업이 완료되면 하나 이상의 파일을 VM에서 지정된 저장소 계정으로 복사할 수 있습니다.  렌더링된 출력을 복사해야 하지만 로그 파일을 저장하는 것도 좋습니다.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Linux VM 풀에 blobfuse 가상 파일 시스템 사용

blobfuse는 Azure Blob Storage에 대한 가상 파일 시스템 드라이버이며, 이를 통해 Linux 파일 시스템을 통해 Storage 계정에서 Blob으로 저장된 파일에 액세스할 수 있습니다.

풀 노드는 시작할 때 파일 시스템을 탑재할 수 있습니다. 또는 탑재는 작업 준비 태스크(작업의 첫 번째 작업이 노드에서 실행되는 경우 실행되는 태스크)의 일부로 발생할 수 있습니다.  blobfuse는 파일을 캐시하기 위해 ramdisk 및 VM 로컬 SSD 모두를 활용하도록 구성될 수 있습니다. 그러면 노드의 여러 작업이 일부 동일한 파일에 액세스하는 경우 성능이 크게 향상됩니다.

[샘플 템플릿은](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) blobfuse 파일 시스템을 사용하여 독립 실행형 V-Ray 렌더링을 실행하는 데 사용되고 다른 애플리케이션에 대한 템플릿의 기반으로 사용될 수 있습니다.

### <a name="accessing-files"></a>파일에 액세스

작업 태스크는 탑재된 파일 시스템을 사용하는 입력 파일 및 출력 파일에 대한 경로를 지정합니다.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>입력 자산 파일을 Blob Storage에서 Batch 풀 VM에 복사

파일이 단순히 Azure Storage의 Blob이므로 표준 Blob API, 도구 및 UI를 사용하여 온-프레미스 파일 시스템과 Blob Storage 간에 파일을 복사할 수 있습니다(예: azcopy, Storage 탐색기, Batch Explorer 등).

## <a name="using-azure-files-with-windows-vms"></a>Windows VM에서 Azure Files 사용

[Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)는 클라우드에서 SMB 프로토콜을 통해 액세스할 수 있는 완전히 관리되는 파일 공유를 제공합니다.  Azure Files는 Azure Blob Storage에 기반한 [비용 효율적](https://azure.microsoft.com/pricing/details/storage/files/) 기능이며 다른 지역에 대한 데이터 복제를 사용하여 구성할 수 있으므로 전역적으로 중복됩니다.  지정된 예측 풀 크기 및 자산 파일 수에서 Azure Files를 사용할지를 결정하기 위해 [대상 크기 조정](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets)을 검토해야 합니다.

Azure 파일 공유를 탑재하는 방법을 설명하는 [블로그 게시물](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) 및 [설명서](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)가 있습니다.

### <a name="mounting-an-azure-files-share"></a>Azure Files 공유 만들기

Batch에서 사용하려면 태스크 간에 연결을 유지할 수 없으므로 태스크가 실행될 때마다 탑재 작업을 수행해야 합니다.  이 작업을 수행하는 가장 쉬운 방법은 풀 구성에서 시작 태스크를 사용하여 자격 증명을 유지하는 데 cmdkey를 사용한 다음, 각 태스크 전에 공유를 탑재하는 것입니다.

JSON 파일에서 사용하기 위해 이스케이프된 풀 템플릿의 cmdkey 사용 예제 - net use 호출에서 cmdkey 호출을 구분하는 경우 시작 태스크에 대한 사용자 컨텍스트는 태스크를 실행하는 데 사용된 것과 동일해야 합니다.

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

작업 태스크 명령줄 예제:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>파일에 액세스

작업 태스크는 매핑된 드라이브 또는 UNC 경로를 사용하여 탑재된 파일 시스템을 사용하는 입력 파일 및 출력 파일에 대한 경로를 지정합니다.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>입력 자산 파일을 Blob Storage에서 Batch 풀 VM에 복사

Azure Files는 Azure Storage를 지원하는 기본 API 및 도구에서 지원됩니다(예: azcopy, Azure CLI, Storage 탐색기, Azure PowerShell, Batch Explorer 등).

[Azure 파일 동기화](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)는 온-프레미스 파일 시스템과 Azure 파일 공유 간에 파일을 자동으로 동기화하기 위해 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

저장소 옵션에 대한 자세한 내용은 심층 설명서를 참조하세요.

* [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Azure 파일](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
