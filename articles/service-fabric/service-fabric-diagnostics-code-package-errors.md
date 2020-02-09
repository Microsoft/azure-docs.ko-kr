---
title: Service Fabric를 사용 하 여 일반적인 코드 패키지 오류 진단
description: Azure Service Fabric에서 일반적인 코드 패키지 오류 문제를 해결 하는 방법을 알아봅니다.
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 344fef70522240da2236a020c96308c472c9c545
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75463103"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Service Fabric를 사용 하 여 일반적인 코드 패키지 오류 진단

이 문서에서는 코드 패키지가 예기치 않게 종료 되는 것을 의미 합니다. 문제 해결 단계와 함께 일반적인 오류 코드의 가능한 원인에 대 한 통찰력을 제공 합니다.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>프로세스나 컨테이너가 예기치 않게 종료 되는 경우는 언제 입니까?

Azure Service Fabric에서 코드 패키지를 시작 하는 요청을 받으면 앱 및 서비스 매니페스트에 설정 된 옵션에 따라 로컬 시스템에서 환경을 준비 하기 시작 합니다. 이러한 준비에는 네트워크 엔드포인트 또는 리소스 예약, 방화벽 규칙 구성 또는 리소스 거 버 넌 스 제약 조건 설정이 포함 될 수 있습니다. 

환경이 제대로 구성 된 후 Service Fabric는 코드 패키지를 가져오려고 시도 합니다. OS 또는 컨테이너 런타임에서 프로세스나 컨테이너가 성공적으로 활성화 되었다고 보고 하는 경우이 단계는 성공으로 간주 됩니다. 정품 인증에 실패 하면 SFX에 다음과 같은 상태 메시지가 표시 됩니다.

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

코드 패키지가 성공적으로 활성화 되 면 Service Fabric는 수명 모니터링을 시작 합니다. 이 시점에서 프로세스나 컨테이너는 여러 가지 이유로 언제 든 지 종료할 수 있습니다. 예를 들어 DLL을 초기화 하지 못했거나 OS에 데스크톱 힙 공간이 부족 한 것일 수 있습니다. 코드 패키지가 종료 되 면 SFX에서 다음 상태 메시지가 표시 됩니다.

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

이 상태 메시지의 종료 코드는 프로세스 또는 컨테이너가 종료 된 이유에 대해 제공 하는 유일한 단서입니다. 모든 수준의 스택에 의해 생성 될 수 있습니다. 예를 들어이 종료 코드는 OS 오류 또는 .NET 문제와 관련이 있거나 코드에서 발생 했을 수 있습니다. 이 문서를 시작 지점으로 사용 하 여 종료 종료 코드의 원본 및 가능한 해결 방법을 진단할 수 있습니다. 그러나 일반적인 시나리오에 대 한 일반적인 해결 방법 이며 표시 되는 오류에는 적용 되지 않을 수 있습니다.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Service Fabric 내 코드 패키지를 종료 하는지 어떻게 알 수 있나요?

Service Fabric은 다양 한 이유로 코드 패키지를 종료 해야 할 수 있습니다. 예를 들어 부하 분산을 위해 다른 노드에 코드 패키지를 추가할 수 있습니다. 다음 표에 종료 코드가 표시 되는 경우 Service Fabric 코드 패키지를 종료 했는지 확인할 수 있습니다.

>[!NOTE]
> 다음 표의 코드가 아닌 종료 코드를 사용 하 여 프로세스나 컨테이너가 종료 되는 경우 Service Fabric는 종료를 담당 하지 않습니다.

종료 코드 | Description
--------- | -----------
7147 | 는 Ctrl + C 신호를 전송 하 여 프로세스나 컨테이너를 정상적으로 종료할 Service Fabric 있음을 나타냅니다.
7148 | Service Fabric 프로세스 또는 컨테이너를 종료 했음을 나타냅니다. 경우에 따라이 오류 코드는 Ctrl + C 신호를 보낸 후 프로세스나 컨테이너가 적절 한 시간 내에 응답 하지 않았고 종료 되어야 함을 나타냅니다.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>기타 일반적인 오류 코드 및 해당 문제 해결 방법

종료 코드 | 16 진수 값 | 간단한 설명 | 근본 원인 | 잠재적 수정
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | 이 오류는 컴퓨터에 데스크톱 힙 공간이 부족 한 경우를 의미 합니다. 이는 특히 노드에서 실행 중인 응용 프로그램에 속하는 많은 프로세스를 포함 하는 경우에 발생할 수 있습니다. | 프로그램이 Ctrl + C 신호에 응답 하도록 빌드되지 않은 경우 클러스터 매니페스트에서 **EnableActivateNoWindow** 설정을 사용 하도록 설정할 수 있습니다. 이 설정을 사용 하면 코드 패키지가 GUI 창 없이 실행 되 고 Ctrl + C 신호를 수신 하지 않습니다. 또한이 작업을 수행 하면 각 프로세스에서 사용 하는 데스크톱 힙 공간의 양이 줄어듭니다. 코드 패키지가 Ctrl + C 신호를 수신 해야 하는 경우 노드의 데스크톱 힙 크기를 늘릴 수 있습니다.
3762504530 | 0xe0434352 | N/A | 이 값은 관리 코드 (즉, .NET)에서 처리 되지 않은 예외에 대 한 오류 코드를 나타냅니다. | 이 종료 코드는 응용 프로그램에서 처리 되지 않은 상태로 유지 되 고 프로세스를 종료 한 예외를 발생 했음을 나타냅니다. 이 오류를 트리거한 항목을 확인 하는 첫 번째 단계로 응용 프로그램의 로그 및 덤프 파일을 디버깅 합니다.

## <a name="next-steps"></a>다음 단계

* [다른 일반적인 시나리오를 진단](service-fabric-diagnostics-common-scenarios.md)하는 방법에 대해 자세히 알아보세요.
* Azure Monitor 로그 및 [Azure Monitor 개요](../operations-management-suite/operations-management-suite-overview.md)를 읽어 제공 되는 내용에 대 한 자세한 개요를 확인 하세요.
* 검색 및 진단에 도움이 되도록 [경고](../log-analytics/log-analytics-alerts.md) 를 Azure Monitor 로그에 대해 자세히 알아보세요.
* Azure Monitor 로그의 일부로 제공 되는 [로그 검색 및 쿼리](../log-analytics/log-analytics-log-searches.md) 기능에 대해 알아봅니다.
