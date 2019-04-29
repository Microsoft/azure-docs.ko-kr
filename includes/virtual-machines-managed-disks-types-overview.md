---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6eae536bd19a2c0e5707d8e0b379774b6eb2707a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618059"
---
# <a name="what-disk-types-are-available-in-azure"></a>Azure에서 사용할 수 있는 디스크 유형

Azure 관리 디스크는 현재 네 개의 디스크 유형을 제공하고 있습니다. 이 중 세 개는 GA(일반 공급)되며, 나머지 하나는 현재 미리 보기에 있습니다. 이러한 네 개의 디스크 유형에는 각각 적절한 대상 고객 시나리오가 있습니다.

## <a name="disk-comparison"></a>디스크 비교

다음 표에서는 사용할 디스크를 결정하는 데 도움을 주기 위해 관리 디스크로 사용할 수 있는 울트라 SSD(반도체 드라이브)(미리 보기), 프리미엄 SSD, 표준 SSD 및 표준 HDD(하드 디스크 드라이브)를 비교하고 있습니다.

|   | 울트라 SSD(미리 보기)   | 프리미엄 SSD   | 표준 SSD   | 표준 HDD   |
|---------|---------|---------|---------|---------|
|디스크 유형   |SSD   |SSD   |SSD   |HDD   |
|시나리오   |IO 집약적 워크로드 - SAP HANA, 최상위 계층 데이터베이스(예: SQL, Oracle) 및 다른 트랜잭션 집약적 워크로드   |프로덕션 및 성능이 중요한 워크로드   |웹 서버, 적게 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트   |백업, 중요하지 않음, 가끔 액세스   |
|디스크 크기   |65,536GiB(기비바이트)(미리 보기)   |32,767GiB    |32,767GiB   |32,767GiB   |
|최대 처리량   |2,000MiB/s(미리 보기)   |900 초   |750 초   |500 초   |
|최대 IOPS   |160,000(미리 보기)   |20,000   |6,000   |2,000   |

## <a name="ultra-ssd-preview"></a>울트라 SSD(미리 보기)

Azure 울트라 SSD(미리 보기)는 Azure IaaS VM에 대한 높은 처리량, 높은 IOPS 및 일관된 짧은 대기 시간 디스크 스토리지를 제공합니다. 울트라 SSD의 몇 가지 추가 이점에는 가상 머신을 다시 시작하지 않고도 워크로드와 함께 디스크의 성능을 동적으로 변경할 수 있다는 것이 포함됩니다. 울트라 SSD는 SAP HANA, 최상위 계층 데이터베이스 및 트랜잭션 집약적 워크로드와 같은 데이터 집약적 워크로드에 적합합니다. 울트라 SSD는 데이터 디스크로만 사용할 수 있습니다. 프리미엄 SSD는 OS 디스크로 사용하는 것이 좋습니다.

### <a name="performance"></a>성능

울트라 SSD를 프로비저닝할 때 디스크의 용량과 성능을 독립적으로 구성할 수 있습니다. 울트라 SSD는 4GiB~64TiB의 몇 가지 고정된 크기로 제공되며, IOPS와 처리량을 독립적으로 구성할 수 있는 유연한 성능 구성 모델을 갖추고 있습니다.

Ultra SSD의 일부 키 기능은 다음과 같습니다.

- 디스크 용량: 울트라 SSD의 용량은 4GiB에서 최대 64TiB입니다.
- 디스크 IOPS: 울트라 SSD는 300IOPS/GiB의 IOPS 제한을 지원하며, 디스크당 최대 160K IOPS입니다. 프로비전한 IOPS를 달성하려면 선택한 디스크 IOPS가 VM IOPS보다 작은지 확인합니다. 최소 디스크 IOPS는 100IOPS입니다.
- 디스크 처리량: 울트라 SSD를 사용하는 경우 단일 디스크의 처리량 한도는 프로비저닝된 IOPS당 256KiB/s이며, 디스크당 최대 2,000MBps입니다(MBps = 초당 10^6바이트). 최소 디스크 처리량은 1MiB입니다.

### <a name="disk-size"></a>디스크 크기

|디스크 크기(GiB)  |IOPS 용량  |처리량 용량(MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2,000         |
|64     |19,200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1,024-65,536(1TiB의 단위로 증가하는 이 범위의 크기)     |160,000         |2,000         |

### <a name="preview-scope-and-limitations"></a>미리 보기 범위 및 제한 사항

미리 보기 동안 울트라 SSD는 다음과 같습니다.

- 미국 동부 2의 단일 가용성 영역에서 지원됨  
- 가용성 영역에서만 사용할 수 있음(영역 외부의 가용성 집합 및 단일 VM 배포에는 울트라 디스크를 연결하는 기능이 없음)
- ES/DS v3 VM에서만 지원됨
- 데이터 디스크로만 사용 가능하며 4k 물리적 섹터 크기만 지원함  
- 빈 디스크로만 만들 수 있음  
- 현재 Azure Resource Manager 템플릿, CLI 및 Python SDK를 통해서만 배포할 수 있습니다.
- 디스크 스냅숏, VM 이미지, 가용성 집합, 가상 머신 확장 집합 및 Azure 디스크 암호화는 아직 지원하지 않습니다.
- Azure Backup 또는 Azure Site Recovery와의 통합은 아직 지원하지 않습니다.
-  [대부분의 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)와 마찬가지로, 이 기능은 GA(일반 공급) 이후에만 프로덕션 워크로드에서 사용할 수 있습니다.
