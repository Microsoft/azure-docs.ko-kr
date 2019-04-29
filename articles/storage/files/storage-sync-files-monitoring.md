---
title: Azure 파일 동기화 모니터링 | Microsoft Docs
description: Azure 파일 동기화를 모니터링하는 방법을 설명합니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: c0f19e3ea4f5952ac96b589fa267a2136c85e4f3
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63759470"
---
# <a name="monitor-azure-file-sync"></a>Azure 파일 동기화 모니터링

Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 유지하면서 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. Azure 파일 동기화는 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환합니다. SMB, NFS 및 FTPS를 포함하여 로컬로 데이터에 액세스하기 위해 Windows Server에서 사용할 수 있는 모든 프로토콜을 사용할 수 있습니다. 전 세계에서 필요한 만큼 많은 캐시를 가질 수 있습니다.

이 문서에서는 Azure portal 및 Windows Server를 사용 하 여 Azure File Sync 배포를 모니터링 하는 방법을 설명 합니다.

다음 모니터링 옵션은 현재 사용할 수 있습니다.

## <a name="azure-portal"></a>Azure portal

Azure Portal에서는 등록된 서버 상태, 서버 엔드포인트 상태(동기화 상태) 및 메트릭을 확인할 수 있습니다.

### <a name="storage-sync-service"></a>저장소 동기화 서비스

등록 된 서버 상태, 서버 끝점 상태 및 메트릭을 보려면 Azure portal에서 저장소 동기화 서비스로 이동 합니다. 등록 된 서버 상태를 볼 수 있습니다 합니다 **등록 된 서버** 블레이드 및 서버 끝점 상태에는 **동기화 그룹** 블레이드입니다.

등록 된 서버 상태:

- 경우는 **등록 된 서버** 상태가 **Online**, 서버 서비스를 사용 하 여 성공적으로 통신 합니다.
- 경우는 **등록 된 서버** 상태가 **오프 라인으로 표시 됩니다**, Storage Sync 모니터 (AzureStorageSyncMonitor.exe) 프로세스 서버에서 실행 되 고 있는지 확인 합니다. 서버 방화벽이 나 프록시 뒤에 있는 경우 참조 [이 문서에서는](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) 방화벽 및 프록시를 구성 하려면.

서버 끝점 상태:

- 포털의 서버 엔드포인트 상태는 서버의 원격 분석 이벤트 로그에 기록되는 동기화 이벤트(ID 9102 및 9302)를 기준으로 합니다. 오류 취소와 같은 일시적인 오류로 인해 실패 하면 동기화 세션 동기화 나타날 수 있습니다 여전히 포털에서 정상으로 현재 동기화 세션 진행 됩니다. 이벤트 ID 9302 파일은 적용 되 고 있는지 확인 됩니다. 자세한 내용은 참조 하세요. [동기화 상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 하 고 [진행 상황을 동기화](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)합니다.
- 동기화 진행 하지는 때문에 동기화 오류를 표시 하는 포털을 참조 합니다 [문제 해결 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 지침에 대 한 합니다.

메트릭:

- 다음 메트릭은 스토리지 동기화 서비스 포털에서 볼 수 있습니다.

  | 메트릭 이름 | 설명 | 블레이드에서 이름 |
  |-|-|-|
  | 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 클라우드 계층화 회수 | 회수되는 데이터 크기 | 등록된 서버 |
  | 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일 수 | 서버 엔드포인트 |
  | 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드) | 동기화 그룹, 서버 엔드포인트 |
  | 서버 온라인 상태 | 서버에서 수신된 하트비트 수 | 등록된 서버 |

- 자세한 내용은 참조 하세요 [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor)합니다.

  > [!Note]  
  > 스토리지 동기화 서비스 포털에서 제공하는 차트의 시간 범위는 24시간입니다. 다른 시간 범위 또는 차원을 보려면 Azure Monitor를 사용하세요.

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)를 사용하여 동기화, 클라우드 계층화 및 서버 연결을 모니터링합니다. Azure 파일 동기화의 메트릭은 기본적으로 사용하도록 설정되며 15분마다 Azure Monitor로 전송됩니다.

Azure Monitor에서 Azure File Sync 메트릭을 보려면를 선택 합니다 **저장소 동기화 서비스** 리소스 종류입니다.

Azure Monitor에서 사용 가능한 Azure 파일 동기화용 메트릭은 다음과 같습니다.

| 메트릭 이름 | 설명 |
|-|-|
| 동기화되는 바이트 수 | 전송되는 데이터 크기(업로드 및 다운로드)입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 클라우드 계층화 회수 | 회수되는 데이터의 크기입니다.<br><br>단위: 바이트<br>집계 유형: 합계<br>해당하는 차원: 서버 이름 |
| 동기화 상태가 아닌 파일 | 동기화할 수 없는 파일의 수입니다.<br><br>단위: 카운트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 동기화된 파일 수 | 전송되는 파일 수(업로드 및 다운로드)<br><br>단위: 카운트<br>집계 유형: 합계<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |
| 서버 온라인 상태 | 서버에서 수신된 하트비트의 수입니다.<br><br>단위: 카운트<br>집계 유형: 최대<br>해당하는 차원: 서버 이름 |
| 동기화 세션 결과 | 동기화 세션 결과입니다(1=정상 완료된 동기화 세션, 0=실패한 동기화 세션).<br><br>단위: 카운트<br>집계 형식: 최대<br>적용 가능한 차원: 서버 엔드포인트 이름, 동기화 방향, 동기화 그룹 이름 |

