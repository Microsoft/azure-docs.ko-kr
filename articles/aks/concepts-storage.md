---
title: 개념 - AKS(Azure Kubernetes Service)의 스토리지
description: 볼륨, 영구적 볼륨, 스토리지 클래스 및 클레임을 포함하여 AKS(Azure Kubernetes Service)의 스토리지에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: d6c14f78fd3cefa5ec41a686ca385639eb3fcb67
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549277"
---
# <a name="storage-options-for-applications-in-azure-kubernetes-service-aks"></a>애플리케이션에 대한 AKS(Azure Kubernetes Service)의 스토리지 옵션

AKS(Azure Kubernetes Service)에서 실행되는 애플리케이션은 데이터를 저장하고 검색해야 할 수 있습니다. 일부 애플리케이션 워크로드의 경우 이 데이터 스토리지는 Pod를 삭제할 때 더 이상 필요하지 않은 노드의 로컬 고속 스토리지를 사용할 수 있습니다. 다른 애플리케이션 워크로드에는 Azure 플랫폼 내의 더 일반적인 데이터 볼륨에서 유지되는 스토리지가 필요할 수 있습니다. 여러 Pod에서 동일한 데이터 볼륨을 공유하거나, Pod가 다른 노드에 다시 예약되는 경우 데이터 볼륨을 다시 연결해야 할 수 있습니다. 마지막으로, 중요한 데이터 또는 애플리케이션 구성 정보를 Pod에 삽입해야 할 수 있습니다.

![애플리케이션에 대한 AKS(Azure Kubernetes Service) 클러스터의 스토리지 옵션](media/concepts-storage/aks-storage-options.png)

이 문서에서는 AKS에서 스토리지를 애플리케이션에 제공하는 핵심 개념을 소개합니다.

