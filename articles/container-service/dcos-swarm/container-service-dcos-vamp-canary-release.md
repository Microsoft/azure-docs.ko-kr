---
title: (사용되지 않음) Azure DC/OS 클러스터에서 Vamp를 사용하여 카나리아 릴리스
description: Vamp를 사용하여 Azure Container Service DC/OS 클러스터에서 서비스를 카나리아 릴리스하고 스마트 트래픽 필터링을 적용하는 방법
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: f1b3c08cce2cb33feab899ea082fc6fb40225182
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61458192"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(사용되지 않음) Azure Container Service DC/OS 클러스터에서 Vamp를 사용하여 마이크로서비스 카나리아 릴리스

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

이 연습에서는 DC/OS 클러스터를 사용하여 Azure Container Service에 Vamp를 설정합니다. Vamp 데모 서비스인 "sava"를 카나리아 릴리스한 후 스마트 트래픽 필터링을 적용하여 서비스와 Firefox의 호환성 문제를 해결합니다. 

> [!TIP] 
> 이 연습에서는 Vamp가 DC/OS 클러스터에서 실행되지만 Vamp를 Kubernetes와 함께 조정자로 사용할 수도 있습니다.
>

## <a name="about-canary-releases-and-vamp"></a>카나리아 릴리스 및 Vamp에 대한 정보


