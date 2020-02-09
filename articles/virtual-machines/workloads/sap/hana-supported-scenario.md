---
title: Azure (Large Instances)의 SAP HANA에 대해 지원 되는 시나리오 | Microsoft Docs
description: Azure(큰 인스턴스)의 SAP HANA에 대해 지원되는 시나리오 및 해당 아키텍처 세부 정보
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/26/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eb8278b053ef52f43171137b02e729bfed085e67
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75894712"
---
# <a name="supported-scenarios-for-hana-large-instances"></a>HANA 큰 인스턴스의 지원되는 시나리오
이 문서에서는 HLI (HANA Large Instances)에 대해 지원 되는 시나리오 및 아키텍처 세부 정보를 설명 합니다.

>[!NOTE]
>이 문서에서 필요한 시나리오를 설명 하지 않은 경우 Microsoft 서비스 관리 팀에 문의 하 여 요구 사항을 평가 합니다.
HLI 단위를 설정 하기 전에 SAP 또는 서비스 구현 파트너를 사용 하 여 디자인의 유효성을 검사 합니다.

## <a name="terms-and-definitions"></a>용어 및 정의
이 문서에서 사용 되는 용어와 정의에 대해 알아보겠습니다.

- **SID**: HANA 시스템의 시스템 식별자입니다.
- **Hli**: Hana Large Instances
- **DR**: 재해 복구
- **일반 dr**: dr 전용 전용 리소스를 포함 하는 시스템 설정
- **다목적 dr**: dr 이벤트에 대해 구성 된 프로덕션 인스턴스와 함께 비프로덕션 환경을 사용 하도록 구성 된 dr 사이트 시스템입니다. 
- **단일 SID**: 하나의 인스턴스가 설치 된 시스템
- **다중 SID**: 여러 인스턴스가 구성 된 시스템 MCOS 환경이 라고도 함
- **Hsr**: SAP HANA 시스템 복제

## <a name="overview"></a>개요
HANA Large Instances는 비즈니스 요구 사항을 달성 하는 데 도움이 되는 다양 한 아키텍처를 지원 합니다. 다음 섹션에서는 아키텍처 시나리오 및 구성 세부 정보를 다룹니다. 

파생 아키텍처 디자인은 전적으로 인프라 측면에서 볼 수 있으며, HANA 배포를 위해 SAP 또는 구현 파트너를 참조 해야 합니다. 이 문서에 나와 있지 않은 시나리오는 Microsoft 계정 팀에 문의 하 여 아키텍처를 검토 하 고 솔루션을 파생 시키십시오.

> [!NOTE]
> 이러한 아키텍처는 SAP에서 지 원하는 TDI (맞춤형 데이터 통합) 디자인을 완벽 하 게 준수 합니다.

이 문서에서는 지원 되는 각 아키텍처의 두 구성 요소에 대해 자세히 설명 합니다.

- 이더넷
- Storage

### <a name="ethernet"></a>이더넷

프로 비전 된 각 서버는 이더넷 인터페이스 집합으로 미리 구성 됩니다. 각 HLI 단위에 구성 된 이더넷 인터페이스는 다음과 같은 네 가지 유형으로 분류 됩니다.

- **A**: 또는 클라이언트 액세스에 사용 됩니다.
- **B**: 노드 간 통신에 사용 됩니다. 이 인터페이스는 요청 된 토폴로지와 관계 없이 모든 서버에서 구성 되지만 스케일 아웃 시나리오에만 사용 됩니다.
- **C**: 노드-저장소 연결에 사용 됩니다.
- **D**: STONITH 설치를 위한 노드-iSCSI 장치 연결에 사용 됩니다. 이 인터페이스는 HSR 설정이 요청 된 경우에만 구성 됩니다.  

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드-노드|
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드-노드|
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH |

