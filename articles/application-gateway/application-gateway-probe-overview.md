---
title: Azure Application Gateway에 대한 상태 모니터링 개요
description: Azure 애플리케이션 게이트웨이는 백 엔드 풀의 모든 리소스 상태를 모니터링 하 고 풀에서 비정상으로 간주 되는 모든 리소스를 자동으로 제거 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 01/28/2020
ms.author: victorh
ms.openlocfilehash: 5c25f591d1011d2efd66851cafd67ceef8b56637
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76766821"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway 상태 모니터링 개요

Azure Application Gateway는 기본적으로 백 엔드 풀의 모든 리소스 상태를 모니터링하고 풀에서 비정상으로 간주한 모든 리소스를 자동으로 제거합니다. Application Gateway는 비정상 인스턴스를 계속 모니터링하며 사용할 수 있게 되고 상태 프로브에 응답하게 되면 정상 백 엔드 풀에 다시 추가합니다. 애플리케이션 게이트웨이에서 상태 프로브를 백 엔드 HTTP 설정에 정의된 동일한 포트와 함께 보냅니다. 이 구성으로 프로브에서 사용자가 백 엔드에 연결하는 데 사용하는 것과 동일한 포트를 테스트합니다. 

상태 프로브에 사용 되는 원본 IP 주소 Application Gateway 백 엔드 풀에 따라 다릅니다.
 
- 백 엔드 풀이 공용 엔드포인트 인 경우 원본 주소는 application gateway 프런트 엔드 공용 IP 주소입니다.
- 백 엔드 풀이 개인 엔드포인트 인 경우 원본 IP 주소는 application gateway 서브넷 개인 IP 주소 공간에서 가져온 것입니다.


![Application Gateway 프로브 예제][1]

기본 상태 프로브 모니터링 사용 외에도 애플리케이션 프로그의 요구 사항에 맞게 상태 프로브를 사용자 지정할 수도 있습니다. 이 문서에서는 기본 및 사용자 지정 상태 프로브를 모두 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>기본 상태 프로브

애플리케이션 게이트웨이는 사용자 지정 프로브 구성을 설정하지 않는 경우 기본 상태 프로브를 자동으로 구성합니다. 모니터링 동작은 백 엔드 풀에 대해 구성된 IP 주소에 대한 HTTP 요청을 만들어 작동합니다. 기본 프로브의 경우 백 엔드 http 설정이 HTTPS에 대해 구성되면 프로브는 HTTPS를 사용하고 백엔드의 상태를 테스트합니다.

