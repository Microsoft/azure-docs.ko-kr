---
title: Azure NetApp Files에 대한 비용 모델 | Microsoft Docs
description: 서비스에서 비용을 관리 하는 Azure NetApp Files에 대한 비용 모델을 설명 합니다.
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
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995109"
---
# <a name="cost-model-for-azure-netapp-files"></a>Azure NetApp Files 비용 모델 

Azure NetApp Files에 대한 비용 모델을 이해 하면 서비스에서 비용을 관리 하는 데 도움이 됩니다.

## <a name="calculation-of-capacity-consumption"></a>용량 소비 계산

Azure NetApp Files는 프로 비전 된 저장소 용량에 대해 요금이 청구 됩니다.  프로 비전 된 용량은 용량 풀을 만들어 할당 합니다.  용량 풀은 시간당 증분 단위로 $/provisioned-GiB/month를 기준으로 요금이 청구 됩니다. 단일 용량 풀의 최소 크기는 TiB 4 이며 용량 풀은 나중에 TiB 증분으로 확장할 수 있습니다. 볼륨은 용량 풀 내에서 만들어집니다.  각 볼륨에는 풀에서 프로 비전 된 용량에서 감소 하는 할당량이 할당 됩니다. 볼륨에 할당할 수 있는 할당량은 최소 100 GiB에서 최대 100 TiB까지 범위를 지정 합니다.  

활성 볼륨의 경우 할당량에 대한 용량 소비는 논리적 (유효) 용량을 기반으로 합니다.

볼륨의 실제 용량 소비가 저장소 할당량을 초과 하는 경우 볼륨을 계속 늘릴 수 있습니다. 실제 볼륨 크기가 시스템 제한 (100 TiB) 보다 작은 경우에만 쓰기를 허용 합니다.  

