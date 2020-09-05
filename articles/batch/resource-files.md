---
title: 리소스 파일 생성 및 사용
description: 다양한 입력 소스에서 Batch 리소스 파일을 만드는 방법을 알아봅니다. 이 문서에서는 이를 만들고 VM에 추가하는 몇 가지 일반적인 방법을 설명합니다.
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 22c0220d08660402bef3fd4aaf0add6adc12a295
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230945"
---
# <a name="creating-and-using-resource-files"></a>리소스 파일 생성 및 사용

Azure Batch 태스크를 처리하려면 일부 데이터 형식이 필요한 경우가 많습니다. 리소스 파일을 사용하면 태스크를 통해 Batch VM(가상 머신)에 이러한 데이터를 제공할 수 있습니다. 모든 유형의 태스크는 태스크, 시작 태스크, 작업 준비 태스크, 작업 릴리스 태스크 등의 리소스 파일을 지원합니다. 이 문서에서는 리소스 파일을 만들고 VM에 추가하는 몇 가지 일반적인 방법을 설명합니다.  

리소스 파일은 Batch VM에 데이터를 추가하지만 데이터 유형과 사용 방식은 자유롭습니다. 그러나 다음과 같은 몇 가지 일반적인 사용 사례가 있습니다.

1. 시작 태스크에서 리소스 파일을 사용하여 각 VM에 공용 파일 프로비저닝
1. 태스크에서 처리할 입력 데이터 프로비저닝

예를 들어 공용 파일은 태스크가 실행하는 애플리케이션을 설치하는 데 사용되는 시작 태스크의 파일일 수 있습니다. 입력 데이터는 원시 이미지 또는 동영상 데이터이거나 Batch가 처리할 정보일 수 있습니다.

## <a name="types-of-resource-files"></a>리소스 파일 형식

리소스 파일을 생성하는 데 사용할 수 있는 몇 가지 옵션이 있습니다. 리소스 파일 생성 프로세스는 원래 데이터가 저장되는 위치에 따라 달라집니다.

리소스 파일 생성 옵션:

- [스토리지 컨테이너 URL](#storage-container-url): Azure의 모든 스토리지 컨테이너에서 리소스 파일을 생성합니다.
- [스토리지 컨테이너 이름](#storage-container-name): Batch에 연결된 Azure 스토리지 계정의 컨테이너 이름으로 리소스 파일을 생성합니다.
- [웹 엔드포인트](#web-endpoint): 모든 유효한 HTTP URL로 리소스 파일을 생성합니다.

### <a name="storage-container-url"></a>스토리지 컨테이너 URL

스토리지 컨테이너 URL을 사용하면 올바른 권한을 가지고 Azure의 모든 스토리지 컨테이너 파일에 액세스할 수 있습니다. 

이 C# 예제에서는 Blob Storage인 Azure 스토리지 컨테이너에 파일이 이미 업로드되었습니다. 리소스 파일을 만드는 데 필요한 데이터에 액세스하려면 먼저 스토리지 컨테이너에 액세스할 수 있어야 합니다.

스토리지 컨테이너에 액세스할 수 있는 올바른 권한을 사용하여 SAS(공유 액세스 서명) URI를 만듭니다. SAS의 만료 시간 및 사용 권한을 설정합니다. 이 경우 시작 시간은 지정되지 않으므로 SAS가 즉시 유효해지고 생성 후 2시간이 지나면 만료됩니다.

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 컨테이너 액세스의 경우 `Read` 및 `List` 권한이 모두 있어야 하지만 Blob 액세스 권한이 있으면 `Read` 권한만 있으면 됩니다.

사용 권한이 구성되면 SAS 토큰을 만들고 스토리지 컨테이너에 액세스할 수 있도록 SAS URL의 형식을 지정합니다. 스토리지 컨테이너용으로 형식이 지정된 SAS URL을 사용하여 [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl)을 통해 리소스 파일을 생성합니다.

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

SAS URL을 생성하는 대신 컨테이너 및 Azure Blob Storage의 해당 Blob에 대한 익명의 공용 읽기 권한을 사용하도록 설정할 수 있습니다. 이렇게 하면 계정 키를 공유하지 않고 SAS를 요구하지 않고도 이러한 리소스에 대해 읽기 전용 권한을 부여할 수 있습니다. 공용 읽기 권한은 일반적으로 특정 Blob을 항상 익명 읽기 액세스에 사용할 수 있게 하려는 경우에 사용됩니다. 이 시나리오가 솔루션에 적합한 경우 [Blob에 대한 익명 액세스](../storage/blobs/storage-manage-access-to-resources.md) 문서를 참조하여 Blob 데이터에 대한 액세스 관리에 대해 자세히 알아보세요.

### <a name="storage-container-name"></a>스토리지 컨테이너 이름

SAS URL을 구성하고 만드는 대신 Azure Storage 컨테이너의 이름을 사용하여 Blob 데이터에 액세스할 수 있습니다. 사용하는 스토리지 컨테이너는 Batch 계정에 연결된 Azure Storage 계정에 있어야 합니다. 이 스토리지 계정을 autostorage 계정이라고 합니다. autostorage 컨테이너를 사용하면 SAS URL을 구성하고 생성하지 않고도 스토리지 컨테이너에 액세스할 수 있습니다.

이 예제에서는 리소스 파일을 만드는 데 사용되는 데이터가 Batch 계정에 연결된 Azure Storage 계정에 이미 있다고 가정합니다. autostorage 계정이 없는 경우 [Batch 계정 만들기](batch-account-create-portal.md)에서 계정을 만들고 연결하는 방법에 대한 자세한 단계별 설명을 참조하세요.

연결된 스토리지 계정을 사용하여 스토리지 컨테이너에 대한 SAS URL을 만들고 구성할 필요가 없습니다. 대신, 연결된 스토리지 계정에 스토리지 컨테이너의 이름을 지정하면 됩니다.

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>웹 엔드포인트

Azure Storage에 업로드되지 않은 데이터도 리소스 파일을 만드는 데 사용할 수 있습니다. 입력 데이터를 포함하는 유효한 HTTP URL을 지정할 수 있습니다. 이 URL이 Batch API에 제공되면 데이터를 사용하여 리소스 파일이 생성됩니다.

다음 C# 예제에서는 입력 데이터가 가상의 GitHub 엔드포인트에서 호스팅됩니다. API는 유효한 웹 엔드포인트에서 파일을 검색하고 태스크에서 사용할 리소스 파일을 생성합니다. 이 경우에는 자격 증명이 필요하지 않습니다.

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

각 Azure Batch 태스크마다 파일을 사용하는 방법이 달라서 Batch는 태스크에 대한 파일을 관리하는 데 사용할 수 있는 옵션을 제공합니다. 다음 시나리오는 포괄적이지는 않지만 대신 몇 가지 일반적인 상황을 다루며 권장 사항을 제공합니다.

### <a name="many-resource-files"></a>여러 리소스 파일

Batch 작업에는 모두 동일한 공용 파일을 사용하는 여러 태스크가 포함될 수 있습니다. 공용 태스크 파일이 여러 태스크에서 공유되는 경우 리소스 파일을 사용하는 대신 애플리케이션 패키지를 사용하여 파일을 포함하는 것이 더 나은 방법일 수 있습니다. 애플리케이션 패키지는 다운로드 속도 최적화를 제공합니다. 또한 애플리케이션 패키지의 데이터는 태스크 간에 캐시되므로 태스크 파일이 자주 변경되지 않는다면 애플리케이션 패키지가 솔루션에 적합할 수 있습니다. 애플리케이션 패키지를 사용하면 여러 리소스 파일을 수동으로 관리하거나 SAS URL을 생성하여 Azure Storage의 파일에 액세스할 필요가 없습니다. Batch는 Azure Storage와 함께 백그라운드에서 작동하여 애플리케이션 패키지를 저장하고 컴퓨팅 노드에 배포합니다.

각 태스크에 해당 작업에 고유한 많은 파일이 있는 경우 고유한 파일을 사용 하는 작업을 업데이트 하거나 교체 해야 하는 경우가 많으므로 응용 프로그램 패키지 콘텐츠를 사용 하는 것이 쉽지 않으므로 리소스 파일이 가장 좋습니다. 리소스 파일은 개별 파일의 업데이트, 추가 또는 편집에 대한 추가 유연성을 제공합니다.

### <a name="number-of-resource-files-per-task"></a>태스크당 리소스 파일 수

한 태스크에 수백 개의 리소스 파일이 지정된 경우 Batch에서 태스크가 너무 크다며 거부할 수 있습니다. 태스크 자체에서 리소스 파일 수를 최소화하여 태스크를 작게 유지하는 것이 가장 좋습니다.

태스크에 필요한 파일 수를 최소화할 방법이 없는 경우 리소스 파일의 스토리지 컨테이너를 참조하는 단일 리소스 파일을 만들어 태스크를 최적화할 수 있습니다. 이렇게 하려면 리소스 파일을 Azure Storage 컨테이너에 넣고 리소스 파일에 다양한 "컨테이너" [메서드](/dotnet/api/microsoft.azure.batch.resourcefile#methods)를 사용합니다. Blob 접두사 옵션을 사용하여 태스크용으로 다운로드할 파일 컬렉션을 지정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 리소스 파일의 대체 옵션인 [애플리케이션 패키지](batch-application-packages.md)에 대해 알아봅니다.
- 리소스 파일에 컨테이너를 사용하는 방법에 대한 자세한 내용은 [컨테이너 워크로드](batch-docker-container-workloads.md)를 참조하세요.
- 태스크에서 출력 데이터를 수집하고 저장하는 방법을 알아보려면 [작업 및 태스크 출력 유지](batch-task-output.md)를 참조하세요.
- Batch 솔루션을 빌드하는 데 사용할 수 있는 [Batch API 및 도구](batch-apis-tools.md)에 대해 알아봅니다.
