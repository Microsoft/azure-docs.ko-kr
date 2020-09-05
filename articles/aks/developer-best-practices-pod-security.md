---
title: 개발자 모범 사례 - AKS(Azure Kubernetes Services)의 pod 보안
description: AKS(Azure Kubernetes Services)에서 pod 보안을 유지하는 방법에 대한 개발자 모범 사례 알아보기
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: fab4943cad1a87bda70a4c4332ab6135ed99bf1b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022278"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Services)의 pod 보안 모범 사례

AKS(Azure Kubernetes Service)에서 애플리케이션을 개발 및 실행할 경우 pod 보안 유지가 핵심 고려 사항입니다. 애플리케이션은 필요한 최소 권한 수 원칙에 따라 디자인해야 합니다. 프라이빗 데이터를 안전하게 유지하는 일은 고객을 위하는 마음입니다. 데이터베이스 연결 문자열, 키 또는 비밀과 인증서를 외부 세계에 노출하여 악의적인 공격 용도로 활용되는 것을 원하지 않을 것입니다. 이러한 항목을 코드에 추가하거나 컨테이너 이미지에 포함하지 않도록 합니다. 이러한 방식은 노출 위험을 초래하며, 컨테이너 이미지를 다시 작성해야 할 때 해당 자격 증명을 순환하는 기능을 제한합니다.

이 모범 사례 문서는 AKS에서 Pod를 보호하는 방법에 중점을 둡니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Pod 보안 컨텍스트를 사용하여 프로세스 및 서비스나 권한 상승에 대한 액세스 제한
> * pod에서 관리되는 ID를 사용하여 다른 Azure 리소스에서 인증
> * Azure Key Vault와 같은 디지털 자격 증명 모음에서 자격 증명 요청 및 검색

[클러스터 보안][best-practices-cluster-security] 및 [컨테이너 이미지 관리][best-practices-container-image-management]에 대한 모범 사례를 참조할 수도 있습니다.

## <a name="secure-pod-access-to-resources"></a>리소스에 대한 pod 액세스 보안 유지

**모범 사례 지침** - 다른 사용자 또는 그룹 권한으로 실행하고 기본 노드 프로세스 및 서비스에 대한 액세스를 제한하려면 pod 보안 컨텍스트 설정을 정의합니다. 필요한 최소 권한 수를 할당합니다.

애플리케이션이 제대로 실행되려면 pod를 *루트* 권한이 아닌 정의된 사용자 또는 그룹 권한으로 실행해야 합니다. pod 또는 컨테이너에 대한 `securityContext`를 사용하여 *runAsUser* 또는 *fsGroup*와 같은 설정을 정의함으로써 해당 권한을 가정할 수 있습니다. 필요한 사용자 또는 그룹 권한만 할당하고, 추가 권한을 가정하는 수단으로 보안 컨텍스트를 사용하지 않도록 합니다. *runAsUser*, 권한 상승 및 기타 Linux 기능 설정은 Linux 노드 및 Pod에서만 사용할 수 있습니다.

루트가 아닌 사용자 권한으로 실행하면 컨테이너는 1024 미만의 권한 있는 포트에 바인딩할 수 없습니다. 이 시나리오에서는 Kubernetes 서비스를 사용하여 앱이 특정 포트에서 실행되고 있는 것처럼 가장할 수 있습니다.

또한 pod 보안 컨텍스트는 프로세스 및 서비스에 액세스하기 위한 추가 기능 또는 권한을 정의할 수도 있습니다. 다음과 같은 일반적인 보안 컨텍스트 정의를 설정할 수 있습니다.

