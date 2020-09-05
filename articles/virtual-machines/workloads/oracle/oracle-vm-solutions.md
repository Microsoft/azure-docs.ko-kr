---
title: Azure 가상 머신의 Oracle 솔루션 | Microsoft Docs
description: Microsoft Azure의 Oracle 가상 머신 이미지에 지원되는 구성 및 제한 사항을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: rgardler
manager: ''
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: rogardle
ms.openlocfilehash: c22c5fa9c1a1680734a8cf5d8f4079e8c7b5c5f7
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723419"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure의 Oracle VM 이미지 및 배포

이 문서에서는 Oracle이 Azure Marketplace에 게시한 가상 머신 이미지에 기반을 둔 Oracle 솔루션에 대한 정보를 다룹니다. Oracle Cloud Infrastructure를 사용하는 클라우드 간 애플리케이션 솔루션에 관심이 있는 경우 [Microsoft Azure 및 Oracle Cloud Infrastructure를 통합하는 Oracle 애플리케이션 솔루션](oracle-oci-overview.md)을 참조하세요.

현재 사용할 수 있는 이미지 목록을 가져오려면 다음 명령을 실행합니다.

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

6 월 2020을 기준으로 다음 이미지를 사용할 수 있습니다.

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

이러한 이미지는 "사용자 라이선스가 필요"한 것으로 간주되며, 따라서 VM을 실행하는 데 발생한 컴퓨팅, 스토리지 및 네트워킹 비용만 청구됩니다.  Oracle 소프트웨어를 사용하는 데 적절한 라이선스가 있으며 현재 Oracle과의 지원 계약이 체결되어 있는 것으로 가정합니다. Oracle에서는 온-프레미스에서 Azure로의 라이선스 이동을 보장합니다. 라이선스 이동에 대한 자세한 내용은 게시된 [Oracle 및 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 참고를 참조하세요.

또한 개인은 솔루션이 Azure에서 처음부터 만들거나 온-프레미스 환경에서 업로드한 사용자 지정 이미지를 기반으로 하도록 선택할 수도 있습니다.

## <a name="oracle-database-vm-images"></a>Oracle 데이터베이스 VM 이미지

Oracle은 Oracle Linux 기반 가상 머신 이미지의 Azure에서 Oracle Database 12.1 이상의 Standard 및 Enterprise Edition을 실행하도록 지원합니다.  Azure에서 Oracle Database의 프로덕션 워크로드에 대한 최상의 성능을 위해 VM 이미지 크기를 적절히 조정하고 Premium SSD 또는 Ultra SSD Managed Disks를 사용해야 합니다. Oracle에서 게시한 VM 이미지를 사용하여 Azure에서 Oracle Database를 신속하게 실행하는 방법에 대한 지침은 [Oracle Database 빠른 시작 연습](oracle-database-quick-create.md)을 참조하세요.

### <a name="attached-disk-configuration-options"></a>연결된 디스크 구성 옵션

연결된 디스크는 Azure Blob Storage 서비스에 의존합니다. 각 표준 디스크는 이론적으로 최대 초당 500개의 입력/출력 작업(IOPS)을 할 수 있습니다. 프리미엄 디스크 제품은 고성능 데이터베이스 워크로드에 적합하며 디스크당 최대 5000개의 IOPS를 제공할 수 있습니다. 성능 요구 사항을 충족하는 경우 단일 디스크를 사용할 수 있습니다. 하지만 여러 개의 연결된 디스크를 사용하여 데이터베이스를 분산시킨 후 Oracle 자동 스토리지 관리(ASM)를 사용하면 효과적으로 IOPS 성능 향상을 개선할 수 있습니다. Oracle ASM 관련 정보는 [Oracle 자동 스토리지 개요](https://www.oracle.com/technetwork/database/index-100339.html)를 참조하세요. Linux Azure VM에서 Oracle ASM을 설치 및 구성하는 방법에 대한 예제는 [Oracle Automated Storage Management 설치 및 구성](configure-oracle-asm.md) 자습서를 참조하세요.

### <a name="shared-storage-configuration-options"></a>공유 스토리지 구성 옵션

Azure NetApp Files는 클라우드의 데이터베이스 같은 고성능 워크로드를 실행하기 위한 핵심 요구 사항을 충족하도록 설계되었으며 다음을 제공합니다.

- VM 전용 NFS 클라이언트 또는 Oracle dNFS를 통해 Oracle 워크로드를 실행하기 위한 Azure 전용 공유 NFS 스토리지 서비스
- 실제 IOPS 수요 범위를 반영하는 확장성 있는 성능 계층
- 짧은 대기 시간
- 중요 업무용 엔터프라이즈 워크로드(예: SAP 및 Oracle)에서 일반적으로 필요한 고가용성, 높은 내구성 및 대규모 관리 효율성
- 빠르고 효율적인 백업 및 복구를 통해 가장 적극적인 RTO 및 RPO SLA 구현

이러한 기능이 가능한 이유는 Azure NetApp Files가 Azure 데이터 센터 환경 내에서 Azure Native 서비스로 실행되는 NetApp® ONTAP® 올플래시 시스템에 기반을 두고 있기 때문입니다. 따라서 다른 Azure 스토리지 옵션과 마찬가지로 프로비저닝하고 사용할 수 있는 이상적인 데이터베이스 스토리지 기술입니다. Azure NetApp Files NFS 볼륨을 배포하고 액세스하는 방법에 대한 자세한 내용은 [Azure NetApp Files 설명서](../../../azure-netapp-files/index.yml)를 참조하세요. Azure NetApp Files에서 Oracle 데이터베이스를 사용하는 방법에 대한 모범 사례와 권장 사항은 [Azure NetApp Files를 사용하여 Azure에 Oracle 배포 모범 사례 가이드](https://www.netapp.com/us/media/tr-4780.pdf)를 참조하세요.

## <a name="licensing-oracle-database--software-on-azure"></a>Azure의 Oracle Database 및 소프트웨어에 라이선스 부여

Microsoft Azure는 Oracle Database를 실행할 권한이 있는 클라우드 환경입니다. 클라우드의 Oracle 데이터베이스에 라이선스를 부여할 경우 Oracle Core Factor 테이블이 적용되지 않습니다. 대신, Enterprise Edition 데이터베이스에 하이퍼 스레딩 기술을 사용하도록 설정된 VM을 사용하는 경우 정책 문서에 명시된 대로 vCPU 두 개를 하나의 Oracle Processor 라이선스로 계수합니다. 정책 세부 정보는 [여기](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)에서 확인할 수 있습니다.
Oracle 데이터베이스에는 일반적으로 더 많은 메모리와 IO가 필요합니다. 따라서 이러한 워크로드에는 [메모리 최적화 VM](../../sizes-memory.md)이 권장됩니다. 워크로드를 더 최적화하려면 많은 메모리, 스토리지 및 I/O 대역폭이 필요하지만 많은 코어 수는 필요하지 않은 Oracle Database 워크로드에 [제한된 코어 vCPU](../../constrained-vcpu.md)가 권장됩니다.

Oracle 소프트웨어와 워크로드를 온-프레미스에서 Microsoft Azure로 마이그레이션하는 경우 Oracle은 [Azure의 Oracle FAQ](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)에 명시된 대로 라이선스 이동을 제공합니다.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle RAC(Oracle Real Application Cluster)

Oracle RAC(Oracle Real Application Cluster)는 온-프레미스 다중 노드 클러스터 구성에서 단일 노드의 오류를 완화하도록 고안되었습니다. 이 기능은 하이퍼스케일(hyper-scale) 퍼블릭 클라우드 환경에 네이티브가 아닌 두 가지 온-프레미스 기술인 네트워크 멀티캐스트 및 공유 디스크에 의존합니다. 데이터베이스 솔루션에 Azure에서 Oracle RAC가 필요한 경우 이러한 기술을 사용 하려면 타사 소프트웨어가 필요 합니다. Oracle RAC에 대한 자세한 내용은 [FlashGrid SkyCluster 페이지](https://www.flashgrid.io/oracle-rac-in-azure/)를 참조하세요.

## <a name="high-availability-and-disaster-recovery-considerations"></a>높은 가용성 및 재해 복구 고려 사항

Azure에서 Oracle 데이터베이스를 사용할 때, 사용자는 모든 가동 중지 시간을 방지하기 위해 높은 가용성과 재해 복구 솔루션을 구현하는 일을 담당합니다.

Oracle RAC에 의존하지 않는 Oracle Database Enterprise Edition의 고가용성 및 재해 복구는 Azure에서 [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 또는 [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)를 사용하여 두 개의 개별 가상 머신에 있는 두 개의 데이터베이스로 구현할 수 있습니다. 두 가상 머신은 영구적인 개인 IP 주소를 통해 서로 액세스할 수 있도록 동일한 [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/)에 있어야 합니다.  또한 Azure가 개별 장애 도메인 및 업그레이드 도메인에 가상 머신을 배치할 수 있도록 동일한 가용성 집합에 VM을 배치하는 것이 좋습니다. 지리적 중복을 포함하려는 경우 다른 두 지역 간에 이 두 데이터베이스를 복제하고 VPN Gateway로 두 인스턴스를 연결할 수 있습니다.

[Azure에서 Oracle Data Guard 구현](configure-oracle-dataguard.md) 자습서는 Azure에서 기본 설치 절차를 수행하는 방법을 안내합니다.  

Oracle 데이터 가드로, 가상 머신에서 주 데이터베이스, 또 다른 가상 머신에서 보조(대기) 데이터 베이스에서 고가용성을 얻을 수 있으며, 양 데이터베이스 간에 단방향 복제를 설정할 수 있습니다. 결과는 해당 데이터 베이스의 복사본에 대한 읽기 엑세스입니다. Oracle 골든 게이트로, 두 개의 데이터베이스 간의 양방향 복제를 구성할 수 있습니다. 이러한 도구를 사용하여 데이터베이스에 대한 고가용성 솔루션을 설정하는 방법을 알아보려면 Oracle 웹 사이트에서 [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) 및 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 설명서를 참조하세요. 데이터베이스의 복사본에 대한 읽기-쓰기 권한이 필요한 경우 [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)를 사용할 수 있습니다.

[Azure에서 Oracle GoldenGate 구현](configure-oracle-golden-gate.md) 자습서는 Azure에서 기본 설치 절차를 수행하는 방법을 안내합니다.

Azure에 HA 및 DR 솔루션이 설계되어 있지만 데이터베이스를 복원할 백업 전략을 마련해야 합니다. [Oracle 데이터베이스 Backup 및 복구](oracle-backup-recovery.md) 자습서는 일관된 백업 설정에 대한 기본 절차를 안내합니다.

## <a name="support-for-jd-edwards"></a>JD Edwards 지원

Oracle 지원 참고 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)에 따라 JD Edwards EnterpriseOne 버전 9.2 이상은 특정 MTR(`Minimum Technical Requirements`)을 준수하는 **모든 퍼블릭 클라우드 제품**에서 지원됩니다.  OS 및 소프트웨어 애플리케이션 호환성에 대한 해당 MTR 사양을 충족하는 사용자 지정 이미지를 만들어야 합니다.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oracle WebLogic Server 가상 머신 제품

Oracle 및 Microsoft는 WebLogic Server를 Azure 애플리케이션 제품 컬렉션의 형태로 Azure Marketplace에 출시하기 위해 협력하고 있습니다.  이러한 제품은 [Oracle WebLogic Server Azure 애플리케이션](oracle-weblogic.md) 문서에 설명되어 있습니다.

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 가상 머신 이미지

- **클러스터링은 Enterprise Edition에서만 지원됩니다.** WebLogic Server Enterprise Edition을 사용할 때만 Oracle WebLogic 클러스터링을 사용하도록 허가됩니다. Oracle WebLogic Server Standard Edition에서 클러스터링을 사용하지 마세요.
- **UDP 멀티 캐스트는 지원되지 않습니다.** Azure는 UDP 유니캐스트를 지원하지만 멀티 캐스팅 및 브로드캐스팅은 지원하지 않습니다. Oracle WebLogic Server는 Azure UDP 유니캐스트 기능에 의존할 수 있습니다. UDP 유니캐스트에 의존하는 최상의 결과를 위해, WebLogic 클러스터 크기를 정적으로 유지하거나 서버를 10개 이하로 유지하는 것이 좋습니다.
- **Oracle WebLogic Server는 T3 액세스의 공용 및 프라이빗 포트가 동일할 것으로 예상합니다(예: Enterprise JavaBeans 사용 시).** *SLWLS*라는 가상 네트워크에 있는, 둘 이상의 VM으로 구성된 Oracle WebLogic Server 클러스터에서 서비스 계층(EJB) 애플리케이션이 실행되는 다중 계층 시나리오를 고려해 보세요. 동일한 가상 네트워크의 다른 서브넷에 있는 클라이언트 계층은 서비스 계층에서 EJB 호출을 시도하는 간단한 Java 프로그램을 실행합니다. 서비스 계층의 부하를 분산해야 하므로 Oracle WebLogic Server 클러스터에 있는 가상 머신에 부하 분산된 퍼블릭 엔드포인트를 만들어야 합니다. 지정한 프라이빗 포트가 공용 포트와 다른 경우(예: 7006:7008), 다음과 같은 오류가 발생합니다.

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   이는 Oracle WebLogic Server가 모든 원격 T3 액세스의 부하 분산 장치 포트 및 WebLogic 관리 서버가 동일할 것으로 예상하기 때문입니다. 앞의 경우 클라이언트는 포트 7006(부하 분산 장치 포트)에 액세스하고 관리되는 서버는 7008(프라이빗 포트)에서 수신 대기합니다. 이 제한은 T3 액세스에만 적용가능하며 HTTP에는 적용할 수 없습니다.

   이 문제를 방지하려면, 다음 해결 방법중 하나를 사용합니다.

- T3 액세스 전용 부하 분산 엔드포인트에 대해 동일한 프라이빗 및 공용 포트 번호를 사용합니다.
- Oracle WebLogic Server를 시작할 때는 다음 JVM 매개변수를 포함합니다.

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

관련 정보는 <https://support.oracle.com>에서 KB 문서 **860340.1**을 참조하세요.

- **동적 클러스터링 및 부하 분산 제한** Oracle WebLogic Server에서 동적 클러스터를 사용하고 Azure에서 단일, 퍼블릭 부하 분산 엔드포인트를 통해 노출한다고 가정해 보세요. 이 작업을 수행하려면 각 관리 서버에 특정 범위에서 동적으로 할당되지 않은 고정된 포트 번호를 사용하고, 관리자가 추적하는 서버보다 더 많은 관리형 서버를 시작하지 않아야 합니다. 즉, 가상 머신당 관리형 서버는 1개 이하입니다. 사용자 환경의 구성으로 인해 가상 머신 수보다 많은 Oracle WebLogic Server가 시작되면(즉, 여러 개의 Oracle WebLogic Server의 인스턴스가 동일한 가상 머신을 공유할 경우), 지정된 포트 번호에 이러한 Oracle WebLogic Server 인스턴스 여러 개가 바인딩될 수 없습니다. 해당 가상 머신의 다른 서버는 실패합니다.

   관리되는 서버에 고유한 포트 번호를 할당하도록 관리 서버를 구성하면 이 구성에 필요한 것처럼 Azure가 단일 공용 포트에서 다수의 프라이빗 포트로 매핑을 지원하지 않기 때문에 부하 분산이 불가능합니다.
- **가상 머신에 있는 Oracle WebLogic Server의 여러 인스턴스.** 배포 요구사항에 따라 가상 머신이 충분히 큰 경우, 동일한 가상 머신 내에서 Oracle WebLogic Server의 여러 인스턴스를 실행하는 옵션을 고려해볼 수 있습니다. 예를 들면 두 개의 코어를 갖고 있는 중간 크기의 가상 머신에서 Oracle WebLogic Server 인스턴스 두 개를 실행하도록 선택할 수 있습니다. 그러나 아키텍처에 단일 실패 지점을 도입하지 않는 것이 좋습니다. 예를 들어 Oracle WebLogic Server의 여러 인스턴스를 실행하는 가상 머신을 하나만 사용하는 경우가 이에 해당합니다. 두 개의 가상 머신을 사용하는 것이 더 나은 접근법이며, 각 가상 머신은 Oracle WebLogic Server의 여러 인스턴스를 실행할 수 있습니다. Oracle WebLogic Sever의 각 인스턴스는 여전히 동일한 클러스터의 일부입니다. 그러나 Azure 부하 분산 장치는 부하가 분산된 서버가 고유한 가상 머신에 분산되도록 하므로, 현재 Azure를 사용하여 동일한 가상 머신 내에서 Oracle WebLogic Server가 노출하는 엔드포인트의 부하를 분산하는 것은 불가능합니다.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 가상 머신 이미지

- **JDK 6 및7 최신 업데이트** Java의 최신 공용 지원 버전(현재 Java 8) 사용이 권장되지만, Azure 또한 JDK 6 및 7 이미지를 사용할 수 있게 합니다. 이는 JDK 8으로 아직 업그레이드 될 준비가 되지 않은 레거시 애플리케이션을 위한 것입니다. 이전 JDK 이미지에 대한 업데이트가 일반 대중에게 더 이상 제공되지 않지만, Oracle과 Microsoft 사이의 파트너 관계로 인하여 Azure에 의해 제공되는 JDK 6 및 7 이미지들은 Oracle이 지원하는 고객중 오직 선택 그룹의 고객들을 위한 가장 최신의 비-공용 업데이트를 포함합니다. JDK 이미지의 최신 버전은 JDK 6 및 7의 업데이트된 릴리스 시간이 지난 후 제공될 것입니다.

   JDK 6 및 7 이미지에서 사용 가능한 JDK와 여기서 파생된 가상 머신과 이미지는 Azure 내에서만 사용할 수 있습니다.
- **64비트 JDK.** Azure가 제공하는 Oracle WebLogic Sever 가상 머신 이미지 및 Oracle JDK 가상 머신 이미지는 64비트 버전 Windows Server와 JDK 모두를 포함합니다.

## <a name="next-steps"></a>다음 단계

지금까지 Microsoft Azure의 가상 머신 이미지에 기반을 둔 최신 Oracle 솔루션에 대한 개요를 살펴보았습니다. 다음 단계에서는 Azure에서 첫 번째 Oracle 데이터베이스를 배포합니다.

> [!div class="nextstepaction"]
> [Azure에서 Oracle 데이터베이스 만들기](oracle-database-quick-create.md)
