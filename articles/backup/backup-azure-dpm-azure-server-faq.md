---
title: Azure Backup Server 및 DPM FAQ
description: 이 문서에서는 MABS (Microsoft Azure Backup 서버) 및 DPM (Data Protection Manager)에 대 한 일반적인 질문에 대 한 답변을 검색 합니다.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/05/2019
ms.openlocfilehash: 00eb064aaf549ee0d83d0cf7d9c9b47c2dc5ef78
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827208"
---
# <a name="azure-backup-server-and-dpm---faq"></a>Azure Backup Server 및 DPM-FAQ

## <a name="general-questions"></a>일반적인 질문

이 문서에서는 Azure Backup Server 및 DPM에 대해 자주 묻는 질문과 대답을 설명 합니다.

### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server"></a>Azure Backup 서버를 사용하여 물리적 서버에 대한 BMR(완전 복구) 백업을 만들 수 있나요?

예.

### <a name="can-i-register-the-server-to-multiple-vaults"></a>여러 자격 증명 모음에 서버를 등록할 수 있나요?

아니요. DPM 또는 Azure Backup 서버는 하나의 자격 증명 모음에만 등록할 수 있습니다.

### <a name="can-i-use-dpm-to-back-up-apps-in-azure-stack"></a>Azure Stack에서 DPM을 사용하여 앱을 백업할 수 있나요?

아니요. Azure Backup을 사용하여 Azure Stack을 보호할 수 있으며 Azure Backup은 DPM을 사용하여 Azure Stack에 있는 앱을 백업하는 기능은 지원하지 않습니다.

### <a name="if-ive-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-install-system-center-dpm-to-back-up-on-premises-workloads-to-azure"></a>내 파일 및 폴더를 보호하기 위해 Azure Backup 에이전트를 설치한 경우 Azure에 온-프레미스 워크로드를 백업하기 위해 System Center DPM을 설치할 수 있나요?

예. 하지만 DPM을 먼저 설치한 후 Azure Backup 에이전트를 설치해야 합니다.  구성 요소를 이 순서대로 설치하면 Azure Backup 에이전트는 DPM과 함께 작동합니다. DPM을 설치하기 전에 Azure Backup 에이전트를 설치하는 것이 권장되지 않으며 지원되지 않습니다.

### <a name="why-cant-i-add-an-external-dpm-server-after-installing-ur7-and-latest-azure-backup-agent"></a>UR7과 최신 Azure Backup 에이전트를 설치한 후에 외부 DPM 서버를 추가할 수 없는 이유는 무엇인가요?

데이터 원본이 클라우드에 보호되어 있는 DPM 서버의 경우(업데이트 롤업 7 이전의 업데이트 롤업을 사용하여), UR7과 최신 Azure Backup 에이전트를 설치한 다음 하루 이상 기다린 후에 **외부 DPM 서버 추가**를 시작해야 합니다. DPM 보호 그룹의 메타데이터가 Azure에 업로드되는 데는 1일의 기간이 필요합니다. 보호 그룹 메타데이터는 야간 작업을 통해 처음으로 업로드됩니다.

### <a name="are-there-recommendations-for-configuring-exclusions-for-antivirus-software"></a>바이러스 백신 소프트웨어에 대 한 제외를 구성 하기 위한 권장 사항이 있나요?

예, 바이러스 백신 제외를 구성 하는 것이 좋습니다. DPM에 대 한 제외는 [dpm 서버에서 바이러스 백신 소프트웨어 실행](https://docs.microsoft.com/system-center/dpm/run-antivirus-server?view=sc-dpm-2019)을 참조 하세요. MABS에 대 한 제외는 [mabs 서버에 대 한 바이러스 백신 구성](backup-azure-mabs-troubleshoot.md#configure-antivirus-for-mabs-server)을 참조 하세요.

## <a name="vmware-and-hyper-v-backup"></a>VMware 및 Hyper-V 백업

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>VMware vCenter 서버를 Azure에 백업할 수 있나요?

예. VMware vCenter Server 및 ESXi 호스트를 Azure에 백업하는 데 Azure Backup Server를 사용할 수 있습니다.

- 지원되는 버전에 대해 [자세히 알아봅니다](backup-mabs-protection-matrix.md).
- [다음 단계를 수행](backup-azure-backup-server-vmware.md)하여 VMware 서버를 백업합니다.

### <a name="do-i-need-a-separate-license-to-recover-a-full-on-premises-vmwarehyper-v-cluster"></a>전체 온-프레미스 VMware/Hyper-v 클러스터를 복구 하려면 별도의 라이선스가 필요 한가요?

VMware/Hyper-V 보호를 위해 별도 라이선스는 필요 없습니다.

- System Center 고객인 경우 System Center DPM(Data Protection Manager)을 사용하여 VMware VM을 보호합니다.
- System Center 고객이 아닌 경우에는 Azure Backup Server(종량제)를 사용하여 VMware VM을 보호할 수 있습니다.

## <a name="sharepoint"></a>SharePoint

### <a name="can-i-recover-a-sharepoint-item-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson-with-protection-on-disk"></a>디스크 보호를 설정하여 SQL AlwaysOn을 사용해 SharePoint를 구성한 경우 SharePoint 항목을 원본 위치로 복구할 수 있나요?

예, 항목을 원본 SharePoint 사이트로 복구할 수 있습니다.

### <a name="can-i-recover-a-sharepoint-database-to-the-original-location-if-sharepoint-is-configured-by-using-sql-alwayson"></a>SQL AlwaysOn을 사용해 SharePoint를 구성한 경우 SharePoint 데이터베이스를 원본 위치로 복구할 수 있나요?

SharePoint 데이터베이스는 SQL AlwaysOn에서 구성 되므로 가용성 그룹을 제거 하지 않으면 수정할 수 없습니다. 따라서 DPM에서 원래 위치로 데이터베이스를 복원할 수 없습니다. SQL Server 데이터베이스를 다른 SQL Server 인스턴스로 복구할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다른 FAQ를 읽어보세요.

- Azure Backup Server 및 DPM 지원 매트릭스에 [대해 자세히 알아보세요](backup-support-matrix-mabs-dpm.md) .
- Azure Backup Server 및 DPM 문제 해결 지침에 [대해 자세히 알아보세요](backup-azure-mabs-troubleshoot.md) .
