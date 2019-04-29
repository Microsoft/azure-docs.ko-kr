---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618073"
---
## <a name="premium-ssd"></a>프리미엄 SSD

Azure 프리미엄 SSD는 IO(입출력) 집약적 워크로드가 있는 VM(가상 머신)에 대기 시간이 짧은 고성능 디스크를 지원합니다. 프리미엄 스토리지 디스크의 속도와 성능을 활용하기 위해 기존 VM 디스크를 프리미엄 SSD로 마이그레이션할 수 있습니다. 프리미엄 SSD는 중요 업무용 프로덕션 애플리케이션에 적합합니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>표준 SSD

Azure 표준 SSD는 더 낮은 IOPS 수준에서 일관된 성능이 필요한 워크로드에 최적화된 비용 효율적 스토리지 옵션입니다. 특히 표준 SSD는 온-프레미스 HDD 솔루션에서 실행 중인 다양한 워크로드에 문제가 발생해서 클라우드로 전환하고자 하는 경우에 적합한 입문용 환경을 제공합니다. 표준 SSD는 HDD 디스크에 비해 더 나은 가용성, 일관성, 안정성 및 대기 시간을 제공합니다. 표준 SSD는 웹 서버, 낮은 IOPS 애플리케이션 서버, 가끔 사용되는 엔터프라이즈 애플리케이션 및 개발/테스트 워크로드에 적합합니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>표준 HDD

Azure 표준 HDD는 대기 시간에 민감하지 않은 워크로드를 실행하는 VM에 안정적이고 저렴한 디스크를 지원합니다. 또한 Blob, 테이블, 큐 및 파일을 지원합니다. 표준 스토리지를 사용하는 경우 데이터는 HDD(하드 디스크 드라이브)에 저장됩니다. VM을 사용할 때 표준 SSD 및 HDD 디스크는 개발/테스트 시나리오 및 비교적 중요하지 않은 워크로드에 사용할 수 있습니다. 표준 스토리지는 모든 Azure 지역에서 사용할 수 있습니다.

### <a name="disk-size"></a>디스크 크기
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>결제

관리 디스크를 사용할 때 적용되는 청구 고려 사항은 다음과 같습니다.

- 디스크 유형
- 관리 디스크 크기
- 스냅숏
- 아웃바운드 데이터 전송
- 트랜잭션 수

**관리 디스크 크기:** 관리 디스크의 요금은 프로비저닝된 크기에 따라 청구됩니다. Azure에서 프로비저닝된 크기(반올림)는 가장 가깝게 제안되는 디스크 크기에 매핑됩니다. 제안되는 디스크 크기에 대한 자세한 내용은 이전 표를 참조하세요. 각 디스크는 지원되는 프로비저닝된 디스크 크기 제안에 매핑되고 이에 따라 요금이 청구됩니다. 예를 들어 200GiB 표준 SSD를 프로비저닝한 경우 E15(256GiB)의 디스크 크기 제안에 매핑됩니다. 프로비전된 디스크에 대한 청구는 Premium Storage 제품의 월별 가격을 사용하여 시간당 비례합니다. 예를 들어 E10 디스크를 프로비전하고 20시간 후 삭제한 경우 20시간에 비례하여 E10 제품에 대해 청구됩니다. 이는 디스크에 기록되는 실제 데이터 양에 관계없이 적용됩니다.

**스냅숏**: 스냅숏 요금은 사용된 크기에 따라 청구됩니다. 예를 들어 프로비저닝된 용량이 64GiB이고 실제 사용된 데이터 크기가 10GiB인 관리 디스크의 스냅숏을 만들면 사용된 10GiB의 데이터 크기에 대해서만 스냅숏 요금이 청구됩니다.