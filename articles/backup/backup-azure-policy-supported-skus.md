---
title: Azure Policy에 대해 지원 되는 VM Sku
description: 백업에서 제공 하는 기본 제공 Azure 정책에 대해 지원 되는 지원 되는 VM Sku (게시자, 이미지 제품 및 이미지 SKU)를 설명 하는 문서입니다.
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1b6a94b0f57ecfea946654c6cae38ac335335e00
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980115"
---
# <a name="supported-vm-skus-for-azure-policy"></a>Azure Policy에 대해 지원 되는 VM Sku

Azure Backup는 **구독 또는 리소스 그룹 내의 지정 된 위치에 있는 모든 Azure vm**에 할당 될 수 있는 기본 제공 정책 (Azure Policy 사용)을 제공 합니다. 이 정책이 지정 된 범위에 할당 되 면 해당 범위에서 만들어진 모든 새 Vm은 **동일한 위치 및 구독의 기존 자격 증명 모음**에 백업 하도록 자동으로 구성 됩니다. 아래 표에는이 정책에서 지원 되는 모든 VM Sku가 나와 있습니다.

### <a name="supported-vms"></a>**지원 되는 Vm**

**정책 이름:** 동일한 위치의 기존 중앙 자격 증명 모음에 대한 위치의 Vm 백업 구성

이미지 게시자 | 이미지 제안 | 이미지 SKU
--- | --- | ---
MicrosoftWindowsServer | WindowsServer | Windows Server 2008 R2 SP1 (2008-R2-SP1)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2008 R2 SP (2008-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 Datacenter (2012-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 Datacenter (2012-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2012 R2 Datacenter (2012-R2-Datacenter)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2012 R2 Datacenter (2012-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter (2016-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 Datacenter-Server Core (2016-Datacenter-Server-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter-Server Core (2016-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2016 Datacenter (2016-smalldisk)
MicrosoftWindowsServer | WindowsServer | 컨테이너를 사용 하는 Windows Server 2019 Datacenter Server Core (2016-Datacenter-컨테이너 포함)
MicrosoftWindowsServer | WindowsServer | Windows Server 2016 원격 데스크톱 세션 호스트 2016 (2016-Datacenter--RDSH)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (2019-Datacenter)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter Server Core (2019-Datacenter-Core)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter Server Core (2019-smalldisk)
MicrosoftWindowsServer | WindowsServer | 컨테이너를 사용 하는 Windows Server 2019 Datacenter Server Core (2019-Datacenter--컨테이너 포함)
MicrosoftWindowsServer | WindowsServer | [smalldisk] 컨테이너를 사용 하는 Windows Server 2019 Datacenter Server Core (2019-smalldisk)
MicrosoftWindowsServer | WindowsServer | [smalldisk] Windows Server 2019 Datacenter (2019-smalldisk)
MicrosoftWindowsServer | WindowsServer | 컨테이너를 포함 하는 Windows Server 2019 Datacenter (2019--컨테이너 포함)
MicrosoftWindowsServer | WindowsServer | [smalldisk] 컨테이너를 포함 하는 Windows Server 2019 Datacenter (2019-smalldisk)
MicrosoftWindowsServer | WindowsServer | Windows Server 2019 Datacenter (zh-cn) (2019-Datacenter-zhcn)
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-Core-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-1709-smalldisk
MicrosoftWindowsServer | WindowsServerSemiAnnual | Datacenter-1803-smalldisk
MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 모든 이미지 Sku
MicrosoftSQLServer | SQL2016SP1-WS2016 | 모든 이미지 Sku
MicrosoftSQLServer | SQL2016-WS2016  | 모든 이미지 Sku
MicrosoftSQLServer | SQL2016SP1-WS2016-BYOL | 모든 이미지 Sku
MicrosoftSQLServer | SQL2012SP3-WS2012R2 | 모든 이미지 Sku
MicrosoftSQLServer | SQL2016-WS2012R2 | 모든 이미지 Sku
MicrosoftSQLServer | SQL2014SP2-WS2012R2 | 모든 이미지 Sku
MicrosoftSQLServer | SQL2012SP3-WS2012R2-BYOL | 모든 이미지 Sku
MicrosoftSQLServer | SQL2014SP1-WS2012R2-BYOL | 모든 이미지 Sku
MicrosoftSQLServer | SQL2014SP2-WS2012R2-BYOL | 모든 이미지 Sku
MicrosoftSQLServer | SQL2016-WS2012R2-BYOL | 모든 이미지 Sku
MicrosoftRServer | MLServer-WS2016 | 모든 이미지 Sku
MicrosoftVisualStudio | VisualStudio | 모든 이미지 Sku
MicrosoftVisualStudio | Windows | 모든 이미지 Sku
MicrosoftDynamicsAX | Dynamics  | AX7-Onebox-U8
microsoft 광고 | windows-데이터 과학-vm | 모든 이미지 Sku
MicrosoftWindowsDesktop | Windows-10 | 모든 이미지 Sku
RedHat | RHEL | 6.7, 6.8, 6.9, 6.10, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7
RedHat | RHEL-HANA | 6.7, 7.2, 7.3
SUSE | SLES | 12. X
SUSE | SLES-HPC | 12. X
SUSE | SLES-HPC-우선 순위 | 12. X
SUSE | SLES-SAP | 12. X
SUSE | SLES-SAP-BYOS | 12. X
SUSE | SLES-우선 순위 | 12. X
SUSE | SLES BYOS | 12. X
SUSE | SLES-SAPCAL | 12. X
SUSE | SLES-표준 | 12. X
Canonical | UbuntuServer | 14.04.0-LTS
Canonical | UbuntuServer | 14.04.1-LTS
Canonical | UbuntuServer | 14.04.2-LTS
Canonical | UbuntuServer | 14.04.3-LTS
Canonical | UbuntuServer | 14.04.4-LTS
Canonical | UbuntuServer | 14.04.5-일-LTS
Canonical | UbuntuServer | 14.04.5-LTS
Canonical | UbuntuServer | 16.04-매일-LTS
Canonical | UbuntuServer | 16.04-LTS
Canonical | UbuntuServer | 16.04.0-LTS
Canonical | UbuntuServer | 18.04-매일-LTS
Canonical | UbuntuServer | 18.04-LTS
Oracle | Oracle-Linux | 6.8, 6.9, 6.10, 7.3, 7.4, 7.5, 7.6
OpenLogic | CentOS | 6.X, 7.X
OpenLogic | CentOS – LVM | 6.X, 7.X
OpenLogic | CentOS – SRIOV | 6.X, 7.X
cloudera | cloudera-centos | 4.X
