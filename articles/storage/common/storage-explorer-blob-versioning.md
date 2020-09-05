---
title: Azure Storage 탐색기 blob 버전 관리 가이드 | Microsoft Docs
description: Azure Storage 탐색기에 대 한 Blob 버전 관리 지침
services: storage
author: chuye
ms.service: storage
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: chuye
ms.openlocfilehash: d318983cec1365b71d14731395e71528621659d6
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051931"
---
# <a name="azure-storage-explorer-blob-versioning-guide"></a>Azure Storage 탐색기 blob 버전 관리 가이드

Microsoft Azure Storage 탐색기는 blob 버전을 간편 하 게 액세스 하 고 관리할 수 있습니다. 이 가이드는 Storage 탐색기에서 blob 버전 관리가 작동 하는 방식을 이해 하는 데 도움이 됩니다. 계속 하기 전에 [blob 버전 관리](https://docs.microsoft.com/azure/storage/blobs/versioning-overview)에 대 한 자세한 내용을 확인 하는 것이 좋습니다.

## <a name="terminology"></a>용어

이 섹션에서는이 문서에서 사용법을 이해 하는 데 도움이 되는 몇 가지 정의를 제공 합니다.

- 일시 삭제: 대체 자동 데이터 보호 기능입니다. [여기](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview)에서 일시 삭제에 대해 자세히 알아볼 수 있습니다.
- 활성 blob: blob 또는 blob 버전이 활성 상태에서 만들어집니다. 활성 상태의 blob 또는 blob 버전 에서만 작동할 수 있습니다.
- 일시 삭제 된 blob: 일시 삭제 된 것으로 표시 된 blob 또는 blob 버전입니다. 일시 삭제 된 blob은 보존 기간 동안만 유지 됩니다.
- Blob 버전: blob 버전 관리를 사용 하 여 만든 blob입니다. 각 blob 버전은 버전 ID와 연결 됩니다.
- 현재 버전: 현재 버전으로 표시 된 blob 버전입니다.
- 이전 버전: 현재 버전이 아닌 blob 버전입니다.
- 버전이 아닌 blob: blob 버전 관리를 사용 하지 않도록 설정 하 여 만든 blob입니다. 버전이 아닌 blob에는 버전 ID가 없습니다.

## <a name="view-blob-versions"></a>Blob 버전 보기

Storage 탐색기는 blob을 볼 수 있는 네 가지 보기를 지원 합니다.

| 보기 | 활성 버전이 아닌 blob | 일시 삭제 된 버전이 아닌 blob | Blob 버전 |
| ---- | :----------: | :-----------: | :------------------: |
| 활성 blob | 예 | 예 | 현재 버전만 |
| 활성 blob 및 일시 삭제 된 blob | 예 | 예 | 현재 버전만 |
| 현재 버전이 없는 활성 blob 및 blob | 예 | 예 | 현재 버전 또는 최신 활성 버전 |
| 현재 버전이 없는 모든 blob 및 blob | 예 | 예 | 최신 버전 또는 최신 버전 |

### <a name="active-blobs"></a>활성 blob

이 뷰에서 Storage 탐색기 표시 합니다.

- 활성 버전이 아닌 blob
- 현재 버전

### <a name="active-blobs-and-soft-deleted-blobs"></a>활성 blob 및 일시 삭제 된 blob

이 뷰에서 Storage 탐색기 표시 합니다.

- 활성 버전이 아닌 blob
- 일시 삭제 된 버전이 아닌 blob
- 최신 버전.

### <a name="active-blobs-and-blobs-without-current-version"></a>현재 버전이 없는 활성 blob 및 blob

이 뷰에서 Storage 탐색기 표시 합니다.

- 활성 버전이 아닌 blob
- 현재 버전
- 최신 활성 이전 버전입니다. 

최신 버전이 없지만 활성 이전 버전을 포함 하는 blob의 경우에는 Storage 탐색기 최신 활성 이전 버전을 해당 blob의 표현으로 표시 합니다.

### <a name="all-blobs-and-blobs-without-current-version"></a>현재 버전이 없는 모든 blob 및 blob

이 뷰에서 Storage 탐색기 표시 합니다.

- 활성 버전이 아닌 blob
- 일시 삭제 된 버전이 아닌 blob
- 현재 버전
- 최신 이전 버전. 

최신 버전이 없는 blob의 경우 Storage 탐색기 최신 이전 버전을 해당 blob의 표현으로 표시 합니다.

> [!Note]
> 서비스 제한으로 인해 blob 버전을 나열할 때 가상 디렉터리의 계층 뷰를 가져오기 위해 몇 가지 추가 처리가 필요 Storage 탐색기. 다음 뷰에서 blob을 나열 하는 데 시간이 더 오래 걸립니다.
> 
> - 현재 버전이 없는 활성 blob 및 blob
> - 현재 버전이 없는 모든 blob 및 blob

## <a name="manage-blob-versions"></a>Blob 버전 관리

### <a name="view-versions-of-a-blob"></a>Blob의 버전 보기

Storage 탐색기는 모든 버전의 blob을 볼 수 있는 **버전 관리** 명령을 제공 합니다. Blob의 버전을 보려면 버전을 보려는 blob을 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 **기록 관리 &rarr; 버전** 관리를 선택 합니다.

### <a name="download-blob-versions"></a>Blob 버전 다운로드

하나 이상의 blob 버전을 다운로드 하려면 다운로드 하려는 blob 버전을 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 **다운로드** 를 선택 합니다.

여러 버전의 blob을 다운로드 하는 경우 다운로드 한 파일의 버전 Id가 파일 이름 시작 부분에 표시 됩니다.

### <a name="delete-blob-versions"></a>Blob 버전 삭제

하나 이상의 blob 버전을 삭제 하려면 삭제할 blob 버전을 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 **삭제** 를 선택 합니다.

Blob 버전에는 일시 삭제 정책이 적용 됩니다. 일시 삭제를 사용 하는 경우 blob 버전은 일시 삭제 됩니다. 한 가지 특별 한 사례는 현재 버전을 삭제 하는 것입니다. 현재 버전을 삭제 하면 자동으로 활성 이전 버전이 됩니다.

### <a name="promote-blob-version"></a>Blob 버전 승격

이전 버전을 현재 버전으로 승격 시켜 blob의 내용을 복원할 수 있습니다. 승격할 blob 버전을 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 **버전 승격** 을 선택 합니다.

확장 되지 않은 blob 버전은 버전이 아닌 blob을 덮어씁니다. 작업을 확인 하기 전에 해당 데이터가 더 이상 필요 하지 않거나 데이터를 직접 백업 하지 않도록 합니다. 현재 버전은 자동으로 이전 버전이 되므로 Storage 탐색기 확인 메시지가 표시 되지 않습니다.

### <a name="undelete-blob-version"></a>Blob 버전 삭제 취소

Blob 버전은 개별적으로 삭제 취소할 수 없습니다. 한 번에 모두 삭제 취소 해야 합니다. Blob의 모든 blob 버전을 삭제 취소 하려면 blob의 버전 중 하나를 선택 하 고 도구 모음 또는 상황에 맞는 메뉴에서 **선택한 삭제 취소** 를 선택 합니다.

### <a name="change-access-tier-of-blob-versions"></a>Blob 버전의 액세스 계층 변경

각 blob 버전에는 고유한 액세스 계층이 있습니다. Blob 버전의 액세스 계층을 변경 하려면 액세스 계층을 변경할 blob 버전을 선택 하 고 상황에 맞는 메뉴에서 **액세스 계층 변경 ...** 을 선택 합니다.

## <a name="see-also"></a>참고 항목

* [Blob 버전 관리](https://docs.microsoft.com/azure/storage/blobs/versioning-overview)
* [Blob에 대한 일시 삭제](https://docs.microsoft.com/azure/storage/blobs/soft-delete-blob-overview)
