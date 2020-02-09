---
title: MARS 에이전트에 대한 지원 매트릭스
description: 이 문서에서는 MARS (Microsoft Azure Recovery Services) 에이전트를 실행 하는 컴퓨터를 백업할 때 지원 Azure Backup를 요약 합니다.
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: a87d778bff5a52f4251d83e401028c9949713e33
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988076"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>MARS(Microsoft Azure Recovery Services) 에이전트를 통한 백업 매트릭스 지원

[Azure Backup 서비스](backup-overview.md) 를 사용 하 여 온-프레미스 컴퓨터와 앱을 백업 하 고 Azure vm (가상 머신)을 백업할 수 있습니다. 이 문서에서는 MARS (Microsoft Azure Recovery Services) 에이전트를 사용 하 여 컴퓨터를 백업 하는 경우 지원 설정 및 제한 사항을 요약 합니다.

## <a name="the-mars-agent"></a>MARS 에이전트

Azure Backup MARS 에이전트를 사용 하 여 온-프레미스 컴퓨터 및 Azure Vm에서 Azure의 백업 Recovery Services 자격 증명 모음으로 데이터를 백업 합니다. MARS 에이전트는 다음을 수행할 수 있습니다.

- 온-프레미스 Windows 컴퓨터에서 실행 하 여 Azure에서 백업 Recovery Services 자격 증명 모음에 직접 백업할 수 있습니다.
- Windows Vm에서를 실행 하 여 자격 증명 모음에 직접 백업할 수 있습니다.
- MABS (Microsoft Azure Backup 서버) 또는 System Center Data Protection Manager (DPM) 서버에서 실행 합니다. 이 시나리오에서 컴퓨터 및 작업은 MABS 또는 DPM 서버에 백업 됩니다. 그러면 MARS 에이전트는이 서버를 Azure의 자격 증명 모음에 백업 합니다.

> [!NOTE]
>Azure Backup는 DST (일광 절약 시간제)의 클록 자동 조정을 지원 하지 않습니다. 실제 시간과 예약 된 백업 시간 간의 불일치를 방지 하기 위해 일광 절약 액을 고려 하도록 정책을 수정 합니다.

