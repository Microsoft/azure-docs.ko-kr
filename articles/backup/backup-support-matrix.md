---
title: Azure Backup 지원 매트릭스
description: Azure Backup 서비스에 대한 지원 설정 및 제한 사항에 대한 요약을 제공합니다.
ms.topic: conceptual
ms.date: 02/17/2019
ms.openlocfilehash: 37347e6febdfc3500c218238606fc96463da631c
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936243"
---
# <a name="support-matrix-for-azure-backup"></a>Azure Backup에 대한 지원 매트릭스

[Azure Backup](backup-overview.md) 를 사용 하 여 Microsoft Azure 클라우드 플랫폼으로 데이터를 백업할 수 있습니다. 이 문서에서는 Azure Backup 시나리오 및 배포에 대한 일반적인 지원 설정 및 제한 사항을 요약 합니다.

기타 지원 매트릭스를 사용할 수 있습니다.

- [AZURE VM (가상 머신) 백업](backup-support-matrix-iaas.md) 에 대한 지원 매트릭스
- [System Center Data Protection Manager (DPM)/Microsoft Azure Backup Server (MABS)](backup-support-matrix-mabs-dpm.md) 를 사용 하 여 백업을 위한 지원 매트릭스
- [MARS (Microsoft Azure Recovery Services) 에이전트](backup-support-matrix-mars-agent.md) 를 사용 하 여 백업에 대한 지원 매트릭스

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="vault-support"></a>자격 증명 모음 지원

Azure Backup는 Recovery Services 자격 증명 모음을 사용 하 여 백업을 오케스트레이션 하 고 관리 합니다. 또한 자격 증명 모음을 사용 하 여 백업 데이터를 저장 합니다.

다음 표에서는 Recovery Services 자격 증명 모음의 기능을 설명 합니다.

