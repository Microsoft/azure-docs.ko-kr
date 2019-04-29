---
title: Azure StorSimple의 Azure Media Services 계정에 파일 업로드 | Microsoft Docs
description: 이 문서에서는 Azure StorSimple 데이터 관리자를 간략하게 설명합니다. 또한 이 문서는 StorSimple에서 데이터를 추출하고 Azure Media Services 계정에 자산으로 업로드하는 방법을 보여 주는 자습서로 연결됩니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 1dd09328-262b-43ef-8099-73241b49a925
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8a2972b8fbc31a3f945f40949128b3f37f2f4b77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544951"
---
# <a name="upload-files-into-an-azure-media-services-account-from-azure-storsimple"></a>Azure StorSimple의 Azure Media Services 계정에 파일 업로드  

이 문서에서는 Azure StorSimple 데이터 관리자를 간략하게 설명합니다. 또한 이 문서는 StorSimple에서 데이터를 추출하고 이 데이터를 AMS(Azure Media Services) 계정에 자산으로 업로드하는 방법을 보여 주는 자습서로 연결됩니다.

> 
> [!NOTE]
> 현재 Azure StorSimple 데이터 관리자는 비공개 미리 보기 상태입니다. 
> 

## <a name="overview"></a>개요

Media Services에서 자산에 디지털 파일을 업로드합니다. 자산에는 비디오, 오디오, 이미지, 미리 보기 컬렉션, 텍스트 트랙 및 선택 자막 파일(및 이러한 파일에 대한 메타데이터)이 포함될 수 있습니다. 파일이 업로드되면 이후 처리 및 스트리밍을 위해 콘텐츠가 클라우드에 안전하게 저장됩니다.

[Azure StorSimple](https://docs.microsoft.com/azure/storsimple/)은 클라우드 저장소를 온-프레미스 솔루션의 확장으로 사용하여 자동으로 온-프레미스 솔루션 및 클라우드 저장소에서 데이터를 계층화합니다. StorSimple 디바이스는 데이터를 클라우드로 보내기 전에 중복을 제거하고 압축하여 큰 파일을 클라우드에 효율적으로 전송하게 됩니다. [StorSimple 데이터 관리자](../../storsimple/storsimple-data-manager-overview.md) 서비스는 StorSimple에서 데이터를 추출하고 AMS 자산으로 제공할 수 있는 API를 제공합니다.

## <a name="get-started"></a>시작하기

1. 자산을 전송하려는 곳에 [Media Services 계정을 만듭니다](media-services-portal-create-account.md).
2. [StorSimple 데이터 관리자](../../storsimple/storsimple-data-manager-overview.md) 문서에 설명된 대로 데이터 관리자 미리 보기에 등록합니다.
3. StorSimple 데이터 관리자 계정을 만듭니다.
4. 데이터 변환 작업을 실행할 때 만들고 StorSimple 디바이스에서 데이터를 추출하고 AMS 계정에 자산으로 전송합니다. 

    저장소 큐는 작업이 실행되기 시작할 때 만들어집니다. 이 큐는 준비가 완료된 변환 Blob에 대한 메시지로 채워집니다. 이 큐의 이름은 작업 정의의 이름과 같습니다. 이 큐를 사용하여 자산이 준비되는 시기를 결정하고 원하는 Media Services 작업을 호출하여 여기서 실행할 수 있습니다. 예를 들어 이 큐를 사용하여 필요한 Media Services 코드가 있는 Azure Function을 트리거할 수 있습니다.

## <a name="see-also"></a>참고 항목

[데이터 관리자에서 작업 트리거를.NET SDK를 사용 합니다.](../../storsimple/storsimple-data-manager-dotnet-jobs.md)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계

이제 업로드된 자산을 인코딩할 수 있습니다. 자세한 내용은 [자산 인코딩](media-services-portal-encode.md)을 참조하세요.
