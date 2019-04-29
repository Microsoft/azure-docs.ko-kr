---
title: 만들고 리소스 파일-Azure Batch를 사용 하 여 | Microsoft Docs
description: 다양 한 입력된 원본에서 Azure Batch 리소스 파일을 만드는 방법에 알아봅니다.
services: batch
author: laurenhughes
manager: jeconnoc
ms.service: batch
ms.topic: article
ms.date: 03/14/2019
ms.author: lahugh
ms.openlocfilehash: 679a1c60e44694bde86cafba21d7f1d2c6fb94d9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60616554"
---
# <a name="creating-and-using-resource-files"></a>만들기 및 리소스 파일 사용

Azure Batch 작업을 특정 형태의 데이터 처리를 종종 필요 합니다. 리소스 파일은이 데이터 작업을 통해 일괄 처리 가상 컴퓨터 (VM)를 제공 하는 수단입니다. 모든 유형의 태스크 리소스 파일을 지원 합니다: 작업, 태스크, 작업 준비 태스크, 작업 릴리스 태스크를 시작 합니다. 이 문서에서는 리소스 파일을 만들고 VM에 배치 하는 방법에 몇 가지 일반적인 방법을 설명 합니다.  

리소스 파일은 일괄 처리의 VM에 데이터를 저장 하는 메커니즘 이지만 데이터 유형과 사용 하는 방식을 유연 합니다. 그러나 가지, 몇 가지 일반적인 사용 사례

1. 시작 태스크의 리소스 파일을 사용 하 여 각 VM에 있는 일반 파일을 프로 비전
1. 작업에서 처리할 입력된 데이터를 프로 비전

공용 파일 수, 예를 들어 태스크가 실행 하는 응용 프로그램을 설치 하는 데 사용 되는 시작 작업에서 파일입니다. 입력된 데이터는 원시 이미지 또는 비디오 데이터 또는 정보 일괄 처리에서 처리할 수 있습니다.

## <a name="types-of-resource-files"></a>리소스 파일 형식

리소스 파일을 생성할 수 있는 몇 가지 옵션이 있습니다. 리소스 파일에 대 한 만들기 프로세스는 원래 데이터를 저장 하는 위치에 따라 달라 집니다.

리소스 파일 만들기에 대 한 옵션:

