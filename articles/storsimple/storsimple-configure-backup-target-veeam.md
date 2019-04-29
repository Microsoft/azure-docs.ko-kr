---
title: Veeam에서 백업 대상으로 StorSimple 8000 시리즈 구성 | Microsoft Docs
description: Veeam을 사용한 StorSimple 백업 대상 구성에 대해 설명합니다.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: e7659cca9081834d41f64ef0fbd8ea3686044bfd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633968"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>Veeam에서 백업 대상으로 StorSimple 구성

## <a name="overview"></a>개요

Azure StorSimple은 Microsoft의 하이브리드 클라우드 저장소 솔루션입니다. StorSimple은 Azure Storage 계정을 온-프레미스 솔루션의 확장으로 사용하고 온-프레미스 스토리지 및 클라우드 스토리지 전반에 걸쳐 데이터를 자동으로 계층화하여 지수 데이터 증가의 복잡성을 해결합니다.

이 문서에서는 Veeam과 StorSimple의 통합 및 두 솔루션을 통합하는 모범 사례에 대해 설명합니다. 또한 StorSimple과 가장 잘 통합되도록 Veeam을 설정하는 방법에 대한 권장 사항을 제공합니다. 개별 백업 요구 사항 및 SLA(서비스 수준 약정)를 충족하도록 Veeam을 설정하는 가장 좋은 방법은 Veeam 모범 사례, 백업 설계자 및 관리자에게 맡기는 것입니다.

이 문서는 구성 단계와 주요 개념을 설명하지만 단계별 구성 또는 설치 가이드가 아닙니다. 기본 구성 요소 및 인프라가 작업 순서대로 배치되고 설명하는 개념을 지원할 준비가 되었다고 가정합니다.

### <a name="who-should-read-this"></a>이 문서의 대상

이 문서의 정보는 저장소, Windows Server 2012 R2, 이더넷, 클라우드 서비스 및 Veeam에 대한 지식이 있는 백업 관리자, 저장소 관리자 및 저장소 설계자에게 가장 유용합니다.

### <a name="supported-versions"></a>지원되는 버전

