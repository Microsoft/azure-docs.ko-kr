---
title: Azure virtual machines의 Oracle 솔루션 | Microsoft Docs
description: Microsoft Azure에서 Oracle 가상 컴퓨터 이미지의 지원 되는 구성 및 제한 사항에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 3abc09f8c82442e3b24a9edf6ef4fb42f19dfde8
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806952"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM 이미지 및 Microsoft Azure에 대 한 배포

이 문서에서는 Azure Marketplace Oracle에서 게시 한 가상 머신 이미지를 기반으로 하는 Oracle 솔루션에 대 한 정보를 다룹니다. Oracle 클라우드 인프라를 사용 하는 클라우드 간 응용 프로그램 솔루션에 관심이 있는 경우 oracle [응용 프로그램 솔루션 Microsoft Azure 및 Oracle 클라우드 인프라 통합](oracle-oci-overview.md)을 참조 하세요.

현재 사용할 수 있는 이미지 목록을 가져오려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2019 년 5 월 다음 이미지를 사용할 수 있습니다.

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

이러한 이미지는 "사용자 라이선스가 필요"한 것으로 간주되며, 따라서 VM을 실행하는 데 발생한 컴퓨팅, 스토리지 및 네트워킹 비용만 청구됩니다.  Oracle 소프트웨어를 사용하는 데 적절한 라이선스가 있으며 현재 Oracle과의 지원 계약이 체결되어 있는 것으로 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. 라이선스 이동에 대한 자세한 내용은 게시된 [Oracle 및 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 참고를 참조하세요. 

또한 개인은 솔루션이 Azure에서 처음부터 만들거나 온-프레미스 환경에서 업로드한 사용자 지정 이미지를 기반으로 하도록 선택할 수도 있습니다.

## <a name="oracle-database-vm-images"></a>Oracle 데이터베이스 VM 이미지
Oracle은 Oracle Linux을 기반으로 하는 가상 머신 이미지의 Azure에서 Oracle Database 12.1 이상 Standard 및 Enterprise edition을 지원 합니다.  Azure에서 Oracle Database의 프로덕션 워크 로드에 대 한 최상의 성능을 위해 VM 이미지 크기를 적절히 조정 하 고 프리미엄 SSD 또는 울트라 SSD Managed Disks를 사용 해야 합니다. Oracle 게시 된 VM 이미지를 사용 하 여 Azure에서 Oracle Database를 빠르게 실행 하는 방법에 대 한 지침은 [Oracle Database 빠른 시작 연습](oracle-database-quick-create.md)을 참조 하세요.

### <a name="attached-disk-configuration-options"></a>연결된 디스크 구성 옵션

연결된 디스크는 Azure Blob Storage 서비스에 의존합니다. 각 표준 디스크는 이론적으로 최대 초당 500개의 입력/출력 작업(IOPS)을 할 수 있습니다. 프리미엄 디스크 제품은 고성능 데이터베이스 워크로드에 적합하며 디스크당 최대 5000개의 IOPS를 제공할 수 있습니다. 성능 요구 사항을 충족 하는 경우 단일 디스크를 사용할 수 있습니다. 그러나 여러 개의 연결 된 디스크를 사용 하 여 데이터베이스 데이터를 분산 하 고 Oracle ASM (자동 저장소 관리)을 사용 하는 경우에는 유효 IOPS 성능을 향상 시킬 수 있습니다. Oracle ASM 관련 정보는 [Oracle 자동 스토리지 개요](https://www.oracle.com/technetwork/database/index-100339.html)를 참조하세요. Linux Azure VM에서 Oracle ASM을 설치 하 고 구성 하는 방법에 대 한 예는 [Oracle 자동화 된 저장소 관리 설치 및 구성](configure-oracle-asm.md) 자습서를 참조 하세요.

### <a name="shared-storage-configuration-options"></a>공유 저장소 구성 옵션

Azure NetApp Files는 클라우드의 데이터베이스와 같은 고성능 워크 로드를 실행 하기 위한 핵심 요구 사항을 충족 하 고를 제공 하도록 설계 되었습니다.
- VM native NFS 클라이언트 또는 Oracle dNFS를 통해 Oracle 워크 로드를 실행 하기 위한 Azure native shared NFS 저장소 서비스
- 실제 IOPS 수요를 반영 하는 확장 가능한 성능 계층
- 짧은 대기 시간
- 중요 업무용 엔터프라이즈 워크 로드 (예: SAP 및 Oracle)에서 일반적으로 요구 하는 고가용성, 높은 내구성 및 관리 효율성
- 가장 적극적인 RTO 및 RPO SLA를 얻기 위한 빠르고 효율적인 백업 및 복구

이러한 기능은 Azure NetApp Files는 NetApp® ONTAP® azure 데이터 센터 환경 내에서 실행 되는 모든 플래시 시스템에서 Azure 네이티브 서비스로 실행 되기 때문에 가능 합니다. 그 결과 다른 Azure 저장소 옵션과 마찬가지로 프로 비전 하 고 사용할 수 있는 이상적인 데이터베이스 저장소 기술이 있습니다. NFS 볼륨 Azure NetApp Files를 배포 하 고 액세스 하는 방법에 대 한 자세한 내용은 [Azure NetApp Files 설명서](https://docs.microsoft.com/azure/azure-netapp-files/) 를 참조 하세요. Azure NetApp Files에서 Oracle 데이터베이스를 운영 하는 방법에 대 한 모범 사례 권장 사항은 [Azure NetApp Files를 사용 하 여 Azure 배포 모범 사례 가이드의 oracle](https://www.netapp.com/us/media/tr-4780.pdf) 을 참조 하세요.


## <a name="licensing-oracle-database--software-on-azure"></a>Azure의 라이선스 Oracle Database & 소프트웨어
Microsoft Azure은 Oracle Database을 실행할 수 있는 권한이 있는 클라우드 환경입니다. Oracle 데이터베이스를 클라우드에서 라이선스를 라이선싱 하는 경우에는 Oracle Core 요소 테이블을 사용할 수 없습니다. 대신, 엔터프라이즈 버전 데이터베이스에 대해 하이퍼 스레딩 기술을 사용 하도록 설정 된 Vm을 사용 하는 경우 하이퍼 프로세서를 사용 하는 경우 두 개의 vCPUs를 단일 Oracle 프로세서 라이선스 (정책 문서에 명시 된 대로)로 계산 합니다. 정책 세부 정보는 [여기](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)에서 찾을 수 있습니다.
Oracle 데이터베이스에는 일반적으로 더 높은 메모리와 IO가 필요 합니다. 이러한 이유로 이러한 작업에는 메모리 액세스에 [최적화 된 vm](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 을 권장 합니다. 워크 로드를 더 최적화 하기 위해, 높은 메모리, 저장소 및 i/o 대역폭을 필요로 하지만 높은 코어 수가 아닌 Oracle Database 워크 로드에 대해 [제한 된 코어 vCPUs](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) 를 권장 합니다.

Oracle 소프트웨어와 워크 로드를 온-프레미스에서 Microsoft Azure로 마이그레이션하는 경우 Oracle은 [Azure의 ORACLE FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) 에 명시 된 대로 라이선스 이동성을 제공 합니다.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle RAC(Oracle Real Application Cluster)
Oracle RAC (실제 응용 프로그램 클러스터)는 온-프레미스 다중 노드 클러스터 구성에서 단일 노드의 오류를 완화 하도록 설계 되었습니다. 이 기능은 하이퍼스케일(hyper-scale) 퍼블릭 클라우드 환경에 네이티브가 아닌 두 가지 온-프레미스 기술인 네트워크 멀티캐스트 및 공유 디스크에 의존합니다. 데이터베이스 솔루션에 Azure에서 Oracle RAC가 필요한 경우 이러한 기술을 사용 하려면 타사 소프트웨어가 필요 합니다. Oracle RAC에 대 한 자세한 내용은 [FlashGrid SkyCluster 페이지](https://www.flashgrid.io/oracle-rac-in-azure/)를 참조 하세요.

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항
Azure에서 Oracle 데이터베이스를 사용 하는 경우 가동 중지 시간을 방지 하기 위해 고가용성 및 재해 복구 솔루션을 구현 해야 합니다. 

GoldenGate Oracle Database Enterprise Edition에 대 한 고가용성 및 재해 복구 (Oracle RAC를 사용 하지 않음)는 [데이터 가드, Active Data guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)또는 [oracle](https://www.oracle.com/technetwork/middleware/goldengate)를 사용 하 여 Azure에서 두 개의 별도 가상 컴퓨터에 두 개의 데이터베이스를 사용 하 여 구현할 수 있습니다. 두 가상 머신은 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 동일한 [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에 있어야 합니다.  또한 Azure가 개별 장애 도메인 및 업그레이드 도메인에 가상 머신을 배치할 수 있도록 동일한 가용성 집합에 VM을 배치하는 것이 좋습니다. 지리적 중복성을 사용 하려면 두 개의 서로 다른 지역 간에 복제 하도록 두 데이터베이스를 설정 하 고 VPN Gateway에 두 인스턴스를 연결 합니다.

[Azure에서 Oracle Data Guard 구현](configure-oracle-dataguard.md) 자습서에서는 azure의 기본 설정 절차를 안내 합니다.  

Oracle 데이터 가드로, 가상 컴퓨터에서 주 데이터베이스, 또 다른 가상 컴퓨터에서 보조(대기) 데이터 베이스에서 고가용성을 얻을 수 있으며, 양 데이터베이스 간에 단방향 복제를 설정할 수 있습니다. 결과는 해당 데이터 베이스의 복사본에 대한 읽기 엑세스입니다. Oracle 골든 게이트로, 두 개의 데이터베이스 간의 양방향 복제를 구성할 수 있습니다. 이러한 도구를 사용하여 데이터베이스에 대한 고가용성 솔루션을 설정하는 방법을 알아보려면 Oracle 웹 사이트에서 [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 및 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 설명서를 참조하세요. 데이터베이스의 복사본에 대한 읽기-쓰기 권한이 필요한 경우 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)를 사용할 수 있습니다.

[Azure에서 Oracle GoldenGate 구현](configure-oracle-golden-gate.md) 자습서에서는 azure의 기본 설정 절차를 안내 합니다.

Azure에서 HA 및 DR 솔루션을 설계 하는 것 외에도 데이터베이스를 복원 하기 위한 백업 전략이 필요 합니다. [Oracle Database 백업 및 복구](oracle-backup-recovery.md) 자습서에서는 일관 된 백업을 설정 하기 위한 기본 절차를 안내 합니다.


## <a name="support-for-jd-edwards"></a>JD Edwards 지원
Oracle 지원 정보 [문서 ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)에 따라 Jd Edwards enterpriseone 버전 9.2 이상이 특정 `Minimum Technical Requirements` (mtr)를 충족 하는 **공용 클라우드 제품** 에서 지원 됩니다.  OS 및 소프트웨어 애플리케이션 호환성에 대한 해당 MTR 사양을 충족하는 사용자 지정 이미지를 만들어야 합니다. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 가상 머신 이미지

* **클러스터링은 Enterprise Edition에서만 지원됩니다.** Oracle WebLogic 서버 Enterprise Edition을 사용 하는 경우에만 WebLogic 클러스터링을 사용 하도록 허가 됩니다. Oracle WebLogic Server Standard Edition에서 클러스터링을 사용 하지 마십시오.
* **UDP 멀티 캐스트는 지원되지 않습니다.** Azure는 UDP 유니캐스트를 지원하지만 멀티 캐스팅 및 브로드캐스팅은 지원하지 않습니다. Oracle WebLogic 서버는 Azure UDP 유니캐스트 기능을 사용할 수 있습니다. UDP 유니캐스트를 사용 하는 최상의 결과를 위해서는 WebLogic 클러스터 크기를 정적으로 유지 하거나 관리 되는 서버를 10 개 이하로 유지 하는 것이 좋습니다.
* **Oracle WebLogic 서버는 T3 액세스 (예: 엔터프라이즈 JavaBeans를 사용 하는 경우)에 대해 공용 및 개인 포트가 동일 해야 합니다.** *SLWLS*라는 가상 네트워크에서 두 개 이상의 vm으로 구성 된 Oracle Weblogic 서버 클러스터에서 서비스 계층 (EJB) 응용 프로그램이 실행 되는 다중 계층 시나리오를 고려해 보세요. 클라이언트 계층이 동일한 가상 네트워크의 다른 서브넷에 있으며, 서비스 계층에서 EJB를 호출 하려고 하는 간단한 Java 프로그램을 실행 합니다. 서비스 계층의 부하를 분산 해야 하므로 Oracle WebLogic 서버 클러스터의 가상 컴퓨터에 대해 부하 분산 된 공용 엔드포인트을 만들어야 합니다. 지정한 프라이빗 포트가 공용 포트와 다른 경우(예: 7006:7008), 다음과 같은 오류가 발생합니다.

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   이는 원격 T3 액세스의 경우 Oracle WebLogic 서버는 부하 분산 장치 포트와 WebLogic 관리 되는 서버 포트가 동일 하기 때문입니다. 앞의 경우 클라이언트는 포트 7006(부하 분산 장치 포트)에 액세스하고 관리되는 서버는 7008(프라이빗 포트)에서 수신 대기합니다. 이 제한은 T3 액세스에만 적용가능하며 HTTP에는 적용할 수 없습니다.

   이 문제를 방지하려면, 다음 해결 방법중 하나를 사용합니다.

  * T3 액세스 전용 부하 분산 엔드포인트에 대해 동일한 프라이빗 및 공용 포트 번호를 사용합니다.
  * Oracle WebLogic 서버를 시작할 때 다음 JVM 매개 변수를 포함 합니다.

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

관련 정보는 <https://support.oracle.com>에서 KB 문서 **860340.1**을 참조하세요.

* **동적 클러스터링 및 부하 분산 제한** Oracle WebLogic 서버에서 동적 클러스터를 사용 하 고 Azure에서 단일 공용 부하 분산 된 엔드포인트을 통해이를 노출 한다고 가정 합니다. 각 관리 되는 서버 (범위에서 동적으로 할당 되지 않은)에 대해 고정 포트 번호를 사용 하 고 관리자가 추적 하는 컴퓨터 보다 많은 관리 서버를 시작 하지 않는 한이 작업을 수행할 수 있습니다. 즉, 가상 컴퓨터 마다 관리 되는 서버가 두 개 이상 있는 것은 아닙니다. 구성으로 인해 가상 컴퓨터 보다 더 많은 Oracle WebLogic 서버가 시작 되는 경우 (즉, 여러 Oracle WebLogic 서버 인스턴스가 동일한 가상 컴퓨터를 공유 하는 경우), 이러한 인스턴스 중에는 지정 된 포트 번호에 바인딩할 Oracle WebLogic 서버입니다. 해당 가상 컴퓨터의 다른 사용자는 실패 합니다.

   관리되는 서버에 고유한 포트 번호를 할당하도록 관리 서버를 구성하면 이 구성에 필요한 것처럼 Azure가 단일 공용 포트에서 다수의 프라이빗 포트로 매핑을 지원하지 않기 때문에 부하 분산이 불가능합니다.
* **가상 컴퓨터의 여러 Oracle WebLogic 서버 인스턴스입니다.** 배포 요구 사항에 따라 가상 컴퓨터가 충분히 클 경우 동일한 가상 컴퓨터에서 여러 Oracle WebLogic 서버 인스턴스를 실행 하는 것을 고려할 수 있습니다. 예를 들어 두 개의 코어가 포함 된 중간 규모의 가상 머신에서 Oracle WebLogic 서버의 두 인스턴스를 실행 하도록 선택할 수 있습니다. 그러나 단일 실패 지점이 아키텍처에 도입 되는 것을 방지 하는 것이 좋습니다 .이는 Oracle WebLogic 서버의 여러 인스턴스를 실행 하는 하나의 가상 머신만 사용 하는 경우에 해당 합니다. 두 개 이상의 가상 컴퓨터를 사용 하는 것이 더 나은 방법일 수 있으며, 각 가상 컴퓨터는 Oracle WebLogic 서버의 여러 인스턴스를 실행할 수 있습니다. Oracle WebLogic 서버의 각 인스턴스는 여전히 동일한 클러스터에 포함 될 수 있습니다. 그러나 Azure 부하 분산 장치는 부하 분산 된 서버를 고유 하 게 배포 해야 하기 때문에 현재 Azure를 사용 하 여 동일한 가상 머신 내에서 이러한 Oracle WebLogic 서버 배포에서 노출 하는 엔드포인트의 부하를 분산할 수 없습니다. 가상 컴퓨터.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 가상 머신 이미지
* **JDK 6 및7 최신 업데이트** Java의 최신 공용 지원 버전(현재 Java 8) 사용이 권장되지만, Azure 또한 JDK 6 및 7 이미지를 사용할 수 있게 합니다. 이는 JDK 8으로 아직 업그레이드 될 준비가 되지 않은 레거시 애플리케이션을 위한 것입니다. 이전 JDK 이미지에 대한 업데이트가 일반 대중에게 더 이상 제공되지 않지만, Oracle과 Microsoft 사이의 파트너 관계로 인하여 Azure에 의해 제공되는 JDK 6 및 7 이미지들은 Oracle이 지원하는 고객중 오직 선택 그룹의 고객들을 위한 가장 최신의 비-공용 업데이트를 포함합니다. JDK 이미지의 최신 버전은 JDK 6 및 7의 업데이트된 릴리스 시간이 지난 후 제공될 것입니다.

   JDK 6 및 7 이미지에서 사용할 수 있는 JDK와 여기에서 파생 된 가상 머신 및 이미지는 Azure 내 에서만 사용할 수 있습니다.
* **64비트 JDK.** Azure가 제공하는 Oracle WebLogic Sever 가상 머신 이미지 및 Oracle JDK 가상 머신 이미지는 64비트 버전 Windows Server와 JDK 모두를 포함합니다.

## <a name="next-steps"></a>다음 단계
이제 Microsoft Azure의 가상 머신 이미지를 기반으로 하는 현재 Oracle 솔루션에 대 한 개요를 확인할 수 있습니다. 다음 단계는 Azure에서 첫 번째 Oracle 데이터베이스를 배포 하는 것입니다.

> [!div class="nextstepaction"]
> [Azure에서 Oracle 데이터베이스 만들기](oracle-database-quick-create.md)
