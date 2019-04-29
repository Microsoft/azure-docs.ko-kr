---
title: REST를 사용하여 Azure Media Services 계정에 파일 업로드 | Microsoft Docs
description: 자산을 만들고 업로드하여 Media Services에 미디어 콘텐츠를 가져오는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: a241f66adecbab1d0b1462f379d3765d6c1de252
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466802"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>REST를 사용하여 Media Services 계정에 파일 업로드

Media Services에서 자산에 연결된 Blob 컨테이너에 디지털 파일을 업로드합니다. [자산](https://docs.microsoft.com/rest/api/media/operations/asset) 엔터티에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 자산의 컨테이너에 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

이 문서에서는 REST를 사용하여 로컬 파일을 업로드하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 항목에 설명된 단계를 완료하려면 다음을 수행해야 합니다.

- [자산 개념](assets-concept.md)을 검토합니다.
- [Azure Media Services REST API 호출에 대해 Postman 구성](media-rest-apis-with-postman.md)
    
    [Azure AD 토큰 가져오기](media-rest-apis-with-postman.md#get-azure-ad-token) 항목의 마지막 단계를 수행해야 합니다. 

## <a name="create-an-asset"></a>자산 만들기

이 섹션에서는 새 자산을 만드는 방법을 보여 줍니다.

1. **자산** -> **자산 만들기 또는 업데이트**를 선택합니다.
2. **보내기**를 누릅니다.

    ![자산 만들기](./media/upload-files/postman-create-asset.png)

새로 만든 자산에 대한 정보를 포함하는 **응답**이 표시됩니다.

## <a name="get-a-sas-url-with-read-write-permissions"></a>읽기/쓰기 권한을 사용하여 SAS URL을 가져오기 

이 섹션에서는 만든 자산에 대해 생성된 SAS URL을 가져오는 방법을 보여 줍니다. 읽기/쓰기 권한을 사용하여 SAS URL을 만든 후 디지털 파일을 자산 컨테이너에 업로드하는 데 사용할 수 있습니다.

1. **자산** -> **자산 URL 목록**을 선택합니다.
2. **보내기**를 누릅니다.

    ![파일 업로드](./media/upload-files/postman-create-sas-locator.png)

자산 URL에 대한 정보를 포함하는 **응답**이 표시됩니다. 첫 번째 URL을 복사한 후 파일을 업로드하는 데 사용합니다.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>업로드 URL을 사용하여 Blob Storage에 파일 업로드

Azure Storage API 또는 SDK(예: [Storage REST API](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md) 또는 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용합니다.

## <a name="next-steps"></a>다음 단계

[자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)
