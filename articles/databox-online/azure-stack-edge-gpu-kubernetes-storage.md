---
title: Azure Stack Edge 장치의 Kubernetes 저장소 관리 이해 | Microsoft Docs
description: Kubernetes 저장소 관리가 Azure Stack Edge 장치에서 어떻게 발생 하는지 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 57574b66ddb20e592a5979a4b827347f7c8e09af
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268094"
---
# <a name="kubernetes-storage-management-on-your-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 저장소 관리 Kubernetes

Azure Stack Edge 장치에서 계산 역할을 구성할 때 Kubernetes 클러스터가 만들어집니다. Kubernetes 클러스터가 만들어지면 컨테이너 화 된 응용 프로그램을 pod의 Kubernetes 클러스터에 배포할 수 있습니다. Kubernetes 클러스터에서 pod에 저장소를 제공 하는 고유한 방법이 있습니다. 

이 문서에서는 Kubernetes 클러스터에서 저장소를 프로 비전 하는 방법에 대해 설명 합니다 .이 방법은 특히 Azure Stack Edge 장치의 컨텍스트에서 제공 합니다. 

## <a name="storage-requirements-for-kubernetes-pods"></a>Kubernetes pod에 대 한 저장소 요구 사항

Kubernetes pod는 상태 비저장 이지만 응용 프로그램이 실행 하는 응용 프로그램은 일반적으로 상태 저장입니다. Pod는 수명이 짧고 Kubernetes 노드 간에 다시 시작, 장애 조치 (failover) 또는 이동을 수행할 수 있으므로 pod와 연결 된 저장소의 경우 다음 요구 사항을 충족 해야 합니다. 

저장소는 다음을 수행 해야 합니다.

- Pod 외부에 살고 있습니다.
- Pod 수명 주기와 독립적 이어야 합니다.
- 모든 Kubernetes 노드에서 액세스할 수 있습니다.

Kubernetes에 대해 저장소를 관리 하는 방법을 이해 하려면 다음 두 가지 API 리소스를 이해 해야 합니다. 

- **PersistentVolume (PV)**: Kubernetes 클러스터의 저장소 부분입니다. Kubernetes 저장소는로 정적으로 프로 비전 될 수 있습니다 `PersistentVolume` . 로 동적으로 프로 비전 할 수도 있습니다  `StorageClass` .

- **PersistentVolumeClaim (PVC)**: 사용자의 저장소에 대 한 요청입니다. Pvc는 PV 리소스를 사용 합니다. Pvc는 특정 크기와 액세스 모드를 요청할 수 있습니다. 

    사용자가 다양 `PersistentVolumes` 한 문제에 대 한 다양 한 속성을 필요로 하기 때문에 클러스터 관리자는 `PersistentVolumes` 크기 및 액세스 모드 보다 더 다양 한 방법으로 다양 한을 제공할 수 있어야 합니다. 이러한 요구 사항에는 리소스가 필요 `StorageClass` 합니다.

저장소 프로 비전은 정적 이거나 동적일 수 있습니다. 각 프로 비전 유형에 대해서는 다음 섹션에서 설명 합니다.

## <a name="staticprovisioning"></a>정적 프로 비전

Kubernetes cluster admins는 저장소를 정적으로 프로 비전 할 수 있습니다. 이렇게 하려면 SMB/NFS 파일 시스템를 기반으로 하는 저장소 백 엔드를 사용 하거나 온-프레미스 환경에서 네트워크를 통해 로컬로 연결 된 iSCSI 디스크를 사용 하거나 클라우드에서 Azure Files 또는 Azure 디스크를 사용할 수 있습니다. 이 유형의 저장소는 기본적으로 프로 비전 되지 않으며, 클러스터 관리자는이 프로 비전을 계획 하 고 관리 해야 합니다. 
 
Kubernetes에서 정적으로 프로 비전 된 저장소를 사용 하는 방법을 보여 주는 다이어그램은 다음과 같습니다. 

![PersistentVolumes를 통한 정적 프로 비전](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-persistent-volumes-1.png)

다음 단계가 수행 됩니다. 

1. **저장소 프로 비전**: 클러스터 관리자가 저장소를 프로 비전 합니다. 이 예제에서 클러스터 관리자는 이러한 공유에 해당 하는 Kubernetes 클러스터에 영구적 볼륨 개체를 자동으로 만드는 SMB 공유를 하나 이상 만듭니다. 

1. **클레임 저장소**: 저장소를 요청 하는 PVC 배포를 제출 합니다. 저장소에 대 한이 클레임은 PVC (PersistentVolumeClaim)입니다. PV의 크기 및 액세스 모드가 PVC의 크기와 일치 하는 경우 PVC는 PV에 바인딩됩니다. 1 대 1로 된 PVC 및 PV 맵

1. **컨테이너에 Pvc 탑재**: PVC가 PV에 바인딩되면이 pvc를 컨테이너의 경로에 탑재할 수 있습니다. 컨테이너의 응용 프로그램 논리가이 경로를 읽거나 쓸 때 데이터는 SMB 저장소에 기록 됩니다.
 

## <a name="dynamicprovisioning"></a>동적 프로비저닝

Kubernetes에서 정적으로 프로 비전 된 저장소를 사용 하는 방법을 보여 주는 다이어그램은 다음과 같습니다. 

![StorageClasses를 통한 동적 프로 비전](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-storage-classes-1.png)

다음 단계가 수행 됩니다. 

