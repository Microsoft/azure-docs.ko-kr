---
title: Edge 장치 Azure Stack Kubernetes 역할 기반 Access Control 이해 | Microsoft Docs
description: Kubernetes 역할 기반 Access Control Azure Stack에 지 장치에서 발생 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 697c686b61a86cb01327364ad73f30f88e2e151d
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268077"
---
# <a name="kubernetes-role-based-access-control-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 Kubernetes 역할 기반 Access Control


Azure Stack Edge 장치에서 계산 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes를 사용 하 여 RBAC (역할 기반 액세스 제어)를 사용 하 여 장치의 클러스터 리소스에 대 한 액세스를 제한할 수 있습니다.

이 문서에서는 Kubernetes에서 제공 하는 RBAC 시스템에 대 한 개요를 제공 하 고,가 Kubernetes RBAC를 Azure Stack Edge 장치에 구현 하는 방법을 제공 합니다. 

## <a name="rbac-for-kubernetes"></a>Kubernetes 용 RBAC

Kubernetes RBAC를 사용 하 여 사용자 또는 사용자 그룹을 할당 하거나, 리소스를 만들거나 수정 하는 등의 작업을 수행 하거나, 실행 중인 응용 프로그램 워크 로드에서 로그를 볼 수 있습니다. 이러한 권한은 단일 네임 스페이스로 범위를 지정 하거나 전체 클러스터에서 부여할 수 있습니다. 

Kubernetes 클러스터를 설정 하면이 클러스터에 해당 하는 단일 사용자가 만들어지며 클러스터 관리 사용자 라고 합니다.  `kubeconfig`파일은 클러스터 관리 사용자와 연결 됩니다. `kubeconfig`이 파일은 사용자를 인증 하기 위해 클러스터에 연결 하는 데 필요한 모든 구성 정보를 포함 하는 텍스트 파일입니다.

## <a name="namespaces-types"></a>네임 스페이스 형식

Kubernetes 리소스 (예: pod 및 배포)는 논리적으로 네임 스페이스로 그룹화 됩니다. 이러한 그룹은 Kubernetes 클러스터를 논리적으로 분할 하 고 리소스를 만들거나 보거나 관리 하기 위해 액세스를 제한 하는 방법을 제공 합니다. 사용자는 할당된 네임스페이스 내의 리소스와만 상호 작용할 수 있습니다.

네임 스페이스는 여러 사용자가 여러 팀 또는 프로젝트에 걸쳐 분산 된 환경에서 사용 하기 위한 것입니다. 소수의 사용자를 포함 하는 클러스터의 경우에는 네임 스페이스를 만들거나 고려할 필요가 없습니다. 제공 하는 기능이 필요한 경우 네임 스페이스 사용을 시작 합니다.

자세한 내용은 [Kubernetes 네임스페이스](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)를 참조하세요.


Azure Stack Edge 장치에는 다음 네임 스페이스가 있습니다.

- **시스템 네임 스페이스** -이 네임 스페이스는 DNS 및 프록시와 같은 네트워크 기능 또는 Kubernetes 대시보드의 핵심 리소스가 존재 하는 곳입니다. 일반적으로 사용자 고유의 애플리케이션은 이 네임스페이스에 배포하지 않습니다. 이 네임 스페이스를 사용 하 여 Kubernetes 클러스터 문제를 디버그 합니다. 

    장치에는 여러 시스템 네임 스페이스가 있으며 이러한 시스템 네임 스페이스에 해당 하는 이름은 예약 되어 있습니다. 다음은 예약 된 시스템 네임 스페이스 목록입니다. 
    - kube-시스템
    - metallb-시스템
    - d 네임 스페이스
    - default
    - kubernetes-대시보드
    - default
    - kube-임대
    - kube-public
    - iotedge
    - azure-호

    사용자가 만든 사용자 네임 스페이스에 대해 예약 된 이름을 사용 하지 않아야 합니다. 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **사용자 네임 스페이스** - **kubectl** 을 통해 응용 프로그램을 로컬로 배포 하는 데 사용할 수 있는 네임 스페이스입니다.
 
- **IoT Edge 네임 스페이스** -이 네임 스페이스에 연결 하 여 `iotedge` IoT Edge를 통해 응용 프로그램을 배포 합니다.

- **Azure arc 네임 스페이스** -이 `azure-arc` 네임 스페이스에 연결 하 여 azure arc를 통해 응용 프로그램을 배포 합니다. 

## <a name="namespaces-and-users"></a>네임 스페이스 및 사용자

실제 세계에서는 클러스터를 여러 네임 스페이스로 분할 하는 것이 중요 합니다. 

- **여러 사용자**: 여러 사용자가 있는 경우 여러 네임 스페이스에서 각 사용자가 서로 격리 된 특정 네임 스페이스에 응용 프로그램 및 서비스를 배포할 수 있도록 허용 합니다. 
- **단일 사용자**: 단일 사용자가 있는 경우에도 여러 네임 스페이스를 사용 하면 해당 사용자가 동일한 Kubernetes 클러스터에서 여러 버전의 응용 프로그램을 실행할 수 있습니다.

### <a name="roles-and-rolebindings"></a>역할 및 RoleBindings

Kubernetes에는 네임 스페이스 수준 및 클러스터 수준에서 사용자 또는 리소스에 대 한 권한을 부여할 수 있는 역할 및 역할 바인딩 개념이 있습니다. 

