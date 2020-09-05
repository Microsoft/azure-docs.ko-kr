---
title: Azure 애플리케이션 게이트웨이 구성 개요
description: 이 문서에서는 Azure 애플리케이션 게이트웨이의 구성 요소를 구성 하는 방법을 설명 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: absha
ms.openlocfilehash: 32809c33e1c365d8d333bb89a5c2f773b311c2ff
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88511085"
---
# <a name="application-gateway-configuration-overview"></a>Application Gateway 구성 개요

Azure 애플리케이션 게이트웨이는 다양 한 시나리오에 대해 다양 한 방법으로 구성할 수 있는 여러 구성 요소로 구성 되어 있습니다. 이 문서에서는 각 구성 요소를 구성 하는 방법을 보여 줍니다.

![Application Gateway 구성 요소 흐름 차트](./media/configuration-overview/configuration-overview1.png)

이 이미지는 세 개의 수신기가 있는 응용 프로그램을 보여 줍니다. 처음 두 개는 각각 및에 대 한 다중 사이트 수신기 `http://acme.com/*` `http://fabrikam.com/*` 입니다. 둘 다 포트 80에서 수신 합니다. 세 번째는 이전에 SSL (SSL(Secure Sockets Layer)) 종료로 알려진 종단 간 TLS (전송 계층 보안) 종료를 포함 하는 기본 수신기입니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>사전 요구 사항

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 가상 네트워크 및 전용 서브넷

Application gateway는 가상 네트워크의 전용 배포입니다. 가상 네트워크 내에는 application gateway에 대 한 전용 서브넷이 필요 합니다. 특정 응용 프로그램 게이트웨이 배포의 인스턴스를 서브넷에 여러 개 포함할 수 있습니다. 서브넷에 다른 응용 프로그램 게이트웨이를 배포할 수도 있습니다. 그러나 응용 프로그램 게이트웨이 서브넷에 다른 리소스를 배포할 수 없습니다.

> [!NOTE]
> 동일한 서브넷에서 Standard_v2와 표준 Azure 애플리케이션 게이트웨이를 혼합할 수 없습니다.

#### <a name="size-of-the-subnet"></a>서브넷 크기

Application Gateway은 인스턴스당 하나의 개인 IP 주소를 사용 하 고 개인 프런트 엔드 IP가 구성 된 경우 다른 개인 IP 주소를 사용 합니다.

또한 Azure는 내부적으로 사용 하기 위해 각 서브넷에 5 개의 IP 주소를 예약 합니다. 처음 네 개의 ip 주소는 마지막 IP 주소입니다. 예를 들어 개인 프런트 엔드 IP가 없는 15 개의 응용 프로그램 게이트웨이 인스턴스를 생각해 보세요. 이 서브넷에 대 한 IP 주소는 20 개 이상 필요 합니다. 내부 사용은 5이 고 응용 프로그램 게이트웨이 인스턴스의 경우 15입니다.

27 개의 응용 프로그램 게이트웨이 인스턴스와 개인 프런트 엔드 IP의 IP 주소가 있는 서브넷을 고려 합니다. 이 경우에는 응용 프로그램 게이트웨이 인스턴스의 경우 27 개의 IP 주소, 즉 개인 프런트 엔드에 대 한 27 개와 내부용으로 5 개의 IP 주소를 사용 33 해야 합니다.

Application Gateway (Standard 또는 WAF) SKU는 최대 32 인스턴스 (32 인스턴스 IP 주소 + 1 개인 프런트 엔드 IP + 5 Azure 예약 됨)를 지원할 수 있으므로 최소 서브넷 크기인/26을 권장 합니다.

Application Gateway (Standard_v2 또는 WAF_v2 SKU)는 최대 125 인스턴스 (125 인스턴스 IP 주소 + 1 개인 프런트 엔드 IP + 5 Azure 예약 됨)를 지원할 수 있으므로 최소 서브넷 크기인/24를 권장 합니다.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Application Gateway 서브넷의 네트워크 보안 그룹

NSGs (네트워크 보안 그룹)는 Application Gateway에서 지원 됩니다. 하지만 다음과 같은 몇 가지 제한 사항이 있습니다.

- 들어오는 인터넷 트래픽을 Application Gateway v1 SKU의 경우 65503-65534 TCP 포트에서 허용하고, 대상 서브넷이 **Any**이고 원본이 **GatewayManager** 서비스 태그인 v2 SKU의 경우 65200-65535 TCP 포트에서 허용해야 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호 (잠김) 됩니다. 이러한 끝점에서는 외부 엔터티 (해당 게이트웨이의 고객 포함)를 통신할 수 없습니다.

- 아웃 바운드 인터넷 연결은 차단할 수 없습니다. NSG의 기본 아웃 바운드 규칙은 인터넷 연결을 허용 합니다. 다음을 수행하는 것이 좋습니다.

  - 기본 아웃 바운드 규칙을 제거 하지 마십시오.
  - 아웃 바운드 연결을 거부 하는 아웃 바운드 규칙은 만들지 마십시오.

- **Azureloadbalancer** 태그에서 대상 서브넷으로의 트래픽은 허용 되어야 **합니다.**

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>몇 가지 원본 Ip에 Application Gateway 액세스 허용

이 시나리오에서는 Application Gateway 서브넷에 NSGs를 사용 합니다. 이 우선 순위에 따라 서브넷에 다음과 같은 제한 사항을 적용 합니다.

