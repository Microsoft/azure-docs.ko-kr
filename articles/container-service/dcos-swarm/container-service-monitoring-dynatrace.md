---
title: (사용되지 않음) Azure DC/OS 클러스터 모니터링 - Dynatrace
description: Dynatrace를 사용하여 Azure Container Service DC/OS 클러스터를 모니터링합니다. DC/OS 대시보드를 사용하여 Dynatrace OneAgent를 배포합니다.
services: container-service
author: MartinGoodwell
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/13/2016
ms.author: rogardle
ms.custom: mvc
ms.openlocfilehash: 8f34a00d9256c288a2842e905c06d5336522eece
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119859"
---
# <a name="deprecated-monitor-an-azure-container-service-dcos-cluster-with-dynatrace-saasmanaged"></a>(사용되지 않음) Dynatrace SaaS/Managed를 사용하여 Azure Container Service DC/OS 클러스터 모니터링

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

이 문서에서는 [Dynatrace](https://www.dynatrace.com/) OneAgent를 배포하여 Azure Container Service 클러스터의 모든 에이전트 노드를 모니터링하는 방법을 보여 줍니다. 이러한 구성을 위해서는 Dynatrace SaaS/Managed 계정이 필요합니다. 

## <a name="dynatrace-saasmanaged"></a>Dynatrace SaaS/Managed
Dynatrace는 매우 동적인 컨테이너 및 클러스터 환경을 위한 클라우드 네이티브 모니터링 솔루션입니다. 이러한 솔루션을 통해 실시간 사용 현황 데이터를 사용하여 컨테이너 배포 및 메모리 할당을 보다 효과적으로 최적화할 수 있습니다. 자동화된 기준 지정, 문제 상관 관계 및 근본 원인 검색을 제공하여 애플리케이션 및 인프라 문제를 자동으로 파악할 수 있습니다.

다음 그림에서는 Dynatrace UI를 보여 줍니다.

![Dynatrace UI](./media/container-service-monitoring-dynatrace/dynatrace.png)

## <a name="prerequisites"></a>필수 조건 
Azure Container Service를 통해 구성된 클러스터를 [배포](container-service-deployment.md) 및 [연결](./../container-service-connect.md)합니다. [Marathon UI](container-service-mesos-marathon-ui.md)를 탐색합니다. [https://www.dynatrace.com/trial/](https://www.dynatrace.com/trial/)로 이동하여 Dynatrace SaaS 계정을 설정합니다.  

## <a name="configure-a-dynatrace-deployment-with-marathon"></a>Marathon으로 Dynatrace 배포 구성
이러한 단계는 Marathon으로 클러스터에 Dynatrace 애플리케이션을 배포 및 구성하는 방법을 보여 줍니다.

1. [http://localhost:80/](http://localhost:80/)을 통해 DC/OS UI에 액세스합니다. DC/OS UI에 액세스했으면 **Universe** 탭으로 이동한 다음 **Dynatrace**를 찾습니다.

    ![DC/OS Universe의 Dynatrace](./media/container-service-monitoring-dynatrace/dynatrace-universe.png)

2. 구성을 완료하려면 Dynatrace SaaS 계정 또는 무료 평가판 계정이 필요합니다. Dynatrace 대시보드에 로그인한 후 **Dynatrace 배포**를 선택합니다.

    ![Dynatrace PaaS 통합 설정](./media/container-service-monitoring-dynatrace/setup-paas.png)

3. 페이지에서 **PaaS 통합 설정**을 선택합니다. 

    ![Dynatrace API 토큰](./media/container-service-monitoring-dynatrace/api-token.png) 

4. DC/OS Universe 내에 있는 Dynatrace OneAgent 구성에 API 토큰을 입력합니다. 

    ![DC/OS Universe의 Dynatrace OneAgent 구성](./media/container-service-monitoring-dynatrace/dynatrace-config.png)

5. 실행하려는 노드 수로 인스턴스를 설정합니다. 더 높은 숫자로 설정해도 작동하지만 DC/OS는 실제로 해당 숫자에 도달할 때까지 계속 새 인스턴스를 찾습니다. 원할 경우 1000000 같은 값으로 설정할 수도 있습니다. 이 경우 클러스터에 새 노드가 추가될 때마다 Dynatrace는 계속해서 추가 인스턴스를 배포하려고 시도하는 DC/OS의 가격으로 에이전트를 해당 새 노드에 자동으로 배포합니다.

    ![DC/OS Universe-인스턴스의 Dynatrace 구성](./media/container-service-monitoring-dynatrace/dynatrace-config2.png)

## <a name="next-steps"></a>다음 단계

패키지를 설치했으면 Dynatrace 대시보드로 돌아갑니다. 클러스터 내에서 컨테이너에 대한 다른 사용 현황 메트릭을 탐색할 수 있습니다. 