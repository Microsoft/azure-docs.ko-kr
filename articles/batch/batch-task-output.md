---
title: 데이터 저장소에 완료된 작업 및 태스크의 결과 또는 로그 유지 - Azure Batch | Microsoft Docs
description: Batch 작업 및 태스크의 출력 데이터를 유지하기 위한 다양한 옵션을 알아봅니다. Azure Storage 또는 다른 데이터 저장소에 데이터를 유지할 수 있습니다.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bc579cd372616563b61e5ba04fe32612f3efb1c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60549961"
---
# <a name="persist-job-and-task-output"></a>작업 및 태스크 출력 유지

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

태스크 출력의 일반적인 예는 다음과 같습니다.

- 태스크에서 입력 데이터를 처리할 때 만든 파일
- 태스크 실행과 관련된 로그 파일

이 문서에서는 태스크 출력을 유지하기 위한 다양한 옵션을 설명합니다.

## <a name="options-for-persisting-output"></a>출력 유지 옵션

시나리오에 따라 태스크 출력을 유지하는 데 사용할 수 있는 별도의 몇 가지 방법이 있습니다.

- [Batch 서비스 API 사용](batch-task-output-files.md)  
- [.NET용 Batch 파일 규칙 라이브러리 사용](batch-task-output-file-conventions.md).  
- 애플리케이션에 Batch 파일 규칙 표준을 구현합니다.
- 사용자 지정 파일 이동 솔루션을 구현합니다.

다음 섹션에서는 출력을 유지하기 위한 일반적인 디자인 고려 사항 뿐만 아니라 각 접근 방식에 대해 간단히 설명합니다.

### <a name="use-the-batch-service-api"></a>Batch 서비스 API 사용

Batch 서비스는 [태스크를 작업에 추가](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job)하거나 [태스크의 컬렉션을 작업에 추가](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job)할 때 태스크 데이터에 대한 Azure Storage의 출력 파일을 지정하도록 지원합니다.

Batch 서비스 API를 사용하여 태스크 출력을 유지하는 방법에 대한 자세한 내용은 [Batch 서비스 API를 사용하여 Azure Storage에 태스크 데이터 유지](batch-task-output-files.md)를 참조하세요.

### <a name="use-the-batch-file-conventions-library-for-net"></a>.NET용 Batch 파일 규칙 라이브러리 사용

Batch는 Azure Storage에서 태스크 출력 파일의 이름을 지정하기 위한 선택적인 규칙 집합을 정의합니다. [Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)(영문)은 이러한 규칙을 설명합니다. 파일 규칙 표준에서는 지정된 출력 파일에 대한 Azure Storage 대상 컨테이너 및 Blob 경로의 이름을 작업 및 태스크의 이름을 기반으로 하여 결정합니다.

출력 데이터 파일의 이름을 지정하는 데 파일 규칙 표준을 사용할지 여부는 사용자에게 달려 있습니다. 원하는 경우 대상 컨테이너와 Blob의 이름을 지정할 수도 있습니다. 파일 규칙 표준을 사용하여 출력 파일 이름을 지정하면 [Azure Portal][portal]에서 출력 파일을 볼 수 있습니다.

개발자가 C# 및 .NET을 사용하여 [Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)(영문)에 따라 Batch 솔루션을 빌드하면 [.NET용 파일 규칙 라이브러리][nuget_package](영문)를 사용하여 Azure Storage 계정에 태스크 데이터를 유지할 수 있습니다. 파일 규칙 라이브러리는 출력 파일을 Azure Storage로 이동하고 대상 컨테이너와 Blob의 이름을 잘 알려진 방법으로 지정합니다.

.NET용 파일 규칙 라이브러리를 사용하여 태스크 출력을 유지하는 방법에 대한 자세한 내용은 [.NET용 Batch 파일 규칙 라이브러리를 사용하여 Azure Storage에 작업 및 태스크 데이터 유지](batch-task-output-file-conventions.md)를 참조하세요.

### <a name="implement-the-batch-file-conventions-standard"></a>Batch 파일 규칙 표준 구현

.NET 이외의 언어를 사용하는 경우 사용자 고유의 애플리케이션에서 [Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)을 구현할 수 있습니다.

검증된 명명 체계가 필요하거나 Azure Portal에서 태스크 출력을 보려는 경우 파일 규칙 이름 지정 표준을 직접 구현할 수도 있습니다.

### <a name="implement-a-custom-file-movement-solution"></a>사용자 지정 파일 이동 솔루션 구현

사용자 고유의 완벽한 파일 이동 솔루션도 구현할 수 있습니다. 다음과 같은 경우에는 이 방법을 사용합니다.