백업 옵션은 에이전트가 설치 된 위치에 따라 달라 집니다. 자세한 내용은 [MARS 에이전트를 사용 하는 Azure Backup 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)를 참조 하세요. MABS 및 DPM 백업 아키텍처에 대한 자세한 내용은 [dpm 또는 MABS에 백업](backup-architecture.md#architecture-back-up-to-dpmmabs)을 참조 하세요. 백업 아키텍처에 대한 [요구](backup-support-matrix-mabs-dpm.md) 사항도 참조 하세요.

**설치** | **세부 정보**
--- | ---
최신 MARS 에이전트 다운로드 | 자격 증명 모음에서 최신 버전의 에이전트를 다운로드하거나 [직접 다운로드](https://aka.ms/azurebackup_agent)할 수 있습니다.
컴퓨터에 직접 설치 | 온-프레미스 Windows server 또는 [지원 되는 운영 체제](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems)중 하나를 실행 하는 windows VM에 MARS 에이전트를 직접 설치할 수 있습니다.
백업 서버에 설치 | Azure에 백업하도록 DPM 또는 MABS를 설정할 때 MARS 에이전트를 서버에 다운로드하여 설치합니다. Backup server 지원 매트릭스의 [지원 되는 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) 에 에이전트를 설치할 수 있습니다.

> [!NOTE]
> 기본적으로 백업을 사용 하도록 설정 된 Azure Vm에는 Azure Backup 확장 설치가 있습니다. 이 확장은 전체 VM을 백업합니다. 전체 VM이 아닌 특정 폴더와 파일을 백업하려면 MARS 에이전트를 확장과 함께 Azure VM에 설치하고 실행할 수 있습니다.
> Azure VM에서 MARS 에이전트를 실행 하는 경우 VM의 임시 저장소에 있는 파일 또는 폴더를 백업 합니다. 파일이 나 폴더가 임시 저장소에서 제거 되거나 임시 저장소가 제거 되 면 백업이 실패 합니다.

## <a name="cache-folder-support"></a>캐시 폴더 지원

MARS 에이전트를 사용 하 여 데이터를 백업 하는 경우 에이전트는 데이터의 스냅숏을 만들고 데이터를 Azure에 전송 하기 전에 로컬 캐시 폴더에 저장 합니다. 캐시 (스크래치) 폴더에는 다음과 같은 몇 가지 요구 사항이 있습니다.

**Cache** | **세부 정보**
--- | ---
크기 |  캐시 폴더의 사용 가능한 공간은 전체 백업 데이터 크기의 5 ~ 10% 이상 이어야 합니다.
위치 | 캐시 폴더는 백업 중인 컴퓨터에 로컬로 저장 되어 있어야 하며 온라인 상태 여야 합니다. 캐시 폴더는 네트워크 공유, 이동식 미디어 또는 오프 라인 볼륨에 있지 않아야 합니다.
폴더 | 중복 제거 된 볼륨이 나 압축 된 폴더 (스파스 또는 재분석 지점 포함)에서 캐시 폴더를 암호화 해서는 안 됩니다.
위치 변경 | 백업 엔진 (`net stop bengine`)을 중지 하 고 캐시 폴더를 새 드라이브에 복사 하 여 캐시 위치를 변경할 수 있습니다. 새 드라이브에 충분 한 공간이 있는지 확인 합니다. 그런 다음 **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** 및 **config/cloudbackupprovider/ScratchLocation**)에서 두 레지스트리 항목을 새 위치로 업데이트 하 고 엔진을 다시 시작 합니다.

## <a name="networking-and-access-support"></a>네트워킹 및 액세스 지원

### <a name="url-and-ip-access"></a>URL 및 IP 액세스

MARS 에이전트에서 액세스해야 하는 URL은 다음과 같습니다.

- <http://www.msftncsi.com/ncsi.txt>
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

그리고 다음 IP 주소에 대해 다음을 수행 합니다.

- 20.190.128.0/18
- 40.126.0.0/18

위에 나열 된 모든 Url 및 IP 주소에 대한 액세스는 포트 443에서 HTTPS 프로토콜을 사용 합니다.

### <a name="throttling-support"></a>제한 지원

**기능** | **세부 정보**
--- | ---
대역폭 제어 | 지원됩니다. MARS 에이전트에서 **속성 변경** 을 사용 하 여 대역폭을 조정 합니다.
네트워크 제한 | Windows Server 2008 R2, Windows Server 2008 SP2 또는 Windows 7을 실행 하는 백업 된 컴퓨터에는 사용할 수 없습니다.

## <a name="support-for-direct-backups"></a>직접 백업 지원

>[!NOTE]
> MARS 에이전트는 Windows Server Core Sku를 지원 하지 않습니다.

MARS 에이전트를 사용 하 여에서 실행 되는 아래 나열 된 운영 체제에서 Azure로 직접 백업할 수 있습니다.

1. 온-프레미스 Windows Server
2. Windows가 실행되는 Azure VM

운영 체제는 64 비트 여야 하며, 최신 서비스 팩 및 업데이트를 실행 해야 합니다. 다음 표에는 이러한 운영 체제가 요약 되어 있습니다.

**운영 체제** | **파일/폴더** | **시스템 상태** | **소프트웨어/모듈 요구 사항**
--- | --- | --- | ---
Windows 10(Enterprise, Pro, Home) | 예 | 아닙니다. |  소프트웨어/모듈 요구 사항에 해당 하는 서버 버전 확인
Windows 8.1(Enterprise, Pro)| 예 |아닙니다. | 소프트웨어/모듈 요구 사항에 해당 하는 서버 버전 확인
Windows 8(Enterprise, Pro) | 예 | 아닙니다. | 소프트웨어/모듈 요구 사항에 해당 하는 서버 버전 확인
Windows 7(Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | 예 | 아닙니다. | 소프트웨어/모듈 요구 사항에 해당 하는 서버 버전 확인
Windows Server 2016(Standard, Datacenter, Essentials) | 예 | 예 | -.NET 4.5 <br> -Windows PowerShell <br> -최신 호환 Microsoft VC + + 재배포 가능 패키지 <br> -MMC (Microsoft Management Console) 3.0
Windows Server 2012 R2(Standard, Datacenter, Foundation, Essentials) | 예 | 예 | -.NET 4.5 <br> -Windows PowerShell <br> -최신 호환 Microsoft VC + + 재배포 가능 패키지 <br> -MMC (Microsoft Management Console) 3.0
Windows Server 2012(Standard, Datacenter, Foundation) | 예 | 예 |-.NET 4.5 <br> -Windows PowerShell <br> -최신 호환 Microsoft VC + + 재배포 가능 패키지 <br> -MMC (Microsoft Management Console) 3.0 <br> -배포 이미지 서비스 및 관리 (DISM.EXE)
Windows Storage Server 2016/2012 R2/2012 (표준, 작업 그룹) | 예 | 아닙니다. | -.NET 4.5 <br> -Windows PowerShell <br> -최신 호환 Microsoft VC + + 재배포 가능 패키지 <br> -MMC (Microsoft Management Console) 3.0
Windows Server 2019(Standard, Datacenter, Essentials) | 예 | 예 | -.NET 4.5 <br> -Windows PowerShell <br> -최신 호환 Microsoft VC + + 재배포 가능 패키지 <br> -MMC (Microsoft Management Console) 3.0

자세한 내용은 [지원 되는 MABS 및 DPM 운영 체제](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)를 참조 하세요.

## <a name="backup-limits"></a>Backup 제한

### <a name="size-limits"></a>크기 한도

Azure Backup 백업할 수 있는 파일 또는 폴더 데이터 원본의 크기를 제한 합니다. 단일 볼륨에서 백업 하는 항목은이 표에 요약 된 크기를 초과할 수 없습니다.

**운영 체제** | **크기 제한**
--- | ---
Windows Server 2012 이상 |54,400GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 이상| 54,400GB
Windows 7| 1700 GB

### <a name="other-limitations"></a>기타 제한 사항

- MARS는 단일 자격 증명 모음에 대해 동일한 이름의 여러 컴퓨터에 대한 보호를 지원 하지 않습니다.

## <a name="supported-file-types-for-backup"></a>백업에 지원되는 파일 형식

**형식** | **지원**
--- | ---
암호화됨| 지원됩니다.
Compressed | 지원됩니다.
스파스 | 지원됩니다.
압축 및 스파스 |지원됩니다.
하드 링크| 지원되지 않습니다. 건너뜁니다.
재분석 지점| 지원되지 않습니다. 건너뜁니다.
암호화 및 스파스 |지원되지 않습니다. 건너뜁니다.
압축된 스트림| 지원되지 않습니다. 건너뜁니다.
스파스 스트림| 지원되지 않습니다. 건너뜁니다.
OneDrive (동기화 된 파일이 스파스 스트림)| 지원되지 않습니다.

## <a name="supported-drives-or-volumes-for-backup"></a>백업에 지원 되는 드라이브 또는 볼륨

**드라이브/볼륨** | **지원** | **세부 정보**
--- | --- | ---
읽기 전용 볼륨| 지원하지 않음 | VSS (볼륨 복사 섀도 서비스)는 볼륨에 쓸 수 있는 경우에만 작동 합니다.
오프라인 볼륨| 지원하지 않음 |VSS는 볼륨이 온라인 상태인 경우에만 작동 합니다.
네트워크 공유| 지원하지 않음 |볼륨은 서버에서 로컬 이어야 합니다.
BitLocker 잠김 볼륨| 지원하지 않음 |백업을 시작 하기 전에 볼륨을 잠금 해제 해야 합니다.
파일 시스템 id| 지원하지 않음 |NTFS만 지원 됩니다.
이동식 미디어| 지원하지 않음 |모든 백업 항목 원본의 상태는 *고정* 이어야 합니다.
중복 제거된 드라이브 | 지원됨 | Azure Backup에서 중복 제거된 데이터를 일반 데이터로 변환합니다. 이는 데이터를 최적화, 암호화, 저장 및 자격 증명 모음으로 전송 합니다.

## <a name="support-for-initial-offline-backup"></a>오프라인 초기 백업 지원

Azure Backup는 디스크를 사용 하 여 초기 백업 데이터를 Azure로 전송 하는 *오프 라인 시드에* 지원 합니다. 이 지원은 초기 백업이 tb (테라바이트) 크기 범위에 있을 가능성이 있는 경우에 유용 합니다. 오프라인 백업이 지원되는 대상은 다음과 같습니다.

- MARS 에이전트를 실행 하는 온-프레미스 컴퓨터의 파일 및 폴더에 대한 직접 백업
- DPM 서버 또는 MABS에서 파일 및 워크로드 백업

시스템 상태 파일에는 오프 라인 백업을 사용할 수 없습니다.

## <a name="support-for-data-restoration"></a>데이터 복원 지원

Azure Backup의 [즉시 복원](backup-instant-restore-capability.md) 기능을 사용 하 여 데이터를 자격 증명 모음에 복사 하기 전에 복원할 수 있습니다. 백업 중인 컴퓨터 .NET Framework 4.5.2 이상에서 실행 중 이어야 합니다.

이전 버전의 운영 체제를 실행 하는 대상 컴퓨터에 백업을 복원할 수 없습니다. 예를 들어 Windows 7을 실행 하는 컴퓨터에서 가져온 백업은 Windows 8 이상에서 복원할 수 있습니다. 그러나 windows 8을 실행 하는 컴퓨터에서 가져온 백업은 Windows 7을 실행 하는 컴퓨터에서 복원할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- [MARS 에이전트를 사용 하는 백업 아키텍처](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)에 대해 자세히 알아보세요.
- [MABS 또는 DPM 서버에서 MARS 에이전트를 실행할](backup-support-matrix-mabs-dpm.md)때 지원 되는 기능을 알아봅니다.
