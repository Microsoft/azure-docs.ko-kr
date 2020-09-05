---
title: SAP 워크 로드에 대 한 Azure storage 유형
description: SAP 워크 로드에 대 한 Azure storage 유형 계획
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/23/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 819ac1f01cc182c79571de35ec0753f694dc7722
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653616"
---
# <a name="azure-storage-types-for-sap-workload"></a>SAP 워크로드에 대한 Azure Storage 형식
Azure에는 용량, 처리량, 대기 시간 및 가격이 크게 다른 많은 저장소 유형이 있습니다. 일부 저장소 유형은 또는 SAP 시나리오에 사용할 수 있는 제한 되지 않습니다. 반면, 몇 가지 Azure storage 유형은 특정 SAP 워크 로드 시나리오에 적합 하거나 최적화 되어 있습니다. 특히 SAP HANA의 경우 일부 Azure storage 유형은 SAP HANA 사용에 대해 인증을 받았습니다. 이 문서에서는 다양 한 유형의 저장소를 살펴보고 SAP 워크 로드 및 SAP 구성 요소에 대 한 기능 및 유용성을 설명 합니다.

이 문서 전체에서 사용 되는 단위에 대 한 설명을 참조 하세요. 공용 클라우드 공급 업체는 GiB ([Gid bibyte](https://en.wikipedia.org/wiki/Gibibyte)) 또는 TiB ([Tebibyte](https://en.wikipedia.org/wiki/Tebibyte) 를 기가바이트 또는 테라바이트 대신 크기 단위로)를 사용 하도록 이동 했습니다. 따라서 모든 Azure 설명서 및 prizing는 이러한 단위를 사용 합니다.  문서 전체에서 MiB, GiB 및 TiB 단위의 이러한 크기 단위를 독점적으로 참조 하 고 있습니다. MB, GB 및 TB를 사용 하 여 계획 해야 할 수 있습니다. 따라서 250 MiB/sec 처리량이 아닌 400 MiB/sec 처리량의 크기를 조정 해야 하는 경우 계산에서 약간의 차이를 염두에 두어야 합니다.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage 복원력

표준 HDD, 표준 SSD, Azure premium storage 및 울트라 디스크의 Microsoft Azure 저장소는 기본 VHD (OS 포함) 및 VM 연결 데이터 디스크나 Vhd를 세 개의 다른 저장소 노드에 있는 세 개의 복사본으로 유지 합니다. 저장소 노드 오류가 발생 하는 경우 다른 복제본으로 장애 조치 (failover) 하 고 새 복제본의 시드가 투명 합니다. 이러한 중복성의 결과로 여러 Azure 디스크에서 모든 종류의 저장소 중복성 계층을 사용할 필요는 **없습니다** . 이 사실을 LRS(로컬 중복 스토리지)라고 합니다. LRS는 Azure에서 이러한 유형의 저장소에 대 한 기본값입니다. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 는 다른 네이티브 Azure 저장소와 동일한 sla를 얻기 위해 충분 한 중복성을 제공 합니다.

Azure에서 제공 해야 하는 다양 한 저장소 형식 중 일부에 적용 되는 [복제 Azure Storage](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 문서에 설명 된 여러 가지 중복성 방법이 있습니다. 

### <a name="azure-managed-disks"></a>Azure managed disks

관리 디스크는 Azure Storage 계정에 저장 된 Vhd 대신 사용할 수 있는 Azure Resource Manager의 리소스 유형입니다. Managed Disks 연결 된 가상 컴퓨터의 [가용성 집합] [가상-컴퓨터-관리 가용성]에 자동으로 맞춰집니다. 따라서 가상 컴퓨터와 가상 컴퓨터에서 실행 되는 서비스의 가용성을 향상 시킬 수 있습니다. 자세한 내용은 [개요 문서](../../managed-disks-overview.md)를 참조하세요.

복원 력과 관련 된이 예제에서는 관리 디스크의 장점을 보여 줍니다.

- Azure 가용성 집합에서 SAP 시스템에 대 한 두 개의 DBMS Vm을 배포 하 고 있습니다. 
- Azure에서 Vm을 배포 하면 OS 이미지를 포함 하는 디스크가 다른 저장소 클러스터에 배치 됩니다. 이렇게 하면 두 Vm이 단일 Azure storage 클러스터의 문제로 영향을 받지 않습니다.
- 데이터베이스의 데이터 및 로그 파일을 저장 하기 위해 이러한 Vm에 할당 하는 새 관리 디스크를 만들 때 두 Vm에 대 한 이러한 새 디스크는 별도의 저장소 클러스터에도 배포 되므로 첫 번째 VM의 디스크는 두 번째 VM의 디스크와 저장소 클러스터를 공유 하지 않습니다.

관리 디스크를 사용 하지 않고 고객 정의 저장소 계정에 배포 하는 경우 디스크 할당은 임의로 지정 되며 복원 력을 위해 AvSet 내에 Vm을 배포 한다는 사실을 인식 하지 못합니다.

> [!NOTE]
> 이러한 이유로, 관리 디스크를 통해 독점적으로 사용할 수 있는 몇 가지 다른 향상 된 기능을 제공 하려면 디스크에 대해 Azure 블록 저장소를 사용 하는 Vm의 새 배포 (Azure NetApp Files를 제외한 모든 Azure storage)에서 기본 VHD/OS 디스크, SAP 데이터베이스 파일이 포함 된 데이터 디스크에 대해 Azure managed disks를 사용 해야 합니다. 가용성 집합을 통해 Vm을 배포 하는지 여부에 관계 없이 가용성 영역 또는 집합 및 영역에 독립적입니다. 백업을 저장 하는 데 사용 되는 디스크는 관리 디스크에 반드시 필요한 것은 아닙니다.

> [!NOTE]
> Azure managed disks는 로컬 중복성 (LRS)만 제공 합니다. 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP 워크 로드를 사용 하는 저장소 시나리오
지속형 저장소는 Azure에서 배포 하는 스택의 다양 한 구성 요소에서 SAP 워크 로드에 필요 합니다. 이러한 시나리오는 최소한 다음과 같이 나열 됩니다.

- 운영 체제 및 해당 디스크에 설치 하는 기타 소프트웨어를 보관 하는 VM의 기본 VHD입니다. 이 디스크/VHD는 VM의 루트입니다. 변경 내용을 저장 해야 합니다. 따라서 다음에 VM을 중지 하 고 다시 시작 하면 이전에 적용 된 모든 변경 내용이 유지 됩니다. 특히 VM이 원래 실행 되는 것이 아니라 Azure에서 다른 호스트로 배포 되는 경우
- 지속형 데이터 디스크. 이러한 디스크는 응용 프로그램 데이터를 저장 하기 위해 연결 하는 Vhd입니다. 이 응용 프로그램 데이터는 데이터베이스, 백업 파일 또는 소프트웨어 설치의 데이터 및 로그/다시 실행 파일 일 수 있습니다. 는 운영 체제를 포함 하는 기본 VHD 이외의 모든 디스크를 의미 합니다.
- NetWeaver 또는 S/4HANA에 대 한 전역 전송 디렉터리를 포함 하는 공유 디스크 또는 파일 공유 이러한 공유의 콘텐츠는 여러 Vm에서 실행 되는 소프트웨어에서 사용 되거나 고가용성 장애 조치 (failover) 클러스터 시나리오를 작성 하는 데 사용 됩니다.
- EDI 프로세스 또는 이와 유사한/sapmnt 디렉터리 또는 공통 파일 공유 이러한 공유의 콘텐츠는 여러 Vm에서 실행 되는 소프트웨어에서 사용 되거나 고가용성 장애 조치 (failover) 클러스터 시나리오를 작성 하는 데 사용 됩니다.

다음 섹션에서는 위의 네 가지 시나리오에 적용 되는 다양 한 Azure storage 유형 및 SAP 워크 로드에 대 한 유용성을 설명 합니다. 다양 한 Azure storage 유형을 사용 해야 하는 방법에 대 한 일반적인 분류는 [azure에서 사용할 수 있는 디스크 유형](../../disks-types.md)문서에 설명 되어 있습니다. SAP 워크 로드에 대해 서로 다른 Azure storage 유형을 사용 하기 위한 권장 사항은 majorly 되지 않습니다.

S/4HANA의 SAP NetWeaver/응용 프로그램 계층에 대 한 Azure storage 형식에 대 한 지원 제한 사항은 [sap support note 2015553](https://launchpad.support.sap.com/#/notes/2015553) for SAP HANA 인증 및 지원 되는 azure 저장소 형식에 대 한 자세한 내용은 [azure 가상 컴퓨터 저장소 구성 SAP HANA](./hana-vm-operations-storage.md)문서를 참조 하세요.

다른 Azure storage 유형을 설명 하는 섹션에서는 SAP 지원 저장소를 사용 하는 제한 사항 및 가능성에 대 한 배경 정보를 제공 합니다. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP storage 시나리오에 대 한 저장소 권장 사항
세부 정보를 살펴보기 전에 문서의 시작 부분에 이미 요약 및 권장 사항을 제공 하 고 있습니다. 반면 특정 유형의 Azure storage에 대 한 세부 정보는 문서의이 섹션에 나와 있습니다. 테이블에서 SAP storage 시나리오에 대 한 저장소 권장 사항을 요약 하면 다음과 같습니다.

| 사용 시나리오 | 표준 HDD | 표준 SSD | Premium Storage | Ultra disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| OS 디스크 | 적합 하지 않음 |  제한 된 적합 (비-prod) | 권장 | 가능 하지 않음 | 가능 하지 않음 |
| 전역 전송 디렉터리 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장 | 권장 |
| /sapmnt | 적합 하지 않음 | 제한 된 적합 (비-prod) | 권장 | 권장 | 권장 |
| DBMS 데이터 볼륨 SAP HANA M/Mv2 VM 제품군 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장 | 권장<sup>2</sup> |
| M/Mv2 VM 제품군 SAP HANA DBMS 로그 볼륨 | 지원되지 않음 | 지원되지 않음 | 권장<sup>1</sup> | 권장 | 권장<sup>2</sup> | 
| Esv3/Edsv4 VM 제품군 SAP HANA DBMS 데이터 볼륨 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장 | 권장<sup>2</sup> |
| Esv3/Edsv4 VM 제품군 SAP HANA DBMS 로그 볼륨 | 지원되지 않음 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장<sup>2</sup> | 
| DBMS 데이터 볼륨 비 HANA | 지원되지 않음 | 제한 된 적합 (비-prod) | 권장 | 권장 | 지원되지 않음 |
| DBMS 로그 볼륨 비 HANA M/Mv2 VM 제품군 | 지원되지 않음 | 제한 된 적합 (비-prod) | 권장<sup>1</sup> | 권장 | 지원되지 않음 |
| DBMS 로그 볼륨 비-M o M/Mv2 VM 제품군 | 지원되지 않음 | 제한 된 적합 (비-prod) | 최대 중간 규모의 워크 로드에 적합 | 권장 | 지원되지 않음 |


<sup>1</sup> 로그/다시 실행 로그 볼륨에 대 한 M/Mv2 VM 제품군에 대해 [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md) 를 <sup>사용 하는</sup> 경우 anf를 사용 하려면/hana/data와/hana/log를 사용 해야 합니다. 

다음과 같은 다양 한 저장소 유형 목록에서 짐작할 수 있는 특징이 있습니다.

| 사용 시나리오 | 표준 HDD | 표준 SSD | Premium Storage | Ultra disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| 처리량/IOPS SLA | no | 아니요 | 예 | 예 | 예 |
| 대기 시간 읽기 | high | 중간에서 높음 | low | 하위 밀리초 | 하위 밀리초 |
| 대기 시간 쓰기 | high | 중간에서 높음  | 낮음 (1-밀리초<sup>1</sup>) | 하위 밀리초 | 하위 밀리초 |
| HANA 지원 | no | 아니요 | 예<sup>1</sup> | 예 | 예 |
| 디스크 스냅숏 가능 | 예 | 예 | 예 | 아니요 | 예 |
| 가용성 집합을 사용 하는 경우 다른 저장소 클러스터에 디스크 할당 | 관리 디스크를 통해 | 관리 디스크를 통해 | 관리 디스크를 통해 | 가용성 집합을 통해 배포 된 Vm에서 지원 되지 않는 디스크 유형 | 아니요<sup>3</sup> |
| 가용성 영역 정렬 | 예 | 예 | 예 | 예 | Microsoft 참여 요구 |
| 영역 중복성 | 관리 디스크가 아닙니다. | 관리 디스크가 아닙니다. | 관리 디스크가 아닙니다. | 아니요 | 아니요 |
| 지리적 중복성 | 관리 디스크가 아닙니다. | 관리 디스크가 아닙니다. | 아니요 | 아니요 | 아니요 |


<sup>1</sup> 로그/다시 실행 로그 볼륨에 대 한 M/Mv2 VM 제품군에 대해 [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md) 사용

<sup>2</sup> 비용은 프로 비전 된 IOPS 및 처리량에 따라 달라 집니다.

<sup>3</sup> 다른 anf 용량 풀을 만드는 것은 여러 저장소 단위로 용량 풀을 배포 하는 것을 보장 하지 않습니다.


> [!IMPORTANT]
> Azure NetApp Files (ANF)를 사용 하 여 1 밀리초 미만의 i/o 대기 시간을 얻으려면 Microsoft와 협업 하 여 ANF를 기반으로 하는 Vm과 NFS 공유 간의 올바른 배치를 정렬 해야 합니다. 지금까지 배포 된 VM과 ANF에 호스트 된 NFS 볼륨 간에 자동으로 근접 하는 메커니즘이 없습니다. 서로 다른 Azure 지역을 다르게 설정 하는 경우 VM과 NFS 공유가 근접 하 게 할당 되지 않은 경우 추가 된 네트워크 대기 시간이 1 밀리초를 초과 하 여 i/o 대기 시간을 푸시할 수 있습니다.


> [!IMPORTANT]
> 현재 제공 되는 Azure 블록 저장소를 기반으로 하는 관리 디스크 또는 Azure NetApp Files 영역 또는 지리적 중복성을 제공 하지 않습니다. 결과적으로, 고가용성 및 재해 복구 아키텍처가 이러한 관리 디스크, NFS 또는 SMB 공유에 대 한 모든 유형의 Azure native storage 복제를 사용 하지 않는지 확인 해야 합니다.


## <a name="azure-premium-storage"></a>Azure premium storage
Azure premium SSD 저장소는 다음을 제공 하는 목표로 도입 되었습니다.

* 낮은 i/o 대기 시간
* IOPS 및 처리량에 대 한 Sla
* I/o 대기 시간의 변동 감소

이 유형의 저장소는 DBMS 워크 로드를 대상으로 하며, 작은 숫자 밀리초의 대기 시간을 요구 하는 저장소 트래픽과, Azure premium storage의 경우 IOPS 및 처리량 비용에 대 한 Sla는 해당 디스크에 저장 된 실제 데이터 볼륨이 아니라 디스크 내에 저장 된 데이터의 크기에 관계 없이 이러한 디스크의 크기 범주에 해당 합니다. [프리미엄 SSD](../../disks-types.md#premium-ssd)문서에 표시 된 크기 범주에 직접 매핑되지 않는 디스크를 premium storage에 만들 수도 있습니다. 이 문서의 결론은 다음과 같습니다.

- 저장소는 범위로 구성 됩니다. 예를 들어 513 GiB ~ 1024 GiB 용량 범위의 디스크는 동일한 기능 및 동일한 월별 비용을 공유 합니다.
- GiB 당 IOPS는 크기 범주에 걸쳐 선형으로 추적 되지 않습니다. 32 GiB 미만의 작은 디스크는 GiB 당 IOPS 속도가 더 높습니다. 32 GiB ~ 1024 GiB 보다 큰 디스크의 경우 GiB 당 IOPS 요금은 GiB 당 4-5 IOPS 사이입니다. 32767 GiB 큰 디스크의 경우 GiB 당 IOPS 속도는 1이 됩니다.
- 이 저장소에 대 한 i/o 처리량은 디스크 범주의 크기와는 선형이 아닙니다. 65 GiB 및 128 GiB 용량의 범주와 같이 작은 디스크의 처리량은 약 780KB/GiB입니다. 32767 GiB 디스크와 같은 매우 큰 디스크의 경우 처리량은 약 28KB b/GiB입니다.
- IOPS 및 처리량 Sla는 디스크 용량을 변경 하지 않고는 변경할 수 없습니다.

Azure에는 Azure premium storage 또는 Azure Ultra disk 저장소 사용에 연결 된 99.9%의 단일 인스턴스 VM SLA가 있습니다. SLA는 [Virtual Machines에 대 한 sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/)에 설명 되어 있습니다. 이 단일 VM SLA를 준수 하기 위해 기본 VHD 디스크 및 연결 된 **모든** 디스크는 azure premium Storage 또는 azure Ultra disk 저장소 여야 합니다.

SAP 워크 로드에 대 한 기능 행렬은 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 기본 VHD | 낫지만 | 모든 시스템 |
| 데이터 디스크 | 낫지만 | 모든 시스템- [특히 SAP HANA](../../how-to-enable-write-accelerator.md) |
| SAP 전역 전송 디렉터리 | YES | [지원됨](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 낫지만 | 모든 시스템 |
| 백업 저장소 | 낫지만 | 백업에 대 한 단기 저장 |
| 공유/공유 디스크 | 사용할 수 없음 | Azure Premium 파일 또는 제 3 자가 필요 합니다. |
| 복원력 | LRS | GRS 또는 ZRS를 디스크에 사용할 수 없음 |
| 대기 시간 | 낮음-보통 | - |
| IOPS SLA | YES | - |
| IOPS 선형-용량 | 대괄호 안의 반 선형  | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 디스크당 최대 IOPS | 2만 [디스크 크기에 종속](https://azure.microsoft.com/pricing/details/managed-disks/) | [VM 제한](../../sizes.md) 도 고려 |
| 처리량 SLA | YES | - |
| 처리량 선형-용량 | 대괄호 안의 반 선형 | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 인증 | YES | [특별히 SAP HANA](../../how-to-enable-write-accelerator.md) |
| 디스크 스냅숏 가능 | YES | - |
| Azure Backup VM 스냅숏 가능 | YES | [쓰기 가속기](../../how-to-enable-write-accelerator.md) 캐시 된 디스크 제외  |
| 비용 | MEDIUM | - |

Azure premium storage는 Azure premium storage와 함께 제공 되는 일반적인 캐싱 유형을 사용 하는 SAP HANA 저장소 대기 시간 Kpi를 충족 하지 않습니다. SAP HANA 로그 쓰기에 대 한 저장소 대기 시간 Kpi를 충족 하기 위해 [쓰기 가속기 사용](../../how-to-enable-write-accelerator.md)문서에 설명 된 대로 Azure 쓰기 가속기 캐싱을 사용 해야 합니다. Azure 쓰기 가속기는 트랜잭션 로그 쓰기 및 다시 실행 로그 쓰기에 대해 다른 모든 DBMS 시스템을 활용 합니다. 따라서 모든 SAP DBMS 배포에서 사용 하는 것이 좋습니다. SAP HANA azure premium storage와 함께 Azure 쓰기 가속기를 사용 하는 것은 필수입니다.



**요약:** Azure premium storage는 SAP 워크 로드에 권장 되는 Azure storage 유형 중 하나입니다. 이 권장 사항은 프로덕션이 아닌 프로덕션 시스템에도 적용 됩니다. Azure premium storage는 데이터베이스 워크 로드를 처리 하는 데 적합 합니다. Azure 쓰기 가속기을 사용 하 여 Azure premium 디스크에 대 한 쓰기 대기 시간을 크게 향상 시킬 수 있습니다. 그러나 높은 IOPS 및 처리량 속도를 갖춘 DBMS 시스템의 경우에는 저장소 용량을 과도 하 게 프로 비전 하거나 Linux의 논리 볼륨 관리자와 같은 기능을 사용 하 여 한 쪽에서 원하는 용량을 제공 하는 스트라이프 세트를 구축 해야 하며, 필요한 IOPS 나 처리량은 최고 비용 효율적으로 사용 해야 합니다.


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium storage에 대 한 Azure 버스트 기능
Azure premium storage 디스크의 용량을 512 GiB 하는 경우 버스트 기능이 제공 됩니다. 디스크 버스트 작동 방식에 대 한 정확한 방법은 [디스크 버스트](../../linux/disk-bursting.md)문서에 설명 되어 있습니다. 이 문서를 읽으면 i/o 워크 로드가 디스크의 공칭 IOPS 및 처리량 보다 낮은 시간에 발생 IOPS 및 처리량의 개념을 이해 하 게 됩니다 (명목상 처리량에 대 한 자세한 내용은 [관리 되는 디스크 가격](https://azure.microsoft.com/pricing/details/managed-disks/)참조). 현재 사용량과 디스크의 명목상 값 사이에 IOPS 및 처리량의 변화량을 계산 하려고 합니다. 버스트는 최대 30 분으로 제한 됩니다.

이 버스트 기능이 계획 될 수 있는 이상적인 사례는 다른 DBMS에 대 한 데이터 파일을 포함 하는 볼륨이 나 디스크가 될 가능성이 높습니다. 이러한 볼륨에 대해 예상 되는 i/o 워크 로드, 특히 중소 규모의 시스템은 다음과 같습니다.

- 데이터를 메모리에 캐시 하는 것이 가장 좋지만 HANA의 경우와 마찬가지로 메모리에 완전히 있어야 하기 때문에 낮은 수준의 읽기 작업
- 정기적으로 실행 되는 데이터베이스 검사점 또는 저장점에 의해 트리거되는 쓰기 버스트
- 저장소 스냅숏을 통해 백업이 실행 되지 않는 경우 연속 스트림에서 읽는 백업 워크 로드
- SAP HANA 인스턴스를 다시 시작한 후에 메모리에 데이터를 로드 합니다.

특히 워크 로드에서 초당 몇 백 개의 트랜잭션을 처리 하는 소규모 DBMS 시스템에서 트랜잭션 또는 다시 실행 로그를 저장 하는 디스크 또는 볼륨에 대해 버스트 기능을 사용할 수 있습니다. 이러한 디스크 또는 볼륨에 대해 예상 되는 작업은 다음과 같습니다.

- 응용 프로그램에서 발급 한 모든 커밋이 i/o 작업을 트리거할 가능성이 있으므로 작업 및 워크 로드의 특성에 따라 달라 지는 디스크에 대 한 일반 쓰기
- 인덱스를 만들거나 다시 작성 하는 것과 같은 운영 작업의 사례에 대 한 처리량의 높은 작업
- 트랜잭션 로그 또는 다시 실행 로그 백업을 수행할 때의 읽기 버스트


## <a name="azure-ultra-disk"></a>Azure Ultra disk
Azure ultra disks는Azure IaaS VM에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. 울트라 디스크의 몇 가지 추가 이점에는 VM (가상 머신)을 다시 시작할 필요 없이 워크 로드와 함께 디스크의 IOPS 및 처리량을 동적으로 변경 하는 기능이 포함 됩니다. 울트라 디스크는 SAP DBMS 워크 로드와 같은 데이터를 많이 사용 하는 워크 로드에 적합 합니다. Ultra disks는 데이터 디스크로만 사용할 수 있으며 운영 체제를 저장 하는 기본 VHD 디스크로 사용할 수 없습니다. Azure premium storage를 기반 VHD 디스크로 사용 하는 것이 좋습니다.

울트라 디스크를 만들 때 3 개의 차원을 정의할 수 있습니다.

- 디스크의 용량입니다. 범위는 4 GiB ~ 65536 GiB
- 디스크에 대해 프로 비전 된 IOPS입니다. 최대 값은 디스크 용량에 적용 됩니다. 자세한 내용은 [Ultra disk](../../disks-types.md#ultra-disk) 문서를 참조 하세요.
- 프로 비전 된 저장소 대역폭. 디스크 용량에 따라 최대 대역폭이 다르게 적용 됩니다. 자세한 내용은 [Ultra disk](../../disks-types.md#ultra-disk) 문서를 참조 하세요.

단일 디스크의 비용은 특정 디스크에 대해 개별적으로 정의할 수 있는 3 차원에 따라 결정 됩니다. 


SAP 워크 로드에 대 한 기능 행렬은 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 기본 VHD | 작동 하지 않음 | - |
| 데이터 디스크 | 낫지만 | 모든 시스템  |
| SAP 전역 전송 디렉터리 | YES | [지원됨](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 낫지만 | 모든 시스템 |
| 백업 저장소 | 낫지만 | 백업에 대 한 단기 저장 |
| 공유/공유 디스크 | 사용할 수 없음 | 타사 필요 |
| 복원력 | LRS | GRS 또는 ZRS를 디스크에 사용할 수 없음 |
| 대기 시간 | 매우 낮음 | - |
| IOPS SLA | YES | - |
| IOPS 선형-용량 | 대괄호 안의 반 선형  | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 디스크당 최대 IOPS | 1200 ~ 16만 | 디스크 용량에 따라 달라 집니다. |
| 처리량 SLA | YES | - |
| 처리량 선형-용량 | 대괄호 안의 반 선형 | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 인증 | YES | - |
| 디스크 스냅숏 가능 | 아니요 | - |
| Azure Backup VM 스냅숏 가능 | 아니요 | - |
| 비용 | Premium storage 보다 높음 | - |



**요약:** Azure ultra disks는 모든 종류의 SAP 워크 로드에 대 한 대기 시간이 짧은 적절 한 저장소입니다. 지금까지 울트라 디스크는 가용성 영역 (영역 배포)을 통해 배포 된 Vm과 조합 하 여 사용할 수 있습니다. 이 시점에서 Ultra disk는 저장소 스냅숏을 지원 하지 않습니다. 다른 모든 저장소와 반대 되는 경우에는 기본 VHD 디스크에 대해 Ultra disk를 사용할 수 없습니다. 울트라 디스크는 i/o 작업이 많은 공간을 차지 하 고 대역폭 및 IOPS의 최대 사용량에 맞게 크기를 조정 하는 대신 배포 된 저장소 처리량 또는 IOPS를 저장소 워크 로드 패턴에 맞게 조정 하는 경우에 적합 합니다.


## <a name="azure-netapp-files-anf"></a>Azure NetApp 파일 (ANF)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 는 높은 수준의 AZURE 기본 NFS 및 SMB 공유를 제공 하는 목표를 가진 Microsoft와 netapp의 공동 작업의 결과입니다. 이에 대 한 강조는 DBMS 배포 시나리오를 가능 하 게 하는 높은 대역폭과 짧은 대기 시간 저장소를 제공 하는 것입니다. 시간이 지남에 따라 Azure를 통해 NetApp storage의 일반적인 작동 기능도 가능 합니다. NFS/SMB 공유는 저장소 처리량과 가격을 구분 하는 세 가지 서비스 수준으로 제공 됩니다. 서비스 수준은 [Azure NetApp Files의 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)문서에 설명 되어 있습니다. 여러 유형의 SAP 워크 로드에 대해 다음과 같은 서비스 수준을 권장 합니다.

- SAP DBMS 워크 로드: 성능, 이상적인 Ultra
- SAPMNT share: 성능, 이상적인 Ultra
- 전역 전송 디렉터리: 성능, 이상적인 Ultra

> [!NOTE]
> 최소 프로 비전 크기는 용량 풀 이라고 하는 4 개의 TiB unit입니다. 그런 다음이 용량 풀에서 볼륨을 만듭니다. 반면에 빌드할 수 있는 가장 작은 볼륨은 100 GiB입니다. TiB 단계에서 용량 풀을 확장할 수 있습니다. 가격 책정에 대 한 자세한 내용은 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 을 참조 하세요.

ANF 저장소는 현재 몇 가지 SAP 워크 로드 시나리오에 대해 지원 됩니다.

- SAP의 글로벌 전송 디렉터리에 대 한 SMB 또는 NFS 공유 제공
- 에 설명 된 대로 공유는 고가용성 시나리오에서 sapmnt 합니다.
    - [SAP 응용 프로그램용 SMB (Azure NetApp Files)를 사용 하는 Windows의 Azure Vm에서 SAP NetWeaver에 대 한 고가용성](./high-availability-guide-windows-netapp-files-smb.md)
    - [SAP 애플리케이션용 Azure NetApp Files를 사용하여 SUSE Linux Enterprise Server에서 Azure VM의 SAP NetWeaver 고가용성 실현](./high-availability-guide-suse-netapp-files.md)
    - [SAP 응용 프로그램에 대해 Azure NetApp Files을 사용 하는 Red Hat Enterprise Linux에서 SAP NetWeaver에 대 한 Azure Virtual Machines 고가용성](./high-availability-guide-rhel-netapp-files.md)
- [Azure 가상 머신 저장소 구성 SAP HANA](./hana-vm-operations-storage.md) 문서에 설명 된 대로/hana/data 및/hana/log 볼륨 및/또는 nfs v 4.1에 대 한 nfs v 4.1 공유를 사용 하는 SAP HANA 배포 및/hana/shared 볼륨의 nfs v3 볼륨

> [!NOTE]
> Azure NetApp Files 기반 NFS 또는 SMB 공유에 대해서는 다른 DBMS 작업을 지원 하지 않습니다. 변경 되는 경우 업데이트 및 변경 내용이 제공 됩니다.

Azure premium storage를 사용 하는 것 처럼 GB 당 고정 또는 선형 처리량 크기는 최소 처리량을 충족 해야 하는 경우 문제가 될 수 있습니다. 이는 SAP HANA의 경우와 같습니다. ANF를 사용 하 여이 문제는 Azure premium disk를 사용 하는 것 보다 더 두드러지게 될 수 있습니다. Azure premium disk의 경우 GiB 당 비교적 높은 처리량을 가진 몇 개의 작은 디스크를 사용 하 여 비용 효율적이 고 처리량이 더 높을 수 있습니다. 이러한 종류의 스트라이프는 ANF에 호스트 된 NFS 또는 SMB 공유에 대해 작동 하지 않습니다. 이러한 제한으로 인해 다음과 같은 과도 한 용량이 배포 되었습니다.

- 예를 들어, ANF에 호스트 된 NFS 볼륨에서 250 MiB/sec의 처리량을 얻으려면 Ultra service 수준의 1.95 TiB 용량을 배포 해야 합니다. 
- 400 MiB/sec를 실현 하려면 3.125 TiB 용량을 배포 해야 합니다. 하지만 볼륨에 필요한 처리량을 얻으려면 용량을 과도 하 게 프로 비전 해야 할 수 있습니다. 이 과도 한 용량 프로 비전은 작은 HANA 인스턴스의 가격 책정에 영향을 줍니다. 
- SAP/sapmnt 디렉터리에 대 한 ANF의 맨 위에서 NFS를 사용 하는 공간에서 일반적으로 Azure NetApp Files에 의해 적용 되는 100 150 GiB GiB의 최소 용량을 사용 합니다. 그러나 고객 환경에서는 12.8 MiB/sec의 관련 처리량 (Ultra service 수준 사용)이 부족 하 여 SAP 시스템의 안정성에 부정적인 영향을 줄 수 있음을 보여 주었습니다. 이러한 경우 고객은/sapmnt 볼륨의 볼륨을 늘려서 문제를 방지할 수 있으므로 해당 볼륨에 더 많은 처리량이 제공 됩니다.

SAP 워크 로드에 대 한 기능 행렬은 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 기본 VHD | 작동 하지 않음 | - |
| 데이터 디스크 | 낫지만 | SAP HANA만  |
| SAP 전역 전송 디렉터리 | YES | SMB 및 NFS |
| SAP sapmnt | 낫지만 | 모든 시스템 SMB (Windows에만 해당) 또는 NFS (Linux만 해당) |
| 백업 저장소 | 낫지만 | - |
| 공유/공유 디스크 | YES | SMB 3.0, NFS v3 및 NFS v 4.1 |
| 복원력 | LRS | GRS 또는 ZRS를 디스크에 사용할 수 없음 |
| 대기 시간 | 매우 낮음 | - |
| IOPS SLA | YES | - |
| IOPS 선형-용량 | 엄격 하 게 선형  | [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 에 따라 달라 집니다. |
| 처리량 SLA | YES | - |
| 처리량 선형-용량 | 대괄호 안의 반 선형 | [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 에 따라 달라 집니다. |
| HANA 인증 | YES | - |
| 디스크 스냅숏 가능 | YES | - |
| Azure Backup VM 스냅숏 가능 | 아니요 | - |
| 비용 | Premium storage 보다 높음 | - |


ANF 저장소의 추가 기본 제공 기능:

- 볼륨의 스냅숏을 수행 하는 기능
- 스냅숏에서 ANF 볼륨 복제
- 스냅숏에서 볼륨 복원 (맞춤 되돌리기)

**요약**: AZURE NETAPP FILES는 NFS 및 SMB 볼륨이 나 공유를 배포할 수 있도록 하는 HANA 인증 짧은 대기 시간 저장소입니다. 저장소에는 볼륨의 GiB 용량 당 선형 방식으로 다양 한 처리량과 IOPS를 제공 하는 세 가지 서비스 수준이 제공 됩니다. ANF 저장소는 대기 노드를 사용 하 여 SAP HANA 스케일 아웃 시나리오를 배포할 수 있도록 합니다. 저장소는/sapmnt 또는 SAP 전역 전송 디렉터리에 필요한 파일 공유를 제공 하는 데 적합 합니다. ANF 저장소는 네이티브 NetApp 기능으로 사용할 수 있는 기능 가용성과 함께 제공 됩니다.  



## <a name="azure-standard-ssd-storage"></a>Azure 표준 SSD 저장소
Azure standard HDD 저장소에 비해 Azure standard SSD 저장소는 더 나은 가용성, 일관성, 안정성 및 대기 시간을 제공 합니다. 낮은 IOPS 수준에서 일관 된 성능을 필요로 하는 워크 로드에 최적화 되어 있습니다. 이 저장소는 IOPS 및 처리량 요구가 낮은 비프로덕션 SAP 시스템에 사용 되는 최소 저장소입니다. SAP 워크 로드에 대 한 기능 행렬은 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 기본 VHD | 적절 한 제한 됨 | 비프로덕션 시스템 |
| 데이터 디스크 | 적절 한 제한 됨 | 낮은 IOPS 및 대기 시간 요구가 있는 일부 비프로덕션 시스템 |
| SAP 전역 전송 디렉터리 | 아니요 | [지원 안 됨](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 적절 한 제한 됨 | 비프로덕션 시스템 |
| 백업 저장소 | 낫지만 | - |
| 공유/공유 디스크 | 사용할 수 없음 | 타사 필요 |
| 복원력 | LRS, GRS | 디스크에 사용할 수 있는 ZRS 없음 |
| 대기 시간 | high | SAP 전역 전송 디렉터리 또는 프로덕션 시스템에 대해 너무 높음 |
| IOPS SLA | 아니요 | - |
| 디스크당 최대 IOPS | 500 | 디스크 크기와 무관 |
| 처리량 SLA | 아니요 | - |
| HANA 인증 | 아니요 | - |
| 디스크 스냅숏 가능 | YES | - |
| Azure Backup VM 스냅숏 가능 | YES | - |
| 비용 | LOW | - |



**요약:** Azure standard SSD 저장소는 기본 VHD에 대해 비프로덕션 Vm에 대 한 최소 권장 사항입니다. 상대적 대기 시간이 낮은 대기 시간 및/또는 낮은 IOPS 및 처리량 속도를 포함 하는 최종 DBMS 배포입니다. 이 Azure storage 유형은 더 이상 SAP Global 전송 디렉터리 호스팅을 지원 하지 않습니다. 



## <a name="azure-standard-hdd-storage"></a>Azure 표준 HDD 저장소
Azure 표준 HDD 저장소는 2014 년에 SAP NetWeaver 워크 로드에 대 한 인증을 받은 경우 유일한 저장소 유형 이었습니다. 2014 년에 Azure virtual machines는 작고 저장소 처리량이 적습니다. 따라서이 저장소 유형은 요구를 충족 하기만 하면 됩니다. 저장소는 대기 시간을 구분 하지 않는 워크 로드에 이상적 이며 SAP 공간에서 경험을 경험 하지 않습니다. Azure Vm의 처리량이 증가 하 고 이러한 Vm이 생성 되는 워크 로드가 증가 함에 따라서이 저장소 유형은 SAP 시나리오에서 더 이상 사용 되는 것으로 간주 되지 않습니다. SAP 워크 로드에 대 한 기능 행렬은 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 기본 VHD | 적합 하지 않음 | - |
| 데이터 디스크 | 적합 하지 않음 | - |
| SAP 전역 전송 디렉터리 | 아니요 | [지원 안 됨](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 아니요 | 지원되지 않음 |
| 백업 저장소 | 낫지만 | - |
| 공유/공유 디스크 | 사용할 수 없음 | Azure Files 또는 타사 필요 |
| 복원력 | LRS, GRS | 디스크에 사용할 수 있는 ZRS 없음 |
| 대기 시간 | high | DBMS 사용, SAP 전역 전송 디렉터리 또는 sapmnt/saploc에 대해 너무 높음 |
| IOPS SLA | 아니요 | - |
| 디스크당 최대 IOPS | 500 | 디스크 크기와 무관 |
| 처리량 SLA | 아니요 | - |
| HANA 인증 | 아니요 | - |
| 디스크 스냅숏 가능 | YES | - |
| Azure Backup VM 스냅숏 가능 | YES | - |
| 비용 | LOW | - |



**요약:** 표준 HDD은 SAP 백업을 저장 하는 데만 사용 해야 하는 Azure storage 유형입니다. 여기서는 데이터를 조회 하는 데 사용 되는 사용 중지 된 시스템과 같은 비활성 시스템의 기본 VHD로만 사용 해야 합니다. 하지만 활성 개발, QA 또는 프로덕션 Vm은 해당 저장소를 기반으로 해야 합니다. 해당 저장소에서 데이터베이스 파일이 호스트 되 고 있지 않아야 합니다.


## <a name="azure-vm-limits-in-storage-traffic"></a>저장소 트래픽의 Azure VM 제한
온-프레미스 시나리오와 반대로, 선택 하는 개별 VM 유형은 달성할 수 있는 저장소 대역폭에서 중요 한 역할을 담당 합니다. 다른 저장소 유형에 대해 다음 사항을 고려해 야 합니다.

| 스토리지 유형| Linux | Windows | 의견 |
| --- | --- | --- | --- |
| 표준 HDD | [Azure에서 Linux Vm에 대 한 크기](../../sizes.md) | [Azure에서 Windows Vm에 대 한 크기](../../sizes.md) | 보통 또는 큰 Vm의 저장소 제한을 터치 하기 어려울 가능성이 높습니다. |
| 표준 SSD | [Azure에서 Linux Vm에 대 한 크기](../../sizes.md) | [Azure에서 Windows Vm에 대 한 크기](../../sizes.md) | 보통 또는 큰 Vm의 저장소 제한을 터치 하기 어려울 가능성이 높습니다. |
| Premium Storage | [Azure에서 Linux Vm에 대 한 크기](../../sizes.md) | [Azure에서 Windows Vm에 대 한 크기](../../sizes.md) | 저장소 구성으로 적중 IOPS 또는 저장소 처리량 VM 제한에 대해 알아보기 |
| Ultra disk 저장소 | [Azure에서 Linux Vm에 대 한 크기](../../sizes.md) | [Azure에서 Windows Vm에 대 한 크기](../../sizes.md) | 저장소 구성으로 적중 IOPS 또는 저장소 처리량 VM 제한에 대해 알아보기 |
| Azure NetApp Files | [Azure에서 Linux Vm에 대 한 크기](../../sizes.md) | [Azure에서 Windows Vm에 대 한 크기](../../sizes.md) | 저장소 트래픽이 저장소 대역폭이 아닌 네트워크 처리량 대역폭을 사용 하 고 있습니다. |

제한 사항으로 다음을 확인할 수 있습니다.

- VM이 작을수록 연결할 수 있는 디스크의 수를 줄입니다. 이는 ANF에는 적용 되지 않습니다. NFS 또는 SMB 공유를 탑재 했기 때문에 연결할 공유 볼륨의 수 제한이 발생 하지 않습니다.
- Vm에는 premium storage 디스크 및 Ultra disks를 사용 하 여 쉽게 초과할 수 있는 i/o 처리량 및 IOPS 제한이 있습니다.
- ANF를 사용 하는 경우 공유 볼륨에 대 한 트래픽은 VM의 네트워크 대역폭을 사용 하 고 저장소 대역폭은 사용 하지 않습니다.
- 더블 숫자 TiB 용량 공간에 대규모 NFS 볼륨이 있으면 단일 VM에서 이러한 볼륨에 액세스 하는 처리량은 공유 볼륨과 상호 작용 하는 단일 세션에 대 한 Linux의 제한을 기반으로 정체 되기 됩니다. 

SAP 시스템의 수명 주기에서 Azure Vm의 크기를 조정 하는 경우 새 VM과 더 큰 VM 유형의 IOPS 및 저장소 처리량 제한을 평가 해야 합니다. 일부 경우에는 Azure VM의 새 기능에 대 한 저장소 구성을 조정 하는 것이 적합할 수도 있습니다. 


## <a name="striping-or-not-striping"></a>스트라이핑 또는 스트라이핑 안 함
여러 Azure 디스크에서 하나의 큰 볼륨으로 스트라이프 세트를 만들면 개별 디스크의 IOPS 및 처리량을 하나의 볼륨에 누적 시킬 수 있습니다. Azure standard storage 및 Azure premium storage에만 사용 됩니다. 디스크 용량에 독립적으로 처리량 및 IOPS를 구성할 수 있는 Azure Ultra disk는 스트라이프 세트를 사용 하지 않아도 됩니다. NFS 또는 SMB 기반 공유 볼륨은 스트라이프 할 수 없습니다. Azure premium storage 처리량 및 IOPS의 비선형 특성으로 인해 대용량 단일 Azure premium storage 디스크와 동일한 IOPS 및 처리량을 사용 하 여 더 작은 용량을 프로 비전 할 수 있습니다. Azure premium storage를 사용 하 여 저렴 한 비용으로 높은 처리량 또는 IOPS를 달성할 수 있는 방법입니다. 예를 들면 다음과 같습니다.

- 두 P15 premium storage 디스크에 대 한 스트라이프를 통해 다음의 처리량을 얻습니다. 
- 250 MiB/초. 이러한 볼륨에는 512 GiB 용량이 있습니다. 초당 250 MiB 처리량을 제공 하는 단일 디스크를 사용 하려면 2 개의 TiB 용량으로 P40 디스크를 선택 해야 합니다. 
- 또는 스트라이프를 사용 하 여 512 GiB의 전체 용량으로 4 개의 P10 premium storage 디스크를 스트라이프 하 여 400 MiB/sec의 처리량을 달성할 수 있습니다. 초당 최소 500 MiB 처리량을 포함 하는 단일 디스크를 사용 하려면 P60 premium storage 디스크를 8 TiB으로 선택 해야 합니다. 비용 절감 또는 프리미엄 저장소는 용량과 함께 선형 이므로 스트라이프를 사용 하 여 비용을 절감할 수 있습니다.

일부 규칙은 다음에 스트라이핑 해야 합니다.

- Azure storage는 데이터 중복을 이미 유지 하므로 VM에서 구성 된 저장소를 사용 해야 합니다.
- 스트라이프 세트가 적용 되는 디스크는 동일한 크기 여야 합니다.

Azure premium storage를 사용 하 여 최적의 가격/성능 비율을 구현 하는 것이 가장 좋은 방법입니다. 스트라이프에 몇 가지 추가 배포 및 관리 오버 헤드가 있음을 이해 합니다.

특정 스트라이프 크기 권장 사항에 대해서는 [Azure 가상 머신 저장소 구성 SAP HANA](./hana-vm-operations-storage.md)와 같은 다른 DBMS에 대 한 설명서를 참조 하세요.




## <a name="next-steps"></a>다음 단계
문서를 읽어보세요.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md)
- [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)
 