- [저장소 컨테이너 URL](#storage-container-url): Azure에서 모든 저장소 컨테이너에서 리소스 파일을 생성합니다.
- [저장소 컨테이너 이름](#storage-container-name): 일괄 처리에 연결 된 Azure storage 계정에 컨테이너의 이름에서 리소스 파일을 생성 합니다.
- [웹 끝점](#web-endpoint): 모든 올바른 HTTP URL에서 리소스 파일을 생성합니다.

### <a name="storage-container-url"></a>저장소 컨테이너 URL

저장소 컨테이너 URL을 사용 하 여 Azure에서 모든 저장소 컨테이너에 파일에 액세스할 수 있습니다 하는 것을 의미 합니다. 적절 한 권한이 있는

이 C# 예제에서는 파일이 blob 저장소로 Azure storage 컨테이너에 이미 업로드 되었습니다. 리소스 파일을 만드는 데 필요한 데이터에 액세스 하려면 먼저 저장소 컨테이너에 대 한 액세스를 가져올 해야 합니다.

공유 액세스 서명 (SAS) 저장소 컨테이너에 액세스 하려면 적절 한 권한이 있는 URI 만듭니다. 만료 시간과 SAS에 대 한 권한을 설정 합니다. 이 경우 시작 시간이 지정 됩니다, SAS가 즉시 유효 하 고 생성 된 후 2 시간 내에 만료 됩니다.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 컨테이너 액세스에 대 한 있어야 `Read` 하 고 `List` 권한을 blob 액세스를 사용 해야 하는 반면 `Read` 권한.

사용 권한을 구성 되 면 SAS 토큰을 만들고 저장소 컨테이너에 대 한 액세스에 대 한 SAS URL을 포맷 합니다. 사용 하 여 리소스 파일을 생성할 서식이 지정 된 SAS URL을 사용 하 여 저장소 컨테이너에 대 한 [ `FromStorageContainerUrl` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)합니다.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

SAS URL을 생성 하는 대신 컨테이너 및 Azure Blob storage에서 해당 blob에 익명 공용 읽기 액세스를 사용 하도록 설정 하는 것입니다. 이 작업을 수행 하면 SAS를 요구 하지 않고 사용자의 계정 키를 공유 하지 않고 이러한 리소스에 대 한 읽기 전용 액세스를 부여할 수 있습니다. 공용 읽기 액세스는 일반적으로 사용 시나리오 하려는 특정 blob을 항상 익명 읽기 권한에 사용할 수 있습니다. 이 시나리오에 적합 한 솔루션을 표시 합니다 [blob에 대 한 익명 액세스](../storage/blobs/storage-manage-access-to-resources.md) blob 데이터에 대 한 액세스를 관리 하는 방법에 대 한 자세한 문서.

### <a name="storage-container-name"></a>저장소 컨테이너 이름

를 구성 하 고 SAS URL을 작성 하는 대신 blob 데이터에 액세스 하려면 Azure 저장소 컨테이너의 이름을 사용할 수 있습니다. 저장소 컨테이너 autostorage 계정과 알려진 Batch 계정에 연결 된 Azure storage 계정에 해야를 사용 합니다. Autostorage 계정의 저장소 컨테이너 이름을 사용 하 여 구성 하 고 저장소 컨테이너에 액세스 하려면 SAS URL 만들기를 건너뛸 수 있습니다.

이 예제에서는 리소스 파일 만들기에 사용 되는 데이터에에서 이미 있다고 Batch 계정에 연결 된 Azure Storage 계정을 가정 합니다. Autostorage 계정이 없으면의 단계를 참조 [Batch 계정 만들기](batch-account-create-portal.md) 만들고 계정을 연결 하는 방법에 대 한 세부 정보에 대 한 합니다.

연결 된 저장소 계정을 사용 하 여 만들고 저장소 컨테이너 SAS URL을 구성할 필요가 없습니다. 대신, 연결 된 저장소 계정의 저장소 컨테이너의 이름을 제공 합니다.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>웹 엔드포인트

데이터를 Azure Storage에 업로드 되지 않습니다 여전히 리소스 파일을 만드는 데 사용할 수 있습니다. 입력된 데이터가 포함 된 모든 올바른 HTTP URL을 지정할 수 있습니다. Batch API에 제공 된 URL은 하 고 데이터는 리소스 파일을 만드는 데 다음 키를 누릅니다.

다음에서 C# 예제에서는 입력된 데이터를 가상의 GitHub 끝점에서 호스팅됩니다. 올바른 웹 끝점에서 파일을 검색 하 고 작업에서 사용할 수 있도록 리소스 파일을 생성 하는 API입니다. 이 시나리오에 대 한 자격 증명이 필요 합니다.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

각 Azure Batch 태스크에서는 파일 다르게 일괄 처리 작업에는 파일 관리에 사용할 수 있는 옵션에 따라서 합니다. 다음 시나리오 포괄적인 수 있지만 대신 몇 가지 일반적인 경우를 설명 하 고 권장 사항을 제공 하기 위한 되지 않습니다.

### <a name="many-resource-files"></a>여러 리소스 파일

일괄 처리 작업 모두 동일 하 고 일반적인 파일을 사용 하는 몇 가지 작업을 포함할 수 있습니다. 일반 작업 파일은 여러 작업 간에 공유할 수 하는 경우 리소스 파일을 사용 하는 대신 파일을 포함 하는 응용 프로그램 패키지를 사용 하 여 방법이 더 나을 수 있습니다. 응용 프로그램 패키지 다운로드 속도 대 한 최적화를 제공합니다. 작업 간에 응용 프로그램 패키지의 데이터는 캐시 하는 또한 응용 프로그램 패키지 적합 한 솔루션을 수 있습니다 작업 파일 자주 변경 하지 않습니다. 응용 프로그램 패키지를 사용 하 여 수동으로 여러 리소스 파일을 관리 하거나 Azure Storage에 파일에 액세스 하려면 SAS Url을 생성할 필요가 없습니다. 일괄 처리를 저장 하 고 계산 노드에 응용 프로그램 패키지를 배포 하는 Azure Storage를 사용 하 여 백그라운드에서 작동 합니다.

각 작업에 여러 파일이 해당 작업에 고유한 경우 리소스 파일은 중 가능성이 가장 높은 가장 적합 한 옵션입니다. 고유 파일을 자주 사용 하는 작업으로 쉽게 응용 프로그램 패키지 콘텐츠를 사용 하 여 수행할 수 없는 업데이트 하거나 교체 해야 합니다. 리소스 파일을 업데이트, 추가 또는 개별 파일 편집을 위한 추가적인 유연성을 제공 합니다.

### <a name="number-of-resource-files-per-task"></a>태스크 당 리소스 파일의 수

작업을 지정 하는 몇 백 리소스 파일의 경우 일괄 처리는 너무 커서에 대 한 작업을 거부할 수 있습니다. 작업 자체에서 리소스 파일의 수를 최소화 하 여 작업을 작게 유지 하는 것이 좋습니다.

없으므로 작업 파일 수를 최소화 하기 위해 필요한 경우 리소스 파일의 저장소 컨테이너를 참조 하는 단일 리소스 파일을 만들어 작업을 최적화할 수 있습니다. 이 위해 Azure Storage 컨테이너에 리소스 파일을 배치 및 리소스 파일의 다른 "컨테이너" 모드를 사용 합니다. Blob 접두사 옵션을 사용 하 여 작업에 대해 다운로드할 파일의 컬렉션을 지정 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 [응용 프로그램 패키지](batch-application-packages.md) 리소스 파일을 대신 합니다.
- 리소스 파일에 대 한 컨테이너를 사용 하는 방법에 대 한 자세한 내용은 참조 하세요. [컨테이너 워크 로드](batch-docker-container-workloads.md)합니다.
- 수집 작업에서 출력 데이터를 저장 하는 방법을 참조 하십시오 [작업 및 태스크 출력 유지](batch-task-output.md)합니다.
- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.