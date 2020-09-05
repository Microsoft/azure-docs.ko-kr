---
title: Azure Data Lake Storage Gen2 검색 (미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Data Lake Storage Gen2에서 콘텐츠 및 메타 데이터를 인덱싱하는 방법을 알아봅니다. 이 기능은 현재 공개 미리 보기 상태입니다.
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: b1a88398d657e6bc242c7db12f3c0ddc1af828ca
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935860"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에서 문서 인덱싱

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 지원은 현재 공개 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성 하 여 미리 보기에 대 한 액세스를 요청할 수 있습니다. [REST API 버전 2020-06-30-미리 보기](search-api-preview.md) 및 포털에서이 기능을 제공 합니다. 현재 .NET SDK는 지원되지 않습니다.


Azure storage 계정을 설정할 때 [계층적 네임 스페이스](../storage/blobs/data-lake-storage-namespace.md)를 사용 하도록 설정 하는 옵션이 있습니다. 이를 통해 계정의 콘텐츠 컬렉션을 디렉터리 및 중첩 된 하위 디렉터리의 계층 구조로 구성할 수 있습니다. 계층적 네임 스페이스를 사용 하도록 설정 하 여 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)를 사용 하도록 설정 합니다.

이 문서에서는 Azure Data Lake Storage Gen2에 있는 인덱싱 문서를 시작 하는 방법을 설명 합니다.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Azure Data Lake Storage Gen2 인덱서 설정

Data Lake Storage Gen2에서 콘텐츠를 색인화 하려면 몇 가지 단계를 완료 해야 합니다.

### <a name="step-1-sign-up-for-the-preview"></a>1 단계: 미리 보기 등록

[이 양식을](https://aka.ms/azure-cognitive-search/indexer-preview)작성 하 여 Data Lake Storage Gen2 인덱서 미리 보기에 등록 합니다. 미리 보기에 동의 하면 확인 전자 메일을 받게 됩니다.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>2 단계: Azure Blob storage 인덱싱 설정 단계를 따릅니다.

미리 보기 등록에 성공 했음을 확인 한 후에는 인덱싱 파이프라인을 만들 준비가 완료 된 것입니다.

[REST API 버전 2020-06-30-미리 보기](search-api-preview.md) 또는 포털을 사용 하 여 Data Lake Storage Gen2에서 콘텐츠 및 메타 데이터를 인덱싱할 수 있습니다. 지금은 .NET SDK 지원이 없습니다.

Data Lake Storage Gen2의 인덱싱 콘텐츠는 Azure Blob Storage에서 콘텐츠를 인덱싱하는 것과 동일 합니다. Data Lake Storage Gen2 데이터 원본, 인덱스 및 인덱서를 설정 하는 방법을 이해 하려면 [Azure Cognitive Search를 사용 하 여 Azure Blob Storage 문서를 인덱싱하는 방법](search-howto-indexing-azure-blob-storage.md)을 참조 하세요. 또한 Blob storage 문서는 지원 되는 문서 형식, 추출 되는 blob 메타 데이터 속성, 증분 인덱싱 등에 대 한 정보를 제공 합니다. 이 정보는 Data Lake Storage Gen2에 대해 동일 합니다.

## <a name="access-control"></a>Access Control

Azure Data Lake Storage Gen2은 azure RBAC (역할 기반 액세스 제어) 및 POSIX와 같은 Acl (액세스 제어 목록)을 모두 지 원하는 [액세스 제어 모델](../storage/blobs/data-lake-storage-access-control.md) 을 구현 합니다. Data Lake Storage Gen2에서 콘텐츠를 인덱싱할 때 Azure Cognitive Search는 콘텐츠에서 RBAC 및 ACL 정보를 추출 하지 않습니다. 따라서이 정보는 Azure Cognitive Search 인덱스에 포함 되지 않습니다.

인덱스의 각 문서에 대 한 액세스 제어를 유지 하는 것이 중요 한 경우에는 응용 프로그램 개발자가 [보안 트리밍을](./search-security-trimming-for-azure-search.md)구현 하는 것이 중요 합니다.

## <a name="change-detection"></a>변경 내용 검색

Data Lake Storage Gen2 인덱서는 변경 내용 검색을 지원 합니다. 즉, 인덱서가 실행 될 때 blob의 타임 스탬프에 의해 결정 된 대로 변경 된 blob만 다시 인덱싱합니다 `LastModified` .

> [!NOTE] 
> Data Lake Storage Gen2 디렉터리의 이름을 바꿀 수 있습니다. 디렉터리의 이름을 바꾸면 해당 디렉터리의 blob에 대 한 타임 스탬프가 업데이트 되지 않습니다. 결과적으로 인덱서는 이러한 blob의 인덱스를 만들지 않습니다. 디렉터리 이름 바꾸기 후에 디렉터리의 blob이 새 Url을 포함 하 여 인덱싱해야 해야 하는 경우 나중에 실행 하는 `LastModified` 동안 인덱서가 인덱스를 다시 인덱싱 하도록 디렉터리의 모든 blob에 대 한 타임 스탬프를 업데이트 해야 합니다.