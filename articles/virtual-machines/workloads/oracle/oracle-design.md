---
title: Azure에서 Oracle 데이터베이스 설계 및 구현 | Microsoft Docs
description: Azure 환경에서 Oracle 데이터베이스를 설계하고 구현합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: c5a76b9cee8fd6eb09ee4d24c1380202fd17cc6d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60836309"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Azure에서 Oracle 데이터베이스 설계 및 구현

## <a name="assumptions"></a>가정

- 온-프레미스에서 Azure로 Oracle 데이터베이스 마이그레이션할 계획입니다.
- Oracle AWR 보고서의 다양한 메트릭에 대해 이해하고 있습니다.
- 애플리케이션 성능 및 플랫폼 사용률에 대해 기본적으로 이해하고 있습니다.

## <a name="goals"></a>목표

- Azure에서 Oracle 배포를 최적화하는 방법을 이해합니다.
- Azure 환경에서 Oracle 데이터베이스에 대한 성능 튜닝 옵션을 탐색합니다.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>온-프레미스와 Azure 구현 간의 차이점 

다음은 온-프레미스 애플리케이션을 Azure로 마이그레이션할 때 유의해야 할 몇 가지 중요 사항입니다. 

중요한 차이점 중 하나는 Azure 구현에서 VM, 디스크 및 가상 네트워크와 같은 리소스가 다른 클라이언트와 공유된다는 것입니다. 또한 리소스는 요구 사항에 따라 제한될 수도 있습니다. Azure에서는 실패 방지(MTBF)에 집중하는 대신 실패 극복(MTTR)에 더 많이 집중합니다.

다음 표에는 Oracle 데이터베이스의 온-프레미스 구현과 Azure 구현 간의 차이점이 나열되어 있습니다.