1. 인바운드 액세스 포트 (예: HTTP 액세스용 포트 80)로 전체 Application Gateway 서브넷 주소 범위 및 대상 포트를 대상으로 하는 원본 IP 또는 IP 범위에서 들어오는 트래픽을 허용 합니다.
2. 원본에서 **Gmanager** 서비스 태그 및 **destination으로 들어오는** 요청을 Application Gateway v1 sku의 경우 65503-65534로, [백 엔드 상태 통신](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)의 경우 포트 65200-65535을 사용 하도록 허용 합니다. 이 포트 범위는 Azure 인프라 통신에 필요합니다. 이러한 포트는 Azure 인증서에 의해 보호 (잠김) 됩니다. 적절 한 인증서가 없는 경우 외부 엔터티는 해당 끝점에 대 한 변경 내용을 초기화할 수 없습니다.
3. [네트워크 보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)에서 들어오는 Azure Load Balancer 프로브 (*azureloadbalancer* 태그) 및 인바운드 가상 네트워크 트래픽 (*VirtualNetwork* 태그)을 허용 합니다.
4. 모든 수신 트래픽 거부 규칙을 사용 하 여 차단 합니다.
5. 모든 대상에 대해 인터넷에 대 한 아웃 바운드 트래픽을 허용 합니다.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Application Gateway 서브넷에서 지원되는 사용자 정의 경로

> [!IMPORTANT]
> Application Gateway 서브넷에서 UDRs를 사용 하면 [백 엔드 상태 보기](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) 의 상태가 **알 수 없음**으로 표시 될 수 있습니다. 이로 인해 Application Gateway 로그 및 메트릭이 생성 되지 않을 수도 있습니다. 백 엔드 상태, 로그 및 메트릭을 볼 수 있도록 Application Gateway 서브넷에서 UDRs를 사용 하지 않는 것이 좋습니다.

- **v1**

   V1 SKU의 경우 UDRs (사용자 정의 경로)는 종단 간 요청/응답 통신을 변경 하지 않는 한 Application Gateway 서브넷에서 지원 됩니다. 예를 들어 Application Gateway 서브넷에서 UDR을 설정 하 여 패킷 검사를 위한 방화벽 어플라이언스를 가리키도록 할 수 있습니다. 그러나 검사 후 패킷이 의도 한 대상에 도달할 수 있는지 확인 해야 합니다. 그렇지 않으면 잘못 된 상태 프로브 또는 트래픽 라우팅 동작이 발생할 수 있습니다. 여기에는 Azure Express 경로 또는 가상 네트워크의 VPN 게이트웨이에서 전파 되는 학습 된 경로 또는 기본 0.0.0.0/0 경로가 포함 됩니다. 0.0.0.0/0을 온-프레미스 (강제 터널링)에서 리디렉션해야 하는 모든 시나리오는 v1에서 지원 되지 않습니다.

- **v2**

   V2 SKU의 경우 지원 되는 시나리오와 지원 되지 않는 시나리오는 다음과 같습니다.

   **v2 지원 시나리오**
   > [!WARNING]
   > 경로 테이블을 잘못 구성 하면 Application Gateway v 2에서 비대칭 라우팅이 발생할 수 있습니다. 모든 관리/제어 평면 트래픽이 가상 어플라이언스를 통하지 않고 인터넷으로 직접 전송 되는지 확인 합니다. 로깅 및 메트릭도 영향을 받을 수 있습니다.


  **시나리오 1**: udr을 사용 하 여 Application Gateway 서브넷에 대 한 BGP (Border Gateway Protocol) 경로 전파 사용 안 함

   경우에 따라 기본 게이트웨이 경로 (0.0.0.0/0)가 Application Gateway 가상 네트워크와 연결 된 Express 경로 또는 VPN 게이트웨이를 통해 보급 됩니다. 이렇게 하면 인터넷에 직접 경로를 사용 해야 하는 관리 평면 트래픽이 중단 됩니다. 이러한 시나리오에서는 UDR을 사용 하 여 BGP 경로 전파를 사용 하지 않도록 설정할 수 있습니다. 

   BGP 경로 전파를 사용 하지 않도록 설정 하려면 다음 단계를 사용 합니다.

   1. Azure에서 경로 테이블 리소스를 만듭니다.
   2. **가상 네트워크 게이트웨이 경로 전파** 매개 변수를 사용 하지 않도록 설정 합니다. 
   3. 적절 한 서브넷에 경로 테이블을 연결 합니다. 

   이 시나리오에 UDR을 사용 하도록 설정 하면 기존 설정이 중단 되지 않습니다.

  **시나리오 2**: Udr-인터넷에 0.0.0.0/0을 전달 합니다.

   UDR을 만들어 0.0.0.0/0 트래픽을 인터넷으로 직접 보낼 수 있습니다. 

  **시나리오 3**: kubenet를 사용 하는 Azure Kubernetes Service에 대 한 udr

  AKS (Azure Kubernetes Service) 및 AGIC (Application Gateway 수신 컨트롤러)와 함께 kubenet를 사용 하는 경우 Application Gateway에서 pod로 전송 된 트래픽을 올바른 노드로 라우팅할 수 있도록 경로 테이블이 필요 합니다. Azure CNI를 사용 하는 경우에는이 작업이 필요 하지 않습니다. 

  Kubenet가 작동할 수 있도록 경로 테이블을 사용 하려면 다음 단계를 수행 합니다.

  1. AKS에서 만든 리소스 그룹으로 이동 합니다 (리소스 그룹의 이름은 "MC_"로 시작 해야 함).
  2. 해당 리소스 그룹에서 AKS에 의해 생성 된 경로 테이블을 찾습니다. 경로 테이블은 다음 정보로 채워야 합니다.
     - 주소 접두사는 AKS에서 연결 하려는 pod의 IP 범위 여야 합니다. 
     - 다음 홉 형식은 가상 어플라이언스 여야 합니다. 
     - 다음 홉 주소는 pod을 호스트 하는 노드의 IP 주소 여야 합니다.
  3. 이 경로 테이블을 Application Gateway 서브넷에 연결 합니다. 
    
  **v2 지원 되지 않는 시나리오**

  **시나리오 1**: 가상 어플라이언스에 대 한 udr

  가상 어플라이언스, 허브/스포크 가상 네트워크 또는 온-프레미스 (강제 터널링)를 통해 0.0.0.0/0을 리디렉션해야 하는 모든 시나리오는 s 2에서 지원 되지 않습니다.

