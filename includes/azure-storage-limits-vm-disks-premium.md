---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: e7e57c6a821731874dcb1d99a3133b6ede1da26e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60386338"
---
**프리미엄 관리 되지 않는 가상 머신 디스크: 계정 별로 제한**

| 리소스 | 기본 제한 |
| --- | --- |
| 계정당 총 디스크 용량 |35TB |
| 계정당 총 스냅숏 용량 |10TB |
| 최대 대역폭 (수신 + 송신) 계정당<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*수신* 저장소 계정에 전송 되는 요청에서 모든 데이터를 가리킵니다. *송신* 저장소 계정에서 수신 되는 응답에서 모든 데이터를 가리킵니다.

**프리미엄 관리 되지 않는 가상 머신 디스크: 디스크 당 제한**

| Premium Storage 디스크 유형 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 디스크 크기 |128GiB |512GiB |1,024 giB (1 TB) |2,048은 giB (2TB)|(4TB) 4095 giB|
| 디스크당 최대 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 디스크당 최대 처리량 |100MB/초 | 150MB/초 |200MB/초 |250MB/초 |250MB/초 |
| 저장소 계정당 디스크의 최대 수 |280 |70 |35 | 17 | 8 |

**프리미엄 관리 되지 않는 가상 머신 디스크: VM 당 제한**

| 리소스 | 기본 제한 |
| --- | --- |
| VM 당 최대 IOPS |GS5 VM에서 80,000 IOPS |
| VM 당 최대 처리량 |GS5 VM을 사용 하 여 초당 2,000MB |

