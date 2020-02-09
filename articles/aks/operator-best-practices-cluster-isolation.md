---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 클러스터 격리
description: AKS(Azure Kubernetes Services)의 클러스터 격리에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: cb15f637337df05c61eeac611286b49e23b6adac
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549192"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 클러스터 격리 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 관리할 경우 종종 팀 및 워크로드를 격리해야 합니다. AKS는 다중 테넌트 클러스터를 실행하고 리소스를 격리하는 방법의 유연성을 제공합니다. Kubernetes 투자를 최대화하려면 이러한 다중 테넌트 및 격리 기능을 이해하고 구현해야 합니다.

이 모범 사례 문서는 클러스터 운영을 위한 격리를 중점적으로 설명합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 다중 테넌트 클러스터 및 리소스 격리 계획
> * AKS 클러스터에서 논리적 또는 물리적 격리 사용

## <a name="design-clusters-for-multi-tenancy"></a>다중 테넌트용 클러스터 설계

Kubernetes는 동일한 클러스터에서 팀 및 워크로드를 논리적으로 격리할 수 있는 기능을 제공합니다. 목표는 각 팀에 필요한 리소스로 범위가 지정된 가장 적은 권한을 제공하는 것입니다. Kubernetes의 [네임 스페이스][k8s-namespaces] 는 논리적 격리 경계를 만듭니다. 격리 및 다중 테넌트에 대한 추가 Kubernetes 기능 및 고려 사항은 다음과 같습니다.

* **예약**에는 리소스 할당량 및 Pod 중단 예산과 같은 기본 기능 사용이 포함됩니다. 이러한 기능에 대한 자세한 내용은 [AKS의 기본 scheduler 기능에 대한 모범 사례][aks-best-practices-scheduler]를 참조 하세요.
  * 더 고급 스케줄러 기능에는 taint/toleration, 노드 선택기 및 노드/Pod 선호도 또는 선호도 방지가 포함됩니다. 이러한 기능에 대한 자세한 내용은 [AKS의 advanced scheduler 기능 모범 사례][aks-best-practices-advanced-scheduler]를 참조 하세요.
* **네트워킹**에는 네트워크 정책을 사용하여 Pod 내/외부 트래픽 흐름을 제어하는 기능이 포함됩니다.
* **인증 및 권한 부여**에는 Azure Key Vault에서 RBAC(역할 기반 액세스 제어) 및 Azure AD(Active Directory) 통합, Pod ID 및 비밀을 사용하는 기능이 포함됩니다. 이러한 기능에 대한 자세한 내용은 [AKS의 인증 및 권한 부여에 대한 모범 사례][aks-best-practices-identity]를 참조 하세요.
* **컨테이너**에는 취약성을 위한 Pod 보안 정책, Pod 보안 컨텍스트, 스캔 이미지 및 런타임이 포함됩니다. 또한 컨테이너 액세스를 기본 노드로 제한하는 App Armor 또는 Seccomp(Secure Computing) 사용이 포함됩니다.

## <a name="logically-isolate-clusters"></a>논리적으로 클러스터 격리

**모범 사례 가이드** - 논리적 격리를 사용하여 팀 및 프로젝트를 분리합니다. 팀 또는 애플리케이션에 배포하는 물리적 AKS 클러스터 수를 최소화해 보세요.

논리적 격리를 사용하면 여러 워크로드, 팀 또는 환경에 단일 AKS 클러스터를 사용할 수 있습니다. Kubernetes [네임 스페이스][k8s-namespaces] 는 워크 로드 및 리소스에 대한 논리적 격리 경계를 형성 합니다.

![AKS에서 Kubernetes 클러스터의 논리적 격리](media/operator-best-practices-cluster-isolation/logical-isolation.png)

클러스터의 논리적 분리는 일반적으로 클러스터를 물리적으로 분리하는 것보다 더 높은 Pod 밀도를 제공합니다. 클러스터에서 유휴 상태인 여유 컴퓨팅 용량이 부족합니다. Kubernetes 클러스터 자동 크기 조정기와 결합된 경우에는 요구에 맞게 노드 수를 강화 또는 규모 축소할 수 있습니다. 이 자동 크기 조정의 모범 사례 접근 방식을 사용하면 필요한 만큼만 노드를 실행하고 비용을 최소화할 수 있습니다.

AKS 또는 다른 곳의 Kubernetes 환경은 악의적인 다중 테넌트 사용에 대해 완전히 안전하지 않습니다. 다중 테넌트 환경에서 여러 테넌트가 일반적인 공유 인프라에서 작동 하 고 있습니다. 결과적으로 모든 테넌트를 신뢰할 수 없는 경우에는 다른 테넌트가 다른 테넌트의 보안 및 서비스에 영향을 주지 않도록 추가 계획을 수행 해야 합니다. *Pod 보안 정책*과 같은 추가 보안 기능과 노드에 대해 보다 세분화된 RBAC(역할 기반 액세스 제어)를 사용하면 악용이 더 어려워집니다. 그러나 악의적인 다중 테넌트 워크로드를 실행할 때 진정한 보안을 위해서는 하이퍼바이저가 신뢰할 수 있는 유일한 보안 수준입니다. Kubernetes의 보안 도메인은 개별 노드가 아닌 전체 클러스터가 됩니다. 이러한 유형의 악의적인 다중 테넌트 워크로드의 경우 물리적으로 격리된 클러스터를 사용해야 합니다.

## <a name="physically-isolate-clusters"></a>물리적으로 클러스터 격리

**모범 사례 가이드** - 개별 팀 또는 애플리케이션 배포에 대한 물리적 격리 사용을 최소화합니다. 대신에 이전 섹션에서 설명한 대로 ‘논리적’ 격리를 사용합니다.

일반적인 클러스터 격리 방법은 물리적으로 분리된 AKS 클러스터를 사용하는 것입니다. 이 격리 모델에서는 팀 또는 워크로드에 자체 AKS 클러스터가 할당됩니다. 이 접근 방식은 워크로드 또는 팀을 격리하는 가장 간편한 방법처럼 보이지만, 추가적인 관리 및 재무 오버헤드가 더해집니다. 이제 이러한 여러 클러스터를 유지 관리해야 하고 개별적으로 액세스를 제공하고 권한을 할당해야 합니다. 또한 모든 개별 노드에 대한 요금이 청구됩니다.

![AKS에서 개별 Kubernetes 클러스터의 물리적 격리](media/operator-best-practices-cluster-isolation/physical-isolation.png)

물리적으로 분리된 클러스터는 일반적으로 Pod 밀도가 낮습니다. 각 팀 또는 워크로드에는 고유한 AKS 클러스터가 있으므로 클러스터는 대개 컴퓨팅 리소스를 사용하여 초과 프로비전됩니다. 종종 작은 수의 pod가 해당 노드에 예약 됩니다. 노드의 사용되지 않은 용량은 다른 팀에서 개발 중인 애플리케이션 또는 서비스에 사용할 수 없습니다. 이러한 초과 리소스로 인해 물리적으로 분리된 클러스터에서 추가 비용이 발생합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 클러스터 격리를 중점적으로 설명했습니다. AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [기본 Kubernetes scheduler 기능][aks-best-practices-scheduler]
* [Advanced Kubernetes scheduler 기능][aks-best-practices-advanced-scheduler]
* [인증 및 권한 부여][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