> 
> |  | **온-프레미스 구현** | **Azure 구현** |
> | --- | --- | --- |
> | **네트워킹** |LAN/WAN  |SDN(소프트웨어 방식 네트워킹)|
> | **보안 그룹** |IP/포트 제한 도구 |[NSG(네트워크 보안 그룹)](https://azure.microsoft.com/blog/network-security-groups) |
> | **복원력** |MTBF(평균 고장 간격) |MTTR(평균 복구 시간)|
> | **계획된 유지 보수** |패치/업그레이드|[가용성 집합](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)(Azure에서 관리되는 패치/업그레이드) |
> | **리소스** |전용  |다른 클라이언트와 공유|
> | **지역** |데이터 센터 |[지역 쌍](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Storage** |SAN/실제 디스크 |[Azure 관리 저장소](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **규모** |수직적 확장 |수평적 확장|


### <a name="requirements"></a>요구 사항

- 데이터베이스 크기 및 증가 속도를 결정합니다.
- Oracle AWR 보고서 또는 다른 네트워크 모니터링 도구에 따라 예측할 수 있는 IOPS 요구 사항을 결정합니다.

## <a name="configuration-options"></a>구성 옵션

Azure 환경에서 성능을 향상시키기 위해 조정할 수 있는 잠재적인 네 가지 영역이 있습니다.

- 가상 머신 크기
- 네트워크 처리량
- 디스크 형식 및 구성
- 디스크 캐시 설정

### <a name="generate-an-awr-report"></a>AWR 보고서 생성

기존 Oracle 데이터베이스가 있고 Azure로 마이그레이션하도록 계획하는 경우 몇 가지 옵션이 있습니다. Oracle AWR 보고서를 실행하여 메트릭(IOPS, Mbps, GiBs 등)을 얻을 수 있습니다. 그런 다음 수집한 메트릭을 기반으로 하여 VM을 선택합니다. 또는 인프라 팀에 문의하여 유사한 정보를 얻을 수 있습니다.

일반 및 최대 워크로드 모두에서 AWR 보고서를 실행하여 비교하는 것이 좋을 수도 있습니다. 이러한 보고서에 따라 평균 워크로드 또는 최대 워크로드를 기준으로 VM 크기를 조정할 수 있습니다.

다음은 AWR 보고서를 생성하는 방법의 예제입니다.

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>주요 메트릭

다음은 AWR 보고서에서 얻을 수 있는 메트릭입니다.

- 총 코어 수
- CPU 클럭 속도
- 전체 메모리(GB)
- CPU 사용률
- 최대 데이터 전송 속도
- I/O 변경률(읽기/쓰기)
- 다시 실행 로그 속도(MBPs)
- 네트워크 처리량
- 네트워크 대기 시간 비율(낮음/높음)
- 데이터베이스 크기(GB)
- 클라이언트 간 SQL*Net을 통해 수신된 바이트 수

### <a name="virtual-machine-size"></a>가상 머신 크기

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. AWR 보고서의 CPU, 메모리 및 I/O 사용량에 따라 VM 크기 예측

시스템 병목 현상이 발생하는 위치를 나타내는 상위 5개 시간 지정 전경 이벤트를 확인할 수 있습니다.

예를 들어 다음 다이어그램에서는 로그 파일 동기화가 맨 위에 있습니다. LGWR에서 로그 버퍼를 다시 실행 로그 파일에 쓰기 전에 필요한 대기 수를 나타냅니다. 이러한 결과는 더 효율적으로 수행되는 저장소 또는 디스크가 필요함을 나타냅니다. 또한 다이어그램에서는 CPU(코어) 수와 메모리 양도 보여 줍니다.

![AWR 보고서 페이지의 스크린샷](./media/oracle-design/cpu_memory_info.png)

다음 다이어그램에서는 읽기 및 쓰기의 총 I/O 수를 보여 줍니다. 보고서 시간 동안 59GB의 읽기 및 247.3GB의 쓰기가 있었습니다.

![AWR 보고서 페이지의 스크린샷](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. VM 선택

다음 단계에서는 AWR 보고서에서 수집한 정보에 따라 요구 사항을 충족하는 비슷한 크기의 VM을 선택합니다. [메모리 최적화](../../linux/sizes-memory.md) 문서에서 사용 가능한 VM 목록을 찾을 수 있습니다.

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. ACU에 따라 비슷한 VM 시리즈로 VM 크기 미세 조정

VM을 선택한 후에는 해당 VM에 대한 ACU에 주의해야 합니다. 요구 사항에 더 적합한 ACU 값에 따라 다른 VM을 선택할 수도 있습니다. 자세한 내용은 [Azure 컴퓨팅 단위](https://docs.microsoft.com/azure/virtual-machines/windows/acu)를 참조하세요.

![ACU 단위 페이지의 스크린샷](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>네트워크 처리량

다음 다이어그램에서는 처리량과 IOPS 간의 관계를 보여 줍니다.

![처리량 스크린샷](./media/oracle-design/throughput.png)

총 네트워크 처리량은 다음 정보를 기반으로 하여 예측합니다.
- SQL*Net 트래픽
- MBps x 서버 수(Oracle Data Guard와 같은 아웃바운드 스트림)
- 애플리케이션 복제와 같은 다른 요소

![SQL*Net 처리량의 스크린샷](./media/oracle-design/sqlnet_info.png)

네트워크 대역폭 요구 사항에 따라 다양한 게이트웨이 유형을 선택할 수 있습니다. 여기에는 기본, VpnGw 및 Azure ExpressRoute가 포함됩니다. 자세한 내용은 [VPN Gateway 가격 페이지](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)를 참조하세요.

**권장 사항**

- 네트워크 대기 시간은 온-프레미스 배포에 비해 더 높습니다. 네트워크 왕복 수를 줄이면 성능이 크게 향상될 수 있습니다.
- 왕복을 줄이려면 동일한 가상 머신에서 트랜잭션이 많은 애플리케이션 또는 "채팅 가능한(chatty)" 앱을 통합합니다.

### <a name="disk-types-and-configurations"></a>디스크 형식 및 구성

- ‘기본 OS 디스크’: 이러한 디스크 유형은 영구적 데이터 및 캐싱을 제공합니다. 시작 시 OS 액세스에 최적화되며, 트랜잭션 또는 데이터 웨어하우스(분석) 워크로드용으로는 설계되지 않았습니다.

- *비관리 디스크*: 이러한 디스크 유형에서는 VM 디스크에 해당하는 VHD(가상 하드 디스크) 파일을 저장하는 스토리지 계정을 사용자가 관리합니다. VHD 파일은 Azure Storage 계정에 페이지 Blob으로 저장됩니다.

- ‘관리 디스크’: Azure에서 VM 디스크에 사용하는 스토리지 계정을 관리합니다. 필요한 디스크 유형(프리미엄 또는 표준)과 디스크 크기를 지정합니다. Azure에서 사용자에게 맞는 디스크를 만들고 관리합니다.

- ‘Premium Storage 디스크’: 이러한 디스크 유형은 프로덕션 워크로드에 가장 적합합니다. Premium Storage는 특정 크기 시리즈 VM(예: DS, DSv2, GS 및 F 시리즈 VM)에 연결할 수 있는 VM 디스크를 지원합니다. 다양한 크기로 제공되며 32GB에서 4,096GB까지 다양한 디스크를 선택할 수 있습니다. 디스크 크기마다 자체 성능 사양이 있습니다. 애플리케이션 요구 사항에 따라 하나 이상의 디스크를 VM에 연결할 수 있습니다.

포털에서 새 관리 디스크를 만드는 경우 사용하려는 디스크 유형에 대한 **계정 유형**을 선택할 수 있습니다. 사용 가능한 모든 디스크가 드롭다운 메뉴에 표시되는 것은 아닙니다. 특정 VM 크기를 선택하면 해당 VM 크기를 기반으로 하여 사용할 수 있는 Premium Storage SKU만 메뉴에 표시됩니다.

![관리되는 디스크 페이지의 스크린샷](./media/oracle-design/premium_disk01.png)

VM에서 저장소를 구성한 후 데이터베이스를 만들기 전에 디스크를 로드하여 테스트할 수 있습니다. 대기 시간 및 처리량 모두와 관련된 I/O 속도를 알고 있으면 VM에서 대기 시간 목표로 예상되는 처리량을 지원할 수 있는지 확인하는 데 도움이 됩니다.

Oracle Orion, Sysbench 및 Fio와 같이 애플리케이션 부하 테스트를 위한 여러 가지 도구가 있습니다.

Oracle 데이터베이스를 배포한 후에 부하 테스트를 다시 실행합니다. 일반 및 최대 워크로드를 시작합니다. 그러면 결과에서 사용자 환경의 초기 계획을 보여 줍니다.

저장소 크기 대신 IOPS 속도에 따라 저장소 크기를 조정하는 것이 더 중요할 수 있습니다. 예를 들어 필요한 IOPS가 5,000이지만 200GB만 필요한 경우 200GB 이상의 저장소가 있어도 P30 클래스 프리미엄 디스크를 받을 수도 있습니다.

IOPS 속도는 AWR 보고서에서 얻을 수 있으며, 다시 실행 로그, 물리적 읽기 및 쓰기 속도로 결정됩니다.

![AWR 보고서 페이지의 스크린샷](./media/oracle-design/awr_report.png)

예를 들어 다시 실행 크기는 초당 12,200,000바이트이며, 11.63MBps와 같습니다.
IOPS는 12,200,000 / 2,358 = 5,174입니다.

I/O 요구 사항에 대해 명확히 알고 있으면 이러한 요구 사항에 가장 적합한 드라이브 조합을 선택할 수 있습니다.

**권장 사항**

- 데이터 테이블스페이스의 경우 관리되는 저장소 또는 Oracle ASM을 사용하여 I/O 워크로드를 여러 디스크에 분산합니다.
- 읽기 및 쓰기 집약적 작업에 대한 I/O 블록 크기가 늘어남에 따라 데이터 디스크를 더 많이 추가합니다.
- 대규모 순차적 프로세스에 대한 블록 크기를 늘립니다.
- 데이터 압축을 사용하여 I/O를 줄입니다(데이터 및 인덱스 모두에 해당).
- 개별 데이터 디스크에서 다시 실행 로그, 시스템, 임시 디스크를 분리하고 TS를 실행 취소합니다.
- 기본 OS 디스크(/dev/sda)에 애플리케이션 파일을 배치하지 않습니다. 이러한 디스크는 빠른 VM 부팅 시간에 맞게 최적화되지 않으며, 애플리케이션에 좋은 성능을 제공하지 않을 수 있습니다.

### <a name="disk-cache-settings"></a>디스크 캐시 설정

호스트 캐싱에는 세 가지 옵션이 있습니다.

- ‘읽기 전용’: 모든 요청이 이후 읽기를 위해 캐시됩니다. 모든 쓰기는 Azure Blob Storage에 직접 유지됩니다.

- ‘읽기/쓰기’: “미리 읽기” 알고리즘입니다. 읽기 및 쓰기가 향후 읽기에 대해 캐시됩니다. 연속 쓰기(write-through) 이외의 쓰기 작업은 먼저 로컬 캐시에 유지됩니다. SQL Server의 경우 연속 쓰기를 사용하므로 쓰기가 Azure Storage에 유지됩니다. 또한 가벼운 워크로드에 대해 가장 낮은 디스크 대기 시간을 제공합니다.

- ‘없음’(사용 안 함): 이 옵션을 사용하면 캐시를 무시할 수 있습니다. 모든 데이터가 디스크에 전송되며 Azure Storage에 유지됩니다. 이 메서드를 사용하면 I/O 집약적 워크로드에 대해 가장 높은 I/O 속도를 얻을 수 있습니다. "트랜잭션 비용"도 고려해야 합니다.

**권장 사항**

처리량을 최대화하려면 호스트 캐싱에 대해 **없음**으로 시작하는 것이 좋습니다. Premium Storage의 경우 **읽기 전용** 또는 **없음** 옵션을 사용하여 파일 시스템을 탑재할 때 "barrier"를 사용하지 않도록 설정해야 합니다. /etc/fstab 파일을 UUID로 디스크에 업데이트합니다.

![관리되는 디스크 페이지의 스크린샷](./media/oracle-design/premium_disk02.png)

- OS 디스크의 경우 기본 **읽기/쓰기** 캐싱을 사용합니다.
- 시스템, 임시 및 실행 취소 디스크의 경우 캐싱에 **없음**을 사용합니다.
- 데이터 디스크의 경우 캐싱에 **없음**을 사용합니다. 그러나 데이터베이스가 읽기 전용이거나 읽기 집약적인 경우 **읽기 전용** 캐싱을 사용합니다.

데이터 디스크 설정을 저장한 후에 OS 수준에서 드라이브를 분리한 다음 변경한 후에 다시 탑재하지 않는 한 호스트 캐시 설정은 변경할 수 없습니다.


## <a name="security"></a>보안

Azure 환경을 설정하고 구성한 후의 다음 단계는 네트워크를 보호하는 것입니다. 몇 가지 권장 사항입니다.

- ‘NSG 정책’: NSG는 서브넷 또는 NIC에서 정의될 수 있습니다. 애플리케이션 방화벽과 같이 작업에 대한 보안 및 강제 라우팅 모두를 위해 서브넷 수준에서 액세스를 제어하는 것이 더 간단합니다.

- *Jumpbox*: 더 안전한 액세스를 위해 관리자는 애플리케이션 서비스 또는 데이터베이스에 직접 연결해서는 안 됩니다. Jumpbox는 관리자 컴퓨터와 Azure 리소스 간 미디어로 사용됩니다.
![Jumpbox 토폴로지 페이지의 스크린샷](./media/oracle-design/jumpbox.png)

    관리자 컴퓨터는 Jumpbox에 대해 IP가 제한된 액세스만 제공해야 합니다. Jumpbox에는 애플리케이션과 데이터베이스에 대한 액세스 권한이 있어야 합니다.

- ‘개인 네트워크’(서브넷): NSG 정책에 따라 더 나은 제어를 설정할 수 있도록 애플리케이션 서비스와 데이터베이스를 별도의 서브넷에 두는 것이 좋습니다.


## <a name="additional-reading"></a>추가 참조 자료

- [Oracle ASM 구성](configure-oracle-asm.md)
- [Oracle Data Guard 구성](configure-oracle-dataguard.md)
- [Oracle Golden Gate 구성](configure-oracle-golden-gate.md)
- [Oracle 백업 및 복구](oracle-backup-recovery.md)

## <a name="next-steps"></a>다음 단계

- [자습서: 고가용성 VM 만들기](../../linux/create-cli-complete.md)
- [VM 배포 Azure CLI 샘플 탐색](../../linux/cli-samples.md)