## <a name="windows-server"></a>Windows Server

Windows Server에서 클라우드 계층화를 등록 된 서버를 볼 수 있으며 상태 동기화 키를 누릅니다.

### <a name="event-logs"></a>이벤트 로그

서버의 원격 분석 이벤트 로그를 사용하여 등록된 서버, 동기화 및 클라우드 계층화 상태를 모니터링합니다. 원격 분석 이벤트 로그 아래의 이벤트 뷰어에 위치한 *Applications and Services\Microsoft\FileSync\Agent*합니다.

동기화 상태:

- 이벤트 ID 9102 동기화 세션이 완료 된 후에 기록 됩니다. 이 이벤트를 사용 하 여 성공적으로 동기화 세션 되는지 확인할 수 (**HResult = 0**) 항목별 있는지 및 동기화 오류가 발생 합니다. 자세한 내용은 참조는 [동기화 상태](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) 및 [항목별 오류](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) 설명서.

  > [!Note]  
  > 세션 실패를 전체 동기화 하거나 0이 아닌 PerItemErrorCount 경우도 있습니다. 그러나 정방향 진행을 여전히 할 및 일부 파일이 성공적으로 동기화 합니다. AppliedFileCount AppliedDirCount, AppliedTombstoneCount와 AppliedSizeBytes 적용 된 필드에이 확인할 수 있습니다. 이러한 필드는 세션의 양을 성공을 알려 줍니다. 행에 실패 하는 여러 동기화 세션을 볼 있고 적용된 카운트를 늘리는, 경우 지원 티켓을 열기 전에 다시 시도 하려면 동기화 시간을 제공 합니다.

- 활성 동기화 세션이 있으면 5~10분마다 이벤트 ID 9302가 기록됩니다. 이 이벤트를 사용 하 여 현재 동기화 세션 진행 하는 경우를 확인 하려면 (**AppliedItemCount > 0**). 동기화 진행 되지 않습니다, 경우에 동기화 세션 해야 결국 실패 하 고는 이벤트 ID 9102 오류로 기록 됩니다. 자세한 내용은 참조는 [동기화 중인 문서의](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)합니다.

등록 된 서버 상태:

- 서버가 서비스에 작업을 쿼리하면 30초마다 이벤트 ID 9301이 기록됩니다. GetNextJob 끝날 경우 **상태 = 0**, 서버는 서비스와 통신할 수 있습니다. 오류가 발생 하 여 GetNextJob 완료 되 면 확인 합니다 [문제 해결 설명서](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) 지침에 대 한 합니다.

클라우드 계층화 상태:

- 서버에서 계층화의 활동을 모니터링 하려면 9016 및 9029 아래의 이벤트 뷰어에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9003를 사용 하 여 *Applications and Services\Microsoft\FileSync\Agent*합니다.

  - 이벤트 ID 9003은 서버 엔드포인트에 대한 오류 분포를 제공합니다. 예를 들면 다음과 같습니다. ErrorCode 및 총 오류 수입니다. 오류 코드 별로 하나의 이벤트가 기록 됩니다.
  - 이벤트 ID 9016은 볼륨에 대한 고스팅 결과를 제공합니다. 예를 들면 다음과 같습니다. 사용 가능한 공간 (%) 세션에서 고스팅 파일의 수 이며, ghost를 실패 한 파일의 수입니다.
  - 이벤트 ID 9029는 서버 엔드포인트의 고스팅 세션 정보를 제공합니다. 예를 들면 다음과 같습니다. 세션에서 계층화 된 파일 수가 세션에서 시도 하는 파일 수 및 파일 수가 이미 계층화 합니다.
  
- 회수 서버에서의 활동을 모니터링 하려면 사용 9006, 9009 및 9059 아래의 이벤트 뷰어에 있는 원격 분석 이벤트 로그에서 이벤트 ID 9005 *Applications and Services\Microsoft\FileSync\Agent*합니다.

  - 9005 이벤트 ID는 서버 엔드포인트에 대한 회수 안정성을 제공합니다. 예를 들면 다음과 같습니다. 총 고유 파일에 액세스할 수 및 총 실패 한 액세스를 사용 하 여 고유한 파일 수입니다.
  - 이벤트 ID 9006은 서버 엔드포인트에 대한 회수 오류 분포를 제공합니다. 예를 들면 다음과 같습니다. 총 실패 한 요청 및 오류 코드입니다. 오류 코드 별로 하나의 이벤트가 기록 됩니다.
  - 이벤트 ID 9009는 서버 엔드포인트의 회수 세션 정보를 제공합니다. 예를 들면 다음과 같습니다. DurationSeconds CountFilesRecallSucceeded, 하며 CountFilesRecallFailed 합니다.
  - 이벤트 ID 9059는 서버 엔드포인트의 애플리케이션 회수 분포를 제공합니다. 예를 들면 다음과 같습니다. ShareId, 응용 프로그램 이름 및 TotalEgressNetworkBytes 합니다.

### <a name="performance-counters"></a>성능 카운터

동기화 활동을 모니터링할 서버에서 Azure 파일 동기화 성능 카운터를 사용합니다.

서버에서 Azure File Sync 성능 카운터를 보려면 성능 모니터 (Perfmon.exe)를 엽니다. 아래에 카운터를 찾을 수 있습니다 합니다 **AFS 바이트가 전송** 하 고 **AFS 동기화 작업** 개체입니다.

성능 모니터에서 사용 가능한 Azure 파일 동기화용 성능 카운터는 다음과 같습니다.

| 성능 개체\카운터 이름 | 설명 |
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