예: 포트 80에서 HTTP 네트워크 트래픽을 수신할 백 엔드 서버 A, B, C를 사용하도록 애플리케이션 게이트웨이를 구성합니다. 기본 상태 모니터링은 정상 HTTP 응답에 대해 3개의 서버를 30초마다 테스트합니다. 정상 HTTP 응답은 [상태 코드](https://msdn.microsoft.com/library/aa287675.aspx) 200에서 399 사이입니다.

서버 A에 대한 기본 프로브 확인이 실패하면 애플리케이션 게이트웨이가 백 엔드 풀에서 이를 제거하고 네트워크 트래픽이 이 서버로 이동되지 않습니다. 기본 프로브는 서버 A에 대해 30초마다 계속 확인합니다. 서버 A가 기본 상태 프로브에서 하나의 요청에 성공적으로 응답 하는 경우 백 엔드 풀에 정상으로 다시 추가 되 고 트래픽이 서버에 다시 전달 되기 시작 합니다.

### <a name="probe-matching"></a>프로브 일치

기본적으로 상태 코드가 200 ~ 399 사이인 HTTP (S) 응답은 정상으로 간주 됩니다. 사용자 지정 상태 프로브는 또한 일치하는 두 조건을 지원합니다. 일치 조건을 사용 하 여 필요에 따라 정상 응답을 만드는의 기본 해석을 수정할 수 있습니다.

다음이 일치하는 조건입니다. 

- **HTTP 응답 상태 코드 일치** - 사용자 지정 http 응답 코드 또는 응답 코드 범위를 수용하기 위한 프로브 일치 조건입니다. 응답 상태 코드 또는 상태 코드 범위를 구분하는 개별 쉼표가 지원됩니다.
- **HTTP 응답 본문 일치** - HTTP 응답 본문을 살펴보고 사용자 지정 문자열과 일치하는 프로브 일치 조건입니다. 일치는 응답 본문에 사용자 지정 문자열이 있는지만 찾지만 전체 정규식 일치는 검색 하지 않습니다.

`New-AzApplicationGatewayProbeHealthResponseMatch` cmdlet을 사용하여 일치 조건을 지정할 수 있습니다.

예:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
일치 조건이 지정되면 PowerShell에서 `-Match` 매개 변수를 사용하여 프로브 구성에 연결할 수 있습니다.

### <a name="default-health-probe-settings"></a>기본 상태 프로브 설정

| 프로브 속성 | 값 | Description |
| --- | --- | --- |
| 프로브 URL |http://127.0.0.1:\<port\>/ |URL 경로 |
| 간격 |30 |다음 상태 프로브가 전송되기 전에 대기할 시간의 양(초)입니다.|
| 시간 제한 |30 |프로브를 비정상으로 표시하기 전에 애플리케이션 게이트웨이에서 프로브 응답에 대해 대기할 시간의 양(초)입니다. 프로브가 정상으로 반환하는 경우 해당 백 엔드는 즉시 정상으로 표시됩니다.|
| 비정상 임계값 |3 |일반 상태 프로브에 오류가 발생 한 경우 전송할 프로브 수를 제어 합니다. 이러한 추가 상태 프로브는 빠르게 연속적으로 전송 되어 백 엔드의 상태를 빠르게 확인 하 고 프로브 간격을 기다리지 않습니다. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

> [!NOTE]
> 포트는 백 엔드 HTTP 설정과 동일한 포트입니다.

기본 프로브는 http:\//127.0.0.1:\<포트\> 에서만 상태를 확인 합니다. 사용자 지정 URL로 이동하거나 다른 모든 설정을 수정하도록 상태 프로브를 구성하려면 사용자 지정 프로브를 사용해야 합니다.

### <a name="probe-intervals"></a>프로브 간격

Application Gateway의 모든 인스턴스는 서로 독립적으로 백 엔드를 검색합니다. 동일한 프로브 구성이 각 Application Gateway 인스턴스에 적용됩니다. 예를 들어 프로브 구성이 30초마다 상태 프로브를 보내는 것이고 애플리케이션 게이트웨이에 두 개의 인스턴스가 있는 경우 두 인스턴스 모두는 30초마다 상태 프로브를 보냅니다.

또한 여러 수신기가 있는 경우 각 수신기는 서로 독립적으로 백 엔드를 검색합니다. 예를 들어 두 개의 서로 다른 포트(두 개의 백 엔드 http 설정으로 구성됨)에서 동일한 백 엔드 풀을 가리키는 두 개의 수신기가 있는 경우 각 수신기는 동일한 백 엔드를 독립적으로 검색합니다. 이 경우 두 수신기에 대한 각 애플리케이션 게이트웨이 인스턴스에서 두 개의 프로브가 있습니다. 이 시나리오에서 애플리케이션 게이트웨이의 두 개의 인스턴스가 있는 경우 백 엔드 가상 머신에는 구성된 프로브 간격마다 4개의 프로브가 표시됩니다.

## <a name="custom-health-probe"></a>사용자 지정 상태 프로브

사용자 지정 프로브를 통해 상태 모니터링을 보다 세부적으로 제어할 수 있습니다. 사용자 지정 프로브를 사용하는 경우 프로브 간격, 테스트할 URL 및 경로, 백 엔드 풀 인스턴스를 비정상으로 표시하기 전에 허용할 실패 응답 횟수를 구성할 수 있습니다.

### <a name="custom-health-probe-settings"></a>사용자 지정 상태 프로브 설정

다음 표에는 사용자 지정 상태 프로브의 속성을 위한 정의가 나와 있습니다.

| 프로브 속성 | Description |
| --- | --- |
| 이름 |프로브 이름입니다. 이 이름은 백 엔드 HTTP 설정에서 프로브를 참조하는 데 사용됩니다. |
| 프로토콜 |프로브를 보내는 데 사용하는 프로토콜입니다. 프로브는 백 엔드 HTTP 설정에 정의된 프로토콜을 사용합니다. |
| 호스트 |프로브에 보낼 호스트 이름입니다. 다중 사이트를 Application Gateway에 구성하는 경우에만 적용할 수 있습니다. 그렇지 않으면 '127.0.0.1'을 사용합니다. 이 값은 VM 호스트 이름과 다릅니다. |
| 경로 |프로브의 상대 경로입니다. 올바른 경로는 '/'부터 시작합니다. |
| 간격 |프로브 간격(초). 이 값은 연속된 두 프로브 사이의 시간 간격입니다. |
| 시간 제한 |프로브 시간 제한(초) 이 시간 제한 기간 내에 올바른 응답을 받지 못하면 프로브는 실패로 표시 됩니다.  |
| 비정상 임계값 |프로브 재시도 횟수. 연속된 프로브 실패 횟수가 비정상 임계값에 도달하면 백 엔드 서버가 표시됩니다. |

> [!IMPORTANT]
> Application Gateway가 단일 사이트에 대해 구성된 경우 기본적으로 호스트 이름은 '127.0.0.1'로 지정해야 합니다. 그렇지 않으면 사용자 지정 프로브에서 구성되어야 합니다.
> 참고로 사용자 지정 프로브는 \<protocol\>://\<host\>:\<port\>\<path\>로 전송됩니다. 사용된 포트는 항상 백 엔드 HTTP 설정에 정의된 것과 동일한 포트입니다.

## <a name="nsg-considerations"></a>NSG 고려 사항

Application gateway 서브넷에 NSG (네트워크 보안 그룹)가 있는 경우 인바운드 트래픽에 대한 응용 프로그램 게이트웨이 서브넷에서 포트 범위 65503-65534를 열어야 합니다. 이러한 포트는 백 엔드 상태 API가 제대로 작동하기 위해 필요합니다.

또한 아웃바운드 인터넷 연결은 차단될 수 없으며, AzureLoadBalancer 태그에서 오는 인바운드 트래픽은 허용되어야 합니다.

## <a name="next-steps"></a>다음 단계
Application Gateway 상태 모니터링에 대해 알아본 후에 PowerShell 및 Azure Resource Manager 배포 모델을 사용하여 Azure Portal의 [사용자 지정 상태 프로브](application-gateway-create-probe-portal.md) 또는 [사용자 지정 상태 프로브](application-gateway-create-probe-ps.md)를 구성할 수 있습니다.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
