---
title: Azure (Large Instances)에서 Azure virtual machines로 SAP HANA 마이그레이션 | Microsoft Docs
description: Azure (Large Instances)의 SAP HANA를 Azure virtual machines로 마이그레이션하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154921"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure Virtual Machines에 대 한 Azure Large Instance 마이그레이션 SAP HANA
이 문서에서는 가능한 Azure Large Instance 배포 시나리오를 설명 하 고 전환 가동 중지 시간을 최소화 하는 계획 및 마이그레이션 방식을 제공 합니다.

## <a name="overview"></a>개요
2016 년 9 월에 HLI (SAP HANA 용 Azure Large Instances)를 발표 했으므로 많은 고객이 메모리 내 계산 플랫폼용으로이 하드웨어를 서비스로 채택 했습니다.  최근 몇 년 동안 HANA 스케일 아웃 배포 모델 지원과 결합 된 Azure VM 확장성 확장이 대부분의 엔터프라이즈 고객의 ERP 데이터베이스 용량 수요를 초과 했습니다.  물리적 서버에서 Azure Vm으로 SAP HANA 워크 로드를 마이그레이션하는 것을 나타내는 고객이 표시 되기 시작 합니다.
이 가이드는 단계별 구성 문서는 아니지만, 일반적인 배포 모델을 설명 하 고, 계획, 마이그레이션 권고를 제공 하며,이를 통해 전환 가동 중지 시간을 최소화 하기 위해 필요한 고려 사항을 호출 하려고 합니다.

## <a name="assumptions"></a>가정
이 문서에서는 다음과 같이 가정합니다.
- 중요 한 것은 중요 한 소프트웨어를 업그레이드 하거나 패치를 적용 하지 않고 HLI (Hana Large Instance)에서 Azure VM으로의 동종 HANA 데이터베이스 계산 서비스 마이그레이션입니다. 이러한 사소한 업데이트에는 관련 SAP notes에서 지원 되는 것으로 명시적으로 명시 된 최신 OS 버전 또는 HANA 버전이 사용 됩니다. 
- 필요한 경우 모든 업데이트/업그레이드 작업은 마이그레이션 전이나 후에 수행 해야 합니다.  예를 들어 MDC 배포로 변환 하는 SAP HANA MCOS를 사용할 수 있습니다. 
- 최소 가동 중지 시간을 제공 하는 마이그레이션 방법은 시스템 복제를 SAP HANA 합니다. 다른 마이그레이션 방법은이 문서의 범위에 포함 되지 않습니다.
- 이는 HLI의 Rev3 및 Rev4 Sku 모두에 적용 됩니다.
- HANA 배포 아키텍처는 마이그레이션 중에 주로 변경 되지 않은 상태로 유지 됩니다.  즉, 하나의 단일 인스턴스 DR이 있는 시스템은 대상에서 동일 하 게 유지 됩니다.
- 고객은 대상 아키텍처의 Service Level Agreement(서비스 수준 약정) (SLA)를 검토 하 고 파악 했습니다. 
- HLIs와 Vm 간의 상용 용어 차이로 인해 고객은 비용 관리를 위해 Vm의 사용을 모니터링 해야 합니다.
- 고객은 HLI가 전용 계산 플랫폼인 것을 이해 하 고 인정 합니다. 그러나 Vm은 공유 인프라에서 실행 되며 다른 테 넌 트에서 안전 하 게 격리 됩니다.
- 고객은 대상 Vm이 원하는 아키텍처를 지원 하는지 확인 했습니다. SAP HANA 배포에 대해 인증 된 모든 지원 되는 VM Sku를 보려면 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)를 확인 하세요.
- 고객은 디자인 및 마이그레이션 계획의 유효성을 검사 했습니다.
- 기본 사이트와 함께 재해 복구 노드를 계획 합니다.  고객은 마이그레이션 후 Vm에서 실행 되는 기본 사이트에 HLI DR 노드를 사용할 수 없습니다.
- 고객은 비즈니스 복구 및 규정 준수 요구 사항에 따라 필요한 백업 파일을 대상 Vm에 복사 했습니다. 이는 전환 기간 동안 특정 시점 복구 요구를 처리 하는 것입니다.
- HSR HA의 경우 고객은 [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) 및 [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)에 대 한 SAP HANA HA 가이드에 따라 stonith 장치를 설정 하 고 구성 해야 합니다.  HLI 케이스와 같이 미리 구성 되지 않습니다.
- 이 마이그레이션 방식은 Optane 구성이 포함 된 HLI Sku를 포함 하지 않습니다.

