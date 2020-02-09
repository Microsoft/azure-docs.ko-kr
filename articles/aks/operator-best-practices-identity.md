---
title: 운영자 모범 사례 - AKS(Azure Kubernetes Services)의 ID
description: AKS(Azure Kubernetes Services)에서 클러스터의 인증 및 권한 부여를 관리하는 방법에 대한 클러스터 운영자 모범 사례 알아보기
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: mlearned
ms.openlocfilehash: 35cded14977988c80455a9c28a0e01e7913d58f4
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069509"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 인증 및 권한 부여 모범 사례

AKS(Azure Kubernetes Service)에서 클러스터를 배포 및 유지 관리하는 경우 리소스 및 서비스에 대한 액세스를 관리하는 방법을 구현해야 합니다. 이러한 컨트롤이 없으면 계정이 필요하지 않은 리소스 및 서비스에 액세스할 수 있습니다. 또한 변경하는 데 사용되는 자격 증명 세트를 추적하기 어려울 수 있습니다.

이 모범 사례 문서에서는 클러스터 운영자가 AKS 클러스터의 액세스 및 ID를 관리하는 방법을 중점적으로 설명합니다. 이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * Azure Active Directory를 사용하여 AKS 클러스터 사용자 인증
> * RBAC(역할 기반 액세스 제어)로 리소스 액세스 제어
> * 관리형 ID를 사용하여 다른 서비스와 함께 인증

## <a name="use-azure-active-directory"></a>Azure Active Directory 사용

**모범 사례 가이드** - Azure AD 통합을 사용하여 AKS 클러스터를 배포합니다. Azure AD를 사용하면 ID 관리 구성 요소가 중앙 집중화됩니다. 사용자 계정 또는 그룹 상태의 변경 내용은 AKS 클러스터에 액세스할 때 자동으로 업데이트됩니다. 다음 섹션에 설명된 대로 Role 또는 ClusterRole과 Binding을 사용하여 사용자 또는 그룹의 범위를 필요한 최소한의 권한으로 지정합니다.

Kubernetes 클러스터의 개발자 및 애플리케이션 소유자는 다양한 다른 리소스에 액세스해야 합니다. Kubernetes는 어떤 사용자가 어떤 리소스를 조작할지 제어할 수 있는 ID 관리 솔루션을 제공하지 않습니다. 대신 일반적으로 클러스터를 기존 ID 솔루션과 통합합니다. Azure AD(Active Directory)는 기업환경에 맞게 준비된 ID 관리 솔루션을 제공하며 AKS 클러스터와 통합할 수 있습니다.

AKS의 Azure AD 통합형 클러스터를 사용하여 리소스에 대한 액세스 권한을 정의하는 *Role* 또는 *ClusterRole*을 만듭니다. 그런 다음, Azure AD의 사용자 또는 그룹에 역할을 ‘바인딩’ 합니다. 이러한 Kubernetes RBAC(역할 기반 액세스 제어)는 다음 섹션에서 설명합니다. Azure AD 통합 및 리소스 액세스 제어 방법은 다음 다이어그램에서 볼 수 있습니다.

