---
title: 저장소 및 백업에 대 한 모범 사례
titleSuffix: Azure Kubernetes Service
description: AKS(Azure Kubernetes Services)의 스토리지, 데이터 암호화 및 백업에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 722fe393ad7637be20360463a4c3b6234224a036
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653973"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 스토리지 및 백업 모범 사례

AKS(Azure Kubernetes Services)에서 클러스터를 만들고 관리할 때 애플리케이션에 스토리지가 필요한 경우가 종종 있습니다. 애플리케이션에 적절한 스토리지를 제공하려면 pod의 성능 요구 및 액세스 방법을 이해하는 것이 중요합니다. AKS 노드 크기도 이러한 스토리지 선택에 영향을 줄 수 있습니다. 또한 연결된 스토리지에 대한 백업 방법을 게획하고 복원 프로세스를 테스트해야 합니다.

이 모범 사례 문서는 클러스터 운영자를 위한 스토리지 고려 사항을 중점적으로 설명합니다. 이 문서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * 사용할 수 있는 스토리지 유형
> * 스토리지 성능에 맞게 AKS 노드 크기를 정확히 조정하는 방법
> * 볼륨의 동적 및 정적 프로비전 간 차이
> * 데이터 볼륨을 백업 및 보호하는 방법

## <a name="choose-the-appropriate-storage-type"></a>적절한 스토리지 유형 선택

**모범 사례 지침** - 애플리케이션에서 올바른 스토리지를 선택할 필요성을 이해합니다. 프로덕션 워크로드를 위해서는 고성능의 SSD 기반 스토리지를 사용합니다. 여러 동시 연결에 대한 요구가 있을 때 네트워크 기반 스토리지를 계획합니다.

경우에 따라 애플리케이션에는 다양한 유형 및 속도의 스토리지가 필요합니다. 애플리케이션에 개별 pod에 연결되거나 여러 pod 간에 공유되는 스토리지가 필요한가요? 스토리지가 데이터에 대한 읽기 전용 액세스용인가요 아니면 구조화된 대량의 데이터를 쓰기 위한 것인가요? 이러한 스토리지는 사용할 가장 적절한 스토리지 유형을 결정해야 합니다.

다음 표에서는 사용 가능한 스토리지 유형 및 해당 기능을 설명합니다.

| 사용 사례 | 볼륨 플러그 인 | 한 번 읽기/쓰기 | 여러 번 읽기 전용 | 여러 번 읽기/쓰기 | Windows Server 컨테이너 지원 |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| 공유 구성       | Azure 파일   | 예 | 예 | 예 | 예 |
| 구조화된 앱 데이터        | Azure 디스크   | 예 | 아니요  | 아니요  | 예 |
| 구조화되지 않은 데이터, 파일 시스템 작업 | [BlobFuse][blobfuse] | 예 | 예 | 예 | 아니요 |

AKS에서 볼륨용으로 제공되는 두 가지 기본 유형의 스토리지는 Azure 디스크 또는 Azure 파일에서 지원됩니다. 보안을 강화하기 위해 두 가지 유형의 스토리지는 미사용 데이터를 암호화하는 Azure SSE(스토리지 서비스 암호화)를 기본적으로 사용합니다. 현재 AKS 노드 수준에서 Azure Disk Encryption을 사용하여 디스크를 암호화할 수 없습니다.

Azure Files 및 Azure 디스크는 모두 Standard 및 Premium 성능 계층에서 사용할 수 있습니다.

- *프리미엄* 디스크는 고성능 SSD(반도체 디스크)를 통해 지원됩니다. 프리미엄 디스크는 모든 프로덕션 워크로드에 권장됩니다.
- *표준* 디스크는 일반 회전 디스크(HDD)에서 지원되며 보관용 또는 자주 액세스하지 않는 데이터에 적합합니다.