- **역할**: 사용자에 대 한 권한을 **역할로** 정의한 다음 **역할** 을 사용 하 여 네임 스페이스 내에서 사용 권한을 부여할 수 있습니다. 
- **Rolebindings**: 역할을 정의한 후에는 **rolebindings** 를 사용 하 여 지정 된 네임 스페이스에 대 한 역할을 할당할 수 있습니다. 

이 접근 방식을 사용 하면 사용자가 할당 된 네임 스페이스의 응용 프로그램 리소스에만 액세스할 수 있는 단일 Kubernetes 클러스터를 논리적으로 구분할 수 있습니다. 

## <a name="rbac-on-azure-stack-edge"></a>Azure Stack Edge의 RBAC

현재 RBAC 구현에서는 Azure Stack Edge를 사용 하 여 제한 된 PowerShell runspace에서 다음 작업을 수행할 수 있습니다.

- 네임 스페이스를 만듭니다.  
- 추가 사용자를 만듭니다.
- 만든 네임 스페이스에 대 한 관리자 액세스 권한을 부여 합니다. 클러스터 관리자 역할에 대 한 액세스 권한이 없거나 클러스터 차원 리소스의 보기에 대 한 액세스 권한이 없다는 점에 유의 하세요.
- `kubeconfig`Kubernetes 클러스터에 액세스 하기 위한 정보가 포함 된 파일을 가져옵니다.


Azure Stack Edge 장치에는 여러 시스템 네임 스페이스가 있으며, 파일을 사용 `kubeconfig` 하 여 해당 네임 스페이스에 액세스 하는 사용자 네임 스페이스를 만들 수 있습니다. 사용자는 이러한 네임 스페이스에 대 한 모든 권한을 가지 며 사용자를 만들거나 수정 하거나 사용자에 게 액세스 권한을 부여할 수 있습니다. 클러스터 관리자만 시스템 네임 스페이스 및 클러스터 차원 리소스에 대 한 모든 권한을 가집니다. 에는 `aseuser` 시스템 네임 스페이스에 대 한 읽기 전용 액세스 권한이 있습니다.

다음은 Azure Stack Edge 장치에서 RBAC의 구현을 보여 주는 다이어그램입니다.

![Azure Stack Edge 장치의 RBAC](./media/azure-stack-edge-gpu-kubernetes-rbac/rbac-view-1.png)

이 다이어그램에서 Alice, Bob 및 척 적은 할당 된 사용자 네임 스페이스에만 액세스할 수 있습니다 .이 경우에는 `ns1` `ns2` 각각, 및 `ns3` 입니다. 이러한 네임 스페이스 내에는 관리자 액세스 권한이 있습니다. 반면에 클러스터 관리자는 시스템 네임 스페이스 및 클러스터 차원 리소스에 대 한 관리자 액세스 권한을 가집니다.

`kubectl`명령을 사용 하 여 네임 스페이스를 만들거나, 사용자를 할당 하거나, 사용자를 할당 하거나, 파일을 다운로드할 수 있습니다 `kubeconfig` . 개략적인 워크플로는 다음과 같습니다.

1. 네임 스페이스 및 사용자를 만듭니다.  

    `New-HcsKubernetesNamespace -Namespace`  

2. 사용자를 만듭니다.  

    `New-HcsKubernetesUser -UserName`  

3. 만든 사용자와 네임 스페이스를 연결 합니다.  

    `Grant-HcsKubernetesNamespaceAccess -Namespace -UserName`  

4. 사용자 구성을에 저장 `C:\Users\<username>\.kube` 합니다.  

5. `kubectl`에 응용 프로그램 배포를 설치 하 고 시작 `kubectl` 합니다. 

자세한 단계별 지침은 [Azure Stack Edge에서 kuebctl를 통해 Kubernetes Cluster 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)로 이동 합니다.


Azure Stack Edge 장치에서 네임 스페이스와 사용자를 사용 하는 경우 다음 사항이 적용 됩니다.

- 시스템 네임 스페이스에 대 한 사용자 만들기, 사용자에 게 네임 스페이스 액세스 권한 부여 또는 취소와 같은 작업을 수행할 수 없습니다. 시스템 네임 스페이스의 예로는 `kube-system` ,,,,, 등이 `metallb-system` `kubernetes-dashboard` `default` `kube-node-lease` `kube-public` 있습니다. 시스템 네임 스페이스에는 `iotedge` (IoT Edge 네임 스페이스) 및 `azure-arc` (Azure Arc 네임 스페이스)와 같은 배포 유형에 예약 된 네임 스페이스도 포함 되어 있습니다.
- 사용자 네임 스페이스를 만들고 해당 네임 스페이스 내에서 추가 사용자를 만들고 해당 사용자에 대 한 네임 스페이스 액세스 권한을 부여 하거나 취소할 수 있습니다.
- 모든 시스템 네임 스페이스에 대 한 이름과 동일한 이름으로는 네임 스페이스를 만들 수 없습니다. 시스템 네임 스페이스의 이름은 예약 되어 있습니다.  
- 다른 사용자 네임 스페이스에서 이미 사용 중인 이름을 가진 사용자 네임 스페이스를 만들 수 없습니다. 예를 들어를 만든가 있는 경우 `test-ns` 다른 네임 스페이스를 만들 수 없습니다 `test-ns` .
- 이미 예약 된 이름의 사용자를 만들 수는 없습니다. 예를 들어 `aseuser` 는 예약 된 클러스터 관리자 이므로 사용할 수 없습니다.


## <a name="next-steps"></a>다음 단계

사용자를 만들고 네임 스페이스를 만들고 사용자에 게 네임 스페이스에 대 한 액세스 권한을 부여 하는 방법을 이해 하려면 [kubectl를 통해 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)를 참조 하세요.