HLI 단위에 구성 된 토폴로지를 기준으로 인터페이스를 선택 합니다. 예를 들어 "B" 인터페이스는 노드 간 통신에 대해 설정 되며,이는 확장 토폴로지가 구성 된 경우에 유용 합니다. 이 인터페이스는 단일 노드, 수직 확장 구성에 사용 되지 않습니다. 인터페이스 사용에 대 한 자세한 내용은이 문서의 뒷부분에 나오는 필수 시나리오를 검토 하세요. 

필요한 경우 고유한 NIC 카드를 추가로 정의할 수 있습니다. 그러나 기존 Nic의 구성을 변경할 수 *없습니다* .

>[!NOTE]
>실제 인터페이스 또는 결합의 추가 인터페이스를 찾을 수 있습니다. 사용 사례에 대해 앞에서 언급 한 인터페이스만 고려해 야 합니다. 다른 모든 항목은 무시 해도 됩니다.

할당 된 IP 주소가 두 개인 단위에 대 한 분포는 다음과 같습니다.

- "A" 이더넷에는 Microsoft에 제출한 서버 IP 풀 주소 범위 내에 있는 할당 된 IP 주소가 있어야 합니다. 이 IP 주소는 OS의 */etc/hosts* 디렉터리에서 유지 관리 해야 합니다.

- 이더넷 "C"에는 NFS에 대 한 통신에 사용 되는 IP 주소가 할당 되어 있어야 합니다. 이 주소는 테넌트 내에서 인스턴스 간 트래픽을 허용 하기 위해 *등/호스트* 디렉터리에서 유지 관리할 필요가 *없습니다* .

HANA 시스템 복제 또는 HANA 스케일 아웃 배포의 경우 할당 된 IP 주소가 두 개인 블레이드 구성이 적합 하지 않습니다. 할당 된 IP 주소가 두 개만 있고 이러한 구성을 배포 하려면 Azure 서비스 관리에서 SAP HANA에 문의 하세요. 세 번째 VLAN의 세 번째 IP 주소를 할당할 수 있습니다. 세 개의 NIC 포트에서 할당 된 IP 주소가 세 개인 HANA Large Instances 장치의 경우 다음 사용 규칙이 적용 됩니다.

- "A" 이더넷은 Microsoft에 제출한 서버 IP 풀 주소 범위를 벗어난 IP 주소를 할당 해야 합니다. 이 IP 주소는 OS의 *기타/호스트* 디렉터리에서 유지 관리할 수 없습니다.

- 이더넷 "B"는 다양 한 인스턴스 간의 통신을 위해 *기타/호스트* 디렉터리에서 배타적으로 유지 관리 해야 합니다. 이 IP 주소는 HANA가 노드 간 구성에 사용 하는 IP 주소로 스케일 아웃 HANA 구성에서 유지 관리 됩니다.

- 이더넷 "C"에는 NFS 저장소에 대 한 통신에 사용 되는 IP 주소가 할당 되어 있어야 합니다. 이 유형의 주소는 *기타/호스트* 디렉터리에서 유지 관리 하면 안 됩니다.

- 이더넷 "D"는 Pacemaker의 STONITH 장치에 액세스 하는 데 독점적으로 사용 되어야 합니다. 이 인터페이스는 HANA 시스템 복제를 구성 하 고 SBD 기반 장치를 사용 하 여 운영 체제의 자동 장애 조치 (failover)를 수행 하려는 경우에 필요 합니다.


### <a name="storage"></a>Storage
저장소는 요청 된 토폴로지에 따라 미리 구성 됩니다. 볼륨 크기 및 탑재 지점은 서버 수, Sku 수 및 구성 된 토폴로지에 따라 달라 집니다. 자세한 내용은이 문서의 뒷부분에 나오는 필수 시나리오를 검토 하세요. 저장소를 더 많이 필요로 하는 경우 1TB 단위로 구매할 수 있습니다.

>[!NOTE]
>/Usr/sap/\<SID > 탑재 지점은/hana/shared 탑재 지점에 대 한 기호화 된 링크입니다.


## <a name="supported-scenarios"></a>지원되는 시나리오

