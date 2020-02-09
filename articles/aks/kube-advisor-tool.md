---
title: 모범 사례 구현을 위해 Azure에서 Kubernetes 배포 확인
description: kube-advisor를 사용하여 Azure Kubernetes Service에 대한 배포의 모범 사례 구현을 확인하는 방법 알아보기
services: container-service
author: seanmck
ms.service: container-service
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 03c5eb2e32a0a8ec51844511276d9efba5651068
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2019
ms.locfileid: "65073768"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>클러스터에서 Kubernetes 모범 사례 확인

애플리케이션에 대한 최고의 성능과 복원력을 보장하기 위해 Kubernetes 배포 시 따라야 하는 몇 가지 모범 사례가 있습니다. kube-advisor 도구를 사용하여 이러한 제안 사항을 따르지 않는 배포를 찾을 수 있습니다.

## <a name="about-kube-advisor"></a>kube-advisor에 대한 정보

[kube-advisor 도구][kube-advisor-github]는 클러스터에서 실행되도록 설계된 단일 컨테이너입니다. Kubernetes API 서버에 배포에 대한 정보를 쿼리하여 제안된 개선 사항 집합을 반환합니다.

Kube-관리자 도구 리소스 요청과 PodSpecs에 대한 Windows 응용 프로그램 뿐만 아니라 Linux 응용 프로그램에서 누락 된 제한에 대해 보고할 수 있지만 kube-관리자 도구 자체 Linux pod에서 예약 해야 합니다. Pod를 사용 하 여 특정 OS를 사용 하 여 노드 풀에서 실행 되도록 예약할 수 있습니다는 [노드 선택기] [ k8s-node-selector] pod 구성에서 합니다.

> [!NOTE]
> kube-advisor 도구는 최상의 노력을 기준으로 Microsoft에서 지원됩니다. 문제와 제안은 GitHub에 제출해야 합니다.

## <a name="running-kube-advisor"></a>kube-advisor 실행

[RBAC(역할 기반 액세스 제어)](azure-ad-integration.md)용으로 구성된 클러스터에서 도구를 실행하려면 다음 명령을 사용합니다. 첫 번째 명령은 Kubernetes 서비스 계정을 만듭니다. 두 번째 명령은 이 서비스 계정을 사용하여 Pod에서 도구를 실행하고 종료 후 Pod를 삭제하도록 구성합니다. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }"
```

RBAC를 사용하지 않는 경우 다음과 같이 명령을 실행할 수 있습니다.

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

몇 초 안에 배포에 대한 잠재적 개선 사항을 설명하는 테이블이 나타납니다.

![Kube-advisor 출력](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>수행된 검사

이 도구는 몇 가지 Kubernetes 모범 사례를 자체적인 수정 제안 사항을 적용하여 검증합니다.

### <a name="resource-requests-and-limits"></a>리소스 요청 및 제한

Kubernetes는 [Pod 사양에 대한 리소스 요청 및 제한][kube-cpumem]을 정의하도록 지원합니다. 요청은 컨테이너를 실행하는 데 필요한 최소 CPU 및 메모리를 정의합니다. 제한은 허용해야 하는 최대 CPU 및 메모리를 정의합니다.

기본적으로 Pod 사양에는 요청 또는 제한이 설정되지 않습니다. 이로 인해 노드가 과도하게 예약되고 컨테이너가 부족할 수 있습니다. kube-advisor 도구는 요청 및 제한이 설정되지 않은 Pod를 강조 표시합니다.

## <a name="cleaning-up"></a>정리

클러스터에 RBAC를 사용하도록 설정된 경우 다음 명령을 사용하여 도구를 실행한 후에 `ClusterRoleBinding`을 정리할 수 있습니다.

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

RBAC를 사용할 수 없는 클러스터에 대해 도구를 실행하는 경우 정리가 필요하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Kubernetes Service 문제 해결](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors