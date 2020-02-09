---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: Azure NetApp Files 리소스에 대한 제한과 리소스 제한 증가를 요청 하는 방법을 설명 합니다.
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
ms.date: 12/09/2019
ms.author: b-juche
ms.openlocfilehash: 6fcea0aaecb860e07c2066877494c05b51f43ca4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976250"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 한계

다음 표에서는 Azure NetApp Files에 대한 리소스 제한을 설명 합니다.

|  리소스  |  기본 제한  |  지원 요청을 통해 조정 가능  |
|----------------|---------------------|--------------------------------------|
|  Azure 지역별 NetApp 계정 수   |  10    |  yes   |
|  NetApp 계정 당 용량 풀 수   |    25     |   yes   |
|  용량 풀 당 볼륨 수     |    500   |    yes     |
|  볼륨당 스냅숏 수       |    255     |    아닙니다.        |
|  Azure Virtual Network Azure NetApp Files (Microsoft NetApp/볼륨)에 위임 된 서브넷 수    |   1   |    아닙니다.    |
|  Azure NetApp Files에 액세스할 수 있는 VNet (피어 링 Vnet 포함)의 Ip 수   |    1000   |    yes   |
|  단일 용량 풀의 최소 크기   |  4TiB     |    아닙니다.  |
|  단일 용량 풀의 최대 크기    |  500TiB   |   아닙니다.   |
|  단일 볼륨의 최소 크기    |    100GiB    |    아닙니다.    |
|  단일 볼륨의 최대 크기     |    100 TiB    |    아닙니다.    |
|  볼륨당 최대 파일 수 ([maxfiles](#maxfiles))     |    1억    |    yes    |    
|  단일 파일의 최대 크기     |    16TiB    |    아닙니다.    |    

## Maxfiles 제한<a name="maxfiles"></a> 

Azure NetApp Files 볼륨에는 *maxfiles*라는 제한이 있습니다. Maxfiles 한도는 볼륨에 포함 될 수 있는 파일 수입니다. Azure NetApp Files 볼륨의 maxfiles 제한은 볼륨의 크기 (할당량)를 기준으로 인덱싱됩니다. 볼륨에 대한 maxfiles 제한은 프로 비전 된 볼륨 크기의 TiB 2000만 파일의 비율을 늘리거나 줄입니다. 

서비스는 프로 비전 된 크기에 따라 볼륨의 maxfiles 제한을 동적으로 조정 합니다. 예를 들어 처음에 크기가 1 TiB 구성 된 볼륨은 maxfiles 한도가 2000만입니다. 이후에 볼륨의 크기를 변경 하면 다음 규칙에 따라 maxfiles 한도가 자동으로 readjustment 됩니다. 

|    볼륨 크기 (할당량)     |  Maxfiles 한도의 자동 readjustment    |
|----------------------------|-------------------|
|    < 1 TiB                 |    2000만     |
|    > = 1 TiB < 2 TiB    |    4000만     |
|    > = 2 TiB 하지만 < 3 TiB    |    6천만     |
|    > = 3 TiB < 4 TiB    |    8000만     |
|    > = 4 TiB                |    1억    |

모든 볼륨 크기에 대해 [지원 요청](#limit_increase) 을 시작 하 여 maxfiles 제한을 1억 이상 늘릴 수 있습니다.

## 요청 제한 증가<a name="limit_increase"></a> 

Azure 지원 요청을 만들어 위의 표에서 조정 가능한 제한을 늘릴 수 있습니다. 

Azure Portal 탐색 평면에서: 

1. **도움말 + 지원**을 클릭 합니다.
2. **+ 새 지원 요청**을 클릭 합니다.
3. 기본 사항 탭에서 다음 정보를 제공 합니다. 
    1. 문제 유형: **서비스 및 구독 제한 (할당량)** 을 선택 합니다.
    2. 구독: 할당량이 증가 해야 하는 리소스에 대한 구독을 선택 합니다.
    3. 할당량 유형: **저장소: Azure NetApp Files 제한**을 선택 합니다.
    4. **다음: 솔루션**을 클릭 합니다.
4. 세부 정보 탭에서 다음을 수행 합니다.
    1. 설명 상자에 해당 하는 리소스 종류에 대한 다음 정보를 제공 합니다.

        |  리소스  |    부모 리소스      |    요청 된 새 제한     |    할당량 증가 이유       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  계좌 |  *구독 ID*   |  *요청 된 새 최대 **계정** 번호*    |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |
        |  풀    |  *구독 ID, 계정 URI*  |  *요청 된 새 최대 **풀** 번호*   |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |
        |  볼륨  |  *구독 ID, 계정 URI, 풀 URI*   |  *요청 된 새 최대 **볼륨** 번호*     |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |
        |  Maxfiles  |  *구독 ID, 계정 URI, 풀 URI, 볼륨 URI*   |  *요청 된 새 최대 **maxfiles** 수*     |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |    

    2. 적절 한 지원 방법을 지정 하 고 계약 정보를 제공 합니다.

    3. **다음: 검토 + 만들기** 를 클릭 하 여 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files에 대한 비용 모델](azure-netapp-files-cost-model.md)
