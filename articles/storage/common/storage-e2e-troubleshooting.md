---
title: 진단 및 메시지 분석기를 사용한 Azure Storage 문제 해결 | Microsoft Docs
description: Azure Storage 분석, AzCopy 및 Microsoft Message Analyzer를 사용한 종단 간 문제 해결을 보여 주는 자습서
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f88a560d4fa819a055534530ddc0862e4aa330fe
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098612"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Azure Storage 메트릭 및 로깅, AzCopy 및 Message Analyzer를 사용한 종단 간 문제 해결
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

진단 및 문제 해결은 Microsoft Azure Storage를 사용하는 클라이언트 애플리케이션을 빌드 및 지원하기 위한 핵심 기술입니다. 오류 및 성능 문제의 진단 및 문제 해결은 Azure 애플리케이션의 분산된 특성 때문에 기존 환경에서보다 더욱 복잡할 수 있습니다.

이 자습서에서는 성능에 영향을 줄 수 있는 특정 오류를 확인하는 방법 및 클라이언트 애플리케이션을 최적화하기 위해 Microsoft 및 Azure Storage에서 제공하는 도구를 사용하여 종단 간에서 비롯한 해당 오류의 문제 해결 방법을 보여 줍니다.

이 자습서는 종단 간 문제 해결 시나리오의 실습 탐색을 제공합니다. Azure Storage 애플리케이션의 문제 해결에 대한 심층적인 개념 가이드는 [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)을 참조하세요.

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Azure Storage 애플리케이션 문제 해결 도구
Microsoft Azure Storage를 사용하는 클라이언트 애플리케이션 문제를 해결하기 위해 여러 도구 조합을 사용하여 문제가 발생한 시기 및 문제의 가능한 원인을 확인할 수 있습니다. 이러한 도구로는 다음이 있습니다.

* **Azure Storage 분석**. [Azure Storage 분석](/rest/api/storageservices/Storage-Analytics) 은 Azure Storage에 대한 로깅 및 메트릭을 제공합니다.

  * **Storage 메트릭** 은 Storage 계정에 대한 트랜잭션 메트릭 및 용량 메트릭을 추적합니다. 메트릭을 사용하여 다양한 여러 측정값에 따른 애플리케이션의 성능을 확인할 수 있습니다. 저장소 분석에서 추적하는 메트릭 유형에 대한 자세한 내용은 [저장소 분석 메트릭 테이블 스키마](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) 를 참조하세요.
  * **스토리지 로깅**은 Azure Storage 서비스에 대한 각 요청을 서버 쪽 로그에 기록합니다. 로그는 수행된 작업, 작업의 상태 및 대기 시간 정보를 비롯하여 각 요청의 자세한 데이터를 추적합니다. 저장소 분석에서 로그에 기록한 요청 및 응답 데이터에 대한 자세한 내용은 [저장소 분석 로그 형식](/rest/api/storageservices/Storage-Analytics-Log-Format) 을 참조하세요.