* **allowPrivilegeEscalation**은 pod가 *루트* 권한을 가정할 수 있는지를 정의합니다. 이 설정이 항상 *false*로 설정되도록 애플리케이션을 디자인합니다.
* **Linux 기능**을 사용하여 pod가 기본 노드 프로세스에 액세스하도록 할 수 있습니다. 이러한 기능을 할당할 때는 주의해야 합니다. 필요한 최소 권한 수를 할당합니다. 자세한 내용은 [Linux 기능][linux-capabilities]을 참조하세요.
* **SELinux 레이블**은 서비스, 프로세스 및 파일 시스템 액세스에 대한 액세스 정책을 정의할 수 있는 Linux 커널 보안 모듈입니다. 마찬가지로 필요한 최소 권한 수를 할당합니다. 자세한 내용은 [Kubernetes의 SELinux 옵션][selinux-labels]을 참조하세요.

다음 예제 pod YAML 매니페스트는 정의할 보안 컨텍스트 설정을 지정합니다.

* Pod는 사용자 ID *1000*과 그룹 ID *2000*의 일부로 실행됩니다.
* `root`를 사용하도록 권한을 에스컬레이션할 수 없습니다.
* Linux 기능이 네트워크 인터페이스 및 호스트의 실시간(하드웨어) 클록에 액세스할 수 있도록 허용합니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

클러스터 운영자와 함께 필요한 보안 컨텍스트 설정을 확인합니다. pod에 필요한 추가 권한 및 액세스 권한을 최소화하도록 애플리케이션을 디자인합니다. AppArmor 및 seccomp(보안 컴퓨팅)를 사용하여 액세스를 제한하는 추가 보안 기능이 있습니다. 이러한 보안 기능은 클러스터 운영자가 구현할 수 있습니다. 자세한 내용은 [리소스에 대한 컨테이너 액세스 보호][apparmor-seccomp]를 참조하세요.

## <a name="limit-credential-exposure"></a>자격 증명 노출 제한

**모범 사례 지침** - 애플리케이션 코드에서 자격 증명을 정의하지 않도록 합니다. Azure 리소스에 대해 관리 ID를 사용하여 pod가 다른 리소스에 대한 액세스를 요청하도록 합니다. 또한 Azure Key Vault와 같은 디지털 자격 증명 모음을 사용하여 디지털 키 및 자격 증명을 저장하고 검색하는 것이 좋습니다. Pod 관리 ID는 Linux Pod 및 컨테이너 이미지에만 사용됩니다.

애플리케이션 코드에 자격 증명이 노출될 위험을 제한하려면 고정 또는 공유 자격 증명을 사용하지 않도록 합니다. 자격 증명 또는 키를 코드에 직접 포함하면 안 됩니다. 이러한 자격 증명이 노출되면 애플리케이션을 업데이트하고 다시 배포해야 합니다. 더 나은 방법은 pod에 고유한 ID를 부여하고 스스로 인증을 받는 방법을 제공하거나 디지털 자격 증명 모음에서 자격 증명을 자동으로 검색하는 것입니다.

### <a name="use-azure-container-compute-upstream-projects"></a>Azure Container Compute 업스트림 프로젝트 사용

> [!IMPORTANT]
> 관련 AKS 오픈 소스 프로젝트는 Azure 기술 지원 서비스에서 지원되지 않습니다. 사용자가 클러스터에 직접 설치하고 커뮤니티에서 피드백을 수집할 수 있도록 제공됩니다.