1. **저장소 클래스 정의**: 클러스터 관리자는 Kubernetes 클러스터의 운영 환경에 따라 저장소 클래스를 정의 합니다. 또한 클러스터 관리자는 Kubernetes 클러스터에 배포 된 다른 pod 또는 응용 프로그램에 해당 하는 provisioner를 배포 합니다. Provisioner에는 공유를 동적으로 프로 비전 하는 모든 세부 정보가 있습니다.  

1. **클레임 저장소**: 저장소를 요청 하는 응용 프로그램을 제출 합니다. 이 저장소 클래스 참조를 사용 하 여 PVC를 만든 후에는 provisioner가 호출 됩니다. 

1. **동적으로 저장소 프로 비전**: provisioner는 로컬 디스크 저장소와 연결 된 공유를 동적으로 만듭니다. 공유를 만든 후에는이 공유에 해당 하는 PV 개체도 자동으로 만듭니다.

1. **컨테이너에 Pvc 탑재**: PVC를 PV에 바인딩한 후에는 정적 프로 비전과 동일한 방식으로 경로에 pvc를 탑재 하 고 공유에서 읽거나 쓸 수 있습니다.


## <a name="storage-provisioning-on-azure-stack-edge"></a>Azure Stack Edge에서 저장소 프로 비전

Azure Stack Edge 장치에서 정적으로 프로 비전 `PersistentVolumes` 된 장치는 장치의 저장소 기능을 사용 하 여 생성 됩니다. 공유를 프로 비전 하 고 **Edge compute로 공유** 옵션을 사용 하는 경우이 작업은 Kubernetes 클러스터에서 자동으로 PV 리소스를 만듭니다.

![정적 프로 비전을 위한 Azure Portal의 로컬 공유 만들기](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-2.png)

클라우드 계층화를 사용 하려면에 지 계산 옵션을 사용 하도록 설정 하 여 Edge 클라우드 공유를 만들 수 있습니다. 이 공유에 대해 PV가 자동으로 다시 생성 됩니다. 에 지 공유에 쓰는 모든 응용 프로그램 데이터는 클라우드에 계층화 됩니다. 

![정적 프로 비전을 위한 Azure Portal의 클라우드 공유 만들기](./media/azure-stack-edge-gpu-kubernetes-storage/static-provisioning-azure-portal-1.png)

SMB 및 NFS 공유를 모두 만들어 Azure Stack Edge 장치에서 PVs를 정적으로 프로 비전 할 수 있습니다. PV가 프로 비전 되 면이 저장소를 요청 하는 PVC를 제출 합니다. `yaml`저장소를 클레임 하 고 프로 비전 한 공유를 사용 하는 PVC 배포의 예는 다음과 같습니다.


```yml
kind: PersistentVolumeClaim 
apiVersion: v1 
metadata: 
  name: pvc-smb-flexvol 
spec: 
  accessModes: 
  - ReadWriteMany 
  resources: 
    requests: 
      storage: 10Gi 
  volumeName: <nfs-or-smb-share-name-here> 
  storageClassName: ""
```

자세한 내용은 [kubectl를 통해 Azure Stack Edge에서 정적 프로 비전을 통해 상태 저장 응용 프로그램 배포](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)를 참조 하세요.

Azure Stack Edge에는 `StorageClass` `ase-node-local` Kubernetes 노드에 연결 된 데이터 디스크 저장소를 사용 하는 라는 기본 제공도 있습니다. 이는 `StorageClass` 동적 프로비저닝을 지원 합니다. `StorageClass`Pod 응용 프로그램에서 참조를 만들 수 있으며 PV가 자동으로 만들어집니다. 자세한 내용은 쿼리할 [Kubernetes 대시보드](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md) 를 참조 하세요 `ase-node-local StorageClass` .

![Kubernetes 대시보드의 기본 제공 저장소 클래스](./media/azure-stack-edge-gpu-kubernetes-storage/dynamic-provisioning-builtin-storage-class-1.png)

자세한 내용은 [kuebctl를 통해 Azure Stack Edge에서 동적 프로 비전을 통해 상태 저장 응용 프로그램 배포](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)를 참조 하세요.

## <a name="choose-storage-type"></a>저장소 유형 선택

배포 하는 작업에 따라 저장소 유형을 선택 해야 할 수 있습니다. 

- `ReadWriteMany`여러 노드에서 볼륨을 읽기/쓰기로 탑재 하는에 대 한 액세스 모드를 원하는 경우 `PersistentVolumes` SMB/NFS 공유에 대해 정적 프로 비전을 사용 합니다.

- 배포 하는 응용 프로그램에 POSIX 준수 요구 사항이 있는 경우 (예: MongoDB, PostgreSQL, MySQL 또는 프로메테우스와 같은 응용 프로그램) 기본 제공 StorageClass를 사용 합니다. 액세스 모드가 `ReadWriteOnce` 이거나 볼륨이 단일 노드에서 읽기/쓰기로 탑재 됩니다. 


액세스 모드에 대 한 자세한 내용은 [Kubernetes 볼륨 액세스 모드](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)를 참조 하세요.


## <a name="next-steps"></a>다음 단계

을 정적으로 프로 비전 할 수 있는 방법을 이해 하려면 `PersistentVolume` 다음을 참조 하세요.

- [Kubectl를 통해 Azure Stack Edge에서 정적 프로 비전을 통해 상태 저장 응용 프로그램을 배포](azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes.md)합니다.

를 동적으로 프로 비전 하는 방법에 대 한 자세한 `StorageClass` 내용은 다음을 참조 하세요.

- [Kuebctl를 통해 Azure Stack Edge에서 동적 프로 비전을 통해 상태 저장 응용 프로그램을 배포](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)합니다.