- [볼륨](#volumes)
- [영구적 볼륨](#persistent-volumes)
- [스토리지 클래스](#storage-classes)
- [영구적 볼륨 클레임](#persistent-volume-claims)

## <a name="volumes"></a>볼륨

애플리케이션은 종종 데이터를 저장하고 검색할 수 있어야 합니다. Kubernetes는 일반적으로 개별 Pod를 사용 후 삭제 가능한 임시 리소스로 취급하므로 애플리케이션이 다양한 방식으로 데이터를 사용하고 필요에 따라 유지할 수 있습니다. *볼륨*은 Pod 간에 애플리케이션 수명 주기를 통해 데이터를 저장, 검색 및 유지하는 방법을 나타냅니다.

데이터를 저장하고 검색하는 기존 볼륨은 Azure Storage에서 지원하는 Kubernetes 리소스로 만들어집니다. 이러한 데이터 볼륨은 수동으로 만들어 Pod에 직접 할당하거나 Kubernetes에서 자동으로 만들 수 있습니다. 이러한 데이터 볼륨은 Azure Disks 또는 Azure Files를 사용할 수 있습니다.

- *Azure Disks*를 사용하여 *DataDisk* Kubernetes 리소스를 만들 수 있습니다. 디스크는 고성능 SSD를 통해 지원되는 Azure Premium 스토리지 또는 일반 HDD를 통해 지원되는 Azure Standard 스토리지를 사용할 수 있습니다. 대부분의 프로덕션 및 개발 워크로드의 경우 Premium 스토리지를 사용합니다. Azure 디스크는 *Readwriteonce 번*탑재 되므로 단일 pod 에서만 사용할 수 있습니다. 여러 pod에서 동시에 액세스할 수 있는 저장소 볼륨의 경우 Azure Files를 사용 합니다.
- *Azure Files*를 사용하여 Azure Storage 계정을 통해 지원되는 SMB 3.0 공유를 Pod에 탑재할 수 있습니다. Files를 사용하면 여러 노드 및 Pod 간에 데이터를 공유할 수 있습니다. 파일은 고성능 Ssd에서 지원 되는 regular Hdd 또는 Azure Premium storage에서 지 원하는 Azure Standard storage를 사용할 수 있습니다.
> [!NOTE] 
> Azure Files Kubernetes 1.13 이상을 실행 하는 AKS 클러스터의 premium storage를 지원 합니다.

Kubernetes에서 볼륨은 단순히 정보를 저장하고 검색할 수 있는 기존 디스크 이상의 것을 나타낼 수 있습니다. 또한 Kubernetes 볼륨은 컨테이너에서 사용할 수 있도록 데이터를 Pod에 삽입하는 방법으로도 사용할 수 있습니다. Kubernetes의 일반적인 추가 볼륨 유형은 다음과 같습니다.

- *emptyDir* - 일반적으로 Pod용 사용 후 삭제 공간으로 사용됩니다. Pod 내의 모든 컨테이너에서 볼륨의 데이터에 액세스할 수 있습니다. 이 볼륨 유형에 기록된 데이터는 Pod의 수명 동안만 유지되며, Pod가 삭제되면 해당 볼륨도 삭제됩니다. 이 볼륨은 일반적으로 기본 로컬 노드 디스크 저장소를 사용 하지만 노드의 메모리에만 존재할 수 있습니다.
- *비밀* - 암호와 같은 중요한 데이터를 Pod에 삽입하는 데 사용됩니다. 먼저 Kubernetes API를 사용하여 비밀을 만듭니다. Pod 또는 배포를 정의할 때 특정 비밀을 요청할 수 있습니다. 비밀은 예약된 Pod가 있고 비밀이 필요한 노드에만 제공되며, 디스크에 기록되지 않고 *tmpfs*에 저장됩니다. 비밀이 필요한 노드의 마지막 Pod가 삭제되면 노드의 tmpfs에서 비밀이 삭제됩니다. 비밀은 지정된 네임스페이스 내에 저장되며 동일한 네임스페이스 내의 Pod에서만 액세스할 수 있습니다.
- *configMap* - 애플리케이션 구성 정보와 같은 키-값 쌍 속성을 Pod에 삽입하는 데 사용됩니다. 컨테이너 이미지 내에서 애플리케이션 구성 정보를 정의하는 대신, 배포할 때 Pod의 새 인스턴스에 쉽게 업데이트하고 적용할 수 있는 Kubernetes 리소스로 정의할 수 있습니다. Secret을 사용하는 것과 마찬가지로, 먼저 Kubernetes API를 사용하여 ConfigMap 개체를 만듭니다. 그러면 Pod 또는 배포를 정의할 때 이 ConfigMap을 요청할 수 있습니다. ConfigMap은 지정된 네임스페이스 내에 저장되며 동일한 네임스페이스 내의 Pod에서만 액세스할 수 있습니다.

## <a name="persistent-volumes"></a>영구적 볼륨

Pod 수명 주기의 일부로 정의 및 생성되는 볼륨은 Pod가 삭제될 때까지만 존재합니다. 유지 관리 이벤트 중에 한 Pod가 다른 호스트에, 특히 StatefulSets에 다시 예약되는 경우 Pods에서 스토리지가 남아 있을 것으로 예상합니다. *PV(영구적 볼륨)* 는 Kubernetes API에서 만들어지고 관리되는 스토리지 리소스로 개별 Pod의 수명을 초과하여 존재할 수 있습니다.

Azure Disks 또는 Azure Files는 PersistentVolume을 제공하는 데 사용됩니다. 앞서의 '볼륨' 섹션에서 설명한 대로 Disks 또는 Files의 선택은 종종 데이터 또는 성능 계층에 대한 동시 액세스의 필요성에 의해 결정됩니다.

![AKS(Azure Kubernetes Service) 클러스터의 영구적 볼륨](media/concepts-storage/persistent-volumes.png)

PersistentVolume은 클러스터 관리자에서 *정적으로* 만들거나 Kubernetes API 서버에서 *동적으로* 만들 수 있습니다. Pod가 예약되어 있고 현재 사용할 수 없는 스토리지를 요청하는 경우 Kubernetes에서 기본 Azure Disks 또는 Azure Files 스토리지를 만들어 해당 Pod에 연결할 수 있습니다. 동적 프로비전은 *StorageClass*를 사용하여 만들어야 하는Azure 스토리지의 유형을 식별합니다.

## <a name="storage-classes"></a>스토리지 클래스

Premium 및 Standard와 같은 다른 계층의 스토리지를 정의하기 위해 *StorageClass*를 만들 수 있습니다. StorageClass는 *reclaimPolicy*도 정의합니다. 이 reclaimPolicy는 Pod가 삭제되고 영구적 볼륨이 더 이상 필요하지 않을 때 기본 Azure Storage 리소스의 동작을 제어합니다. 기본 스토리지 리소스는 삭제하거나 나중에 Pod에서 사용할 수 있도록 유지할 수 있습니다.

AKS에서 만드는 두 가지 초기 StorageClass는 다음과 같습니다.

- *default* - Azure Standard Storage를 사용하여 Managed Disk를 만듭니다. 회수 정책은 사용 중인 영구 볼륨이 삭제 될 때 기본 Azure 디스크가 삭제 됨을 나타냅니다.
- *managed-premium* - Azure Premium 스토리지를 사용하여 Managed Disk를 만듭니다. 회수 정책은 다시 사용 하는 영구 볼륨이 삭제 될 때 기본 Azure 디스크가 삭제 됨을 나타냅니다.

영구적 볼륨에 대해 지정된 StorageClass가 없는 경우 기본 StorageClass가 사용됩니다. 영구적 볼륨을 요청하는 경우 필요한 스토리지를 적절하게 사용하도록 주의해야 합니다. `kubectl`을 사용하여 추가로 필요한 StorageClass를 만들 수 있습니다. 다음 예제에서는 Premium Managed Disks를 사용하고, Pod가 삭제되면 기본 Azure Disk를 *유지*해야 한다고 지정합니다.

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-retain
provisioner: kubernetes.io/azure-disk
reclaimPolicy: Retain
parameters:
  storageaccounttype: Premium_LRS
  kind: Managed
```

## <a name="persistent-volume-claims"></a>영구적 볼륨 클레임

PersistentVolumeClaim은 특정 StorageClass의 Disk 또는 File 스토리지, 액세스 모드 및 크기를 요청합니다. 정의된 StorageClass에 따라 클레임을 처리할 기존 리소스가 없는 경우 Kubernetes API 서버는 Azure에서 기본 스토리지 리소스를 동적으로 프로비전할 수 있습니다. 볼륨이 Pod에 연결되면 Pod 정의에 볼륨 탑재가 포함됩니다.

![AKS(Azure Kubernetes Service) 클러스터의 영구적 볼륨 클레임](media/concepts-storage/persistent-volume-claims.png)

사용 가능한 스토리지 리소스가 요청한 Pod에 할당되면 PersistentVolume이 PersistentVolumeClaim에 *바인딩*됩니다. 클레임에 대한 영구적 볼륨의 1:1 매핑이 있습니다.

다음 YAML 매니페스트 예제에서는 *managed-premium* StorageClass를 사용하고 *5Gi* 디스크 크기를 요청하는 영구적 볼륨 클레임을 보여 줍니다.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: azure-managed-disk
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium
  resources:
    requests:
      storage: 5Gi
```

Pod 정의가 만들어지면 원하는 스토리지를 요청하는 영구적 볼륨 클레임이 지정됩니다. 또한 애플리케이션에서 데이터를 읽고 쓸 수 있도록 *volumeMount*도 지정합니다. 다음 YAML 매니페스트 예제에서는 이전의 영구적 볼륨 클레임을 사용하여 볼륨을 */mnt/azure*에 탑재하는 방법을 보여 줍니다.

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx
      volumeMounts:
      - mountPath: "/mnt/azure"
        name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: azure-managed-disk
```

## <a name="next-steps"></a>다음 단계

관련 모범 사례는 [AKS의 저장소 및 백업에 대한 모범 사례][operator-best-practices-storage]를 참조 하세요.

Azure Disks 또는 Azure Files를 사용하는 동적 및 정적 볼륨을 만드는 방법을 알아보려면 다음 방법 문서를 참조하세요.

- [Azure 디스크를 사용 하 여 정적 볼륨 만들기][aks-static-disks]
- [Azure Files를 사용 하 여 정적 볼륨 만들기][aks-static-files]
- [Azure 디스크를 사용 하 여 동적 볼륨 만들기][aks-dynamic-disks]
- [Azure Files를 사용 하 여 동적 볼륨 만들기][aks-dynamic-files]

Kubernetes 및 AKS 핵심 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Kubernetes/AKS 클러스터 및 워크 로드][aks-concepts-clusters-workloads]
- [Kubernetes/AKS id][aks-concepts-identity]
- [Kubernetes/AKS 보안][aks-concepts-security]
- [Kubernetes/AKS 가상 네트워크][aks-concepts-network]
- [Kubernetes/AKS 크기 조정][aks-concepts-scale]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[aks-static-disks]: azure-disk-volume.md
[aks-static-files]: azure-files-volume.md
[aks-dynamic-disks]: azure-disks-dynamic-pv.md
[aks-dynamic-files]: azure-files-dynamic-pv.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-network]: concepts-network.md
[operator-best-practices-storage]: operator-best-practices-storage.md