-   Veeam 9 이상 버전
-   [StorSimple 업데이트 3 이상 버전](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>StorSimple이 백업 대상인 이유는 무엇인가요?

StorSimple이 백업 대상으로 적합한 이유는 다음과 같습니다.

-   변경 없이 빠른 백업 대상으로 사용할 백업 애플리케이션용 표준 로컬 저장소를 제공합니다. StorSimple을 사용하여 최근 백업을 빠르게 복원할 수도 있습니다.
-   클라우드 계층화는 비용 효율적인 Azure Storage를 사용할 수 있도록 Azure 클라우드 스토리지 계정과 완벽하게 통합됩니다.
-   재해 복구를 위해 오프사이트 저장소를 자동으로 제공합니다.


## <a name="key-concepts"></a>주요 개념

모든 저장소 솔루션과 마찬가지로 중요한 성공 요소로서 솔루션의 저장소 성능, SLA, 변경률 및 용량 증가 요구 사항을 신중하게 평가해야 합니다. 기본 아이디어는 해당 작업을 수행하기 위해 클라우드 계층, 액세스 시간 및 처리량을 클라우드에 도입하여 StorSimple의 기능에서 중심적인 역할을 수행하는 것입니다.

StorSimple은 잘 정의된 데이터(핫 데이터)의 작업 집합에서 작동하는 애플리케이션에 대한 저장소를 제공하도록 설계되었습니다. 이 모델에서 데이터의 작업 집합은 로컬 계층에 저장되고 데이터의 나머지 비작업/콜드/보관 집합은 클라우드에서 계층화됩니다. 다음 그림에서 이 모델을 나타냅니다. 평평한 녹색선은 StorSimple 디바이스의 로컬 계층에 저장된 데이터를 나타냅니다. 빨간색 선은 모든 계층에서 StorSimple 솔루션에 저장된 총 데이터 양을 나타냅니다. 평편한 녹색선과 빨강색 지수 곡선 사이의 간격은 클라우드에 저장된 데이터의 총량을 나타냅니다.

**StorSimple 계층화**
![StorSimple 계층화 다이어그램](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

이 아키텍처를 염두에 두면 StorSimple이 백업 대상으로 작동하는 데 적합하다는 점을 알 수 있습니다. StorSimple을 사용하면 다음 작업을 수행할 수 있습니다.

-   데이터의 로컬 작업 집합에서 가장 빈번하게 복원을 수행합니다.
-   복원이 빈번하게 수행되지 않는 오프사이트 재해 복구 및 오래된 데이터에 대해 클라우드를 사용합니다.

## <a name="storsimple-benefits"></a>StorSimple 이점

StorSimple은 온-프레미스 및 클라우드 저장소에 대한 원활한 액세스를 활용하여 Microsoft Azure와 원활하게 통합된 온-프레미스 솔루션을 제공합니다.

StorSimple은 SSD(Solid-State Device) 및 SAS(Serial-Attached SCSI) 스토리지가 있는 온-프레미스 장치와 Azure Storage 간에 자동 계층화를 사용합니다. 자동 계층화는 자주 액세스하는 데이터를 SSD 및 SAS 계층에서 로컬로 유지하지만, 그렇지 않은 데이터는 Azure Storage로 이동합니다.

StorSimple은 다음과 같은 이점을 제공합니다.

-   전례 없는 중복 제거 수준을 달성하기 위해 클라우드를 사용하는 고유한 중복 제거 및 압축 알고리즘
-   고가용성
-   Azure 지역에서 복제를 사용하여 지역에서 복제
-   Azure 통합
-   클라우드의 데이터 암호화
-   향상된 재해 복구 및 규정 준수

StorSimple은 기본 백업 대상과 보조 백업 대상이라는 두 가지 주요 배포 시나리오를 제공하지만 기본적으로는 일반 블록 저장소 디바이스입니다. StorSimple은 모든 압축 및 중복 제거를 수행합니다. 클라우드와 애플리케이션 및 파일 시스템 간에 데이터를 원활하게 전송하고 검색합니다.

StorSimple에 대한 자세한 내용은 [StorSimple 8000 시리즈: 하이브리드 클라우드 스토리지 솔루션](storsimple-overview.md)을 참조하세요. 또한 [StorSimple 8000 시리즈 기술 사양](storsimple-technical-specifications-and-compliance.md)도 검토할 수 있습니다.

> [!IMPORTANT]
> StorSimple 디바이스를 백업 대상으로 사용하는 것은 StorSimple 8000 업데이트 3 이상 버전에서만 지원됩니다.

## <a name="architecture-overview"></a>아키텍처 개요

다음 표에서는 디바이스 모델-아키텍처 초기 지침을 보여줍니다.

**로컬 및 클라우드 저장소의 StorSimple 용량**

| 저장소 용량 | 8100 | 8600 |
|---|---|---|
| 로컬 저장소 용량 | &lt; 10TiB\*  | &lt; 20TiB\*  |
| 클라우드 저장소 용량 | &gt; 200TiB\* | &gt; 500TiB\* |

\*저장소 크기는 중복 제거 또는 압축을 사용한다고 가정하지 않습니다.

**기본 및 보조 백업의 StorSimple 용량**

| Backup 시나리오  | 로컬 저장소 용량  | 클라우드 저장소 용량  |
|---|---|---|
| 기본 백업  | RPO(복구 지점 목표)를 충족하기 위해 빠른 복구용 로컬 저장소에 최근 백업 저장 | 클라우드 용량에 적합한 Backup 기록(RPO) |
| 보조 백업 | 클라우드 용량에 백업 데이터의 보조 복사본을 저장할 수 있습니다.  | N/A  |

## <a name="storsimple-as-a-primary-backup-target"></a>기본 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 백업을 위한 유일한 리포지토리로서 백업 애플리케이션에 제공됩니다. 다음 그림에서는 모든 백업이 백업 및 복원을 위해 계층화된 StorSimple 볼륨을 사용하는 솔루션 아키텍처를 보여 줍니다.

![기본 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>기본 대상 백업 논리 단계

1.  백업 서버에서 대상 백업 에이전트에 연결하고, 백업 에이전트는 백업 서버에 데이터를 전송합니다.
2.  백업 서버에서 계층화된 StorSimple 볼륨에 데이터를 씁니다.
3.  백업 서버에서 카탈로그 데이터베이스를 업데이트한 다음 백업 작업을 완료합니다.
4.  스냅숏 스크립트에서 StorSimple 클라우드 스냅숏 관리자를 트리거합니다(시작 또는 삭제).
5.  백업 서버에서 보존 정책에 따라 만료된 백업을 삭제합니다.

### <a name="primary-target-restore-logical-steps"></a>기본 대상 복원 논리 단계

1.  백업 서버에서 저장소 리포지토리로부터 해당 데이터를 복원하기 시작합니다.
2.  백업 에이전트에서 백업 서버로부터 데이터를 수신합니다.
3.  백업 서버에서 복원 작업을 완료합니다.

## <a name="storsimple-as-a-secondary-backup-target"></a>보조 백업 대상인 StorSimple

이 시나리오에서 StorSimple 볼륨은 주로 장기 보존 또는 보관에 사용됩니다.

다음 그림에서는 초기 백업 및 복원이 고성능 볼륨을 대상으로 하는 아키텍처를 보여 줍니다. 이러한 백업은 설정된 일정에 따라 계층화된 StorSimple 볼륨에 복사 및 보관됩니다.

보존 정책, 용량 및 성능 요구 사항을 처리할 수 있도록 고성능 볼륨의 크기를 조정하는 것이 중요합니다.

![보조 백업 대상 논리 다이어그램인 StorSimple](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>보조 대상 백업 논리 단계

1.  백업 서버에서 대상 백업 에이전트에 연결하고, 백업 에이전트는 백업 서버에 데이터를 전송합니다.
2.  백업 서버에서 고성능 저장소에 데이터를 씁니다.
3.  백업 서버에서 카탈로그 데이터베이스를 업데이트한 다음 백업 작업을 완료합니다.
4.  백업 서버에서 보존 정책에 따라 StorSimple에 백업을 복사합니다.
5.  스냅숏 스크립트에서 StorSimple 클라우드 스냅숏 관리자를 트리거합니다(시작 또는 삭제).
6.  백업 서버에서 보존 정책에 따라 만료된 백업을 삭제합니다.

### <a name="secondary-target-restore-logical-steps"></a>보조 대상 복원 논리 단계

1.  백업 서버에서 저장소 리포지토리로부터 해당 데이터를 복원하기 시작합니다.
2.  백업 에이전트에서 백업 서버로부터 데이터를 수신합니다.
3.  백업 서버에서 복원 작업을 완료합니다.

## <a name="deploy-the-solution"></a>솔루션 배포

솔루션을 배포하려면 다음 세 단계가 필요합니다.

1. 네트워크 인프라를 준비합니다.
2. 백업 대상으로 StorSimple 디바이스를 배포합니다.
3. Veeam를 배포합니다.

각 단계에 대해 다음 섹션에서 자세히 설명합니다.

### <a name="set-up-the-network"></a>네트워크 설정

StorSimple은 Azure 클라우드와 통합된 솔루션이기 때문에 StorSimple에는 Azure 클라우드에 대한 활성 연결이 필요합니다. 이 연결은 클라우드 스냅숏, 데이터 관리 및 메타데이터 전송과 같은 작업에서 오래되고 자주 액세스되지 않는 데이터를 Azure 클라우드 저장소에 계층화하는 데 사용됩니다.

솔루션을 최적으로 수행하려면 다음과 같은 네트워킹 모범 사례를 따르는 것이 좋습니다.

-   StorSimple 계층화를 Azure에 연결하는 링크는 대역폭 요구 사항을 충족해야 합니다. 이렇게 하려면 RPO 및 RTO(복구 시간 목표) SLA와 일치하도록 인프라 스위치에 필요한 QoS(서비스 품질) 수준을 적용합니다.
-   최대 Azure Blob Storage 액세스 대기 시간은 약 80ms여야 합니다.

### <a name="deploy-storsimple"></a>StorSimple 배포

단계별 StorSimple 배포 지침은 [온-프레미스 StorSimple 디바이스 배포](storsimple-deployment-walkthrough-u2.md)를 참조하세요.

### <a name="deploy-veeam"></a>Veeam 배포

Veeam 설치 모범 사례는 [Veeam Backup 및 복제 모범 사례](https://bp.veeam.expert/)(영문)를 참조하고, [Veeam 도움말 센터(기술 문서)](https://www.veeam.com/documentation-guides-datasheets.html)(영문)에서 사용자 가이드를 읽어보세요.

## <a name="set-up-the-solution"></a>솔루션 설정

이 섹션에서는 몇 가지 구성 예를 보여 줍니다. 다음 예제 및 권장 사항에서는 가장 기본적인 구현을 보여 줍니다. 이 구현은 특정 백업 요구 사항에 직접 적용되지 않을 수도 있습니다.

### <a name="set-up-storsimple"></a>StorSimple 설정

| StorSimple 배포 작업  | 추가 설명 |
|---|---|
| 온-프레미스 StorSimple 디바이스를 배포합니다. | 지원되는 버전: 업데이트 3 이상 버전. |
| 백업 대상을 켭니다. | 다음 명령을 사용하여 백업 대상 모드를 설정하거나 해제하고 상태를 가져옵니다. 자세한 내용은 [StorSimple 디바이스에 원격으로 연결](storsimple-remote-connect.md)을 참조하세요.</br> 백업 모드 설정: `Set-HCSBackupApplianceMode -enable` </br> 백업 모드 해제: `Set-HCSBackupApplianceMode -disable` </br> 백업 모드 설정의 현재 상태 가져오기: `Get-HCSBackupApplianceMode` |
| 백업 데이터를 저장하는 볼륨에 대한 일반적인 볼륨 컨테이너를 만듭니다. 볼륨 컨테이너에 있는 모든 데이터의 중복을 제거합니다. | StorSimple 볼륨 컨테이너는 중복 제거 도메인을 정의합니다.  |
| StorSimple 볼륨을 만듭니다. | 볼륨 크기가 클라우드 스냅숏 기간에 영향을 주기 때문에 가능하면 예상되는 사용량에 가까운 크기로 볼륨을 만듭니다. 볼륨 크기를 조정하는 방법에 대한 내용은 [보존 정책](#retention-policies)을 참조하세요.</br> </br> 계층화된 StorSimple 볼륨을 사용하고 **자주 액세스하지 않는 보관 데이터에 이 볼륨 사용** 확인란을 선택합니다. </br> 로컬로 고정된 볼륨만 사용하는 것은 지원되지 않습니다. |
| 모든 백업 대상 볼륨에 대한 고유한 StorSimple 백업 정책을 만듭니다. | StorSimple 백업 정책은 볼륨 일관성 그룹을 정의합니다. |
| 스냅숏이 만료될 때 일정을 사용하지 않도록 설정합니다. | 스냅숏은 후처리 작업으로 트리거됩니다. |

### <a name="set-up-the-host-backup-server-storage"></a>호스트 백업 서버 저장소 설정

다음 지침에 따라 호스트 백업 서버 저장소를 설정합니다.  

- [Windows 디스크 관리]에서 만든 스팬 볼륨은 사용하지 않습니다. 스팬 볼륨은 지원하지 않습니다.
- 64KB 할당 단위 크기의 NTFS를 사용하여 볼륨을 포맷합니다.
- Veeam 서버에 직접 StorSimple 볼륨을 매핑합니다.
    - 물리적 서버에 대한 iSCSI를 사용합니다.


## <a name="best-practices-for-storsimple-and-veeam"></a>StorSimple 및 Veeam에 대한 모범 사례

다음 섹션의 지침에 따라 솔루션을 설정합니다.

### <a name="operating-system-best-practices"></a>운영 체제 모범 사례

- NTFS 파일 시스템에 대한 Windows Server 암호화 및 중복 제거를 사용하지 않도록 설정합니다.
- StorSimple 볼륨에 Windows Server 조각 모음을 사용하지 않도록 설정합니다.
- StorSimple 볼륨에 Windows Server 인덱싱을 사용하지 않도록 설정합니다.
- StorSimple 볼륨에서가 아니라 원본 호스트에서 바이러스 백신 검사를 실행합니다.
- [작업 관리자]에서 기본 [Windows Server 유지 관리](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx)를 해제합니다. 다음 방법 중 하나로 이 작업을 수행합니다.
  - [Windows 작업 Scheduler]에서 [유지 관리 구성 도구]를 해제합니다.
  - Windows Sysinternals에서 [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx)을 다운로드합니다. PsExec을 다운로드한 후 관리자 권한으로 Windows PowerShell을 실행하고 다음을 입력합니다.
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple 모범 사례

-   StorSimple 디바이스가 [업데이트 3 이상](storsimple-install-update-3.md)으로 업데이트되었는지 확인합니다.
-   iSCSI 및 클라우드 트래픽을 격리합니다. StorSimple과 백업 서버 간의 트래픽에 전용 iSCSI 연결을 사용합니다.
-   StorSimple 디바이스가 전용 백업 대상인지 확인합니다. 혼합 워크로드는 RTO 및 RPO에 영향을 주기 때문에 지원하지 않습니다.

### <a name="veeam-best-practices"></a>Veeam 모범 사례

-   Veeam 데이터베이스는 서버에 대해 로컬이어야 하고 StorSimple 볼륨에 상주하지 않아야 합니다.
-   재난 복구를 위해 Veeam 데이터베이스를 StorSimple 볼륨에 백업합니다.
-   이 솔루션에 대한 Veeam 전체 백업과 증분 백업을 지원합니다. 가상 및 차등 백업을 사용하지 않는 것이 좋습니다.
-   Backup 데이터 파일에는 특정 작업에 대한 데이터만 포함되어야 합니다. 예를 들어 다른 작업에 미디어 추가가 허용되지 않습니다.
-   작업 검증을 해제합니다. 필요한 경우 최신 백업 작업 후에 검증을 예약해야 합니다. 이 작업이 백업 창에 주는 영향을 이해하는 것이 중요합니다.
-   미디어 미리 할당을 설정합니다.
-   병렬 처리가 설정되어 있는지 확인합니다.
-   압축을 해제합니다.
-   백업 작업에서 중복 제거를 해제합니다.
-   최적화를 **LAN 대상**으로 설정합니다.
-   **전체 활성 백업 만들기**(2주마다)를 설정합니다.
-   백업 리포지토리에서 **VM별 백업 파일 사용**을 설정합니다.
-   **작업당 여러 업로드 스트림 사용**을 **8**로 설정합니다(최대 16개 허용). StorSimple 디바이스의 CPU 사용률에 따라 이 숫자를 위아래로 조정합니다.

## <a name="retention-policies"></a>보존 정책

가장 일반적인 백업 보존 정책 유형 중 하나는 GFS(Grandfather, Father, and Son) 정책입니다. GFS 정책에서는 증분 백업이 매일 수행되고, 전체 백업은 매주 및 매월 수행됩니다. 이 정책을 적용하면 6개의 계층화된 StorSimple 볼륨이 만들어집니다. 하나의 볼륨에는 매주, 매월 및 매년 전체 백업이 포함됩니다. 나머지 5개의 볼륨에는 매일 증분 백업이 저장됩니다.

다음 예제에서는 GFS 회전을 사용합니다. 이 예제에서는 다음을 가정합니다.

-   중복 제거되지 않거나 압축되지 않은 데이터를 사용합니다.
-   전체 백업은 각각 1TiB입니다.
-   매일 증분 백업은 각각 500GiB입니다.
-   4개의 매주 백업이 1개월 동안 보관됩니다.
-   12개의 매월 백업이 1년 동안 보관됩니다.
-   1개의 매년 백업이 10년 동안 보관됩니다.

앞서의 가정에 따라 매월 및 매년 전체 백업에 대해 26TiB의 계층화된 StorSimple 볼륨을 만듭니다. 매일 증분 백업 각각에 대해 5TiB의 계층화된 StorSimple 볼륨을 만듭니다.

| Backup 유형 보존 | 크기(TiB) | GFS 승수\* | 총 용량(TiB)  |
|---|---|---|---|
| 매주 전체 | 1 | 4  | 4 |
| 매일 증분 | 0.5 | 20(주기는 월별 주 수와 동일함) | 12(추가 할당량의 경우 2) |
| 매월 전체 | 1 | 12 | 12 |
| 매년 전체 | 1  | 10 | 10 |
| GFS 요구 사항 |   | 38 |   |
| 추가 할당량  | 4  |   | 42개의 총 GFS 요구 사항  |

\* GFS 승수는 백업 정책 요구 사항을 충족하기 위해 보호하고 유지해야 하는 복사본의 수입니다.

## <a name="set-up-veeam-storage"></a>Veeam 저장소 설정

### <a name="to-set-up-veeam-storage"></a>Veeam 저장소를 설정하려면

1.  Veeam Backup 및 복제 콘솔의 **리포지토리 도구**에서 **Backup 인프라**로 이동합니다. **Backup 리포지토리**를 마우스 오른쪽 단추로 클릭한 다음 **Backup 리포지토리 추가**를 선택합니다.

    ![Veeam 관리 콘솔 - 백업 리포지토리 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  **새 Backup 리포지토리** 대화 상자에서 리포지토리의 이름과 설명을 입력합니다. **다음**을 선택합니다.

    ![Veeam 관리 콘솔 - 이름 및 설명 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  유형에 대해 **Microsoft Windows 서버**를 선택합니다. Veeam 서버를 선택합니다. **다음**을 선택합니다.

    ![Veeam 관리 콘솔 - 백업 리포지토리의 유형 선택](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  **위치**를 지정하려면 볼륨을 찾아보고 선택합니다. **최대 동시 작업 수 제한:** 확인란을 선택하고 값을 **4**로 설정합니다. 이렇게 하면 각 VM(가상 컴퓨터)이 처리되는 동안 4개의 가상 디스크만 동시에 처리됩니다. **고급** 단추를 선택합니다.

    ![Veeam 관리 콘솔 - 볼륨 선택](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  **저장소 호환성 설정** 대화 상자에서 **VM별 백업 파일 사용** 확인란을 선택합니다.

    ![Veeam 관리 콘솔 - 저장소 호환성 설정](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  **새 Backup 리포지토리** 대화 상자에서 **탑재 서버에서 vPower NFS 서비스 사용(권장)** 확인란을 선택합니다. **다음**을 선택합니다.

    ![Veeam 관리 콘솔 - 백업 리포지토리 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  설정을 검토한 후 **다음**을 선택합니다.

    ![Veeam 관리 콘솔 - 백업 리포지토리 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    리포지토리는 Veeam 서버에 추가됩니다.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>StorSimple을 기본 백업 대상으로 설정

> [!IMPORTANT]
> 클라우드에 계층화된 백업에서 데이터를 복원하는 경우 클라우드 속도로 수행됩니다.

다음 그림에서는 일반 볼륨을 백업 작업에 매핑하는 방식을 보여 줍니다. 이 경우 모든 매주 백업은 토요일 전체 디스크에 매핑되고, 증분 백업은 월요일-금요일 증분 디스크에 매핑됩니다. 모든 백업 및 복원은 계층화된 StorSimple 볼륨에서 수행됩니다.

![기본 백업 대상 구성 논리 다이어그램](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>기본 백업 대상인 StorSimple에 대한 GFS 일정 예

다음은 GFS 회전 일정(4주, 매월 및 매년)의 예입니다.

| 빈도/백업 유형 | 전체 | 증분(1-5일)  |   
|---|---|---|
| 매주(1-4주) | 토요일 | 월요일-금요일 |
| 매월  | 토요일  |   |
| 매년 | 토요일  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Veeam 백업 작업에 StorSimple 볼륨 할당

기본 백업 대상 시나리오의 경우 기본 Veeam StorSimple 볼륨을 사용하여 매일 작업을 만듭니다. 보조 백업 대상 시나리오의 경우 DAS(직접 연결 저장소), NAS(네트워크 연결 저장소) 또는 JBOD(Just a Bunch of Disks) 저장소를 사용하여 매일 작업을 만듭니다.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Veeam 백업 작업에 StorSimple 볼륨을 할당하려면

1.  Veeam Backup 및 복제 콘솔에서 **Backup 및 복제**를 선택합니다. **Backup**을 마우스 오른쪽 단추로 클릭한 다음 환경에 따라 **VMware** 또는 **Hyper-V**를 선택합니다.

    ![Veeam 관리 콘솔, 새 백업 작업](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  **새 Backup 작업** 대화 상자에서 매일 백업 작업의 이름과 설명을 입력합니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  백업할 가상 머신을 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  **프록시 Backup** 및 **Backup 리포지토리**에 대해 원하는 값을 선택합니다. 로컬로 연결된 저장소의 환경에 대한 RPO 및 RTO 정의에 따라 **디스크에 유지할 복원 지점**의 값을 선택합니다. **고급**을 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. **고급 설정** 대화 상자의 **Backup** 탭에서 **증분**을 선택합니다. **정기적으로 전체 가상 백업 만들기** 확인란이 선택 취소되어 있는지 확인합니다. **정기적으로 전체 활성 백업 만들기** 확인란을 선택합니다. **전체 활성 백업**에서 **매주 선택한 요일에** 확인란을 토요일로 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 고급 설정 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. **저장소** 탭에서 **인라인 데이터 중복 제거 사용** 확인란이 선택 취소되어 있는지 확인합니다. **스왑 파일 블록 제외** 및 **삭제된 파일 블록 제외** 확인란을 선택합니다. **압축 수준**을 **없음**으로 설정합니다. 안정된 성능 및 중복 제거를 위해 **저장소 최적화**를 **LAN 대상**으로 설정합니다. **확인**을 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 고급 설정 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Veeam 중복 제거 및 압축 설정에 대한 자세한 내용은 [데이터 압축 및 중복 제거](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html)(영문)를 참조하세요.

7.  **Backup 작업 편집** 대화 상자에서 **애플리케이션 인식 처리 사용**(선택 사항) 확인란을 선택할 수 있습니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 게스트 처리 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  지정할 수 있는 시간에 매일 한 번씩 실행되도록 일정을 설정합니다.

    ![Veeam 관리 콘솔 - 새 백업 작업 일정 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>StorSimple을 보조 백업 대상으로 설정

> [!NOTE]
> 클라우드에 계층화된 백업에서 데이터를 복원하는 경우 클라우드 속도로 수행됩니다.

이 모델에서 임시 캐시의 역할을 하는 StorSimple 이외의 저장소 미디어가 있어야 합니다. 예를 들어 RAID(Redundant Array of Independent Disks) 볼륨을 사용하여 공간, I/O(입출력) 및 대역폭을 수용할 수 있습니다. RAID 5, 50 및 10을 사용하는 것이 좋습니다.

다음 그림에서는 일반적인 단기 보존 로컬(서버 쪽) 볼륨 및 장기 보존 보관 볼륨을 보여 줍니다. 이 시나리오에서 모든 백업은 서버 쪽 로컬 RAID 볼륨에서 실행됩니다. 이러한 백업이 정기적으로 복제되고 보관 볼륨에 보관됩니다. 단기 보존 용량 및 성능 요구 사항을 처리할 수 있도록 서버 쪽 로컬 RAID 볼륨의 크기를 조정하는 것이 중요합니다.

![보조 백업 대상인 StorSimple 논리 다이어그램](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>보조 백업 대상 GFS 예제인 StorSimple

다음 표에서는 로컬 디스크 및 StorSimple 디스크에서 백업을 실행하도록 설정하는 방법을 보여 줍니다. 여기에는 개별 용량 및 전체 용량에 대한 요구 사항이 있습니다.

| Backup 유형 및 보존 | 구성된 저장소 | 크기(TiB) | GFS 승수 | 총 용량\*(TiB) |
|---|---|---|---|---|
| 1주차(전체 및 증분) |로컬 디스크(단기)| 1 | 1 | 1 |
| StorSimple 2-4주 |StorSimple 디스크(장기) | 1 | 4 | 4 |
| 매월 전체 |StorSimple 디스크(장기) | 1 | 12 | 12 |
| 매년 전체 |StorSimple 디스크(장기) | 1 | 1 | 1 |
|GFS 볼륨 크기 요구 사항 |  |  |  | 18*|

\* 총 용량에는 17TiB의 StorSimple 디스크 및 1TiB 로컬 RAID 볼륨이 포함됩니다.


### <a name="gfs-example-schedule"></a>GFS 예제 일정

GFS 회전 매주, 매월 및 매년 일정

| 주 | 전체 | 증분 1일차 | 증분 2일차 | 증분 3일차 | 증분 4일차 | 증분 5일차 |
|---|---|---|---|---|---|---|
| 1주차 | 로컬 RAID 볼륨  | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 | 로컬 RAID 볼륨 |
| 2주차 | StorSimple 2-4주 |   |   |   |   |   |
| 3주차 | StorSimple 2-4주 |   |   |   |   |   |
| 4주차 | StorSimple 2-4주 |   |   |   |   |   |
| 매월 | StorSimple 매월 |   |   |   |   |   |
| 매년 | StorSimple 매년  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Veeam 복사 작업에 StorSimple 볼륨 할당

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Veeam 복사 작업에 StorSimple 볼륨을 할당하려면

1.  Veeam Backup 및 복제 콘솔에서 **Backup 및 복제**를 선택합니다. **Backup**을 마우스 오른쪽 단추로 클릭한 다음 환경에 따라 **VMware** 또는 **Hyper-V**를 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 복사 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  **새 Backup 복사 작업** 대화 상자에서 작업의 이름과 설명을 입력합니다.

    ![Veeam 관리 콘솔 - 새 백업 복사 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  처리할 VM을 선택합니다. [백업에서]를 선택하고 이전에 만든 매일 백업을 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 복사 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  필요한 경우 백업 복사 작업에서 개체를 제외합니다.

5.  백업 저장소를 선택하고 **유지할 복원 지점**의 값을 설정합니다. **보관 목적으로 다음 복원 지점 유지** 확인란을 선택해야 합니다. 백업 빈도를 정의한 다음 **고급**을 선택합니다.

    ![Veeam 관리 콘솔 - 새 백업 복사 작업 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  다음 고급 설정을 지정합니다.

    * **유지 관리** 탭에서 저장소 수준 손상 보호를 해제합니다.

    ![Veeam 관리 콘솔 - 새 백업 복사 작업 고급 설정 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * **저장소** 탭에서 중복 제거 및 압축이 해제되어 있는지 확인합니다.

    ![Veeam 관리 콘솔 - 새 백업 복사 작업 고급 설정 페이지](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  직접적인 데이터 전송임을 지정합니다.

8.  필요에 따라 백업 복사 창 일정을 정의한 다음 마법사를 마칩니다.

자세한 내용은 [백업 복사 작업 만들기](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html)를 참조하세요.

## <a name="storsimple-cloud-snapshots"></a>StorSimple 클라우드 스냅숏

StorSimple 클라우드 스냅숏은 StorSimple 디바이스에 있는 데이터를 보호합니다. 클라우드 스냅숏을 만드는 것은 로컬 백업 테이프를 오프 사이트 시설로 전달하는 것과 같습니다. Azure 지역 중복 저장소를 사용하는 경우 클라우드 스냅숏을 만드는 것은 백업 테이프를 여러 사이트로 전달하는 것과 같습니다. 재해 발생 후 디바이스를 복원해야 하는 경우 다른 StorSimple 디바이스를 온라인 상태로 전환하여 장애 조치를 수행할 수 있습니다. 장애 조치 후에 최근의 클라우드 스냅숏에서 클라우드 속도로 데이터에 액세스할 수 있습니다.

다음 섹션에서는 백업 후처리 중에 StorSimple 클라우드 스냅숏을 시작하고 삭제하는 간단한 스크립트를 만드는 방법에 대해 설명합니다.

> [!NOTE]
> 수동 또는 프로그래밍 방식으로 만든 스냅숏은 StorSimple 스냅숏 만료 정책을 따르지 않습니다. 이러한 스냅숏은 수동 또는 프로그래밍 방식으로 삭제되어야 합니다.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>스크립트를 사용하여 클라우드 스냅숏 시작 및 삭제

> [!NOTE]
> StorSimple 스냅숏을 삭제하기 전에 규정 준수 및 데이터 보존 영향을 신중하게 평가합니다. 사후 백업 스크립트를 실행하는 방법에 대한 자세한 내용은 Veeam 설명서를 참조하세요.


### <a name="backup-lifecycle"></a>Backup 주기

![Backup 주기 다이어그램](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>요구 사항

-   스크립트를 실행하는 서버에는 Azure 클라우드 리소스에 대한 액세스 권한이 있어야 합니다.
-   사용자 계정에는 필요한 사용 권한이 있어야 합니다.
-   StorSimple 볼륨과 연관된 StorSimple 백업 정책은 설정해야 하지만 사용하도록 설정되면 안 됩니다.
-   StorSimple 리소스 이름, 등록 키, 디바이스 이름 및 백업 정책 ID가 필요합니다.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>클라우드 스냅숏을 시작하거나 삭제하려면

1. [Azure PowerShell 설치](/powershell/azure/overview)
2. [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) PowerShell 스크립트를 다운로드 및 설치합니다.
3. 스크립트를 실행하는 서버에서 관리자 권한으로 PowerShell을 실행합니다. `-WhatIf $true`를 포함하는 스크립트를 실행하여 스크립트가 어떻게 변경되는지 확인합니다. 유효성 검사가 완료되면 `-WhatIf $false`를 전달합니다. 아래 명령을 실행합니다.
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. 백업 작업에 스크립트를 추가하려면 Veeam 작업 고급 옵션을 편집합니다.

    ![Veeam 백업 고급 설정 스크립트 탭](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

매일 백업 작업의 끝에서 StorSimple 클라우드 스냅숏 백업 정책을 후처리 스크립트로 실행하는 것이 좋습니다. RPO 및 RTO를 충족할 수 있도록 백업 애플리케이션 환경을 백업 및 복원하는 방법에 대한 자세한 내용은 백업 설계자에게 문의하세요.

## <a name="storsimple-as-a-restore-source"></a>복원 원본인 StorSimple

StorSimple 디바이스에서 복원하면 모든 블록 저장소 디바이스에서 복원하는 것처럼 작동합니다. 클라우드에 계층화된 데이터를 복원하는 경우 복원은 클라우드 속도로 수행됩니다. 로컬 데이터의 경우 복원은 디바이스의 로컬 디스크 속도로 수행됩니다.

Veeam을 사용하면 Veeam 콘솔에 있는 기본 제공 탐색기 보기에서 StorSimple을 통해 빠르고 세분화된 파일 수준 복구를 수행할 수 있습니다. Veeam 탐색기를 사용하여 백업에서 개별 항목(예: 이메일 메시지, Active Directory 개체 또는 SharePoint 항목)을 복구합니다. 온-프레미스 VM을 중지하지 않고 복구를 수행할 수 있습니다. 또한 Azure SQL Database 및 Oracle 데이터베이스에 대한 특정 시점 복구도 수행할 수 있습니다. Veeam 및 StorSimple을 사용하면 Azure에서 항목 수준 복구 프로세스를 빠르고 쉽게 수행할 수 있습니다. 복원을 수행하는 방법에 대한 내용은 다음 Veeam 설명서를 참조하세요.

- [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)용
- [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)용
- [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)용
- [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)용
- [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)용


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple 장애 조치(failover) 및 재해 복구

> [!NOTE]
> 백업 대상 시나리오의 경우 StorSimple 클라우드 어플라이언스는 복원 대상으로 지원되지 않습니다.

재해는 다양한 요인으로 발생할 수 있습니다. 다음 표에서는 일반적인 재해 복구 시나리오를 나열합니다.

| 시나리오 | 영향 | 복구 방법 | 메모 |
|---|---|---|---|
| StorSimple 디바이스 오류 | Backup 및 복원 작업이 중단됩니다. | 실패한 디바이스를 교체하고 [StorSimple 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행합니다. | 디바이스 복구 후에 복원을 수행해야 하는 경우 전체 데이터 작업 집합이 클라우드에서 새 디바이스로 검색됩니다. 모든 작업이 클라우드 속도로 수행됩니다. 인덱스 및 카탈로그 재검색 프로세스로 인해 모든 백업 세트를 검색하고 클라우드 계층에서 로컬 디바이스 계층으로 가져오므로 많은 시간이 소요될 수 있습니다. |
| Veeam 서버 오류 | Backup 및 복원 작업이 중단됩니다. | [Veeam 도움말 센터(기술 문서)](https://www.veeam.com/documentation-guides-datasheets.html)에서 설명한 대로 백업 서버를 다시 빌드하고 데이터베이스 복원을 수행합니다.  | 피해 복구 사이트에서 Veeam 서버를 다시 빌드하거나 복원해야 합니다. 데이터베이스를 가장 최근의 지점으로 복원합니다. 복원된 Veeam 데이터베이스가 최신 백업 작업과 동기화되지 않은 경우 인덱싱 및 카탈로그가 필요합니다. 이 인덱스 및 카탈로그 재검색 프로세스로 인해 모든 백업 세트를 검색하고 클라우드 계층에서 로컬 디바이스 계층으로 가져올 수 있습니다. 그러면 더욱 시간이 많이 걸립니다. |
| 백업 서버와 StorSimple이 모두 손실되는 사이트 오류 | Backup 및 복원 작업이 중단됩니다. | 먼저 StorSimple을 복원한 다음 Veeam을 복원합니다. | 먼저 StorSimple을 복원한 다음 Veeam을 복원합니다. 디바이스 복구 후에 복원을 수행해야 하는 경우 전체 데이터 작업 집합이 클라우드에서 새 디바이스로 검색됩니다. 모든 작업이 클라우드 속도로 수행됩니다. |


## <a name="references"></a>참조

이 문서에서는 다음 문서를 참조했습니다.

- [StorSimple 다중 경로 I/O 설정](storsimple-configure-mpio-windows-server.md)
- [Storage 시나리오: 씬 프로비저닝](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [GPT 드라이브 사용](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [공유 폴더의 섀도 복사본 설정](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>다음 단계

- [백업 세트에서 복원](storsimple-restore-from-backup-set-u2.md)하는 방법에 대해 자세히 알아보세요.
- [디바이스 장애 조치 및 재해 복구](storsimple-device-failover-disaster-recovery.md)를 수행하는 방법에 대해 자세히 알아보세요.
