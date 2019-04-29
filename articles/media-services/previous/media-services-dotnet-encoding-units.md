---
title: 인코딩 단위를 추가하여 미디어 처리 크기 조정 - Azure | Microsoft Docs
description: .NET을 사용하여 인코딩 단위를 추가하는 방법 알아보기
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;milangada;
ms.openlocfilehash: 65b647a375ecbe70f7f29af5b09827f1c34dcd6f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217226"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>.NET SDK를 사용하여 인코딩 크기를 조정하는 방법
> [!div class="op_single_selector"]
> * [포털](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST (영문)](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>개요
> [!IMPORTANT]
> 미디어 처리 크기 조정에 대해 자세히 알아보려면 해당 [개요](media-services-scale-media-processing-overview.md)를 검토하세요.
> 
> 

.NET SDK를 사용하여 예약 단위 유형 및 인코딩 예약 단위 수를 변경하려면 다음을 수행합니다.

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>지원 티켓 열기

기본적으로 모든 Media Services 계정은 S2 또는 S3 MRU(미디어 예약 단위) 10개 또는 S1 MRU 25개, 주문형 스트리밍 예약 단위 최대 5개까지 확장할 수 있습니다. 지원 티켓을 열어 더 높은 한도를 요청할 수 있습니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

