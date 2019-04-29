---
title: Azure CDN 청구 이해 | Microsoft Docs
description: 이 FAQ에서는 Azure CDN 청구의 원리를 설명합니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: magattus
ms.openlocfilehash: af8e57f39b5b83b1d1be09c29d8b6eb5d49c7b6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782063"
---
# <a name="understanding-azure-cdn-billing"></a>Azure CDN 청구 이해

이 FAQ에서는 CDN(Content Delivery Network)이 호스트하는 콘텐츠의 청구 구조에 대해 설명합니다.

## <a name="what-is-a-billing-region"></a>청구 영역이란?
청구 영역은 Azure CDN에서 제공하는 개체에 적용할 요율을 결정하는 데 사용되는 지리적 영역입니다. 현재 청구 영역 및 해당 영역은 다음과 같습니다.

- 영역 1: 북아메리카, 유럽, 중동 및 아프리카

- 영역 2: 아시아 태평양(일본 포함)

- 영역 3: 남미

- 영역 4: 오스트레일리아 및 뉴질랜드

- 영역 5: 인도

POP(point-of-presence) 지역에 대한 정보는 [지역별 Azure CDN POP 위치](https://docs.microsoft.com/azure/cdn/cdn-pop-locations)를 참조하세요. 예를 들어 멕시코의 POP는 북아메리카 지역에 있고 따라서 영역 1에 포함됩니다. 

Azure CDN 가격 책정에 대한 정보는 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

## <a name="how-are-delivery-charges-calculated-by-region"></a>지역별 배달 요금 계산 방법
Azure CDN 청구 지역은 최종 사용자에게 콘텐츠를 배달하는 원본 서버의 위치를 기반으로 합니다. 클라이언트의 대상(실제 위치)은 청구 지역으로 간주되지 않습니다.

예를 들어 멕시코에 위치한 사용자가 요청을 발급하고 이 요청이 피어링 또는 트래픽 상황으로 인해 미국 POP에 있는 서버를 통해 처리되는 경우 청구 지역은 미국이 됩니다.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>청구 가능 Azure CDN 트랜잭션이란?
CDN에서 종료되는 모든 HTTP(S) 요청은 청구 가능 이벤트로, 모든 응답 형식(성공, 실패 또는 기타)을 포함합니다. 그러나 응답에 따라 트래픽 양이 달라질 수 있습니다. 예를 들어 *304 수정되지 않음* 및 헤더에만 적용되는 기타 응답은 작은 헤더 응답이기 때문에 트래픽을 거의 생성하지 않습니다. 마찬가지로, 오류 응답(예: *404 찾을 수 없음*)은 청구 가능하지만 응답 페이로드가 아주 작기 때문에 적은 비용이 발생합니다.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Azure CDN 사용과 관련된 기타 Azure 비용으로 어떤 것이 있나요?
Azure CDN을 사용하면 개체의 원본으로 사용되는 서비스에 대한 사용 요금이 발생합니다. 이러한 비용은 일반적으로 전체 CDN 사용 비용의 일부입니다.

콘텐츠 원본으로 Azure Blob Storage를 사용하는 경우 다음과 같은 캐시 채우기에 대한 스토리지 비용도 발생합니다.

- 사용된 실제 GB: 원본 개체의 실제 스토리지입니다.

- 전송량(GB): CDN 캐시를 채우기 위해 전송된 데이터의 양입니다.

- 트랜잭션: 캐시를 채우는 데 필요합니다.

Azure Storage 청구에 대한 자세한 내용은 [Azure Storage 청구 이해 - 대역폭, 트랜잭션 및 용량](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)을 참조하세요.

*호스팅된 서비스 배달*을 사용하는 경우 다음과 같이 요금이 발생합니다.

- Azure 컴퓨팅 시간: 원점으로 작동하는 컴퓨팅 인스턴스입니다.

- Azure 컴퓨팅 전송: Azure CDN 캐시를 채우기 위해 컴퓨팅 인스턴스에서 전송되는 데이터입니다.

클라이언트에서 바이트 범위 요청을 사용하는 경우(원본 서비스에 관계없이) 다음과 같은 고려 사항이 적용됩니다.

- *바이트 범위 요청*은 CDN에서 청구 가능 트랜잭션입니다. 클라이언트에서 바이트 범위 요청을 발급하는 경우 이 요청의 대상은 개체의 하위 집합(범위)입니다. CDN은 요청된 콘텐츠의 일부만 사용하여 응답합니다. 이 부분 응답은 청구 가능 트랜잭션이며 전송량은 범위 응답의 크기(더하기 헤더)로 제한됩니다.

- 개체의 일부분에만 해당하는(바이트 범위 헤더를 지정하여) 요청이 도착하면 CDN이 전체 개체를 캐시에 페치할 수도 있습니다. 결과적으로, CDN의 청구 가능 트랜잭션이 응답의 일부를 대상으로 하더라도 원본의 청구 가능 트랜잭션에 개체의 전체 크기가 포함될 수 있습니다.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>캐시를 지원하기 위해 얼마나 많은 전송 작업이 발생합니까?
CDN POP는 캐시를 채워야 할 때마다 캐시되는 개체의 원본에 요청을 보냅니다. 결과적으로, 캐시가 누락될 때마다 원본에서 청구 가능 트랜잭션이 발생합니다. 캐시 누락 수는 다양한 요인에 따라 달라집니다.

- 콘텐츠의 캐시 가능 수준: 콘텐츠의 TTL(Time to Live)/만료 값이 높고 액세스 빈도가 높아 캐시에서 자주 사용되는 경우 부하의 대부분이 CDN에서 처리됩니다. 캐시 누락이든 개체 새고 고침이든, 일반적으로 캐시 적중 비율이 90%를 넘을 때, 다시 말해서 원본으로 돌아가야 하는 클라이언트 요청 비율이 10% 미만일 때 우수하다고 말합니다.

- 개체를 로드해야 하는 노드 수: 노드가 원본에서 개체를 로드할 때마다 청구 가능한 트랜잭션이 발생합니다. 따라서 글로벌 콘텐츠에서 더 많은 청구 가능 트랜잭션이 발생합니다(더 많은 노드에서 액세스하므로).

- TTL 영향: 개체의 TTL이 높다는 것은 원본에서 개체를 가져오는 빈도를 낮춰야 한다는 뜻입니다. 또한 브라우저 같은 클라이언트에서 개체를 더 오래 캐시할 수 있으며, 따라서 CDN의 트랜잭션을 줄일 수 있다는 의미입니다.

## <a name="how-do-i-manage-my-costs-most-effectively"></a>비용을 효과적으로 관리하는 방법
콘텐츠에서 TTL을 최대한 길게 설정합니다. 
