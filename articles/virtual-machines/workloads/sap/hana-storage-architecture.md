---
title: Azure(대규모 인스턴스)의 SAP HANA에 대한 스토리지 아키텍처 | Microsoft Docs
description: Azure(대규모 인스턴스)에서 SAP HANA를 배포하는 방법에 대한 스토리지 아키텍처입니다.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff0a674e4f07dd38eaab2ffdbafe1f39e44e6ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676856"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>SAP HANA(대규모 인스턴스) 스토리지 아키텍처

Azure의 SAP HANA(대규모 인스턴스)의 스토리지 레이아웃은 SAP 권장 지침에 따라 클래식 배포 모델에서 SAP HANA를 통해 구성됩니다. 이 지침은 [SAP HANA 스토리지 요구 사항](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) 백서에 나와 있습니다.

유형 I 클래스의 HANA 대규모 인스턴스는 메모리 볼륨으로 스토리지 볼륨의 4배를 제공합니다. 유형 II 클래스의 HANA 대규모 인스턴스 디바이스의 경우 스토리지는 4배를 초과할 수 없습니다. 장치에는 HANA 트랜잭션 로그 백업을 저장하기 위한 볼륨이 제공됩니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 설치 및 구성](hana-installation.md)을 참조하세요.

스토리지 할당이라는 면에서 다음 표를 참조하세요. 다음 표에는 다른 HANA 대규모 인스턴스 장치와 함께 제공되는 여러 볼륨에 대한 대략적인 용량이 나와 있습니다.

| HANA 큰 인스턴스 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1,280GB | 512 GB | 768 GB | 512 GB |
| S72m | 3,328GB | 768 GB |1,280GB | 768 GB |
| S96 | 1,280GB | 512 GB | 768 GB | 512 GB |
| S192 | 4,608GB | 1,024GB | 1,536GB | 1,024GB |
| S192m | 11,520 GB | 1,536GB | 1,792GB | 1,536GB |
| S192xm |  11,520 GB |  1,536GB |  1,792GB |  1,536GB |
| S384 | 11,520 GB | 1,536GB | 1,792GB | 1,536GB |
| S384m | 12,000 GB | 2,050GB | 2,050GB | 2,040GB |
| S384xm | 16,000 GB | 2,050GB | 2,050GB | 2,040GB |
| S384xxm |  20,000 GB | 3,100GB | 2,050GB | 3,100GB |
| S576m | 20,000 GB | 3,100GB | 2,050GB | 3,100GB |
| S576xm | 31,744GB | 4,096GB | 2,048GB | 4,096GB |
| S768m | 28,000 GB | 3,100GB | 2,050GB | 3,100GB |
| S768xm | 40,960GB | 6,144GB | 4,096GB | 6,144GB |
| S960m | 36,000 GB | 4,100GB | 2,050GB | 4,100GB |
| S896m | 33792 GB | 512 GB | 1,024GB | 512 GB |

HANA Large Instances의 최신 Sku는 다음과 같은 저장소 구성으로 제공 됩니다.

| HANA 큰 인스턴스 SKU | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4224 GB | 512 GB | 1,024GB | 512 GB |
| S224oo | 6336 GB | 512 GB | 1,024GB | 512 GB |
| S224m | 8448 GB | 512 GB | 1,024GB | 512 GB |
| S224om | 8448 GB | 512 GB | 1,024GB | 512 GB |
| S224ooo | 10560 GB | 512 GB | 1,024GB | 512 GB |
| S224oom | 12672 GB | 512 GB | 1,024GB | 512 GB |
| S448 | 8448 GB | 512 GB | 1,024GB | 512 GB |
| S448oo | 12672 GB | 512 GB | 1,024GB | 512 GB |
| S448m | 16896 GB | 512 GB | 1,024GB | 512 GB |
| S448om | 16896 GB | 512 GB | 1,024GB | 512 GB |
| S448ooo | 21120 GB | 512 GB | 1,024GB | 512 GB |
| S448oom | 25344 GB | 512 GB | 1,024GB | 512 GB |
| S672 | 12672 GB | 512 GB | 1,024GB | 512 GB |
| S672oo | 19008 GB | 512 GB | 1,024GB | 512 GB |
| S672m | 25344 GB | 512 GB | 1,024GB | 512 GB |
| S672om | 25344 GB | 512 GB | 1,024GB | 512 GB |
| S672ooo | 31680 GB | 512 GB | 1,024GB | 512 GB |
| S672oom | 38016 GB | 512 GB | 1,024GB | 512 GB |
| S896 | 16896 GB | 512 GB | 1,024GB | 512 GB |
| S896oo | 25344 GB | 512 GB | 1,024GB | 512 GB |
| S896om | 33792 GB | 512 GB | 1,024GB | 512 GB |
| S896ooo | 42240 GB | 512 GB | 1,024GB | 512 GB |
| S896oom | 50688 GB | 512 GB | 1,024GB | 512 GB |


