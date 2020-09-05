---
title: 파일 및 폴더의 느린 백업 문제 해결
description: Azure Backup 성능 문제의 원인을 진단하는 데 도움이 되는 문제 해결 지침을 제공합니다.
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: b3f2ac343ef4a703f347ec8a57f242a636bb32d2
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88824018"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Azure Backup에서 파일 및 폴더의 느린 백업 문제 해결

이 문서에서는 Azure Backup을 사용할 때 파일 및 폴더의 느린 백업 성능 문제에 대한 원인을 진단하는 데 도움이 되는 문제 해결 지침을 제공합니다. Azure Backup 에이전트를 사용하여 파일을 백업하는 경우 백업 프로세스가 예상보다 오래 걸릴 수 있습니다. 이러한 지연은 다음 중 하나 이상에 의해 발생할 수 있습니다.

* [백업 중인 컴퓨터에서 성능 병목 현상이 발생하는 경우](#cause1)
* [다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup 프로세스를 방해하는 경우](#cause2)
* [Backup 에이전트가 Azure VM(가상 머신)에서 실행 중인 경우](#cause3)  
* [많은 수(수백만 개)의 파일을 백업하는 경우](#cause4)

문제 해결을 시작하기 전에 [최신 Azure Backup 에이전트](https://aka.ms/azurebackup_agent)를 다운로드하여 설치하는 것이 좋습니다. Microsoft는 다양한 문제를 해결하고, 기능을 추가하고, 성능을 향상시키기 위해 Backup 에이전트를 자주 업데이트합니다.

일반적인 구성 문제가 발생하지 않도록 [Azure Backup 서비스 - FAQ](backup-azure-backup-faq.md) 를 검토하는 것이 좋습니다.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>원인: 최적화되지 않은 모드에서 실행 중인 백업 작업

* MARS 에이전트는 USN(업데이트 시퀀스 번호) 변경 저널을 사용하여 **최적화된 모드**에서 백업 작업을 실행하거나 전체 볼륨 검색을 통해 디렉터리 또는 파일의 변경 내용을 확인하여 **최적화되지 않은 모드**에서 백업 작업을 실행할 수 있습니다.
* 최적화되지 않은 모드에서는 에이전트가 볼륨의 각 파일을 검색하고 메타데이터와 비교하여 변경된 파일을 확인해야 하므로 속도가 느립니다.
* 이를 확인하려면 아래와 같이 MARS 에이전트 콘솔에서 **작업 세부 정보**를 열고 상태가 **데이터를 전송하는 중(최적화되지 않았으므로 시간이 더 걸릴 수 있음)** 인지 확인합니다.

    ![최적화되지 않은 모드에서 실행](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* 다음 조건에서는 백업 작업이 최적화되지 않은 모드에서 실행될 수 있습니다.
  * 첫 번째 백업(초기 복제라고도 함)은 항상 최적화되지 않은 모드에서 실행됩니다.
  * 이전 백업 작업이 실패하면 예약된 다음 백업 작업이 최적화되지 않은 상태로 실행됩니다.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>원인: 컴퓨터의 성능 병목 현상

백업 중인 컴퓨터의 병목 현상으로 인해 지연이 발생할 수 있습니다. 예를 들어 컴퓨터가 디스크에 읽거나 쓰는 기능 또는 네트워크를 통해 데이터를 전송하는 데 사용할 수 있는 대역폭으로 인해 병목 현상이 발생할 수 있습니다.

Windows에서는 이러한 병목 상태를 검색할 수 있는 [성능 모니터](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481)(Perfmon)라는 기본 제공 도구를 제공합니다.

다음은 최적의 백업을 위해 병목 현상을 진단하는 데 도움이 될 수 있는 몇 가지 성능 카운터 및 범위입니다.

| 카운터 | 상태 |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |<li> 100% 유휴 상태에서 50% 유휴 = 정상</br><li> 49% 유휴 ~ 20% 유휴 상태 = 경고 또는 모니터</br><li> 19% 유휴 상태에서 0% 유휴 = 위험 또는 사양을 벗어남 |
| 논리 디스크(실제 디스크)--%평균 Disk Sec Read or Write |<li> 0.001 밀리초 ~ 0.015 밀리초 = 정상</br><li> 0.015 밀리초 ~ 0.025 밀리초 = 경고 또는 모니터</br><li> 0.026 밀리초 이상 = 위험 또는 사양 부족 |
| 논리 디스크(실제 디스크)--현재 디스크 큐 길이(모든 인스턴스) |6분이 넘는 시간 동안 80개 요청 |
| Memory--Pool Non Paged Bytes |<li> 사용 된 풀의 60% 미만 = 정상<br><li> 사용 된 풀의 61% ~ 80% = 경고 또는 모니터</br><li> 사용 된 풀 수가 80% 보다 큼 = 위험 또는 사양을 벗어남 |
| Memory--Pool Paged Bytes |<li> 사용 된 풀의 60% 미만 = 정상</br><li> 사용 된 풀의 61% ~ 80% = 경고 또는 모니터</br><li> 사용 된 풀 수가 80% 보다 큼 = 위험 또는 사양을 벗어남 |
| Memory--Available Megabytes |<li> 사용 가능한 메모리의 50% 이상 = 정상</br><li> 사용 가능한 메모리의 25% = 모니터</br><li>사용 가능한 메모리의 10% = 경고</br><li> 100 MB 미만 또는 사용 가능한 사용 가능한 메모리의 5% = 위험 또는 사양 부족 |
| Processor--\%%Processor Time(모든 인스턴스) |<li> 60% 미만의 사용 됨 = 정상</br><li> 61% ~ 90% 사용 됨 = 모니터 또는 주의</br><li> 91% ~ 100% 사용 됨 = 위험 |

> [!NOTE]
> 인프라가 가능한 원인으로 격리되면 더 나은 성능을 위해 주기적으로 디스크의 조각 모음을 수행하는 것이 좋습니다.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>원인: 다른 프로세스 또는 바이러스 백신 소프트웨어가 Azure Backup을 방해함

Windows 시스템 내의 다른 프로세스가 Azure Backup 에이전트 프로세스의 성능에 부정적인 영향을 미치는 경우가 확인되었습니다. 예를 들어 Azure Backup 에이전트 및 다른 프로그램을 함께 사용하여 데이터를 백업하거나 바이러스 백신 소프트웨어가 실행되고 있고 백업할 파일이 잠겨 있는 경우 파일을 여러 번 잠그면서 경합이 발생할 수 있습니다. 이 경우 백업이 실패하거나 작업이 예상보다 오래 걸릴 수 있습니다.

이 시나리오에서 가장 좋은 방법은 다른 백업 프로그램을 해제하고 Azure Backup 에이전트의 백업 시간이 달라지는지 확인하는 것입니다. 일반적으로 여러 개의 백업이 동시에 실행되지 않도록 하는 것만으로도 백업이 서로 영향을 주지 않도록 할 수 있습니다.

바이러스 백신 프로그램의 경우 다음 파일 및 위치를 제외하는 것이 좋습니다.

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe를 프로세스로 제외합니다.
* C:\Program Files\Microsoft Azure Recovery Services Agent\ 폴더를 제외합니다.
* 스크래치 위치를 제외합니다(표준 위치를 사용하지 않는 경우).

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>원인: Azure 가상 머신에서 실행되는 Backup 에이전트

VM에서 백업 에이전트를 실행하는 경우 실제 컴퓨터에서 실행할 때보다 성능이 더 느려집니다. 이것은 IOPS 제한 때문으로 예상됩니다.  그러나 백업되는 데이터 드라이브를 Azure Premium Storage로 전환하여 성능을 최적화할 수 있습니다. 이 문제를 해결하기 위해 계속 노력 중이며, 이후 릴리스에서 수정 사항을 사용할 수 있게 될 것입니다.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>원인: 많은 수(수백만 개)의 파일 백업

이동하는 데이터의 양이 많을수록 시간이 더 오래 걸립니다. 경우에 따라 백업 시간은 데이터의 크기뿐만 아니라 파일 또는 폴더 수와도 관련이 있습니다. 특히 수백만 개의 작은 파일(몇 바이트에서 몇 킬로바이트 크기)을 백업하는 경우 더욱 그렇습니다.

이 동작은 데이터를 백업하고 Azure로 이동하는 동안 Azure에서 동시에 파일 카탈로그를 만들기 때문에 발생합니다. 간혹 카탈로그 작업이 예상보다 오래 걸릴 수도 있습니다.

다음 표시기는 병목 상태를 이해하고 그에 따라 다음 단계에서 작업을 진행하는 데 유용할 수 있습니다.

* **UI에 데이터 전송 진행률이 표시됨**. 데이터가 여전히 전송 중입니다. 네트워크 대역폭 또는 데이터 크기로 인해 지연이 발생할 수 있습니다.
* **UI에 데이터 전송 진행률이 표시 되지 않습니다**. C:\Program Files\Microsoft Azure Recovery Services Agent\Temp에 있는 로그를 연 다음, 로그에서 FileProvider::EndData 항목을 확인합니다. 이 항목은 데이터 전송이 완료되었으며 카탈로그 작업이 진행 중임을 나타냅니다. 백업 작업을 취소하지 말고, 대신 카탈로그 작업가 끝날 때까지 좀 더 기다리세요. 문제가 지속되면 [Azure 지원](https://portal.azure.com/#create/Microsoft.Support)에 문의하세요.

대량 디스크를 백업 하려는 경우 첫 번째 백업 (초기 복제)에 [Azure Data Box](./offline-backup-azure-data-box.md) 를 사용 하는 것이 좋습니다.  Data Box를 사용할 수 없는 경우 네트워크를 통해 장기간 데이터를 전송하는 동안 사용자 환경에서 일시적인 네트워크 문제로 인한 백업 오류가 발생할 수 있습니다.  이러한 오류를 방지하려면 초기 백업에 폴더 몇 개를 추가하고 모든 폴더가 Azure에 성공적으로 백업될 때까지 점진적으로 폴더를 추가하면 됩니다.  이후 증분 백업은 상대적으로 더 빠르게 수행됩니다.

## <a name="next-steps"></a>다음 단계

* [파일 및 폴더 백업에 대한 일반적인 질문](backup-azure-file-folder-backup-faq.md)