다음 섹션의 아키텍처 다이어그램은 다음 표기법을 사용 합니다.

[아키텍처 다이어그램 ![테이블](media/hana-supported-scenario/Legends.png)](media/hana-supported-scenario/Legends.png#lightbox)

지원 되는 시나리오는 다음과 같습니다.

* SID가 1개인 단일 노드
* 단일 노드 MCOS
* DR이 있는 단일 노드 (보통)
* DR (다목적)이 포함 된 단일 노드
* STONITH가 있는 HSR
* DR을 사용한 HSR (normal/다목적) 
* 호스트 자동 장애 조치(Failover)(1+1) 
* 대기 상태로 스케일 아웃
* 대기 없이 스케일 아웃
* DR을 사용하여 스케일 아웃

## <a name="single-node-with-one-sid"></a>SID가 1개인 단일 노드

이 토폴로지는 하나의 SID를 사용 하는 강화 구성에서 하나의 노드를 지원 합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![SID가 1개인 단일 노드](media/hana-supported-scenario/Single-node-with-one-SID.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | HANA 설치 | 
|/hana/data/SID/mnt00001 | 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 로그 파일 설치 | 
|/hana/logbackups/SID | 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="single-node-mcos"></a>단일 노드 MCOS

이 토폴로지는 여러 Sid를 사용 하는 수직 확장 구성에서 하나의 노드를 지원 합니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![단일 노드 MCOS](media/hana-supported-scenario/single-node-mcos.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID1 | SID1에 대 한 HANA 설치 | 
|/hana/data/SID1/mnt00001 | SID1 용 데이터 파일 설치 | 
|/hana/log/SID1/mnt00001 | SID1에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID1 | SID1의 다시 실행 로그 |
|/hana/shared/SID2 | SID2에 대 한 HANA 설치 | 
|/hana/data/SID2/mnt00001 | SID2 용 데이터 파일 설치 | 
|/hana/log/SID2/mnt00001 | SID2에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID2 | SID2의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 볼륨 크기 분포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.

## <a name="single-node-with-dr-using-storage-replication"></a>저장소 복제를 사용 하는 DR이 있는 단일 노드
 
이 토폴로지는 주 SID에 대 한 DR 사이트로 저장소 기반 복제를 사용 하 여 하나 이상의 Sid를 사용 하는 확장 구성의 한 노드를 지원 합니다. 다이어그램에서 단일 SID 시스템만 기본 사이트에 표시 되지만 MCOS 시스템도 지원 됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![저장소 복제를 사용 하는 DR이 있는 단일 노드](media/hana-supported-scenario/Single-node-with-dr.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.
- DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하는 데 볼륨 및 탑재 지점이 구성 됩니다 ("HANA 설치에 필요"로 표시 됨). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨 ("저장소 복제"로 표시 됨)은 프로덕션 사이트의 스냅숏을 통해 복제 됩니다. 이러한 볼륨은 장애 조치 (failover) 중에만 탑재 됩니다. 자세한 내용은 [재해 복구 장애 조치 (failover) 절차](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)를 참조 하세요.
- *SKU 유형 I 클래스* 의 부팅 볼륨이 DR 노드에 복제 됩니다.


## <a name="single-node-with-dr-multipurpose-using-storage-replication"></a>저장소 복제를 사용 하는 DR (다목적)이 포함 된 단일 노드
 
이 토폴로지는 주 SID에 대 한 DR 사이트로 저장소 기반 복제를 사용 하 여 하나 이상의 Sid를 사용 하는 확장 구성의 한 노드를 지원 합니다. 다이어그램에서 단일 SID 시스템만 기본 사이트에 표시 되지만 다중 SID (MCOS) 시스템도 지원 됩니다. DR 사이트에서 HLI 단위는 기본 사이트에서 프로덕션 작업을 실행 하는 동안 QA 인스턴스에 사용 됩니다. DR 장애 조치 (failover) 또는 장애 조치 (failover)를 수행 하는 동안 DR 사이트의 QA 인스턴스가 중단 됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![저장소 복제를 사용 하는 DR (다목적)이 포함 된 단일 노드](media/hana-supported-scenario/single-node-with-dr-multipurpose.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID에 대 한 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID에 대 한 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID에 대 한 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.
- DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하는 데 볼륨 및 탑재 지점이 구성 됩니다 ("HANA 설치에 필요"로 표시 됨). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨 ("저장소 복제"로 표시 됨)은 프로덕션 사이트의 스냅숏을 통해 복제 됩니다. 이러한 볼륨은 장애 조치 (failover) 중에만 탑재 됩니다. 자세한 내용은 [재해 복구 장애 조치 (failover) 절차](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)를 참조 하세요. 
- DR 사이트에서: qa 인스턴스 설치에 대해 QA의 데이터, 로그 백업, 로그 및 공유 볼륨 ("QA 인스턴스 설치"로 표시 됨)이 구성 되어 있습니다.
- *SKU 유형 I 클래스* 의 부팅 볼륨이 DR 노드에 복제 됩니다.

## <a name="hsr-with-stonith-for-high-availability"></a>고가용성을 위한 STONITH가 있는 HSR
 
이 토폴로지는 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원 합니다. 이 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원 됩니다. 즉, MCOS 시나리오는 지원 *되지 않습니다* .

> [!NOTE]
> 12 월 2019 현재이 아키텍처는 SUSE 운영 체제에 대해서만 지원 됩니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![고가용성을 위한 STONITH가 있는 HSR](media/hana-supported-scenario/HSR-with-STONITH.png)



### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**보조 노드**|
|/hana/shared/SID | 보조 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH를 사용 하는 것은 선택 사항입니다.


## <a name="high-availability-with-hsr-and-dr-with-storage-replication"></a>저장소 복제를 사용 하는 HSR 및 DR을 사용 하는 고가용성
 
이 토폴로지는 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원 합니다. Normal 및 다목적 DRs가 모두 지원 됩니다. 이러한 구성은 노드의 단일 HANA 인스턴스에 대해서만 지원 됩니다. 즉, MCOS 시나리오는 이러한 구성에서 지원 *되지 않습니다* .

다이어그램에서 다목적 시나리오는 DR 사이트에서 표시 됩니다. 여기서 HLI 단위는 기본 사이트에서 실행 되는 동안 QA 인스턴스에 사용 됩니다. DR 장애 조치 (failover) 또는 장애 조치 (failover)를 수행 하는 동안 DR 사이트의 QA 인스턴스가 중단 됩니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![저장소 복제를 사용 하는 HSR 및 DR을 사용 하는 고가용성](media/hana-supported-scenario/HSR-with-DR.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | STONITH에 사용 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | STONITH에 사용 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트의 주 노드**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**기본 사이트의 보조 노드**|
|/hana/shared/SID | 보조 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 보조 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 보조 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 보조 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/shared/QA-SID | QA SID에 대 한 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID에 대 한 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID에 대 한 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.
- STONITH: STONITH 설치에 대해 SBD가 구성됩니다. 그러나 STONITH를 사용 하는 것은 선택 사항입니다.
- DR 사이트에서: 기본 및 보조 노드 복제에 *는 두 개의 저장소 볼륨 집합이 필요* 합니다.
- DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하는 데 볼륨 및 탑재 지점이 구성 됩니다 ("HANA 설치에 필요"로 표시 됨). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨 ("저장소 복제"로 표시 됨)은 프로덕션 사이트의 스냅숏을 통해 복제 됩니다. 이러한 볼륨은 장애 조치 (failover) 중에만 탑재 됩니다. 자세한 내용은 [재해 복구 장애 조치 (failover) 절차](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)를 참조 하세요. 
- DR 사이트에서: qa 인스턴스 설치에 대해 QA의 데이터, 로그 백업, 로그 및 공유 볼륨 ("QA 인스턴스 설치"로 표시 됨)이 구성 되어 있습니다.
- *SKU 유형 I 클래스* 의 부팅 볼륨이 DR 노드에 복제 됩니다.


## <a name="host-auto-failover-11"></a>호스트 자동 장애 조치(Failover)(1+1)
 
이 토폴로지는 호스트 자동 장애 조치(Failover) 구성의 두 노드를 지원합니다. 마스터/작업자 역할이 있고 대기 인 노드가 하나 있습니다. *SAP는 S/4 HANA에 대해서만이 시나리오를 지원 합니다.* 자세한 내용은 [OSS note 2408419-SAP S/4HANA-다중 노드 지원](https://launchpad.support.sap.com/#/notes/2408419)을 참조 하세요.



### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![호스트 자동 장애 조치(Failover)(1+1)](media/hana-supported-scenario/scaleup-with-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터 및 대기 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |



### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- 대기 시: 대기 단위에서 HANA 인스턴스를 설치 하는 데 볼륨 및 탑재 지점이 구성 됩니다 ("HANA 설치에 필요"로 표시 됨).
 

## <a name="scale-out-with-standby"></a>대기 상태로 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 마스터 역할이 있는 노드 하나, 작업자 역할이 있는 하나 이상의 노드 및 하나 이상의 노드가 대기 상태입니다. 그러나 한 시점에 하나의 마스터 노드만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![대기 상태로 스케일 아웃](media/hana-supported-scenario/scaleout-nm-standby.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터, 작업자 및 대기 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


## <a name="scale-out-without-standby"></a>대기 없이 스케일 아웃
 
이 토폴로지는 스케일 아웃 구성의 여러 노드를 지원합니다. 마스터 역할이 있는 노드 하 나와 작업자 역할이 있는 하나 이상의 노드가 있습니다. 그러나 한 시점에 하나의 마스터 노드만 있을 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![대기 없이 스케일 아웃](media/hana-supported-scenario/scaleout-nm.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**마스터 및 작업자 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.

## <a name="scale-out-with-dr-using-storage-replication"></a>저장소 복제를 사용 하 여 DR 확장
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. Normal 및 다목적 DRs가 모두 지원 됩니다. 이 다이어그램에는 단일 용도 DR만 표시됩니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![저장소 복제를 사용 하 여 DR 확장](media/hana-supported-scenario/scaleout-with-dr.png)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
-  DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하는 데 볼륨 및 탑재 지점이 구성 됩니다 ("HANA 설치에 필요"로 표시 됨). 
- DR 사이트에서: 데이터, 로그 백업 및 공유 볼륨 ("저장소 복제"로 표시 됨)은 프로덕션 사이트의 스냅숏을 통해 복제 됩니다. 이러한 볼륨은 장애 조치 (failover) 중에만 탑재 됩니다. 자세한 내용은 [재해 복구 장애 조치 (failover) 절차](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)를 참조 하세요. 
- *SKU 유형 I 클래스* 의 부팅 볼륨이 DR 노드에 복제 됩니다.


## <a name="single-node-with-dr-using-hsr"></a>HSR를 사용 하는 DR을 사용 하는 단일 노드
 
이 토폴로지는 주 SID에 대 한 DR 사이트로의 HANA 시스템 복제를 사용 하 여 하나의 SID를 사용 하는 확장 구성에서 하나의 노드를 지원 합니다. 다이어그램에서 단일 SID 시스템만 기본 사이트에 표시 되지만 다중 SID (MCOS) 시스템도 지원 됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR를 사용 하는 DR을 사용 하는 단일 노드](media/hana-supported-scenario/single-node-hsr-dr-111.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI/HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 HLI 단위 (기본 및 DR) 모두에 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|/hana/shared/SID | SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.
- 주 노드는 HANA 시스템 복제를 사용 하 여 DR 노드와 동기화 됩니다. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) 는 express 경로 회로를 함께 연결 하 여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용 됩니다.



## <a name="single-node-hsr-to-dr-cost-optimized"></a>단일 노드 HSR에서 DR으로 (비용 최적화) 
 
 이 토폴로지는 주 SID에 대 한 DR 사이트로의 HANA 시스템 복제를 사용 하 여 하나의 SID를 사용 하는 확장 구성에서 하나의 노드를 지원 합니다. 다이어그램에서 단일 SID 시스템만 기본 사이트에 표시 되지만 다중 SID (MCOS) 시스템도 지원 됩니다. DR 사이트에서 HLI 단위는 기본 사이트에서 프로덕션 작업을 실행 하는 동안 QA 인스턴스에 사용 됩니다. DR 장애 조치 (failover) 또는 장애 조치 (failover)를 수행 하는 동안 DR 사이트의 QA 인스턴스가 중단 됩니다.

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![단일 노드 HSR에서 DR으로 (비용 최적화)](media/hana-supported-scenario/single-node-hsr-dr-cost-optimized-121.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI/HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|/hana/shared/QA-SID | QA SID에 대 한 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID에 대 한 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID에 대 한 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- MCOS: 볼륨 크기 배포는 메모리의 데이터베이스 크기를 기준으로 합니다. 다중 SID 환경에서 지원 되는 메모리의 데이터베이스 크기에 대 한 자세한 내용은 [개요 및 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)를 참조 하세요.
- DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하는 볼륨 및 탑재 지점이 구성 됩니다 ("DR 사이트에서 PROD 인스턴스"로 표시 됨). 
- DR 사이트에서: qa 인스턴스 설치에 대해 QA의 데이터, 로그 백업, 로그 및 공유 볼륨 ("QA 인스턴스 설치"로 표시 됨)이 구성 되어 있습니다.
- 주 노드는 HANA 시스템 복제를 사용 하 여 DR 노드와 동기화 됩니다. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) 는 express 경로 회로를 함께 연결 하 여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용 됩니다.

## <a name="high-availability-and-disaster-recovery-with-hsr"></a>HSR에서 고가용성 및 재해 복구 
 
 이 토폴로지는 로컬 지역의 고가용성을 위한 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원 합니다. DR의 경우 DR 지역의 세 번째 노드는 HSR (비동기 모드)을 사용 하 여 기본 사이트와 동기화 됩니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR에서 고가용성 및 재해 복구](media/hana-supported-scenario/hana-system-replication-dr-131.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI/HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하기 위해 볼륨 및 탑재 지점이 구성 됩니다 ("PROD DR 인스턴스"로 표시 됨). 
- 기본 사이트 노드는 HANA 시스템 복제를 사용 하 여 DR 노드와 동기화 됩니다. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) 는 express 경로 회로를 함께 연결 하 여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용 됩니다.

## <a name="high-availability-and-disaster-recovery-with-hsr-cost-optimized"></a>HSR를 통한 고가용성 및 재해 복구 (비용 최적화)
 
 이 토폴로지는 로컬 지역의 고가용성을 위한 HANA 시스템 복제 구성에 대해 두 개의 노드를 지원 합니다. Dr의 경우 DR 지역의 세 번째 노드는 HSR (비동기 모드)을 사용 하 여 기본 사이트와 동기화 하지만 다른 인스턴스 (예: QA)는 DR 노드에서 이미 실행 되 고 있습니다. 

### <a name="architecture-diagram"></a>아키텍처 다이어그램  

![HSR를 통한 고가용성 및 재해 복구 (비용 최적화)](media/hana-supported-scenario/hana-system-replication-dr-cost-optimized-141.png)

### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 구성되었으나 사용되고 있지 않음 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI/HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 구성되었으나 사용되고 있지 않음 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**기본 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 사이트**|
|/hana/shared/SID | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|/hana/shared/QA-SID | QA SID에 대 한 HANA 설치 | 
|/hana/data/QA-SID/mnt00001 | QA SID에 대 한 데이터 파일 설치 | 
|/hana/log/QA-SID/mnt00001 | QA SID에 대 한 로그 파일 설치 |
|/hana/logbackups/QA-SID | QA SID의 다시 실행 로그 |

### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트에서: DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하기 위해 볼륨 및 탑재 지점이 구성 됩니다 ("PROD DR 인스턴스"로 표시 됨). 
- DR 사이트에서: qa 인스턴스 설치에 대해 QA의 데이터, 로그 백업, 로그 및 공유 볼륨 ("QA 인스턴스 설치"로 표시 됨)이 구성 되어 있습니다.
- 기본 사이트 노드는 HANA 시스템 복제를 사용 하 여 DR 노드와 동기화 됩니다. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) 는 express 경로 회로를 함께 연결 하 여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용 됩니다.

## <a name="scale-out-with-dr-using-hsr"></a>HSR를 사용 하 여 DR으로 확장
 
이 토폴로지는 DR이 있는 스케일 아웃의 여러 노드를 지원합니다. 대기 노드가 있거나 없는 상태로 이 토폴로지를 요청할 수 있습니다. 기본 사이트 노드는 HANA 시스템 복제 (비동기 모드)를 사용 하 여 DR 사이트 노드와 동기화 됩니다.


### <a name="architecture-diagram"></a>아키텍처 다이어그램  

[HSR를 사용 하 여 DR으로 확장 ![](media/hana-supported-scenario/scale-out-dr-hsr-151.png)](media/hana-supported-scenario/scale-out-dr-hsr-151.png#lightbox)


### <a name="ethernet"></a>이더넷
다음 네트워크 인터페이스는 미리 구성됩니다.

| NIC 논리 인터페이스 | SKU 유형 | SUSE OS를 포함하는 이름 | RHEL OS를 포함하는 이름 | 사용 사례|
| --- | --- | --- | --- | --- |
| A | 유형 I | eth0.tenant | eno1.tenant | 클라이언트-HLI/HLI |
| B | 유형 I | eth2.tenant | eno3.tenant | 노드 간 통신 |
| C | 유형 I | eth1.tenant | eno2.tenant | 노드-저장소 |
| D | 유형 I | eth4.tenant | eno4.tenant | 구성되었으나 사용되고 있지 않음 |
| A | 유형 II | vlan\<tenantNo> | team0.tenant | 클라이언트-HLI/HLI |
| B | 유형 II | vlan\<tenantNo+2> | team0.tenant+2 | 노드 간 통신 |
| C | 유형 II | vlan\<tenantNo+1> | team0.tenant+1 | 노드-저장소 |
| D | 유형 II | vlan\<tenantNo+3> | team0.tenant+3 | 구성되었으나 사용되고 있지 않음 |

### <a name="storage"></a>Storage
다음 탑재 지점은 미리 구성 되어 있습니다.

| 탑재 지점 | 사용 사례 | 
| --- | --- |
|**주 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |
|**DR 노드**|
|/hana/shared | 프로덕션 SID에 대 한 HANA 설치 | 
|/hana/data/SID/mnt00001 | 프로덕션 SID에 대 한 데이터 파일 설치 | 
|/hana/log/SID/mnt00001 | 프로덕션 SID에 대 한 로그 파일 설치 | 
|/hana/logbackups/SID | 프로덕션 SID의 다시 실행 로그 |


### <a name="key-considerations"></a>주요 고려 사항
- /usr/sap/SID는 /hana/shared/SID의 심볼 링크입니다.
- DR 사이트에서: 볼륨 및 탑재 지점은 DR HLI 단위에서 프로덕션 HANA 인스턴스를 설치 하도록 구성 됩니다. 
- 기본 사이트 노드는 HANA 시스템 복제를 사용 하 여 DR 노드와 동기화 됩니다. 
- [Global Reach](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) 는 express 경로 회로를 함께 연결 하 여 지역 네트워크 간에 개인 네트워크를 만드는 데 사용 됩니다.


## <a name="next-steps"></a>다음 단계
- HANA Large Instances에 대 한 [인프라 및 연결](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- HANA 큰 인스턴스에 대 한 [고가용성 및 재해 복구](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
