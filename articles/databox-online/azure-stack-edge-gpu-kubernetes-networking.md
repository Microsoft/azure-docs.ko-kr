---
title: Azure Stack Edge 장치의 Kubernetes 네트워킹 이해 | Microsoft Docs
description: Kubernetes 네트워킹이 Azure Stack Edge 장치에서 작동 하는 방식을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: alkohli
ms.openlocfilehash: 4eab89710e031ead0a3758afd2367e60d26f395b
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268128"
---
# <a name="kubernetes-networking-in-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치의 Kubernetes 네트워킹

Azure Stack Edge 장치에서 계산 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터가 만들어지면 컨테이너 화 된 응용 프로그램을 Pod의 Kubernetes 클러스터에 배포할 수 있습니다. Kubernetes 클러스터의 Pod에는 네트워킹을 사용 하는 고유한 방법이 있습니다. 

이 문서에서는 Kubernetes 클러스터의 네트워킹을 일반적인 방법으로 설명 하 고 Azure Stack Edge 장치의 컨텍스트에서 특히 

## <a name="networking-requirements"></a>네트워킹 요구 사항

다음은 Kubernetes 클러스터에 배포 되는 일반적인 2 계층 앱의 예입니다.

- 앱에는 백 엔드에 웹 서버 프런트 엔드 및 데이터베이스 응용 프로그램이 있습니다. 
- 모든 pod에는 IP가 할당 되지만 이러한 ip는 pod의 다시 시작 및 장애 조치 시 변경 될 수 있습니다. 
- 각 앱은 여러 pod 구성 되며 모든 pod 복제본에서 트래픽의 부하 분산이 있어야 합니다. 

![Kubernetes 네트워킹 요구 사항](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-1.png)

위의 시나리오에서 다음과 같은 네트워킹 요구 사항이 발생 합니다.

 - 이름 또는 IP 주소를 통해 Kubernetes 클러스터 외부의 응용 프로그램 사용자가 외부 연결 응용 프로그램에 액세스할 수 있어야 합니다. 
 - Kubernetes 클러스터 내의 응용 프로그램 (예: 프런트 엔드 및 백 엔드 pod)은 서로 통신할 수 있어야 합니다.

위의 요구 사항을 모두 해결 하기 위해 Kubernetes 서비스를 도입 했습니다. 


## <a name="networking-services"></a>네트워킹 서비스

Kubernetes 서비스에는 다음과 같은 두 가지 유형이 있습니다. 

- **클러스터 IP 서비스** -응용 프로그램 pod에 대 한 일정 끝점을 제공 하는 것으로 생각 합니다. 이러한 서비스와 연결 된 모든 pod는 Kubernetes 클러스터 외부에서 액세스할 수 없습니다. 이러한 서비스에 사용 되는 IP 주소는 개인 네트워크의 주소 공간에서 제공 됩니다. 
- **부하 분산 장치 ip** -클러스터 ip 서비스와 마찬가지로 연결 된 ip는 외부 네트워크에서 제공 되며 Kubernetes 클러스터 외부에서 액세스할 수 있습니다.


<!--## Networking example for an app


![Kubernetes networking example](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-networking-2.png)

Each of these applications pods has a label associated with it. For example, the web server application pods have a label `app = WS` and the service has a label selector which the same as `app = WS`. Whenever a service of type load balancer or cluster IP is created, there is a control loop that runs in the master and publishes an endpoint corresponding to this service. This service uses a combination of labels and label selectors to discover the pods associated with this service. As a pod gets created, the new endpoint for the pod is added to the endpoint mapping. Whenever a pod is deleted, it gets deleted from the endpoint mapping. Using this endpoint controller, the service has a most up-to-date view of the pods that make up this application.

For discovery of applications within the cluster, Kubernetes cluster has an inbuilt DNS server pod. This is a cluster DNS that resolves service names to cluster IP. Anytime a cluster IP service is created, a DNS record is added to the DNS server that maps the name of the service to the cluster internal IP. That is how the applications within the cluster can discover each other. For load balancing, there is also the `kube-proxy`. This runs on every node and captures the traffic that comes in through the cluster IP and then distributes the traffic across the pods. 

When an application or the end user would first use the IP address associated with the service of type load balancer to discover the service. Then it would use the label select `app = WS` to discover the pods associated with the application. The `kube-proxy` component would then distribute the traffic and ensure that it hits one of the web server application pods. If the web server app wanted to talk to the database app, then it would simply use the name of the service and using the name and the DNS server pod, resolve the name to an IP address. Again using labels and label selector, it would discover the pods associated with the database application. The `kube-proxy` would then distribute the traffic across each of the database app nodes.-->


## <a name="kubernetes-networking-on-azure-stack-edge"></a>Azure Stack Edge의 Kubernetes 네트워킹

Calico, Metallb 및 Core DNS는 모두 Azure Stack에 지에 대 한 네트워킹에 설치 되는 구성 요소입니다. 

- **Calico** 은 개인 ip 범위에서 모든 POD에 ip 주소를 할당 하 고 이러한 pod에 대 한 네트워킹을 구성 하 여 한 노드의 pod가 다른 노드의 pod와 통신할 수 있도록 합니다. 
- **Metallb** 는 클러스터 내 pod에서 실행 되며, 부하 분산 장치 유형의 서비스에 IP 주소를 할당 합니다. 부하 분산 장치 IP 주소는 로컬 UI를 통해 제공 되는 서비스 Ip 범위에서 선택 됩니다. 
- **핵심 dns** -이 추가 기능을 통해 DNS 레코드 매핑 서비스 이름이 클러스터 IP 주소에 구성 됩니다.

Kubernetes 노드와 외부 서비스에 사용 되는 IP 주소는 장치의 로컬 UI에서 **계산 네트워크** 페이지를 통해 제공 됩니다.

![로컬 UI에서 IP 할당 Kubernetes](./media/azure-stack-edge-gpu-kubernetes-networking/kubernetes-ip-assignment-local-ui-1.png)

IP 할당은 다음에 대 한 것입니다.

- **Kubernetes Node ip**:이 IP 범위는 Kubernetes master 및 worker 노드에 사용 됩니다. 이러한 IP는 Kubernetes 노드가 서로 통신할 때 사용 됩니다.

- **Kubernetes external Service ip**:이 IP 범위는 Kubernetes 클러스터 외부에 노출 되는 외부 서비스 (Load Balancer 서비스 라고도 함)에 사용 됩니다.  


## <a name="next-steps"></a>다음 단계

Azure Stack Edge에서 Kubernetes 네트워킹을 구성 하려면 다음을 참조 하세요.

- [IoT Edge를 통해 Azure Stack Edge에서 외부적으로 상태 비저장 응용 프로그램을 노출](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)합니다.

- [Kuebctl를 통해 Azure Stack Edge에서 외부적으로 상태 비저장 응용 프로그램을 노출](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)합니다.