## <a name="front-end-ip"></a>프런트 엔드 IP

공용 IP 주소, 개인 IP 주소 또는 둘 다를 갖도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 클라이언트에서 인터넷 연결 VIP (가상 IP)를 통해 인터넷을 통해 액세스 해야 하는 백 엔드를 호스트 하는 경우 공용 IP가 필요 합니다.

> [!NOTE]
> 현재 Application Gateway V2는 사설 IP 전용 모드를 지원하지 않습니다. 다음 조합을 지원 합니다.
>* 사설 IP 및 공용 IP
>* 공용 IP 전용
>
> 자세한 내용은 [Application Gateway에 대 한 질문과 대답](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)을 참조 하세요.


공용 IP는 인터넷에 노출 되지 않은 내부 끝점에 필요 하지 않습니다. 이를 ilb ( *내부 부하 분산 장치* ) 끝점 또는 개인 프런트 엔드 IP 라고 합니다. 응용 프로그램 게이트웨이 ILB는 인터넷에 노출 되지 않는 내부 lob (기간 업무) 응용 프로그램에 유용 합니다. 또한 인터넷에 노출 되지 않지만 라운드 로빈 부하 분산, 세션 유지, TLS 종료를 필요로 하는 보안 경계 내에 있는 다중 계층 응용 프로그램의 서비스 및 계층에도 유용 합니다.

하나의 공용 IP 주소 또는 하나의 개인 IP 주소만 지원 됩니다. 응용 프로그램 게이트웨이를 만들 때 프런트 엔드 IP를 선택 합니다.

- 공용 IP의 경우 새 공용 IP 주소를 만들거나 application gateway와 동일한 위치에 있는 기존 공용 IP를 사용할 수 있습니다. 자세한 내용은 [고정 및 동적 공용 IP 주소](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)를 참조 하세요.

- 개인 IP의 경우 응용 프로그램 게이트웨이가 생성 되는 서브넷의 개인 IP 주소를 지정할 수 있습니다. 하나를 지정 하지 않으면 임의 IP 주소가 서브넷에서 자동으로 선택 됩니다. 선택한 IP 주소 유형 (정적 또는 동적)은 나중에 변경할 수 없습니다. 자세한 내용은 [내부 부하 분산 장치를 사용 하 여 응용 프로그램 게이트웨이 만들기](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)를 참조 하세요.

프런트 엔드 ip 주소는 프런트 엔드 IP에서 들어오는 요청을 확인 하는 *수신기*에 연결 됩니다.

## <a name="listeners"></a>수신기

수신기는 포트, 프로토콜, 호스트 및 IP 주소를 사용 하 여 들어오는 연결 요청을 확인 하는 논리적 엔터티입니다. 수신기를 구성할 때 게이트웨이의 들어오는 요청에서 해당 값과 일치 하는 값을 입력 해야 합니다.

Azure Portal를 사용 하 여 응용 프로그램 게이트웨이를 만드는 경우 수신기의 프로토콜 및 포트를 선택 하 여 기본 수신기도 만듭니다. 수신기에서 HTTP2 지원을 사용할지 여부를 선택할 수 있습니다. 응용 프로그램 게이트웨이를 만든 후에는 해당 기본 수신기 (*appGatewayHttpListener*)의 설정을 편집 하거나 새 수신기를 만들 수 있습니다.

### <a name="listener-type"></a>수신기 형식

새 수신기를 만들 때 [ *기본* *사이트와 다중 사이트*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)중에서 선택할 수 있습니다.

- 모든 도메인에 대 한 모든 요청을 수락 하 여 백 엔드 풀에 전달 하려는 경우 기본을 선택 합니다. [기본 수신기를 사용 하 여 응용 프로그램 게이트웨이를 만드는 방법을](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)알아봅니다.

- *호스트* 헤더 또는 호스트 이름에 따라 여러 백 엔드 풀에 요청을 전달 하려는 경우 다중 사이트 수신기를 선택 합니다. 여기에서 들어오는 요청과 일치 하는 호스트 이름도 지정 해야 합니다. Application Gateway HTTP 1.1 호스트 헤더를 사용 하 여 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트 하기 때문입니다. 자세히 알아보려면 [Application Gateway를 사용 하 여 여러 사이트 호스팅](multiple-site-overview.md)을 참조 하세요.

#### <a name="order-of-processing-listeners"></a>처리 수신기 순서

