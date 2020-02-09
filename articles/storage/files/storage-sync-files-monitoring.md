---
title: Azure 파일 동기화 모니터링 | Microsoft Docs
description: Azure 파일 동기화를 모니터링하는 방법을 설명합니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ac09f9b59bc6f47adc9311cc910352c1a0d73b5d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699282"
---
# <a name="monitor-azure-file-sync"></a>Azure 파일 동기화 모니터링

Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure Monitor, Storage 동기화 서비스 및 Windows Server를 사용 하 여 Azure File Sync 배포를 모니터링 하는 방법을 설명 합니다.

현재 사용할 수 있는 모니터링 옵션은 다음과 같습니다.

## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 를 사용 하 여 메트릭을 확인 하 고 동기화, 클라우드 계층화 및 서버 연결에 대 한 경고를 구성 합니다.  

### <a name="metrics"></a>metrics

Azure 파일 동기화의 메트릭은 기본적으로 사용하도록 설정되며 15분마다 Azure Monitor로 전송됩니다.

Azure Monitor에서 Azure File Sync 메트릭을 보려면 **저장소 동기화 서비스** 리소스 종류를 선택 합니다.

Azure Monitor에서 사용 가능한 Azure 파일 동기화용 메트릭은 다음과 같습니다.

| 메트릭 이름 | 설명 |
|-|-|
| 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드)입니다.<br><br>단위: 바이트<br>집계 유형: Sum<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 클라우드 계층화 회수 | 회수되는 데이터의 크기입니다.<br><br>**참고**: 이 메트릭은 나중에 제거 될 예정입니다. 클라우드 계층화 회수 크기 메트릭을 사용 하 여 회수 된 데이터의 크기를 모니터링 합니다.<br><br>단위: 바이트<br>집계 유형: Sum<br>해당하는 차원: 서버 이름 |
| 클라우드 계층화 회수 크기 | 회수되는 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: Sum<br>해당하는 차원: 서버 이름, 동기화 그룹 이름 |
| 응용 프로그램별 클라우드 계층화 회수 크기 | 응용 프로그램에서 회수 한 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: Sum<br>해당하는 차원: 응용 프로그램 이름, 서버 이름, 동기화 그룹 이름 |
| 클라우드 계층화 회수 처리량 | 데이터 회수 처리량의 크기입니다.<br><br>단위: 바이트<br>집계 유형: Sum<br>해당하는 차원: 서버 이름, 동기화 그룹 이름 |
| 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일의 수입니다.<br><br>단위: 개수<br>집계 유형: Sum<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드)<br><br>단위: 개수<br>집계 유형: Sum<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 서버 온라인 상태 | 서버에서 수신된 하트비트의 수입니다.<br><br>단위: 개수<br>집계 유형: 최대값<br>해당하는 차원: 서버 이름 |
| 동기화 세션 결과 | 동기화 세션 결과입니다(1=정상 완료된 동기화 세션, 0=실패한 동기화 세션).<br><br>단위: 개수<br>집계 형식: 최대값<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |

### <a name="alerts"></a>,

Azure Monitor에서 경고를 구성 하려면 Storage 동기화 서비스를 선택 하 고 경고에 사용할 [Azure File Sync 메트릭을](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) 선택 합니다.  

다음 표에서는 모니터링할 몇 가지 예제 시나리오와 해당 경고에 사용할 적절 한 메트릭을 보여 줍니다.

| 시나리오 | 경고에 사용할 메트릭입니다. |
|-|-|
| 포털의 서버 엔드포인트 상태 = 오류 | 동기화 세션 결과 |
| 파일이 서버 또는 클라우드 엔드포인트과 동기화 되지 않습니다. | 동기화 상태가 아닌 파일 |
| 등록 된 서버가 저장소 동기화 서비스와 통신 하지 못함 | 서버 온라인 상태 |
| 클라우드 계층화 회수 크기가 하루에 500GiB을 초과 했습니다.  | 클라우드 계층화 회수 크기 |

Azure Monitor에서 경고를 구성 하는 방법에 대 한 자세한 내용은 [Microsoft Azure의 경고 개요]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)를 참조 하세요.

## <a name="storage-sync-service"></a>스토리지 동기화 서비스

