---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "68669753"
---
Azure 가상 컴퓨터에 많은 데이터 디스크를 연결할 수 있습니다. VM의 데이터 디스크에 대한 확장성 및 성능 목표에 따라 성능 및 용량 요구 사항을 충족 하는 데 필요한 디스크의 수와 유형을 결정할 수 있습니다.

> [!IMPORTANT]
> 최적의 성능을 얻기 위해 가상 머신에 연결되어 자주 활용되는 디스크의 수를 제한하여 가능한 제한을 방지합니다. 연결 된 모든 디스크가 동시에 과도 하 게 사용 되지 않는 경우에는 가상 컴퓨터에서 더 많은 수의 디스크를 지원할 수 있습니다.

**Azure managed disks의 경우:**

다음 표에서는 구독 당 지역별 리소스 수의 기본 및 최대 제한을 보여 줍니다. 리소스 그룹당 Managed Disks, 스냅숏 및 이미지 수에는 제한이 없습니다.  

> | 리소스 | 기본 제한  | 최대 제한 |
> | --- | --- | --- |
> | 표준 관리 디스크 | 50,000 | 50,000 |
> | 표준 SSD 관리 디스크 | 50,000 | 50,000 |
> | 프리미엄 managed disks | 50,000 | 50,000 |
> | Standard_LRS 스냅숏 | 50,000 | 50,000 |
> | Standard_ZRS 스냅숏 | 50,000 | 50,000 |
> | 관리 이미지 | 50,000 | 50,000 |

* **Standard storage 계정:** Standard storage 계정의 최대 총 요청 율은 2만 IOPS입니다. 표준 저장소 계정의 모든 가상 머신 디스크에 대한 총 IOPS는이 제한을 초과할 수 없습니다.
  
    요청 률 제한을 기반으로 단일 표준 저장소 계정에서 지 원하는 매우 많이 사용 되는 디스크 수를 대략적으로 계산할 수 있습니다. 예를 들어 기본 계층 VM의 경우 과도 하 게 사용 되는 디스크의 최대 수는 약 66입니다 .이는 디스크당 20000/300 IOPS입니다. 표준 계층 VM에 대해 매우 많이 사용 되는 디스크의 최대 수는 약 40 (디스크당 20000/500 IOPS)입니다. 

* **Premium storage 계정:** Premium storage 계정의 최대 총 처리량 속도는 50 Gbps입니다. 모든 VM 디스크의 총 처리량은 이 제한을 초과할 수 없습니다.

