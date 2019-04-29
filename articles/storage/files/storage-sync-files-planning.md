---
title: Azure 파일 동기화 배포에 대한 계획 | Microsoft Docs
description: Azure Files 배포를 계획할 때 고려할 사항을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f29625ed8ddd6eabf8b75380d84d7a7b64396d7a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766854"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure 파일 동기화 배포에 대한 계획
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure 파일 동기화 배포에 대한 중요 고려 사항을 설명합니다. [Azure Files 배포에 대한 계획](storage-files-planning.md)도 읽어보는 것이 좋습니다. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure 파일 동기화 용어
Azure 파일 동기화 배포 계획을 세부적으로 알아보기 전에 용어를 이해하는 것이 중요합니다.

### <a name="storage-sync-service"></a>저장소 동기화 서비스
저장소 동기화 서비스는 Azure 파일 동기화의 최상위 Azure 리소스입니다. Storage 동기화 서비스 리소스는 스토리지 계정 리소스의 피어로, Azure 리소스 그룹에 쉽게 배포할 수 있습니다. Storage 동기화 서비스는 여러 동기화 그룹을 통해 여러 스토리지 계정과 동기화 관계를 만들 수 있기 때문에 스토리지 계정 리소스와는 별개의 최상위 수준 리소스가 필요합니다. 하나의 구독으로 여러 저장소 동기화 서비스 리소스가 배포될 수 있습니다.

### <a name="sync-group"></a>동기화 그룹
동기화 그룹은 파일 집합에 대한 동기화 토폴로지를 정의합니다. 동기화 그룹 내 엔드포인트는 서로 동기화된 상태를 유지합니다. 예를 들어 Azure 파일 동기화를 사용하여 관리하려는 2개의 고유한 파일 집합이 있는 경우 2개의 동기화 그룹을 만들고 각 동기화 그룹에 다른 엔드포인트를 추가합니다. 저장소 동기화 서비스는 필요한 만큼의 동기화 그룹을 호스트할 수 있습니다.  

### <a name="registered-server"></a>등록된 서버
등록된 서버 개체는 서버(또는 클러스터)와 Storage 동기화 서비스 간의 신뢰 관계를 나타냅니다. 원하는 수 만큼의 서버를 Storage 동기화 서비스 인스턴스에 등록할 수 있습니다. 그렇지만 한 번에 하나의 서버(또는 클러스터)만 하나의 Storage 동기화 서비스에 등록될 수 있습니다.

