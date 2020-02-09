---
title: Azure 애플리케이션 게이트웨이 수신 컨트롤러 란?
description: 이 문서에서는 Application Gateway 수신 컨트롤러의 용도를 소개 합니다.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 838145f8573e11deff8566c932a9c73c6f59f03b
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561663"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러는 무엇입니까?
AGIC (Application Gateway 수신 컨트롤러)는 Kubernetes 응용 프로그램으로, azure [Kubernetes 서비스 (AKS)](https://azure.microsoft.com/services/kubernetes-service/) 고객이 azure의 기본 [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7 부하 분산 장치를 활용 하 여 클라우드 소프트웨어를 인터넷에 노출할 수 있도록 합니다. AGIC는 호스트 되는 Kubernetes 클러스터를 모니터링 하 고 Application Gateway를 지속적으로 업데이트 하 여 선택 된 서비스가 인터넷에 노출 되도록 합니다.

수신 컨트롤러는 고객의 AKS에서 자체 pod로 실행 됩니다. AGIC는 Kubernetes 리소스의 하위 집합을 모니터링 하 여 변경 내용을 모니터링 합니다. AKS 클러스터의 상태는 특정 구성 Application Gateway 변환 되 고 [ARM (Azure Resource Manager)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)에 적용 됩니다.

## <a name="benefits-of-application-gateway-ingress-controller"></a>Application Gateway 수신 컨트롤러의 이점
AGIC를 사용 하면 배포가 단일 Application Gateway 수신 컨트롤러를 사용 하 여 여러 AKS 클러스터를 제어할 수 있습니다. 또한 AGIC를 사용 하면 AKS 클러스터 앞에 다른 부하 분산 장치/공용 IP를 사용 하지 않아도 되며 요청이 AKS 클러스터에 도달 하기 전에 데이터 경로에서 여러 홉을 피할 수 있습니다. Application Gateway는 해당 개인 IP를 직접 사용 하 여 pod에 대한 통신을 수행 하며 NodePort 또는 KubeProxy services가 필요 하지 않습니다. 또한 배포에 더 나은 성능을 제공 합니다.

수신 컨트롤러는 Standard_v2 및 WAF_v2 Sku 에서만 지원 되며 자동 크기 조정 이점도 제공 합니다. AKS 클러스터의 리소스를 사용 하지 않고 트래픽 로드의 증가 또는 감소에 대응 하 여 적절 하 게 크기를 조정할 수 Application Gateway.

AGIC 외에도 Application Gateway를 사용 하면 TLS 정책 및 WAF (웹 응용 프로그램 방화벽) 기능을 제공 하 여 AKS 클러스터를 보호할 수 있습니다.

![Azure 애플리케이션 게이트웨이 + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC는 서비스 및 배포/Pod와 함께 Kubernetes [수신 리소스](https://kubernetes.io/docs/user-guide/ingress/)를 통해 구성 됩니다. Azure의 기본 Application Gateway L7 부하 분산 장치를 활용 하 여 다양 한 기능을 제공 합니다. 몇 가지를 예로 들자면 다음과 같습니다.
  - URL 라우팅
  - 쿠키 기반 선호도
  - SSL 종료
  - 엔드투엔드 SSL
  - 공용, 개인 및 하이브리드 웹 사이트 지원
  - 통합된 웹 애플리케이션 방화벽

AGIC는 여러 네임 스페이스를 처리 하 고 ProhibitedTargets를 가질 수 있습니다. 즉, AGIC는 기존 백 엔드에 영향을 주지 않고 AKS 클러스터에 대해 특별히 Application Gateway를 구성할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- [**최적의 배포**](ingress-controller-install-new.md): AGIC, AKS 및 Application Gateway를 설치 하는 방법에 대한 지침은 빈 슬레이트 인프라에 있습니다.
- [**Brownfield 배포**](ingress-controller-install-existing.md): 기존 AKS 및 APPLICATION GATEWAY에 AGIC을 설치 합니다.

