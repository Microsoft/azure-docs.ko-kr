---
title: Azure Media Services를 사용하여 다중 비트 전송률 스트림을 만들 때 온-프레미스 인코더 구성 | Microsoft Docs
description: 이 토픽에서는 추가 처리를 위해 라이브 이벤트를 캡처하고 단일 비트 전송률 라이브 스트림을 AMS 채널(라이브 인코딩 사용)로 보내는 데 사용할 수 있는 온-프레미스 라이브 인코더를 나열합니다. 토픽은 나열된 인코더의 구성 방법을 보여 주는 자습서에 연결합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0ec6f046-0841-4673-9057-883bdbc30d5c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 77f2d433b32d180c7b3a819af96a50b721c2087f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89263436"
---
# <a name="how-to-configure-on-premises-encoders-when-using-azure-media-services-to-create-multi-bitrate-streams"></a>Azure Media Services를 사용할 때 온-프레미스 인코더를 구성하여 다중 비트 전송률 스트림을 만드는 방법

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

이 토픽에서는 추가 처리를 위해 라이브 이벤트를 캡처하고 단일 비트 전송률 라이브 스트림을 AMS 채널(라이브 인코딩 사용)로 보내는 데 사용할 수 있는 온-프레미스 라이브 인코더를 나열합니다. 또한 나열된 인코더의 구성 방법을 보여 주는 자습서에 연결합니다.

> [!NOTE]
> RTMP를 통해 스트리밍할 때 방화벽 및/또는 프록시 설정을 검사하여 아웃바운드 TCP 포트 1935 및 1936이 열려 있는지 확인합니다.

## <a name="haivision-kb-encoder"></a>Haivision KB Encoder
단일 비트 전송률 라이브 스트림을 AMS 채널로 전송하도록 [Haivision KB Encoder](https://www.haivision.com/products/kb-series/) 인코더를 구성하는 방법에 대한 정보는 [Haivision KB Encoder 구성](media-services-configure-kb-live-encoder.md)을 참조하세요.

## <a name="telestream-wirecast"></a>Telestream Wirecast
단일 비트 전송률 라이브 스트림을 AMS 채널로 전송하도록 [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) 인코더를 구성하는 방법에 대한 정보는 [Wirecast 구성](media-services-configure-wirecast-live-encoder.md)을 참조하세요.

## <a name="elemental-live"></a>Elemental Live
자세한 내용은 [Elemental Live](https://www.elemental.com/products/aws-elemental-appliances-software/#elemental-live)를 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>다음 단계

[Azure Media Services를 사용하여 다중 비트 전송률 스트림을 만드는 라이브 스트리밍](media-services-manage-live-encoder-enabled-channels.md)

