---
title: MABS (Azure Backup Server) V3 UR1 protection 매트릭스
description: 이 문서에서는 Azure Backup Server에서 보호하는 모든 워크로드, 데이터 형식 및 설치 프로그램을 나열하는 지원 매트릭스를 제공합니다.
ms.date: 03/19/2020
ms.topic: conceptual
ms.openlocfilehash: 70d8b8cd26a40b0c7ec8b538bcb702d281f829e6
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826840"
---
# <a name="mabs-azure-backup-server-v3-ur1-protection-matrix"></a>MABS (Azure Backup Server) V3 UR1 protection 매트릭스

이 문서에는 다양한 서버 및 Azure Backup Server로 보호할 수 있는 워크로드가 나와 있습니다. 다음 매트릭스는 Azure Backup Server를 사용하여 보호할 수 있는 항목을 나열합니다.

MABS v3 UR1에 대해 다음 행렬을 사용 합니다.

* 워크로드 - 기술의 워크로드 유형.

* 버전 – 워크 로드에 대해 지원 되는 MABS 버전입니다.

* MABS 설치 – MABS를 설치 하려는 컴퓨터/위치입니다.

* 보호 및 복구 - 지원되는 배포 또는 지원되는 스토리지 컨테이너 같은 워크로드에 대한 세부 정보를 나열합니다.