**기능** | **세부 정보**
--- | ---
**구독의 자격 증명 모음 수** | 단일 구독에서 최대 500개의 Recovery Services 자격 증명 모음입니다.
**자격 증명 모음의 머신 수** | 단일 자격 증명 모음에서 최대 1000 Azure Vm.<br/><br/> 최대 50대의 MABS 서버를 단일 자격 증명 모음에 등록할 수 있습니다.
**자격 증명 모음 스토리지의 데이터 원본 수** | 최대 54400 GB Azure VM 백업에는 제한이 없습니다.
**자격 증명 모음에 대한 백업 횟수** | **Azure vm:** 하루에 한 번<br/><br/>**DPM/MABS로 보호 되는 컴퓨터:** 하루에 두 번<br/><br/> **MARS 에이전트를 사용 하 여 직접 백업 하는 컴퓨터:** 하루에 3 번
**자격 증명 모음 간 백업** | 백업은 한 지역 내에서 수행됩니다.<br/><br/> 백업할 VM이 포함된 각 Azure 지역에 자격 증명 모음이 있어야 합니다. 다른 지역으로 백업할 수 없습니다.
**자격 증명 모음 이동** | [자격 증명](https://review.docs.microsoft.com/azure/backup/backup-azure-move-recovery-services-vault) 모음은 구독 간에 이동 하거나 동일한 구독의 리소스 그룹 간에 이동할 수 있습니다. 그러나 하위 자격 증명 모음을 지역 간에 이동 하는 것은 지원 되지 않습니다.
**자격 증명 모음 간 데이터 이동** | 자격 증명 모음 간에 백업 데이터를 이동 하는 것은 지원 되지 않습니다.
**자격 증명 모음 스토리지 유형 수정** | 백업을 저장 하기 전에 자격 증명 모음에 대한 저장소 복제 유형 (지역 중복 저장소 또는 로컬 중복 저장소)을 수정할 수 있습니다. 자격 증명 모음에서 백업이 시작되면 복제 유형을 수정할 수 없습니다.

## <a name="on-premises-backup-support"></a>온-프레미스 백업 지원

온-프레미스 컴퓨터를 백업 하려는 경우 다음 같습니다 지원 됩니다.

**머신** | **백업 대상** | **위치** | **기능**
--- | --- | --- | ---
**MARS 에이전트를 사용한 Windows 머신 직접 백업** | 파일, 폴더, 시스템 상태 | Recovery Services 자격 증명 모음에 백업 합니다. | 하루에 세 번 백업<br/><br/> 앱 인식 백업 없음<br/><br/> 파일, 폴더, 볼륨 복원
**MARS 에이전트를 사용한 Linux 머신 직접 백업** | 백업이 지원 되지 않음
**DPM에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | 로컬 DPM 스토리지에 백업합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Vm에 대해 지원 되는 Linux (Hyper-v/VMware)<br/><br/> Oracle은 지원 되지 않습니다.
**MABS에 백업** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS 로컬 스토리지에 백업합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅샷<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Vm에 대해 지원 되는 Linux (Hyper-v/VMware)<br/><br/> Oracle은 지원 되지 않습니다.

## <a name="azure-vm-backup-support"></a>Azure VM 백업 지원

### <a name="azure-vm-limits"></a>Azure VM 제한

**제한** | **세부 정보**
--- | ---
**Azure VM 데이터 디스크** | 16 개로 제한 <br> 16개 이상의 디스크(최대 32개 디스크)를 사용하여 VM의 프라이빗 미리 보기에 등록하려면 AskAzureBackupTeam@microsoft.com으로 이메일을 보내주세요.
**Azure VM 데이터 디스크 크기** | 개별 디스크 크기는 최대 32 TB 이며 VM의 모든 디스크에 대해 최대 256 TB를 결합할 수 있습니다.

### <a name="azure-vm-backup-options"></a>Azure VM 백업 옵션

Azure Vm을 백업 하려는 경우 다음 같습니다 지원 됩니다.

**머신** | **백업 대상** | **위치** | **기능**
--- | --- | --- | ---
**VM 확장을 사용 하 여 Azure VM 백업** | 전체 VM | 자격 증명 모음에 백업 합니다. | VM에 백업을 사용하도록 설정할 때 설치된 확장<br/><br/> 하루에 한 번 백업 합니다.<br/><br/> Windows Vm에 대한 앱 인식 백업 Linux Vm에 대한 파일 일치 백업 사용자 지정 스크립트를 사용 하 여 Linux 컴퓨터에 대한 앱 일관성을 구성할 수 있습니다.<br/><br/> VM 또는 디스크를 복원 합니다.<br/><br/> Azure VM을 온-프레미스 위치에 백업할 수 없습니다.
**MARS 에이전트를 사용 하 여 Azure VM 백업** | 파일, 폴더, 시스템 상태 | 자격 증명 모음에 백업 합니다. | 하루에 세 번 백업 합니다.<br/><br/> 전체 VM이 아닌 특정 파일 또는 폴더를 백업 하려는 경우 MARS 에이전트는 VM 확장과 함께 실행할 수 있습니다.
**Azure VM(DPM 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | DPM을 실행 하는 Azure VM의 로컬 저장소에 백업 합니다. 그런 다음, DPM에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏.<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음
**Azure VM(MABS 사용)** | 파일, 폴더, 볼륨, 시스템 상태, 앱 데이터 | MABS를 실행 하는 Azure VM의 로컬 저장소에 백업 합니다. 그런 다음, MABS에서 자격 증명 모음에 백업합니다. | 앱 인식 스냅숏.<br/><br/> 백업 및 복구에 대한 전체 세분성<br/><br/> Linux는 VM(Hyper-V/VMware)에 지원됨<br/><br/> Oracle은 지원되지 않음

## <a name="linux-backup-support"></a>Linux 백업 지원

Linux 컴퓨터를 백업 하려는 경우 다음 같습니다 지원 됩니다.

**백업 유형** | **Linux(Azure 인증)**
--- | ---
**Linux를 실행 하는 온-프레미스 컴퓨터의 직접 백업** | 지원되지 않습니다. MARS 에이전트는 Windows 컴퓨터에만 설치할 수 있습니다.
**에이전트 확장을 사용 하 여 Linux를 실행 하는 Azure VM 백업** | [사용자 지정 스크립트](backup-azure-linux-app-consistent.md)를 사용 하 여 앱 일치 백업<br/><br/> 파일 수준 복구입니다.<br/><br/> 복구 지점 또는 디스크에서 VM을 만들어 복원합니다.
**DPM을 사용 하 여 Linux를 실행 하는 온-프레미스 또는 Azure VM 백업** | Hyper-v 및 VMWare에서 Linux 게스트 Vm의 파일 일치 백업<br/><br/> Hyper-v 및 VMWare Linux 게스트 Vm의 VM 복원<br/><br/> Azure VM에는 파일 일치 백업을 사용할 수 없습니다.
**MABS를 사용 하 여 Linux를 실행 하는 온-프레미스 컴퓨터 또는 Azure VM 백업** | Hyper-v 및 VMWare에서 Linux 게스트 Vm의 파일 일치 백업<br/><br/> Hyper-v 및 VMWare Linux 게스트 Vm의 VM 복원<br/><br/> Azure VM에서 파일 일치 백업을 사용할 수 없습니다.

## <a name="daylight-saving-time-support"></a>일광 절약 시간 지원

Azure Backup는 Azure VM 백업에 대한 일광 절약 시간에 대한 자동 클록 조정을 지원 하지 않습니다. 필요에 따라 수동으로 백업 정책을 수정합니다.

## <a name="disk-deduplication-support"></a>디스크 중복 제거 지원

디스크 중복 제거 지원은 다음과 같습니다.

- 디스크 중복 제거는 DPM 또는 MABs를 사용 하 여 Windows를 실행 하는 Hyper-v Vm을 백업 하는 경우 온-프레미스에서 지원 됩니다. Windows Server는 VM에 백업 저장소로 연결 된 Vhd (가상 하드 디스크)에서 데이터 중복 제거 (호스트 수준)를 수행 합니다.
- Azure에서는 백업 구성 요소에 대한 중복 제거가 지원되지 않습니다. DPM 및 MABS를 Azure에 배포 하는 경우 VM에 연결 된 저장소 디스크를 중복 제거할 수 없습니다.

## <a name="security-and-encryption-support"></a>보안 및 암호화 지원

Azure Backup은 전송 중 및 미사용 데이터에 대한 암호화를 지원 합니다.

### <a name="network-traffic-to-azure"></a>Azure에 대한 네트워크 트래픽

- AES(Advanced Encryption Standard) 256를 사용 하 여 서버에서 Recovery Services 자격 증명 모음으로의 백업 트래픽을 암호화 합니다.
- 백업 데이터는 보안 HTTPS 링크를 통해 전송됩니다.
- 백업 데이터는 Recovery Services 자격 증명 모음에 암호화 된 형식으로 저장 됩니다.
- 사용자만 이 데이터의 잠금을 해제할 수 있는 암호를 보유합니다. Microsoft는 어떠한 경우에도 백업 데이터의 암호를 해독할 수 없습니다.

    > [!WARNING]
    > 자격 증명 모음이 설정되면 암호화 키에 대한 액세스 권한만 부여됩니다. Microsoft는 복사본을 유지 관리할 수 없으며, 키에 대한 액세스 권한도 없습니다. 키를 잃어버리면 Microsoft에서 백업 데이터를 복구할 수 없습니다.

### <a name="data-security"></a>데이터 보안

- Azure Vm을 백업 하는 경우 가상 머신 *내에서* 암호화를 설정 해야 합니다.
- Azure Backup은 Azure Disk Encryption을 지원하며, Windows 가상 머신의 BitLocker와 Linux 가상 머신의 **dm-crypt**를 사용합니다.
- 백 엔드에서 Azure Backup는 미사용 데이터를 보호 하는 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 사용 합니다.

**머신** | **전송 중** | **저장**
--- | --- | ---
**DPM/MABS를 사용 하지 않는 온-프레미스 Windows 컴퓨터** | ![예][green] | ![예][green]
**Azure VM** | ![예][green] | ![예][green]
**DPM을 사용 하는 온-프레미스 Windows 컴퓨터 또는 Azure Vm** | ![예][green] | ![예][green]
**MABS를 사용 하는 온-프레미스 Windows 컴퓨터 또는 Azure Vm** | ![예][green] | ![예][green]

## <a name="compression-support"></a>압축 지원

Backup은 다음 표에 요약 된 것 처럼 백업 트래픽의 압축을 지원 합니다.

- Azure Vm의 경우 VM 확장은 저장소 네트워크를 통해 Azure storage 계정에서 직접 데이터를 읽으므로이 트래픽을 압축할 필요가 없습니다.
- DPM 또는 MABS를 사용 하는 경우 데이터를 백업 하기 전에 압축 하 여 대역폭을 절약할 수 있습니다.

**머신** | **MABS/DPM에 압축(TCP)** | **자격 증명 모음으로 압축 (HTTPS)**
--- | --- | ---
**온-프레미스 Windows 머신 직접 백업** | 해당 없음 | ![예][green]
**VM 확장을 사용 하 여 Azure Vm 백업** | 해당 없음 | 해당 없음
**MABS/DPM을 사용 하 여 온-프레미스/Azure 컴퓨터에서 백업** | ![예][green] | ![예][green]

## <a name="retention-limits"></a>보존 제한

**설정** | **제한**
--- | ---
**보호 된 인스턴스당 최대 복구 위치 (컴퓨터 또는 작업)** | 9999
**복구 지점에 대한 최대 만료 시간** | 무제한
**DPM/MABS에 대한 최대 백업 빈도** | SQL Server에 대해 15분마다<br/><br/> 다른 작업에 대해 한 시간에 한 번
**자격 증명 모음에 대한 최대 백업 빈도** | **MARS를 실행 하는 온-프레미스 Windows 컴퓨터 또는 Azure vm:** 하루 3 개<br/><br/> **DPM/MABS:** 하루에 2 개<br/><br/> **AZURE VM 백업:** 하루에 하나씩
**복구 지점 보존** | 매일, 매주, 매월, 매년
**최대 보존 기간** | 백업 빈도에 따라 다름
**DPM/MABS 디스크의 복구 위치** | 파일 서버의 경우 64 448 앱 서버 <br/><br/>온-프레미스 DPM의 무제한 테이프 복구 시점

## <a name="cross-region-restore"></a>지역 간 복원

Azure Backup는 데이터 가용성 및 복원 력 기능을 강화 하는 지역 간 복원 기능을 추가 하 여 고객에 게 보조 지역으로 데이터를 복원할 수 있는 모든 권한을 제공 합니다. 이 기능을 구성 하려면 [지역 간 복원 설정 문서](backup-create-rs-vault.md#set-cross-region-restore)를 참조 하세요. 이 기능은 다음과 같은 관리 형식에 대해 지원 됩니다.

| 백업 관리 유형 | 지원됨                                                    | 지원되는 지역 |
| ---------------------- | ------------------------------------------------------------ | ----------------- |
| Azure VM               | 예. 1TB 미만의 디스크를 사용 하 여 암호화 된 Vm 및 Vm에 대해 지원 되는 공개 제한 된 미리 보기 | 미국 중서부   |
| MARS 에이전트/온-프레미스 | 아닙니다.                                                           | N/A               |
| SQL/SAP HANA          | 아닙니다.                                                           | N/A               |
| AFS                    | 아닙니다.                                                           | N/A               |



## <a name="next-steps"></a>다음 단계

- Azure VM 백업에 대한 [지원 매트릭스를 검토](backup-support-matrix-iaas.md)합니다.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
