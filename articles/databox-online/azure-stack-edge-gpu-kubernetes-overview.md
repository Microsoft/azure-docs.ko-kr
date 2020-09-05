---
title: Microsoft Azure Stack Edge 장치의 Kubernetes 클러스터 개요 | Microsoft Docs
description: Azure Stack Edge 장치에서 Kubernetes을 구현 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: be7e01644d7d1f232c50b1efbadf100671a262fe
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268111"
---
# <a name="kubernetes-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치의 Kubernetes

Kubernetes는 컨테이너 화 된 응용 프로그램을 오케스트레이션 하는 인기 있는 오픈 소스 플랫폼입니다. 이 문서에서는 Kubernetes에 대 한 개요를 제공 하 고, Azure Stack Edge 장치에서 Kubernetes가 작동 하는 방식을 설명 합니다. 

## <a name="about-kubernetes"></a>Kubernetes 정보 

Kubernetes는 컨테이너 기반 응용 프로그램 및 연결 된 네트워킹 및 저장소 구성 요소를 관리할 수 있는 쉽고 안정적인 플랫폼을 제공 합니다. Kubernetes를 사용 하 여 컨테이너 화 된 앱을 신속 하 게 빌드, 제공 및 확장할 수 있습니다.

개방형 플랫폼인 Kubernetes를 사용 하 여 원하는 프로그래밍 언어, OS 라이브러리 또는 메시징 버스로 응용 프로그램을 빌드할 수 있습니다. 릴리스를 예약 및 배포 하기 위해 Kubernetes는 기존 연속 통합 및 지속적인 업데이트 도구와 통합할 수 있습니다.

자세한 내용은 [Kubernetes 작동 방식](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)을 참조 하세요.

## <a name="kubernetes-on-azure-stack-edge"></a>Azure Stack Edge의 Kubernetes

Azure Stack Edge 장치에서 계산을 구성 하 여 Kubernetes 클러스터를 만들 수 있습니다. 계산 역할이 구성 되 면 마스터 및 작업자 노드를 포함 한 Kubernetes 클러스터가 모두 배포 되 고 구성 됩니다. 이 클러스터는 `kubectl` , IoT Edge 또는 Azure Arc를 통해 워크 로드 배포에 사용 됩니다.

Azure Stack Edge 장치는 인프라 클러스터를 구성 하는 1 개 노드 구성으로 사용할 수 있습니다. Kubernetes 클러스터는 인프라 클러스터와는 별개 이며 인프라 클러스터 위에 배포 됩니다. Kubernetes 클러스터가 응용 프로그램 오케스트레이션의 역할만 담당 하는 반면 인프라 클러스터는 Azure Stack Edge 장치에 대 한 영구 저장소를 제공 합니다. 

이 경우 Kubernetes 클러스터에는 마스터 노드와 작업자 노드가 있습니다. 클러스터의 Kubernetes 노드는 응용 프로그램 및 클라우드 워크플로를 실행 하는 가상 머신입니다. 

Kubernetes 마스터 노드는 클러스터에 대해 원하는 상태를 유지 관리 하는 일을 담당 합니다. 또한 마스터 노드는 컨테이너 화 된 응용 프로그램을 실행 하는 작업자 노드를 제어 합니다. 

다음 다이어그램은 1 노드 Azure Stack Edge 장치에 대 한 Kubernetes의 구현을 보여 줍니다. 1 개 노드 장치는 항상 사용 가능 하지 않으며, 단일 노드가 실패 하면 장치가 중단 됩니다. Kubernetes 클러스터도 중단 됩니다.

