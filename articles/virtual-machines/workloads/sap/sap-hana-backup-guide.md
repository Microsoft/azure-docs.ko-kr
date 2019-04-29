---
title: Azure Virtual Machines의 SAP HANA Backup 가이드 | Microsoft Docs
description: Azure 가상 머신의 SAP HANA에 대한 두 가지 주요 백업 방법을 제공합니다
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: rclaus
ms.openlocfilehash: 89896fab7b1c359007ed23d4f9d9771e366ca68a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60937031"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Azure Virtual Machines의 SAP HANA Backup 가이드

## <a name="getting-started"></a>시작하기

Azure 가상 머신에서 실행되는 SAP HANA에 대한 백업 가이드에서는 Azure 관련 항목만 설명합니다. 일반적인 SAP HANA 백업 관련 항목은 SAP HANA 설명서를 참조하세요(이 문서 뒷부분의 _SAP HANA 백업 설명서_ 참조).

이 문서는 Azure 가상 머신의 SAP HANA에 제공되는 다음 두 가지 주요 백업 방법에 집중하고 있습니다.

- Azure Linux Virtual Machine의 파일 시스템에 HANA 백업([파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 참조)
- 수동으로 Azure 저장소 Blob 스냅숏 기능을 사용하는 저장소 스냅숏 또는 Azure Backup 서비스를 기반으로 한 HANA 백업([저장소 스냅숏에 기반한 SAP HANA 백업](sap-hana-backup-storage-snapshots.md) 참조)

SAP HANA는 타사 백업 도구에서 SAP HANA와 직접 통합할 수 있게 하는 백업 API를 제공합니다. (이 내용은 이 가이드의 범위를 벗어납니다.) 현재 이 API를 기반으로 하여 SAP HANA와 Azure Backup 서비스를 직접 통합할 수는 없습니다.

SAP HANA는 Azure M-Series와 같은 다양한 Azure VM 유형에서 공식적으로 지원됩니다. SAP HANA 인증 Azure VM의 전체 목록을 보려면 [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)(공인 IaaS 플랫폼 찾기)를 확인하세요. 이 문서는 Azure의 SAP HANA에 대한 새 제품이 출시됨에 따라 업데이트될 예정입니다.

또한 Azure에서 사용할 수 있는 SAP HANA 하이브리드 솔루션도 있으며, 이 경우 SAP HANA는 물리적 서버에서 가상화되지 않은 상태로 실행됩니다. 그러나 이 SAP HANA Azure 백업 가이드에서는 SAP HANA가 &quot;큰 인스턴스&quot;에서 실행되는 것이 아니라 Azure VM에서 실행되는 순수한 Azure 환경을 다루고 있습니다. 저장소 스냅숏을 기반으로 하는 &quot;큰 인스턴스&quot;에 대한이 백업 솔루션에 대한 자세한 내용은 [Azure의 SAP HANA(큰 인스턴스) 개요 및 아키텍처](hana-overview-architecture.md)를 참조하세요.

Azure에서 지원되는 SAP 제품에 대한 일반 정보는 [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533)에 있습니다.

다음 세 가지 그림에서는 현재 기본 Azure 기능을 사용하는 SAP HANA 백업 옵션에 대한 개요를 제공하며, 향후의 잠재적인 세 가지 백업 시나리오를 보여 줍니다. [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 및 [저장소 스냅숏에 기반한 SAP HANA 백업](sap-hana-backup-storage-snapshots.md) 관련 문서에서 다중 테라바이트 크기의 SAP HANA 백업에 대한 크기 및 성능 고려 사항을 포함하여 이러한 옵션에 대해 자세히 설명합니다.

![그림: 현재 VM 상태를 저장하는 두 가지 방법](media/sap-hana-backup-guide/image001.png)

이 그림에서는 Azure Backup 서비스 또는 VM 디스크의 수동 스냅숏을 통해 현재 VM 상태를 저장하는 방법을 보여 줍니다. 이 방법을 사용하면 SAP HANA 백업을 관리할 필요가 없습니다. 디스크 스냅숏 시나리오의 과제는 파일 시스템 일관성 및 애플리케이션 일치 디스크 상태입니다. 일관성 원칙은 이 문서 뒷부분의 _저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성_ 섹션에서 설명합니다. 또한 SAP HANA 백업과 관련된 Azure Backup 서비스의 기능 및 제한 사항은 이 문서의 뒷부분에서 설명합니다.

![그림: VM 내부에서 SAP HANA 파일 백업을 수행하는 옵션](media/sap-hana-backup-guide/image002.png)

이 그림에서는 VM 내부에서 SAP HANA 파일 백업을 수행한 다음 다양한 도구를 사용하여 다른 위치에 HANA 백업 파일을 저장하는 옵션을 보여 줍니다. HANA 백업을 수행하면 스냅숏 기반 백업 솔루션보다 많은 시간이 필요하지만 무결성 및 일관성과 관련하여 장점이 있습니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다.

![그림: 향후의 잠재적인 SAP HANA 백업 시나리오](media/sap-hana-backup-guide/image003.png)

이 그림에서는 향후의 잠재적인 SAP HANA 백업 시나리오를 보여 줍니다. SAP HANA가 보조 복제에서 백업하는 것을 허용한다면 백업 전략에 추가 옵션을 추가하면 좋겠지만, 현재 SAP HANA Wiki의 게시물에 따르면 그렇게 할 수 없습니다.

_&quot;보조 쪽에서 백업을 수행할 수 있을까요?_

_아니요, 현재는 기본 쪽에서만 데이터 및 로그 백업을 수행할 수 있습니다. 자동 로그 백업을 사용하도록 설정된 경우 보조 쪽으로 인계한 후에 로그 백업이 자동으로 보조 쪽에 기록됩니다.&quot;_

## <a name="sap-resources-for-hana-backup"></a>HANA 백업을 위한 SAP 리소스

### <a name="sap-hana-backup-documentation"></a>SAP HANA 백업 설명서

- [SAP HANA 관리 소개](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)(영문)
- [Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문)
- [ABAP DBACOCKPIT을 사용하여 HANA Backup 예약](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)(영문)
- [데이터 Backup 예약(SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)(영문)
- [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148): SAP HANA 백업에 대한 FAQ
- [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883): SAP HANA 데이터베이스 및 저장소 스냅숏에 대한 FAQ
- [SAP Note 1820529](https://launchpad.support.sap.com/#/notes/1820529): 백업 및 복구에 부적합한 네트워크 파일 시스템

### <a name="why-sap-hana-backup"></a>SAP HANA를 백업하는 이유

Azure 스토리지는 기본적으로 가용성과 안정성을 제공합니다(Azure 스토리지에 대한 자세한 내용은 [Microsoft Azure Storage 소개](../../../storage/common/storage-introduction.md) 참조).

&quot;백업&quot;은 적어도 Azure SLA에 따라 SAP HANA 서버 VM에 연결된 Azure VHD에서 SAP HANA 데이터 및 로그 파일을 유지해야 합니다. 이 방법은 VM 실패를 다루는 한편, SAP HANA 데이터 및 로그 파일의 잠재적 손상이나 우발적인 데이터 또는 파일 삭제와 같은 논리적 오류는 다루지 않습니다. 또한 규정 준수 또는 법적 이유로도 Backup이 필요합니다. 요약하자면, SAP HANA 백업은 항상 필요합니다.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA 백업의 정확성을 확인하는 방법
저장소 스냅숏을 사용하는 경우 다른 시스템에서 테스트 복원을 실행하는 것이 좋습니다. 이 방법은 백업이 올바른지, 백업 및 복원을 위한 내부 프로세스가 예상대로 작동하는지 확인하는 방법을 제공합니다. 이는 온-프레미스에서 중요한 걸림돌이지만, 이 목적을 위해 필요한 리소스를 일시적으로 제공하여 클라우드에서 매우 쉽게 수행할 수 있습니다.

간단한 복원을 수행하고 HANA가 가동 중인지 확인하는 것만으로는 충분하지 않다는 것을 명심해야 합니다. 이상적으로는 테이블 일관성 검사를 실행하여 복원된 데이터베이스가 정상인지 확인해야 합니다. SAP HANA는 [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584)에서 설명하는 여러 종류의 일관성 검사를 제공합니다.

테이블 일관성 검사에 대한 내용은 SAP 웹 사이트의 [테이블 및 카탈로그 일관성 검사](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)(영문)에서 찾을 수도 있습니다.

표준 파일 백업의 경우 테스트 복원이 필요하지 않습니다. 복원에 사용할 수 있는 백업을 확인하는 데 유용한 두 가지 SAP HANA 도구, 즉 hdbbackupdiag 및 hdbbackupcheck가 있습니다. 이러한 도구에 대한 자세한 내용은 [복구가 가능한지 수동으로 확인](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm)(영문)을 참조하세요.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>HANA 백업 및 저장소 스냅숏의 장단점

SAP에서는 HANA 백업과 저장소 스냅숏을 비교하여 어느 한 쪽을 선택하지 않습니다. 장단점을 나열하여 상황과 사용 가능한 저장소 기술에 따라 사용할 대상을 결정할 수 있습니다([Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문) 참조).

Azure에서 Azure Blob 스냅숏 기능이 파일 시스템 일관성을 보장하지 않는다는 사실을 알고 있어야 합니다([PowerShell과 함께 Blob 스냅숏 사용](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)(영문) 참조). 다음에 나오는 _저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성_ 섹션에서는 이 기능과 관련된 몇 가지 고려 사항에 대해 설명합니다.

또한이 문서에 설명 된 대로 blob 스냅숏을 사용 하 여 자주 작업 하는 경우 청구의 함축적 의미를 이해 해야 하나: [이해 하는 방법을 스냅숏에서 요금이 발생](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)-이 되었습니다&#39;Azure 가상 디스크를 사용 하는 것 만큼 명확 하지.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>저장소 스냅숏을 만들 때의 SAP HANA 데이터 일관성

스토리지 스냅숏을 만들 때 파일 시스템 및 애플리케이션 일관성은 복잡한 문제입니다. 문제를 방지하는 가장 쉬운 방법은 SAP HANA, 심지어는 전체 가상 컴퓨터를 종료하는 것입니다. 종료는 데모 또는 프로토타입, 심지어는 개발 시스템에서도 수행할 수 있지만, 프로덕션 시스템에서는 옵션이 아닙니다.

Azure에서는 Azure Blob 스냅숏 기능이 파일 시스템 일관성을 보장하지 않는다는 사실을 명심해야 합니다. 그러나 하나의 가상 디스크만 있는 한 SAP HANA 스냅숏 기능을 사용하여 제대로 작동합니다. 이처럼 단일 디스크만 사용하는 경우에도 추가 항목을 확인해야 합니다. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883)에는 저장소 스냅숏을 통한 SAP HANA 백업에 대한 중요한 정보가 있습니다. 예를 들어 XFS 파일 시스템을 사용하면 일관성을 보장하기 위해 저장소 스냅숏을 시작하기 전에 **xfs\_freeze**를 실행해야 합니다(**xfs\_freeze**에 대한 자세한 내용은 [xfs\_freeze(8) - Linux 매뉴얼 페이지](https://linux.die.net/man/8/xfs_freeze) 참조).

단일 파일 시스템이 여러 디스크/볼륨에 걸쳐 있는 경우 일관성 원칙은 더욱 어려워집니다. 예를 들어 mdadm 또는 LVM 및 스트라이핑을 사용합니다. 위에서 언급한 SAP Note에서 다음과 같이 설명하고 있습니다.

_&quot;그러나 저장소 시스템은 SAP HANA 데이터 볼륨마다 저장소 스냅숏을 만드는 동안 I/O 일관성을 보장해야 합니다. 즉 SAP HANA 서비스 관련 데이터 볼륨의 스냅숏 작업은 원자성 작업이어야 합니다.&quot;_

다음 단계에서는 네 개의 Azure 가상 디스크를 확장하는 XFS 파일 시스템이 있다고 가정하여 HANA 데이터 영역을 나타내는 일관된 스냅숏을 제공합니다.

- HANA 스냅숏 준비
- 파일 시스템 고정(예: **xfs\_freeze** 사용).
- Azure에서 필요한 모든 Blob 스냅숏 만들기
- 파일 시스템 고정 취소(unfreeze)
- HANA 스냅숏 확인

파일 시스템에 관계 없이 모든 경우에 안전한 측면에서 위의 절차를 사용하는 것이 좋습니다. 또는 단일 디스크를 사용하거나 여러 디스크에서 mdadm 또는 LVM을 통해 스트라이핑합니다.

HANA 스냅숏을 확인하는 것이 중요합니다. &quot;기록 중 복사&quot;(Copy-on-Write)로 인해 스냅숏 준비 모드에 있는 동안 SAP HANA에 추가 디스크 공간이 필요하지 않을 수 있습니다. 또한 먼저 SAP HANA 스냅숏이 확인되어야 새 백업을 시작할 수 있습니다.

Azure Backup 서비스는 Azure VM 확장을 사용하여 파일 시스템 일관성을 처리합니다. 이러한 VM 확장은 독립 실행형으로 제공되지 않습니다. 그럼에도 불구하고 SAP HANA 일관성을 관리해야 합니다. 자세한 내용은 [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 관련 문서를 참조하세요.

### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA 백업 일정 전략

[Backup 및 복구 전략 계획](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)(영문) SAP HANA 문서에는 백업을 수행하는 기본 계획이 나와 있습니다.

- 저장소 스냅숏(매일)
- 파일 또는 백업 형식을 사용한 전체 데이터 백업(매주 1회)
- 자동 로그 백업

필요에 따라 저장소 스냅숏 없이 완전히 이동할 수 있습니다. 증분 또는 차등 백업과 같은 HANA 델타 백업으로 대체할 수 있습니다([델타 Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)(영문) 참조).

HANA 관리 가이드에서는 예제 목록을 제공합니다. 다음 백업 순서를 사용하여 SAP HANA를 특정 시점으로 복구하도록 제안합니다.

1. 전체 데이터 백업
2. 차등 백업
3. 증분 백업 1
4. 증분 백업 2
5. 로그 백업

특정 백업 유형을 수행하는 시기와 빈도에 대한 정확한 일정과 관련한 일반적인 지침은 제시할 수 없습니다. 이는 고객마다 요구 사항이 매우 다르며, 시스템에서 발생하는 데이터 변경의 정도에 따라 달라지기 때문입니다. 일반적인 지침으로 볼 수 있는 SAP 측면의 한 가지 기본 권장 사항은 전체 HANA 백업을 일주일에 한 번씩 수행하는 것입니다.
로그 백업과 관련하여 [로그 Backup](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)(영문) SAP HANA 설명서를 참조하세요.

또한 SAP에서는 무한정 증가하지 않도록 몇 가지 백업 카탈로그 정리 작업을 수행하도록 권장합니다([Backup 카탈로그 및 Backup Storage 정리](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)(영문) 참조).

### <a name="sap-hana-configuration-files"></a>SAP HANA 구성 파일

[SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)의 FAQ에서 설명한 대로 SAP HANA 구성 파일은 표준 HANA 백업의 일부가 아닙니다. 이러한 구성 파일은 시스템을 복원하는 데 반드시 필요하지는 않습니다. HANA 구성은 복원 후에 수동으로 변경할 수 있습니다. 복원 프로세스 중에 동일한 사용자 지정 구성을 가져오려면 HANA 구성 파일을 별도로 백업해야 합니다.

표준 HANA 백업이 전용 HANA 백업 파일 시스템으로 이동하는 경우, 해당 구성 파일도 동일한 백업 파일 시스템으로 복사한, 다음 멋진 Blob Storage와 같은 최종 스토리지 대상으로 모두 복사하면 됩니다.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit은 브라우저를 통해 SAP HANA를 모니터링하고 관리할 수 있는 방법을 제공합니다. 또한 SAP HANA 백업을 처리할 수 있으므로 SAP HANA Studio 및 ABAP DBACOCKPIT을 대신하여 사용할 수 있습니다(자세한 내용은 [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm)(영문) 참조).

![그림: SAP HANA Cockpit 데이터베이스 관리 화면](media/sap-hana-backup-guide/image004.png)

이 그림에서는 SAP HANA Cockpit 데이터베이스 관리 화면과 백업 타일(왼쪽)보여 줍니다. 백업 타일을 보려면 로그인 계정에 적절한 사용자 권한이 필요합니다.

![진행 중인 백업을 모니터링하는 SAP HANA Cockpit](media/sap-hana-backup-guide/image005.png)

Backup이 진행 중일 때 SAP HANA Cockpit에서 모니터링할 수 있으며, 완료되면 모든 백업 세부 정보를 사용할 수 있습니다.

![Gnome 데스크톱과 함께 Azure SLES 12 VM에서 Firefox를 사용하는 예](media/sap-hana-backup-guide/image006.png)

앞서의 스크린샷들은 Azure Windows VM에서 만들어졌으며, 이 스크린샷은 Gnome 데스크톱과 함께 Azure SLES 12 VM에서 Firefox를 사용하는 예입니다. SAP HANA Cockpit에서 SAP HANA 백업 일정을 정의하는 옵션을 보여 줍니다. 또한 누구나 볼 수 있듯이 백업 파일의 접두사로 날짜/시간을 제안합니다. SAP HANA Studio에서 전체 파일 백업을 수행할 때 기본 접두사는 &quot;COMPLETE\_DATA\_BACKUP&quot;입니다. 고유한 접두사를 사용하는 것이 좋습니다.

### <a name="sap-hana-backup-encryption"></a>SAP HANA 백업 암호화

SAP HANA에서는 데이터 및 로그의 암호화를 제공합니다. SAP HANA 데이터 및 로그가 암호화되지 않으면 백업도 암호화되지 않습니다. SAP HANA 백업을 암호화하기 위해 어떤 형태의 타사 솔루션을 사용하는 것은 고객에게 달려 있습니다. SAP HANA 암호화에 대한 자세한 내용은 [데이터 및 로그 볼륨 암호화](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm)(영문)를 참조하세요.

Microsoft Azure에서 고객은 IaaS VM 암호화 기능을 사용하여 암호화할 수 있습니다. 예를 들어 VM에 연결된 전용 데이터 디스크를 사용할 수 있습니다. 이러한 디스크는 SAP HANA 백업을 저장한 다음 복사본을 만드는 데 사용됩니다.

Azure Backup 서비스는 암호화된 VM/디스크를 처리할 수 있습니다([Azure Backup으로 암호화된 가상 머신을 백업 및 복원하는 방법](../../../backup/backup-azure-vms-encryption.md) 참조).

또 다른 옵션은 암호화를 사용하지 않고 SAP HANA VM과 해당 디스크를 유지 관리하고, 암호화를 사용하도록 설정된 스토리지 계정에 SAP HANA 백업 파일을 저장하는 것입니다([휴지 상태의 데이터에 대한 Azure Storage 서비스 암호화](../../../storage/common/storage-service-encryption.md) 참조).

## <a name="test-setup"></a>테스트 설정

### <a name="test-virtual-machine-on-azure"></a>Azure에서 Virtual Machine 테스트

테스트를 수행하기 위해 다음과 같은 백업/복원 테스트에 Azure GS5 VM의 SAP HANA 설치를 사용했습니다. 원칙은 M-Series VM의 경우와 동일합니다.

![그림: HANA 테스트 VM에 대한 Azure Portal 개요의 일부](media/sap-hana-backup-guide/image007.png)

이 그림에서는 HANA 테스트 VM에 대한 Azure Portal 개요의 일부를 보여 줍니다.

### <a name="test-backup-size"></a>백업 크기 테스트

![그림: HANA Studio 백업 콘솔 - HANA 인덱스 서버에 대한 백업 파일 크기: 229GB](media/sap-hana-backup-guide/image008.png)

실질적인 성능 데이터를 얻기 위해 총 데이터 백업 크기가 200GB 이상이 되도록 더미 테이블에 데이터가 채워졌습니다. HANA Studio의 백업 콘솔에서 가져온 그림에서 HANA 인덱스 서버에 대한 백업 파일 크기는 229GB입니다. 테스트의 경우 SAP HANA Studio의 기본 백업 접두사인 "COMPLETE_DATA_BACKUP"이 사용되었습니다. 실제 프로덕션 시스템에서는 더 유용한 접두사를 정의해야 합니다. SAP HANA Cockpit에서 날짜/시간을 제안합니다.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Azure 저장소에 파일을 직접 복사하는 테스트 도구

SAP HANA 백업 파일을 Azure Blob Storage 또는 Azure 파일 공유로 직접 전송하는 데 blobxfer 도구가 사용되었습니다. 이는 두 대상을 모두 지원하고 명령줄 인터페이스로 인해 자동화 스크립트에 쉽게 통합될 수 있기 때문입니다. blobxfer 도구는 [GitHub](https://github.com/Azure/blobxfer)에서 사용할 수 있습니다.

### <a name="test-backup-size-estimation"></a>테스트 백업 크기 추정

SAP HANA의 백업 크기를 추정하는 것은 중요합니다. 이 추정값은 파일 복사 중 병렬 처리로 인해 여러 백업 파일의 최대 백업 파일 크기를 정의하여 성능을 향상시키는 데 도움이 됩니다. (자세한 내용은 이 문서의 뒷부분에서 설명합니다.) 또한 전체 백업 또는 델타 백업(증분 또는 차등)을 수행할지 여부도 결정해야 합니다.

다행히도 백업 파일의 크기를 추정하는 간단한 SQL 문(**select \* from M\_BACKUP\_SIZE\_ESTIMATIONS**)이 있습니다([데이터 Backup을 위해 파일 시스템에 필요한 공간 추정](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)(영문) 참조).

![디스크의 전체 데이터 백업 실제 크기와 거의 일치하는 SQL 문의 출력](media/sap-hana-backup-guide/image009.png)

테스트 시스템의 경우 이 SQL 문의 출력은 디스크의 전체 데이터 백업 실제 크기와 거의 일치합니다.

### <a name="test-hana-backup-file-size"></a>테스트 HANA 백업 파일 크기

![HANA 백업 파일의 최대 파일 크기를 제한할 수 있는 HANA Studio 백업 콘솔](media/sap-hana-backup-guide/image010.png)

HANA Studio 백업 콘솔을 사용하면 HANA 백업 파일의 최대 파일 크기를 제한할 수 있습니다. 샘플 환경에서 이 기능을 사용하면 하나의 230GB 백업 파일 대신 여러 개의 작은 백업 파일을 얻을 수 있습니다. 파일 크기가 작을수록 성능에 상당한 영향을 줍니다([파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) 관련 문서 참조).

## <a name="summary"></a>요약

다음 표에서는 테스트 결과에 따라 Azure 가상 머신에서 실행되는 SAP HANA 데이터베이스를 백업하는 솔루션의 장단점을 보여 줍니다.

**파일 시스템에 SAP HANA 백업, 나중에 최종 백업 대상으로 백업 파일 복사**

|해결 방법                                           |장점                                 |단점                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|VM 디스크에서 HANA 백업 유지                      |추가 관리 작업 없음     |로컬 VM 디스크 공간 사용           |
|Blob Storage에 백업 파일을 복사하는 blobxfer 도구 |여러 파일을 복사하는 병렬 처리, 멋진 Blob Storage를 사용하기 위한 선택 항목 | 추가 도구 유지 관리 및 사용자 지정 스크립팅 | 
|Powershell 또는 CLI를 통한 Blob 복사                    |추가 도구 필요하지 않음, Azure Powershell 또는 CLI를 통해 수행할 수 있음 |수동 프로세스, 고객이 복원을 위해 복사된 Blob의 스크립팅 및 관리를 처리해야 함|
|NFS 공유에 복사                                  |HANA 서버에 영향을 미치지 않고 다른 VM에서 백업 파일 사후 처리|느린 복사 프로세스|
|Azure 파일 서비스에 blobxfer 복사                |로컬 VM 디스크 공간 사용 안 함|HANA 백업에서 직접 쓰기 지원 안 함, 현재 파일 공유 크기 제한은 5TB임|
|Azure Backup 에이전트                                 | 선호하는 솔루션이 될 것임         | Linux에서는 현재 사용할 수 없음    |



**저장소 스냅숏에 기반한 SAP HANA 백업**

|해결 방법                                           |장점                                 |단점                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup 서비스                               | Blob 스냅숏에 기반한 VM 백업 허용 | 파일 수준 복원을 사용하지 않을 경우 복원 프로세스를 위한 새 VM을 만들어야 하므로 새 SAP HANA 라이선스 키가 필요함|
|수동 Blob 스냅숏                              | 고유한 VM ID를 변경하지 않고 특정 VM 디스크를 만들고 복원할 수 있는 유연성|고객이 모든 작업을 수동으로 수행해야 함|

## <a name="next-steps"></a>다음 단계
* [파일 수준의 SAP HANA Azure Backup](sap-hana-backup-file-level.md) - 파일 기반 백업 옵션을 설명합니다.
* [저장소 스냅숏에 기반한 SAP HANA 백업](sap-hana-backup-storage-snapshots.md) - 저장소 스냅숏을 기반으로 하는 백업 옵션을 설명합니다.
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