[카나리아 릴리스](https://martinfowler.com/bliki/CanaryRelease.html)란 Netflix, Facebook, Spotify 등의 혁신적인 조직에서 채택한 영리한 배포 전략입니다. 문제를 줄이고, 안전망을 도입하고, 혁신을 가속화하는 합리적인 방법입니다. 그렇다면 왜 모든 회사에서 이 방법을 사용하지 않을까요? 카나리아 전략을 포함하도록 CI/CD 파이프라인을 확장하면 복잡성이 높아지며 방대한 DevOps 지식과 경험이 필요합니다. 중소기업과 대기업 모두 시작하기도 전에 포기할 정도로 복잡합니다. 

[Vamp](https://vamp.io/)는 이 전환을 좀 더 용이하게 하고 고객이 선호하는 컨테이너 스케줄러에 카나리아 릴리스 기능을 가져오도록 설계된 오픈 소스 시스템입니다. Vamp의 카나리아 기능은 백분율 기반 롤아웃보다 우수합니다. 트래픽을 필터링하고 다양한 조건으로 나눌 수 있습니다. 예를 들어 특정 사용자, IP 범위 또는 디바이스를 대상으로 할 수 있습니다. Vamp는 성능 메트릭을 추적 및 분석하여 실제 데이터를 기반으로 자동화가 가능합니다. 오류 발생 시 자동으로 롤백하도록 설정하거나 부하 또는 대기 시간에 따라 개별 서비스 변형의 규모를 조정할 수 있습니다.

## <a name="set-up-azure-container-service-with-dcos"></a>DC/OS를 사용하여 Azure Container Service 설정



1. 마스터 하나와 기본 크기의 에이전트 두 개가 있는 [DC/OS 클러스터를 배포](container-service-deployment.md)합니다. 

2. DC/OS 클러스터에 연결할 [SSH 터널을 만듭니다](../container-service-connect.md). 이 문서에서는 로컬 포트 80에서 클러스터에 터널링하는 것으로 가정합니다.


## <a name="set-up-vamp"></a>Vamp 설치

실행 중인 DC/OS 클러스터를 설정 했으므로 DC/OS UI에서 Vamp를 설치할 수 있습니다 (http:\//localhost:80). 

![DC/OS UI](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

설치는 두 단계로 수행됩니다.

1. **Elasticsearch를 배포**합니다.

2. 그런 다음 Vamp DC/OS Universe 패키지를 설치하여 **Vamp를 배포**합니다.

### <a name="deploy-elasticsearch"></a>Elasticsearch 배포

Vamp는 메트릭 수집 및 집계에 사용할 Elasticsearch가 필요합니다. [magneticio Docker 이미지](https://hub.docker.com/r/magneticio/elastic/)를 사용하여 호환 Vamp Elasticsearch 스택을 배포할 수 있습니다.

1. DC/OS UI에서 **서비스**로 이동하여 **서비스 배포**를 클릭합니다.

2. **새 서비스 배포** 팝업에서 **JSON 모드**를 선택합니다.

   ![JSON 모드 선택](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. 다음 JSON에 붙여 넣습니다. 이 구성은 Elasticsearch 포트에서 1GB RAM 컨테이너와 기본적인 상태 검사를 실행합니다.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. **배포**을 참조하십시오.

   DC/OS가 Elasticsearch 컨테이너를 배포합니다. **서비스** 페이지에서 진행률을 추적할 수 있습니다.  

   ![Elasticsearch 배포](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Vamp 배포

Elasticsearch가 **실행 중**으로 보고하면 DC/OS Universe 패키지를 추가할 수 있습니다. 

1. **Universe**로 이동하여 **vamp**를 검색합니다. 
   ![DC/OS universe의 Vamp](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. vamp 패키지 옆에서 **설치**를 클릭하고 **고급 설치**를 선택합니다.

3. 아래로 스크롤하여 elasticsearch url(`http://elasticsearch.marathon.mesos:9200`)을 입력합니다. 

   ![Elasticsearch URL 입력](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. **확인 후 설치**를 클릭한 다음 **설치**를 클릭하여 배포를 시작합니다.  

   DC/OS가 모든 필수 Vamp 구성 요소를 배포합니다. **서비스** 페이지에서 진행률을 추적할 수 있습니다.
  
   ![universe 패키지로 Vamp 배포](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. 배포가 완료되면 Vamp UI에 액세스할 수 있습니다.

   ![DC/OS의 Vamp 서비스](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>첫 번째 서비스 배포

Vamp가 실행 중이니, 청사진의 서비스를 배포합니다. 

[Vamp 청사진](https://vamp.io/documentation/using-vamp/blueprints/)은 가장 간단한 형태로 엔드포인트(게이트웨이), 클러스터 및 배포할 서비스를 설명합니다. Vamp는 클러스터를 사용하여 동일한 서비스의 여러 변형을 카나리아 릴리스 또는 A/B 테스트를 위한 논리 그룹으로 그룹화합니다.  

이 시나리오에서는 [**sava**](https://github.com/magneticio/sava)라고 하는 샘플 모놀리식 애플리케이션을 사용하며 버전은 1.0입니다. 모놀리식은 magneticio/sava:1.0.0 아래의 Docker 허브에 있는 Docker 컨테이너에 패키지됩니다. 앱은 보통 포트 8080에서 실행되지만 이 예에서는 포트 9050 아래에 노출하려 합니다. 간단한 청사진을 사용하여 Vamp를 통해 앱을 배포합니다.

1. **배포**로 이동합니다.

2. **추가**를 클릭합니다.

3. 다음 청사진 YAML에 붙여 넣습니다. 이 청사진에는 서비스 변형이 하나뿐인 클러스터가 하나 포함되어 있으며 이후 단계에서 변경할 것입니다.

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. **저장**을 클릭합니다. Vamp가 배포를 시작합니다.

배포가 **배포** 페이지에 나열됩니다. 배포를 클릭하여 상태를 모니터링합니다.

![Vamp UI - sava 배포](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![Vamp UI의 sava 서비스](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

게이트웨이 두 개가 생성되며 **게이트웨이** 페이지에 나열됩니다.

* 실행 중인 서비스(9050 포트)에 액세스하는 안정적인 엔드포인트 
* Vamp가 관리하는 내부 게이트웨이(이 게이트웨이는 나중에 자세히 설명). 

![Vamp UI - sava 게이트웨이](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

Sava 서비스가 배포되었지만 아직 Azure Load Balancer는 이 서비스에 트래픽을 전달해야 한다는 것을 모르기 때문에 외부에서 이 서비스에 액세스할 수 없습니다. 서비스에 액세스하려면 Azure 네트워킹 구성을 업데이트합니다.


## <a name="update-the-azure-network-configuration"></a>Azure 네트워크 구성 업데이트

Vamp가 DC/OS 에이전트 노드에 sava 서비스를 배포하고 포트 9050에서 안정적인 엔드포인트를 노출했습니다. DC/OS 클러스터 외부에서 서비스에 액세스하려면 클러스터 배포에서 Azure 네트워크 구성을 다음과 같이 변경해야 합니다. 

1. 포트 9050의 트래픽을 sava 인스턴스로 전달하도록 상태 프로브와 규칙을 사용하여 에이전트(**dcos-agent-lb-xxxx**라는 이름의 리소스)에 대해 **Azure Load Balancer를 구성**합니다. 

2. 포트 9050의 트래픽을 허용하도록 공용 에이전트(**XXXX-agent-public-nsg-XXXX**라는 이름의 리소스)에 대해 **네트워크 보안 그룹을 업데이트**합니다.

Azure Portal을 사용하여 이러한 작업을 완료하는 자세한 단계는 [Azure Container Service 애플리케이션에 공용 액세스를 사용하도록 설정](container-service-enable-public-access.md)을 참조하세요. 모든 포트 설정에 대해 포트 9050을 지정합니다.


모든 항목이 생성된 후에는 DC/OS 에이전트 부하 분산 장치(**dcos-agent-lb-xxxx**라는 이름의 리소스)의 **개요** 블레이드로 이동합니다. **공용 IP 주소**를 확인하고, 포트 9050에서 sava에 액세스할 때 이 주소를 사용합니다.

![Azure Portal - 공용 IP 주소 가져오기](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>카나리아 릴리스 실행

이 애플리케이션의 새 버전을 프로덕션 환경에 카나리아 릴리스하려 한다고 가정해 봅시다. magneticio/sava:1.1.0으로 컨테이너화되었으며 사용 준비가 완료되었습니다. Vamp를 사용하면 실행 중인 배포에 새 서비스를 쉽게 추가할 수 있습니다. 이러한 "병합된" 서비스는 클러스터의 기존 서비스와 함께 배포되고 0%의 가중치가 할당됩니다. 트래픽 분산을 조정하기 전에는 새로 병합된 서비스에 트래픽이 전달되지 않습니다. Vamp UI의 가중치 슬라이더를 사용하여 분산을 완벽하게 제어할 수 있으며, 증분 조정(카나리아 릴리스) 또는 인스턴트 롤백이 가능합니다.

### <a name="merge-a-new-service-variant"></a>새로운 서비스 변형 병합

새 sava 1.1 서비스를 실행 중인 배포와 병합하려면:

1. Vamp UI에서 **청사진**을 클릭합니다.

2. **추가**를 클릭하여 다음 청사진 YAML에 붙여넣습니다. 이 청사진은 기존 클러스터(sava_cluster) 내에 배포할 새 서비스 변형(sava: 1.1.0)에 대해 설명합니다.

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. **저장**을 클릭합니다. 청사진이 저장되고 **청사진** 페이지에 나열됩니다.

4. sava:1.1 청사진에서 동작 메뉴를 열고 **병합**을 클릭합니다.

   ![Vamp UI - 청사진](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. **sava** 배포를 선택하고 **병합**을 클릭합니다.

   ![Vamp UI - 청사진을 배포와 병합](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp는 청사진에 설명된 새 sava:1.1.0 서비스 변형을 실행 중인 배포의 **sava_cluster**에 있는 sava:1.0.0과 함께 배포합니다. 

![Vamp UI - 업데이트된 sava 배포](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

**sava/sava_cluster/webport** 게이트웨이(클러스터 엔드포인트)도 업데이트되고, 새로 배포된 sava: 1.1.0에 대 한 경로가 추가됩니다. 아직은 트래픽이 여기로 라우팅되지 않습니다(**가중치**가 0%로 설정).

![Vamp UI - 클러스터 게이트웨이](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>카나리아 릴리스

두 sava 버전이 동일한 클러스터에 배포된 상태에서, **가중치** 슬라이더를 이동하여 둘 사이의 트래픽 분산을 조정합니다.

1. ![가중치](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) 옆에 있는 **Vamp UI - 편집**을 클릭합니다.

2. 가중치 분산을 50%/50%로 설정하고 **저장**을 클릭합니다.

   ![Vamp UI - 게이트웨이 가중치 슬라이더](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. 브라우저로 돌아가서 sava 페이지를 몇 번 더 새로 고칩니다. 이제 sava 애플리케이션이 sava:1.0 페이지와 sava:1.1 페이지 사이에서 전환됩니다.

   ![sava1.0 및 sava1.1 서비스 교대](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > 정적 자산의 캐시 때문에 이 페이지 교대는 브라우저의 "시크릿" 또는 "익명" 모드에서 가장 잘 작동합니다.
  >

### <a name="filter-traffic"></a>트래픽 필터링

배포 후 sava:1.1.0에서 Firefox 브라우저 표시 문제를 일으키는 호환성 문제를 발견했다고 가정해 봅시다. 들어오는 트래픽을 필터링하고 모든 Firefox 사용자를 알려진 안정적인 sava:1.0.0에 직접 전달하도록 Vamp를 설정할 수 있습니다. 이 필터는 Firefox 사용자의 불편을 즉시 해결하며, 그 외의 모든 사용자는 향상된 sava:1.1.0의 이점을 계속해서 이용할 수 있습니다.

Vamp는 **조건**을 사용하여 게이트웨이의 경로 간 트래픽을 필터링합니다. 트래픽은 각 경로에 적용된 조건에 따라 필터링된 후 전달됩니다. 나머지 트래픽은 게이트웨이 가중치 설정에 따라 분산됩니다.

모든 Firefox 사용자를 필터링하고 이전 sava:1.0.0에 전달하는 조건을 만들 수 있습니다.

1. sava/sava_cluster/webport **게이트웨이** 페이지에서 ![Vamp UI - 편집](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png)을 클릭하여 sava/sava_cluster/sava:1.0.0/webport 경로에 **조건**을 추가합니다. 

2. 조건 **user-agent == Firefox**를 입력하고 ![Vamp UI - 저장](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)을 클릭합니다.

   Vamp가 기본 강도 0%인 조건을 추가합니다. 트래픽 필터링을 시작하려면 조건 강도를 조정해야 합니다.

3. ![Vamp UI - 편집](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png)을 클릭하여 조건에 적용되는 **강도**를 변경합니다.
 
4. **강도**를 100%로 설정하고 ![Vamp UI - 저장](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png)을 클릭합니다.

   이제 Vamp는 조건과 일치하는 모든 트래픽(모든 Firefox 사용자)을 sava:1.0.0으로 보냅니다.

   ![Vamp UI - 게이트웨이에 조건 적용](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. 마지막으로 나머지 트래픽(모든 비 Firefox 사용자)을 새 sava:1.1.0으로 보내도록 게이트웨이 가중치를 조정합니다. ![가중치](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) 옆에 있는 **Vamp UI - 편집**을 클릭하고 100%가 sava/sava_cluster/sava:1.1.0/webport 경로로 전달되도록 가중치 분산을 설정합니다.

   이제 조건에 의해 필터링되지 않는 모든 트래픽은 새 sava:1.1.0으로 전달됩니다.

6. 실제로 작동 중인 필터를 보려면 두 가지 브라우저(하나는 Firefox, 다른 하나는 그 외의 브라우저)를 열고 두 브라우저에서 sava 서비스에 액세스합니다. Firefox에 대한 모든 요청은 sava:1.0.0으로 전달되고, 그 외의 브라우저는 sava:1.1.0으로 전달됩니다.

   ![Vamp UI - 트래픽 필터링](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>요약

이 문서에서는 DC/OS 클러스터의 Vamp에 대해 간략히 소개했습니다. 초보자를 위해 Azure Container Service DC/OS 클러스터에서 Vamp를 실행하고, Vamp 청사진을 사용하여 서비스를 배포하고, 노출된 엔드포인트(게이트웨이)에서 서비스에 액세스했습니다.

또한 Vamp의 강력한 기능 몇 가지를 살펴보았습니다. 새 서비스 변형을 실행 중인 배포와 병합하고 증분 방식으로 도입한 후 트래픽을 필터링하여 알려진 호환성 문제를 해결했습니다.


## <a name="next-steps"></a>다음 단계

* [Vamp REST API](https://vamp.io/documentation/api/api-reference/)를 통해 Vamp 작업을 관리하는 방법을 알아봅니다.

* Node.js에서 Vamp 자동화 스크립트를 빌드하고 [Vamp 워크플로](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow)로 실행합니다.

* 추가 [VAMP 자습서](https://vamp.io/documentation/tutorials/)를 살펴봅니다.