### <a name="azure-file-sync-agent"></a>Azure 파일 동기화 에이전트
Azure 파일 동기화 에이전트는 Windows Server가 Azure 파일 공유와 동기화되도록 하는 다운로드 가능 패키지입니다. Azure 파일 동기화 에이전트는 다음 3가지 주요 구성 요소로 이루어집니다. 
- **FileSyncSvc.exe**: 서버 엔드포인트의 변경 내용을 모니터링하고 Azure와의 동기화 세션을 시작하는 백그라운드 Windows 서비스입니다.
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
> Azure 파일 동기화는 Azure 파일 공유를 직접 변경하도록 지원합니다. 그러나 먼저 Azure 파일 공유의 변경 내용이 Azure 파일 동기화 변경 검색 작업에서 검색되어야 합니다. 변경 내용 검색 작업은 클라우드 엔드포인트에 대해 24시간마다 한 번씩만 시작됩니다. 또한 REST 프로토콜을 통해 수행한 Azure 파일 공유 변경 내용은 SMB 마지막 수정 시간을 업데이트하지 않으며 동기화 기능에서 변경 내용으로 표시되지 않습니다. 자세한 내용은 [Azure Files 질문과 대답](storage-files-faq.md#afs-change-detection)을 참조하세요.

### <a name="cloud-tiering"></a>클라우드 계층화 
Azure 파일 동기화의 선택적 기능인 클라우드 계층화를 사용하는 경우 액세스 빈도가 높은 파일은 서버에 로컬로 캐시되고 그 외의 모든 파일은 정책 설정에 따라 Azure Files에서 계층화됩니다. 자세한 내용은 [클라우드 계층화 이해](storage-sync-cloud-tiering.md)를 참조하세요.

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure 파일 동기화 시스템 요구 사항 및 상호 운용성 
이 섹션에서는 Azure 파일 동기화 에이전트 시스템 요구 사항 및 Windows Server 기능과 역할 및 타사 솔루션과의 상호 운용성에 대해 설명합니다.

### <a name="evaluation-tool"></a>평가 도구
Azure 파일 동기화를 배포하기 전에 Azure 파일 동기화 평가 도구를 사용하여 시스템과 호환되는지 여부를 평가해야 합니다. 이 도구는 Azure PowerShell cmdlet이며, 지원되지 않는 문자 또는 지원되지 않는 OS 버전과 같은 파일 시스템과 데이터 세트와 관련된 잠재적인 문제를 확인합니다. 이 검사에는 아래에 언급된 기능 전부는 아니지만 대부분이 포함됩니다. 이 섹션의 나머지 부분을 자세히 참조하여 배포가 원활하게 진행되도록 하는 것이 좋습니다. 

#### <a name="download-instructions"></a>다운로드 지침
1. 최신 버전의 PackageManagement와 PowerShellGet이 설치되어 있는지 확인합니다(이 경우 미리 보기 모듈을 설치할 수 있음).
    
    ```powershell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. PowerShell을 다시 시작합니다.
3. 모듈 설치
    
    ```powershell
        Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
    ```

#### <a name="usage"></a>사용 현황  
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
- Windows Server 2012 R2, Windows Server 2016 또는 Windows Server 2019를 실행하는 서버.

    | Version | 지원되는 SKU | 지원되는 배포 옵션 |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter 및 Standard | 전체(UI가 있는 서버) |
    | Windows Server 2016 | Datacenter 및 Standard | 전체(UI가 있는 서버) |
    | Windows Server 2012 R2 | Datacenter 및 Standard | 전체(UI가 있는 서버) |

    이후 버전의 Windows Server는 출시되면 추가될 예정입니다. 이전 버전의 Windows는 사용자의 의견에 따라 추가될 수 있습니다.

    > [!Important]  
    > Windows 업데이트의 최신 업데이트를 사용하여 Azure 파일 동기화와 함께 사용되는 모든 서버를 최신 상태로 유지하는 것이 좋습니다. 

- 2GiB 이상의 메모리가 있는 서버

    > [!Important]  
    > 서버가 동적 메모리를 사용하도록 설정된 가상 머신에서 실행되는 경우 VM을 2,048MiB 이상의 메모리로 구성해야 합니다.
    
- NTFS 파일 시스템으로 포맷되어 로컬로 연결된 볼륨

### <a name="file-system-features"></a>파일 시스템 기능

| 기능 | 상태 지원 | 메모 |
|---------|----------------|-------|
| ACL(액세스 제어 목록) | 완전하게 지원 | Windows ACL은 Azure 파일 동기화에 의해 유지되며 서버 엔드포인트에서 Windows Server에 의해 적용됩니다. 파일이 클라우드에서 직접 액세스될 경우 Azure Files에서 Windows ACL을 (아직) 지원하지 않는 것입니다. |
| 하드 링크 | 생략 | |
| 바로 가기 링크 | 생략 | |
| 탑재 지점 | 부분적으로 지원됨 | 탑재 지점은 서버 엔드포인트의 루트일 수 있지만, 서버 엔드포인트의 네임스페이스에 포함된 경우 건너뜁니다. |
| 분기 동기화 | 생략 | 분산 파일 시스템 DfrsrPrivate 및 DFSRoots 폴더를 예로 들 수 있습니다. |
| 재분석 지점 | 생략 | |
| NTFS 압축 | 완전하게 지원 | |
| 스파스 파일 | 완전하게 지원 | 스파스 파일은 동기화되지만(차단되지 않음) 전체 파일로 클라우드와 동기화됩니다. 클라우드(또는 다른 서버)에서 파일 콘텐츠가 변경될 경우 변경 내용이 다운로드되면 파일은 더 이상 스파스 파일이 아닙니다. |
| ADS(대체 데이터 스트림) | 보존되지만 동기화되지 않음 | 예를 들어, 파일 분류 인프라에서 만들어진 분류 태그는 동기화되지 않습니다. 각 서버 엔드포인트의 파일에 대한 기존 분류 태그는 그대로 유지됩니다. |

> [!Note]  
> NTFS 볼륨만 지원됩니다. ReFS, FAT, FAT32 및 다른 파일 시스템은 지원되지 않습니다.

### <a name="files-skipped"></a>건너뛴 파일

| 파일/폴더 | 참고 |
|-|-|
| Desktop.ini | 시스템에 특정된 파일 |
| ethumbs.db$ | 썸네일의 임시 파일 |
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
**에이전트 버전 5.0.2.0**   
Windows Server 2016 및 Windows Server 2019에서 클라우드 계층화를 사용하면 볼륨의 데이터 중복 제거가 지원됩니다. 클라우드 계층화가 사용되는 볼륨에서 중복 제거를 사용하면 추가 스토리지를 프로비저닝하지 않고도 더 많은 파일을 온-프레미스에 캐시할 수 있습니다.

**Windows Server 2012 R2 또는 이전 에이전트 버전**  
클라우드 계층화를 사용하도록 설정하지 않은 볼륨의 경우 Azure 파일 동기화는 볼륨에 Windows Server 데이터 중복 제거를 사용하도록 지원합니다.

### <a name="distributed-file-system-dfs"></a>분산 파일 시스템(DFS)
Azure 파일 동기화에서는 DFS-N(DFS 네임스페이스) 및 DFS-R(DFS 복제)과의 상호 작용을 지원합니다.

**DFS 네임스페이스(DFS-N)**: DFS-N 서버에서 Azure 파일 동기화가 완전히 지원됩니다. 하나 이상의 DFS-N 멤버에 Azure 파일 동기화 에이전트를 설치하면 서버 엔드포인트 및 클라우드 엔드포인트 간에 데이터를 동기화할 수 있습니다. 자세한 내용은 [DFS 네임스페이스 개요](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)를 참조하세요.
 
**DFS 복제(DFS-R)**: DFS-R 및 Azure 파일 동기화는 모두 복제 솔루션이므로 대부분의 경우, DFS-R을 Azure 파일 동기화로 대체하는 것이 좋습니다. 하지만 DFS-R과 Azure 파일 동기화를 함께 사용해야 하는 몇 가지 시나리오가 있습니다.

- DFS-R 배포에서 Azure 파일 동기화 배포로 마이그레이션하는 중입니다. 자세한 내용은 [DFS 복제(DFS-R) 배포를 Azure 파일 동기화로 마이그레이션](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)을 참조하세요.
- 파일 데이터의 복사본을 필요로 하는 모든 온-프레미스 서버를 인터넷에 직접 연결할 수는 없습니다.
- 분기 서버는 Azure 파일 동기화를 사용할 단일 허브 서버에 데이터를 통합합니다.

Azure 파일 동기화 및 DFS-R을 나란히 사용하려면

1. Azure 파일 동기화 클라우드 계층화는 DFS-R 복제 폴더를 포함하는 볼륨에서 사용하지 않도록 설정해야 합니다.
2. 서버 엔드포인트는 DFS-R 읽기 전용 복제 폴더에 대해 구성할 수 없습니다.

자세한 내용은 [DFS 복제 개요](https://technet.microsoft.com/library/jj127250)를 참조하세요.

### <a name="sysprep"></a>Sysprep
Azure 파일 동기화 에이전트가 설치되어 있는 서버에서 sysprep 사용은 지원되지 않으며 예기치 않은 결과가 발생할 수 있습니다. 에이전트 설치 및 서버 등록은 서버 이미지 배포 및 sysprep 최소 설치 완료 후 발생해야 합니다.

### <a name="windows-search"></a>Windows 검색
클라우드 계층화가 서버 엔드포인트에서 활성화되면 계층화된 파일은 건너뛰고 Windows 검색을 통해 인덱싱되지 않습니다. 계층화되지 않은 파일은 제대로 인덱싱됩니다.

### <a name="antivirus-solutions"></a>바이러스 백신 솔루션
바이러스 백신은 알려진 악성 코드 파일을 검색하는 방식으로 작동하기 때문에 바이러스 백신 제품은 계층화된 파일의 회수를 발생할 수 있습니다. Azure 파일 동기화 에이전트의 버전 4.0 이상에서 계층화된 파일에는 보안 Windows 특성 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS가 설정되어 있습니다. 이 특성이 설정된 파일 읽기를 건너뛰도록(대부분 자동으로 수행) 솔루션을 구성하는 방법을 소프트웨어 공급업체에 문의하세요.

Microsoft의 사내 바이러스 백신 솔루션, Windows Defender 및 SCEP(System Center Endpoint Protection) 모두는 이 특성이 설정된 파일 읽기를 자동으로 건너뜁니다. 이러한 솔루션을 테스트하여 하나의 사소한 문제를 확인했습니다. 즉 기존 동기화 그룹에 서버를 추가하면 800바이트보다 작은 파일이 새 서버에서 회수(다운로드)됩니다. 이러한 파일은 새 서버에 남아 있지만 계층화 크기 요구 사항(64kb 초과)을 충족하지 않으므로 계층화되지 않습니다.

### <a name="backup-solutions"></a>백업 솔루션
바이러스 백신 솔루션과 같은 백업 솔루션은 계층화된 파일이 회수되도록 할 수 있습니다. 온-프레미스 백업 제품을 사용하지 않고 클라우드 백업 솔루션을 사용하여 Azure 파일 공유를 백업하는 것이 좋습니다.

온-프레미스 백업 솔루션을 사용하는 경우 클라우드 계층화가 비활성화된 동기화 그룹의 서버에서 백업을 수행해야 합니다. 복원을 수행할 때 볼륨 수준 또는 파일 수준 복원 옵션을 사용합니다. 파일 수준 복원 옵션을 사용하여 복원된 파일은 동기화 그룹의 모든 엔드포인트에 동기화되고 기존 파일은 백업에서 복원된 버전으로 대체됩니다.  볼륨 수준 복원은 Azure 파일 공유 또는 기타 서버 엔드포인트의 최신 파일 버전을 대체하지 않습니다.

> [!Note]  
> BMR(완전 복구) 복원은 예기치 않은 결과가 발생할 수 있으며 현재 지원되지 않습니다.

> [!Note]  
> VSS 스냅숏([이전 버전] 탭 포함)은 현재 클라우드 계층화를 사용할 수 있는 볼륨에서 지원되지 않습니다. 클라우드 계층화가 사용하도록 설정된 경우 Azure 파일 공유 스냅숏을 사용하여 백업에서 파일을 복원할 수 있습니다.

### <a name="encryption-solutions"></a>암호화 솔루션
암호화 솔루션에 대한 지원은 구현되는 방식에 따라 달라집니다. Azure 파일 동기화와 함께 작동하는 기능

- BitLocker 암호화
- Azure Information Protection, Azure RMS(Rights Management Services) 및 Active Directory RMS

Azure 파일 동기화와 함께 작동하지 않는 기능

- NTFS EFS(암호화 파일 시스템)

일반적으로 Azure 파일 동기화는 BitLocker처럼 파일 시스템 아래에 있는 암호화 솔루션 및 Azure Information Protection과 같은 파일 형식으로 구현된 솔루션과의 상호 운용성을 지원해야 합니다. 파일 시스템 위에 있는 솔루션(예: NTFS EFS)에 대해서는 특별한 상호 운용성이 설정되지 않았습니다.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>다른 HSM(계층적 저장소 관리) 솔루션
다른 HSM 솔루션은 Azure 파일 동기화와 함께 사용하면 안 됩니다.

## <a name="region-availability"></a>지역 가용성
Azure 파일 동기화는 다음 지역에서만 사용할 수 있습니다.

| 지역 | 데이터 센터 위치 |
|--------|---------------------|
| 오스트레일리아 동부 | 뉴사우스웨일스 |
| 오스트레일리아 남동부 | 빅토리아 |
| 브라질 남부 | 상파울루 Paolo 상태 |
| 캐나다 중부 | 토론토 |
| 캐나다 동부 | 퀘벡 시티 |
| 중앙 인도 | 푸네 |
| 미국 중부 | 아이오와 |
| 동아시아 | 홍콩 특별 행정구 |
| 미국 동부 | 버지니아 |
| 미국 동부2 | 버지니아 |
| 한국 중부| 서울 |
| 한국 남부| 부산 |
| 일본 동부 | 도쿄, 사이타마 |
| 일본 서부 | 오사카 |
| 미국 중북부 | 일리노이 |
| 유럽 북부 | 아일랜드 |
| 미국 중남부 | 텍사스 |
| 인도 남부 | 첸나이 |
| 동남아시아 | 싱가포르 |
| 영국 남부 | 런던 |
| 영국 서부 | 카디프 |
| 서유럽 | 네덜란드 |
| 미국 서부 | 캘리포니아 |

Azure 파일 동기화에서는 Storage 동기화 서비스와 동일한 지역에 있는 Azure 파일 공유와의 동기화만 지원합니다.

### <a name="azure-disaster-recovery"></a>Azure 재해 복구
Azure 지역의 손실에 대해 보호하려면 Azure 파일 동기화가 [GRS(지역 중복 저장소) 중복](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 옵션과 통합해야 합니다. GRS 저장소는 일반적으로 상호 작용하는 주 지역의 저장소 및 쌍을 이루는 보조 지역의 저장소 간에 비동기 블록 복제를 사용하여 작동합니다. Azure 지역이 일시적 또는 영구적으로 오프라인으로 전환하게 하는 재해 발생 시 Microsoft는 쌍을 이루는 하위 지역에 스토리지를 장애 조치(Failover)합니다. 

> [!Warning]  
> Azure 파일 공유를 GRS 스토리지 계정에 클라우드 엔드포인트로 사용하는 경우 스토리지 계정의 장애 조치(Failover)를 시작하지 않아야 합니다. 이러한 계정을 장애 조치(failover)하면 동기화가 더 이상 진행되지 않고, 새로 계층화된 파일의 경우 예기치 않은 데이터 손실이 발생할 수도 있습니다. Azure 지역의 손실의 경우 Microsoft는 Azure 파일 동기화와 호환되는 방식으로 스토리지 계정의 장애 조치(Failover)를 트리거합니다.

지역 중복 저장소 및 Azure 파일 동기화 간의 장애 조치 통합을 지원하려면 모든 Azure 파일 동기화 지역이 저장소로 사용되는 보조 지역과 일치하는 해당 보조 지역과 쌍을 이루어야 합니다. 이러한 쌍은 다음과 같습니다.

| 주 지역      | 쌍을 이루는 지역      |
|---------------------|--------------------|
| 오스트레일리아 동부      | 오스트레일리아 남동부 |
| 오스트레일리아 남동부 | 오스트레일리아 동부     |
| 캐나다 중부      | 캐나다 동부        |
| 캐나다 동부         | 캐나다 중부     |
| 중앙 인도       | 인도 남부        |
| 미국 중부          | 미국 동부 2          |
| 동아시아           | 동남아시아     |
| 미국 동부             | 미국 서부            |
| 미국 동부 2           | 미국 중부         |
| 한국 중부       | 한국 남부        |
| 한국 남부         | 한국 중부      |
| 유럽 북부        | 서유럽        |
| 미국 중북부    | 미국 중남부   |
| 인도 남부         | 중앙 인도      |
| 동남아시아      | 동아시아          |
| 영국 남부            | 영국 서부            |
| 영국 서부             | 영국 남부           |
| 서유럽         | 유럽 북부       |
| 미국 서부             | 미국 동부            |

## <a name="azure-file-sync-agent-update-policy"></a>Azure 파일 동기화 에이전트 업데이트 정책
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>다음 단계
* [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [Azure Files 배포](storage-files-deployment-guide.md)
* [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
* [Azure 파일 동기화 모니터링](storage-sync-files-monitoring.md)
