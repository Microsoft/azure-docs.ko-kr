---
title: Azure Kubernetes Service에서 지원되는 Kubernetes 버전
description: AKS(Azure Kubernetes Service)의 Kubernetes 버전 지원 정책 및 클러스터 수명 주기에 대해 알아봅니다.
services: container-service
ms.topic: article
ms.date: 07/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 45c60069c27a2bb9b34cc53eb394bd44a04bd0ba
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144620"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 지원되는 Kubernetes 버전

Kubernetes 커뮤니티에서는 대략 3개월마다 부 버전을 릴리스합니다. 이러한 릴리스에는 새로운 기능과 향상된 기능이 포함됩니다. 패치는 더 자주 릴리스되며(매주 릴리스되기도 함), 부 버전의 중요한 버그 수정용으로만 제공됩니다. 이러한 패치 릴리스에는 보안 취약점 또는 주요 버그에 대 한 수정이 포함 되어 있습니다.

## <a name="kubernetes-versions"></a>Kubernetes 버전

Kubernetes는 표준 [의미](https://semver.org/) 체계 버전 관리 체계를 사용 합니다. 즉, 각 Kubernetes 버전은이 번호 매기기 체계를 따릅니다.

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

버전의 각 번호는 이전 버전과의 호환성을 나타냅니다.

* 호환 되지 않는 API 변경 또는 이전 버전과의 호환성이 손상 될 때 주 버전은 변경 됩니다.
* 사소한 버전은 다른 부 릴리스와 이전 버전과 호환 되는 기능이 변경 될 때 변경 됩니다.
* 이전 버전과 호환 되는 버그 수정이 수행 될 때 패치 버전이 변경 됩니다.

사용자는 실행 중인 부 버전의 최신 패치 릴리스를 실행 해야 합니다. 예를 들어 프로덕션 클러스터가 켜져 **`1.17.7`** 있고 **`1.17.8`** *1.17* 시리즈에 사용할 수 있는 최신 패치 버전 인 경우, **`1.17.8`** 클러스터가 완전히 패치 되 고 지원 되는지 확인 하기 위해 가능한 한 빨리로 업그레이드 해야 합니다.

## <a name="kubernetes-version-support-policy"></a>Kubernetes 버전 지원 정책

AKS은 모든 SLO 또는 SLA 측정에서 사용 하도록 설정 된 버전 및 모든 지역에서 사용할 수 있는 경우 일반 공급 버전을 정의 합니다. AKS는 Kubernetes의 GA 부 버전 3 개를 지원 합니다.

* AKS에서 릴리스된 최신 GA 부 버전입니다 (N 이라고 함). 
* 이전 두 부 버전 
* 지원 되는 각 부 버전은 최대 2 개의 안정 된 패치를 지원 합니다.
* AKS는 미리 보기 사용 [약관][preview-terms]에 따라 명시적으로 레이블이 지정 된 미리 보기 버전을 지원할 수도 있습니다.

> [!NOTE]
> AKS은 점진적 지역 배포를 포함 하는 안전한 배포 방법을 사용 합니다. 즉, 새 릴리스 또는 모든 지역에서 새 버전을 사용할 수 있도록 최대 10 일이 소요 될 수 있습니다.

AKS에서 지원 되는 Kubernetes 버전은 "N-2"로 알려져 있습니다. (N (최신 릴리스)-2 (부 버전)).

예를 들어 AKS이 *1.17* 를 도입 하는 경우 다음 버전에 대 한 지원이 제공 됩니다.

새 부 버전    |    지원 되는 버전 목록
-----------------    |    ----------------------
1.17               |    1.17, 1.17, 1.16, 1.16, 1.15. e, 1.15. f

여기서 ". letter"는 패치 버전을 나타냅니다.

새 부 버전이 도입 되 면 지원 되는 가장 오래 된 부 버전 및 패치 릴리스가 더 이상 사용 되지 않고 제거 됩니다. 예를 들어 현재 지원 되는 버전 목록은 다음과 같습니다.

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

및 AKS는 1.18을 릴리스 합니다. \* 즉, 모든 1.15 \* 버전이 제거 되 고 30 일 후에 지원 되지 않습니다.

> [!NOTE]
> 고객이 지원 되지 않는 Kubernetes 버전을 실행 하는 경우 클러스터에 대 한 지원을 요청할 때 업그레이드 하 라는 메시지가 표시 됩니다. 지원 되지 않는 Kubernetes 릴리스를 실행 하는 클러스터는 [AKS 지원 정책](./support-policies.md)에 포함 되지 않습니다.

위의 외에도 AKS는 지정 된 부 버전의 최대 2 개의 **패치** 릴리스를 지원 합니다. 따라서 지원 되는 버전은 다음과 같습니다.

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

AKS에서 및를 해제 하는 경우 `1.17.9` `1.16.11` 가장 오래 된 패치 버전이 더 이상 사용 되지 않고 제거 되며 지원 되는 버전 목록은 다음과 같이 됩니다.

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>지원 되는 `kubectl` 버전

Kube 서버 버전에 비해 이전 또는 최신 버전의 부 버전 하나를 사용할 수 있으며 `kubectl` ,이는 [kubectl에 대 한 Kubernetes 지원 정책과](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)일치 합니다. *kube-apiserver*

예를 들어 *kube server* 가 *1.17*에 있으면 kube을 *1.16* *1.18* `kubectl` 해당 *서버*와 함께 사용 하 여 1.16에 버전 1.18를 사용할 수 있습니다.

버전을 설치 하거나 업데이트 하려면 `kubectl` 를 실행 `az aks install-cli` 합니다.

## <a name="release-and-deprecation-process"></a>릴리스 및 사용 중단 프로세스

[AKS Kubernetes 릴리스 달력](#aks-kubernetes-release-calendar)에서 예정 된 버전 릴리스 및 결함를 참조할 수 있습니다.

Kubernetes의 새 **부** 버전
1. AKS는 새 버전 릴리스의 계획 된 날짜와 [AKS 릴리스 정보](https://aka.ms/aks/releasenotes) 에 대 한 이전 버전의 사용 중단을 제거 하기 전에 최소 30 일 전에 사전 공지를 게시 합니다.
2. AKS는 AKS 및 포털 액세스 권한이 있는 모든 사용자가 사용할 수 있는 [서비스 상태 알림을](../service-health/service-health-overview.md) 게시 하 고 구독 관리자에 게 예정 된 버전 제거 날짜를 사용 하 여 전자 메일을 보냅니다.
3. 지원 되는 부 버전 릴리스로 업그레이드 하 여 지원 받기를 계속 하는 경우 **30 일 동안** 사용자가 버전을 제거 해야 합니다.

Kubernetes의 새로운 **패치** 버전
  * 패치 버전의 긴급 한 특성으로 인해 이러한 서비스를 사용할 수 있게 되 면 서비스에 도입할 수 있습니다.
  * 일반적으로 AKS는 새로운 패치 버전 릴리스에 대 한 광범위 한 통신을 수행 하지 않습니다. 그러나 AKS는 AKS에서 적시에 지원 하기 위해 사용 가능한 CVE 패치를 지속적으로 모니터링 하 고 유효성을 검사 합니다. 중요 한 패치가 있거나 사용자 작업이 필요한 경우 AKS은 새로 사용 가능한 패치로 업그레이드 하도록 사용자에 게 알립니다.
  * 사용자는 AKS에서 패치 릴리스가 제거 된 시간부터 **30 일 이내** 에 지원 되는 패치로 업그레이드 하 고 지원을 계속 받을 수 있습니다.

### <a name="supported-versions-policy-exceptions"></a>지원 되는 버전 정책 예외

AKS는 하나 이상의 중요 한 프로덕션에 영향을 주는 버그 또는 보안 문제를 사전에 확인 하지 않고 식별 된 새/기존 버전을 추가 하거나 제거할 권리를 보유 합니다.

특정 패치 릴리스를 건너뛰거나 버그 또는 보안 문제의 심각도에 따라 롤아웃 가속화를 사용할 수 있습니다.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal 및 CLI 버전

포털 또는 Azure CLI를 사용 하 여 AKS 클러스터를 배포 하는 경우 클러스터는 기본적으로 N-1 부 버전 및 최신 패치로 설정 됩니다. 예를 들어 AKS에서 *1.17*, *1.17*, *1.16*, *1.16*, *1.15. e*및 *1.15*를 지 원하는 경우 선택 된 기본 버전은 *1.16입니다.*

사용자의 구독과 지역에 현재 제공되는 버전을 알아보려면 [az aks get-versions][az-aks-get-versions] 명령을 사용합니다. 다음 예제에는 *EastUS* 지역에 제공되는 Kubernetes 버전이 나열되어 있습니다.

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes 릴리스 일정

이전 릴리스 내역은 [여기](https://en.wikipedia.org/wiki/Kubernetes#History)를 참조 하세요.

|  K8s 버전 | 업스트림 릴리스  | AKS 미리 보기  | AKS GA  | 수명 종료 |
|--------------|-------------------|--------------|---------|-------------|
| 1.17  | 12 월-09-19  | 1 월 2019   | 7 월 2020  | 1.20 GA | 
| 1.18  | 3 월-23-20  | 2020년 5월   | 8 월 2020  | 1.21 GA | 
| 1.19  | 8 월-04-20  | 8 월 2020   | 11 월 2020  | 1.22 GA | 
| 1.20  | * 11 월 2020    | * Dec 2020   | * 1 월 2021  | 1.23 GA | 

\* 보류 중인 업스트림 릴리스 날짜 확인입니다.

## <a name="faq"></a>FAQ

**사용자가 지원 되지 않는 부 버전으로 Kubernetes 클러스터를 업그레이드 하는 경우 어떻게 되나요?**

이상 버전을 사용 하는 경우이는 지원 되지 않으며 업그레이드를 요청 하는 것을 *의미 합니다.* 버전 n-3에서 n-2로 업그레이드 하는 경우 지원 정책 내에 다시 로그인 됩니다. 예를 들면 다음과 같습니다.

- 가장 오래 된 지원 되는 AKS 버전이 *1.15입니다. a* 와 *1.14* 에 있는 경우에는 지원 되지 않습니다.
- *1.14* 에서 1.15로 업그레이드 하는 경우 *에는* 지원 정책 내에 다시 로그인 합니다.

다운 그레이드은 지원 되지 않습니다.

**' 지원 외부 '의 의미**

' 지원 안 함 '은 실행 중인 버전이 지원 되는 버전 목록 외부에 있다는 것을 의미 하며, 버전 사용 중단 후 30 일의 유예 기간을 초과 하지 않는 한 지원을 요청할 때 지원 되는 버전으로 클러스터를 업그레이드 하 라는 메시지가 표시 됩니다. 또한 AKS은 지원 되는 버전 목록 외부의 클러스터에 대 한 런타임 또는 다른 보증을 수행 하지 않습니다.

**사용자가 지원 되지 않는 부 버전으로 Kubernetes 클러스터의 크기를 조정 하면 어떻게 되나요?**

AKS에서 지원 하지 않는 부 버전의 경우 확장 또는 축소는 계속 작동 하지만 서비스 품질을 보장 하지 않으므로 클러스터를 다시 지원 하도록 업그레이드 하는 것이 좋습니다.

**사용자가 계속 해 서 Kubernetes 버전을 계속 사용할 수 있나요?**

클러스터가 3 개 이상의 부 버전을 지원 하지 않고 보안 위험을 발생 시킬 수 있는 경우 Azure는 클러스터를 사전에 업그레이드 하도록 합니다. 추가 작업을 수행 하지 않는 경우 Azure는 사용자를 대신 하 여 자동으로 클러스터를 업그레이드할 권리를 보유 합니다.

**노드 풀이 지원 되는 AKS 버전 중 하나가 아닌 경우 제어 평면에서 지 원하는 버전은 무엇 인가요?**

컨트롤 평면은 모든 노드 풀의 버전 창 내에 있어야 합니다. 제어 평면 또는 노드 풀을 업그레이드 하는 방법에 대 한 자세한 내용은 [노드 풀 업그레이드](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)에 대 한 설명서를 참조 하세요.

**클러스터를 업그레이드 하는 동안 여러 AKS 버전을 건너뛸 수 있나요?**

지원 되는 AKS 클러스터를 업그레이드 하는 경우 Kubernetes 부 버전은 건너뛸 수 없습니다. 예를 들어 *1.12*. x  ->  *1.13 x x* x x x x *x x*x x x x x x x x x x x x x x x x x x x x x. x를  ->  사용할 수 있지만*1.14* *1.12.x*  ->  *1.14.x*

1.14에서 업그레이드 하려면 *1.12.x*  ->  *1.14.x*먼저 *1.12*. x  ->  *1.13.* x에서 업그레이드 한 다음 *1.13.* x  ->  *1.14*에서 업그레이드 합니다.

지원 되지 않는 버전에서 지원 되는 버전으로 업그레이드 하는 경우에만 여러 버전을 건너뛸 수 있습니다. 예를 들어 *지원 되는* *1.10* . x >에서 업그레이드를 완료할 수 있습니다.

## <a name="next-steps"></a>다음 단계

클러스터를 업그레이드하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service) 클러스터 업그레이드][aks-upgrade]를 참조하세요.

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