>[!NOTE]
>32 비트 보호 에이전트에 대 한 지원은 MABS v3 UR1에서 더 이상 사용 되지 않습니다. [32 비트 보호 에이전트 사용](backup-mabs-whats-new-mabs.md#32-bit-protection-agent-deprecation)중단을 참조 하세요.

## <a name="protection-support-matrix"></a>보호 지원 매트릭스

다음 섹션에서는 MABS에 대 한 보호 지원 매트릭스에 대해 자세히 설명 합니다.

* [애플리케이션 백업](#applications-backup)
* [VM 백업](#vm-backup)
* [Linux](#linux)

## <a name="applications-backup"></a>애플리케이션 백업

| **작업**               | **Version**                                                  | **Azure Backup Server 설치**                       | **지원 되는 Azure Backup Server** | **보호 및 복구**                                  |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| 클라이언트 컴퓨터 (64 비트) | Windows 10                                                  | 물리적 서버  <br><br>    Hyper-V 가상 머신   <br><br>   VMware 가상 머신 | V3 UR1                            | 볼륨, 공유, 폴더, 파일, 중복 제거 된 볼륨   <br><br>   보호된 볼륨은 NTFS여야 합니다. FAT 및 FAT32는 지원되지 않습니다.  <br><br>    볼륨은 1GB 이상이어야 합니다. Azure Backup Server는 VSS (볼륨 섀도 복사본 서비스)를 사용 하 여 데이터 스냅숏을 생성 하며, 스냅숏은 볼륨이 1gb 이상인 경우에만 작동 합니다. |
| Servers (64 비트)          | Windows Server 2019, 2016, 2012 R2, 2012                    | Azure 가상 머신 (워크 로드가 Azure 가상 머신으로 실행 되는 경우)  <br><br>    물리적 서버  <br><br>    Hyper-V 가상 머신 <br><br>     VMware 가상 머신  <br><br>    Azure Stack | V3 UR1                            | 볼륨, 공유, 폴더, 파일 <br><br>    중복 제거 된 볼륨 (NTFS 전용)  <br><br>   시스템 상태 및 운영 체제 미 설치 (워크 로드가 Azure 가상 머신으로 실행 되는 경우 지원 되지 않음) |
| Servers (64 비트)          | Windows Server 2008 R2 SP1, Windows Server 2008 SP2 ( [Windows Management Framework](https://www.microsoft.com/download/details.aspx?id=54616)를 설치 해야 하는 경우) | 물리적 서버  <br><br>    Hyper-V 가상 머신  <br><br>      VMware 가상 컴퓨터  <br><br>   Azure Stack | V3 UR1                            | 볼륨, 공유, 폴더, 파일, 시스템 상태/완전 복구        |
| SQL Server                | SQL Server 2019, 2017, 2016, [지원 되는 sps](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202016), 2014 및 지원 되는 [sps](https://support.microsoft.com/lifecycle/search?alpha=SQL%20Server%202014) | 물리적 서버  <br><br>     Hyper-V 가상 머신   <br><br>     VMware 가상 컴퓨터  <br><br>   Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)  <br><br>     Azure Stack | V3 UR1                            | 모든 배포 시나리오: 데이터베이스       <br><br>  MABS v3 UR1는 ReFS 볼륨을 통해 SQL 데이터베이스의 백업을 지원 합니다.                  |
| Exchange                   | Exchange 2019, 2016                                         | 물리적 서버   <br><br>   Hyper-V 가상 머신  <br><br>      VMware 가상 컴퓨터  <br><br>   Azure Stack  <br><br>    Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우) | V3 UR1                            | 보호 (모든 배포 시나리오): 독립 실행형 Exchange server, DAG (데이터베이스 가용성 그룹)의 데이터베이스  <br><br>    복구(모든 배포 시나리오): 사서함, DAG의 사서함 데이터베이스    <br><br>  참조를 통한 Exchange 백업은 MABS v3 UR1에서 지원 됩니다. |
| SharePoint                 | SharePoint 2019, 2016 (최신 Sp 포함)                       | 물리적 서버  <br><br>    Hyper-V 가상 머신 <br><br>    VMware 가상 컴퓨터  <br><br>   Azure 가상 머신(워크로드가 Azure 가상 머신으로 실행 중인 경우)   <br><br>   Azure Stack | V3 UR1                            | 보호 (모든 배포 시나리오): 팜, 프런트 엔드 웹 서버 콘텐츠  <br><br>    복구 (모든 배포 시나리오): 팜, 데이터베이스, 웹 응용 프로그램, 파일 또는 목록 항목, SharePoint 검색, 프런트 엔드 웹 서버  <br><br>    콘텐츠 데이터베이스에 대해 SQL Server 2012 AlwaysOn 기능을 사용 하는 SharePoint 팜을 보호 하는 것은 지원 되지 않습니다. |

## <a name="vm-backup"></a>VM 백업

| **작업**                                                 | **Version**                                             | **Azure Backup Server 설치**                      | **지원 되는 Azure Backup Server** | **보호 및 복구**                                 |
| ------------------------------------------------------------ | ------------------------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Hyper-v 호스트-MABS 보호 에이전트 (hyper-v 호스트 서버, 클러스터 또는 VM) | Windows Server 2019, 2016, 2012 R2, 2012               | 물리적 서버  <br><br>    Hyper-V 가상 머신 <br><br>    VMware 가상 컴퓨터 | V3 UR1                             | 보호: Hyper-v 컴퓨터, Csv (클러스터 공유 볼륨)  <br><br>    복구: 가상 머신, Windows, 볼륨, 가상 하드 드라이브에 대해서만 사용할 수 있는 파일 및 폴더의 항목 수준 복구 |
| VMware Vm                                                  | VMware server 5.5, 6.0 또는 6.5, 6.7 (사용이 허가 된 버전) | Hyper-v 가상 컴퓨터  <br><br>   VMware 가상 컴퓨터         | V3 UR1                             | 보호: Csv (클러스터 공유 볼륨), NFS 및 SAN 저장소의 VMware Vm   <br><br>     복구: 가상 머신, Windows, 볼륨, 가상 하드 드라이브에 대해서만 사용할 수 있는 파일 및 폴더의 항목 수준 복구 <br><br>    VMware vApps는 지원 되지 않습니다. |

## <a name="linux"></a>Linux

| **작업** | **Version**                               | **Azure Backup Server 설치**                      | **지원 되는 Azure Backup Server** | **보호 및 복구**                                 |
| ------------ | ----------------------------------------- | ------------------------------------------------------------ | ---------------------------------- | ------------------------------------------------------------ |
| Linux        | Hyper-v 또는 VMware 게스트로 실행 되는 Linux | 실제 서버, 온-프레미스 Hyper-v VM, VMWare의 Windows VM | V3 UR1                             | Hyper-v는 Windows Server 2012 R2, Windows Server 2016 또는 Windows Server 2019에서 실행 되어야 합니다. 보호: 전체 가상 머신   <br><br>   복구: 전체 가상 머신   <br><br>    파일에 일관적인 스냅샷만 지원됩니다.    <br><br>   지원 되는 Linux 배포 및 버전의 전체 목록은 [Azure에서 보증 배포의 Linux](../virtual-machines/linux/endorsed-distros.md)문서를 참조 하세요. |

## <a name="azure-expressroute-support"></a>Azure Express 경로 지원

공용 피어 링 (이전 회로에 사용 가능) 및 Microsoft 피어 링을 사용 하 여 Azure Express 경로를 통해 데이터를 백업할 수 있습니다. 개인 피어 링에 대 한 백업은 지원 되지 않습니다.

공용 피어 링 사용: 다음 도메인/주소에 대 한 액세스를 확인 합니다.

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Microsoft 피어 링을 사용 하 여 다음 서비스/지역 및 관련 커뮤니티 값을 선택 합니다.

* Azure Active Directory(12076:5060)
* Microsoft Azure 지역 (Recovery Services 자격 증명 모음의 위치에 따라)
* Azure Storage (Recovery Services 자격 증명 모음의 위치에 따라)

자세한 내용은 [express 경로 라우팅 요구 사항](../expressroute/expressroute-routing.md)을 참조 하세요.

>[!NOTE]
>공용 피어 링은 새 회로에서 사용 되지 않습니다.

## <a name="cluster-support"></a>클러스터 지원

Azure Backup Server는 다음과 같은 클러스터 된 애플리케이션에서 데이터를 보호할 수 있습니다.

* 파일 서버

* SQL Server

* Hyper-v-스케일 아웃 MABS 보호 에이전트를 사용 하 여 Hyper-v 클러스터를 보호 하는 경우 보호 된 Hyper-v 작업에 대 한 보조 보호를 추가할 수 없습니다.

* Exchange Server - Azure Backup Server는 지원되는 Exchange Server 버전의 공유되지 않은 디스크 클러스터(클러스터 연속 복제)를 보호할 수 있으며 로컬 연속 복제를 위해 구성된 Exchange Server도 보호할 수 있습니다.

* SQL Server - Azure Backup Server는 CSV(클러스터 공유 볼륨)에서 호스트되는 SQL Server 데이터베이스를 백업할 수 없습니다.

Azure Backup Server는 MABS 서버와 동일한 도메인에 있는 클러스터 작업 및 하위 도메인 이나 트러스트 된 도메인에 있는 클러스터 작업을 보호할 수 있습니다. 트러스트되지 않은 도메인 또는 작업 그룹의 데이터 원본을 보호하려면 단일 서버의 경우 NTLM 또는 인증서 인증을 사용하거나 클러스터의 경우 인증서 인증만 사용합니다.
