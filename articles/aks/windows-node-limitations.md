---
title: AKS (Azure Kubernetes Service)의 Windows Server 노드 풀에 대한 제한 사항
description: AKS (Azure Kubernetes Service)에서 Windows Server 노드 풀 및 응용 프로그램 작업을 실행할 때 알려진 제한 사항에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 3dd7399b68388d92d38b0f64c6e816cb94b3f295
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768574"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes 서비스 (AKS)에서 Windows Server 노드 풀 및 응용 프로그램 작업에 대한 현재 제한 사항

AKS (Azure Kubernetes Service)에서 노드에서 게스트 OS로 Windows Server를 실행 하는 노드 풀을 만들 수 있습니다. 이러한 노드는 .NET Framework에서 빌드된 것과 같은 네이티브 Windows 컨테이너 응용 프로그램을 실행할 수 있습니다. Linux 및 Windows OS에서 컨테이너 지원을 제공 하는 방법에는 중요 한 차이점이 있으므로 일부 일반적인 Kubernetes 및 pod 관련 기능은 현재 Windows 노드 풀에서 사용할 수 없습니다.

이 문서에서는 AKS의 Windows Server 노드에 대한 몇 가지 제한 사항 및 OS 개념을 간략하게 설명 합니다. Windows Server의 노드 풀은 현재 미리 보기 상태입니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 옵트인입니다. 미리 보기는 "있는 그대로" 및 "사용 가능한 상태로" 제공 되며 서비스 수준 계약 및 제한 된 보증에서 제외 됩니다. AKS 미리 보기는 최상의 노력에 대한 고객 지원에서 부분적으로 다룹니다. 이러한 기능은 프로덕션 용도로는 사용할 수 없습니다. 자세한 내용은 다음 지원 문서를 참조 하세요.
>
> * [AKS 지원 정책][aks-support-policies]
> * [Azure 지원 FAQ][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>지원 되는 Windows 운영 체제는 무엇 인가요?

AKS는 Windows Server 2019를 호스트 OS 버전으로 사용 하며 프로세스 격리만 지원 합니다. 다른 Windows Server 버전을 사용 하 여 작성 된 컨테이너 이미지는 지원 되지 않습니다. [Windows 컨테이너 버전 호환성][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows 및 Linux에서 Kubernetes?

창 서버 노드 풀 지원에는 Kubernetes 프로젝트의 업스트림 Windows Server에 포함 되는 몇 가지 제한 사항이 포함 되어 있습니다. 이러한 제한 사항은 AKS에 국한 되지 않습니다. Kubernetes의 Windows Server에 대한이 업스트림 지원에 대한 자세한 내용은 Kubernetes 프로젝트의 [Kubernetes의 windows 지원 소개][intro-windows] 문서에서 [지원 되는 기능 및 제한 사항][upstream-limitations] 섹션을 참조 하세요.

Kubernetes는 주로 Linux에 초점을 맞추고 있습니다. 업스트림 [Kubernetes.io][kubernetes] 웹 사이트에서 사용 되는 많은 예제는 Linux 노드에서 사용 하기 위한 것입니다. Windows Server 컨테이너를 사용 하는 배포를 만들 때 OS 수준에서 다음과 같은 사항을 고려해 야 합니다.

- **Id** -Linux는 정수 UID (사용자 id)를 사용 하 여 사용자를 식별 합니다. 사용자에 게는 로그온에 사용할 수 있는 영숫자 사용자 이름도 있으며, Linux는 사용자의 UID로 변환 됩니다. 마찬가지로 Linux는 정수 그룹 식별자 (GID)를 기준으로 사용자 그룹을 식별 하 고 그룹 이름을 해당 GID로 변환 합니다.
    - Windows Server에서는 Windows 보안 액세스 관리자 (SAM) 데이터베이스에 저장 된 더 큰 이진 SID (보안 식별자)를 사용 합니다. 이 데이터베이스는 호스트와 컨테이너 간에 공유 되거나 컨테이너 간에 공유 되지 않습니다.
- **파일 사용 권한** -Windows Server는 사용 권한 비트 마스크 및 UID + GID가 아닌 sid를 기반으로 하는 액세스 제어 목록을 사용 합니다.
- **파일 경로** -Windows Server의 규칙은/대신 \를 사용 하는 것입니다.
    - 볼륨을 탑재 하는 pod 사양에서 Windows Server 컨테이너에 대한 경로를 올바르게 지정 합니다. 예를 들어 Linux 컨테이너에서 */mnt/volume* 의 탑재 지점 대신 드라이브 문자 및 위치를 지정 합니다. 예를 들어 *K:* 드라이브로 탑재 하려면 */sk/shhhs* 와 같이 지정 합니다.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows에서 지원 되는 디스크 종류는 무엇 인가요?

Azure 디스크 및 Azure Files는 Windows Server 컨테이너에서 NTFS 볼륨으로 액세스 되는 지원 되는 볼륨 유형입니다.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>AKS에서 Windows 전용 클러스터를 실행할 수 있나요?

AKS 클러스터의 마스터 노드 (제어 평면)는 AKS 서비스에서 호스트 되므로 마스터 구성 요소를 호스트 하는 노드의 운영 체제에 노출 되지 않습니다. 모든 AKS 클러스터는 Linux 기반 기본 첫 번째 노드 풀로 생성 됩니다. 이 노드 풀에는 클러스터가 작동 하는 데 필요한 시스템 서비스가 포함 됩니다. 클러스터의 안정성 및 클러스터 작업을 수행 하는 기능을 보장 하기 위해 첫 번째 노드 풀에서 두 개 이상의 노드를 실행 하는 것이 좋습니다. AKS 클러스터 자체를 삭제 하지 않는 한 첫 번째 Linux 기반 노드 풀을 삭제할 수 없습니다.

## <a name="what-network-plug-ins-are-supported"></a>지원 되는 네트워크 플러그 인은 무엇 인가요?

Windows 노드 풀을 사용 하는 AKS 클러스터는 Azure CNI (고급) 네트워킹 모델을 사용 해야 합니다. Kubenet (기본) 네트워킹은 지원 되지 않습니다. 네트워크 모델의 차이점에 대한 자세한 내용은 [AKS의 응용 프로그램에 대한 네트워크 개념][azure-network-models]을 참조 하세요. -Azure CNI 네트워크 모델에는 IP 주소 관리에 대한 추가 계획 및 고려 사항이 필요 합니다. Azure CNI를 계획 하 고 구현 하는 방법에 대한 자세한 내용은 [AKS에서 AZURE cni 네트워킹 구성][configure-azure-cni]을 참조 하세요.

## <a name="can-i-change-the-min--of-pods-per-node"></a>노드당 pod의 수를 변경할 수 있나요?

클러스터의 안정성을 보장 하기 위해 현재 최소 30 pod 설정 해야 하는 요구 사항입니다.

## <a name="how-do-patch-my-windows-nodes"></a>Windows 노드를 패치 하는 방법

최신 패치 수정 및 업데이트를 얻으려면 AKS의 Windows Server 노드를 *업그레이드* 해야 합니다. AKS의 노드에서 Windows 업데이트를 사용할 수 없습니다. AKS는 패치를 사용할 수 있게 되는 즉시 새 노드 풀 이미지를 해제 합니다. 즉, 패치 및 핫픽스를 최신 상태로 유지 하기 위해 노드 풀을 업그레이드 해야 합니다. Kubernetes 버전이 사용 되는 경우에도 마찬가지입니다. AKS 릴리스 정보는 새 버전을 사용할 수 있는 시기를 표시 합니다. Windows Server 노드 풀을 업그레이드 하는 방법에 대한 자세한 내용은 [AKS에서 노드 풀 업그레이드][nodepool-upgrade]를 참조 하세요.

> [!NOTE]
> 업데이트 된 Windows Server 이미지는 노드 풀을 업그레이드 하기 전에 클러스터 업그레이드 (제어 평면 업그레이드)를 수행한 경우에만 사용 됩니다.
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Windows 노드 풀에 대한 서비스 사용자를 회전 어떻게 할까요??

미리 보기 중에는 Windows 노드 풀에서 서비스 사용자 회전을 미리 보기 제한으로 지원 하지 않습니다. 서비스 주체를 업데이트 하기 위해 새 Windows 노드 풀을 만들고 이전 풀에서 새 풀로 pod을 마이그레이션합니다. 이 작업이 완료 되 면 이전 노드 풀을 삭제 합니다.

## <a name="how-many-node-pools-can-i-create"></a>만들 수 있는 노드 풀은 몇 개입니까?

AKS 클러스터의 노드 풀은 최대 8 개까지 지정할 수 있습니다. 이러한 노드 풀에서 최대 400 노드를 사용할 수 있습니다. [노드 풀 제한][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Windows 노드 풀의 이름을 지정할 수 있는 항목

이름을 최대 6 자까지 유지 해야 합니다. 이는 AKS의 현재 제한 사항입니다.

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 노드에서 모든 기능이 지원 되나요?

네트워크 정책 및 kubenet 현재 Windows 노드에서 지원 되지 않습니다. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Windows 노드에서 수신 컨트롤러를 실행할 수 있나요?

예, Windows Server 컨테이너를 지 원하는 수신 컨트롤러는 AKS의 Windows 노드에서 실행할 수 있습니다.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Windows 노드에서 Azure Dev Spaces를 사용할 수 있나요?

Azure Dev Spaces 현재 Linux 기반 노드 풀 에서만 사용할 수 있습니다.

## <a name="can-my-windows-server-containers-use-gmsa"></a>내 Windows Server 컨테이너에서 gMSA를 사용할 수 있나요?

GMSA (그룹 관리 서비스 계정) 지원은 현재 AKS에서 사용할 수 없습니다.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Windows 노드 및 컨테이너가 있는 컨테이너에 대한 Azure Monitor를 사용할 수 있나요?

예, Windows 컨테이너에서 로그 (stdout)를 수집 하지 Azure Monitor 수 있습니다. Windows 컨테이너에서 stdout 로그의 라이브 스트림에 계속 연결할 수 있습니다.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>지원 되지 않는 기능이 필요한 경우 어떻게 하나요?

AKS에는 Windows에 필요한 모든 기능을 제공 하기 위해 노력 하 고 있지만, 차이가 발생 하는 경우 오픈 소스 업스트림 [AKS][aks-engine] 프로젝트는 windows 지원을 포함 하 여 Azure에서 Kubernetes를 실행 하는 쉽고 완전히 사용자 지정 가능한 방법을 제공 합니다. [AKS 로드맵][aks-roadmap]의 기능 로드맵을 확인 하세요.

## <a name="next-steps"></a>다음 단계

AKS에서 Windows Server 컨테이너를 시작 하려면 [AKS에서 Windows server를 실행 하는 노드 풀을 만듭니다][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
