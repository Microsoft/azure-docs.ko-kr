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
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75832576"
---
# <a name="service-levels-for-azure-netapp-files"></a>Azure NetApp Files에 대한 서비스 수준
서비스 수준은 용량 풀의 특성입니다. 서비스 수준은 볼륨에 할당 된 할당량을 기준으로 용량 풀의 볼륨에 허용 되는 최대 처리량으로 정의 되 고 구분 됩니다.

## <a name="supported-service-levels"></a>지원 되는 서비스 수준

Azure NetApp Files은 *Ultra*, *Premium*및 *Standard*의 세 가지 서비스 수준을 지원 합니다. 

* <a name="Ultra"></a>Ultra storage

    Ultra storage 계층은 할당 된 볼륨 할당량의 TiB 1 개에 대한 최대 128 MiB/s의 처리량을 제공 합니다. 

* <a name="Premium"></a>Premium Storage

    Premium storage 계층은 할당 된 볼륨 할당량의 TiB 1 개에 대한 최대 64 MiB/s의 처리량을 제공 합니다. 

* <a name="Standard"></a>Standard Storage

    표준 저장소 계층은 할당 된 볼륨 할당량의 TiB 1 개에 대한 최대 16 개의 MiB/s를 제공 합니다.

## <a name="throughput-limits"></a>처리량 한도

볼륨의 처리량 제한은 다음 요소의 조합에 따라 결정 됩니다.
* 볼륨이 속한 용량 풀의 서비스 수준
* 볼륨에 할당 된 할당량  

이 개념은 아래 다이어그램에 나와 있습니다.

![서비스 수준 그림](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

위의 예제 1에서 TiB 할당 된 Premium storage 계층이 있는 용량 풀의 볼륨에는 처리량 제한인 128 MiB/s (2 TiB * 64 MiB/s)가 할당 됩니다. 이 시나리오는 용량 풀 크기나 실제 볼륨 소비에 관계 없이 적용 됩니다.

위의 예 2에서는 GiB 할당 된 Premium storage 계층이 있는 용량 풀의 볼륨에 100의 처리량 제한이 6.25 MiB/s (0.09765625 TiB * 64 MiB/s)로 할당 됩니다. 이 시나리오는 용량 풀 크기나 실제 볼륨 소비에 관계 없이 적용 됩니다.

## <a name="next-steps"></a>다음 단계

- 여러 서비스 수준의 가격은 [Azure NetApp Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/netapp/)를 참조하세요.
- 용량 풀의 용량 소비 계산에 대 [한 Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md) 을 참조 하세요. 
- [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
- [Azure NetApp Files Service Level Agreement(서비스 수준 약정) (SLA)를](https://azure.microsoft.com/support/legal/sla/netapp/) 참조 하세요.