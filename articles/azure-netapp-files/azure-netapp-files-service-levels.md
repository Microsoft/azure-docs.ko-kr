---
title: Azure NetApp Files의 서비스 수준 | Microsoft Docs
description: Azure NetApp Files의 서비스 수준에 대한 처리량 성능을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: c2086eb0c5529d8e570a545e35fc716f70c7541f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60691056"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files에 대한 서비스 수준
Azure NetApp Files는 두 가지 서비스 수준(“프리미엄” 및 “표준”)을 지원합니다. 

## <a name="Premium"></a>Premium Storage

‘Premium’ Storage는 TiB당 최대 64MiB의 처리량을 제공합니다. 처리량 성능은 볼륨 할당량을 기준으로 인덱싱됩니다. 예를 들어 실제 사용량과 관계없이 프로비전된 할당량이 2TiB인 Premium Storage 볼륨의 처리량은 128MiB/초입니다.

## <a name="Standard"></a>Standard Storage

‘Standard’ Storage는 TiB당 최대 16MiB의 처리량을 제공합니다. 처리량 성능은 볼륨 할당량을 기준으로 인덱싱됩니다. 예를 들어 실제 사용량과 관계없이 프로비전된 할당량이 2TiB인 Standard Storage 볼륨의 처리량은 32MiB/초입니다.

## <a name="next-steps"></a>다음 단계

- 여러 서비스 수준의 가격은 [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)를 참조하세요.
- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
