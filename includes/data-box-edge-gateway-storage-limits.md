---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 94fe099984fae77c65658d7085a8540ff4f2448b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60755247"
---
이 섹션에서는 Data Box Gateway/Data Box Edge 서비스에 적용되는, Azure Storage 서비스에 대한 제한 및 Azure Files, Azure 블록 Blob 및 Azure 페이지 Blob에 대한 필수 명명 규칙을 설명합니다. 저장소 제한을 신중히 검토하고 모든 권장 사항을 수행합니다.

Azure 저장소 서비스 제한에 대한 최신 정보 및 공유, 컨테이너 및 파일 이름 지정에 대한 모범 사례는 다음으로 이동합니다.

- [컨테이너 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [공유 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [블록 Blob 및 페이지 Blob 규칙](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Azure Storage 서비스 제한을 초과하거나 Azure Files/Blob 명명 규칙을 준수하지 않는 파일 또는 디렉터리가 있는 경우 이러한 파일 또는 디렉터리는 Data Box Gateway/Data Box Edge 서비스를 통해 Azure Storage로 수집되지 않습니다.