애플리케이션 성능 요구 및 액세스 패턴을 이해하여 적절한 스토리지 계층을 선택합니다. Managed Disks 크기 및 성능 계층에 대한 자세한 내용은 [Azure Managed Disks 개요][managed-disks]를 참조하세요.

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>스토리지 클래스를 만들고 사용하여 애플리케이션 요구 정의

사용하는 스토리지 유형은 Kubernetes *스토리지 클래스*를 사용하여 정의합니다. 그런 후에는 스토리지 클래스가 pod 또는 배포 사양에서 참조됩니다. 적절한 스토리지를 만들고 pod에 연결하는 데 이러한 정의가 함께 사용됩니다. 자세한 내용은 [AKS의 스토리지 클래스][aks-concepts-storage-classes]를 참조하세요.

## <a name="size-the-nodes-for-storage-needs"></a>스토리지 요구에 맞게 노드 크기 조정

**모범 사례 지침** - 각 노드 크기는 최대 디스크 수를 지원합니다. 또한 다양한 노드 크기는 다양한 양의 로컬 스토리지 및 네트워크 대역폭을 제공합니다. 애플리케이션 요구 사항을 계획하여 적절한 크기의 노드를 배포하세요.

AKS 노드는 Azure VM으로 실행됩니다. 다양한 유형과 크기의 VM을 사용할 수 있습니다. 각 VM 크기는 서로 다른 양의 핵심 리소스(예: CPU 및 메모리)를 제공합니다. 이러한 VM 크기에는 최대 디스크 수가 연결될 수 있습니다. 또한 VM 크기에 따라 최대 로컬 및 연결된 디스크 IOPS(초당 입/출력 작업)와 같은 스토리지 성능이 다릅니다.

애플리케이션이 스토리지 솔루션으로 Azure 디스크가 필요한 경우 적절한 노드 VM 크기를 계획하고 선택합니다. CPU 및 메모리 양이 VM 크기를 선택할 때 유일한 고려 대상은 아닙니다. 스토리지 기능도 중요합니다. 예를 들어, *Standard_B2ms* and *Standard_DS2_v2* VM 크기 둘 다에는 비슷한 양의 CPU 및 메모리 리소스가 포함되어 있습니다. 그렇지만 잠재적인 스토리지 성능은 다음 표에 나오는 것처럼 다릅니다.