- Azure Storage 이외의 다른 데이터 저장소에 태스크 데이터를 저장하려고 합니다. Azure SQL 또는 Azure DataLake와 같은 데이터 저장소에 파일을 업로드하려면 사용자 지정 스크립트 또는 실행 파일을 만들어 해당 위치에 업로드할 수 있습니다. 그런 다음 기본 실행 파일을 실행한 후 명령줄에서 호출할 수 있습니다. 예를 들어 Windows 노드에서 두 명령(`doMyWork.exe && uploadMyFilesToSql.exe`)을 호출할 수 있습니다.
- 초기 결과의 검사점 확인(check-pointing) 또는 초기 업로드를 수행하려고 합니다.
- 오류 처리에 대한 세부적인 제어를 유지하려고 합니다. 예를 들어 태스크 종속성 동작을 사용하여 특정 태스크 종료 코드에 기반한 특정 업로드 작업을 수행하려는 경우 사용자 고유의 솔루션을 구현하는 것이 좋습니다. 태스크 종속성 동작에 대한 자세한 내용은 [태스크 종속성을 만들어 다른 태스크에 종속된 태스크 실행](batch-task-dependencies.md)을 참조하세요.

## <a name="design-considerations-for-persisting-output"></a>출력 유지 설계 시 고려 사항

Batch 솔루션을 디자인할 때 작업 및 태스크 출력과 관련된 다음 요소를 고려해 보세요.

- **컴퓨팅 노드 수명**: 컴퓨팅 노드는 일시적으로만 활성화되는 경우가 많으며 특히 자동 크기 조정이 가능한 풀에서는 항상 일시적으로만 활성화됩니다. 노드가 존재하는 동안과 태스크에 대해 설정한 파일 보존 기간 내에서만 노드에서 실행되는 태스크의 출력을 사용할 수 있습니다. 태스크가 완료된 후 필요할 수 있는 출력을 태스크에서 생성하는 경우 태스크는 출력 파일을 Azure Storage와 같은 영구 저장소에 업로드해야 합니다.

- **출력 스토리지**: 작업 출력용 데이터 저장소로는 Azure Storage를 사용하는 것이 좋지만 어떤 지속형 스토리지라도 사용 가능합니다. Azure Storage에 태스크 출력을 쓰는 것은 Batch 서비스 API에 통합되어 있습니다. 다른 형태의 영구 스토리지를 사용하는 경우 태스크 출력을 유지하기 위해 애플리케이션 논리를 직접 작성해야 합니다.

- **출력 검색**: 작업 출력은 풀의 컴퓨팅 노드에서 직접 검색할 수도 있고, 작업 출력을 영구적으로 저장한 경우에는 Azure Storage 또는 다른 데이터 저장소에서 검색할 수도 있습니다. 태스크의 출력을 계산 노드에서 직접 검색하려면 파일 이름과 노드에서의 해당 출력 위치가 필요합니다. Azure Storage에 태스크 출력을 유지하는 경우 Azure Storage SDK를 사용하여 출력 파일을 다운로드하려면 Azure Storage의 파일에 대한 전체 경로가 필요합니다.

- **출력 보기**: Azure Portal에서 Batch 태스크로 이동하여 **노드의 파일**을 선택하면 원하는 출력 파일만 표시되는 것이 아니라 태스크와 연관된 모든 파일이 표시됩니다. 계산 노드의 파일은 노드가 존재하는 동안 해당 태스크에 대해 설정한 파일 보존 기간 내에서만 사용할 수 있습니다. Azure Storage에 유지한 태스크 출력을 보려면 Azure Portal 또는 Azure Storage 클라이언트 애플리케이션(예: [Azure Storage 탐색기][storage_explorer])을 사용할 수 있습니다. 포털이나 다른 도구를 사용하여 Azure Storage의 출력 데이터를 보려면 파일의 위치를 파악하여 직접 이동해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Batch 서비스 API를 사용하여 Azure Storage에 태스크 데이터 유지](batch-task-output-files.md)에서 Batch 서비스 API의 새 기능을 사용하여 태스크 데이터를 유지합니다.
- [.NET용 Batch 파일 규칙 라이브러리를 사용하여 Azure Storage에 작업 및 태스크 데이터 유지](batch-task-output-file-conventions.md)에서 .NET용 Batch 파일 규칙 라이브러리를 사용하는 방법을 알아봅니다.
- GitHub의 [PersistOutputs][github_persistoutputs] 샘플 프로젝트를 참조하세요. 여기서는 .NET용 Batch 클라이언트 라이브러리와 .NET용 파일 규칙 라이브러리를 모두 사용하여 태스크 출력을 영구 저장소에 유지하는 방법을 보여 줍니다.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
