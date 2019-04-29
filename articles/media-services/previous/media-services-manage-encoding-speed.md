---
title: Azure Media Services를 사용하여 인코딩의 속도 및 동시성 관리 | Microsoft Docs
description: 이 문서에서는 Azure Media Services를 사용하여 인코딩 작업/태스크의 속도 및 동시성을 관리하는 방법에 대해 간략하게 설명합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 676313f8-a158-4e3a-a99b-2c29a341ecc9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bcaadc8dd61899aff860ad246e30170c99ec0f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463755"
---
#  <a name="manage-speed-and-concurrency-of-your-encoding"></a>인코딩 속도 및 동시성 관리  

이 문서에서는 인코딩 작업/태스크의 속도 및 동시성 관리하는 방법에 대해 간략하게 설명합니다.

## <a name="overview"></a>개요

Media Services에서 **예약 단위 유형**은 미디어 처리 태스크를 처리하는 속도를 결정합니다. 예약 단위 유형 **S1**, **S2** 또는 **S3** 중에서 선택할 수 있습니다. 예를 들어 **S2** 예약 단위 유형을 사용하는 경우 **S1** 유형에 비해 동일한 인코딩 작업이 더 빠르게 실행됩니다. [인코딩 단위 크기 조정](media-services-scale-media-processing-overview.md) 항목에서는 여러 인코딩 속도를 선택할 때 적절한 속도를 결정할 수 있도록 하는 테이블을 보여 줍니다.

예약 단위 유형을 지정하는 것 외에도 계정에 **예약 단위**를 프로비전하도록 지정할 수 있습니다. 프로비전되는 예약 단위의 수에 따라 특정 계정에서 동시에 처리할 수 있는 미디어 작업의 수가 결정됩니다. 예를 들어 계정에 5개의 예약 단위가 있는 경우 처리할 작업이 있다면 5개의 미디어 작업이 동시에 실행됩니다. 나머지 작업은 큐에 대기하다가 실행 중인 작업이 완료되면 순차적으로 처리를 위해 선택됩니다. 계정에 프로비전된 예약 단위가 없는 경우에는 작업이 순차적으로 선택됩니다. 이 경우 한 작업 완료와 다음 작업 시작 사이의 대기 시간은 시스템의 리소스 가용성에 따라 다릅니다.

인코딩 단위의 크기를 조정하는 방법을 보여 주는 자세한 정보 및 예제는 [이](media-services-scale-media-processing-overview.md) 항목을 참조하세요.

## <a name="next-step"></a>다음 단계

[인코딩 단위 크기 조정](media-services-scale-media-processing-overview.md)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