| 노드 유형 및 크기 | vCPU | 메모리(GiB) | 최대 데이터 디스크 수 | 캐시되지 않은 최대 IOPS | 캐시되지 않은 최대 처리량(MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1,920                  | 22.5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6,400                  | 96                             |

여기에서 *Standard_DS2_v2*는 연결되는 디스크 수를 2배로 늘릴 수 있도록 하며, 3~4배의 IOPS 및 디스크 처리량을 제공합니다. 핵심 컴퓨팅 리소스 및 비교한 비용만 살펴보면 *Standard_B2ms* VM 크기를 선택할 수 있으며 이 경우 스토리지 성능 저하 및 제한을 겪게 됩니다. 애플리케이션 개발 팀의 도움을 받아 스토리지 용량 및 성능 요구 사항을 이해하세요. 성능 요구를 충족하거나 능가하는 적절한 AKS 노드용 VM 크기를 선택합니다. 정기적으로 애플리케이션 기준을 조절하면서 필요에 맞게 VM 크기를 조정합니다.

사용 가능한 VM 크기에 대한 자세한 내용은 [Azure의 Linux Virtual Machines 크기][vm-sizes]를 참조하세요.

## <a name="dynamically-provision-volumes"></a>동적으로 볼륨 프로비전

**모범 사례 지침** - 관리 오버 헤드를 줄이고 크기를 조정할 수 있도록 하려면 고정적으로 영구 볼륨을 만들어 할당하지 마세요. 동적 프로비저닝을 사용합니다. 스토리지 클래스에서 pod가 삭제될 때 불필요한 스토리지 비용을 최소화하도록 적절한 회수 정책을 정의합니다.

Pod에 스토리지를 연결해야 할 경우에는 영구 볼륨을 사용합니다. 이러한 영구 볼륨은 수동으로 또는 동적으로 만들 수 있습니다. 영구 볼륨을 수동으로 만들면 관리 오버헤드가 추가되고 크기 조정 능력이 제한됩니다. 동적 영구 볼륨 프로비저닝을 사용하여 스토리지 관리를 간소화하고 애플리케이션이 필요에 따라 커지고 크기를 조정할 수 있도록 합니다.

![AKS(Azure Kubernetes Service) 클러스터의 영구적 볼륨 클레임](media/concepts-storage/persistent-volume-claims.png)

PVC(영구 볼륨 클레임)를 사용하면 필요에 따라 스토리지를 동적으로 만들 수 있습니다. 기본 Azure 디스크는 pod의 요청이 있으면 만들어집니다. Pod 정의에서 생성 하 고 지정 된 탑재 경로에 연결할 볼륨을 요청 합니다.

볼륨을 동적으로 만들고 사용하는 방법에 대한 개념은 [영구 볼륨 클레임][aks-concepts-storage-pvcs]을 참조하세요.

이러한 볼륨이 작동 중인 모습을 확인하려면 [Azure 디스크][dynamic-disks] 또는 [Azure 파일][dynamic-files]을 사용하여 영구 볼륨을 동적으로 만든 후 사용하는 방법을 참조하세요.

스토리지 클래스 정의의 일부로, 해당 *reclaimPolicy*를 설정합니다. 이 reclaimPolicy는 Pod가 삭제되고 영구적 볼륨이 더 이상 필요하지 않을 때 기본 Azure Storage 리소스의 동작을 제어합니다. 기본 스토리지 리소스는 삭제하거나 나중에 Pod에서 사용할 수 있도록 유지할 수 있습니다. reclaimPolicy를 *retain* 또는 *delete*로 설정할 수 있습니다. 애플리케이션 요구를 이해하고, 사용되어 요금이 청구된 미사용 스토리지의 양을 최소화하기 위해 보존되는 스토리지를 정기적으로 검사합니다.

스토리지 클래스 옵션에 대한 자세한 내용은 [스토리지 회수 정책][reclaim-policy]을 참조하세요.

## <a name="secure-and-back-up-your-data"></a>데이터 보안 및 백업

**모범 사례 지침** -Velero 또는 Azure Backup와 같이 저장소 유형에 적절 한 도구를 사용 하 여 데이터를 백업 합니다. 이러한 백업의 무결성 및 보안을 확인합니다.

애플리케이션이 디스크 또는 파일에 유지되는 데이터를 저장 및 사용할 경우에는 해당 데이터의 백업 또는 스냅샷을 정기적으로 생성해야 합니다. Azure 디스크는 기본 제공 스냅샷 기술을 사용할 수 있습니다. 스냅숏 작업을 수행 하기 전에 디스크에 쓰기를 플러시하는 응용 프로그램을 확인 해야 할 수 있습니다. [Velero][velero] 는 추가 클러스터 리소스 및 구성과 함께 영구 볼륨을 백업할 수 있습니다. [애플리케이션에서 상태를 제거][remove-state]할 수 없으면 영구 볼륨에서 데이터를 백업하고 복원 작업을 정기적으로 테스트하여 데이터 무결성 및 필요한 프로세스를 확인합니다.

다양한 데이터 백업 방법의 제한 사항을 이해하고 스냅샷 전에 데이터를 중지해야 하는지를 확인합니다. 데이터 백업을 수행해도 클러스터 배포의 애플리케이션 환경을 복원하지 못할 수 있습니다. 이러한 시나리오에 대한 자세한 내용은 [AKS의 비즈니스 연속성 및 재해 복구를 위한 모범 사례][best-practices-multi-region]를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS의 스토리지 모범 사례를 중점적으로 설명했습니다. Kubernetes의 스토리지 기본 사항에 대한 자세한 내용은 [AKS의 애플리케이션에 대한 스토리지 개념][aks-concepts-storage]을 참조하세요.

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