다음 [관련 AKS 오픈 소스 프로젝트][aks-associated-projects]를 사용하면 Pod를 자동으로 인증하거나 디지털 자격 증명 모음에서 자격 증명 및 키를 요청할 수 있습니다. 이러한 프로젝트는 Azure Container Compute 업스트림 팀에서 유지 관리되며 [사용할 수 있는 광범위한 프로젝트 목록](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support)의 일부입니다.

 * [Azure Active Directory Pod ID][aad-pod-identity]
 * [비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>pod 관리 ID 사용

Azure 리소스에 대해 관리 ID를 사용하면 Pod는 이를 지원하는 Azure 서비스(예: 스토리지 또는 SQL)에 대해 자체 인증할 수 있습니다. pod에는 Azure Active Directory에서 인증을 받고 디지털 토큰을 받을 수 있도록 하는 Azure ID가 할당됩니다. 이 디지털 토큰은 pod가 서비스에 액세스하고 필요한 작업을 수행할 수 있는 권한이 있는지를 확인하는 다른 Azure 서비스에 제공될 수 있습니다. 이 방식은 예를 들어 데이터베이스 연결 문자열에 필요한 비밀이 없음을 의미합니다. pod 관리 ID의 간소화된 워크플로가 다음 다이어그램에 나와 있습니다.

:::image type="content" source="media/developer-best-practices-pod-security/basic-pod-identity.svg" alt-text="Azure의 pod 관리 ID에 대한 간소화된 워크플로":::

관리 ID를 사용할 경우 애플리케이션 코드는 Azure Storage와 같은 서비스에 액세스하기 위해 자격 증명을 포함할 필요가 없습니다. 각 pod는 고유한 ID를 사용하여 인증을 받으므로 사용자는 액세스 권한을 감사 및 검토할 수 있습니다. 애플리케이션이 다른 Azure 서비스에 연결되면 관리 ID를 사용하여 자격 증명 재사용 및 노출 위험을 제한합니다.

Pod ID에 대한 자세한 내용은 [애플리케이션과 함께 Pod 관리 ID를 사용하도록 AKS 클러스터 구성][aad-pod-identity]을 참조하세요.

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>비밀 저장소 CSI 드라이버와 함께 Azure Key Vault 사용

Pod ID 프로젝트를 사용하면 Azure 서비스 지원에 대한 인증이 가능합니다. Azure 리소스에 대한 관리 ID가 없는 사용자 고유의 서비스 또는 애플리케이션의 경우 자격 증명 또는 키를 사용하여 계속 인증할 수 있습니다. 이러한 비밀 콘텐츠를 저장하는 데 디지털 자격 증명 모음을 사용할 수 있습니다.

애플리케이션에 자격 증명이 필요한 경우 디지털 자격 증명 모음과 통신하고 최신 비밀 콘텐츠를 검색한 다음, 필요한 서비스에 연결합니다. Azure Key Vault는 이러한 디지털 자격 증명 모음일 수 있습니다. pod 관리 ID를 사용하여 Azure Key Vault에서 자격 증명을 검색하기 위한 간소화된 워크플로가 다음 다이어그램에 나와 있습니다.

:::image type="content" source="media/developer-best-practices-pod-security/basic-key-vault.svg" alt-text="pod 관리 ID를 사용하여 Key Vault에서 자격 증명을 검색 하기 위한 간소화된 워크플로":::

Key Vault를 사용하여 자격 증명, 스토리지 계정 키 또는 인증서와 같은 암호를 저장하고 정기적으로 순환합니다. [비밀 저장소 CSI 드라이버용 Azure Key Vault 공급자](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)를 사용하여 Azure Key Vault를 AKS 클러스터와 통합할 수 있습니다. 비밀 저장소 CSI 드라이버를 사용하면 AKS 클러스터가 기본적으로 Key Vault에서 비밀 콘텐츠를 검색하여 요청 Pod에만 안전하게 제공할 수 있습니다. 클러스터 운영자와 협력하여 비밀 저장소 CSI 드라이버를 AKS 작업자 노드에 배포합니다. Pod 관리 ID를 사용하여 Key Vault에 대한 액세스를 요청하고 비밀 저장소 CSI 드라이버를 통해 필요한 비밀 콘텐츠를 검색할 수 있습니다.

비밀 저장소 CSI 드라이버가 설치된 Azure Key Vault는 1.16 이상의 Kubernetes 버전을 필요로 하는 Linux 노드 및 Pod용으로 사용할 수 있습니다. Windows 노드 및 Pod의 경우 1.18 이상의 Kubernetes 버전이 필요합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 pod를 보호하는 방법을 중점적으로 설명했습니다. 이러한 일부 영역을 구현하려면 다음 문서를 참조하세요.

* [AKS를 통해 Azure 리소스에 관리 ID 사용][aad-pod-identity]
* [Azure Key Vault와 AKS 통합][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://awesomeopensource.com/projects/aks?categoryPage=11

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
