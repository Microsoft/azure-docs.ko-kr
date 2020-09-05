---
title: Azure Files 성능 문제 해결 가이드
description: Azure 파일 공유의 알려진 성능 문제를 해결 합니다. 이러한 문제가 발생할 경우 잠재적 원인과 관련 해결 방법을 검색 합니다.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: fe1460d4353addff1b8e3095cfe06c1fcb3b7bd0
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88782373"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Azure Files 성능 문제 해결

이 문서에서는 Azure 파일 공유와 관련 된 몇 가지 일반적인 문제를 나열 합니다. 이러한 문제가 발생 하는 경우 잠재적 원인과 해결 방법을 제공 합니다.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>대기 시간이 짧고 처리량이 높고 일반 성능 문제가 발생 합니다.

### <a name="cause-1-share-was-throttled"></a>원인 1: 공유가 제한 됨

파일 공유에 대 한 IOPS, 수신 또는 송신 제한에 도달 하면 요청이 제한 됩니다. 표준 및 프리미엄 파일 공유에 대 한 한도를 이해 하려면 [파일 공유 및 파일 배율 목표](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#file-share-and-file-scale-targets)를 참조 하세요.

공유를 제한 하 고 있는지 확인 하려면 포털에서 Azure 메트릭을 활용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** 를 선택한 다음 **메트릭을**검색 합니다.

1. **메트릭**을 선택합니다.

1. 저장소 계정을 리소스로 선택 합니다.

1. 메트릭 네임 스페이스로 **파일** 을 선택 합니다.

1. **트랜잭션을** 메트릭으로 선택 합니다.

1. **Responsetype** 에 대 한 필터를 추가 하 고 요청에 **SuccessWithThrottling** (SMB 용) 또는 **ClientThrottlingError** (REST)의 응답 코드가 있는지 확인 합니다.

![프리미엄 파일 공유 메트릭 옵션](media/storage-troubleshooting-premium-fileshares/metrics.png)

> [!NOTE]
> 파일 공유를 제한 하는 경우 경고를 수신 하려면 [파일 공유를 제한 하는 경우 경고를 만드는 방법](#how-to-create-an-alert-if-a-file-share-is-throttled)을 참조 하세요.

### <a name="solution"></a>솔루션

- 표준 파일 공유를 사용 하는 경우 저장소 계정에서 [대량 파일 공유](https://docs.microsoft.com/azure/storage/files/storage-files-how-to-create-large-file-share?tabs=azure-portal) 를 사용 하도록 설정 합니다. 대량 파일 공유는 공유 당 최대 1만 IOPS를 지원 합니다.
- 프리미엄 파일 공유를 사용 하는 경우 프로 비전 된 파일 공유 크기를 늘려 IOPS 제한을 늘립니다. 자세한 내용은 Azure Files 계획 가이드의 [premium 파일 공유에 대 한 프로 비전 이해](https://docs.microsoft.com/azure/storage/files/storage-files-planning#understanding-provisioning-for-premium-file-shares) 섹션을 참조 하십시오.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>원인 2: 메타 데이터/네임 스페이스 작업량이 많은 작업

대부분의 요청이 메타 데이터 중심 (예: createfile/system.windows.forms.openfiledialog.openfile/closefile/queryinfo/queryinfo) 이면 읽기/쓰기 작업과 비교할 때 대기 시간이 더 나빠질 수 있습니다.

대부분의 요청이 메타 데이터 중심 인지 확인 하기 위해 위와 동일한 단계를 사용할 수 있습니다. **Responsetype**에 대 한 필터를 추가 하는 대신 **API 이름**에 대 한 필터를 추가 합니다.

![메트릭의 API 이름 필터링](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>해결 방법

- 메타 데이터 작업의 수를 줄이도록 응용 프로그램을 수정할 수 있는지 확인 합니다.
- 파일 공유에 VHD를 추가 하 고 클라이언트에서 SMB를 통해 VHD를 탑재 하 여 데이터에 대 한 파일 작업을 수행 합니다. 이 방법은 단일 작성기 및 다중 판독기 시나리오에서 작동 하며 메타 데이터 작업을 로컬으로 허용 하 여 로컬 직접 연결 된 저장소와 비슷한 성능을 제공 합니다.

### <a name="cause-3-single-threaded-application"></a>원인 3: 단일 스레드 응용 프로그램

고객이 사용 하는 응용 프로그램이 단일 스레드 인 경우 프로 비전 된 공유 크기에 따라 허용 되는 최대 크기 보다 훨씬 낮은 IOPS/처리량이 발생할 수 있습니다.

### <a name="solution"></a>솔루션

- 스레드 수를 늘려 응용 프로그램 병렬 처리를 늘립니다.
- 병렬 처리를 사용할 수 있는 응용 프로그램으로 전환 합니다. 예를 들어 복사 작업의 경우 고객은 Windows 클라이언트의 AzCopy 또는 RoboCopy를 사용 하거나 Linux 클라이언트에서 **parallel** 명령을 사용할 수 있습니다.

## <a name="very-high-latency-for-requests"></a>요청에 대 한 대기 시간 매우 높음

### <a name="cause"></a>원인

클라이언트 VM은 파일 공유와 다른 지역에 있을 수 있습니다.

### <a name="solution"></a>솔루션

- 파일 공유와 동일한 지역에 있는 VM에서 응용 프로그램을 실행 합니다.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>클라이언트에서 네트워크에 의해 지원 되는 최대 처리량을 달성할 수 없습니다.

이에 대 한 한 가지 잠재적인 원인은 SMB 다중 채널 지원 부족입니다. 현재 Azure 파일 공유는 단일 채널만 지원 하므로 클라이언트 VM에서 서버로의 연결은 하나 뿐입니다. 이 단일 연결은 클라이언트 VM의 단일 코어로 해석 VM에서 달성 가능한 최대 처리량은 단일 코어에 의해 바인딩됩니다.

### <a name="workaround"></a>해결 방법

- 코어 크기가 더 큰 VM을 가져오면 처리량을 향상 시킬 수 있습니다.
- 여러 Vm에서 클라이언트 응용 프로그램을 실행 하면 처리량이 증가 합니다.

- 가능한 경우 REST Api를 사용 합니다.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Windows 클라이언트에 비해 Linux 클라이언트의 처리량은 훨씬 낮습니다.

### <a name="cause"></a>원인

Linux에서 SMB 클라이언트를 구현 하는 것과 관련 하 여 알려진 문제입니다.

### <a name="workaround"></a>해결 방법

- 부하를 여러 Vm에 분산 합니다.
- 동일한 VM에서 **nosharesock** 옵션을 사용 하 여 여러 탑재 위치를 사용 하 고 이러한 탑재 지점의 부하를 분산 합니다.
- Linux에서 **nostrictsync** 옵션으로 탑재를 시도 하 여 모든 **fsync** 호출에서 SMB 플러시를 방지 합니다. Azure Files의 경우이 옵션은 데이터 일관성을 방해 하지 않지만 디렉터리 목록에 오래 된 파일 메타 데이터 (**ls-l** 명령)를 발생 시킬 수 있습니다. 파일의 메타 데이터를 직접 쿼리하면 (**stat** 명령) 최신 파일 메타 데이터가 반환 됩니다.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>광범위 한 열기/닫기 작업을 포함 하는 메타 데이터 사용량이 많은 작업에 대 한 대기 시간이 깁니다.

### <a name="cause"></a>원인

디렉터리 임대에 대 한 지원이 부족 합니다.

### <a name="workaround"></a>해결 방법

- 가능 하면 짧은 시간 내에 동일한 디렉터리에 대 한 과도 한 열기/닫기 핸들을 사용 하지 마십시오.
- Linux Vm의 경우 **actimeo = \<sec> ** 를 탑재 옵션으로 지정 하 여 디렉터리 항목 캐시 제한 시간을 늘립니다. 기본적으로 1 초 이므로 3 또는 5와 같은 큰 값이 도움이 될 수 있습니다.
- Linux Vm의 경우 커널을 4.20 이상으로 업그레이드 합니다.

## <a name="low-iops-on-centosrhel"></a>CentOS/RHEL의 낮은 IOPS

### <a name="cause"></a>원인

CentOS/RHEL에서 1 보다 큰 IO 깊이가 지원 되지 않습니다.

### <a name="workaround"></a>해결 방법

- CentOS 8/RHEL 8로 업그레이드 합니다.
- Ubuntu로 변경 합니다.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Linux에서 Azure Files와 서로 파일을 복사하는 속도 느림

Azure Files에 대 한 파일 복사 속도가 느려지는 경우 Linux 문제 해결 가이드의 [linux의 Azure Files에 대 한 저속 파일 복사](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) 섹션을 참조 하세요.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>떨림/톱-IOPS의 이빨 패턴

### <a name="cause"></a>원인

클라이언트 응용 프로그램이 지속적으로 기준선 IOPS를 초과 합니다. 현재, 클라이언트에서 기준선 IOPS를 초과 하는 경우 서비스에 의해 제한 되는 요청 로드의 서비스 측 다듬기는 없습니다. 이렇게 조정 하면 클라이언트에서 떨림/톱 IOPS 패턴이 발생할 수 있습니다. 이 경우 클라이언트에서 달성 한 평균 IOPS는 기준선 IOPS 보다 낮을 수 있습니다.

### <a name="workaround"></a>해결 방법

- 공유가 제한 되지 않도록 클라이언트 응용 프로그램에서 요청 부하를 줄입니다.
- 공유가 제한 되지 않도록 공유의 할당량을 늘립니다.

## <a name="excessive-directoryopendirectoryclose-calls"></a>과도 한 DirectoryOpen/Directoryopen 호출

### <a name="cause"></a>원인

DirectoryOpen/Directoryopen 호출 수가 최상위 API 호출 중에 있고 클라이언트에서 많은 호출을 수행 하지 않을 것으로 생각 되는 경우 Azure 클라이언트 VM에 설치 된 바이러스 백신에 문제가 있을 수 있습니다.

### <a name="workaround"></a>해결 방법

- 이 문제에 대 한 해결 방법은 [Windows 용 4 월 플랫폼 업데이트](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)에서 확인할 수 있습니다.

## <a name="file-creation-is-slower-than-expected"></a>파일 만들기가 예상 보다 느립니다.

### <a name="cause"></a>원인

많은 수의 파일을 만드는 데 사용 하는 워크 로드는 프리미엄 파일 공유와 표준 파일 공유의 성능 간에 상당한 차이를 표시 하지 않습니다.

### <a name="workaround"></a>해결 방법

- 없음

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 또는 Server 2012 r 2의 성능 저하

### <a name="cause"></a>원인

IO를 많이 사용 하는 작업에 대 한 Azure Files 액세스 하는 데 예상 되는 대기 시간

### <a name="workaround"></a>해결 방법

- 사용 가능한 [핫픽스](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)를 설치 합니다.

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>파일 공유를 제한 하는 경우 경고를 만드는 방법

1. **Azure Portal**의 **저장소 계정** 으로 이동 합니다.
2. 모니터링 섹션에서 **경고** 를 클릭 한 다음 **+ 새 경고 규칙**을 클릭 합니다.
3. **리소스 편집**을 클릭 하 고 저장소 계정에 대 한 **파일 리소스 유형을** 선택한 다음 **완료**를 클릭 합니다. 예를 들어 저장소 계정 이름이 contoso 인 경우 contoso/file 리소스를 선택 합니다.
4. 조건 **선택** 을 클릭 하 여 조건을 추가 합니다.
5. 저장소 계정에 대해 지원 되는 신호 목록이 표시 되 면 **트랜잭션** 메트릭을 선택 합니다.
6. **신호 논리 구성** 블레이드에서 **차원 이름** 드롭다운을 클릭 하 고 **응답 유형**을 선택 합니다.
7. **차원 값** 드롭다운을 클릭 하 고 **SuccessWithThrottling** (SMB의 경우) 또는 **ClientThrottlingError** (REST의 경우)를 선택 합니다.

  > [!NOTE]
  > SuccessWithThrottling 또는 ClientThrottlingError 차원 값이 나열 되지 않은 경우 리소스가 제한 되지 않았음을 의미 합니다. 차원 값을 추가 하려면 **차원** 값 드롭다운 옆에 있는 **사용자 지정 값 추가** 를 클릭 하 고 **SuccessWithThrottling** 또는 **ClientThrottlingError**를 입력 한 다음 **확인** 을 클릭 하 고 #7 단계를 반복 합니다.

8. **차원 이름** 드롭다운을 클릭 하 고 **파일 공유**를 선택 합니다.
9. **차원 값** 드롭다운을 클릭 하 고 경고를 표시 하려는 파일 공유를 선택 합니다.

  > [!NOTE]
  > 파일 공유가 표준 파일 공유 인 경우 **모든 현재 및 미래 값**을 선택 합니다. 표준 파일 공유에는 공유 별 메트릭을 사용할 수 없으므로 차원 값 드롭다운에는 파일 공유가 나열 되지 않습니다. 저장소 계정 내에서 파일 공유를 제한 하 고 경고에서 제한 된 파일 공유를 식별 하지 않으면 표준 파일 공유에 대 한 제한 경고가 트리거됩니다. 표준 파일 공유에는 공유 별 메트릭을 사용할 수 없으므로 저장소 계정 마다 하나의 파일 공유를 사용 하는 것이 좋습니다.

10. **경고 매개 변수** (임계값, 연산자, 집계 세분성 및 평가 빈도)를 정의 하 고 **완료**를 클릭 합니다.

  > [!TIP]
  > 정적 임계값을 사용 하는 경우 메트릭 차트는 파일 공유가 현재 제한 되는 경우 적절 한 임계값을 결정 하는 데 도움이 될 수 있습니다. 동적 임계값을 사용 하는 경우 메트릭 차트에는 최근 데이터를 기반으로 계산 된 임계값이 표시 됩니다.

11. **작업 그룹 선택** 을 클릭 하 여 기존 작업 그룹을 선택 하거나 새 작업 그룹을 만들어 경고에 **작업 그룹** (전자 메일, SMS 등)을 추가 합니다.
12. 경고 **규칙 이름**, **설명** 및 **심각도**와 같은 **경고 정보** 를 입력 합니다.
13. 경고 **규칙 만들기** 를 클릭 하 여 경고를 만듭니다.

Azure Monitor에서 경고를 구성 하는 방법에 대 한 자세한 내용은 [Microsoft Azure의 경고 개요]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)를 참조 하세요.

## <a name="see-also"></a>참고 항목
* [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
* [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
* [Azure Files에 대한 FAQ(질문과 대답)](storage-files-faq.md)
