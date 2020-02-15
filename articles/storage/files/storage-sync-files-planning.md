---
title: Azure 파일 동기화 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 483603b8ff2f4b51f85d21d6ff4f02ad6f8a8272
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162092"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure 파일 동기화 배포에 대한 계획
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 파일 동기화 배포에 대한 중요 고려 사항을 설명합니다. [Azure Files 배포에 대한 계획](storage-files-planning.md)도 읽어보는 것이 좋습니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure 파일 동기화 용어
Azure 파일 동기화 배포 계획을 세부적으로 알아보기 전에 용어를 이해하는 것이 중요합니다.

### <a name="storage-sync-service"></a>스토리지 동기화 서비스
저장소 동기화 서비스는 Azure File Sync에 대 한 최상위 수준의 Azure 리소스입니다. 저장소 동기화 서비스 리소스는 저장소 계정 리소스의 피어 이며 유사 하 게 Azure 리소스 그룹에 배포할 수 있습니다. Storage 동기화 서비스는 여러 동기화 그룹을 통해 여러 스토리지 계정과 동기화 관계를 만들 수 있기 때문에 스토리지 계정 리소스와는 별개의 최상위 수준 리소스가 필요합니다. 하나의 구독으로 여러 스토리지 동기화 서비스 리소스가 배포될 수 있습니다.

### <a name="sync-group"></a>동기화 그룹
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 예를 들어 Azure File Sync를 사용 하 여 관리 하려는 2 개의 고유한 파일 집합이 있는 경우 두 개의 동기화 그룹을 만들고 각 동기화 그룹에 다른 끝점을 추가 합니다. 스토리지 동기화 서비스는 필요한 만큼의 동기화 그룹을 호스트할 수 있습니다.  

### <a name="registered-server"></a>등록된 서버
등록된 서버 개체는 서버(또는 클러스터)와 Storage 동기화 서비스 간의 신뢰 관계를 나타냅니다. 원하는 수 만큼의 서버를 Storage 동기화 서비스 인스턴스에 등록할 수 있습니다. 그렇지만 한 번에 하나의 서버(또는 클러스터)만 하나의 Storage 동기화 서비스에 등록될 수 있습니다.

### <a name="azure-file-sync-agent"></a>Azure 파일 동기화 에이전트
Azure 파일 동기화 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. Azure 파일 동기화 에이전트는 다음 3가지 주요 구성 요소로 이루어집니다. 
- **Filesgacutil.exe Csvc. .exe**: 서버 끝점의 변경 내용을 모니터링 하 고 Azure에 대 한 동기화 세션을 시작 하는 백그라운드 서비스입니다.
- **StorageSync.sys**: Azure Files로의 파일 계층화를 담당하는 Azure 파일 동기화 파일 시스템 필터입니다(클라우드 계층화가 사용될 경우).
- **PowerShell 관리 cmdlet**: Microsoft.StorageSync Azure 리소스 공급자와 상호 작용하는 데 사용하는 PowerShell cmdlet입니다. 다음(기본) 위치에서 이러한 항목을 찾을 수 있습니다.
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>서버 엔드포인트
서버 엔드포인트는 서버 볼륨의 폴더와 같이 등록된 서버의 특정 위치를 나타냅니다. 해당 네임스페이스가 겹치지 않는 경우 동일한 볼륨에 여러 서버 엔드포인트가 있을 수 있습니다(예: `F:\sync1` 및 `F:\sync2`). 각 서버 엔드포인트에 대해 개별적으로 클라우드 계층화 정책을 구성할 수 있습니다. 

탑재 지점을 통해 서버 엔드포인트를 만들 수 있습니다. 단, 서버 엔드포인트 내의 탑재 지점을 건너뜁니다.  

시스템 볼륨에서 서버 엔드포인트를 만들 수는 있지만, 그렇게 하려면 두 가지 제한 사항이 있습니다.
* 클라우드 계층화를 사용할 수 없습니다.
* 신속한 네임스페이스 복원(여기서는 시스템은 신속하게 전체 네임스페이스를 가져온 다음. 콘텐츠를 회수하기 위해 시작함)이 수행되지 않습니다.


