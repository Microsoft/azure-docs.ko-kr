---
title: 자산
titleSuffix: Azure Media Services
description: Azure Media Services에서 자산 및 사용 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 3507d3f0ac978c6f7c2f5fc29fc563eaba79d98a
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89258863"
---
# <a name="assets-in-azure-media-services-v3"></a>Azure Media Services v3의 자산

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services에서 [자산은](/rest/api/media/assets) 핵심 개념입니다. 미디어를 입력 하는 위치 (예: 업로드 또는 라이브 수집), 출력 미디어 (작업 출력에서) 및 미디어 게시 (스트리밍의 경우)입니다. 

자산은 [Azure Storage 계정의](storage-account-concept.md) blob 컨테이너에 매핑되고 자산의 파일은 해당 컨테이너에 블록 blob으로 저장 됩니다. 자산에는 Azure Storage에 저장 된 디지털 파일 (비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 닫힌 캡션 파일 포함)에 대 한 정보가 포함 되어 있습니다.

Media Services는 계정이 범용 v2(GPv2) 스토리지를 사용할 때 Blob 계층을 지원합니다. GPv2를 사용하는 경우 파일을 [쿨 또는 보관 스토리지](../../storage/blobs/storage-blob-storage-tiers.md)로 이동할 수 있습니다. **보관** 저장소는 인코딩된 후와 같이 더 이상 필요 하지 않은 경우 원본 파일을 보관 하는 데 적합 합니다.

**보관** 스토리지 계층은 이미 인코딩되었고 인코딩 작업 출력이 출력 Blob 컨테이너에 배치된 대용량 원본 파일에만 사용할 것을 권장합니다. 자산에 연결 하 여 콘텐츠를 스트리밍 또는 분석 하는 데 사용 하려는 출력 컨테이너의 blob은 **핫** 또는 **쿨** 저장소 계층에 있어야 합니다.

## <a name="naming"></a>이름 지정 

### <a name="assets"></a>자산

자산의 이름은 고유 해야 합니다. Media Services v3 리소스 이름 (예: 자산, 작업, 변환)에는 Azure Resource Manager 명명 제약 조건이 적용 됩니다. 자세한 내용은 [명명 규칙](media-services-apis-overview.md#naming-conventions)을 참조 하세요.

### <a name="blobs"></a>Blob

자산 내의 파일/BLOB 이름은 [BLOB 이름 요구 사항](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)과 [NTFS 이름 요구 사항](/windows/win32/fileio/naming-a-file)을 따라야 합니다. 이러한 요구 사항을 따라야 하는 이유는 파일이 처리를 위해 BLOB 스토리지에서 로컬 NTFS 디스크로 복사될 수 있기 때문입니다.

## <a name="next-steps"></a>다음 단계

[Media Services 개요](media-services-overview.md)

## <a name="see-also"></a>참고 항목

[Media Services v2와 v3의 차이점](migrate-from-v2-to-v3.md)