프로 비전 된 용량에 대한 용량 풀에서 사용 되는 총 용량은 할당 된 할당량 또는 풀 내 모든 볼륨의 실제 소비 중 더 큰 합계입니다. 

   ![총 사용 용량 계산](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

아래 다이어그램에서는 이러한 개념을 보여 줍니다.  
* 프로 비전 된 용량의 4 TiB 용량 풀이 있습니다.  풀에는 세 개의 볼륨이 포함 되어 있습니다.  
    * 볼륨 1에는 2 TiB의 할당량이 할당 되며 800 GiB가 사용 됩니다.  
    * 볼륨 2는 1 TiB의 할당량을 할당 하 고 100 GiB의 소비를 가집니다.  
    * 볼륨 3에는 500 GiB의 할당량이 할당 되지만 800 GiB (초과분)가 있습니다.  
* 용량 풀은 4 TiB 용량 (프로 비전 된 용량)에 대해 측정 됩니다.  
    3.8 TiB의 용량이 소비 됩니다 (2 TiB 및 1 TiB, 볼륨 1과 2의 할당량 및 800 GiB, 볼륨 3에 대한 실제 소비). 및 200 GiB의 용량이 남아 있습니다.

   ![볼륨이 3 개인 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>용량 소비의 초과분  

풀의 사용 된 총 용량이 프로 비전 된 용량을 초과 하면 데이터 쓰기는 계속 허용 됩니다.  유예 기간 (1 시간) 후에 사용 된 풀 용량이 여전히 프로 비전 된 용량을 초과 하는 경우 프로 비전 된 용량이 총 사용 용량 보다 클 때까지 풀 크기가 자동으로 1 TiB 증가 됩니다.  예를 들어 위의 그림에서 볼륨 3이 계속 증가 하 고 실제 소비가 1.2 TiB에 도달한 경우 유예 기간이 지나면 풀 크기가 자동으로 5 TiB으로 조정 됩니다.  결과적으로 프로 비전 된 풀 용량 (5 TiB)이 사용 된 용량 (4.2 TiB)을 초과 합니다.  

## <a name="manual-changes-of-the-pool-size"></a>풀 크기의 수동 변경 내용  

수동으로 풀 크기를 늘리거나 줄일 수 있습니다. 그러나 다음과 같은 제약 조건이 적용 됩니다.
* 서비스 최소 및 최대 제한  
    [리소스 제한](azure-netapp-files-resource-limits.md)에 대한 문서를 참조 하세요.
* 최초 4-TiB 최소 구매 후 1 TiB 증분
* 1 시간의 최소 청구 증가값
* 프로 비전 된 풀 크기를 풀에서 사용 된 총 용량 보다 작게 낮출 수 없습니다.

## <a name="behavior-of-maximum-size-pool-overage"></a>최대 크기 풀 초과분 동작   

만들거나 크기를 조정할 수 있는 용량 풀의 최대 크기는 500 TiB입니다.  용량 풀에서 사용 된 총 용량이 500 TiB을 초과 하면 다음 상황이 발생 합니다.
* 볼륨이 시스템의 최대 100 TiB 미만인 경우에도 데이터 쓰기가 허용 됩니다.
* 1 시간 유예 기간이 지나면 풀 프로 비전 된 용량이 총 사용 용량을 초과 하기 전까지 풀은 TiB 증분 단위로 자동으로 크기가 조정 됩니다.
* 500 TiB을 초과 하는 추가 프로 비전 및 청구 된 풀 용량은 볼륨 할당량을 할당 하는 데 사용할 수 없습니다. 또한 성능 QoS 제한을 확장 하는 데 사용할 수 없습니다.  
    성능 제한 및 QoS 크기 조정에 대한 [서비스 수준](azure-netapp-files-service-levels.md) 을 참조 하세요.

아래 다이어그램에서는 이러한 개념을 보여 줍니다.
* Premium storage 계층이 있는 용량 풀 및 500 TiB 용량이 있습니다. 풀에 9 개의 볼륨이 포함 되어 있습니다.
    * 볼륨 1 ~ 8에는 각각 60 TiB의 할당량이 할당 됩니다.  사용 된 총 용량은 480 TiB입니다.  
        각 볼륨에는 처리량이 3.75 GiB/s 인 QoS 제한이 있습니다 (60 TiB * 64 MiB/s).  
    * 볼륨 9에는 20 TiB의 할당량이 할당 됩니다.  
        볼륨 9의 QoS 제한은 처리량이 1.25 GiB/s (20 TiB * 64 MiB/s)입니다.
* 볼륨 9는 초과분 시나리오입니다. 실제 소비의 25 TiB 있습니다.  
    * 1 시간 유예 기간 후에는 용량 풀의 크기가 505 TiB으로 조정 됩니다.  
        즉, 사용 되는 총 용량 = 8 * 60-볼륨 1부터 8 까지의 TiB 할당량 및 볼륨 9에 대한 실제 소비의 25 TiB입니다.
    * 청구 된 용량은 505 TiB입니다.
    * 볼륨 9에 대한 볼륨 할당량을 늘릴 수 없습니다 (풀에 할당 된 총 할당량이 500 TiB을 초과 하지 않을 수 있음).
    * 풀에 대한 총 QoS는 여전히 500 TiB을 기반으로 하기 때문에 추가 QoS 처리량을 할당 하지 못할 수 있습니다.

   ![9 개 볼륨이 있는 용량 풀](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>스냅숏의 용량 소비 

Azure NetApp Files에 있는 스냅숏의 용량 소비는 부모 볼륨의 할당량을 기준으로 청구 됩니다.  그 결과, 볼륨이 속한 용량 풀과 동일한 청구 요금을 공유 합니다.  그러나 활성 볼륨과 달리 스냅숏 소비는 소비 된 증분 용량을 기준으로 측정 됩니다.  Azure NetApp Files 스냅숏은 본질적으로 차등입니다. 데이터의 변경 률에 따라 스냅숏은 활성 볼륨의 논리 용량 보다 훨씬 더 많은 용량을 사용 하는 경우가 많습니다. 예를 들어 10 개의 GiB의 차등 데이터만 포함 하는 500-GiB 볼륨의 스냅숏이 있다고 가정 합니다. 해당 스냅숏에 대한 볼륨 할당량에 대해 부과 되는 용량은 500 GiB가 아닌 10 GiB입니다. 

## <a name="next-steps"></a>다음 단계

* [가격 책정 페이지 Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