등록 된 서버 상태, 서버 엔드포인트 상태 및 메트릭을 보려면 Azure Portal의 저장소 동기화 서비스로 이동 합니다. 등록 된 **서버 블레이드 및** **동기화 그룹** 블레이드에서 서버 엔드포인트 상태에서 등록 된 서버 상태를 볼 수 있습니다.

### <a name="registered-server-health"></a>등록 된 서버 상태

- 등록 된 **서버** 상태가 **온라인**인 경우 서버는 서비스와 성공적으로 통신 하 고 있는 것입니다.
- 등록 된 **서버** 상태가 **오프 라인으로 표시**되는 경우 서버에서 저장소 동기화 모니터 (AzureStorageSyncMonitor .exe) 프로세스가 실행 중인지 확인 합니다. 서버가 방화벽이 나 프록시 뒤에 있는 경우 [이 문서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) 를 참조 하 여 방화벽 및 프록시를 구성 합니다.

### <a name="server-endpoint-health"></a>서버 엔드포인트 상태

- 포털의 서버 엔드포인트 상태는 서버의 원격 분석 이벤트 로그에 기록되는 동기화 이벤트(ID 9102 및 9302)를 기준으로 합니다. 오류 취소와 같은 일시적인 오류로 인해 동기화 세션이 실패 한 경우 현재 동기화 세션이 진행 되는 동안에도 여전히 포털에서 동기화가 정상적으로 나타날 수 있습니다. 이벤트 ID 9302은 파일이 적용 되 고 있는지 여부를 확인 하는 데 사용 됩니다. 자세한 내용은 [동기화 상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 및 [동기화 진행률](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)을 참조 하세요.
- 동기화가 진행 되 고 있지 않으므로 포털에서 동기화 오류가 표시 되는 경우 [문제 해결 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 에서 지침을 참조 하세요.

### <a name="metric-charts"></a>메트릭 차트

- 저장소 동기화 서비스 포털에서 다음과 같은 메트릭 차트를 볼 수 있습니다.

  | 메트릭 이름 | Description | 블레이드 이름 |
  |-|-|-|
  | 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 클라우드 계층화 회수 | 회수되는 데이터 크기 | 등록된 서버 |
  | 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일 수 | 서버 엔드포인트 |
  | 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 서버 온라인 상태 | 서버에서 수신된 하트비트 수 | 등록된 서버 |

- 자세한 내용은 [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)를 참조 하세요.

  > [!Note]  
  > 스토리지 동기화 서비스 포털에서 제공하는 차트의 시간 범위는 24시간입니다. 다른 시간 범위 또는 차원을 보려면 Azure Monitor를 사용하세요.

## <a name="windows-server"></a>Windows Server

Windows Server에서 클라우드 계층화, 등록 된 서버 및 동기화 상태를 볼 수 있습니다.

### <a name="event-logs"></a>이벤트 로그

서버의 원격 분석 이벤트 로그를 사용하여 등록된 서버, 동기화 및 클라우드 계층화 상태를 모니터링합니다. 원격 분석 이벤트 로그는 *응용 프로그램 및 Services\Microsoft\FileSync\Agent*아래 이벤트 뷰어에 있습니다.

동기화 상태:

- 이벤트 ID 9102은 동기화 세션이 완료 된 후에 기록 됩니다. 이 이벤트를 사용 하 여 동기화 세션의 성공 여부 (**HResult = 0**) 및 항목당 동기화 오류가 있는지 여부를 확인 합니다. 자세한 내용은 [동기화 상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 및 [항목 별 오류](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 설명서를 참조 하세요.

  > [!Note]  
  > 동기화 세션이 전반적으로 실패 하거나 0이 아닌 PerItemErrorCount을 포함 하는 경우도 있습니다. 그러나 계속 해 서 진행 하 고 일부 파일은 성공적으로 동기화 됩니다. AppliedFileCount, AppliedDirCount, AppliedTombstoneCount 및 AppliedSizeBytes와 같은 적용 된 필드에서이를 확인할 수 있습니다. 이러한 필드는 성공한 세션의 양을 알려줍니다. 한 행에서 여러 동기화 세션이 실패 하 고 적용 되는 개수가 늘어나면 동기화 시간을 제공 하 여 지원 티켓을 열기 전에 다시 시도 하세요.

- 활성 동기화 세션이 있으면 5~10분마다 이벤트 ID 9302가 기록됩니다. 이 이벤트를 사용 하 여 현재 동기화 세션이 진행 되 고 있는지 여부를 확인할 수 있습니다 (**AppliedItemCount > 0**). 동기화가 진행 되 고 있지 않으면 동기화 세션이 실패 하 고 이벤트 ID 9102이 오류로 기록 됩니다. 자세한 내용은 [동기화 진행률 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)를 참조 하세요.

등록 된 서버 상태:

- 서버가 서비스에 작업을 쿼리하면 30초마다 이벤트 ID 9301이 기록됩니다. GetNextJob가 **status = 0**으로 완료 되 면 서버는 서비스와 통신할 수 있습니다. 오류가 발생 하 여 GetNextJob이 완료 되 면 [문제 해결 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 에서 지침을 확인 합니다.

클라우드 계층화 상태:

- 서버에서 계층화 작업을 모니터링 하려면 *응용 프로그램 및 Services\Microsoft\FileSync\Agent*아래 이벤트 뷰어에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003, 9016 및 9029를 사용 합니다.

  - 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 예를 들어: 총 오류 수 및 ErrorCode 오류 코드 당 하나의 이벤트가 기록 됩니다.
  - 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 예: 사용 가능한 공간 (%)은 세션에서 고스트 된 파일 수와 고스트에 실패 한 파일 수입니다.
  - 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 예: 세션에서 시도 된 파일 수, 세션에서 계층화 된 파일 수 및 이미 계층화 된 파일 수입니다.
  
- 서버에서 회수 활동을 모니터링 하려면 원격 분석 이벤트 로그에서 이벤트 ID 9005, 9006, 9009 및 9059을 사용 합니다 .이 로그는 *응용 프로그램 및 Services\Microsoft\FileSync\Agent*아래 이벤트 뷰어에 있습니다.

  - 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 예를 들어: 액세스 한 총 고유 파일 및 실패 한 액세스의 총 고유 파일.
  - 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 예: 실패 한 총 요청 및 ErrorCode 오류 코드 당 하나의 이벤트가 기록 됩니다.
  - 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. 예를 들어: DurationSeconds, CountFilesRecallSucceeded 및 Countfilesrecallsucceeded.
  - 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. 예를 들어: ShareId, 응용 프로그램 이름, TotalEgressNetworkBytes.

### <a name="performance-counters"></a>성능 카운터

동기화 활동을 모니터링할 서버에서 Azure 파일 동기화 성능 카운터를 사용합니다.

서버에서 Azure File Sync 성능 카운터를 보려면 성능 모니터 (Perfmon.exe)를 엽니다. 이 카운터는 **Afs 바이트 전송** 및 **afs 동기화 작업** 개체 아래에서 찾을 수 있습니다.

성능 모니터에서 사용 가능한 Azure 파일 동기화용 성능 카운터는 다음과 같습니다.

| 성능 개체\카운터 이름 | Description |
|-|-|
| AFS Bytes Transferred\Downloaded Bytes/sec | 초당 다운로드한 바이트 수입니다. |
| AFS Bytes Transferred\Uploaded Bytes/sec | 초당 업로드한 바이트 수입니다. |
| AFS Bytes Transferred\Total Bytes/sec | 초당 총 바이트 수(업로드/다운로드)입니다. |
| AFS Sync Operations\Downloaded Sync Files/sec | 초당 다운로드한 파일 수입니다. |
| AFS Sync Operations\Uploaded Sync Files/sec | 초당 업로드한 파일 수입니다. |
| AFS Sync Operations\Total Sync File Operations/sec | 동기화된 파일의 총 수(업로드/다운로드)입니다. |

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포에 대한 계획](storage-sync-files-planning.md)
- [방화벽 및 프록시 설정 고려](storage-sync-files-firewall-and-proxy.md)
- [Azure 파일 동기화 배포](storage-sync-files-deployment-guide.md)
- [Azure 파일 동기화 문제 해결](storage-sync-files-troubleshoot.md)
- [Azure Files 질문과 대답](storage-files-faq.md)
