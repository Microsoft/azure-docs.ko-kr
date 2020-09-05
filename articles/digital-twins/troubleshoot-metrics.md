---
title: Azure Monitor를 사용 하 여 메트릭 보기
titleSuffix: Azure Digital Twins
description: Azure Monitor에서 Azure Digital Twins 메트릭을 보는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ecd402c30fee63ad594fff5e4fdc3b1610fe7e4e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003884"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Azure Digital Twins 문제 해결: 메트릭

이 문서에서 설명 하는 메트릭은 Azure 구독에서 Azure Digital Twins 리소스의 상태에 대 한 정보를 제공 합니다. Azure Digital Twins 메트릭은 Azure Digital Twins 서비스의 전반적인 상태와 여기에 연결 된 리소스를 평가 하는 데 도움이 됩니다. 이러한 사용자 지향 통계는 azure 지원에 문의 하지 않고도 문제에 대 한 근본 원인 분석을 수행 하는 데 도움이 되는 Azure Digital Twins의 기능을 확인 하는 데 도움이 됩니다.

메트릭은 기본적으로 사용하도록 설정됩니다. [Azure Portal](https://portal.azure.com)에서 Azure Digital twins 메트릭을 볼 수 있습니다.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure Digital Twins 메트릭을 보는 방법

1. Azure Digital Twins 인스턴스를 만듭니다. [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-scripted.md)에서 Azure Digital twins 인스턴스를 설정 하는 방법에 대 한 지침을 찾을 수 있습니다.

2. [Azure Portal](https://portal.azure.com) 에서 Azure Digital twins 인스턴스를 찾습니다. 포털 검색 표시줄에 이름을 입력 하 여 페이지를 열 수 있습니다. 

    인스턴스의 메뉴에서 **메트릭**을 선택 합니다.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Digital Twins의 메트릭 페이지를 보여 주는 스크린샷":::

    이 페이지에는 Azure Digital Twins 인스턴스의 메트릭이 표시 됩니다. 목록에서 보려는 메트릭을 선택 하 여 메트릭의 사용자 지정 보기를 만들 수도 있습니다.
    
3. 메뉴에서 **진단 설정** 을 선택 하 고 **진단 설정 추가**를 선택 하 여 Event Hubs 끝점 또는 Azure Storage 계정에 메트릭 데이터를 보내도록 선택할 수 있습니다.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="진단 설정 페이지 및 추가할 단추를 보여 주는 스크린샷":::

    이 프로세스에 대 한 자세한 내용은 [*문제 해결: 진단 설정*](troubleshoot-diagnostics.md)을 참조 하세요.

4. 메뉴에서 **경고** 를 선택 하 고 **+ 새 경고 규칙**을 선택 하 여 메트릭 데이터에 대 한 경고를 설정 하도록 선택할 수 있습니다.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="경고 페이지 및 추가할 단추를 보여 주는 스크린샷":::

    이 프로세스에 대 한 자세한 내용은 [*문제 해결: 진단 설정*](troubleshoot-diagnostics.md)을 참조 하세요.

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Twins 메트릭 및 사용 방법

Azure Digital Twins는 인스턴스 상태와 연결 된 리소스의 상태에 대 한 개요를 제공 하는 몇 가지 메트릭을 제공 합니다. 여러 메트릭의 정보를 결합 하 여 인스턴스의 상태를 더 크게 파악할 수도 있습니다. 

다음 표에서는 각 Azure Digital Twins 인스턴스가 추적 하는 메트릭과 각 메트릭이 인스턴스의 전체 상태와 어떻게 관련 되는지를 설명 합니다.

#### <a name="api-request-metrics"></a>API 요청 메트릭

API 요청으로 수행 해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| 설명 | 차원 |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 요청 (미리 보기) | 개수 | 합계 | 디지털 쌍 읽기, 쓰기, 삭제 및 쿼리 작업에 대해 생성 된 API 요청의 수입니다. |  인증은 <br>연산의 <br>프로토콜만 <br>상태 코드, <br>상태 코드 클래스, <br>상태 텍스트 |
| ApiRequestsFailureRate | API 요청 실패율 (미리 보기) | 백분율 | 평균 | 디지털 쌍 읽기, 쓰기, 삭제 및 쿼리 작업에 대 한 내부 오류 (500) 응답 코드를 제공 하는 서비스에서 인스턴스에 대해 수신 하는 API 요청의 백분율입니다. | 인증은 <br>연산의 <br>프로토콜만 <br>상태 코드, <br>상태 코드 클래스, <br>상태 텍스트
| ApiRequestsLatency | API 요청 대기 시간 (미리 보기) | 밀리초 | 평균 | API 요청에 대 한 응답 시간입니다. 이는 서비스가 디지털 쌍 읽기, 쓰기, 삭제 및 쿼리 작업에 대 한 성공/실패 결과를 보낼 때까지 Azure Digital Twins에서 요청을 수신 하는 시간을 나타냅니다. | 인증은 <br>연산의 <br>프로토콜 |

#### <a name="billing-metrics"></a>청구 메트릭

청구로 수행 해야 하는 메트릭:

>[!NOTE]
> 미리 보기가 제공 **되는 동안 청구 비용은 0**입니다. 이러한 메트릭은 선택 가능한 목록에 계속 표시 되지만 미리 보기 중에는 적용 되지 않으며 서비스가 미리 보기 이상으로 이동할 때까지 0으로 유지 됩니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| 설명 | 차원 |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | 청구 API 작업 (미리 보기) | 개수 | 합계 | Azure Digital Twins 서비스에 대해 수행 된 모든 API 요청 수에 대 한 청구 메트릭입니다. | 측정기 Id |
| BillingMessagesProcessed | 청구 메시지 처리 됨 (미리 보기) | 개수 | 합계 | Azure Digital Twins에서 외부 끝점으로 전송 된 메시지 수에 대 한 청구 메트릭입니다. | 측정기 Id |
| BillingQueryUnits | 청구 쿼리 단위 (미리 보기) | 개수 | 합계 | 쿼리 실행에 사용 되는 서비스 리소스 사용의 내부적으로 계산 된 측정값 인 쿼리 단위의 수입니다. 쿼리 단위를 측정 하는 데 사용할 수 있는 도우미 API 인 [QueryChargeHelper 클래스](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) 도 있습니다. | 측정기 Id |

#### <a name="ingress-metrics"></a>수신 메트릭

데이터 수신으로 수행 해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| 설명 | 차원 |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | 수신 이벤트 (미리 보기) | 개수 | 합계 | Azure Digital Twins로 들어오는 원격 분석 이벤트의 수입니다. | 결과 |
| IngressEventsFailureRate | 수신 이벤트 실패율 (미리 보기) | 백분율 | 평균 | 서비스에서 내부 오류 (500) 응답 코드를 반환 하는 들어오는 원격 분석 이벤트의 백분율입니다. | 결과 |
| IngressEventsLatency | 수신 이벤트 대기 시간 (미리 보기) | 밀리초 | 평균 | Azure Digital Twins에 의해 egressed 될 준비가 되 면 이벤트가 도착 하는 데 걸리는 시간입니다 .이 시점에서 서비스는 성공/실패 결과를 전송 합니다. | 결과 |

#### <a name="routing-metrics"></a>라우팅 메트릭

라우팅을 사용 하 여 수행 해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| 설명 | 차원 |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | 메시지 라우팅 (미리 보기) | 개수 | 합계 | Event Hub, Service Bus, Event Grid 등의 끝점 Azure 서비스로 라우팅되는 메시지 수입니다. | 끝점 형식, <br>결과 |
| RoutingFailureRate | 라우팅 실패율 (미리 보기) | 백분율 | 평균 | Azure Digital Twins에서 Event Hub, Service Bus, Event Grid 등의 끝점 Azure 서비스로 라우팅되는 오류를 발생 시키는 이벤트의 백분율입니다. | 끝점 형식, <br>결과 |
| RoutingLatency | 라우팅 대기 시간 (미리 보기) | 밀리초 | 평균 | Azure Digital Twins에서 라우팅하는 이벤트 사이에 경과 된 시간은 이벤트 허브, Service Bus 또는 Event Grid와 같은 끝점 Azure 서비스에 게시 될 때까지 적용 됩니다. | 끝점 형식, <br>결과 |

## <a name="dimensions"></a>차원

차원은 메트릭에 대 한 자세한 정보를 식별 하는 데 도움이 됩니다. 일부 라우팅 메트릭은 끝점 당 정보를 제공 합니다. 다음 표에서는 이러한 차원에 사용할 수 있는 값을 나열 합니다.

| 차원 | 값 |
| --- | --- |
| 인증 | OAuth |
| 작업 (API 요청) | DigitalTwins/DigitalTwins/delete, <br>DigitalTwins/DigitalTwins/write, <br>DigitalTwins/DigitalTwins/read, <br>DigitalTwins/eventroutes/읽기, <br>DigitalTwins/eventroutes/write, <br>DigitalTwins/eventroutes/삭제, <br>DigitalTwins/모델/읽기, <br>DigitalTwins/모델/쓰기, <br>DigitalTwins/모델/삭제, <br>DigitalTwins/쿼리/동작 |
| 엔드포인트 유형 | Event Grid <br>이벤트 허브, <br>Service Bus |
| 프로토콜 | HTTPS |
| 결과 | 성공할 <br>실패 |
| 상태 코드 | 200, 404, 500 등이 있습니다. |
| 상태 코드 클래스 | 2xx, 4xx, 5xx 등이 있습니다. |
| 상태 텍스트 | 내부 서버 오류를 찾을 수 없습니다. |

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에 대해 기록 된 메트릭을 관리 하는 방법에 대 한 자세한 내용은 [*문제 해결: 진단 설정*](troubleshoot-diagnostics.md)을 참조 하세요.