실제로 배포되는 볼륨은 볼륨 크기를 표시하는 데 사용된 배포와 도구에 따라 달라질 수 있습니다.

HANA 대규모 인스턴스 SKU를 세분화하는 경우 가능한 분할 조각의 몇 가지 예는 다음과 같을 수 있습니다.

| 메모리 파티션(GB) | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1,280GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1,792GB | 640 GB | 1,024GB | 640 GB |
| 1,536 | 3,328GB | 768 GB | 1,280GB | 768 GB |


이러한 크기는 볼륨을 표시하는 데 사용된 배포와 도구에 따라 약간 달라질 수 있는 대략적인 볼륨 크기입니다. 또한 2.5TB와 같은 다른 파티션 크기도 있습니다. 이러한 스토리지 크기는 이전 파티션에 사용된 것과 비슷한 수식을 사용하여 계산되었습니다. '파티션'이라는 용어는 운영 체제, 메모리 또는 CPU 리소스가 분할된 방식을 나타내지 않습니다. 이는 단지 하나의 HANA 대규모 인스턴스 디바이스에 배포하려는 다른 HANA 인스턴스에 대한 스토리지 파티션을 나타냅니다. 

더 많은 스토리지가 필요할 수 있습니다. 스토리지는 1TB 단위로 추가로 구입하여 추가할 수 있습니다. 이 추가 스토리지는 추가 볼륨으로 추가될 수 있습니다. 또한 기존 볼륨 중 하나 이상을 확장하는 데도 사용할 수 있습니다. 원래 배포된 볼륨 크기는 줄일 수 없으며, 위의 표에서 대부분 설명되어 있습니다. 볼륨 이름을 변경하거나 탑재할 수도 없습니다. 앞에서 설명한 스토리지 볼륨은 NFS4 볼륨으로 HANA 대규모 인스턴스 디바이스에 연결됩니다.

스토리지 스냅샷은 백업/복원 및 재해 복구 용도로 사용할 수 있습니다. 자세한 내용은 [SAP HANA on Azure(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조하세요.

시나리오의 스토리지 레이아웃 세부 정보는 [HLI 지원 시나리오](hana-supported-scenario.md)를 참조하세요.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>하나의 HANA 대규모 인스턴스 장치에서 여러 SAP HANA 인스턴스 실행

HANA 대규모 인스턴스 장치에 둘 이상의 활성 SAP HANA 인스턴스를 호스팅할 수 있습니다. 스토리지 스냅샷 및 재해 복구 기능을 제공하기 위해 이러한 구성에는 인스턴스당 볼륨 세트가 필요합니다. 현재 HANA 대규모 인스턴스 장치는 다음과 같이 세분화할 수 있습니다.

- **S72, S72m, S96, S144, S192**: 최소 시작 단위는 256GB이며, 256GB씩 증분할 수 있습니다. 다른 증분 단위(예: 256GB, 512GB)는 장치의 메모리 최댓값과 결합할 수 있습니다.
- **S144m 및 S192m**: 256 gb 단위로, 512 gb의 가장 작은 단위입니다. 다른 증분 단위(예: 512GB, 768GB)는 장치의 메모리 최댓값과 결합할 수 있습니다.
- **TYPE II class**: 512 GB 단위로, 가장 작은 시작 단위인 2tb를 사용 합니다. 다른 증분 단위(예: 512GB, 1TB, 1.5TB)는 장치의 메모리 최댓값과 결합할 수 있습니다.

여러 SAP HANA 인스턴스를 실행하는 몇 가지 예는 다음과 같습니다.

| SKU | 메모리 크기 | 스토리지 크기 | 여러 데이터베이스가 장착된 크기 |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 x 768GB HANA 인스턴스<br /> 또는 1 x 512GB 인스턴스 + 1 x 256GB 인스턴스<br /> 또는 3 x 256GB 인스턴스 | 
| S72m | 1.5 TB | 6 TB | 3 x 512GB HANA 인스턴스<br />또는 1 x 512GB 인스턴스 + 1 x 1TB 인스턴스<br />또는 6 x 256GB 인스턴스<br />또는 1x1.5TB 인스턴스 | 
| S192m | 4 TB | 16TB | 8 x 512GB 인스턴스<br />또는 4 x 1TB 인스턴스<br />또는 4 x 512GB 인스턴스 + 2 x 1TB 인스턴스<br />또는 4 x 768GB 인스턴스 + 2 x 512GB 인스턴스<br />또는 1 x 4TB 인스턴스 |
| S384xm | 8 TB | 22 TB | 4 x 2TB 인스턴스<br />또는 2 x 4TB 인스턴스<br />또는 2 x 3TB 인스턴스 + 1 x 2TB 인스턴스<br />또는 2 x 2.5TB 인스턴스 + 1 x 3TB 인스턴스<br />또는 1 x 8TB 인스턴스 |


다른 변형도 있습니다. 

## <a name="encryption-of-data-at-rest"></a>저장소 데이터 암호화
HANA Large Instance에 사용 되는 저장소는 2018 년 이후 디스크에 저장 되는 데이터에 대해 투명 한 암호화를 사용 합니다. 이전 배포에서는 암호화 된 볼륨을 가져오도록 선택할 수 있습니다. 해당 옵션에 대해 결정 한 경우 온라인으로 암호화 된 볼륨을 가져오도록 요청할 수 있습니다. 암호화되지 않은 볼륨에서 암호화된 볼륨으로 이동하는 경우 투명하게 수행되며 가동 중지 시간이 필요하지 않습니다. 

유형 I 클래스 SKU를 사용하면 부팅 LUN이 저장된 볼륨이 암호화됩니다. 수정 3 HANA large instance 스탬프에서 HANA Large Instance의 유형 II 클래스를 사용 하 여 OS 메서드를 사용 하 여 부팅 LUN을 암호화 해야 합니다. 수정 버전 4 HANA 대규모 인스턴스 스탬프에서 형식 II 단위를 사용 하 여 부팅 LUN이 저장 되 고 기본적으로 미사용 암호화 됩니다. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>HANA 큰 인스턴스의 큰 HANA 인스턴스에 대 한 필수 설정
HANA 큰 인스턴스에 사용 되는 저장소에는 파일 크기 제한이 있습니다. [크기 제한은 파일당 16TB입니다](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) . EXT3 파일 시스템의 파일 크기 제한과 달리 HANA는 HANA 큰 인스턴스 저장소에서 적용 하는 저장소 제한을 암시적으로 인식 하지 못합니다. 따라서 HANA는 파일 크기 제한인 16TB에 도달할 때 새 데이터 파일을 자동으로 만들지 않습니다. HANA가 16gb를 초과 하 여 파일을 확장 하려고 하면 HANA에서 오류를 보고 하 고 인덱스 서버가 종료 될 때 충돌 합니다.

> [!IMPORTANT]
> Hana 큰 인스턴스 저장소의 16TB 파일 크기 제한을 초과 하 여 데이터 파일을 확장 하려는 HANA를 방지 하려면 HANA의 global.ini 구성 파일에서 다음 매개 변수를 설정 해야 합니다.
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - SAP note를 참조 하세요 [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - SAP note에 유의 하십시오 [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**다음 단계**
- [HANA 대규모 인스턴스의 지원되는 시나리오](hana-supported-scenario.md) 참조