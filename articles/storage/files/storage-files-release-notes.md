---
title: Azure 파일 동기화 에이전트에 대한 릴리스 정보 | Microsoft Docs
description: Azure 파일 동기화 에이전트에 대한 릴리스 정보
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: d0e830aaca4f952f75c220b4f482ce831883b058
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905579"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 파일 동기화 에이전트에 대한 릴리스 정보
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Windows Server 설치는 Azure 파일 공유의 빠른 캐시로 변환됩니다. 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다(SMB, NFS 및 FTPS 포함). 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 파일 동기화 에이전트의 지원되는 버전에 대한 릴리스 정보를 제공합니다.

## <a name="supported-versions"></a>지원되는 버전
다음 버전은 Azure 파일 동기화 에이전트에 대해 지원됩니다.

| Milestone | 에이전트 버전 번호 | 릴리스 날짜 | 상태 |
|----|----------------------|--------------|------------------|
| 12 월 2019 업데이트 롤업- [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019년 12월 12일 | 지원됨 |
| V9 릴리스- [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019 년 12 월 2 일 | 지원됨 |
| V8 릴리스- [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019년 10월 8일 | 지원됨 |
| 7 월 2019 업데이트 롤업- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019년 7월 24일 | 지원됨 |
| 7 월 2019 업데이트 롤업- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019 년 7 월 12 일 | 지원됨 |
| V7 릴리스- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019 년 6 월 19 일 | 지원됨 |
| 6 월 2019 업데이트 롤업- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 2019 년 6 월 27 일 | 지원됨 |
| 6 월 2019 업데이트 롤업- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | June 13, 2019 | 지원됨 |
| 2019 년 5 월 업데이트 롤업- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019 년 5 월 7 일 | 지원됨 |
| V6 릴리스- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019 년 4 월 21 일 | 지원됨 |
| 4 월 2019 업데이트 롤업- [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 2019 4 월 4 일 | 지원 됨-에이전트 버전이 2020 년 2 월 12 일에 만료 됩니다. |
| 3 월 2019 업데이트 롤업- [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 2019 년 3 월 7 일 | 지원 됨-에이전트 버전이 2020 년 2 월 12 일에 만료 됩니다. |
| V5 릴리스 - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 2019년 2월 12일 | 지원 됨-에이전트 버전이 2020 년 2 월 12 일에 만료 됩니다. |
| V4 릴리스 | 4.0.1.0 - 4.3.0.0 | N/A | 지원 되지 않음-2019 년 11 월 6 일에 에이전트 버전이 만료 됨 |
| V3 릴리스 | 은 3.1.0.0-v3.4.0.0 | N/A | 지원 되지 않음-에이전트 버전이 8 월 19 일에 만료 됨-2019 |
| GA 에이전트 | 1.1.0.0 - 3.0.13.0 | N/A | 미지원 - 2018년 10월 1일에 에이전트 버전 만료 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>에이전트 버전 9.1.0.0
다음 릴리스 정보는 2019 년 12 월 12 일에 릴리스된 Azure File Sync 에이전트의 버전 9.1.0.0에 대 한 것입니다. 이러한 메모는 버전 9.0.0.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결 된 문제:  
- Azure File Sync 에이전트 버전 9.0으로 업그레이드 한 후 다음 오류 중 하나를 사용 하 여 동기화가 실패 합니다.
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>에이전트 버전 9.0.0.0
다음 릴리스 정보는 Azure File Sync 에이전트 (12 월 2 2019 일 출시 출시)의 버전 9.0.0.0입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 셀프 서비스 복원 지원
    - 사용자는 이제 이전 버전 기능을 사용 하 여 파일을 복원할 수 있습니다. V9 릴리스 전에는 클라우드 계층화를 사용 하도록 설정 된 볼륨에서 이전 버전 기능이 지원 되지 않았습니다. 클라우드 계층화를 사용 하는 엔드포인트이 있는 경우 각 볼륨에 대해이 기능을 별도로 사용 하도록 설정 해야 합니다. 자세한 내용은 다음을 참조하세요.  
[이전 버전 및 VSS (볼륨 섀도 복사본 서비스)를 통한 셀프 서비스 복원](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service) 
 
- 더 큰 파일 공유 크기 지원 
    - Azure File Sync은 이제 단일 동기화 네임 스페이스에서 최대 64TiB 및 1억 파일을 지원 합니다.  
 
- 서버 2019에서 데이터 중복 제거 지원 
    - 이제 Windows Server 2019에서 클라우드 계층화를 사용 하도록 설정 하 여 데이터 중복 제거를 지원 합니다. 클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 지원 하려면 Windows update [KB4520062](https://support.microsoft.com/help/4520062) 를 설치 해야 합니다. 
 
- 계층에 대 한 파일의 최소 파일 크기 개선 
    - 파일 계층에 대 한 최소 파일 크기는 이제 파일 시스템 클러스터 크기 (파일 시스템 클러스터 크기 2 배)를 기반으로 합니다. 예를 들어 기본적으로 NTFS 파일 시스템 클러스터 크기는 4KB 이며, 파일 계층에 대 한 결과 최소 파일 크기는 8KB입니다. 
 
- 네트워크 연결 테스트 cmdlet 
    - Azure File Sync 구성의 일부로 여러 서비스 엔드포인트에 연결 해야 합니다. 각 서버에는 서버에서 액세스할 수 있어야 하는 자체 DNS 이름이 있습니다. 이러한 Url은 서버가 등록 된 지역에 대해서도 고유 합니다. 서버를 등록 한 후에는 연결 테스트 cmdlet (PowerShell 및 서버 등록 유틸리티)을 사용 하 여이 서버와 관련 된 모든 Url과의 통신을 테스트할 수 있습니다. 이 cmdlet은 불완전 한 통신에서 서버가 Azure File Sync 완전히 작동 하지 않도록 하 고 프록시 및 방화벽 구성을 미세 조정 하는 데 사용할 수 있는 경우 문제를 해결 하는 데 도움이 됩니다.  
 
        네트워크 연결 테스트를 실행 하려면 다음 PowerShell 명령을 실행 합니다. 
 
        Import-module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        테스트-StorageSyncNetworkConnectivity
 
- 클라우드 계층화를 사용 하는 경우 서버 엔드포인트 개선 제거 
    - 이전과 마찬가지로 서버 엔드포인트을 제거 해도 Azure 파일 공유에서 파일이 제거 되지 않습니다. 그러나 로컬 서버에서 재분석 지점의 동작이 변경 되었습니다. 서버 엔드포인트을 제거할 때 재분석 지점 (서버의 로컬이 아닌 파일에 대 한 포인터)이 삭제 됩니다. 완전히 캐시 된 파일은 서버에 유지 됩니다. 서버 엔드포인트을 제거할 때 [분리 된 계층화 된 파일](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) 을 방지 하기 위해 이러한 기능이 향상 되었습니다. 서버 엔드포인트을 다시 만들 경우 계층화 된 파일의 재분석 지점은 서버에서 다시 만들어집니다.  
 
- 성능과 안정성이 개선되었습니다. 
    - 회수 오류 감소. 이제 회수 크기는 네트워크 대역폭에 따라 자동으로 조정 됩니다. 
    - 동기화 그룹에 새 서버를 추가할 때 다운로드 성능이 개선 되었습니다. 
    - 제약 조건 충돌로 인해 동기화 되지 않는 파일이 줄어듭니다. 
    - 서버 엔드포인트 경로가 볼륨 탑재 지점인 경우 파일이 계층에 실패 하거나 특정 시나리오에서 예기치 않게 회수 됩니다.
    
### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트가 Nano 서버 배포 옵션에서 지원 되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치 된 서버에서 sysprep을 실행 하는 것은 지원 되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다. 자세한 내용은 [서버 엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없음](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조 하세요.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경 된 파일을 즉시 동기화 하기 위해 [AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용 하 여 azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- 파일 시스템을 클라우드 계층화를 사용 하는 볼륨에 있는 경우 파일의 계층에 오류가 발생할 수 있습니다. 파일 시스템은 클라우드 계층화를 사용 하지 않도록 설정 된 볼륨에 있어야 합니다.

## <a name="agent-version-8000"></a>에이전트 버전 8.0.0.0
다음 릴리스 정보는 Azure File Sync 에이전트의 버전 8.0.0.0 (10 월 8 2019 일에 출시 됨)에 대 한 정보입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 복원 성능 향상
    - Azure Backup를 통해 복구를 위한 복구 시간이 단축 됩니다. 복원 된 파일은 Azure File Sync 서버에 훨씬 빠르게 다시 동기화 됩니다. 
- 향상 된 클라우드 계층화 포털 환경  
    - 회수에 실패 한 계층화 된 파일이 있는 경우 이제 서버 엔드포인트 속성에서 회수 오류를 볼 수 있습니다. 또한 서버에 클라우드 계층화 필터 드라이버가 로드 되지 않은 경우 서버 엔드포인트 상태에는 이제 오류와 완화 단계가 표시 됩니다.
- 간단한 에이전트 설치
    - Az\AzureRM PowerShell 모듈은 더 간단 하 고 신속 하 게 설치할 수 있도록 서버를 등록 하는 데 더 이상 필요 하지 않습니다.
- 기타 성능 및 안정성 향상

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트가 Nano 서버 배포 옵션에서 지원 되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치 된 서버에서 sysprep을 실행 하는 것은 지원 되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다. 자세한 내용은 [서버 엔드포인트을 삭제 한 후 서버에서 계층화 된 파일에 액세스할 수 없음](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)을 참조 하세요.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. Azure 파일 공유에서 변경 된 파일을 즉시 동기화 하기 위해 [AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet을 사용 하 여 azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-7200"></a>에이전트 버전 7.2.0.0
다음 릴리스 정보는 2019 년 7 월 24 일에 릴리스된 Azure File Sync 에이전트의 버전 7.2.0.0에 대 한 것입니다. 이러한 메모는 버전 7.0.0.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- 프록시 구성이 null 인 경우 저장소 동기화 에이전트 (Filedataservice Csvc)가 충돌 합니다.
- 서버 엔드포인트은 서버에서 여러 엔드포인트의 이름이 같은 경우 BCDR (오류 0x80c80257-ECS_E_BCDR_IN_PROGRESS)를 시작 합니다.
- 클라우드 계층화 안정성 향상.

## <a name="agent-version-7100"></a>에이전트 버전 7.1.0.0
다음 릴리스 정보는 2019 년 7 월 12 일에 릴리스된 Azure File Sync 에이전트의 버전 7.1.0.0에 대 한 것입니다. 이러한 메모는 버전 7.0.0.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- Windows Server 2012 r 2에서 SMB를 통한 서버 엔드포인트 위치 액세스 또는 검색 속도가 느립니다. 
- Azure File Sync v6 에이전트를 설치한 후 CPU 사용률이 증가 했습니다.
- 클라우드 계층화 원격 분석 개선
- 클라우드 계층화 및 동기화의 기타 안정성 개선

## <a name="agent-version-7000"></a>에이전트 버전 7.0.0.0
다음 릴리스 정보는 Azure File Sync 에이전트의 버전 7.0.0.0 (2019 년 6 월 19 일 출시)입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 더 큰 파일 공유 크기 지원
    - 대규모 Azure 파일 공유의 미리 보기를 사용 하 여 파일 동기화에 대 한 지원 제한을 늘립니다. 이 첫 번째 단계에서는 Azure File Sync 현재 단일 동기화 네임 스페이스에서 최대 25tb 및 5000만 파일을 지원 합니다. 대량 파일 공유 미리 보기에 적용 하려면이 양식 https://aka.ms/azurefilesatscalesurvey 을 입력 합니다. 
- 저장소 계정에 대 한 방화벽 및 가상 네트워크 설정 지원
    - 이제 Azure File Sync에서 저장소 계정에 대 한 방화벽 및 가상 네트워크 설정을 지원 합니다. 방화벽 및 가상 네트워크 설정에서 작동 하도록 배포를 구성 하려면 [방화벽 및 가상 네트워크 설정 구성](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)을 참조 하세요.
- Azure 파일 공유에서 변경 된 파일을 즉시 동기화 하는 PowerShell cmdlet
    - Azure 파일 공유에서 변경 된 파일을 즉시 동기화 하기 위해 AzStorageSyncChangeDetection PowerShell cmdlet을 사용 하 여 Azure 파일 공유의 변경 내용 검색을 수동으로 시작할 수 있습니다. 이 cmdlet은 일부 유형의 자동화 된 프로세스에서 Azure 파일 공유를 변경 하거나 관리자가 변경 작업을 수행 하는 시나리오를 위한 것입니다 (예: 파일 및 디렉터리를 공유로 이동). 최종 사용자가 변경 하는 경우 IaaS VM에 Azure File Sync 에이전트를 설치 하 고, 최종 사용자가 IaaS VM을 통해 파일 공유에 액세스할 수 있도록 하는 것이 좋습니다. 이렇게 하면 AzStorageSyncChangeDetection cmdlet을 사용 하지 않고 모든 변경 내용을 신속 하 게 다른 에이전트와 동기화 할 수 있습니다. 자세히 알아보려면 [AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) 설명서를 참조 하세요.
- 동기화 되지 않는 파일이 발견 된 경우 포털 환경 개선
    - 동기화에 실패 한 파일이 있는 경우 이제는 포털에서 일시적 오류와 영구 오류를 구분 합니다. 일시적 오류는 일반적으로 관리자 작업이 필요 하지 않고 자체적으로 해결 됩니다. 예를 들어 현재 사용 중인 파일은 파일 핸들이 닫힐 때까지 동기화 되지 않습니다. 지속적인 오류의 경우 이제 각 오류의 영향을 받는 파일 수를 표시 합니다. 영구 오류 수는 동기화 그룹에 있는 모든 서버 엔드포인트의 동기화 되지 않은 파일 열에도 표시 됩니다.
- 향상 된 Azure Backup 파일 수준 복원
    - 이제 Azure Backup를 사용 하 여 복원 된 개별 파일이 검색 되 고 서버 엔드포인트에 더 빠르게 동기화 됩니다.
- 클라우드 계층화 회수 cmdlet 안정성 향상 
    - 이제 Invoke-storagesyncfilerecall cmdlet을 사용 하 여 고객이 robocopy와 유사한 파일 다시 시도 횟수 및 파일당 파일 다시 시도 간격을 지정할 수 있습니다. 이전에는이 cmdlet을 통해 지정 된 경로 아래에 있는 모든 계층화 된 파일을 임의 순서로 회수할 수 있습니다. 이 cmdlet은 새로운 주문 매개 변수를 사용 하 여 가장 많이 사용한 데이터를 먼저 회수 하 고 클라우드 계층화 정책을 적용 합니다 (날짜 정책이 충족 되거나 볼륨의 사용 가능한 공간이 충족 되는 경우 먼저 발생 함).
- TLS 1.2에 대 한 지원만 (TLS 1.0 및 1.1 사용 안 함)
    - 이제 Azure File Sync에서는 TLS 1.0 및 1.1을 사용 하지 않는 서버 에서만 TLS 1.2을 사용할 수 있습니다. 이러한 향상 전에 서버에서 TLS 1.0 및 1.1을 사용 하지 않도록 설정 하면 서버 등록이 실패 합니다.
- 동기화 및 클라우드 계층화를 위한 기타 성능 및 안정성 향상
    - 이 릴리스에서는 몇 가지 안정성과 성능 향상이 있습니다. 이러한 작업 중 일부는 클라우드 계층화를 보다 효율적으로 만들고 Azure File Sync 대역폭 제한 일정을 설정 하는 경우 해당 상황에서 전체 작업을 수행 합니다.

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트가 Nano 서버 배포 옵션에서 지원 되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure File Sync 에이전트가 설치 된 서버에서 sysprep을 실행 하는 것은 지원 되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.

## <a name="agent-version-6300"></a>에이전트 버전 6.3.0.0
다음 릴리스 정보는 2019 년 6 월 27 일에 릴리스된 Azure File Sync 에이전트의 버전 6.3.0.0에 대 한 것입니다. 이러한 메모는 버전 6.0.0.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- Windows Server 2012 r 2에서 SMB를 통한 서버 엔드포인트 위치 액세스 또는 검색 속도가 느립니다. 
- Azure File Sync v6 에이전트를 설치한 후 CPU 사용률 증가
- 클라우드 계층화 원격 분석 개선

## <a name="agent-version-6200"></a>에이전트 버전 6.2.0.0
다음 릴리스 정보는 2019 년 6 월 13 일에 릴리스된 Azure File Sync 에이전트의 버전 6.2.0.0에 대 한 것입니다. 이러한 메모는 버전 6.0.0.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- 서버 엔드포인트을 만든 후 백그라운드 회수가 서버에 파일을 다운로드 하는 경우 높은 CPU 사용량이 발생할 수 있습니다.
- 토큰 만료로 인해 ECS_E_SERVER_CREDENTIAL_NEEDED 오류로 인해 동기화 및 클라우드 계층화 작업이 실패할 수 있음
- 파일을 다운로드 하는 URL이 예약 된 문자를 포함 하는 경우 파일을 회수 하지 못할 수 있음 

## <a name="agent-version-6100"></a>에이전트 버전 6.1.0.0
다음 릴리스 정보는 2019 년 5 월 6 일에 릴리스된 Azure File Sync 에이전트의 버전 6.1.0.0입니다. 이러한 메모는 버전 6.0.0.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- Windows 관리 센터는 Azure File Sync 에이전트 버전 6.0이 설치 된 서버에 에이전트 버전 및 서버 엔드포인트 구성을 표시 하지 못합니다.

## <a name="agent-version-6000"></a>에이전트 버전 6.0.0.0
다음 릴리스 정보는 Azure File Sync 에이전트의 버전 6.0.0.0 (2019 4 월 22 일에 출시 됨)에 대 한 정보입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- 에이전트 자동 업데이트 지원
  - 사용자 의견을 들었습니다. Azure File Sync 서버 에이전트에 자동 업데이트 기능을 추가 했습니다. 자세한 내용은 [Azure File Sync 에이전트 업데이트 정책](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)을 참조 하세요.
- Azure 파일 공유 Acl에 대 한 지원
  - Azure File Sync는 항상 서버 엔드포인트 간에 Acl 동기화를 지원 하지만 Acl은 클라우드 엔드포인트 (Azure 파일 공유)과 동기화 되지 않았습니다. 이 릴리스는 서버와 클라우드 엔드포인트 간에 Acl을 동기화 하기 위한 지원을 추가 합니다.
- 서버 엔드포인트에 대 한 병렬 업로드 및 동기화 세션 다운로드 
  - 이제 서버 엔드포인트에서 파일 업로드 및 다운로드를 동시에 지원 합니다. 다운로드가 완료 될 때까지 기다리지 않으므로 파일이 Azure 파일 공유에 업로드 될 수 있습니다. 
- 볼륨 및 계층화 상태를 가져오기 위한 새 클라우드 계층화 cmdlet
  - 이제 두 개의 새로운 서버 로컬 PowerShell cmdlet을 사용 하 여 클라우드 계층화 및 파일 회수 정보를 얻을 수 있습니다. 서버에서 사용할 수 있는 두 이벤트 채널의 로깅 정보를 제공 합니다.
    - StorageSyncFileTieringResult는 계층화 되지 않은 모든 파일 및 해당 경로를 나열 하 고 이유를 보고 합니다.
    - StorageSyncFileRecallResult는 모든 파일 회수 이벤트를 보고 합니다. 회수 된 모든 파일 및 해당 경로 뿐만 아니라 해당 리콜에 대 한 성공 또는 오류를 표시 합니다.
  - 기본적으로 두 이벤트 채널 모두 최대 1mb를 저장할 수 있습니다. 이벤트 채널 크기를 늘려 보고 되는 파일의 양을 늘릴 수 있습니다.
- FIPS 모드 지원
  - 이제 Azure File Sync Azure File Sync 에이전트가 설치 된 서버에서 FIPS 모드를 사용 하도록 지원 합니다.
    - 서버에서 FIPS 모드를 사용 하도록 설정 하기 전에 서버에 Azure File Sync 에이전트 및 [PackageManagement 모듈](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) 을 설치 합니다. 서버에서 FIPS를 이미 사용 하도록 설정한 경우 서버에 [PackageManagement 모듈](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) 을 [수동으로 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) 합니다.
- 클라우드 계층화 및 동기화에 대 한 기타 안정성 향상

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트가 Nano 서버 배포 옵션에서 지원 되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- SMB 클라이언트에서 파일 속성을 볼 때 오프라인 특성은 파일 메타데이터의 SMB 캐싱으로 인해 잘못 설정되도록 나타날 수 있습니다.

## <a name="agent-version-5200"></a>에이전트 버전 5.2.0.0
다음 릴리스 정보는 2019 년 4 월 4 일에 릴리스된 Azure File Sync 에이전트의 버전 5.2.0.0에 대 한 것입니다. 이러한 메모는 버전 5.0.2.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- 오프 라인 데이터 전송 및 데이터 전송 다시 시작 기능을 위한 안정성 향상
- 동기화 원격 분석 개선

## <a name="agent-version-5100"></a>에이전트 버전 5.1.0.0
다음 릴리스 정보는 2019 년 3 월 7 일에 릴리스된 Azure File Sync 에이전트의 버전 5.1.0.0에 대 한 것입니다. 이러한 메모는 버전 5.0.2.0에 대해 나열 된 릴리스 정보에 추가 되었습니다.

이 릴리스에서 해결된 문제 목록:  
- 서버에서 변경 내용 열거가 실패 한 경우 파일을 오류 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED)와 동기화 하지 못할 수 있습니다.
- 동기화 세션 또는 파일에서 오류 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE)를 수신 하는 경우 동기화는 이제 작업을 다시 시도 합니다.
- 파일이 오류 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)과 동기화 되지 않을 수 있습니다.
- 파일을 회수할 때 높은 메모리 사용량이 발생할 수 있습니다.
- 클라우드 계층화 원격 분석 개선 

## <a name="agent-version-5020"></a>에이전트 버전 5.0.2.0
다음 릴리스 정보는 2019년 2월 12일 릴리스된 Azure 파일 동기화 에이전트의 버전 5.0.2.0에 대한 것입니다.

### <a name="improvements-and-issues-that-are-fixed"></a>개선 사항 및 해결된 문제

- Azure Government 클라우드 지원
  - Azure Government 클라우드에 대한 미리 보기 지원이 추가되었습니다. 이 기능을 이용하려면 허용 목록에 구독을 추가하고 Microsoft에서 특수 에이전트를 다운로드해야 합니다. 미리 보기에 대한 액세스 권한을 얻으려면 [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)으로 직접 메일을 보내 주세요.
- 데이터 중복 제거 지원
    - Windows Server 2016 및 Windows Server 2019에서 클라우드 계층화를 사용하면 이제 데이터 중복 제거가 완전히 지원됩니다. 클라우드 계층화가 사용되는 볼륨에서 중복 제거를 사용하면 추가 스토리지를 프로비저닝하지 않고도 더 많은 파일을 온-프레미스에 캐시할 수 있습니다.
- 오프라인 데이터 전송 지원(예: Data Box 사용)
    - 선택한 수단을 통해 대량 데이터를 Azure 파일 동기화로 쉽게 마이그레이션하세요. Azure Data Box, AzCopy 및 타사 마이그레이션 서비스를 선택할 수 있습니다. Data Box의 경우 데이터를 Azure로 가져오기 위해 대량 대역폭을 사용할 필요가 없습니다. 메일로 전송하면 됩니다. 자세한 내용은 [오프라인 데이터 전송 문서](https://aka.ms/AFS/OfflineDataTransfer)를 참조하세요.
- 향상된 동기화 성능
    - 이 릴리스 이전에는 동일한 볼륨에 여러 개의 서버 엔드포인트가 있는 고객의 경우 동기화 성능이 느려질 수 있었습니다. Azure 파일 동기화는 핸들이 열려 있는 파일을 동기화하기 위해 하루 한 번 서버에 임시 VSS 스냅샷을 만듭니다. 이제 VSS 동기화 세션이 활성 상태인 경우 동기화를 통해 볼륨에서 여러 개의 서버 엔드포인트를 동기화할 수 있습니다. 볼륨의 다른 서버 엔드포인트에서 동기화를 다시 시작하기 위해 VSS 동기화 세션이 완료되기를 기다릴 필요가 없습니다.
- 포털의 모니터링 개선
    - 다음을 확인할 수 있도록 스토리지 동기화 서비스 포털에 차트가 추가되었습니다.
        - 동기화된 파일 수
        - 전송된 데이터 크기
        - 동기화되지 않은 파일 수
        - 회수되는 데이터 크기
        - 서버 연결 상태
    - 자세한 내용은 [Azure 파일 동기화 모니터링](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)을 참조하세요.
- 확장성 및 안정성 개선
    - 한 디렉터리에 포함되는 파일 시스템 개체(디렉터리 및 파일)의 최대 개수가 1,000,000개로 증가했습니다. 이전 한도는 200,000개였습니다.
    - 동기화는 대규모 파일의 전송이 중단될 경우 재전송하지 않고 데이터 전송을 다시 시작합니다. 

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 설치 및 사용 지침에 대해서는 계획 가이드의 [평가 도구](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) 섹션을 참조하세요. 

### <a name="agent-installation-and-server-configuration"></a>에이전트 설치 및 서버 구성
Windows Server와 함께 Azure 파일 동기화 에이전트를 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

- 에이전트 설치 패키지는 상승된(관리자) 권한으로 설치되어야 합니다.
- 에이전트는 Windows Server Core 또는 Nano Server 배포 옵션에서 지원되지 않습니다.
- 에이전트는 Windows Server 2019, Windows Server 2016 및 Windows Server 2012 R2에서만 지원됩니다.
- 에이전트에는 2GiB 이상의 메모리가 필요합니다. 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
- 스토리지 동기화 에이전트(FileSyncSvc) 서비스는 SVI(시스템 볼륨 정보) 디렉터리가 압축된 볼륨에 있는 서버 엔드포인트를 지원하지 않습니다. 이 구성은 예기치 않은 결과를 발생시킵니다.
- FIPS 모드는 지원되지 않으므로 사용하지 않도록 설정해야 합니다. 

### <a name="interoperability"></a>상호 운용성
- 바이러스 백신, 백업, 그리고 계층화된 파일에 액세스하는 기타 애플리케이션은 오프라인 특성을 존중하여 해당 파일의 내용 읽기를 건너뛰지 않는 경우 원치 않은 회수가 발생할 수 있습니다. 자세한 내용은 [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)을 참조하세요.
- 파일 서버 리소스 관리자(FSRM) 파일 차단은 파일 차단으로 인해 파일이 차단된 경우 무한 동기화 실패를 유발할 수 있습니다.
- Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 실행은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 서버 이미지를 배포하고 sysprep 최소 설정을 완료한 후에는 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="sync-limitations"></a>동기화 제한 사항
다음 항목은 동기화되지 않지만 시스템의 나머지 부분은 정상적으로 계속해서 작동합니다.
- 지원되지 않는 문자가 있는 파일. 지원되지 않는 문자 목록은 [문제 해결 가이드](storage-sync-files-troubleshoot.md#handling-unsupported-characters)를 참조하세요.
- 마침표로 끝나는 파일 또는 디렉터리.
- 2048자보다 긴 경로입니다.
- 2KB보다 큰 경우 보안 설명자의 DACL(임의 액세스 제어 목록) 부분입니다. (이 문제는 단일 항목에 약 40개 이상의 ACE(액세스 제어 항목)가 있는 경우에만 적용됩니다.)
- 감사에 사용되는 보안 설명자의 SACL(시스템 액세스 제어 목록) 부분입니다.
- 확장된 특성
- 대체 데이터 스트림
- 재분석 지점
- 하드 링크
- 압축(서버 파일에서 설정하는 경우)은 변경 내용이 다른 엔드포인트의 해당 파일에 대해 동기화할 때 유지되지 않습니다.
- 데이터 읽기에서 서비스를 보호하는 EFS(또는 다른 사용자 모드 암호화)로 암호화된 파일

    > [!Note]  
    > Azure 파일 동기화는 항상 전송 중인 데이터를 암호화합니다. 데이터는 Azure에서 미사용 시 상시 암호화됩니다.
 
### <a name="server-endpoint"></a>서버 엔드포인트
- 서버 엔드포인트는 NTFS 볼륨에서만 만들어질 수 있습니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 현재 Azure 파일 동기화에 의해 지원되지 않습니다.
- 계층화된 파일은 서버 엔드포인트를 삭제하기 전에 파일이 회수되지 않은 경우 사용할 수 없게 됩니다. 파일에 대한 액세스를 복원하려면 서버 엔드포인트를 다시 만들어야 합니다. 서버 엔드포인트가 삭제된 지 30일이 지났거나 클라우드 엔드포인트가 삭제된 경우에는 회수하지 않은 계층화된 파일을 사용할 수 없게 됩니다.
- 클라우드 계층화는 시스템 볼륨에서 지원되지 않습니다. 시스템 볼륨에 서버 엔드포인트를 만들려면 서버 엔드포인트를 만들 때 클라우드 계층화를 사용하지 않도록 설정합니다.
- 장애 조치(failover) 클러스터링은 CSV(클러스터 공유 볼륨)가 아닌 클러스터된 디스크로만 지원됩니다.
- 서버 엔드포인트는 중첩될 수 없습니다. 다른 엔드포인트와 병렬로 동일한 볼륨에 공존할 수 있습니다.
- 서버 엔드포인트 위치 내에 OS 또는 애플리케이션 페이징 파일을 저장하지 마세요.
- 서버는 이름이 바뀐 경우 포털의 서버 이름이 업데이트되지 않습니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
- Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다.
- 스토리지 동기화 서비스 및/또는 스토리지 계정은 기존 Azure AD 테넌트 내의 다른 리소스 그룹 또는 구독으로 이동할 수 있습니다. 스토리지 계정이 이동되는 경우 스토리지 계정에 대한 액세스 권한을 하이브리드 파일 동기화 서비스에 부여해야 합니다([Azure 파일 동기화가 스토리지 계정에 액세스할 수 있는지 확인합니다.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac) 참조).

    > [!Note]  
    > Azure 파일 동기화는 구독을 다른 Azure AD 테넌트로 이동하는 작업을 지원하지 않습니다.

### <a name="cloud-tiering"></a>클라우드 계층화
- 계층화된 파일이 Robocopy를 사용하여 다른 위치로 복사되는 경우 결과 파일은 계층화되지 않습니다. Robocopy에서 복사 작업에 해당 특성을 잘못 포함하므로 오프라인 특성을 설정할 수 있습니다.
- robocopy를 사용하여 파일을 복사할 때는 /MIR 옵션을 사용하여 파일 타임스탬프를 보존해야 합니다. 이렇게 하면 오래된 파일이 최근에 액세스한 파일보다 먼저 계층화됩니다.
- SMB 클라이언트에서 파일 속성을 볼 때 오프라인 특성은 파일 메타데이터의 SMB 캐싱으로 인해 잘못 설정되도록 나타날 수 있습니다.
