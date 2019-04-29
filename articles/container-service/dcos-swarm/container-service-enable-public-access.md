---
title: (사용되지 않음) Azure DC/OS 컨테이너 앱에 대한 액세스 허용
description: Azure Container Service에서 DC/OS 컨테이너에 대해 공용 액세스를 사용하도록 설정하는 방법입니다.
services: container-service
author: sauryadas
manager: madhana
ms.service: container-service
ms.topic: article
ms.date: 08/26/2016
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 3e4ba15fa1925ca40ad7760acbd14331fbdd1343
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61457384"
---
# <a name="deprecated-enable-public-access-to-an-azure-container-service-application"></a>(사용되지 않음) Azure Container Service 애플리케이션에 공용 액세스를 사용하도록 설정

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

ACS [공용 에이전트 풀](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) 의 모든 DC/OS 컨테이너가 인터넷에 자동으로 노출됩니다. 기본적으로 포트 **80**, **443**, **8080**이 열리며 이러한 포트에서 수신 대기하는 모든 (공용) 컨테이너에 액세스할 수 있습니다. 이 문서에서는 Azure Container Service의 애플리케이션에 대해 더 많은 포트를 여는 방법을 보여 줍니다.

## <a name="open-a-port-portal"></a>포트 열기(포털)
먼저 원하는 포트를 열어야 합니다.

1. 포털에 로그인합니다.
2. Azure Container Service를 배포한 리소스 그룹을 찾습니다.
3. 에이전트 부하 분산 장치( **XXXX-agent-lb-XXXX**와 유사하게 이름 지정됨)를 선택합니다.
   
    ![Azure Container Service 부하 분산 장치](./media/container-service-enable-public-access/agent-load-balancer.png)
4. **프로브**를 클릭한 후 **추가**를 클릭합니다.
   
    ![Azure Container Service 부하 분산 장치 프로브](./media/container-service-enable-public-access/add-probe.png)
5. 프로브 양식을 입력하고 **확인**을 클릭합니다.
   
   | 필드 | 설명 |
   | --- | --- |
   | 이름 |프로브에 대한 설명이 포함된 이름입니다. |
   | 포트 |테스트할 컨테이너의 포트입니다. |
   | path |(HTTP 모드에 있는 경우) 프로브에 대한 상대 웹 사이트 경로입니다. HTTPS는 지원되지 않습니다. |
   | Interval |프로브 시도 간격(초)입니다. |
   | 비정상 임계값 |몇 번의 연속 프로브 시도 후 컨테이너를 비정상으로 간주할지를 나타냅니다. |
6. 에이전트 부하 분산 장치 속성으로 돌아가 **부하 분산 규칙**을 클릭한 후 **추가**를 클릭합니다.
   
    ![Azure Container Service 부하 분산 장치 규칙](./media/container-service-enable-public-access/add-balancer-rule.png)
7. 부하 분산 장치 양식을 입력하고 **확인**을 클릭합니다.
   
   | 필드 | 설명 |
   | --- | --- |
   | 이름 |부하 분산 장치에 대한 설명이 포함된 이름입니다. |
   | 포트 |공용 수신 포트입니다. |
   | 백 엔드 포트 |트래픽을 라우팅할 컨테이너의 내부 공용 포트입니다. |
   | 백 엔드 풀 |이 풀에 있는 컨테이너가 이 부하 분산 장치의 대상이 됩니다. |
   | 프로브 |프로브는 **백 엔드 풀** 의 대상이 정상인지 확인하는 데 사용됩니다. |
   | 세션 지속성 |세션 기간 동안 클라이언트의 트래픽을 처리하는 방식을 결정합니다.<br><br>**없음**: 동일한 클라이언트의 후속 요청은 컨테이너에 의해 처리할 수 있습니다.<br>**클라이언트 IP**: 동일한 클라이언트 IP의 후속 요청은 동일한 컨테이너에 의해 처리됩니다.<br>**클라이언트 IP 및 프로토콜**: 동일한 클라이언트 IP 및 프로토콜 조합의 후속 요청은 동일한 컨테이너로 처리합니다. |
   | 유휴 상태 시간 제한 |(TCP 전용) *keep-alive* 메시지에 의존하지 않고 TCP/HTTP를 열기 상태로 유지하는 시간(분)입니다. |

## <a name="add-a-security-rule-portal"></a>보안 규칙 추가(포털)
다음으로, 트래픽을 열린 포트에서 방화벽을 통과하도록 라우팅하는 보안 규칙을 추가해야 합니다.

1. 포털에 로그인합니다.
2. Azure Container Service를 배포한 리소스 그룹을 찾습니다.
3. **공용** 에이전트 네트워크 보안 그룹을 선택합니다(이름은 **XXXX-agent-public-nsg-XXXX**와 유사).
   
    ![Azure Container Service 네트워크 보안 그룹](./media/container-service-enable-public-access/agent-nsg.png)
4. **인바운드 보안 규칙**을 선택한 후 **추가**를 선택합니다.
   
    ![Azure Container Service 네트워크 보안 그룹 규칙](./media/container-service-enable-public-access/add-firewall-rule.png)
5. 공용 포트를 허용하도록 방화벽 규칙을 채우고 **확인**을 클릭합니다.
   
   | 필드 | 설명 |
   | --- | --- |
   | 이름 |방화벽 규칙에 대한 설명이 포함된 이름입니다. |
   | 우선 순위 |규칙에 대한 우선순위입니다. 번호가 낮을수록 우선순위가 높습니다. |
   | 원본 |이 규칙에 의해 허용 또는 거부될 IP 주소 범위를 제한합니다. 제한을 지정하지 않으려면 **모두** 를 사용합니다. |
   | 부여 |이 보안 규칙을 위한 미리 정의된 서비스 집합을 선택합니다. 그렇지 않으면 **사용자 지정** 을 사용하여 직접 작성합니다. |
   | 프로토콜 |**TCP** 또는 **UDP**에 따라 트래픽을 제한합니다. 제한을 지정하지 않으려면 **모두** 를 사용합니다. |
   | 포트 범위 |**서비스**가 **사용자 지정**인 경우 이 규칙의 영향을 받는 포트 범위를 지정합니다. **80**과 같은 단일 포트 또는 **1024-1500**과 같은 범위를 사용할 수 있습니다. |
   | 액션(Action) |조건과 일치하는 트래픽을 허용 또는 거부합니다. |

## <a name="next-steps"></a>다음 단계
[공용 및 개인 DC/OS 에이전트](container-service-dcos-agents.md)의 차이점에 대해 알아보세요.

[DC/OS 컨테이너 관리](container-service-mesos-marathon-ui.md)에 대해 자세히 알아보세요.

