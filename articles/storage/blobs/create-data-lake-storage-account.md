---
title: Azure Data Lake Storage Gen2에 대 한 저장소 계정 만들기
description: Azure Data Lake Storage Gen2와 함께 사용할 저장소 계정을 만드는 방법에 대해 알아봅니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 6b63933fc625079bb490942cf3a32232a484fe38
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270326"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 사용할 저장소 계정 만들기

Data Lake Storage Gen2 기능을 사용 하려면 계층적 네임 스페이스를 포함 하는 저장소 계정을 만듭니다.

## <a name="choose-a-storage-account-type"></a>저장소 계정 유형 선택

Data Lake Storage 기능은 다음 유형의 저장소 계정에서 지원 됩니다.

- 범용 v2
- BlockBlobStorage

이러한 항목 중 하나를 선택 하는 방법에 대 한 자세한 내용은 [저장소 계정 개요](../common/storage-account-overview.md)를 참조 하세요.

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>계층적 네임 스페이스를 사용 하 여 저장소 계정 만들기

**계층적 네임 스페이스** 설정을 사용 하는 [일반 용도의 V2 계정](../common/storage-account-create.md) 또는 [blockblobstorage](storage-blob-create-account-block-blob.md) 계정을 만듭니다.

**저장소 계정 만들기** 페이지의 **고급** 탭에서 **계층 구조 네임 스페이스** 설정을 사용 하도록 설정 하 여 계정을 만들 때 Data Lake Storage 기능을 잠금 해제 합니다. 계정을 만들 때이 설정을 사용 하도록 설정 해야 합니다. 나중에 사용 하도록 설정할 수 없습니다.

다음 이미지는 **저장소 계정 만들기** 페이지에서이 설정을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![계층 구조 네임 스페이스 설정](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Data Lake Storage 사용 하려는 기존 저장소 계정이 있고 계층 구조 네임 스페이스 설정을 사용 하지 않도록 설정한 경우 해당 설정을 사용 하도록 설정 된 새 저장소 계정으로 데이터를 마이그레이션해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Storage 계정 개요](../common/storage-account-overview.md)
- [빅 데이터 요구 사항을 위한 Azure Data Lake Storage Gen2 사용](data-lake-storage-data-scenarios.md)
- [Azure Data Lake Storage Gen2의 액세스 제어](data-lake-storage-access-control.md)