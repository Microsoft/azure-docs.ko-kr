---
title: Azure Stack Edge 장치에서 Kubernetes 워크 로드 관리 이해 | Microsoft Docs
description: Azure Stack Edge 장치에서 Kubernetes 워크 로드를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 38c46bdcce64f726b3a7ddf74e0cfd10a14ba663
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268036"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Azure Stack Edge 장치에서 워크 로드 관리 Kubernetes

Azure Stack Edge 장치에서 계산 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터가 만들어지면 컨테이너 화 된 응용 프로그램을 Pod의 Kubernetes 클러스터에 배포할 수 있습니다. Kubernetes 클러스터에서 작업을 배포 하는 방법에는 여러 가지가 있습니다. 

이 문서에서는 Azure Stack Edge 장치에 워크 로드를 배포 하는 데 사용할 수 있는 다양 한 방법을 설명 합니다.

## <a name="workload-types"></a>작업 유형

Azure Stack Edge 장치에 배포할 수 있는 두 가지 일반적인 작업 유형은 상태 비저장 응용 프로그램 또는 상태 저장 응용 프로그램입니다.

- 상태 **비저장 응용 프로그램** 은 상태를 유지 하지 않고 영구 저장소에 데이터를 저장 하지 않습니다. 모든 사용자 및 세션 데이터는 클라이언트에 유지 됩니다. 상태 비저장 응용 프로그램의 몇 가지 예로는 웹 프런트 엔드 (예: Nginx) 및 기타 웹 응용 프로그램이 있습니다.

    Kubernetes 배포를 만들어 클러스터에 상태 비저장 응용 프로그램을 배포할 수 있습니다. 

- 상태 저장 **응용 프로그램** 은 해당 상태를 저장 해야 합니다. 상태 저장 응용 프로그램은 영구적 볼륨과 같은 영구 저장소를 사용 하 여 서버 또는 다른 사용자가 사용할 데이터를 저장 합니다. 상태 저장 응용 프로그램의 예로는 MongoDB와 같은 데이터베이스가 있습니다.

    Kubernetes 배포를 만들어 상태 저장 응용 프로그램을 배포할 수 있습니다. 

## <a name="deployment-flow"></a>배포 흐름

Azure Stack Edge 장치에 응용 프로그램을 배포 하려면 다음 단계를 수행 합니다. 
 
1. **액세스 구성**: 먼저 PowerShell runspace를 사용 하 여 사용자를 만들고, 네임 스페이스를 만들고, 해당 네임 스페이스에 대 한 사용자 액세스 권한을 부여 합니다.
2. **저장소 구성**: 다음에는 Azure Portal에서 Azure Stack Edge 리소스를 사용 하 여 배포할 상태 저장 응용 프로그램에 대해 정적 또는 동적 프로 비전을 사용 하는 영구 볼륨을 만듭니다.
3. **네트워킹 구성**: 마지막으로 서비스를 사용 하 여 외부 및 Kubernetes 클러스터 내에서 응용 프로그램을 노출 합니다.
 
## <a name="deployment-types"></a>배포 형식

작업을 배포 하는 기본 방법에는 세 가지가 있습니다. 이러한 각 배포 방법론을 사용 하면 장치의 고유한 네임 스페이스에 연결한 다음 상태 비저장 또는 상태 저장 응용 프로그램을 배포할 수 있습니다.

![Kubernetes 워크 로드 배포](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **로컬 배포**: `kubectl` Kubernetes를 배포 하는 데 사용할 수 있는와 같은 명령줄 액세스 도구를 통해 수행 됩니다 `yamls` . 파일을 사용 하 여 만든 Azure Stack Edge에서 Kubernetes 클러스터에 연결 합니다 `kubeconfig` . 자세한 내용은 [kubectl를 통해 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조 하세요.

- **IoT Edge 배포**: IoT Edge를 통해 Azure IoT Hub에 연결 합니다. 네임 스페이스를 통해 Azure Stack Edge 장치에서 Kubernetes 클러스터에 연결 합니다 `iotedge` . 이 네임 스페이스에 배포 된 IoT Edge 에이전트는 Azure에 대 한 연결을 담당 합니다. `IoT Edge deployment.json`Azure DevOps CI/CD를 사용 하 여 구성을 적용 합니다. 네임 스페이스 및 IoT Edge 관리는 클라우드 운영자를 통해 수행 됩니다.

- **Azure/Arc 배포**: azure Arc는 Kubernetes 클러스터에 응용 프로그램을 배포 하는 데 사용할 수 있는 하이브리드 관리 도구입니다. 를 통해 Azure Stack Edge 장치에서 Kubernetes 클러스터를 연결 합니다 `azure-arc namespace` . 에이전트는 Azure에 대 한 연결을 담당 하는이 네임 스페이스에 배포 됩니다. GitOps 기반 구성 관리를 사용 하 여 배포 구성을 적용 합니다. 또한 Azure Arc는 컨테이너에 대 한 Azure Monitor를 사용 하 여 클러스터를 보고 모니터링할 수 있습니다. 자세한 내용은 [Azure Arc 사용 Kubernetes?](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)을 참조 하세요.

## <a name="choose-the-deployment-type"></a>배포 유형 선택

응용 프로그램을 배포 하는 동안 다음 정보를 고려 하십시오.

- **단일 또는 여러 형식**: 단일 배포 옵션이 나 다양 한 배포 옵션의 조합을 선택할 수 있습니다.
- **클라우드 및 로컬**: 응용 프로그램에 따라 IoT Edge 및 Azure Arc를 통해 kubectl 또는 클라우드 배포를 통해 로컬 배포를 선택할 수 있습니다. 
    - 로컬 배포는 개발 시나리오에 더 적합 합니다. 로컬 배포를 선택 하면 Azure Stack Edge 장치가 배포 되는 네트워크로 제한 됩니다.
    - 배포할 수 있는 클라우드 에이전트가 있는 경우 클라우드 운영자를 배포 하 고 클라우드 관리를 사용 해야 합니다.
- **IoT Vs Azure Arc**: 배포를 선택 하는 것은 제품 시나리오의 의도에 따라서도 달라 집니다. IoT 또는 IoT 에코 시스템과 보다 긴밀 하 게 통합 된 응용 프로그램 또는 컨테이너를 배포 하는 경우 응용 프로그램을 배포 하는 IoT Edge 방법을 선택 해야 합니다. 기존 Kubernetes 배포가 있는 경우 Azure Arc가 선호 하는 선택입니다.


## <a name="next-steps"></a>다음 단계

Kubectl를 통해 응용 프로그램을 로컬로 배포 하려면 다음을 참조 하세요.

- [Kubectl를 통해 Azure Stack Edge에 상태 비저장 응용 프로그램을 배포](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)합니다.

IoT Edge를 통해 앱을 배포 하려면 다음을 참조 하세요.

- [IoT Edge를 통해 Azure Stack Edge에 샘플 모듈을 배포](azure-stack-edge-gpu-deploy-sample-module.md)합니다.

Azure Arc를 통해 앱을 배포 하려면 다음을 참조 하세요.

- [Azure Arc를 사용 하 여 응용 프로그램을 배포](azure-stack-edge-gpu-deploy-sample-module.md)합니다.
