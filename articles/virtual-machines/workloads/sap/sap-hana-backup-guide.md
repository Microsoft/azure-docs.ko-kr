---
title: Azure Virtual Machines의 SAP HANA Backup 가이드 | Microsoft Docs
description: Azure 가상 머신의 SAP HANA에 대한 두 가지 주요 백업 방법을 제공합니다
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: b5a83b3976dd3d3af1bfd5695815f7571d73dd9d
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88652188"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA Backup 가이드

## <a name="getting-started"></a>시작하기

Azure 가상 머신에서 실행되는 SAP HANA에 대한 백업 가이드에서는 Azure 관련 항목만 설명합니다. 일반적인 SAP HANA 백업 관련 항목은 SAP HANA 설명서를 확인 하세요. 사용자는 주요 데이터베이스 백업 전략, 이유 및 동기를 사용 하 여 사운드 및 유효한 백업 전략에 대해 잘 알고 있어야 하며, 백업 절차, 백업 보존 기간 및 복원 절차에 대 한 회사의 요구 사항을 알고 있어야 합니다.

SAP HANA는 Azure M-Series와 같은 다양한 Azure VM 유형에서 공식적으로 지원됩니다. SAP HANA 인증 된 Azure Vm 및 HANA Large Instance 유닛의 전체 목록은 [인증 된 IaaS 플랫폼 찾기](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)를 확인 하세요. Microsoft Azure는 SAP HANA 물리적 서버에서 가상화 되지 않은를 실행 하는 여러 단위를 제공 합니다. 이 서비스를 [HANA Large Instances](hana-overview-architecture.md)라고 합니다. 이 가이드에서는 HANA Large Instances에 대 한 백업 프로세스 및 도구에 대해서는 다루지 않습니다. 하지만 Azure virtual machines로 제한 될 예정입니다. HANA Large Instances를 사용 하는 백업/복원 프로세스에 대 한 자세한 내용은 [Hli 백업 및 복원](./hana-backup-restore.md)문서를 참조 하세요.

이 문서의 초점은 Azure virtual machines에서 SAP HANA에 대 한 세 가지 백업 가능성에 있습니다.

