---
title: Azure Kubernetes Service에 배포 된 마이크로 서비스에서 Azure API Management 사용 | Microsoft Docs
description: 이 문서에서는 AKS를 사용 하 여 API Management를 배포 하는 옵션을 설명 합니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/14/2019
ms.author: apimpm
ms.openlocfilehash: 1d6773b4daac256234c33bf50fb3736d585ac505
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480997"
---
# <a name="use-azure-api-management-with-microservices-deployed-in-azure-kubernetes-service"></a>Azure Kubernetes Service에 배포 된 마이크로 서비스에서 Azure API Management 사용

마이크로 서비스는 Api를 구축 하는 데 완벽 합니다. AKS ( [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) )를 사용 하 여 클라우드에서 [마이크로 서비스 기반 아키텍처](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/microservices) 를 신속 하 게 배포 하 고 작동할 수 있습니다. 그런 다음 [Azure API Management](https://aka.ms/apimrocks) (API Management)를 활용 하 여 마이크로 서비스를 내부 및 외부 사용을 위한 api로 게시할 수 있습니다. 이 문서에서는 AKS를 사용 하 여 API Management를 배포 하는 옵션을 설명 합니다. Kubernetes, API Management 및 Azure 네트워킹에 대한 기본 지식이 있다고 가정 합니다. 

## <a name="background"></a>배경

마이크로 서비스를 사용을 위한 Api로 게시할 때 마이크로 서비스와 해당 서비스를 사용 하는 클라이언트 간의 통신을 관리 하는 것은 어려울 수 있습니다. 인증, 권한 부여, 제한, 캐싱, 변환 및 모니터링과 같은 여러 가지 교차 문제를 해결 해야 합니다. 이러한 문제는 마이크로 서비스가 내부 또는 외부 클라이언트에 노출 되는지 여부에 관계 없이 유효 합니다. 

[API 게이트웨이](https://docs.microsoft.com/dotnet/architecture/microservices/architect-microservice-container-applications/direct-client-to-microservice-communication-versus-the-api-gateway-pattern) 패턴은 이러한 문제를 해결 합니다. API 게이트웨이는 마이크로 서비스에 대한 전면 도어 역할을 하며, 마이크로 서비스에서 클라이언트를 분리 하 고, 추가 보안 계층을 추가 하 고, 크로스 절삭 문제를 처리 하는 부담을 제거 하 여 마이크로 서비스의 복잡성을 줄입니다. 

[Azure API Management](https://aka.ms/apimrocks) 는 API 게이트웨이 요구를 해결 하는 턴키 솔루션입니다. 마이크로 서비스에 대한 일관 되 고 최신 게이트웨이를 신속 하 게 만들고 Api로 게시할 수 있습니다. 이 솔루션은 전체 수명 주기 API management 솔루션으로 API 검색, API 수명 주기 관리 및 API 분석을 위한 셀프 서비스 개발자 포털을 비롯 한 추가 기능도 제공 합니다.

AKS 및 API Management는 함께 사용 되는 경우 마이크로 서비스 기반 Api를 배포, 게시, 보호, 모니터링 및 관리 하기 위한 플랫폼을 제공 합니다. 이 문서에서는 API Management와 함께 AKS를 배포 하는 몇 가지 옵션을 살펴보겠습니다. 

## <a name="kubernetes-services-and-apis"></a>Kubernetes 서비스 및 Api

Kubernetes 클러스터에서 컨테이너는 [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/)에 배포 되며,이는 임시 이며 수명 주기를 포함 합니다. 작업자 노드가 소멸 되 면 노드에서 실행 되는 Pod가 손실 됩니다. 따라서 Pod의 IP 주소는 언제 든 지 변경 될 수 있습니다. 이를 사용 하 여 pod와 통신할 수 없습니다. 

이 문제를 해결 하기 위해 Kubernetes는 [서비스](https://kubernetes.io/docs/concepts/services-networking/service/)개념을 소개 했습니다. Kubernetes 서비스는 Pod의 논리 그룹을 정의 하 고 해당 Pod에 대한 외부 트래픽 노출, 부하 분산 및 서비스 검색을 가능 하 게 하는 추상화 계층입니다. 

API Management 통해 마이크로 서비스를 Api로 게시할 준비가 되 면 API Management의 Api에 Kubernetes의 서비스를 매핑하는 방법에 대해 생각해 야 합니다. 설정 된 규칙이 없습니다. 비즈니스 기능 또는 도메인을 처음부터 마이크로 서비스로 디자인 하 고 분할 하는 방법에 따라 달라 집니다. 예를 들어 서비스 뒤의 pod 지정 된 리소스 (예: 고객)에 대한 모든 작업을 담당 하는 경우 서비스는 하나의 API에 매핑될 수 있습니다. 리소스에 대한 작업이 여러 마이크로 서비스 (예: GetOrder, PlaceOrder)로 분할 된 경우 여러 서비스는 API management에서 하나의 단일 API로 논리적으로 집계 될 수 있습니다 (그림 1 참조). 

매핑도 향상 될 수도 있습니다. API Management는 마이크로 서비스 앞에서 외관을 만들기 때문에 시간이 지남에 따라 마이크로 서비스를 리팩터링 하 고 올바른 크기를 지정할 수 있습니다. 

![Api에 서비스 매핑](./media/api-management-aks/service-api-mapping.png)

## <a name="deploy-api-management-in-front-of-aks"></a>AKS 앞에 API Management 배포

AKS 클러스터 앞에 API Management를 배포 하는 몇 가지 옵션이 있습니다. 

AKS 클러스터가 항상 VNet (가상 네트워크)에 배포 되는 동안에는 VNet에 API Management 인스턴스를 배포할 필요가 없습니다. API Management 클러스터 VNet 내에 없는 경우 AKS 클러스터는 API Management에 연결할 공용 엔드포인트을 게시 해야 합니다. 이 경우 API Management와 AKS 간의 연결을 보호 해야 합니다. 즉, API Management를 통해서만 클러스터에 액세스할 수 있는지 확인 해야 합니다. 옵션을 살펴보겠습니다. 

### <a name="option-1-expose-services-publicly"></a>옵션 1: 서비스를 공개적으로 노출

AKS 클러스터의 서비스는 NodePort, LoadBalancer 또는 ExternalName의 [서비스 유형을](https://docs.microsoft.com/azure/aks/concepts-network) 사용 하 여 공개적으로 노출할 수 있습니다. 이 경우 서비스는 공용 인터넷에서 직접 액세스할 수 있습니다. 클러스터 앞에 API Management 배포한 후에는 마이크로 서비스에서 인증을 적용 하 여 모든 인바운드 트래픽이 API Management 통과 하는지 확인 해야 합니다. 예를 들어 API Management는 클러스터에 대한 각 요청에 액세스 토큰을 포함할 수 있습니다. 각 마이크로 서비스는 요청을 처리 하기 전에 토큰의 유효성을 검사 해야 합니다. 


특히 마이크로 서비스에 구현 된 인증 논리가 이미 있는 경우 AKS 앞에 API Management를 배포 하는 가장 쉬운 옵션 일 수 있습니다. 

![서비스 직접 게시](./media/api-management-aks/direct.png)

장점:
* 클러스터 VNet에 삽입할 필요가 없기 때문에 API Management 쪽에서 쉽게 구성할 수 있습니다.
* 서비스가 이미 공개적으로 노출 되었으며 마이크로 서비스에 인증 논리가 이미 있는 경우 AKS 쪽이 변경 되지 않습니다.

단점:
* 서비스 엔드포인트의 공개 표시로 인 한 잠재적인 보안 위험
* 인바운드 클러스터 트래픽에 대한 단일 진입점 없음
* 중복 인증 논리를 사용 하 여 마이크로 서비스를 복잡 하 게 합니다.

### <a name="option-2-install-an-ingress-controller"></a>옵션 2: 수신 컨트롤러 설치

옵션 1이 더 쉬울 수도 있지만 위에서 언급 한 것 처럼 주목할 만한 단점이 있습니다. API Management 인스턴스가 클러스터 VNet에 없는 경우 mTLS (상호 TLS 인증)는 API Management 인스턴스와 AKS 클러스터 간의 양방향으로 트래픽을 안전 하 고 신뢰할 수 있도록 하는 강력한 방법입니다. 

상호 TLS 인증은 기본적으로 API Management에서 [지원](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates) 되며 [수신 컨트롤러](https://docs.microsoft.com/azure/aks/ingress-own-tls) (그림 3)를 설치 하 여 Kubernetes에서 사용 하도록 설정할 수 있습니다. 결과적으로, 인증은 마이크로 서비스를 간소화 하는 수신 컨트롤러에서 수행 됩니다. 또한 API Management의 IP 주소를 수신 하 여 허용 목록에 추가 하 API Management만 클러스터에 대한 액세스 권한이 있는지 확인할 수 있습니다.  

 
![수신 컨트롤러를 통해 게시](./media/api-management-aks/ingress-controller.png)


장점:
* 클러스터 VNet에 삽입할 필요가 없고 mTLS가 기본적으로 지원 되기 때문에 API Management 쪽에서 쉽게 구성할 수 있습니다.
* 수신 컨트롤러 계층에서 인바운드 클러스터 트래픽에 대한 보호를 중앙 집중화
* 공개적으로 표시 되는 클러스터 엔드포인트을 최소화 하 여 보안 위험 감소

단점:
* 수신 컨트롤러를 설치, 구성 및 유지 관리 하 고 mTLS에 사용 되는 인증서를 관리 하는 추가 작업으로 인해 클러스터 구성의 복잡성이 증가 합니다.
* 수신 컨트롤러 엔드포인트의 공용 표시 유형으로 인 한 보안 위험


API Management를 통해 API를 게시하면 구독 키를 사용하여 이러한 API에 쉽고 안전하게 액세스할 수 있습니다. 게시된 API를 사용해야 하는 개발자는 이러한 API에 호출 시 HTTP 요청에 유효한 구독 키를 포함해야 합니다. 그렇지 않으면 API Management 게이트웨이에서 호출을 즉시 거부합니다. 그리고 백 엔드 서비스에 호출이 전달되지 않습니다.

API에 액세스하기 위해 구독 키를 얻으려면 구독이 필요합니다. 구독은 일반적으로 구독 키 쌍의 이름을 지정한 컨테이너입니다. 게시된 API를 사용해야 하는 개발자는 구독을 가져올 수 있습니다. API 게시자에게 승인을 받을 필요는 없습니다. 또한 API 게시자는 API 소비자를 위해 직접 구독을 만들 수도 있습니다.

### <a name="option-3-deploy-apim-inside-the-cluster-vnet"></a>옵션 3: 클러스터 VNet 내에 APIM 배포

경우에 따라 규정 제약 조건이 있거나 엄격한 보안 요구 사항이 있는 고객은 공개적으로 노출 된 엔드포인트 때문에 옵션 1과 2가 실행 불가능 한 솔루션을 찾을 수 있습니다. 다른 AKS 클러스터와 마이크로 서비스를 사용 하는 응용 프로그램은 동일한 VNet 내에 상주할 수 있으므로 모든 API 트래픽이 VNet 내에서 유지 되므로 공개적으로 클러스터를 노출할 이유가 없습니다. 이러한 시나리오의 경우 클러스터 VNet에 API Management을 배포할 수 있습니다. [API Management 프리미엄 계층](https://aka.ms/apimpricing) 은 VNet 배포를 지원 합니다. 

VNet (외부 및 내부) [에 API Management를 배포 하](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) 는 두 가지 모드가 있습니다. 

API 소비자가 클러스터 VNet에 없으면 외부 모드 (그림. 4)를 사용 해야 합니다. 이 모드에서 API Management 게이트웨이는 클러스터 VNet에 삽입 되지만 외부 부하 분산 장치를 통해 공용 인터넷에서 액세스할 수 있습니다. 외부 클라이언트가 마이크로 서비스를 사용 하도록 허용 하면서 클러스터를 완전히 숨기는 데 도움이 됩니다. 또한 NSG (네트워크 보안 그룹)와 같은 Azure 네트워킹 기능을 사용 하 여 네트워크 트래픽을 제한할 수 있습니다.

![외부 VNet 모드](./media/api-management-aks/vnet-external.png)

모든 API 소비자가 클러스터 VNet 내에 있는 경우 내부 모드 (그림 5)를 사용할 수 있습니다. 이 모드에서 API Management 게이트웨이는 클러스터 VNET에 삽입 되 고 내부 부하 분산 장치를 통해이 VNet 내 에서만 액세스할 수 있습니다. 공용 인터넷에서 API Management 게이트웨이 또는 AKS 클러스터에 연결할 수 있는 방법은 없습니다. 

![내부 VNet 모드](./media/api-management-aks/vnet-internal.png)

 두 경우 모두 AKS 클러스터는 공개적으로 표시 되지 않습니다. 옵션 2와 비교 하 여 수신 컨트롤러는 필요 하지 않을 수 있습니다. 시나리오와 구성에 따라 API Management와 마이크로 서비스 사이에 인증을 계속 해야 할 수 있습니다. 예를 들어 서비스 메시를 채택 하는 경우 항상 상호 TLS 인증이 필요 합니다. 

장점:
* AKS 클러스터에 공용 엔드포인트이 없기 때문에 가장 안전한 옵션
* 공용 엔드포인트이 없기 때문에 클러스터 구성을 간소화 합니다.
* 내부 모드를 사용 하 여 VNet 내부에서 API Management 및 AKS를 모두 숨기는 기능
* NSG (네트워크 보안 그룹)와 같은 Azure 네트워킹 기능을 사용 하 여 네트워크 트래픽을 제어 하는 기능

단점:
* VNet 내부에서 작동 하도록 API Management 배포 및 구성의 복잡성이 증가 합니다.

## <a name="next-steps"></a>다음 단계

* [AKS의 응용 프로그램에 대한 네트워크 개념](https://docs.microsoft.com/azure/aks/concepts-network) 에 대한 자세한 정보
* [가상 네트워크에서 API Management를 사용 하는 방법](https://docs.microsoft.com/azure/api-management/api-management-using-with-vnet) 에 대한 자세한 정보