![AKS와 Azure Active Directory 통합을 위한 클러스터 수준 인증](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. 개발자는 Azure AD로 인증합니다.
1. Azure AD 토큰 발급 엔드포인트가 액세스 토큰을 발급합니다.
1. 개발자는 `kubectl create pod`과 같은 Azure AD 토큰을 사용하여 작업을 수행합니다.
1. Kubernetes는 Azure Active Directory를 사용하여 토큰의 유효성을 검사하고 개발자의 그룹 멤버 자격을 가져옵니다.
1. Kubernetes RBAC(역할 기반 액세스 제어) 및 클러스터 정책이 적용됩니다.
1. 개발자 요청의 성공 여부는 Azure AD 그룹 멤버 자격 및 Kubernetes RBAC/정책의 이전 유효성 검사에 따라 결정됩니다.

Azure AD를 사용 하는 AKS 클러스터를 만들려면 [AKS와 Azure Active Directory 통합][aks-aad]을 참조 하세요.

## <a name="use-role-based-access-controls-rbac"></a>RBAC(역할 기반 액세스 제어) 사용

**모범 사례 가이드** - Kubernetes RBAC을 사용하여 사용자 또는 그룹이 클러스터의 리소스를 대상으로 가져야 하는 권한을 정의합니다. 필요한 최소 권한을 할당하는 역할 및 바인딩을 만듭니다. Azure AD와 통합되므로 사용자 상태 또는 그룹 멤버 자격의 변경 내용이 자동으로 업데이트되고 클러스터 리소스에 대한 액세스가 최신 상태입니다.

Kubernetes에서는 클러스터의 리소스에 대한 액세스를 세부적으로 제어할 수 있습니다. 권한은 클러스터 수준에서 정의되거나 특정 네임스페이스에 대해 정의될 수 있습니다. 관리할 수 있는 리소스 및 관리에 필요한 권한을 정의할 수 있습니다. 이러한 역할은 바인딩이 있는 사용자 또는 그룹에 적용 됩니다. *역할*, *Clusterroles*및 *바인딩에*대한 자세한 내용은 [Azure Kubernetes 서비스에 대한 액세스 및 id 옵션 (AKS)][aks-concepts-identity]을 참조 하세요.

예를 들어 다음 예제 YAML 매니페스트와 같이 *finance-app*이라는 네임스페이스에서 리소스에 대한 전체 액세스 권한을 부여하는 Role을 만들 수 있습니다.

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

다음 YAML 매니페스트와 같이 Azure AD 사용자 *developer1\@contoso.com* 를 rolebinding에 바인딩하는 rolebinding이 생성 됩니다.

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

*Developer1\@contoso.com* 가 AKS 클러스터에 대해 인증 되 면 *재무 앱* 네임 스페이스의 리소스에 대한 모든 권한을 갖습니다. 이 방식으로 리소스에 대한 액세스를 논리적으로 분리하고 제어합니다. 이전 섹션에 설명된 대로 Kubernetes RBAC는 Azure AD 통합과 함께 사용해야 합니다.

Azure AD 그룹을 사용 하 여 RBAC를 사용 하는 Kubernetes 리소스에 대한 액세스를 제어 하는 방법을 보려면 [AKS에서 역할 기반 액세스 제어 및 Azure Active Directory id를 사용 하 여 클러스터 리소스에 대한 액세스 제어][azure-ad-rbac]를 참조 하세요.

## <a name="use-pod-identities"></a>Pod ID 사용

**모범 사례 가이드** - 고정된 자격 증명은 노출이나 남용의 위험이 있으므로 Pod 또는 컨테이너 이미지 내에서 사용하지 마세요. 대신 Pod ID를 사용하여 중앙 Azure AD ID 솔루션을 통해 액세스를 자동으로 요청합니다. Pod id는 Linux pod 및 컨테이너 이미지만 사용 하기 위한 것입니다.

Pod가 Cosmos DB, Key Vault 또는 Blob Storage와 같은 다른 Azure 서비스에 액세스해야 하는 경우 Pod에는 액세스 자격 증명이 필요합니다. 이러한 액세스 자격 증명은 컨테이너 이미지를 사용하여 정의하거나 Kubernetes 비밀로 삽입할 수 있지만, 수동으로 만들고 할당해야 합니다. 대개 인증서는 Pod 전체에 재사용되며 정기적으로 회전되지 않습니다.

Azure 리소스에 대한 관리 id (현재 연결 된 AKS 오픈 소스 프로젝트로 구현 됨)를 통해 Azure AD를 통해 서비스에 대한 액세스를 자동으로 요청할 수 있습니다. Pod에 대한 자격 증명을 수동으로 정의하는 대신, 액세스 토큰을 실시간으로 요청하며 이를 사용하여 할당된 서비스에만 액세스할 수 있습니다. AKS에서 클러스터 운영자는 Pod가 관리형 ID를 사용할 수 있도록 하는 다음 두 가지 구성 요소를 배포합니다.

* **NMI(노드 관리 ID) 서버**는 AKS 클러스터의 각 노드에서 DaemonSet으로 실행되는 Pod입니다. NMI 서버는 Azure 서비스에 대한 Pod 요청을 수신 대기합니다.
* **MIC(Managed Identity Controller)** 는 Kubernetes API 서버를 쿼리할 권한을 가진 중앙 Pod이고 Pod에 해당하는 Azure ID 매핑을 확인합니다.

Pod가 Azure 서비스에 대한 액세스를 요청하면 네트워크 규칙은 트래픽을 NMI(노드 관리 ID) 서버로 리디렉션합니다. NMI 서버는 원격 주소를 기반으로 Azure 서비스에 대한 액세스를 요청하는 Pod를 식별하고, MIC(Managed Identity Controller)를 쿼리합니다. MIC가 AKS 클러스터에서 Azure ID 매핑을 확인한 다음, NMI 서버가 Pod의 ID 매핑을 기반으로 Azure AD(Active Directory)의 액세스 토큰을 요청합니다. Azure AD는 Pod에 반환되는 NMI 서버에 대한 액세스를 제공합니다. 이 액세스 토큰은 이후 Pod가 Azure의 서비스에 대한 액세스를 요청하는 데 사용할 수 있습니다.

다음 예제에서 개발자는 관리형 ID를 사용하여 Azure SQL Server 인스턴스에 대한 액세스를 요청하는 Pod를 만듭니다.

![Pod ID를 사용하면 Pod가 다른 서비스에 대한 액세스를 자동으로 요청할 수 있습니다.](media/operator-best-practices-identity/pod-identities.png)

1. 클러스터 운영자는 먼저 Pod가 서비스 액세스를 요청할 때 ID를 매핑하는 데 사용할 수 있는 서비스 계정을 만듭니다.
1. NMI 서버와 MIC를 배포하여 액세스 토큰에 대한 Pod 요청을 Azure AD로 릴레이합니다.
1. 개발자는 NMI 서버를 통해 액세스 토큰을 요청하는 관리형 ID를 사용하여 Pod를 배포합니다.
1. 토큰은 Pod에 반환되고 Azure SQL Server 인스턴스에 액세스하는 데 사용됩니다.

> [!NOTE]
> 관리 되는 pod id는 오픈 소스 프로젝트 이며 Azure 기술 지원 서비스에서 지원 하지 않습니다.

Pod id를 사용 하려면 [Kubernetes 응용 프로그램에 대한 id Azure Active Directory][aad-pod-identity]를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 모범 사례 문서에서는 클러스터 및 리소스의 인증 및 권한 부여를 중점적으로 설명했습니다. 이러한 일부 모범 사례를 구현하려면 다음 문서를 참조하세요.

* [AKS와 Azure Active Directory 통합][aks-aad]
* [AKS를 사용 하 여 Azure 리소스에 대한 관리 id 사용][aad-pod-identity]

AKS의 클러스터 작업에 대한 자세한 내용은 다음 모범 사례를 참조하세요.

* [다중 테넌트 및 클러스터 격리][aks-best-practices-cluster-isolation]
* [기본 Kubernetes scheduler 기능][aks-best-practices-scheduler]
* [Advanced Kubernetes scheduler 기능][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