* **Azure Portal**. [Azure Portal](https://portal.azure.com)에서 저장소 계정에 대한 메트릭 및 로깅을 구성할 수 있습니다. 또한 시간이 지남에 따라 애플리케이션이 어떻게 수행되는지 보여 주는 차트 및 그래프를 볼 수 있으며, 애플리케이션이 지정된 메트릭에 대해 예상과 다르게 수행되는 경우 이를 알려 주도록 경고를 구성할 수도 있습니다.

    Azure Portal에서 모니터링 구성에 대한 자세한 내용은 [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md) 을 참조하세요.
* **AzCopy**. Azure Storage의 서버 로그는 Blob으로 저장되므로 AzCopy를 사용하여 해당 로그 Blob을, Microsoft Message Analyzer를 사용한 분석의 로컬 디렉터리로 복사할 수 있습니다. AzCopy에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md) 을 참조하세요.
* **Microsoft Message Analyzer**. Message Analyzer는 로그 파일을 사용하여 로그 데이터를 시각적 형식으로 표시하는 도구입니다. 이 도구를 이용하면 오류 및 성능 문제를 분석하는 데 사용할 수 있는 유용한 집합으로 로그 데이터를 필터링, 검색 및 그룹화할 수 있습니다. Message Analyzer에 대한 자세한 내용은 [Microsoft Message Analyzer 운영 가이드](https://technet.microsoft.com/library/jj649776.aspx)를 참조하세요.

## <a name="about-the-sample-scenario"></a>샘플 시나리오 정보
이 자습서에서는 Azure Storage 메트릭이 Azure Storage를 호출하는 애플리케이션에 대한 낮은 성공률을 나타내는 시나리오를 살펴보겠습니다. 낮은 성공률 메트릭( **Azure Portal** 및 메트릭 테이블에서 [PercentSuccess](https://portal.azure.com) 로 표시됨)은 성공은 했지만 299보다 큰 HTTP 상태 코드를 반환하는 작업을 추적합니다. 서버 쪽 저장소 로그 파일에서 이러한 작업은 트랜잭션 상태 **ClientOtherErrors**로 기록됩니다. 낮은 비율의 성공 메트릭에 대한 자세한 내용은 [메트릭에 PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success)을 참조하세요.

Azure Storage 작업은 299보다 큰 HTTP 상태 코드를 정상적인 기능의 일부로 반환할 수 있습니다. 그러나 일부의 경우에 이러한 오류는 성능 향상을 위해 클라이언트 애플리케이션을 최적화할 수 있음을 나타냅니다.

이 시나리오에서는 100%보다 낮으면 낮은 성공률로 생각합니다. 그러나에 필요에 따라 다른 메트릭 수준을 선택할 수 있습니다. 애플리케이션을 테스트하는 동안 주요 성능 메트릭에 대해 기준 허용 오차를 설정하는 것이 좋습니다. 예를 들어 테스트에 따라 애플리케이션이 90% 또는 85%의 일관된 성공률(백분율)을 유지해야 한다고 결정할 수 있습니다. 메트릭 데이터를 통해 애플리케이션이 해당 수치에서 벗어난 것을 알게 되면 증가를 일으키는 원인을 조사할 수 있습니다.

샘플 시나리오의 경우, 성공률 메트릭을 100% 미만으로 설정하면 로그를 검토하여 메트릭과 상관관계가 있는 오류를 찾아 이를 활용하여 낮은 성공률을 일으키는 원인을 알아냅니다. 구체적으로 400번대 오류를 살펴보겠습니다. 그런 다음 404(찾을 수 없음) 오류를 더욱 자세하게 조사합니다.

### <a name="some-causes-of-400-range-errors"></a>400번대 오류의 몇 가지 원인
아래 예제에서는 Azure Blob Storage에 대한 몇 가지 400번대 요청 오류 및 가능한 해당 원인의 샘플링을 보여 줍니다. 이러한 오류는 물론 300번대 및 500번대 오류로 인해 낮은 성공률이 나타날 수 있습니다.

참고로 아래 목록이 전체 목록은 아닙니다. Azure Storage 일반 오류 및 각 Storage 서비스에 고유한 오류에 대한 자세한 내용은 MSDN에서 [상태 및 오류 코드](https://msdn.microsoft.com/library/azure/dd179382.aspx) 를 참조하세요.

**상태 코드 404(찾을 수 없음) 예제**

Blob 또는 컨테이너를 찾을 수 없어서 컨테이너 또는 Blob에 대한 읽기 작업이 실패할 때 발생합니다.

* 이 요청 이전에 다른 클라이언트가 컨테이너 또는 Blob을 삭제한 경우에 발생합니다.
* 컨테이너 또는 Blob이 있는지를 확인한 이후 컨테이너 또는 Blob을 생성한 API 호출을 사용하는 경우에 발생합니다. CreateIfNotExists API가 컨테이너 또는 Blob이 있는지 확인하기 위해 첫 번째 HEAD 호출을 실행합니다. 그런 다음 컨테이너 또는 Blob이 존재하지 않는 경우 404 오류가 반환되고 컨테이너 또는 Blob을 작성하기 위한 두 번째 PUT 호출이 실행됩니다.

**상태 코드 409(충돌) 예제**

* 해당 이름을 가진 컨테이너 또는 Blob이 있는지 먼저 확인하지 않고 만들기 API를 사용하여 새로운 컨테이너 또는 Blob을 생성하는 경우 발생합니다.
* 컨테이너를 삭제하는데 이 삭제 작업이 완료되기 전에 같은 이름의 새 컨테이너를 생성하려고 시도하는 경우에 발생합니다.
* 컨테이너 또는 Blob에서 임대를 지정하는데 임대가 이미 존재하는 경우에 발생합니다.

**상태 코드 412(전제 조건 실패) 예제**

* 조건부 헤더에서 지정한 조건이 충족되지 않은 경우에 발생합니다.
* 지정된 임대 ID가 컨테이너 또는 Blob의 임대 ID와 일치하지 않은 경우에 발생합니다.

## <a name="generate-log-files-for-analysis"></a>분석에 대한 로그 파일 생성
이 자습서에서는 Message Analyzer를 사용하여 세 가지 유형의 로그 파일로 작업할 수 있지만, 다음과 같이 해당 로그 중 하나를 선택하여 사용할 수 있습니다.

* **서버 로그**, Azure Storage 로깅을 사용하도록 설정한 경우 생성됩니다. 서버 로그에는 Blob, 큐, 테이블, 파일 등의 Azure Storage 서비스 중 하나에 대해 호출된 각 작업에 관한 데이터가 포함됩니다. 서버 로그는 요청 및 응답에 대한 기타 세부 정보뿐만 아니라 어떤 작업이 호출되었는지와 어떤 상태 코드가 반환되었는지도 나타냅니다.
* **.NET 클라이언트 로그**, .NET 애플리케이션 내에서 클라이언트 쪽 로깅을 사용하도록 설정한 경우 생성됩니다. 클라이언트 로그에는 클라이언트가 요청을 준비하고 응답을 받아 처리하는 방법에 대한 자세한 정보가 포함됩니다.
* **HTTP 네트워크 추적 로그**, Azure Storage에 대한 작업을 비롯하여 HTTP/HTTPS 요청 및 응답 데이터에 대한 데이터를 수집합니다. 이 자습서에서는 Message Analyzer를 통해 네트워크 추적을 생성합니다.

### <a name="configure-server-side-logging-and-metrics"></a>서버 쪽 로깅 및 메트릭 구성
먼저 해야 Azure Storage 로깅 및 메트릭을 구성 하려면 데이터를 분석 하는 서비스 쪽에서 할 수 있도록 합니다. [Azure Portal](https://portal.azure.com)을 통해, PowerShell을 사용하여 또는 프로그래밍 방식 등의 다양한 방법으로 로깅 및 메트릭을 구성할 수 있습니다. 참조 [메트릭을 사용 하도록 설정할](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) 하 고 [로깅을 사용 하도록 설정](storage-analytics-logging.md#enable-storage-logging) 로깅 및 메트릭을 구성 하는 방법에 대 한 세부 정보에 대 한 합니다.

### <a name="configure-net-client-side-logging"></a>.NET 클라이언트 쪽 로깅 구성
.NET 애플리케이션에 대해 클라이언트 쪽 로깅을 구성하려면 애플리케이션의 구성 파일(web.config 또는 app.config)에서 .NET 진단을 사용하도록 설정합니다. 자세한 내용은 MSDN에서 [.NET 스토리지 클라이언트 라이브러리를 사용한 클라이언트 쪽 로깅](https://msdn.microsoft.com/library/azure/dn782839.aspx) 및 [Microsoft Azure Storage SDK for Java를 사용한 클라이언트 쪽 로깅](https://msdn.microsoft.com/library/azure/dn782844.aspx)을 참조하세요.

클라이언트 쪽 로그에는 클라이언트가 요청을 준비하고 응답을 받아 처리하는 방법에 대한 자세한 정보가 포함됩니다.

Storage 클라이언트 라이브러리는 애플리케이션의 구성 파일(web.config 또는 app.config)에 지정된 위치에 클라이언트 쪽 로그 데이터를 저장합니다.

### <a name="collect-a-network-trace"></a>네트워크 추적 수집
클라이언트 애플리케이션이 실행되는 동안 Message Analyzer를 사용하여 HTTP/HTTPS 네트워크 추적을 수집할 수 있습니다. Message Analyzer는 백 엔드에서 [Fiddler](https://www.telerik.com/fiddler) 를 사용합니다. 네트워크 추적을 수집하기 전에 다음과 같이 암호화되지 않은 HTTPS 트래픽을 기록하도록 Fiddler를 구성하는 것이 좋습니다.

1. [Fiddler](https://www.telerik.com/download/fiddler)를 설치합니다.
2. Fiddler를 시작합니다.
3. **도구 | Fiddler 옵션**을 선택합니다.
4. 옵션 대화 상자에서 아래와 같이 **HTTPS 연결 캡처** 및 **HTTPS 트래픽 해독**이 모두 선택되어 있는지 확인합니다.

![Fiddler 옵션 구성](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

자습서의 경우 먼저 Message Analyzer에서 네트워크 추적을 수집 및 저장한 다음, 분석 세션을 생성하여 추적 및 로그를 분석합니다. Message Analyzer에서 네트워크 추적을 수집하려면 다음을 수행합니다.

1. Message Analyzer에서 **파일 | 빠른 추적 | 암호화되지 않은 HTTPS**를 선택합니다.
2. 추적이 즉시 시작됩니다. **Stop(중지)** 을 선택하여 추적을 중지한 후 저장소 트래픽만 추적하도록 구성할 수 있습니다.
3. **Edit(편집)** 를 선택하여 추적 세션을 편집할 수 있습니다.
4. **Microsoft-Pef-WebProxy** ETW 공급자의 오른쪽에 있는 **구성** 링크를 선택합니다.
5. **고급 설정** 대화 상자에서 **공급자** 탭을 클릭합니다.
6. **호스트 이름 필터** 필드에서 공백으로 구분하여 저장소 엔드포인트를 지정합니다. 예를 들어 다음과 같이 엔드포인트를 지정할 수 있습니다. `storagesample`를 사용 중인 저장소 계정 이름으로 변경합니다.

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. 대화 상자를 종료하고 **다시 시작**을 클릭하여 적절한 호스트 이름 필터가 있는 추적의 수집을 시작합니다. 그러면 Azure Storage 네트워크 트래픽만 추적에 포함됩니다.

> [!NOTE]
> 네트워크 추적의 수집을 완료한 후 HTTPS 트래픽을 해독하기 위해 Fiddler에서 변경한 설정을 되돌리는 것이 좋습니다. Fiddler의 옵션 대화 상자에서 **HTTPS 연결 캡처** 및 **HTTPS 트래픽 해독** 확인란의 선택을 취소합니다.
>
>

자세한 내용은 Technet의 [네트워크 추적 기능 사용(영문)](https://technet.microsoft.com/library/jj674819.aspx) 을 참조하세요.

## <a name="review-metrics-data-in-the-azure-portal"></a>Azure Portal에서 메트릭 데이터 검토
애플리케이션을 일정 기간 실행하면 [Azure Portal](https://portal.azure.com)에 표시되는 메트릭 차트를 검토하여 서비스가 수행된 방법을 관찰할 수 있습니다.

먼저 Azure Portal의 저장소 계정으로 이동합니다. 기본적으로 **성공 비율**이 포함된 모니터링 차트가 계정 블레이드에 표시됩니다. 이전에 다른 메트릭을 표시하도록 차트를 수정한 경우 **성공 비율** 메트릭을 추가합니다.

이제 추가한 다른 메트릭과 함께 **성공 비율**이 모니터링 차트에 표시됩니다. Message Analyzer에서 로그를 분석하여 조사하는 시나리오에서 성공률이 100% 아래인 몇 가지를 볼 수 있습니다.

메트릭 차트 추가 및 사용자 지정에 대한 자세한 내용은 [메트릭 차트 사용자 지정](storage-monitor-storage-account.md#customize-metrics-charts)을 참조하세요.

> [!NOTE]
> 저장소 메트릭을 사용하도록 설정한 이후 Azure Portal에 메트릭 데이터가 표시되는 데 약간의 시간이 걸릴 수 있습니다. 현재 시간이 경과할 때까지 이전 시간에 대한 시간별 메트릭이 Azure Portal에 표시되지 않기 때문입니다. 현재는 분 메트릭도 Azure Portal에 표시되지 않습니다. 따라서 메트릭을 사용하도록 설정한 시간에 따라 메트릭 데이터가 표시되는 데에는 최대 2시간까지 걸릴 수 있습니다.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>AzCopy를 사용하여 로컬 디렉터리에 서버 로그 복사
Azure Storage는 메트릭이 테이블에 기록되는 동안 서버 로그 데이터를 Blob에 작성합니다. 로그 Blob은 저장소 계정의 잘 알려진 `$logs` 컨테이너에서 사용할 수 있습니다. 로그 Blob은 조사하려는 시간 범위를 쉽게 찾을 수 있도록 연도, 월, 일 및 시간별로 계층적으로 명명됩니다. 예를 들어 `storagesample` 계정에서 2015년 1월 2일, 오전 8-9시에 대한 로그 Blob의 컨테이너는 `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`입니다. 이 컨테이너의 개별 Blob은 `000000.log`부터 시작하여 순차적으로 명명됩니다.

AzCopy 명령줄 도구를 사용하여 이러한 서버 쪽 로그 파일을 로컬 컴퓨터의 원하는 위치에 다운로드할 수 있습니다. 예를 들어 다음 명령을 사용하여 2015년 1월 2일에 발생한 Blob 작업의 로그 파일을 `C:\Temp\Logs\Server` 폴더로 다운로드할 수 있습니다. `<storageaccountname>`은 저장소 계정의 이름으로, `<storageaccountkey>`는 계정 액세스 키로 바꿉니다.

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy는 [Azure 다운로드](https://azure.microsoft.com/downloads/) 페이지에서 다운로드할 수 있습니다. AzCopy 사용에 대한 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)을 참조하세요.

서버 쪽 로그를 다운로드하는 방법에 대한 자세한 내용은 [저장소 로깅 로그 데이터 다운로드](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)를 참조하세요.

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Microsoft Message Analyzer를 사용하여 로그 데이터 분석
Microsoft Message Analyzer는 문제 해결 및 진단 시나리오에서 프로토콜 메시징 트래픽, 이벤트 및 기타 시스템 또는 애플리케이션 메시지를 캡처, 표시 및 분석하는 도구입니다. 또한, Message Analyzer를 통해 로그 및 저장된 추적 파일의 데이터를 로드, 집계 및 분석할 수 있습니다. Message Analyzer에 대한 자세한 내용은 [Microsoft Message Analyzer 운영 가이드(영문)](https://technet.microsoft.com/library/jj649776.aspx)를 참조하세요.

Message Analyzer에는 서버, 클라이언트 및 네트워크 로그를 분석하는 데 도움이 되는 Azure Storage에 대한 자산이 포함되어 있습니다. 이 섹션에서는 이러한 도구를 사용하여 스토리지 로그에서의 낮은 성공 비율 문제를 해결하는 방법에 대해 논의합니다.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Message Analyzer 및 Azure Storage 자산 다운로드 및 설치
1. Microsoft 다운로드 센터에서 [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) 를 다운로드하고 설치 관리자를 실행합니다.
2. Message Analyzer를 시작합니다.
3. **도구** 메뉴에서 **자산 관리자**를 선택합니다. **자산 관리자** 대화 상자에서 **다운로드**를 선택한 다음 **Azure Storage**에 필터링합니다. 아래 그림에 표시된 대로 Azure Storage 자산이 표시됩니다.
4. **Sync All Displayed Items(표시된 모든 항목 동기화)** 를 클릭하여 Azure Storage 자산을 설치합니다. 사용 가능한 자산은 다음과 같습니다.
   * **Azure Storage 색 규칙:** Azure Storage 색 규칙을 통해 색, 텍스트 및 글꼴 스타일을 사용하여 추적에 특정 정보를 포함하는 메시지를 강조 표시하는 특수 필터를 정의할 수 있습니다.
   * **Azure Storage 차트:** Azure Storage 차트는 서버 로그 데이터를 그래프로 표시하는 미리 정의된 차트입니다. 이번에 Azure Storage 차트를 사용하려면 서버 로그를 분석 격자로 로드하기만 하면 됩니다.
   * **Azure Storage 파서:** Azure Storage 파서는 Azure Storage 클라이언트, 서버 및 HTTP 로그를 분석 그리드에 표시하기 위해 구문 분석합니다.
   * **Azure Storage 필터:** Azure Storage 필터는 분석 그리드의 데이터를 쿼리하는 데 사용할 수 있는 미리 정의된 조건입니다.
   * **Azure Storage 보기 레이아웃:** Azure Storage 보기 레이아웃은 분석 그리드의 미리 정의된 열 레이아웃 및 그룹화입니다.
5. 자산을 설치한 후 Message Analyzer를 다시 시작합니다.

![메시지 분석기 자산 관리자](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> 이 자습서의 용도에 맞는 Azure Storage 자산을 모두 설치합니다.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Message Analyzer로 로그 파일 가져오기
Microsoft Message Analyzer에서 분석을 위해 저장된 모든 로그 파일(서버 쪽, 클라이언트 쪽 및 네트워크)을 단일 세션으로 가져올 수 있습니다.

1. Microsoft Message Analyzer에서 **파일** 메뉴의 **새 세션**을 클릭한 후 **빈 세션**을 클릭합니다. **New Session(새 세션)** 대화 상자에서 분석 세션의 이름을 입력합니다. **세션 정보** 패널에서 **파일** 단추를 클릭합니다.
2. Message Analyzer에서 생성한 네트워크 추적 데이터를 로드하려면 **파일 추가**를 클릭하고, 웹 추적 세션의 .matp 파일을 저장한 위치를 찾아 .matp 파일을 선택하고 **열기**를 클릭합니다.
3. 서버 쪽 로그 데이터를 로드하려면 **파일 추가**를 클릭하고, 서버 쪽 로그를 다운로드한 위치를 찾아 분석하려는 시간 범위의 로그 파일을 선택하고 **열기**를 클릭합니다. 그런 다음 **세션 정보** 패널에서 각 서버 쪽 로그 파일의 **텍스트 로그 구성** 드롭다운을 **AzureStorageLog**로 설정하여 Microsoft Message Analyzer가 로그 파일을 올바르게 구문 분석할 수 있도록 합니다.
4. 클라이언트 쪽 로그 데이터를 로드하려면 **파일 추가**를 클릭하고, 클라이언트 쪽 로그를 저장한 위치를 찾아 분석할 로그 파일을 선택하고 **열기**를 클릭합니다. 그런 다음 **세션 정보** 패널에서 각 클라이언트 쪽 로그 파일의 **텍스트 로그 구성** 드롭다운을 **AzureStorageClientDotNetV4**로 설정하여 Microsoft Message Analyzer가 로그 파일을 올바르게 구문 분석할 수 있도록 합니다.
5. **새 세션** 대화 상자에서 **시작**을 클릭하여 로그 데이터를 로드하고 구문 분석합니다. Message Analyzer 분석 그리드에 로그 데이터가 표시됩니다.

아래 그림은 서버, 클라이언트 및 네트워크 추적 로그 파일에 구성된 예제 세션을 보여 줍니다.

![Message Analyzer 세션 구성](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Message Analyzer는 로그 파일을 메모리에 로드합니다. 로그 데이터 집합이 큰 경우 Message Analyzer에서 최상의 성능을 얻으려면 로그를 필터링합니다.

먼저, 검토하고 싶은 시간 프레임을 결정하고 이 시간 프레임을 가능한 한 작게 유지합니다. 대부분의 경우 몇 분 또는 많아야 몇 시간의 기간을 검토합니다. 이러한 요구를 충족할 수 있는 로그의 최소 세트를 가져옵니다.

여전히 로그 데이터의 양이 많으면 해당 데이터를 로드하기 전에 먼저 세션 필터를 지정하여 로그 데이터를 필터링할 수 있습니다. **세션 필터** 상자에서 **라이브러리** 단추를 선택하여 미리 정의된 필터를 선택합니다. 예를 들어 Azure Storage 필터에서 **전역 시간 필터 I**을 선택하여 시간 간격을 필터링합니다. 그런 다음 필터 조건을 편집하여 확인하려는 간격에 대한 시작 및 끝 타임스탬프를 지정할 수 있습니다. 또한, 특정 상태 코드를 필터링할 수 있습니다. 예를 들어 상태 코드가 404인 로그 항목만 로드하도록 선택할 수 있습니다.

로그 데이터를 Microsoft Message Analyzer로 가져오는 방법에 대한 자세한 내용은 TechNet에서 [메시지 데이터 검색(영문)](https://technet.microsoft.com/library/dn772437.aspx) 을 참조하세요.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>클라이언트 요청 ID를 사용하여 로그 파일 데이터의 상관관계 지정
Azure Storage 클라이언트 라이브러리는 모든 요청에 대해 고유한 클라이언트 요청 ID를 자동으로 생성합니다. 이 값은 클라이언트 로그, 서버 로그 및 네트워크 추적에 기록되므로 해당 값을 사용하여 Message Analyzer 내에서 세 로그 모두에 대해 데이터의 상관관계를 지정할 수 있습니다. 클라이언트 요청 ID에 대한 자세한 내용은 [클라이언트 요청 ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) 를 참조하세요.

다음 섹션에서는 미리 구성된 사용자 지정 레이아웃 보기를 사용하여 클라이언트 요청 ID에 따라 데이터의 상관관계를 지정하고 그룹화하는 방법에 관해 설명합니다.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>분석 그리드에 표시할 보기 레이아웃 선택
Message Analyzer의 스토리지 자산에는 다양한 시나리오에서 유용한 그룹화 및 열을 이용하여 데이터를 표시하는 데 사용할 수 있는 미리 구성된 보기인 Azure Storage 보기 레이아웃이 포함되어 있습니다. 또한, 사용자 지정 보기 레이아웃을 생성하고 다시 사용하기 위해 저장할 수도 있습니다.

아래 그림은 도구 모음 리본에서 **보기 레이아웃**을 선택하여 사용할 수 있는 **보기 레이아웃** 메뉴를 보여 줍니다. Azure Storage의 보기 레이아웃은 메뉴의 **Azure Storage** 노드에서 그룹화됩니다. 검색 상자에서 `Azure Storage` 를 검색하여 Azure Storage 보기 레이아웃만 필터링할 수 있습니다. 보기 레이아웃 옆에 있는 별표를 선택하여 즐겨찾기로 만들고 메뉴의 맨 위에 표시되도록 할 수 있습니다.

![보기 레이아웃 메뉴](./media/storage-e2e-troubleshooting/view-layout-menu.png)

먼저 **Grouped by ClientRequestID and Module(ClientRequestID 및 모듈별로 그룹화)** 을 선택합니다. 이 보기 레이아웃은 먼저 세 로그 모두의 로그 데이터를 클라이언트 요청 ID별로 그룹화한 후 원본 로그 파일(또는 Message Analyzer의 **모듈** )별로 그룹화합니다. 이 보기를 사용하여 특정 클라이언트 요청 ID로 드릴다운하고 해당 클라이언트 요청 ID에 대한 세 로그 파일 모두의 데이터를 볼 수 있습니다.

아래 그림은 샘플 로그 데이터에 적용된 이 레이아웃 보기와 표시된 열의 하위 집합을 보여 줍니다. 특정 클라이언트 요청 ID의 레이아웃을 볼 수 있습니다. 분석 그리드에는 클라이언트 로그, 서버 로그 및 네트워크 추적의 데이터가 표시됩니다.

![Azure Storage 보기 레이아웃](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> 다른 로그 파일에는 다른 열이 있으므로 여러 로그 파일의 데이터가 분석 그리드에 표시될 때 일부 열이 지정된 행의 데이터를 포함하지 않을 수 있습니다. 클라이언트 로그 행은 **Timestamp**, **TimeElapsed**, **Source** 및 **Destination** 열의 데이터를 표시하지 않습니다. 이러한 열이 클라이언트 로그에 존재하지 않지만, 네트워크 추적에는 존재하기 때문입니다. 마찬가지로 **Timestamp** 열에는 서버 로그의 타임스탬프 데이터가 표시되지만, 서버 로그의 일부가 아닌 **TimeElapsed**, **Source** 및 **Destination** 열에는 데이터가 표시되지 않습니다.
>
>

Azure Storage 보기 레이아웃을 사용할 뿐만 아니라 고유의 보기 레이아웃을 정의 및 저장할 수도 있습니다. 그룹화 데이터에 대해 원하는 다른 필드를 선택하고 해당 그룹화를 사용자 지정 레이아웃의 일부로 저장할 수도 있습니다.

### <a name="apply-color-rules-to-the-analysis-grid"></a>분석 그리드에 색 규칙 적용
저장소 자산에는 분석 그리드에서 여러 유형의 오류를 식별하는 시각적 수단을 제공하는 색 규칙도 포함되어 있습니다. 미리 정의된 색 규칙이 HTTP 오류에 적용되면 서버 로그 및 네트워크 추적에 대해서만 표시됩니다.

색 규칙을 적용하려면 도구 모음 리본에서 **Color Rules(색 규칙)** 를 선택합니다. 메뉴에서 Azure Storage 색 규칙을 볼 수 있습니다. 자습서의 경우 아래 그림에 표시된 대로 **Client Errors (StatusCode between 400 and 499)[클라이언트 오류(400과 499 사이의 StatusCode)]** 를 선택합니다.

![Azure Storage 보기 레이아웃](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Azure Storage 색 규칙을 사용할 뿐만 아니라 고유의 색 규칙을 정의 및 저장할 수도 있습니다.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>로그 데이터를 그룹화 및 필터링하여 400번대 오류 찾기
다음은 로그 데이터를 그룹화 및 필터링하여 400번대의 모든 오류를 찾습니다.

1. 분석 그리드에서 **StatusCode** 열을 찾아 열 제목을 마우스 오른쪽 단추로 클릭하고 **그룹**을 선택합니다.
2. 그러면 **ClientRequestId** 열이 그룹화됩니다. 이제 분석 그리드의 데이터가 상태 코드 및 클라이언트 요청 ID별로 구성되어 표시됩니다.
3. 표시되지 않은 경우 보기 필터 도구 창을 표시합니다. 도구 모음 리본에서 **도구 창**, **필터 보기**를 차례로 선택합니다.
4. 400번대 오류만 표시되도록 로그 데이터를 필터링하려면 다음과 같은 필터 조건을 **필터 보기** 창에 추가하고 **적용**을 클릭합니다.

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

아래 그림은 이 그룹화 및 필터의 결과를 보여 줍니다. 예를 들어 상태 코드 409의 그룹화 아래에 있는 **ClientRequestID** 필드를 확장하면 해당 상태 코드를 야기한 작업이 표시됩니다.

![Azure Storage 보기 레이아웃](./media/storage-e2e-troubleshooting/400-range-errors1.png)

이 필터를 적용한 이후 클라이언트 로그가 **StatusCode** 열을 포함하지 않기 때문에 클라이언트 로그에서 행이 제외되는 것을 볼 수 있습니다. 먼저 서버 및 네트워크 추적 로그를 검토하여 404 오류를 찾은 후 클라이언트 로그로 돌아가 해당 오류를 야기한 클라이언트 작업을 검사합니다.

> [!NOTE]
> 상태 코드가 null인 로그 항목을 포함하는 필터에 식을 추가한 경우 **StatusCode** 열을 필터링하고 클라이언트 로그를 비롯하여 세 로그 모두의 데이터를 여전히 표시할 수 있습니다. 이 필터 식을 생성하려면 다음을 사용하세요.
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> 이 필터는 클라이언트 로그의 모든 행과 상태 코드가 400보다 큰 서버 로그 및 HTTP 로그의 행만 반환합니다. 클라이언트 요청 ID 및 모듈별로 그룹화한 보기 레이아웃에 이 필터를 적용할 경우 로그 항목을 검색하거나 스크롤하여 세 로그 모두가 표시되는 항목을 찾을 수 있습니다.   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>로그 데이터를 필터링하여 404 오류 찾기
저장소 자산에는 로그 데이터 범위를 좁혀 찾고 있는 오류 또는 추세를 찾는 데 사용할 수 있는 미리 정의된 필터가 포함되어 있습니다. 이제 두 가지 미리 정의된 필터 즉, 404 오류에 대해 서버 및 네트워크 추적 로그를 필터링하는 필터와 지정한 시간 범위에서 데이터를 필터링하는 필터를 적용하겠습니다.

1. 표시되지 않은 경우 보기 필터 도구 창을 표시합니다. 도구 모음 리본에서 **도구 창**, **필터 보기**를 차례로 선택합니다.
2. 보기 필터 창에서 **라이브러리**를 선택하고 `Azure Storage`를 검색하여 Azure Storage 필터를 찾습니다. **모든 로그의 404(찾을 수 없음) 메시지**에 대한 필터를 선택합니다.
3. **라이브러리** 메뉴를 다시 표시하고 **전역 시간 필터**를 찾아 선택합니다.
4. 필터에 있는 타임스탬프를 표시하려는 범위로 편집합니다. 이 작업은 분석할 데이터의 범위를 좁히는 데 도움이 됩니다.
5. 필터는 아래 예제와 같이 표시됩니다. **Apply(적용)** 를 클릭하여 분석 그리드에 필터를 적용합니다.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Azure Storage 보기 레이아웃](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>로그 데이터 분석
데이터를 그룹화하고 필터링했으므로 404 오류를 생성한 개별 요청의 세부 정보를 검사할 수 있습니다. 현재 보기 레이아웃에서 데이터는 클라이언트 요청 ID별로 그룹화된 후 로그 원본별로 그룹화됩니다. StatusCode 필드에 404가 포함된 요청을 필터링하고 있으므로 클라이언트 로그 데이터가 아니라 서버 및 네트워크 추적 데이터만 살펴보겠습니다.

아래 그림은 Blob이 존재하지 않기 때문에 Blob 가져오기 작업에서 404 오류를 생성한 구체적인 요청을 보여 줍니다. 관련 데이터를 표시하기 위해 표준 보기에서 일부 열이 제거되었습니다.

![필터링 된 서버 및 네트워크 추적 로그](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

이제, 이 클라이언트 요청 ID를 클라이언트 로그 데이터와 상호 연결하여 오류가 발생했을 때 클라이언트가 수행한 작업을 알아보겠습니다. 다음과 같이 이 세션의 새 분석 그리드 보기를 표시하여 두 번째 탭에서 열리는 클라이언트 로그 데이터를 볼 수 있습니다.

1. 먼저 **ClientRequestId** 필드의 값을 클립보드로 복사합니다. 행을 선택하고 **ClientRequestId** 필드를 찾아 데이터 값을 마우스 오른쪽 단추로 클릭하고 **'ClientRequestId' 복사**를 선택하여 이 작업을 수행할 수 있습니다.
2. 도구 모음 리본에서 **새 뷰어**를 선택한 후 **분석 그리드**를 선택하여 새 탭을 엽니다. 새 탭에 그룹화, 필터링 또는 색 규칙 없이 로그 파일의 모든 데이터가 표시됩니다.
3. 도구 모음 리본에서 **보기 레이아웃**을 선택한 후 **Azure Storage** 섹션의 **모든 .NET 클라이언트 열**를 선택합니다. 이 보기 레이아웃은 서버 및 네트워크 추적 로그는 물론 클라이언트 로그의 데이터를 보여줍니다. 기본적으로 **MessageNumber** 열을 기준으로 정렬됩니다.
4. 그런 다음 클라이언트 요청 ID의 클라이언트 로그를 검색합니다. 도구 모음 리본에서 **메시지 찾기**를 선택한 후 **찾기** 필드에서 클라이언트 요청 ID에 대한 사용자 지정 필터를 지정합니다. 이 필터에 다음 구문을 사용하여 고유의 클라이언트 요청 ID를 지정합니다.

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer에서 검색 조건이 클라이언트 요청 ID와 일치하는 첫 번째 로그 항목을 찾아 선택합니다. 클라이언트 로그에 각 클라이언트 요청 ID에 대한 여러 항목이 있으므로 모두 함께 쉽게 볼 수 있도록 **ClientRequestId** 필드에서 그룹화하려 할 수 있습니다. 아래 그림은 지정된 클라이언트 요청 ID에 대한 클라이언트 로그의 모든 메시지를 보여 줍니다.

![404 오류를 표시하는 클라이언트 로그 ](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

이러한 두 탭에서 보기 레이아웃에 표시된 데이터를 사용하여 요청 데이터를 분석해 오류를 일으킨 원인을 확인할 수 있습니다. 또한, 이보다 앞선 요청을 살펴보고 이전 이벤트가 404 오류를 발생시킬 수 있었는지 확인할 수도 있습니다. 예를 들어 이 클라이언트 요청 ID에 앞선 클라이언트 로그 항목을 검토하여 Blob이 삭제될 수 있었는지 여부 또는 오류가 컨테이너 또는 Blob에서 CreateIfNotExists API를 호출하는 클라이언트 애플리케이션 때문인지를 확인할 수 있습니다. 클라이언트 로그의 **설명** 필드에서 Blob의 주소를 찾을 수 있습니다. 서버 및 네트워크 추적 로그에서 이 정보는 **요약** 필드에 표시됩니다.

404 오류를 생성한 Blob의 주소를 알고 있다면 추가로 조사할 수 있습니다. 동일한 Blob에서 작업과 연결된 다른 메시지의 로그 항목을 검색하는 경우 클라이언트가 이전에 엔터티를 삭제했는지 확인할 수 있습니다.

## <a name="analyze-other-types-of-storage-errors"></a>다른 유형의 저장소 오류 분석
Message Analyzer를 사용하여 로그 데이터를 분석하는 데 익숙해졌으므로 보기 레이아웃, 색 규칙 및 검색/필터링을 사용하여 다른 유형의 오류를 분석할 수 있습니다. 다음 표는 발생할 수 있는 일부 문제 및 찾는데 사용할 수 있는 필터 조건을 나열합니다. 필터 및 Message Analyzer 필터링 언어를 생성하는 방법에 대한 자세한 내용은 [메시지 데이터 필터링(영문)](https://technet.microsoft.com/library/jj819365.aspx)을 참조하세요.

| 조사하려면... | 필터 식 사용... | 식을 로그(클라이언트, 서버, 네트워크, 모두)에 적용 |
| --- | --- | --- |
| 큐에서 메시지 배달의 예기치 않은 지연 |AzureStorageClientDotNetV4.Description은 "다시 시도 중 작업이 실패 했습니다."를 포함 |클라이언트 |
| PercentThrottlingError에서 HTTP 증가 |HTTP.Response.StatusCode   == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |네트워크 |
| PercentTimeoutError의 증가 |HTTP.Response.StatusCode   == 500 |네트워크 |
| PercentTimeoutError의 증가(모두) |*StatusCode   == 500 |모두 |
| PercentNetworkError의 증가 |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |클라이언트 |
| HTTP 403(사용할 수 없음) 메시지 |HTTP.Response.StatusCode   == 403 |네트워크 |
| HTTP 404(찾을 수 없음) 메시지 |HTTP.Response.StatusCode   == 404 |네트워크 |
| 404(모두) |*StatusCode   == 404 |모두 |
| SAS(공유 액세스 서명) 권한 부여 문제 |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |네트워크 |
| HTTP 409(충돌) 메시지 |HTTP.Response.StatusCode   == 409 |네트워크 |
| 409(모두) |*StatusCode   == 409 |모두 |
| PercentSuccess가 낮게 표시되거나 분석 로그 항목에 트랜잭션 상태가 ClientOtherErrors 상태인 작업이 있음 |AzureStorageLog.RequestStatus ==   "ClientOtherError" |서버 |
| Nagle 경고 |((AzureStorageLog.EndToEndLatencyMS   - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS *   1.5)) 및 (AzureStorageLog.RequestPacketSize <1460) 및 (AzureStorageLog.EndToEndLatencyMS -   AzureStorageLog.ServerLatencyMS >= 200) |서버 |
| 서버 및 네트워크 로그의 시간 범위 |#Timestamp   >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39 |서버, 네트워크 |
| 서버 로그의 시간 범위 |AzureStorageLog.Timestamp   >= 2014-10-20T16:36:38 및 AzureStorageLog.Timestamp <=   2014-10-20T16:36:39 |서버 |

## <a name="next-steps"></a>다음 단계
Azure Storage의 종단 간 시나리오 문제 해결에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Microsoft Azure Storage 모니터링, 진단 및 문제 해결](storage-monitoring-diagnosing-troubleshooting.md)
* [저장소 분석](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Azure Portal에서 저장소 계정 모니터링](storage-monitor-storage-account.md)
* [AzCopy 명령줄 유틸리티로 데이터 전송](storage-use-azcopy.md)
* [Microsoft Message Analyzer 운영 가이드](https://technet.microsoft.com/library/jj649776.aspx)