V1 SKU의 경우 요청은 규칙의 순서와 수신기의 형식에 따라 일치 합니다. 기본 수신기를 사용 하는 규칙이 먼저 순서 대로 제공 되 면 먼저 처리 되 고 해당 포트 및 IP 조합에 대 한 모든 요청을 허용 합니다. 이를 방지 하려면 먼저 다중 사이트 수신기를 사용 하 여 규칙을 구성 하 고 기본 수신기를 사용 하 여 규칙을 목록에서 마지막으로 푸시합니다.

V2 SKU의 경우 다중 사이트 수신기가 기본 수신기 보다 먼저 처리 됩니다.

### <a name="front-end-ip"></a>프런트 엔드 IP

이 수신기와 연결 하려는 프런트 엔드 IP 주소를 선택 합니다. 수신기가이 IP에서 들어오는 요청을 수신 대기 합니다.

### <a name="front-end-port"></a>프런트 엔드 포트

프런트 엔드 포트를 선택 합니다. 기존 포트를 선택 하거나 새로 만듭니다. [허용 되는 포트 범위](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)에서 값을 선택 합니다. 잘 알려진 포트 (예: 80 및 443) 뿐만 아니라 적절 한 모든 사용자 지정 포트를 사용할 수 있습니다. 포트는 공용 수신기 또는 개인 연결 수신기에 사용할 수 있습니다.

### <a name="protocol"></a>프로토콜

HTTP 또는 HTTPS를 선택 합니다.

- HTTP를 선택 하면 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화 되지 않습니다.