![1 노드 Azure Stack Edge 장치에 대 한 Kubernetes 아키텍처](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

Kubernetes 클러스터 아키텍처에 대 한 자세한 내용은 [Kubernetes core 개념](https://kubernetes.io/docs/concepts/architecture/)을 참조 하세요.


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>저장소 볼륨 프로 비전

응용 프로그램 워크 로드를 지원 하기 위해 Azure Stack Edge 장치 공유에 영구적 데이터에 대 한 저장소 볼륨을 탑재할 수 있습니다. 정적 및 동적 볼륨을 모두 사용할 수 있습니다. 

자세한 내용은 [Azure Stack Edge 장치에 대 한 Kubernetes 저장소](azure-stack-edge-gpu-kubernetes-storage.md)의 응용 프로그램에 대 한 저장소 프로 비전 옵션을 참조 하세요.

## <a name="networking"></a>네트워킹

Kubernetes 네트워킹을 사용 하면 컨테이너 간 네트워킹, pod-pod 네트워킹, pod-서비스 네트워킹 및 인터넷-서비스 네트워킹을 비롯 한 Kubernetes 네트워크 내에서 통신을 구성할 수 있습니다. 자세한 내용은 [Azure Stack Edge 장치에 대 한 네트워킹 모델 Kubernetes 네트워킹](azure-stack-edge-gpu-kubernetes-networking.md)을 참조 하세요.

## <a name="updates"></a>업데이트

새 Kubernetes 버전을 사용할 수 있게 되 면 Azure Stack Edge 장치에 사용할 수 있는 표준 업데이트를 사용 하 여 클러스터를 업그레이드할 수 있습니다. 업그레이드 하는 방법에 대 한 단계는 [Azure Stack Edge에 대 한 업데이트 적용](azure-stack-edge-gpu-install-update.md)을 참조 하세요.

## <a name="access-monitoring"></a>액세스, 모니터링

Azure Stack Edge 장치의 Kubernetes 클러스터는 RBAC (역할 기반 액세스 제어)를 허용 합니다. 자세한 내용은 [Azure Stack Edge 장치에서 Kubernetes 클러스터에 대 한 역할 기반 액세스 제어](azure-stack-edge-gpu-kubernetes-rbac.md)를 참조 하세요.

Kubernetes 대시보드를 통해 클러스터 및 리소스의 상태를 모니터링할 수도 있습니다. 컨테이너 로그도 사용할 수 있습니다. 자세한 내용은 [Kubernetes 대시보드를 사용 하 여 Azure Stack Edge 장치에서 Kubernetes 클러스터 상태 모니터링을](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)참조 하세요.

Azure Monitor는 컨테이너, 노드 및 컨트롤러에서 상태 데이터를 수집 하는 추가 기능으로도 사용할 수 있습니다. 자세한 내용은 [Azure Monitor 개요](../azure-monitor/overview.md) 를 참조 하세요.

<!--## Private container registry

Kubernetes on Azure Stack Edge device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>애플리케이션 관리

Azure Stack Edge 장치에서 Kubernetes 클러스터를 만든 후에는 다음 방법 중 하나를 통해이 클러스터에 배포 된 응용 프로그램을 관리할 수 있습니다.

- 를 통한 네이티브 액세스 `kubectl`
- IoT Edge 
- Azure Arc

이러한 방법에 대해서는 다음 섹션에서 설명 합니다.


### <a name="kubernetes-and-kubectl"></a>Kubernetes 및 kubectl

Kubernetes 클러스터가 배포 되 면 클라이언트 컴퓨터에서 로컬로 클러스터에 배포 된 응용 프로그램을 관리할 수 있습니다. 명령줄을 통해 *kubectl* 와 같은 네이티브 도구를 사용 하 여 응용 프로그램과 상호 작용 합니다. 

Kubernetes 클러스터를 배포 하는 방법에 대 한 자세한 내용은 [Azure Stack Edge 장치에 Kubernetes 클러스터 배포](azure-stack-edge-gpu-create-kubernetes-cluster.md)로 이동 하세요. 관리에 대 한 자세한 내용은 [kubectl를 사용 하 여 Azure Stack Edge 장치에서 Kubernetes 클러스터 관리](azure-stack-edge-gpu-create-kubernetes-cluster.md)로 이동 합니다.


### <a name="kubernetes-and-iot-edge"></a>Kubernetes 및 IoT Edge

또한 Kubernetes는 Kubernetes를 제공 하는 Azure Stack에 지 장치에 대 한 IoT Edge 워크 로드와 통합 될 수 있으며, 에코 시스템 및 IoT는 IoT 중심 에코 시스템을 제공 합니다. Kubernetes 계층은 Azure IoT Edge 워크 로드를 배포 하기 위한 인프라 계층으로 사용 됩니다. 모듈 수명 및 네트워크 부하 분산은 Kubernetes에서 관리 되는 반면 edge 응용 프로그램 플랫폼은 IoT Edge를 통해 관리 됩니다.

IoT Edge를 통해 Kubernetes 클러스터에 응용 프로그램을 배포 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요. 

- [IoT Edge를 통해 Azure Stack Edge 장치에 상태 비저장 응용 프로그램을 노출](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)합니다.


### <a name="kubernetes-and-azure-arc"></a>Kubernetes 및 Azure Arc

Azure Arc는 Kubernetes 클러스터에 응용 프로그램을 배포 하는 데 사용할 수 있는 하이브리드 관리 도구입니다. 또한 Azure Arc를 사용 하면 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터를 보고 모니터링할 수 있습니다. 자세한 내용은 [Azure Arc 사용 Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)을 참조 하세요. Azure Arc 가격 책정에 대 한 자세한 내용은 [Azure arc 가격 책정](https://azure.microsoft.com/services/azure-arc/#pricing)을 참조 하세요.


## <a name="next-steps"></a>다음 단계

- [Azure Stack Edge 장치의](azure-stack-edge-gpu-kubernetes-storage.md)Kubernetes 저장소에 대해 자세히 알아보세요.
- [Azure Stack Edge 장치의](azure-stack-edge-gpu-kubernetes-networking.md)Kubernetes 네트워킹 모델을 이해 합니다.
- Azure Portal에 [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) 를 배포 합니다.
