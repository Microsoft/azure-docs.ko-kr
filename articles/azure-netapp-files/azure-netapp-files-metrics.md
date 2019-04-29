---
title: Azure NetApp Files에 대한 메트릭 | Microsoft Docs
description: Azure NetApp Files에 대한 메트릭을 설명합니다.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61084942"
---
# <a name="metrics-for-azure-netapp-files"></a>Azure NetApp Files에 대한 메트릭

Azure NetApp Files는 할당된 스토리지, 실제 스토리지 사용량, 볼륨 처리량, IOPS 및 대기 시간에 대한 메트릭을 제공합니다. 이러한 메트릭을 분석하여 NetApp 계정의 사용 패턴 및 볼륨 성능을 더 잘 파악할 수 있습니다.  

## <a name="capacity_pools"></a>용량 풀에 대한 사용량 메트릭

- ‘할당된 볼륨 풀 크기’  
    프로비전된 용량 풀의 크기(GiB)입니다.  
- ‘할당된 사용되는 볼륨 풀’  
    지정된 용량 풀의 총 볼륨 할당량(GiB)(즉, 용량 풀에 프로비전된 볼륨 크기의 총계)입니다. 볼륨을 만드는 동안 선택한 크기입니다.  
- ‘볼륨 풀 총 논리적 크기’  
    용량 풀의 볼륨에서 사용하는 논리 공간의 총계(GiB)입니다.  
- ‘볼륨 풀 총 스냅숏 크기’  
    스냅숏에서 사용하는 증분 논리 공간의 총계입니다.  

## <a name="volumes"></a>볼륨에 대한 사용량 메트릭

- ‘할당된 볼륨 크기’   
    프로비전된 볼륨 크기(할당량)(GiB)입니다.  
- ‘볼륨 논리적 크기’   
    볼륨에서 사용된 논리 공간의 총계(GiB)입니다. 이 크기에는 활성 파일 시스템 및 스냅숏에서 사용하는 논리 공간이 포함됩니다.  
- ‘볼륨 스냅숏 크기’   
    볼륨의 스냅숏에서 사용하는 증분 논리 공간입니다.  

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