## <a name="deployment-scenarios"></a>배포 시나리오
HLI 고객이 있는 일반적인 배포 모델은 다음 표에 요약 되어 있습니다.  모든 HLI 시나리오에 대해 Azure Vm으로 마이그레이션하는 것이 가능 합니다.  일부 시나리오에서는 현재 Azure 서비스 제공의 이점을 얻기 위해 약간의 아키텍처 수정이 필요할 수 있습니다.

| 시나리오 ID | HLI 시나리오 | 축 자 축으로 마이그레이션 | 설명 |
| --- | --- | --- | --- |
| 1 | [SID가 하나인 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | 예 | - |
| 2 | [MCOS를 사용 하는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | 예 | - |
| 3 | [저장소 복제를 사용 하는 DR이 있는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | 아니요 | Azure 가상 플랫폼에서 저장소 복제를 사용할 수 없습니다. 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경 하세요. |
| 4 | [저장소 복제를 사용 하는 DR (다목적)이 포함 된 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | 아니요 | Azure 가상 플랫폼에서 저장소 복제를 사용할 수 없습니다. 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경 하세요. |
| 5 | [고가용성을 위한 STONITH가 있는 HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | 예 | 대상 Vm에 대 한 미리 구성 된 SBD 없습니다.  STONITH 솔루션을 선택 하 고 배포 합니다.  가능한 옵션: Azure 펜스 에이전트 ( [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)모두에 지원 됨), SBD |
| 6 | [HSR, 저장소 복제를 사용 하는 DR을 사용한 HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | 아니요 | HSR 또는 백업/복원 중 하나를 사용 하 여 DR 요구 사항에 대 한 저장소 복제를 바꿉니다. |
| 7 | [호스트 자동 장애 조치 (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | 예 | Azure Vm에서 공유 저장소에 ANF 사용 |
| 8 | [Standby를 사용 하 여 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | 예 | M128s, M416s, M416ms Vm을 사용 하 여 저장소 전용으로 ANF를 사용 하는 BW/4HANA |
| 9 | [대기 하지 않고 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | 예 | M128s, M416s, M416ms Vm (저장소에 대해 ANF를 사용 하거나 사용 하지 않음)을 사용 하는 BW/4HANA |
| 10 | [저장소 복제를 사용 하 여 DR 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | 아니요 | HSR 또는 백업/복원 중 하나를 사용 하 여 DR 요구 사항에 대 한 저장소 복제를 바꿉니다. |
| 11 | [HSR를 사용 하는 DR을 사용 하는 단일 노드](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | 예 | - |
| 12 | [단일 노드 HSR에서 DR으로 (비용 최적화)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | 예 | - |
| 13 | [HSR에서 HA 및 DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | 예 | - |
| 14 | [HSR에서 HA 및 DR (비용 최적화)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 예 | - |
| 15 | [HSR를 사용 하 여 DR으로 확장](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | 예 | M128s를 사용 하는 BW/4HANA M416s, M416ms Vm (저장소에 ANF를 사용 하거나 사용 하지 않음) |


## <a name="source-hli-planning"></a>원본 (HLI) 계획
HLI 서버를 등록 하는 경우 Microsoft Service Management와 고객은 모두 SAP HANA 데이터베이스 실행을 위한 계산, 네트워크, 저장소 및 OS 특정 설정을 계획 하 고 있습니다.  Azure VM으로 마이그레이션하기 위해 유사한 계획을 수행 해야 합니다.

### <a name="sap-hana-housekeeping"></a>SAP HANA 정리 
HANA 데이터베이스를 마이그레이션하기 전에 데이터베이스 콘텐츠를 정리 하는 것이 좋습니다. 이렇게 하면 원치 않는 데이터 또는 오래 된 로그가 새 데이터베이스로 마이그레이션되지 않습니다.  일반적으로는 이전, 만료 또는 비활성 데이터를 삭제 하거나 보관 해야 합니다.  따라서 이러한 ' 데이터 예방 ' 작업은 프로덕션 사용 전에 데이터 트리밍 유효성을 검사 하기 위해 비프로덕션 시스템에서 테스트 해야 합니다.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>새 Vm 및 또는 가상 네트워크에 대 한 네트워크 연결 허용 
고객의 HLI 배포에서는 [SAP HANA (Large Instances) 네트워크 아키텍처](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)문서에 설명 된 정보를 기반으로 Azure vnet의 네트워크 연결이 설정 되었습니다. 또한 네트워크 트래픽 라우팅은 ' Azure에서 라우팅 ' 섹션에 설명 된 방식으로 수행 됩니다.
1. 새 VM을 마이그레이션의 HSR 대상으로 설정 하기 위해 새 Azure VM이 이미 HSR 노드에 연결할 권한이 있는 IP 주소 범위를 사용 하 여 기존 가상 네트워크에 배치 된 경우 추가 HSR 연결 업데이트는 필요 하지 않습니다.
2. 새 Azure VM이 새 가상 네트워크 (다른 지역에 있을 수 있음)에 배치 되 고 기존 가상 네트워크와 피어 링 경우 원래 HLI 프로 비전의 Express 경로 서비스 키 및 리소스 ID를 사용 하 여이 새 가상 네트워크 IP 범위에 대 한 액세스를 허용할 수 있습니다.  Microsoft 서비스 관리와 함께 가상 네트워크에서 HLI 연결을 사용 하도록 설정 합니다.  참고: 응용 프로그램과 데이터베이스 계층 간의 네트워크 대기 시간을 최소화 하려면 응용 프로그램과 데이터베이스 계층이 모두 동일한 가상 네트워크에 있어야 합니다.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>기존 앱 계층 가용성 집합, 가용성 영역 및 근접 배치 그룹
현재 배포 모델은 특정 서비스 수준 목표를 충족 하기 위해 수행 됩니다.  이 이동에서 대상 인프라가 설정 목표를 충족 하거나 초과 하는지 확인 합니다.  
그렇지 않을 경우 고객 SAP 응용 프로그램 서버가 가용성 집합에 배치 됩니다.  현재 배포 서비스 수준이 만족할 경우 
- DB 호스트 이름에 대 한 Ip를 교환 하 여 이름 확인을 통해 SAP HANA 서버를 교체 하는 경우 추가 작업을 수행할 필요가 없습니다.  
- PPG를 사용 하지 않는 경우 네트워크 대기 시간을 최소화 하기 위해 모든 응용 프로그램과 DB 서버를 동일한 영역에 두어야 합니다.
- PPG를 사용 하는 경우이 문서 대상 계획, 가용성 집합, 가용성 영역 및 근접 배치 그룹 (PPG)의 섹션을 참조 하세요.

### <a name="storage-replication-discontinuance-process-if-used"></a>Storage replication discontinuance process (사용 되는 경우)
저장소 복제를 DR 솔루션으로 사용 하는 경우 SAP 응용 프로그램이 종료 되 고 마지막 SAP HANA 카탈로그, 로그 파일, 데이터 백업이 원격 저장소 볼륨에 복제 된 후 복제를 종료 (예약 되지 않음) 해야 합니다.  이 작업은 물리적 컴퓨터에서 Azure VM으로 전환 하는 동안 재해가 발생 하는 경우 DR HLI에 현재 데이터베이스가 있는 예비 방법입니다.

### <a name="data-backups-preservation-consideration"></a>데이터 백업 유지 고려 사항
Azure VM에서 SAP HANA 하는 경우 HLI의 모든 스냅숏 기반 데이터 또는 로그 백업은 쉽게 액세스 하거나 필요한 경우 VM에 복원 가능한 수 없습니다. 초기 전환 기간 동안에는 Azure 기반 백업에서 지정 시간 복구 요구 사항을 충족 하기에 충분 한 기록을 빌드하기 전에 먼저 HLI, 일/주에 대 한 스냅숏 외에도 파일 수준 백업을 수행 하는 것이 좋습니다.  이러한 백업은 새 SAP HANA VM에서 액세스할 수 있는 Azure Storage 계정에 복사 합니다. HLI 콘텐츠를 백업 하는 것 외에도 롤백이 필요한 경우 SAP 가로의 전체 백업을 쉽게 액세스할 수 있도록 하는 것이 좋습니다.

### <a name="adjusting-system-monitoring"></a>시스템 모니터링 조정 
고객은 다양 한 도구를 사용 하 여 SAP 환경 내에서 시스템에 대 한 경고 알림을 모니터링 하 고 전송 합니다.  이 항목은 필요한 경우 경고 알림 받는 사람을 모니터링 하 고 업데이트 하기 위해 모든 조정을 통합 하는 새 VM을 추가할 때 적절 한 작업에 대 한 간단한 호출 일 뿐입니다.

### <a name="microsoft-operations-team-involvement"></a>Microsoft 운영 팀 참여 
기존 HLI 인스턴스의 Azure Portal 베이스에서 티켓을 엽니다.  지원 티켓이 만들어지면 지원 엔지니어가 전자 메일을 통해 연락을 드립니다.  

### <a name="engage-microsoft-account-team"></a>Microsoft 계정 팀 참여
계산 리소스에 대 한 불필요 한 비용을 최소화 하기 위해 HLI 계약의 갱신 시간에 가까운 마이그레이션을 계획 합니다. HLI 블레이드를 서비스 해제 하려면 단위의 계약 종료 및 실제 종료를 조정 해야 합니다.

## <a name="destination-planning"></a>대상 계획
새 인프라를 구축 하 여 새로운 추가가 다양 한 항목에 적합 한지 확인 해야 합니다.  다음은 contemplation에 대 한 몇 가지 주요 사항입니다.

### <a name="resource-availability-in-the-target-region"></a>대상 지역의 리소스 가용성 
현재 SAP 응용 프로그램 서버의 배포 지역은 일반적으로 연결 된 HLIs에 근접 합니다.  그러나 HLIs는 사용 가능한 Azure 지역 보다 더 작은 위치로 제공 됩니다.  물리적 HLI에서 Azure VM으로 마이그레이션하는 동안 성능 최적화를 위해 모든 관련 서비스의 근접 거리를 ' 미세 조정 ' 하는 기회를 제공 합니다.  이 작업을 수행 하는 동안 선택 된 영역에 관심 있는 리소스가 있는지 확인 하는 것이 중요 합니다.  예를 들어 고가용성을 고려 하기 위해 특정 VM 제품군 또는 Azure 영역 제공의 가용성을 제공 합니다.

### <a name="virtual-network"></a>가상 네트워크 
인프라 설계자는 기존 SAP 응용 프로그램 가상 네트워크 내에서 새로운 HANA 데이터베이스를 실행할지 아니면 새 HANA 데이터베이스를 confronted 선택할 수 있습니다.  기본 결정 요인은 SAP 환경에 대 한 현재 네트워킹 레이아웃입니다.  예를 들어 단일 영역에서 2 개 영역으로 배포 하 고 PPG를 활용 하는 경우 배포 인프라 변경이 고려 됩니다. 이 가용성 향상은 아키텍처 변경을 적용 합니다. 자세한 내용은 [SAP 응용 프로그램을 사용 하 여 최적의 네트워크 대기 시간에 대 한 Azure PPG](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)문서를 참조 하세요.   

### <a name="security"></a>보안
새 SAP HANA VM이 신규 또는 기존 vnet/서브넷에서 발생 하는지 여부는 보호를 요구 하는 새로운 중요 업무용 서비스를 나타냅니다.  이 새로운 서비스 클래스에 대 한 회사 정보 보안 정책을 준수 하는 적절 한 액세스 제어를 평가 하 고 배포 해야 합니다. 

### <a name="vm-sizing-recommendation"></a>VM 크기 조정 권장 사항
또한이 마이그레이션은 HANA 계산 엔진을 적절 하 게 크기를 조정 하는 기회입니다.  Hana [시스템 뷰](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) 를 hana Studio와 함께 사용 하 여 시스템 리소스 사용을 이해할 수 있으며,이를 통해 올바른 크기 조정으로 지출 효율성을 높일 수 있습니다.

### <a name="storage"></a>스토리지 
저장소 성능은 SAP 응용 프로그램 사용자 환경에 영향을 주는 요인 중 하나입니다.  지정 된 VM SKU에 기반 하 여 [Azure 가상 머신 저장소 구성 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)게시 된 최소 저장소 레이아웃 권장 사항이 있습니다. 새 HANA VM에 대 한 적절 한 IO 용량 및 성능을 보장 하기 위해 이러한 최소 사양과 기존 HLI 시스템 통계 비교를 검토 하는 것이 좋습니다.

새 HANA VM 및 연결 된 서버에 대해 PPG를 구성 하는 경우 지원 티켓을 제출 하 여 저장소와 HANA VM의 위치를 검사 하 고 확인 합니다.  
백업 솔루션을 변경 해야 할 수 있으므로 운영 지출을 방지 하기 위해 저장소 비용도 검토 해야 합니다. 

### <a name="storage-replication-for-disaster-recovery"></a>재해 복구를 위한 저장소 복제
HLI에서 저장소 복제는 HANA 데이터베이스에 대 한 재해 복구 복제에 대 한 기본 옵션으로 제공 되었습니다. 이 기능은 Azure VM의 기본 옵션이 아닙니다. HSR, 백업/복원 또는 비즈니스 요구 사항을 충족 하는 기타 지원 되는 솔루션을 고려 합니다.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>가용성 집합, 가용성 영역 및 근접 배치 그룹 
응용 프로그램 계층 간의 거리를 최소화 하 SAP HANA 고 네트워크 대기 시간을 최소한으로 유지 해야 하는 경우 새 데이터베이스 VM 및 현재 SAP 응용 프로그램 서버는 근접 배치 그룹 (PPG)에 배치 해야 합니다. 자세한 내용은 [근접 배치 그룹](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) 을 참조 하세요. 자세한 내용은 Azure 가용성 집합 및 가용성 영역 SAP 배포용 ppg를 참조 하세요.
대상 HANA 시스템의 구성원이 둘 이상의 Azure 영역에 배포 된 경우 고객은 선택한 영역의 대기 시간 프로필을 명확 하 게 확인 해야 합니다. Sap 시스템 구성 요소의 배치는 SAP 응용 프로그램과 데이터베이스 간의 proximal 거리와 관련 하 여 가장 적합 합니다.  공용 도메인 [가용성 영역 대기 시간 테스트 도구](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) 를 사용 하면 측정을 보다 쉽게 수행할 수 있습니다.  


### <a name="backup-strategy"></a>Backup 전략
많은 고객이 HLI에서 SAP HANA에 대 한 타사 백업 솔루션을 이미 사용 하 고 있습니다.  이 경우 보호 된 VM 및 HANA 데이터베이스를 추가로 구성 해야 합니다.  마이그레이션 후 컴퓨터를 서비스 해제 하는 경우 현재에 대 한 진행 중인 HLI 백업 작업은 예약 되지 않습니다.
VM의 SAP HANA에 대 한 Azure backup은 이제 일반 공급 됩니다.  Azure Vm에서 백업, 복원, SAP HANA [백업 백업,](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) [복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [관리](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) 에 대 한 자세한 내용은 다음 링크를 참조 하세요.

### <a name="dr-strategy"></a>DR 전략
서비스 수준 목표가 blob 저장소에 대 한 간단한 백업의 복구 시간을 더 오래 수용 하 고 현재 위치가 나 새 VM으로 복원 하는 경우 가장 간단 하 고 비용이 저렴 한 DR 전략입니다.  
일반적으로 HSR를 사용 하 여 HANA DR이 수행 되는 large instance 플랫폼과 같습니다. Azure VM에서 HSR은 가장 자연스럽 고 기본적인 SAP HANA DR 솔루션 이기도 합니다.  원본 배포가 단일 인스턴스인지, 자동 장애 조치 (failover)를 사용 하거나 사용 하지 않는 클러스터형 인지 또는 다중 노드 스케일 아웃 HANA에 관계 없이 원본 인프라의 대상 HSR 복제본이 DR 지역에 필요 합니다. 이 HSR 대상 DR 복제본은 기본 HSR에서 VM으로의 마이그레이션이 완료 된 후에 설정 됩니다.  DR HANA 인스턴스는 VM 인스턴스의 기본 SAP HANA에 보조 복제 사이트로 등록 됩니다.  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 응용 프로그램 서버 연결 대상 변경
HSR 마이그레이션 접근 방식은 새 HANA DB 호스트와 응용 프로그램 계층에 대 한 새 DB 호스트 이름을 생성 하므로 새 호스트 이름을 반영 하도록 SAP 프로필을 수정 해야 합니다.  스위치를 이름 확인으로 호스트 이름을 확인 하 여 수행 하는 경우에는 프로필을 변경할 필요가 없습니다.

### <a name="operating-system"></a>운영 체제
HLI 및 VM에 대 한 운영 체제 이미지는 동일한 릴리스 수준 (예: SLES 11 SP4)에 있는 경우에도 동일 하지 않습니다. 고객은 HLI에서 필요한 패키지, 핫 픽스, 패치, 커널 및 보안 픽스를 확인 하 여 대상 VM의 운영 체제에 설치할 수 있도록 해야 합니다.  또한 고객이 SAP HSR 용 대상 VM에 최신 OS 버전을 구성 하는 것이 일반적입니다.  [SAP note 2763388](https://launchpad.support.sap.com/#/notes/2763388)에 따라 지원 됩니다.

### <a name="new-sap-license-request"></a>새 SAP 라이선스 요청
새 HANA 시스템에 새 SAP 라이선스를 요청 하는 간단한 호출로, 이제 Vm으로 마이그레이션 되었습니다.

### <a name="service-level-agreement-sla-differences"></a>SLA (서비스 수준 계약)의 차이점
저자는 HLI와 Azure VM 간의 가용성 SLA 차이를 확인 하는 것입니다.  예를 들어 clustered HLIs HA 쌍은 99.99%의 가용성을 제공 합니다. 동일한 SLA를 얻으려면 가용성 영역에서 Vm을 배포 해야 합니다. 이 [문서에서는](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) 고객이 대상 인프라를 적절 하 게 계획할 수 있도록 관련 배포 아키텍처를 사용 하는 가용성에 대해 설명 합니다.


## <a name="migration-strategy"></a>마이그레이션 전략
이 문서에서는 HLI에서 Azure VM으로 마이그레이션하기 위한 HANA 시스템 복제 접근 방법만 다룹니다.  배포 된 대상 저장소 솔루션에 따라 달라 지는 프로세스는 약간 다릅니다. 개략적인 단계는 아래에 설명 되어 있습니다.

### <a name="vm-with-premiumultra-disks-for-data"></a>데이터에 대 한 프리미엄/울트라 디스크가 있는 VM
프리미엄 또는 ultra disks를 사용 하 여 배포 된 Vm의 경우 표준 SAP HANA 시스템 복제 구성이 적용 되며 HSR를 설정 하는 데 사용할 수 있습니다.  참조 된 [SAP 도움말 문서](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) 에서는 보조 시스템으로 이동 하 고, 주 시스템으로 장애 복구 하 고, 시스템 복제를 사용 하지 않도록 설정 하는 등 두 시스템 간에 시스템 복제를 설정 하는 단계에 대 한 개요를 제공 합니다.  마이그레이션의 목적을 위해 HLI 원본 단계에서 시스템 복제를 수행 하 고 사용 하지 않도록 설정 하기만 하면 됩니다.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>데이터 및 로그 볼륨에 대해 ANF를 사용 하는 VM
높은 수준에서 전체 데이터 및 로그 볼륨의 최신 HLI 저장소 스냅숏은 대상 HANA VM에서 액세스 및 복구할 수 있는 Azure Storage에 복사 해야 합니다.  모든 네이티브 Linux 복사 도구를 사용 하 여 복사 프로세스를 수행할 수 있습니다.  

>[!Important]
> 복사 및 데이터 전송은 HANA 데이터베이스 크기 및 네트워크 대역폭에 따라 시간이 오래 걸릴 수 있습니다.  대량 복사 프로세스는 기본 HANA DB 가동 중지 시간 전에 수행 해야 합니다.

### <a name="mcos-to-mdc-conversion"></a>MCOS에서 MDC로 변환
단일 시스템 (MCOS) 배포 모델의 여러 구성 요소는 몇 가지 HLI 고객이 선택 했습니다.  동기는 이전 SAP HANA 버전의 MDC (다중 데이터베이스 컨테이너) 저장소 스냅숏 제한을 피하는 것 이었습니다.  MCOS 모델에서 여러 독립적인 SAP HANA 인스턴스는 하나의 HLI 블레이드에 누적 됩니다.  마이그레이션에 HSR를 사용 하면 각각 하나의 테 넌 트 DB를 사용 하는 여러 HANA Vm이 제대로 작동 합니다.  이 최종 상태는 고객이 accustom으로 제공 되는 것 보다 사용량이 많을 수록 된 가로를 사용 합니다.  SAP HANA 2.0 기본 배포를 사용 하는 경우 HSR 마이그레이션 후 [HANA 테 넌 트 이동을](https://launchpad.support.sap.com/#/notes/2472478) 수행 하는 것이 좋습니다.  이 프로세스는이 독립적인 HANA 데이터베이스를 단일 HANA 컨테이너 하나로 cotenants 넌 트에 통합 합니다.  

### <a name="application-layer-consideration"></a>응용 프로그램 계층 고려 사항
DB 서버는 SAP 시스템의 센터로 표시 됩니다.  모든 응용 프로그램 서버는 SAP HANA DB 근처에 위치 해야 합니다.  새 PPG를 사용 하는 경우에는 HANA VM이 필요할 수 있는 PPG로 기존 응용 프로그램 서버를 재배치할 수 있습니다.  이미 배포 템플릿을 사용할 수 있는 경우 새 응용 프로그램 서버를 구축 하는 것이 더 쉬울 수 있습니다.  
기존 응용 프로그램 서버와 새 HANA VM이 최적으로 배치 된 경우 추가 용량이 필요 하지 않으면 새 응용 프로그램 서버를 빌드할 필요가 없습니다.  
서비스 가용성을 향상 시키기 위해 새 인프라를 구축 하는 경우 기존 응용 프로그램 서버가 불필요 하 게 되어 종료 하 고 삭제 해야 합니다.
대상 VM 호스트 이름이 변경 되 고 HLI 호스트 이름과 다른 경우 SAP 응용 프로그램 서버 프로필은 새 호스트를 가리키도록 조정 해야 합니다.  HANA DB IP 주소만 변경 된 경우 새 HANA VM으로 들어오는 연결을 진행 하려면 DNS 레코드를 업데이트 해야 합니다.

### <a name="acceptance-test"></a>승인 테스트
HLI에서 VM으로 마이그레이션하는 것은 다른 유형의 마이그레이션과 비교할 때 데이터베이스 내용에 대 한 자료가 변경 되지 않지만 새로운 설치 프로그램의 주요 기능 및 성능 측면을 확인 하는 것이 좋습니다.  

### <a name="cutover-plan"></a>계획에 대 한 가공선
이 마이그레이션은 바로 앞에 있지만 기존 DB의 서비스 해제를 포함 합니다.  대체 (fallback)가 필요한 경우에는 연결 된 콘텐츠 및 백업 이미지를 사용 하 여 원본 시스템을 유지 하기 위한 신중한 계획이 중요 합니다.  좋은 계획은 speedier 반전을 제공 합니다.   


## <a name="post-migration"></a>마이그레이션 후
데이터 무결성을 유지 하기 위해 모든 HLI 종속 서비스 또는 연결을 안전 하 게 분리 하기 전 까지는 마이그레이션 작업을 수행 하지 않습니다.  또한 불필요 한 서비스를 종료 합니다.  이 섹션에서는 몇 가지 중요 한 항목을 호출 합니다.

### <a name="decommissioning-the-hli"></a>HLI 서비스 해제
HANA DB를 Azure VM으로 성공적으로 마이그레이션한 후에는 HLI DB에서 프로덕션 비즈니스 트랜잭션을 수행 하지 않습니다.  그러나 로컬 백업 데이터 보존 기간과 같은 기간 동안 HLI를 작동 하 고 실행 하는 것은 필요한 경우 speedier 데이터 복구를 보장 하는 안전한 방법입니다.  그런 다음 HLI 블레이드를 서비스 해제 해야 합니다.  이에 대 한 기술 외에도, 고객은 Microsoft와 계약상 하는 것이 좋습니다.  고객은 Microsoft 담당자에 게 문의 하 여 HLI 서비스 해제 프로세스를 수행 해야 합니다.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>HLI에 대해 구성 된 프록시 (예: Iptables, 이상 IP)를 제거 합니다. 
IPTables와 같은 프록시 서비스를 사용 하 여 온-프레미스 트래픽을 HLI로 라우팅하는 경우 VM으로 성공적으로 마이그레이션한 후에는 해당 서비스가 더 이상 필요 하지 않습니다.  그러나이 연결 서비스는 서비스 해제 항목에 설명 된 대로 계속 해 서 HLI 블레이드가 있는 한 유지 되어야 합니다.  HLI 블레이드가 완전히 서비스 해제 된 후이 서비스를 종료할 수 있습니다. 

### <a name="remove-global-reach-for-hli"></a>HLI에 대 한 Global Reach 제거 
Global Reach은 일반적으로 고객의 온-프레미스 트래픽을 기술 서비스 없이 직접 HLI 테 넌 트에 연결할 수 있는 HLI Express 경로를 사용 하 여 고객의 Express 경로 게이트웨이를 연결 하는 데 사용 됩니다.  IPTables 프록시 서비스의 경우와 같이 HLI 블레이드를 완전히 해제할 때까지 GlobalReach를 유지 해야 합니다.

### <a name="operating-system-subscription--movereuse"></a>운영 체제 구독-이동/재사용
VM 서버가 구현 되 고 HLI 블레이드가 서비스 해제 되 면 os 라이선스의 이중 지불을 방지 하기 위해 OS 구독을 교체 하거나 재사용할 수 있습니다.



## <a name="next-steps"></a>다음 단계
아래 문서를 참조하세요.
- [Azure에서 인프라 구성 및 작업을 SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)합니다.
- [Azure의 SAP 워크 로드: 계획 및 배포 검사 목록](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