- [Tls 종료](features.md#secure-sockets-layer-ssltls-termination) 또는 [종단 간 tls 암호화](https://docs.microsoft.com/azure/application-gateway/ssl-overview)를 원하는 경우 HTTPS를 선택 합니다. 클라이언트와 응용 프로그램 게이트웨이 간의 트래픽이 암호화 됩니다. 그리고 TLS 연결은 application gateway에서 종료 됩니다. 종단 간 TLS 암호화를 원하는 경우 HTTPS를 선택 하 고 **백 엔드 HTTP** 설정을 구성 해야 합니다. 이렇게 하면 응용 프로그램이 응용 프로그램 게이트웨이에서 백 엔드에서 이동할 때 트래픽이 다시 암호화 됩니다.


TLS 종단 및 종단 간 TLS 암호화를 구성 하려면 응용 프로그램 게이트웨이에서 대칭 키를 파생할 수 있도록 수신기에 인증서를 추가 해야 합니다. 이는 TLS 프로토콜 사양에 따라 결정 됩니다. 대칭 키는 게이트웨이로 전송 되는 트래픽을 암호화 하 고 암호 해독 하는 데 사용 됩니다. 게이트웨이 인증서는 PFX (개인 정보 교환) 형식 이어야 합니다. 이 형식을 사용 하면 게이트웨이에서 트래픽을 암호화 하 고 암호 해독 하는 데 사용 하는 개인 키를 내보낼 수 있습니다.

#### <a name="supported-certificates"></a>지원 되는 인증서

[TLS 종료에 대해 지원 되는 인증서를](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)참조 하세요.

### <a name="additional-protocol-support"></a>추가 프로토콜 지원

#### <a name="http2-support"></a>HTTP2 지원

HTTP/2 프로토콜 지원은 응용 프로그램 게이트웨이 수신기에만 연결 되는 클라이언트에서 사용할 수 있습니다. 백 엔드 서버 풀에 대 한 통신은 HTTP/1.1을 통해 전달 됩니다. 기본적으로 HTTP/2 지원은 사용할 수 없습니다. 다음 Azure PowerShell 코드 조각에서는이를 사용 하도록 설정 하는 방법을 보여 줍니다.

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket 지원

WebSocket 지원은 기본적으로 사용 하도록 설정 되어 있습니다. 사용 하거나 사용 하지 않도록 설정할 수 있는 사용자 구성 설정이 없습니다. Websocket은 HTTP 및 HTTPS 수신기 모두와 함께 사용할 수 있습니다.

### <a name="custom-error-pages"></a>사용자 지정 오류 페이지

전역 수준 또는 수신기 수준에서 사용자 지정 오류를 정의할 수 있습니다. 그러나 Azure Portal에서 전역 수준 사용자 지정 오류 페이지를 만드는 것은 현재 지원 되지 않습니다. 수신기 수준에서 403 웹 응용 프로그램 방화벽 오류 또는 502 유지 관리 페이지에 대 한 사용자 지정 오류 페이지를 구성할 수 있습니다. 또한 지정 된 오류 상태 코드에 대해 공개적으로 액세스할 수 있는 blob URL을 지정 해야 합니다. 자세한 내용은 [Application Gateway 사용자 지정 오류 페이지 만들기](https://docs.microsoft.com/azure/application-gateway/custom-error)를 참조하세요.

![Application Gateway 오류 코드](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

전역 사용자 지정 오류 페이지를 구성 하려면 [Azure PowerShell 구성](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration)을 참조 하세요.

### <a name="tls-policy"></a>TLS 정책

TLS/SSL 인증서 관리를 중앙 집중화 하 고 백 엔드 서버 팜에 대 한 암호화 해독 오버 헤드를 줄일 수 있습니다. 중앙 집중식 TLS 처리를 사용 하 여 보안 요구 사항에 적합 한 중앙 TLS 정책을 지정할 수도 있습니다. *기본*, *미리 정의*됨 또는 *사용자 지정* TLS 정책을 선택할 수 있습니다.

Tls 프로토콜 버전을 제어 하도록 TLS 정책을 구성 합니다. Tls 1.0, TLS 1.1 및 TLS 1.2의 TLS 핸드셰이크에 최소 프로토콜 버전을 사용 하도록 응용 프로그램 게이트웨이를 구성할 수 있습니다. 기본적으로 SSL 2.0 및 3.0은 사용 하지 않도록 설정 되며 구성할 수 없습니다. 자세한 내용은 [APPLICATION GATEWAY TLS 정책 개요](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)를 참조 하세요.

수신기를 만든 후에는 요청 라우팅 규칙과 연결 합니다. 이 규칙은 수신기에서 수신 된 요청을 백 엔드로 라우팅하는 방법을 결정 합니다.

## <a name="request-routing-rules"></a>라우팅 규칙 요청

Azure Portal를 사용 하 여 응용 프로그램 게이트웨이를 만들 때 기본 규칙 (*rule1*)을 만듭니다. 이 규칙은 기본 수신기 (*appGatewayHttpListener*)를 기본 백 엔드 풀 (*appGatewayBackendPool*)과 기본 백 엔드 HTTP 설정 (*appGatewayBackendHttpSettings*)에 바인딩합니다. 게이트웨이를 만든 후에는 기본 규칙의 설정을 편집 하거나 새 규칙을 만들 수 있습니다.

### <a name="rule-type"></a>규칙 유형

규칙을 만들 때 [ *기본* 및 *경로 기반*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules)중에서 선택 합니다.

- 연결 된 수신기의 모든 요청 (예: * <i></i> contoso.com/ \* )* 을 단일 백 엔드 풀로 전달 하려면 기본을 선택 합니다.
- 특정 URL 경로에서 특정 백 엔드 풀로 요청을 라우팅하 려는 경우 경로 기반을 선택 합니다. 경로 패턴은 해당 쿼리 매개 변수가 아닌 URL 경로에만 적용 됩니다.

#### <a name="order-of-processing-rules"></a>처리 규칙 순서

V1 및 v2 SKU의 경우 들어오는 요청의 패턴 일치는 경로가 경로 기반 규칙의 URL 경로 맵에 나열 된 순서 대로 처리 됩니다. 요청이 경로 맵에 있는 두 개 이상의 경로에 있는 패턴과 일치 하면 먼저 나열 된 경로가 일치 됩니다. 그리고 요청은 해당 경로와 연결 된 백 엔드에 전달 됩니다.

### <a name="associated-listener"></a>연결 된 수신기

수신기와 연결 된 *요청 라우팅 규칙* 을 평가 하 여 요청을 라우팅할 백 엔드 풀을 결정 하도록 수신기를 규칙에 연결 합니다.

### <a name="associated-back-end-pool"></a>연결 된 백 엔드 풀

수신기가 수신 하는 요청을 처리 하는 백 엔드 대상이 포함 된 백 엔드 풀을 규칙에 연결 합니다.

 - 기본 규칙의 경우 백 엔드 풀은 하나만 허용 됩니다. 연결 된 수신기에 대 한 모든 요청은 해당 백 엔드 풀로 전달 됩니다.

 - 경로 기반 규칙의 경우 각 URL 경로에 해당 하는 여러 백 엔드 풀을 추가 합니다. 입력 한 URL 경로와 일치 하는 요청은 해당 백 엔드 풀로 전달 됩니다. 또한 기본 백 엔드 풀을 추가 합니다. 규칙의 모든 URL 경로와 일치 하지 않는 요청은 해당 풀로 전달 됩니다.

### <a name="associated-back-end-http-setting"></a>연결 된 백 엔드 HTTP 설정

각 규칙에 대 한 백 엔드 HTTP 설정을 추가 합니다. 요청은이 설정에 지정 된 포트 번호, 프로토콜 및 기타 정보를 사용 하 여 응용 프로그램 게이트웨이에서 백 엔드 대상으로 라우팅됩니다.

기본 규칙의 경우 하나의 백 엔드 HTTP 설정만 허용 됩니다. 연결 된 수신기에 대 한 모든 요청은이 HTTP 설정을 사용 하 여 해당 백 엔드 대상으로 전달 됩니다.

경로 기반 규칙의 경우 각 URL 경로에 해당 하는 여러 백 엔드 HTTP 설정을 추가 합니다. 이 설정에서 URL 경로와 일치 하는 요청은 각 URL 경로에 해당 하는 HTTP 설정을 사용 하 여 해당 백 엔드 대상으로 전달 됩니다. 또한 기본 HTTP 설정을 추가 합니다. 이 규칙의 모든 URL 경로와 일치 하지 않는 요청은 기본 HTTP 설정을 사용 하 여 기본 백 엔드 풀로 전달 됩니다.

### <a name="redirection-setting"></a>리디렉션 설정

기본 규칙에 대해 리디렉션이 구성 된 경우 연결 된 수신기에 대 한 모든 요청이 대상으로 리디렉션됩니다. *전역* 리디렉션입니다. 경로 기반 규칙에 대해 리디렉션이 구성 된 경우 특정 사이트 영역의 요청만 리디렉션됩니다. 예를 들면 */cart/ \* *로 표시 되는 쇼핑 카트 영역이 있습니다. *경로 기반* 리디렉션입니다.

리디렉션에 대 한 자세한 내용은 [Application Gateway 리디렉션 개요](redirect-overview.md)를 참조 하세요.

#### <a name="redirection-type"></a>리디렉션 유형

필요한 리디렉션 유형 ( *301)*, *임시 (307)*, *찾음 (302)* 또는 *기타 참조 (303)* 를 선택 합니다.

#### <a name="redirection-target"></a>리디렉션 대상

다른 수신기 또는 외부 사이트를 리디렉션 대상으로 선택 합니다.

##### <a name="listener"></a>수신기

한 수신기에서 다른 수신기로 트래픽을 리디렉션하는 리디렉션 대상으로 수신기를 선택 합니다. 이 설정은 HTTP에서 HTTPS로의 리디렉션을 사용 하도록 설정 하려는 경우에 필요 합니다. 들어오는 HTTPS 요청을 확인 하는 대상 수신기에 들어오는 HTTP 요청을 확인 하는 소스 수신기에서 트래픽을 리디렉션합니다. 리디렉션 대상에 전달 되는 요청의 원래 요청에서 쿼리 문자열과 경로를 포함 하도록 선택할 수도 있습니다.

![Application Gateway 구성 요소 대화 상자](./media/configuration-overview/configure-redirection.png)

HTTP에서 HTTPS로의 리디렉션에 대 한 자세한 내용은 다음을 참조 하세요.
- [Azure Portal를 사용 하 여 HTTP에서 HTTPS로 리디렉션](redirect-http-to-https-portal.md)
- [PowerShell을 사용 하 여 HTTP에서 HTTPS로 리디렉션](redirect-http-to-https-powershell.md)
- [Azure CLI를 사용 하 여 HTTP에서 HTTPS로 리디렉션](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>외부 사이트

이 규칙과 연결 된 수신기의 트래픽을 외부 사이트에 리디렉션하려면 외부 사이트를 선택 합니다. 리디렉션 대상에 전달 되는 요청의 원래 요청에서 쿼리 문자열을 포함 하도록 선택할 수 있습니다. 원래 요청에 있던 외부 사이트에 경로를 전달할 수 없습니다.

리디렉션에 대 한 자세한 내용은 다음을 참조 하세요.
- [PowerShell을 사용 하 여 외부 사이트로 트래픽 리디렉션](redirect-external-site-powershell.md)
- [CLI를 사용 하 여 외부 사이트로 트래픽 리디렉션](redirect-external-site-cli.md)

### <a name="rewrite-http-headers-and-url"></a>HTTP 헤더 및 URL 다시 쓰기

다시 쓰기 규칙을 사용 하 여 요청 및 응답 패킷이 응용 프로그램 게이트웨이를 통해 클라이언트와 백 엔드 풀 간에 이동할 때 URL 경로 및 쿼리 문자열 매개 변수 뿐만 아니라 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다.

헤더 및 URL 매개 변수는 정적 값 또는 다른 헤더 및 서버 변수로 설정할 수 있습니다. 클라이언트 IP 주소 추출, 백 엔드에 대 한 중요 한 정보 제거, 보안 추가 등의 중요 한 사용 사례에 도움이 됩니다.
자세한 내용은 다음을 참조하세요.

 - [HTTP 헤더 및 URL 다시 작성 개요](rewrite-http-headers-url.md)
 - [HTTP 헤더 재작성 구성](rewrite-http-headers-portal.md)
 - [URL 재작성 구성](rewrite-url-portal.md)

## <a name="http-settings"></a>HTTP 설정

응용 프로그램 게이트웨이는 여기에서 지정한 구성을 사용 하 여 백 엔드 서버로 트래픽을 라우팅합니다. HTTP 설정을 만든 후 하나 이상의 요청 라우팅 규칙과 연결 해야 합니다.

### <a name="cookie-based-affinity"></a>쿠키 기반 선호도

Azure 애플리케이션 게이트웨이는 사용자 세션을 유지 관리 하기 위해 게이트웨이 관리 쿠키를 사용 합니다. 사용자가 Application Gateway에 대 한 첫 번째 요청을 보내면 해당 요청은 세션 정보를 포함 하는 해시 값을 사용 하 여 응답에 선호도 쿠키를 설정 하므로 선호도 쿠키를 전송 하는 후속 요청은 동일 하 게 유지 관리 하기 위해 동일한 백 엔드 서버로 라우팅됩니다. 

이 기능은 동일한 서버에 사용자 세션을 유지 하려는 경우와 사용자 세션에 대해 세션 상태가 서버에 로컬로 저장 된 경우에 유용 합니다. 응용 프로그램에서 쿠키 기반 선호도를 처리할 수 없는 경우에는이 기능을 사용할 수 없습니다. 이를 사용 하려면 클라이언트에서 쿠키를 지원 하는지 확인 합니다.

[Chromium browser](https://www.chromium.org/Home) [V80 업데이트](https://chromiumdash.appspot.com/schedule) 는 [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) 특성이 없는 HTTP 쿠키를 SameSite = 느슨한으로 처리 해야 하는 것을 요구 합니다. CORS (크로스-원본 자원 공유) 요청의 경우 타사 컨텍스트에서 쿠키를 보내야 하는 경우 SameSite = None을 사용 해야 합니다 *. 보안* 특성 및이를 HTTPS를 통해 전송 해야 합니다. 그렇지 않으면 HTTP 전용 시나리오에서 브라우저는 타사 컨텍스트의 쿠키를 전송 하지 않습니다. Chrome에서이 업데이트의 목표는 보안을 강화 하 고 CSRF (교차 사이트 요청 위조) 공격을 방지 하는 것입니다. 

이러한 변경을 지원 하기 위해 17 2020 년 2 월 Application Gateway 일부 터 기존 *Applicationgatewayaffinity* 쿠키 외에도 *ApplicationGatewayAffinityCORS* 이라는 다른 쿠키를 삽입 합니다. *ApplicationGatewayAffinityCORS* 쿠키에 추가 된 두 개의 특성이 있습니다 (*"SameSite = None;). Secure "*)를 설정 하므로 원본 간 요청에 대해서도 고정 세션이 유지 됩니다.

기본 선호도 쿠키 이름은 *Applicationgatewayaffinity* 이며 변경할 수 있습니다. 사용자 지정 선호도 쿠키 이름을 사용 하는 경우 추가 쿠키는 CORS로 CORS를 사용 하 여 추가 됩니다. 예를 들면 *CustomCookieNameCORS*입니다.

> [!NOTE]
> *SameSite = None* 특성을 설정 하는 경우 쿠키에 *보안* 플래그가 포함 되 고 HTTPS를 통해 전송 되어야 합니다.  CORS를 통해 세션 선호도가 필요한 경우 작업을 HTTPS로 마이그레이션해야 합니다. 여기 Application Gateway에 대 한 TLS 오프 로드 및 종단 간 TLS 설명서를 참조 하세요. [개요](ssl-overview.md), [Azure Portal를 사용 하 여 tls 종료를 사용 하 여 응용 프로그램 게이트웨이 구성](create-ssl-portal.md), [포털에서 Application Gateway를 사용 하 여 종단 간 tls 구성](end-to-end-ssl-portal.md)

### <a name="connection-draining"></a>연결 드레이닝

연결 드레이닝은 계획 된 서비스 업데이트 중에 백 엔드 풀 멤버를 정상적으로 제거 하는 데 도움이 됩니다. HTTP 설정에서 연결 드레이닝을 사용 하도록 설정 하 여 백 엔드 풀의 모든 멤버에이 설정을 적용할 수 있습니다. 이를 통해 백 엔드 풀의 모든 있음이 인스턴스는 기존 연결을 계속 유지 하 고 구성 가능한 시간 제한에 대해 진행 중인 요청을 처리할 수 있으며 새 요청 또는 연결을 받지 않습니다. 이에 대 한 유일한 예외는 게이트웨이 관리 세션 선호도로 인해 있음이 인스턴스에 대해 바인딩된 요청이 며 있음이 인스턴스로 계속 전달 됩니다. 연결 드레이닝은 백 엔드 풀에서 명시적으로 제거 된 백 엔드 인스턴스에 적용 됩니다.

### <a name="protocol"></a>프로토콜

Application Gateway 백 엔드 서버에 대 한 라우팅 요청에 HTTP 및 HTTPS를 모두 지원 합니다. HTTP를 선택 하는 경우 백 엔드 서버에 대 한 트래픽이 암호화 되지 않습니다. 암호화 되지 않은 통신이 허용 되지 않는 경우 HTTPS를 선택 합니다.

수신기에서 HTTPS와 결합 된이 설정은 [종단 간 TLS](ssl-overview.md)를 지원 합니다. 이를 통해 백 엔드에 암호화 된 중요 한 데이터를 안전 하 게 전송할 수 있습니다. 종단 간 TLS를 사용 하는 백 엔드 풀의 각 백 엔드 서버는 보안 통신을 허용 하는 인증서를 사용 하 여 구성 해야 합니다.

### <a name="port"></a>포트

이 설정은 백 엔드 서버가 application gateway의 트래픽을 수신 대기 하는 포트를 지정 합니다. 1에서 65535 사이의 포트를 구성할 수 있습니다.

### <a name="request-timeout"></a>요청 시간 초과

이 설정은 application gateway가 백 엔드 서버에서 응답을 받기 위해 대기 하는 시간 (초)입니다.

### <a name="override-back-end-path"></a>백 엔드 경로 재정의

이 설정을 사용 하면 요청을 백 엔드에서 전달할 때 사용할 선택적 사용자 지정 전달 경로를 구성할 수 있습니다. **백 엔드 경로 재정의** 필드의 사용자 지정 경로와 일치 하는 들어오는 경로 부분은 전달 된 경로에 복사 됩니다. 다음 표에서는이 기능의 작동 방식을 보여 줍니다.

- HTTP 설정이 기본 요청 라우팅 규칙에 연결 된 경우:

  | 원래 요청  | 백 엔드 경로 재정의 | 백 엔드에 전달 된 요청 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | 설명은/override            | /override/home/              |
  | /home/secondhome/ | 설명은/override            | /override/home/secondhome/   |

- HTTP 설정이 경로 기반 요청 라우팅 규칙에 연결 된 경우:

  | 원래 요청           | 경로 규칙       | 백 엔드 경로 재정의 | 백 엔드에 전달 된 요청 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | 설명은/override            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | 설명은/override            | /override/home/secondhome/   |
  | /home/                     | pathrule      | 설명은/override            | /override/home/              |
  | /home/secondhome/          | pathrule      | 설명은/override            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | 설명은/override            | 설명은/override                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | 설명은/override            | /override/secondhome/        |
  | pathrule                 | pathrule      | 설명은/override            | 설명은/override                   |

### <a name="use-for-app-service"></a>App service에 사용

Azure App Service 백 엔드에 대 한 두 가지 필수 설정을 선택 하는 UI 전용 바로 가기입니다. **백 엔드 주소에서 호스트 이름을 선택**하 고 아직 없는 경우 새 사용자 지정 프로브를 만듭니다. 자세한 내용은이 문서의 [백 엔드 주소 설정에서 호스트 이름 선택](#pick) 섹션을 참조 하세요. 새 프로브가 만들어지고 프로브 헤더는 백 엔드 구성원의 주소에서 선택 됩니다.

### <a name="use-custom-probe"></a>사용자 지정 프로브 사용

이 설정은 [사용자 지정 프로브](application-gateway-probe-overview.md#custom-health-probe) 를 HTTP 설정과 연결 합니다. HTTP 설정에는 사용자 지정 프로브를 하나만 연결할 수 있습니다. 사용자 지정 프로브를 명시적으로 연결 하지 않으면 [기본 프로브](application-gateway-probe-overview.md#default-health-probe-settings) 를 사용 하 여 백 엔드의 상태를 모니터링 합니다. 백 엔드의 상태 모니터링을 보다 효율적으로 제어 하기 위해 사용자 지정 프로브를 만드는 것이 좋습니다.

> [!NOTE]
> 해당 HTTP 설정이 수신기와 명시적으로 연결 되지 않은 경우 사용자 지정 프로브는 백 엔드 풀의 상태를 모니터링 하지 않습니다.

### <a name="pick-host-name-from-back-end-address"></a><a name="pick"></a>백 엔드 주소에서 호스트 이름 선택

이 기능은 요청의 *호스트* 헤더를 백 엔드 풀의 호스트 이름으로 동적으로 설정 합니다. IP 주소 또는 FQDN을 사용 합니다.

이 기능을 사용 하면 백 엔드의 도메인 이름이 application gateway의 DNS 이름과 다를 수 있으며 백 엔드는 특정 호스트 헤더를 사용 하 여 올바른 끝점을 확인 합니다.

예를 들어, 다중 테 넌 트 서비스는 백 엔드입니다. App service는 단일 IP 주소를 사용 하 여 공유 공간을 사용 하는 다중 테 넌 트 서비스입니다. 따라서 앱 서비스는 사용자 지정 도메인 설정에서 구성 된 호스트 이름을 통해서만 액세스할 수 있습니다.

기본적으로 사용자 지정 도메인 이름은 *example.azurewebsites.net*입니다. App service에 명시적으로 등록 되지 않은 호스트 이름 또는 응용 프로그램 게이트웨이의 FQDN을 통해 응용 프로그램 게이트웨이를 사용 하 여 app service에 액세스 하려면 원래 요청에서 app service의 호스트 이름으로 호스트 이름을 재정의 합니다. 이렇게 하려면 **백 엔드 주소에서 호스트 이름 선택** 설정을 사용 하도록 설정 합니다.

기존 사용자 지정 DNS 이름을 app service에 매핑한 사용자 지정 도메인의 경우이 설정을 사용 하도록 설정할 필요가 없습니다.

> [!NOTE]
> 전용 배포 인 App Service Environment에는이 설정이 필요 하지 않습니다.

### <a name="host-name-override"></a>호스트 이름 재정의

이 기능은 응용 프로그램 게이트웨이에서 들어오는 요청의 *호스트* 헤더를 지정한 호스트 이름으로 바꿉니다.

예를 들어 **호스트 이름** 설정에 *www.contoso.com* 가 지정 된 경우 `https://appgw.eastus.cloudapp.azure.com/path1` `https://www.contoso.com/path1` 요청이 백 엔드 서버에 전달 되 면 원래 요청 *이 *로 변경 됩니다.

## <a name="back-end-pool"></a>백 엔드 풀

백 엔드 풀은 특정 가상 머신, 가상 머신 확장 집합, IP 주소/FQDN 또는 app service의 네 가지 유형의 백 엔드 구성원으로 지정할 수 있습니다. 

백 엔드 풀을 만든 후 하나 이상의 요청 라우팅 규칙과 연결 해야 합니다. 또한 응용 프로그램 게이트웨이에서 각 백 엔드 풀에 대 한 상태 프로브를 구성 해야 합니다. 요청 라우팅 규칙 조건이 충족 되 면 응용 프로그램 게이트웨이는 해당 백 엔드 풀의 정상 서버 (상태 프로브에 의해 결정 됨)로 트래픽을 전달 합니다.

## <a name="health-probes"></a>상태 프로브

Application gateway는 기본적으로 백 엔드에서 모든 리소스의 상태를 모니터링 합니다. 하지만 상태 모니터링에 대 한 제어를 강화 하기 위해 각 백 엔드 HTTP 설정에 대 한 사용자 지정 프로브를 만드는 것이 좋습니다. 사용자 지정 프로브를 구성 하는 방법에 대 한 자세한 내용은 [사용자 지정 상태 프로브 설정](application-gateway-probe-overview.md#custom-health-probe-settings)을 참조 하세요.

> [!NOTE]
> 사용자 지정 상태 프로브를 만든 후에는 백 엔드 HTTP 설정에 연결 해야 합니다. 해당 HTTP 설정이 규칙을 사용 하 여 수신기와 명시적으로 연결 되지 않은 경우 사용자 지정 프로브는 백 엔드 풀의 상태를 모니터링 하지 않습니다.

## <a name="next-steps"></a>다음 단계

Application Gateway 구성 요소에 대해 배웠으므로 이제 다음을 수행할 수 있습니다.

- [Azure Portal에서 응용 프로그램 게이트웨이 만들기](quick-create-portal.md)
- [PowerShell을 사용 하 여 응용 프로그램 게이트웨이 만들기](quick-create-powershell.md)
- [Azure CLI를 사용하여 Application Gateway 만들기](quick-create-cli.md)
