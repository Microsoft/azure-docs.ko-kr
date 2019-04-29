---
title: (사용되지 않음) Marathon UI를 사용하여 Azure DC/OS 클러스터 관리
description: Marathon 웹 UI를 사용하여 컨테이너를 Azure 컨테이너 서비스 클러스터 서비스에 배포합니다.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 16c16c0217a796ffbb57e10430f90cb4a7660ac6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61468230"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(사용되지 않음) Marathon 웹 UI를 통해 Azure Container Service DC/OS 클러스터 관리

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS는 기본 하드웨어를 추상화하는 동안 클러스터형 워크로드를 배포 및 확장하기 위한 환경을 제공합니다. DC/OS의 상단에 계산 워크로드의 예약 및 실행을 관리하는 프레임워크가 있습니다.

프레임워크를 널리 사용되고 있는 많은 워크로드에 사용할 수 있지만 이 문서에서는 Marathon으로 컨테이너를 배포하는 방법을 설명합니다. 


## <a name="prerequisites"></a>필수 조건
이러한 예제를 통해 작업하기 전에 Azure 컨테이너 서비스에 구성된 DC/OS 클러스터가 필요합니다. 또한 이 클러스터에 원격으로 연결해야 합니다. 이러한 항목에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure 컨테이너 서비스 클러스터 배포](container-service-deployment.md)
* [Azure 컨테이너 서비스 클러스터에 연결](../container-service-connect.md)

> [!NOTE]
> 이 문서에서는 로컬 포트 80을 통해 DC/OS 클러스터에 터널링한다고 가정합니다.
>

## <a name="explore-the-dcos-ui"></a>DC/OS UI 탐색
SSH (Secure Shell) 터널이 [설정](../container-service-connect.md), http로 찾아보기: \/ /localhost/입니다. DC/OS 웹 UI가 로드되면서 클러스터에 대한 정보(예: 사용된 리소스, 활성 에이전트)가 표시되고 실행 중인 서비스를 보여 줍니다.

![DC/OS UI](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Marathon UI 탐색
Marathon UI를 보려면 http로 찾아보기: \/ /localhost/marathon입니다. 이 화면에서는 Azure 컨테이너 서비스 DC/OS 클러스터에 새 컨테이너 또는 다른 애플리케이션을 시작할 수 있습니다. 컨테이너 및 애플리케이션을 실행하는 방법에 대한 정보를 볼 수 있습니다.  

![Marathon UI](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Docker로 포맷된 컨테이너 배포
Marathon을 사용하여 새 컨테이너를 배포하려면, **애플리케이션 만들기**를 클릭하고 양식 탭에 다음 정보를 입력합니다.

| 필드 | 값 |
| --- | --- |
| ID |nginx |
| 메모리 | 32 |
| Image |nginx |
| 네트워크 |Bridged |
| 호스트 포트 |80 |
| 프로토콜 |TCP |

![새 애플리케이션 UI--일반](./media/container-service-mesos-marathon-ui/dcos4.png)

![새 애플리케이션 UI--Docker 컨테이너](./media/container-service-mesos-marathon-ui/dcos5.png)

![새 애플리케이션 UI--포트 및 서비스 검색](./media/container-service-mesos-marathon-ui/dcos6.png)

에이전트의 포트에 컨테이너 포트를 정적으로 매핑하려는 경우 JSON 모드를 사용해야 합니다. 이렇게 하려면 토글을 사용하여 새 애플리케이션 마법사를 **JSON 모드** 로 전환합니다. 그런 다음 애플리케이션 정의의 `portMappings` 섹션에 다음 설정을 입력합니다. 이 예제는 컨테이너의 포트 80을 DC/OS 에이전트의 포트80으로 바인딩합니다. 이렇게 변경한 후에 이 마법사를 JSON 모드에서 해제할 수 있습니다.

```none
"hostPort": 80,
```

![새 애플리케이션 UI--포트 80 예제](./media/container-service-mesos-marathon-ui/dcos13.png)

상태 검사를 설정하려면 **상태 검사** 탭에서 경로를 설정합니다.

![새 애플리케이션 UI--상태 검사](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

DC/OS 클러스터는 사설 및 공용 에이전트와 함께 배포됩니다. 클러스터를 인터넷에서 애플리케이션에 액세스할 수 있으려면 공용 에이전트에 애플리케이션을 배포해야 합니다. 이를 위해, 새 애플리케이션 마법사의 **선택 사항** 탭을 선택하고 **수락된 리소스 역할**에 **slave_public**을 입력합니다.

그런 다음 **애플리케이션 만들기**를 클릭합니다.

![새 애플리케이션 UI--공용 에이전트 설정](./media/container-service-mesos-marathon-ui/dcos14.png)

다시 Marathon 주 페이지에서 컨테이너에 대한 배포 상태를 볼 수 있습니다. 처음에는 **배포 중** 상태가 표시됩니다. 성공적으로 배포된 후에는 상태가 **실행 중**으로 변경됩니다.

![Marathon 기본 페이지 UI--컨테이너 배포 상태](./media/container-service-mesos-marathon-ui/dcos7.png)

DC/OS로 다시 전환 하면 웹 UI (http:\//localhost/), DC/OS 클러스터에서 작업 (이 경우 Docker로 포맷 된 컨테이너를) 실행 되 고 있는지를 표시 합니다.

![DC/OS 웹 UI--클러스터에서 실행 중인 작업](./media/container-service-mesos-marathon-ui/dcos8.png)

또한 태스크가 실행되는 클러스터 노드를 보려면 **노드** 탭을 클릭합니다.

![DC/OS 웹 UI--작업 클러스터 노드](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>컨테이너에 연결

이 예제에서 애플리케이션은 공용 에이전트 노드에서 실행되고 있습니다. 클러스터의 에이전트 FQDN으로 이동하여 인터넷에서 애플리케이션에 연결합니다. `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com` 여기서 다음이 적용됩니다.

* **DNSPREFIX** 는 클러스터를 배포할 때 제공한 DNS 접두사입니다.
* **REGION** 은 리소스 그룹이 있는 하위 지역입니다.

    ![인터넷의 Nginx](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>다음 단계
* [DC/OS 및 Marathon API 작업](container-service-mesos-marathon-rest.md)

* Mesos와 함께 Azure Container Service에 대해 자세히 알아보기

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
