---
title: Azure Arc 지원 Kubernetes 개요
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: 이 문서에서는 Azure Arc 지원 Kubernetes 개요를 제공합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.custom: references_regions
ms.openlocfilehash: 885c96b83edb83cfb62fc117d9b4406792827056
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723727"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Azure Arc 지원 Kubernetes 미리 보기란?

Azure Arc 지원 Kubernetes 미리 보기를 사용하여 Azure 내부 또는 외부에서 Kubernetes 클러스터를 연결하고 구성할 수 있습니다. Kubernetes 클러스터가 Azure Arc에 연결되면 Azure Portal에 표시됩니다. Azure Resource Manager ID와 관리 ID가 있습니다. 클러스터는 표준 Azure 구독에 연결되고, 리소스 그룹에 위치하며, 다른 Azure 리소스와 마찬가지로 태그를 받을 수 있습니다. 

Kubernetes 클러스터를 Azure에 연결하려면 클러스터 관리자가 에이전트를 배포해야 합니다. 이러한 에이전트는 `azure-arc`라는 Kubernetes 네임스페이스에서 실행되며 표준 Kubernetes 배포입니다. 에이전트는 Azure에 대한 연결을 담당하고, Azure Arc 로그 및 메트릭을 수집하고, 구성 요청을 감시합니다. 

Azure Arc 지원 Kubernetes는 전송 중인 데이터를 보호하기 위해 업계 표준 SSL을 지원합니다. 또한, 데이터는 기밀 유지를 위해 Azure Cosmos DB 데이터베이스에 암호화된 상태로 유지됩니다.
 
> [!NOTE]
> Azure Arc 지원 Kubernetes는 미리 보기로 제공됩니다. 프로덕션 워크로드에는 권장하지 않습니다.

## <a name="supported-kubernetes-distributions"></a>지원되는 Kubernetes 배포

Azure Arc 지원 Kubernetes는 Azure의 AKS 엔진, Azure Stack Hub의 AKS 엔진, GKE, EKS 및 VMware vSphere 클러스터와 같은 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터와 함께 작동합니다.

Azure Arc 지원 Kubernetes 기능은 다음 배포에서 Arc 팀에 의해 테스트되었습니다.
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* AKS 엔진
* Azure Stack Hub의 AKS 엔진
* 클러스터 API 공급자 Azure

## <a name="supported-scenarios"></a>지원되는 시나리오 

Azure Arc 지원 Kubernetes는 다음 시나리오를 지원합니다. 

* 인벤토리, 그룹화 및 태그 지정을 위해 Azure 외부에서 실행되는 Kubernetes 연결.

* GitOps 기반 구성 관리를 사용하여 애플리케이션 배포 및 구성 적용. 

* 컨테이너에 대한 Azure Monitor를 사용하여 클러스터를 확인 및 모니터링. 

* Kubernetes에 대한 Azure Policy를 사용하여 정책 적용. 

## <a name="supported-regions"></a>지원되는 지역 

현재 Azure Arc 지원 Kubernetes는 다음 지역에서 지원됩니다. 

* 미국 동부 
* 서유럽

## <a name="next-steps"></a>다음 단계

* [클러스터 연결](./connect-cluster.md)