> [!Note]  
> 비이동식 볼륨만 사용할 수 있습니다.  원격 공유에서 매핑된 드라이브는 서버 엔드포인트 경로에서 지원되지 않습니다.  또한 시스템 볼륨에서 클라우드 계층화가 지원되지 않지만, 서버 엔드포인트가 Windows 시스템 볼륨에 있을 수 있습니다.

기존 파일 집합이 있는 서버 위치를 동기화 그룹에 서버 엔드포인트로 추가하는 경우 해당 파일은 동기화 그룹의 다른 엔드포인트에 이미 있는 다른 파일에 병합됩니다.

### <a name="cloud-endpoint"></a>클라우드 엔드포인트
클라우드 엔드포인트는 동기화 그룹의 일부인 Azure 파일 공유입니다. 전체 Azure 파일 공유가 동기화되며, Azure 파일 공유는 하나의 클라우드 엔드포인트의 구성원만 될 수 있습니다. 따라서 Azure 파일 공유는 하나의 동기화 그룹의 구성원만 될 수 있습니다. 기존 파일 집합이 있는 Azure 파일 공유를 동기화 그룹에 클라우드 엔드포인트로 추가하는 경우 기존 파일은 동기화 그룹의 다른 엔드포인트에 이미 있는 다른 파일에 병합됩니다.

> [!Important]  
> Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 또한 REST 프로토콜을 통해 Azure 파일 공유에 대 한 변경 내용은 SMB 마지막 수정 시간을 업데이트 하지 않으며 동기화에의 한 변경으로 표시 되지 않습니다. 자세한 내용은 [Azure Files 질문과 대답](storage-files-faq.md#afs-change-detection)을 참조 하세요.

### <a name="cloud-tiering"></a>클라우드 계층화 
Azure 파일 동기화의 선택적 기능인 클라우드 계층화를 사용하는 경우 액세스 빈도가 높은 파일은 서버에 로컬로 캐시되고 그 외의 모든 파일은 정책 설정에 따라 Azure Files에서 계층화됩니다. 자세한 내용은 [클라우드 계층화 이해](storage-sync-cloud-tiering.md)를 참조하세요.

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure 파일 동기화 시스템 요구 사항 및 상호 운용성 
이 섹션에서는 Azure 파일 동기화 에이전트 시스템 요구 사항 및 Windows Server 기능과 역할 및 타사 솔루션과의 상호 운용성에 대해 설명합니다.

### <a name="evaluation-cmdlet"></a>Evaluation cmdlet
Azure File Sync를 배포 하기 전에 Azure File Sync evaluation cmdlet을 사용 하 여 시스템과 호환 되는지 여부를 평가 해야 합니다. 이 cmdlet은 지원 되지 않는 문자 또는 지원 되지 않는 운영 체제 버전과 같은 파일 시스템 및 데이터 집합의 잠재적인 문제를 확인 합니다. 해당 검사는 아래에 설명 된 기능 중 일부에 대해서만 적용 됩니다. 배포를 원활 하 게 진행 하려면이 섹션의 나머지 부분을 자세히 읽어 보는 것이 좋습니다. 

[Azure PowerShell 설치 및 구성](https://docs.microsoft.com/powershell/azure/install-Az-ps)지침에 따라 설치할 수 있는 Az PowerShell module을 설치 하 여 evaluation cmdlet을 설치할 수 있습니다.

#### <a name="usage"></a>사용  
평가 도구는 몇 가지 다른 방법으로 호출할 수 있습니다. 즉 시스템 검사, 데이터 세트 검사 또는 둘 다를 수행할 수 있습니다. 시스템 검사 및 데이터 세트 검사를 모두 수행하려면 다음을 수행합니다. 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

데이터 세트만 테스트하려면 다음을 수행합니다.
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
시스템 요구 사항만 테스트하려면 다음을 수행합니다.
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
결과를 CSV 형식으로 표시하려면 다음을 수행합니다.
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>시스템 요구 사항
- 다음 운영 체제 버전 중 하나를 실행 하는 서버:

    | 버전 | 지원되는 SKU | 지원되는 배포 옵션 |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter 및 Standard | 전체 및 코어 |
    | Windows Server 2016 | Datacenter 및 Standard | 전체 및 코어 |
    | Windows Server 2012 R2 | Datacenter 및 Standard | 전체 및 코어 |
    | 저장소에 대 한 Windows Server IoT 2019| Datacenter 및 Standard | 전체 및 코어 |
    | Windows Storage Server 2016| Datacenter 및 Standard | 전체 및 코어 |
    | Windows Storage Server 2012 R2| Datacenter 및 Standard | 전체 및 코어 |

    이후 버전의 Windows Server는 출시되면 추가될 예정입니다.

    > [!Important]  
    > Windows 업데이트의 최신 업데이트를 사용하여 Azure 파일 동기화와 함께 사용되는 모든 서버를 최신 상태로 유지하는 것이 좋습니다. 

- 2GiB 이상의 메모리가 있는 서버

    > [!Important]  
    > 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
    
- NTFS 파일 시스템으로 포맷되어 로컬로 연결된 볼륨

### <a name="file-system-features"></a>파일 시스템 기능

| 기능 | 상태 지원 | 메모 |
|---------|----------------|-------|
| ACL(액세스 제어 목록) | 완벽하게 지원 | Windows ACL은 Azure 파일 동기화에 의해 유지되며 서버 엔드포인트에서 Windows Server에 의해 적용됩니다. 파일이 클라우드에서 직접 액세스될 경우 Azure Files에서 Windows ACL을 (아직) 지원하지 않는 것입니다. |
| 하드 링크 | 건너뜀 | |
| 바로 가기 링크 | 건너뜀 | |
| 탑재 지점 | 부분적으로 지원됨 | 탑재 지점은 서버 엔드포인트의 루트일 수 있지만, 서버 엔드포인트의 네임스페이스에 포함된 경우 건너뜁니다. |
| 분기 동기화 | 건너뜀 | 분산 파일 시스템 DfrsrPrivate 및 DFSRoots 폴더를 예로 들 수 있습니다. |
| 재분석 지점 | 건너뜀 | |
| NTFS 압축 | 완벽하게 지원 | |
| 스파스 파일 | 완벽하게 지원 | 스파스 파일은 동기화되지만(차단되지 않음) 전체 파일로 클라우드와 동기화됩니다. 클라우드(또는 다른 서버)에서 파일 콘텐츠가 변경될 경우 변경 내용이 다운로드되면 파일은 더 이상 스파스 파일이 아닙니다. |
| ADS(대체 데이터 스트림) | 보존되지만 동기화되지 않음 | 예를 들어, 파일 분류 인프라에서 만들어진 분류 태그는 동기화되지 않습니다. 각 서버 엔드포인트의 파일에 대한 기존 분류 태그는 그대로 유지됩니다. |

> [!Note]  
> NTFS 볼륨만 지원됩니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 지원되지 않습니다.

### <a name="files-skipped"></a>건너뛴 파일

| 파일/폴더 | 참고 |
|-|-|
| pagefile.sys | 시스템에 특정된 파일 |
| Desktop.ini | 시스템에 특정된 파일 |
| thumbs.db | 썸네일의 임시 파일 |
| ehthumbs.db | 미디어 미리 보기의 임시 파일 |
| ~$\*.\* | Office 임시 파일 |
| \*.tmp | 임시 파일 |
| \*.laccdb | Access DB 잠금 파일|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 내부 동기화 파일|
| \\시스템 볼륨 정보 | 볼륨에 특정된 폴더 |
| $RECYCLE.BIN| 폴더 |
| \\SyncShareState | 동기화할 폴더 |

### <a name="failover-clustering"></a>장애 조치(Failover) 클러스터링
Windows Server 장애 조치(Failover) 클러스터링은 "범용 파일 서버" 배포 옵션의 Azure 파일 동기화에서 지원됩니다. 장애 조치(Failover) 클러스터링은 "애플리케이션 데이터용 스케일 아웃 파일 서버" 또는 "CSV(클러스터된 공유 볼륨)"에서는 지원되지 않습니다.

> [!Note]  
> 동기화가 제대로 작동하려면 장애 조치(Failover) 클러스터의 모든 노드에 Azure 파일 동기화 에이전트를 설치해야 합니다.

### <a name="data-deduplication"></a>데이터 중복 제거
**Windows server 2016 및 Windows server 2019**   
Windows Server 2016 및 Windows Server 2019에서 클라우드 계층화를 사용하면 볼륨의 데이터 중복 제거가 지원됩니다. 클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 사용 하도록 설정 하면 더 많은 저장소를 프로 비전 하지 않고 온-프레미스에서 더 많은 파일을 캐시할 수 

클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 사용 하도록 설정 하면 서버 끝점 위치 내의 중복 제거 최적화 파일은 클라우드 계층화 정책 설정에 따라 일반 파일과 비슷하게 계층화 됩니다. 중복 제거 최적화 파일이 계층화 되 면 데이터 중복 제거 가비지 수집 작업이 자동으로 실행 되어 볼륨의 다른 파일에서 더 이상 참조 되지 않는 불필요 한 청크를 제거 하 여 디스크 공간을 회수 합니다.

볼륨 절감은 서버에만 적용 됩니다. Azure 파일 공유의 데이터는 중복 제거 된 되지 않습니다.

> [!Note]  
> Windows Server 2019에서 클라우드 계층화를 사용 하는 볼륨에서 데이터 중복 제거를 지원 하려면 Windows update [KB4520062](https://support.microsoft.com/help/4520062) 가 설치 되어 있어야 하 Azure File Sync 고 에이전트 버전 9.0.0.0 이상이 필요 합니다.

**Windows Server 2012 R2**  
Azure File Sync는 Windows Server 2012 r 2의 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원 하지 않습니다. 볼륨에서 데이터 중복 제거를 사용 하는 경우 클라우드 계층화를 사용 하지 않도록 설정 해야 합니다. 

**참고 사항**
- Azure File Sync 에이전트를 설치 하기 전에 데이터 중복 제거를 설치한 경우 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원 하려면 다시 시작 해야 합니다.
- 클라우드 계층화를 사용 하도록 설정한 후 볼륨에서 데이터 중복 제거를 사용 하는 경우 초기 중복 제거 최적화 작업은 볼륨에서 아직 계층화 되지 않은 파일을 최적화 하 고 클라우드 계층화에 다음과 같은 영향을 미칩니다.
    - 사용 가능한 공간 정책은 열 지도를 사용 하 여 볼륨의 사용 가능한 공간에 따라 파일을 계속 계층 합니다.
    - 날짜 정책은 파일에 액세스 하는 중복 제거 최적화 작업으로 인해 계층화 될 수 있는 파일의 계층화를 건너뜁니다.
- 지속적인 중복 제거 최적화 작업의 경우 파일이 아직 계층화 되지 않은 경우 데이터 중복 제거 [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) 설정에 의해 날짜 정책을 사용한 클라우드 계층화가 지연 됩니다. 
    - 예: MinimumFileAgeDays 설정이 7 일이 고 클라우드 계층화 날짜 정책이 30 일인 경우 날짜 정책은 37 일 후에 파일을 계층화 합니다.
    - 참고: Azure File Sync에 의해 파일이 계층화 되 면 중복 제거 최적화 작업은 파일을 건너뜁니다.
- Azure File Sync 에이전트가 설치 된 Windows Server 2012 r 2를 실행 하는 서버를 Windows Server 2016 또는 Windows Server 2019로 업그레이드 하는 경우 동일한 볼륨에서 데이터 중복 제거 및 클라우드 계층화를 지원 하려면 다음 단계를 수행 해야 합니다.  
    - Windows Server 2012 r 2 용 Azure File Sync 에이전트를 제거 하 고 서버를 다시 시작 합니다.
    - 새 서버 운영 체제 버전 (Windows Server 2016 또는 Windows Server 2019)에 대 한 Azure File Sync 에이전트를 다운로드 합니다.
    - Azure File Sync 에이전트를 설치 하 고 서버를 다시 시작 합니다.  
    
    참고: 에이전트를 제거 하 고 다시 설치 하면 서버의 Azure File Sync 구성 설정이 유지 됩니다.

### <a name="distributed-file-system-dfs"></a>분산 파일 시스템(DFS)
Azure 파일 동기화에서는 DFS-N(DFS 네임스페이스) 및 DFS-R(DFS 복제)과의 상호 작용을 지원합니다.

**DFS 네임 스페이스(DFS-N)** : DFS-N 서버에서 Azure 파일 동기화가 완전히 지원됩니다. 하나 이상의 DFS-N 멤버에 Azure 파일 동기화 에이전트를 설치하면 서버 엔드포인트 및 클라우드 엔드포인트 간에 데이터를 동기화할 수 있습니다. 자세한 내용은 [DFS 네임스페이스 개요](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)를 참조하세요.
 
**DFS 복제 (dfs-r)** : dfs 및 Azure File Sync 모두 복제 솔루션 이기 때문에 대부분의 경우에는 Dfs-r을 Azure File Sync로 바꾸는 것이 좋습니다. 그러나 DFS-R을 사용 하 고 함께 Azure File Sync 하는 몇 가지 시나리오가 있습니다.

- DFS-R 배포에서 Azure 파일 동기화 배포로 마이그레이션하는 중입니다. 자세한 내용은 [DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)을 참조하세요.
- 파일 데이터의 복사본을 필요로 하는 모든 온-프레미스 서버를 인터넷에 직접 연결할 수 있는 것은 아닙니다.
- 분기 서버는 Azure 파일 동기화를 사용할 단일 허브 서버에 데이터를 통합합니다.

Azure File Sync와 DFS가 나란히 작동 하도록 하려면 다음을 수행 합니다.

1. Azure 파일 동기화 클라우드 계층화는 DFS-R 복제 폴더를 포함하는 볼륨에서 사용하지 않도록 설정해야 합니다.
2. 서버 엔드포인트는 DFS-R 읽기 전용 복제 폴더에 대해 구성할 수 없습니다.

자세한 내용은 [DFS 복제 개요](https://technet.microsoft.com/library/jj127250)를 참조하세요.

### <a name="sysprep"></a>Sysprep
Azure File Sync 에이전트가 설치 된 서버에서 sysprep를 사용 하는 것은 지원 되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 에이전트 설치 및 서버 등록은 서버 이미지 배포 및 sysprep 최소 설치 완료 후 발생해야 합니다.

### <a name="windows-search"></a>Windows 검색
클라우드 계층화가 서버 엔드포인트에서 활성화되면 계층화된 파일은 건너뛰고 Windows 검색을 통해 인덱싱되지 않습니다. 계층화되지 않은 파일은 제대로 인덱싱됩니다.

### <a name="antivirus-solutions"></a>바이러스 백신 솔루션
바이러스 백신은 알려진 악성 코드 파일을 검색하는 방식으로 작동하기 때문에 바이러스 백신 제품은 계층화된 파일의 회수를 발생할 수 있습니다. Azure 파일 동기화 에이전트의 버전 4.0 이상에서 계층화된 파일에는 보안 Windows 특성 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS가 설정되어 있습니다. 이 특성이 설정된 파일 읽기를 건너뛰도록(대부분 자동으로 수행) 솔루션을 구성하는 방법을 소프트웨어 공급업체에 문의하세요. 

Microsoft의 사내 바이러스 백신 솔루션, Windows Defender 및 SCEP(System Center Endpoint Protection) 모두는 이 특성이 설정된 파일 읽기를 자동으로 건너뜁니다. 이러한 솔루션을 테스트하여 하나의 사소한 문제를 확인했습니다. 즉 기존 동기화 그룹에 서버를 추가하면 800바이트보다 작은 파일이 새 서버에서 회수(다운로드)됩니다. 이러한 파일은 새 서버에 남아 있지만 계층화 크기 요구 사항(64kb 초과)을 충족하지 않으므로 계층화되지 않습니다.

> [!Note]  
> 바이러스 백신 공급 업체는 Microsoft 다운로드 센터에서 다운로드할 수 있는 [Azure File Sync 바이러스 백신 호환성 테스트 도구 모음](https://www.microsoft.com/download/details.aspx?id=58322)을 사용 하 여 제품과 Azure File Sync 간의 호환성을 확인할 수 있습니다.

### <a name="backup-solutions"></a>백업 솔루션
바이러스 백신 솔루션과 같은 백업 솔루션은 계층화된 파일이 회수되도록 할 수 있습니다. 온-프레미스 백업 제품을 사용하지 않고 클라우드 백업 솔루션을 사용하여 Azure 파일 공유를 백업하는 것이 좋습니다.

온-프레미스 백업 솔루션을 사용 하는 경우 클라우드 계층화를 사용 하지 않는 동기화 그룹의 서버에서 백업을 수행 해야 합니다. 복원을 수행할 때 볼륨 수준 또는 파일 수준 복원 옵션을 사용합니다. 파일 수준 복원 옵션을 사용하여 복원된 파일은 동기화 그룹의 모든 엔드포인트에 동기화되고 기존 파일은 백업에서 복원된 버전으로 대체됩니다.  볼륨 수준 복원은 Azure 파일 공유 또는 기타 서버 엔드포인트의 최신 파일 버전을 대체하지 않습니다.

> [!Note]  
> BMR(완전 복구) 복원은 예기치 않은 결과가 발생할 수 있으며 현재 지원되지 않습니다.

> [!Note]  
> Azure File Sync 에이전트 버전 9를 사용 하는 경우 클라우드 계층화를 사용 하는 볼륨에서 VSS 스냅숏 (이전 버전 탭 포함)이 지원 됩니다. 그러나 PowerShell을 통해 이전 버전 호환성을 사용 하도록 설정 해야 합니다. [방법을 알아보세요](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>암호화 솔루션
암호화 솔루션에 대한 지원은 구현되는 방식에 따라 달라집니다. Azure 파일 동기화와 함께 작동하는 기능

- BitLocker 암호화
- Azure Information Protection, Azure RMS(Rights Management Services) 및 Active Directory RMS

Azure 파일 동기화와 함께 작동하지 않는 기능

- NTFS EFS(암호화 파일 시스템)

일반적으로 Azure 파일 동기화는 BitLocker처럼 파일 시스템 아래에 있는 암호화 솔루션 및 Azure Information Protection과 같은 파일 형식으로 구현된 솔루션과의 상호 운용성을 지원해야 합니다. 파일 시스템 위에 있는 솔루션(예: NTFS EFS)에 대해서는 특별한 상호 운용성이 설정되지 않았습니다.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>다른 HSM(계층적 스토리지 관리) 솔루션
다른 HSM 솔루션은 Azure 파일 동기화와 함께 사용하면 안 됩니다.

## <a name="region-availability"></a>지역 가용성
Azure 파일 동기화는 다음 지역에서만 사용할 수 있습니다.

| 지역 | 데이터 센터 위치 |
|--------|---------------------|
| 오스트레일리아 동부 | 뉴사우스웨일스 |
| 오스트레일리아 남동부 | 빅토리아 |
| 브라질 남부 | 상 파울로 상태 |
| 캐나다 중부 | Toronto |
| 캐나다 동부 | 퀘벡 시티 |
| 인도 중부 | 푸네 |
| 미국 중부 | 아이오와 |
| 동아시아 | 홍콩 특별 행정구 |
| 미국 동부 | 버지니아 |
| 미국 동부2 | 버지니아 |
| 프랑스 중부 | 파리 |
| 프랑스 남부 * | 마르세유에 |
| 한국 중부 | 서울 |
| 한국 남부 | 부산 |
| 일본 동부 | 도쿄, 사이타마 |
| 일본 서부 | 오사카 |
| 미국 중북부 | 일리노이 |
| 북유럽 | 아일랜드 |
| 남아프리카 북부 | 요하네스버그 |
| 남아프리카 공화국 * | 카보베르데 |
| 미국 중남부 | 텍사스 |
| 인도 남부 | 첸나이 |
| 동남아시아 | 싱가포르 |
| 영국 남부 | London |
| 영국 서부 | 카디프 |
| US Gov 애리조나 | Arizona |
| US Gov 텍사스 | 텍사스 |
| US Gov 버지니아 | 버지니아 |
| 아랍에미리트 북부 | Dubai |
| 아랍에미리트 중부 * | 아부다비 |
| 서유럽 | 네덜란드 |
| 미국 중서부 | 와이오밍 |
| 미국 서부 | 캘리포니아 |
| 미국 서부 2 | 워싱턴 |

Azure 파일 동기화에서는 Storage 동기화 서비스와 동일한 지역에 있는 Azure 파일 공유와의 동기화만 지원합니다.

별표로 표시 된 영역의 경우 해당 지역에서 Azure Storage에 대 한 액세스를 요청 하려면 Azure 지원에 문의 해야 합니다. 이 프로세스는 [이 문서](https://azure.microsoft.com/global-infrastructure/geographies/)에 설명 되어 있습니다.

### <a name="azure-disaster-recovery"></a>Azure 재해 복구
Azure 지역의 손실을 방지 하기 위해 Azure File Sync는 GRS ( [지역 중복 저장소](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) )와 통합 됩니다. GRS 스토리지는 일반적으로 상호 작용하는 주 지역의 스토리지 및 쌍을 이루는 보조 지역의 스토리지 간에 비동기 블록 복제를 사용하여 작동합니다. Azure 지역이 일시적 또는 영구적으로 오프 라인으로 전환 되는 재해가 발생 한 경우 Microsoft는 페어링된 지역으로 저장소를 장애 조치 (failover) 합니다. 

> [!Warning]  
> Azure 파일 공유를 GRS 스토리지 계정의 클라우드 엔드포인트로 사용하는 경우 스토리지 계정 장애 조치(failover)를 시작하면 안 됩니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. Azure 지역이 손실되는 경우 Microsoft는 Azure 파일 동기화와 호환되는 방식으로 스토리지 계정의 장애 조치(failover)를 트리거합니다.

지역 중복 스토리지 및 Azure 파일 동기화 간의 장애 조치 통합을 지원하려면 모든 Azure 파일 동기화 지역이 스토리지로 사용되는 보조 지역과 일치하는 해당 보조 지역과 쌍을 이루어야 합니다. 이러한 쌍은 다음과 같습니다.

| 주 지역      | 쌍을 이루는 지역      |
|---------------------|--------------------|
| 오스트레일리아 동부      | 오스트레일리아 남동부|
| 오스트레일리아 남동부 | 오스트레일리아 동부     |
| 브라질 남부        | 미국 중남부   |
| 캐나다 중부      | 캐나다 동부        |
| 캐나다 동부         | 캐나다 중부     |
| 인도 중부       | 인도 남부        |
| 미국 중부          | 미국 동부 2          |
| 동아시아           | 동남아시아     |
| 미국 동부             | 미국 서부            |
| 미국 동부 2           | 미국 중부         |
| 프랑스 중부      | 프랑스 남부       |
| 프랑스 남부        | 프랑스 중부     |
| 일본 동부          | 일본 서부         |
| 일본 서부          | 일본 동부         |
| 한국 중부       | 한국 남부        |
| 한국 남부         | 한국 중부      |
| 북유럽        | 서유럽        |
| 미국 중북부    | 미국 중남부   |
| 남아프리카 북부  | 남아프리카 서부  |
| 남아프리카 서부   | 남아프리카 북부 |
| 미국 중남부    | 미국 중북부   |
| 인도 남부         | 인도 중부      |
| 동남아시아      | 동아시아          |
| 영국 남부            | 영국 서부            |
| 영국 서부             | 영국 남부           |
| US Gov 애리조나      | US Gov 텍사스       |
| US Gov 아이오와         | US Gov 버지니아    |
| US Gov 버지니아      | US Gov 텍사스       |
| 서유럽         | 북유럽       |
| 미국 중서부     | 미국 서부 2          |
| 미국 서부             | 미국 동부            |
| 미국 서부 2           | 미국 중서부    |

## <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>권장 Azure File Sync 컴퓨터 구성

Azure File Sync 컴퓨터 요구 사항은 네임 스페이스의 개체 수와 데이터 집합의 변동에 따라 결정 됩니다. 단일 서버를 여러 동기화 그룹에 연결할 수 있으며 서버가 연결 된 전체 네임 스페이스에 대 한 다음 테이블 계정에 나열 된 개체 수를 지정할 수 있습니다. 예를 들어 서버 끝점 A에는 1000만 개의 개체가 있고 서버 끝점 B에는 1000만 objects = 2000만 개체가 있습니다. 이 배포 예에서는 초기 마이그레이션의 경우 16GiB CPU, 안정 된 상태에 대 한 메모리 및 (가능한 경우)의 메모리를 권장 합니다.
 
네임 스페이스 데이터는 성능상의 이유로 메모리에 저장 됩니다. 따라서 좋은 성능을 유지 하기 위해 더 큰 네임 스페이스에 더 많은 메모리가 필요 하 고, 더 많은 변동에는 처리 하는 데 더 많은 CPU가 필요 합니다. 
 
다음 표에서는 일반적인 범용 파일 공유의 용량에 대 한 변환 뿐만 아니라 네임 스페이스의 크기를 모두 제공 했습니다. 여기서 평균 파일 크기는 512KiB입니다. 파일 크기가 더 작은 경우 동일한 용량의 용량에 메모리를 더 추가 하는 것이 좋습니다. 네임 스페이스의 크기에 맞게 메모리 구성을 설정 합니다.

| 네임 스페이스 크기-파일 & 디렉터리 (수백만)  | 일반적인 용량 (TiB)  | CPU 코어 수  | 권장 메모리 (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (초기 동기화)/2 (일반적인 변동)      |
| 5        | 2.4     | 2        | 16 (초기 동기화)/4 (일반적인 변동)    |
| 10       | 4.8     | 4        | 32 (초기 동기화)/8 (일반적인 변동)   |
| 30       | 14.3    | 8        | 48 (초기 동기화)/16 (일반적인 변동)   |
| 50       | 23.8    | 16       | 64 (초기 동기화)/32 (일반적인 변동)  |
| 100*     | 47.7   | 32       | 128 (초기 동기화)/32 (일반적인 변동)  |

1억 개 이상의 파일을 \*& 디렉터리가 테스트 되지 않았습니다. 소프트 제한입니다.

> [!TIP]
> 네임 스페이스의 초기 동기화는 집약적인 작업 이므로 초기 동기화가 완료 될 때까지 추가 메모리를 할당 하는 것이 좋습니다. 이는 필수는 아니지만 초기 동기화 속도를 높일 수 있습니다. 
> 
> 일반적인 변동은 하루에 변경 되는 네임 스페이스의 0.5%입니다. 더 높은 변동 수준을 위해 CPU를 더 추가 하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계
* [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
* [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
