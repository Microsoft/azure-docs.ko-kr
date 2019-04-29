---
title: (사용되지 않음) Azure DC/OS 클러스터 모니터링 - ELK 스택
description: ELK(Elasticsearch, Logstash 및 Kibana)를 사용하여 Azure Container Service 클러스터에서 DC/OS 클러스터를 모니터링합니다.
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 03/27/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 342cf23db2df7d7c79a2b56df96d1a78d6ba215e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61467771"
---
# <a name="deprecated-monitor-an-azure-container-service-cluster-with-elk"></a>(사용되지 않음) ELK를 사용하여 Azure Container Service 클러스터 모니터링

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

이 문서에서는 Azure Container Service의 DC/OS 클러스터에 ELK(Elasticsearch, Logstash, Kibana) 스택을 배포하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
Azure Container Service를 통해 구성된 DC/OS 클러스터를 [배포](container-service-deployment.md) 및 [연결](../container-service-connect.md)합니다. [여기](container-service-mesos-marathon-ui.md)에서 DC/OS 대시보드 및 Marathon 서비스에 대해 알아봅니다. 또한 [Marathon Load Balancer](container-service-load-balancing.md)를 설치합니다.


## <a name="elk-elasticsearch-logstash-kibana"></a>ELK(Elasticsearch, Logstash, Kibana)
ELK 스택은 Elasticsearch, Logstash 및 Kibana가 조합된 것으로, 클러스터에서 로그를 모니터링하고 분석하는 데 사용할 수 있는 종단 간 스택을 제공합니다.

## <a name="configure-the-elk-stack-on-a-dcos-cluster"></a>DC/OS 클러스터에서 ELK 스택 구성
DC/OS UI를 통해 [http://localhost:80/](http://localhost:80/)에 액세스합니다. DC/OS UI에서 **Universe**로 이동합니다. DC/OS Universe에서 Elasticsearch, Logstash 및 Kibana를 검색한 후 이 순서대로 설치합니다. **고급 설치** 링크로 이동하면 구성에 대해 보다 자세한 정보를 얻을 수 있습니다.

![ELK1](./media/container-service-monitoring-elk/elk1.PNG) ![ELK2](./media/container-service-monitoring-elk/elk2.PNG) ![ELK3](./media/container-service-monitoring-elk/elk3.PNG) 

ELK 컨테이너가 작동되고 실행되면 Marathon-LB를 통해 Kibana에 액세스할 수 있도록 설정해야 합니다. 아래와 같이 **서비스** > **kibana**로 이동한 후 **편집**을 클릭합니다.

![ELK4](./media/container-service-monitoring-elk/elk4.PNG)


**JSON 모드**로 전환한 후 아래로 스크롤하여 레이블 섹션까지 이동합니다.
아래 표시된 것처럼 여기에 `"HAPROXY_GROUP": "external"` 항목을 추가해야 합니다.
**변경 내용 배포**를 클릭하면 컨테이너가 다시 시작됩니다.

![ELK5](./media/container-service-monitoring-elk/elk5.PNG)


HAPROXY 대시보드에서 Kibana가 서비스로 등록되어 있는지 확인하려는 경우 HAPROXY가 포트 9090에서 실행되므로 에이전트 클러스터에서 9090 포트를 열어야 합니다.
기본적으로 DC/OS 에이전트 클러스터에서 포트 80, 8080 및 443을 엽니다.
포트를 열고 공용 액세스를 제공하기 위한 지침이 [여기](container-service-enable-public-access.md)에 나와 있습니다.

HAPROXY 대시보드에 액세스하려면 `http://$PUBLIC_NODE_IP_ADDRESS:9090/haproxy?stats`에서 Marathon-LB 관리자 인터페이스를 엽니다.
해당 URL로 이동되면 아래와 같이 HAPROXY 대시보드가 표시되며 Kibana에 대한 서비스 항목이 표시됩니다.

![ELK6](./media/container-service-monitoring-elk/elk6.PNG)


포트 5601에 구축된 Kibana 대시보드에 액세스하려면 포트 5601을 열어야 합니다. [여기](container-service-enable-public-access.md)에 나와 있는 지침을 따르세요. 그런 후 `http://localhost:5601`에서 Kibana 대시보드를 엽니다.

## <a name="next-steps"></a>다음 단계

* 시스템 및 애플리케이션 로그 전달 및 설정은 [ELK로 DC/OS에서 로그 관리](https://docs.mesosphere.com/1.8/administration/logging/elk/)를 참조하세요.

* 로그를 필터링하려면 [ELK로 로그 필터링](https://docs.mesosphere.com/1.8/administration/logging/filter-elk/)을 참조하세요. 

 