- [Azure Backup 서비스](../../../backup/backup-overview.md) 를 통한 HANA 백업 
- Azure Linux Virtual Machine의 파일 시스템에 HANA 백업([파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 참조)
- 수동으로 또는 Azure Backup 서비스를 사용 하 여 Azure storage blob 스냅숏 기능을 사용 하는 저장소 스냅숏을 기반으로 하는 HANA 백업


SAP HANA는 타사 백업 도구에서 SAP HANA와 직접 통합할 수 있게 하는 백업 API를 제공합니다. Azure Backup 서비스 또는 [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) 와 같은 제품은이 소유 인터페이스를 사용 하 여 SAP HANA 데이터베이스를 트리거하거나 로그 백업을 다시 실행 합니다. 


Azure에서 지원 되는 SAP 소프트웨어를 찾는 방법에 대 한 정보는 [azure 배포에 대해 지원 되는 sap 소프트웨어](./sap-supported-product-on-azure.md)문서에서 찾을 수 있습니다.

## <a name="azure-backup-service"></a>Azure Backup 서비스

표시 되는 첫 번째 시나리오는 Azure Backup 서비스가 SAP HANA 인터페이스를 사용 하 여 `backint` SAP HANA 데이터베이스에서를 사용 하 여 스트리밍 백업을 수행 하는 시나리오입니다. 또는 Azure Backup 서비스에 대 한 보다 일반적인 기능을 사용 하 여 응용 프로그램 일치 디스크 스냅숏을 만들고이를 Azure Backup 서비스로 전송 합니다.

Azure Backup는 [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)라는 소유 SAP HANA 인터페이스를 사용 하 여 SAP HANA에 대 한 백업 솔루션으로 인증 됩니다. 솔루션에 대 한 자세한 내용, 해당 기능 및 사용 가능한 Azure 지역에 대 한 자세한 내용은 [Azure vm의 SAP HANA 데이터베이스 백업에 대 한 지원 매트릭스](../../../backup/sap-hana-backup-support-matrix.md#scenario-support)문서를 참조 하세요. HANA에 대 한 Azure Backup 서비스에 대 한 자세한 내용 및 원칙은 [Azure vm의 SAP HANA 데이터베이스 백업에 대 한](../../../backup/sap-hana-db-about.md)문서를 참조 하세요. 

Azure Backup 서비스를 활용 하는 두 번째 가능성은 Azure Premium Storage의 디스크 스냅숏을 사용 하 여 응용 프로그램 일치 백업을 만드는 것입니다. [Azure Ultra disk](../../disks-enable-ultra-ssd.md) 및 [AZURE NETAPP FILES](https://azure.microsoft.com/services/netapp/) 같은 다른 HANA 인증 azure 저장소는 Azure Backup 서비스를 통해 이러한 종류의 스냅숏을 지원 하지 않습니다. 이러한 문서를 읽습니다.

- [Azure에서 VM 백업 인프라 계획](../../../backup/backup-azure-vms-introduction.md)
- [Azure Linux VM의 애플리케이션 일치 백업](../../../backup/backup-azure-linux-app-consistent.md) 

이 작업 시퀀스는 다음과 같습니다.

- Azure Backup 응용 프로그램을 배치 하는 프리 스냅숏 스크립트를 실행 해야 합니다 .이 경우에는 SAP HANA 일관 된 상태입니다.
- 이 일관 된 상태가 확인 되 면 Azure Backup에서 디스크 스냅숏을 실행 합니다.
- 스냅숏을 완료 한 후 Azure Backup는 프리 스냅숏 스크립트에서 수행한 작업을 실행 취소 합니다.
- 성공적으로 실행 된 후에는 데이터를 백업 자격 증명 모음으로 스트리밍할 Azure Backup

SAP HANA의 경우 대부분의 고객은 SAP HANA 다시 실행 로그가 포함 된 볼륨에 대해 Azure 쓰기 가속기를 사용 하 게 됩니다. Azure Backup 서비스는 스냅숏에서 이러한 볼륨을 자동으로 제외 합니다. 이 제외는 복원에 대 한 HANA의 기능에는 영향을 주지 않습니다. 하지만 거의 모든 다른 SAP 지원 DBMS를 사용 하 여 복원 하는 기능을 차단 합니다.

이러한 가능성의 단점은 사용자 고유의 프리 스냅숏 및 포스트 스냅숏 스크립트를 개발 해야 한다는 점입니다. 프리 스냅숏 스크립트는 HANA 스냅숏을 만들고 최종 예외 사례를 처리 해야 합니다. 반면, 포스트 스냅숏 스크립트는 HANA 스냅숏을 다시 삭제 해야 합니다. 필요한 논리에 대 한 자세한 내용은 [SAP 지원 정보 #2039883](https://launchpad.support.sap.com/#/notes/2039883)를 참조 하세요. 이 문서에서 ' 저장소 스냅숏 작성 시 데이터 일관성 SAP HANA ' 섹션의 고려 사항은 이러한 백업 유형에 완전히 적용 됩니다.

> [!NOTE]
> 여러 데이터베이스 컨테이너를 사용 하는 배포의 SAP HANA에 대 한 디스크 스냅숏 기반 백업, 최소 버전의 HANA 2.0 SP04 필요
> 

이 문서의 뒷부분에 있는 저장소 스냅숏에 대 한 세부 정보를 참조 하세요.

![그림: 현재 VM 상태를 저장하는 두 가지 방법](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>기타 HANA 백업 방법
고려할 수 있는 다른 백업 방법에는 세 가지가 있습니다.

- Azure NetApp Files (ANF)를 기반으로 하는 NFS 공유에 대 한 백업 ANF에는 백업을 저장 하는 볼륨의 스냅숏을 만들 수 있는 기능이 있습니다. 백업을 작성 하는 데 필요한 처리량을 고려 하 여이 솔루션은 비용이 많이 들 수 있습니다. HANA가 Azure native NFS 공유에 직접 백업을 쓸 수 있기 때문에 쉽게 설정할 수 있습니다.
- 표준 SSD 또는 Azure Premium Storage의 VM에 연결 된 디스크에 대해 HANA 백업 실행 다음 단계로 Azure Blob storage에 대해 이러한 백업 파일을 복사할 수 있습니다. 이 전략은 가격이 좋을 수 있습니다.
- 표준 SSD 또는 Azure Premium Storage의 VM에 연결 된 디스크에 대해 HANA 백업 실행 다음 단계로 디스크는 정기적으로 스냅숏 됩니다. 첫 번째 스냅숏 후에는 증분 스냅숏을 사용 하 여 비용을 줄일 수 있습니다.

![그림: VM 내부에서 SAP HANA 파일 백업을 수행하는 옵션](media/sap-hana-backup-guide/other-hana-backup-paths.png)

이 그림에서는 VM 내부에서 SAP HANA 파일 백업을 수행한 다음 다양한 도구를 사용하여 다른 위치에 HANA 백업 파일을 저장하는 옵션을 보여 줍니다. 그러나 타사 백업 서비스 또는 Azure Backup 서비스와 관련 되지 않은 모든 솔루션은 공통적인 몇 가지 기능이 있습니다. 이러한 기능 중 일부는 보존 관리, 자동 복원 프로세스 및 Azure Backup 서비스 또는 기타 특수 한 타사 백업 제품군 및 서비스에서 제공 하는 자동 지정 시간 복구 제공과 같이 나열 될 수 있습니다. 이러한 타사 서비스 대부분은 Azure에서 실행할 수 있습니다. 


## <a name="sap-resources-for-hana-backup"></a>HANA 백업을 위한 SAP 리소스

### <a name="sap-hana-backup-documentation"></a>SAP HANA 백업 설명서

- [SAP HANA 관리 소개](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)(영문)
- [Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문)
- [ABAP DBACOCKPIT을 사용하여 HANA Backup 예약](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)(영문)
- [데이터 Backup 예약(SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)(영문)
- [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148): SAP HANA 백업에 대한 FAQ
- [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883): SAP HANA 데이터베이스 및 스토리지 스냅샷에 대한 FAQ
- [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529): 백업 및 복구에 부적합한 네트워크 파일 시스템

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA 백업의 정확성을 확인하는 방법
백업 방법에 관계 없이 다른 시스템에 대해 테스트 복원을 실행 하는 것은 절대적으로 필요 합니다. 이 방법은 백업이 올바른지, 백업 및 복원을 위한 내부 프로세스가 예상대로 작동하는지 확인하는 방법을 제공합니다. 백업을 복원 하는 것은 인프라 요구 사항으로 인해 온-프레미스로 장애물 수 있지만,이 목적을 위해 필요한 리소스를 일시적으로 제공 하 여 클라우드에서 더 쉽게 수행할 수 있습니다. 복원 기능을 사용 하 여 백업 파일을 확인할 수 있는 HANA와 함께 제공 되는 도구가 있습니다. 그러나 빈번한 복원 연습은 데이터베이스 복원 프로세스를 테스트 하 고 해당 프로세스를 작업 담당자에 게 학습 하는 것입니다.

간단한 복원을 수행하고 HANA가 가동 중인지 확인하는 것만으로는 충분하지 않다는 것을 명심해야 합니다. 테이블 일관성 확인을 실행 하 여 복원 된 데이터베이스에 문제가 있는지 확인 해야 합니다. SAP HANA는 [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584)에서 설명하는 여러 종류의 일관성 검사를 제공합니다.

테이블 일관성 검사에 대한 내용은 SAP 웹 사이트의 [테이블 및 카탈로그 일관성 검사](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)(영문)에서 찾을 수도 있습니다.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 백업 및 스토리지 스냅샷의 장단점

SAP에서는 HANA 백업과 스토리지 스냅샷을 비교하여 어느 한 쪽을 선택하지 않습니다. 장단점을 나열하여 상황과 사용 가능한 스토리지 기술에 따라 사용할 대상을 결정할 수 있습니다([Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문) 참조).

Azure에서 Azure blob snapshot&#39;기능이 여러 디스크 간에 파일 시스템 일관성을 제공 하지 않는다는 사실을 알고 있어야 합니다 ( [PowerShell에서 blob 스냅숏 사용](/archive/blogs/cie/using-blob-snapshots-with-powershell)참조). 

또한 [스냅샷 요금 청구 방법 이해](/rest/api/storageservices/understanding-how-snapshots-accrue-charges) 문서에서 설명한 대로 Blob 스냅샷을 자주 사용하는 경우 청구의 함축적 의미를 이해해야 합니다. 이는 Azure 가상 디스크를 사용하는 것만큼 명확하지 않기 때문입니다.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>스토리지 스냅샷을 만들 때의 SAP HANA 데이터 일관성

앞에서 설명한 대로 Azure Backup의 스냅숏 백업 기능을 설명 하는 것 처럼, 파일 시스템 및 응용 프로그램 일관성은 저장소 스냅숏을 만들 때 필수입니다. 문제를 방지하는 가장 쉬운 방법은 SAP HANA, 심지어는 전체 가상 컴퓨터를 종료하는 것입니다. 프로덕션 인스턴스에 적합 하지 않은 항목입니다.

> [!NOTE]
> 여러 데이터베이스 컨테이너를 사용 하는 배포의 SAP HANA에 대 한 디스크 스냅숏 기반 백업, 최소 버전의 HANA 2.0 SP04 필요
> 

Azure storage는 스냅숏 프로세스 중에 VM에 연결 된 여러 디스크 또는 볼륨에서 파일 시스템 일관성을 제공 하지 않습니다. 즉, 응용 프로그램에서 스냅숏에서 응용 프로그램 일관성을 전달 해야 합니다 .이 경우에는 SAP HANA 자체입니다. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) 에는 저장소 스냅숏으로 SAP HANA 백업에 대 한 중요 한 정보가 있습니다. 예를 들어 XFS 파일 시스템을 사용 하는 경우 응용 프로그램 일관성을 제공 하기 위해 저장소 스냅숏을 시작 하기 전에 **XFS \_ freeze** 를 실행 해야 합니다 (XFS 고정에 대 한 자세한 내용은 [XFS 고정 \_ (8)-Linux 매뉴얼 페이지](https://linux.die.net/man/8/xfs_freeze) ** \_ 참조).**

다음 단계에서는 네 개의 Azure 가상 디스크를 확장하는 XFS 파일 시스템이 있다고 가정하여 HANA 데이터 영역을 나타내는 일관된 스냅샷을 제공합니다.

1. HANA 데이터 스냅숏 만들기 준비
1. 모든 디스크/볼륨의 파일 시스템을 고정 합니다 (예: **xfs \_ freeze**사용).
1. Azure에서 필요한 모든 Blob 스냅샷 만들기
1. 파일 시스템 고정 취소(unfreeze)
1. HANA 데이터 스냅숏 확인 (스냅숏이 삭제 됨)

Azure Backup 기능을 사용 하 여 응용 프로그램 일치 스냅숏 백업을 수행 하는 경우 프리 스냅숏 스크립트에 대해에서 #1 단계를 코딩/스크립팅 해야 합니다. Azure Backup 서비스는 #2 및 #3 단계를 실행 합니다. 사후 스냅숏 스크립트에서 코드를 다시 제공 해야 하 #4 단계 및 #5. Azure backup 서비스를 사용 하지 않는 경우에는 코드/스크립트 단계 #2 하 고 직접 #3 해야 합니다.
HANA 데이터 스냅숏 만들기에 대 한 자세한 내용은 다음 문서에서 찾을 수 있습니다.

- [HANA 데이터 스냅숏] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- #1 단계를 수행 하는 방법에 대 한 자세한 내용은 [데이터 스냅숏 만들기 (네이티브 SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 문서에서 찾을 수 있습니다. 
- #5 단계에서 필요에 따라 HANA 데이터 스냅숏을 확인/삭제 하는 방법에 대 한 자세한 내용은 [데이터 스냅숏 만들기 (네이티브 SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 문서에서 찾을 수 있습니다. 

HANA 스냅샷을 확인하는 것이 중요합니다. &quot;기록 중 복사&quot;(Copy-on-Write)로 인해 스냅샷 준비 모드에 있는 동안 SAP HANA에 추가 디스크 공간이 필요하지 않을 수 있습니다. 또한 먼저 SAP HANA 스냅샷이 확인되어야 새 백업을 시작할 수 있습니다.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 백업 일정 전략

[백업 및 복구 전략을 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) 하는 SAP HANA 문서에는 백업을 수행 하기 위한 기본 계획이 명시 되어 있습니다. SAP HANA에 대 한 백업/복원 전략 및 프로세스를 정의 하는 다른 DBMS와 HANA 및 사용자 환경에 대 한 SAP 설명서를 사용 합니다. 여러 유형의 백업 및 보존 기간에 대 한 시퀀스는 제공 해야 하는 Sla에 따라 달라 집니다.


### <a name="sap-hana-backup-encryption"></a>SAP HANA 백업 암호화

SAP HANA에서는 데이터 및 로그의 암호화를 제공합니다. SAP HANA 데이터와 로그가 암호화 되지 않은 경우에는 기본적으로 백업이 암호화 되지 않습니다. 그러나 SAP HANA은 [SAP HANA 백업 암호화](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)에 설명 된 대로 별도의 백업 암호화를 제공 합니다. 이전 버전의 SAP HANA를 실행 하는 경우 백업 암호화가 이미 제공 된 기능의 일부 인지 여부를 확인 해야 할 수 있습니다.  


## <a name="next-steps"></a>다음 단계
* [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) - 파일 기반 백업 옵션을 설명합니다.
* Azure (큰 인스턴스)의 SAP HANA에 대 한 고가용성 및 재해 복구 계획을 수립 하는 방법을 알아보려면 [azure에서 SAP HANA (큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조 하세요.
