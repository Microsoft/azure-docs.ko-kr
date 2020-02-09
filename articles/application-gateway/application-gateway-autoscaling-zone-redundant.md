---
title: 자동 크기 조정 및 영역 중복 Application Gateway v2
description: 이 문서에서는 자동 크기 조정 및 영역 중복 기능을 포함 하는 Azure 애플리케이션 Standard_v2 및 WAF_v2 SKU를 소개 합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/09/2019
ms.author: victorh
ms.openlocfilehash: 8fe38870f593dd57d8e4dad5601ea404e99c3d10
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031563"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>자동 크기 조정 및 영역 중복 Application Gateway v2 

Application Gateway 및 WAF (웹 응용 프로그램 방화벽)는 Standard_v2 및 WAF_v2 SKU 에서도 사용할 수 있습니다. V2 SKU는 성능 향상을 제공 하 고 자동 크기 조정, 영역 중복성 및 정적 Vip 지원과 같은 중요 한 새 기능에 대한 지원을 추가 합니다. 표준 및 WAF SKU 아래의 기존 기능은 새 v2 SKU에서 계속 지원 되지만, [비교](#differences-with-v1-sku) 섹션에 몇 가지 예외가 있습니다.

새 v2 SKU에는 다음과 같은 향상 된 기능이 포함 되어 있습니다.

- **자동 크기 조정**: 자동 크기 조정 SKU의 Application Gateway 또는 WAF 배포는 트래픽 부하 패턴의 변화에 따라 강화 또는 축소할 수 있습니다. 또한 자동 크기 조정을 사용하면 프로비전 시 배포 크기 또는 인스턴스 수를 선택할 필요가 없습니다. 이 SKU는 진정한 탄력성을 제공 합니다. Standard_v2 및 WAF_v2 SKU에서 Application Gateway는 고정 용량 (자동 크기 조정 사용 안 함)과 자동 크기 조정 사용 모드에서 모두 작동할 수 있습니다. 고정 용량 모드는 워크로드가 일관적이고 예측 가능한 시나리오에 유용합니다. 자동 크기 조정 모드는 응용 프로그램 트래픽의 분산을 표시 하는 응용 프로그램에서 유용 합니다.
- **영역 중복성**: Application Gateway 또는 waf 배포는 여러 가용성 영역에 걸쳐 있을 수 있으므로 Traffic Manager를 사용 하 여 각 영역에 별도의 Application Gateway 인스턴스를 프로 비전 할 필요가 없습니다. Application Gateway 인스턴스가 배포 되는 단일 영역 또는 여러 영역을 선택할 수 있으며,이로 인해 영역 오류에 대한 복원 력이 향상 됩니다. 애플리케이션에 대한 백 엔드 풀을 가용성 영역 전반에 유사하게 배포할 수 있습니다.

  영역 중복성은 Azure 영역을 사용할 수 있는 경우에만 사용할 수 있습니다. 다른 지역에서는 다른 모든 기능이 지원 됩니다. 자세한 내용은 [Azure에서 가용성 영역이란?](../availability-zones/az-overview.md#services-support-by-region)을 참조하세요.
- **정적 vip**: APPLICATION GATEWAY v2 SKU는 정적 vip 형식을 독점적으로 지원 합니다. 이렇게 하면 응용 프로그램 게이트웨이와 연결 된 VIP가 다시 시작 된 후에도 배포의 수명 주기 동안 변경 되지 않습니다.  V1에는 정적 VIP가 없으므로 응용 프로그램 게이트웨이를 통해 App Services 하려면 도메인 이름 라우팅에 대한 IP 주소 대신 응용 프로그램 게이트웨이 URL을 사용 해야 합니다.
- **헤더 재작성**: Application Gateway를 사용 하면 v2 SKU를 사용 하 여 HTTP 요청 및 응답 헤더를 추가, 제거 또는 업데이트할 수 있습니다. 자세한 내용은 [Application Gateway를 사용 하 여 HTTP 헤더 다시 작성](rewrite-http-headers.md) 을 참조 하세요.
- **Key Vault 통합 (미리 보기)** : Application Gateway V2는 HTTPS 사용 수신기에 연결 된 서버 인증서에 대한 Key Vault (공개 미리 보기)와의 통합을 지원 합니다. 자세한 내용은 [Key Vault 인증서를 사용 하는 SSL 종료](key-vault-certs.md)를 참조 하십시오.
- **Azure Kubernetes Service 수신 컨트롤러 (미리 보기)** : Application Gateway V2 수신 컨트롤러를 사용 하면 Azure 애플리케이션 게이트웨이를 AKS CLUSTER 라는 AKS (Azure Kubernetes Service)에 대한 수신으로 사용할 수 있습니다. 자세한 내용은 [설명서 페이지](https://azure.github.io/application-gateway-kubernetes-ingress/)를 참조 하세요.
- **성능 향상**: v2 Sku는 표준/WAF sku와 비교해 서 최대 5 배 더 나은 SSL 오프 로드 성능을 제공 합니다.
- **더 빠른 배포 및 업데이트 시간** V2 SKU는 표준/WAF SKU와 비교 하 여 더 빠른 배포 및 업데이트 시간을 제공 합니다. 여기에는 WAF 구성 변경 내용도 포함 됩니다.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>지원되는 지역

Standard_v2 및 WAF_v2 SKU는 미국 중 북부, 미국 동부, 미국 서 부, 미국 서 부 2, 미국 동부, 미국 동부 2, 미국 동부, 북부, 유럽 서부, 동남 아시아, 프랑스 중부, 영국 서부, 일본 동부, 일본 서 부, 오스트레일리아 동부와 같은 지역에서 사용할 수 있습니다. , 오스트레일리아 남동쪽, 브라질 남부, 캐나다 중부, 캐나다 동부, 동아시아, 대한민국 중부, 한국 남부, 영국 남부, 인도 중부, 인도 서 부, 인도 남부.

## <a name="pricing"></a>가격

V2 SKU를 사용 하는 경우 가격 책정 모델은 사용량에 따라 결정 되며 더 이상 인스턴스 수 나 크기에 연결 되지 않습니다. V2 SKU 가격에는 다음과 같은 두 가지 구성 요소가 있습니다.

- **고정 가격** -Standard_v2 또는 WAF_v2 게이트웨이를 프로 비전 하는 매시간 (또는 일부 시간) 가격입니다. 0 개 이상의 추가 최소 인스턴스는 항상 고정 가격이 포함 된 서비스의 고가용성을 보장 합니다.
- **용량** 단가-고정 비용 외에 부과 되는 소비 기반 비용입니다. 용량 단위 요금은 매시간 또는 부분적으로 계산 됩니다. 용량 단위 계산 단위, 영구 연결 및 처리량의 세 가지 차원이 있습니다. Compute 단위는 소비 된 프로세서 용량의 측정 단위입니다. 계산 단위에 영향을 주는 요소는 TLS 연결/초, URL 재작성 계산 및 WAF 규칙 처리입니다. 영구 연결은 지정 된 청구 간격으로 application gateway에 설정 된 TCP 연결을 측정 한 것입니다. 처리량은 지정 된 청구 간격에 따라 시스템에서 처리 되는 평균 메가 비트/초입니다.  청구는 예약 된 인스턴스 수 위의 모든 항목에 대한 용량 단위 수준에서 수행 됩니다.

각 용량 단위는 1 개의 compute 단위 또는 2500 영구 연결 또는 2.22 Mbps 처리량으로 구성 됩니다.

Compute 단위 지침:

- **Standard_v2** -각 계산 단위는 RSA 2048 비트 키 TLS 인증서를 사용 하 여 초당 약 50 연결을 사용할 수 있습니다.
- **WAF_v2** -각각의 compute 단위는 초당 초당 10 개 요청을 지원할 수 있습니다. 70-30%의 70 트래픽 혼합은 2 k b 미만의 요청을 받고 WAF 성능은 현재 응답 크기의 영향을 받지 않습니다.

> [!NOTE]
> 각 인스턴스는 현재 약 10 개의 용량 단위를 지원할 수 있습니다.
> 계산 단위에서 처리할 수 있는 요청 수는 TLS 인증서 키 크기, 키 교환 알고리즘, 헤더 다시 작성 및 WAF 들어오는 요청 크기의 경우와 같은 다양 한 조건에 따라 달라 집니다. Compute 단위당 요청 빈도를 확인 하려면 응용 프로그램 테스트를 수행 하는 것이 좋습니다. 청구를 시작 하기 전에 용량 단위와 계산 단위를 메트릭으로 사용할 수 있습니다.

다음 표에서는 예제 가격을 보여 주며 설명 목적 으로만 사용 됩니다.

**미국 동부 가격**:

|              SKU 이름                             | 고정 가격 ($/시간)  | 용량 단가 ($/시간)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0.20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

가격 책정에 대한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/application-gateway/)를 참조 하세요. 

**예제 1**

Application Gateway Standard_v2은 수동 크기 조정 모드에서 자동 크기 조정 없이 프로 비전 되어 5 개의 인스턴스로 고정 된 용량을 제공 합니다.

고정 가격 = 744 (시간) * $0.20 = $148.8 <br>
용량 단위 = 744 (시간) * 인스턴스당 10 개의 용량 단위 * 5 인스턴스 * $0.008 용량 단위 시간당 = $297.6

총 가격 = $148.8 + $297.6 = $446.4

**예제 2**

Application Gateway standard_v2는 1 개월 동안 최소 인스턴스를 사용 하 여 프로 비전 되 고,이 시간 동안에는 25 개의 새 SSL 연결, 평균 8.88 Mbps 데이터 전송을 수신 합니다. 연결이 수명이 짧은 경우 가격은 다음과 같습니다.

고정 가격 = 744 (시간) * $0.20 = $148.8

용량 단가 = 744 (시간) * Max (연결에 대한 25/50 계산 단위/초, 처리량을 위한 8.88/2.22 용량 단위) * $0.008 = 744 * 4 * 0.008 = $23.81

Total price = $148.8 + 23.81 = $172.61

여기에서 볼 수 있듯이 전체 인스턴스가 아닌 4 개의 용량 단위에 대해서만 요금이 청구 됩니다. 

> [!NOTE]
> Max 함수는 값 쌍에서 가장 큰 값을 반환 합니다.


**예 3**

Application Gateway standard_v2는 최소 5 개 인스턴스를 사용 하 여 한 달 동안 프로 비전 됩니다. 트래픽이 없고 연결이 짧은 경우 가격은 다음과 같습니다.

고정 가격 = 744 (시간) * $0.20 = $148.8

용량 단가 = 744 (시간) * Max (연결에 대한 0/50 계산 단위/초, 처리량을 위한 0/2.22 용량 단위) * $0.008 = 744 * 50 * 0.008 = $297.60

Total price = $148.80 + 297.60 = $446.4

이 경우 트래픽이 없더라도 5 개의 인스턴스 전체에 대해 요금이 청구 됩니다.

**예제 4**

Application Gateway standard_v2는 최소 5 개 인스턴스를 사용 하 여 한 달 동안 프로 비전 되지만 이번에는 평균 125 mbps 데이터 전송과 초당 25 개의 SSL 연결을 제공 합니다. 트래픽이 없고 연결이 짧은 경우 가격은 다음과 같습니다.

고정 가격 = 744 (시간) * $0.20 = $148.8

용량 단가 = 744 (시간) * Max (연결에 대한 25/50 계산 단위/초, 처리량을 위한 125/2.22 용량 단위) * $0.008 = 744 * 57 * 0.008 = $339.26

Total price = $148.80 + 339.26 = $488.06

이 경우 전체 5 개의 인스턴스 및 7 개의 용량 단위 (인스턴스의 7/10)에 대해 요금이 청구 됩니다.  

**예제 5**

Application Gateway WAF_v2는 한 달 동안 프로 비전 됩니다. 이 시간 동안에는 25 개의 새 SSL 연결/초, 평균 8.88 Mbps 데이터 전송 및 초당 80 요청을 수신 합니다. 연결이 단기 하 고 응용 프로그램에 대한 단위 계산을 계산 단위 당 10 개의 RPS를 지 원하는 경우 가격은 다음과 같습니다.

고정 가격 = 744 (시간) * $0.36 = $267.84

용량 단가 = 744 (시간) * Max (계산 단위 최대 (연결/초, 80/10 WAF RPS), 처리량을 위한 8.88/2.22 용량 단위) * $0.0144 = 744 * 8 * 0.0144 = $85.71

총 가격 = $267.84 + $85.71 = $353.55

> [!NOTE]
> Max 함수는 값 쌍에서 가장 큰 값을 반환 합니다.

## <a name="scaling-application-gateway-and-waf-v2"></a>크기 조정 Application Gateway 및 WAF v2

Application Gateway 및 WAF는 다음과 같은 두 가지 모드로 확장 되도록 구성할 수 있습니다.

- 자동 크기 **조정-자동 크기 조정을 사용** 하는 경우 Application Gateway 및 waf v2 sku는 응용 프로그램 트래픽 요구 사항에 따라 확장 또는 축소 됩니다. 이 모드는 응용 프로그램에 더 나은 탄력성을 제공 하 고 응용 프로그램 게이트웨이 크기 또는 인스턴스 수를 추측 하지 않아도 됩니다. 또한이 모드를 사용 하면 예상 된 최대 트래픽 부하에 대해 게이트웨이가 최대 프로 비전 된 용량에서 실행 되도록 요구 하지 않아 비용을 절감할 수 있습니다. 최소 및 최대 인스턴스 수를 지정 해야 합니다. 최소 용량을 사용 하면 트래픽이 없는 경우에도 Application Gateway 및 WAF v2가 지정 된 최소 인스턴스 수에 속하지 않습니다. 각 인스턴스는 예약 된 추가 용량 단위 10 개로 계산 됩니다. 0은 예약 된 용량을 의미 하지 않으며 본질적으로 자동 크기 조정입니다. 0 개 이상의 추가 최소 인스턴스는 항상 고정 가격이 포함 된 서비스의 고가용성을 보장 합니다. 필요에 따라 최대 인스턴스 수를 지정 하 여 Application Gateway 지정 된 인스턴스 수를 초과 하지 않도록 할 수도 있습니다. 게이트웨이에서 제공 하는 트래픽 양에 대한 요금이 계속 청구 됩니다. 인스턴스 수의 범위는 0 ~ 125입니다. 지정 하지 않으면 최대 인스턴스 수의 기본값은 20입니다.
- **수동** -게이트웨이가 자동 크기 조정 되지 않는 수동 모드를 선택할 수 있습니다. 이 모드에서는 Application Gateway 또는 WAF가 처리할 수 있는 것 보다 많은 트래픽이 있는 경우 트래픽이 손실 될 수 있습니다. 수동 모드에서는 인스턴스 수를 지정 하는 것이 필수입니다. 인스턴스 수는 1 개에서 125 개 사이에 다를 수 있습니다.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>V1 SKU와 v2 SKU 간의 기능 비교

다음 표에서는 각 SKU에서 사용할 수 있는 기능을 비교 합니다.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 자동 확장                                       |          | &#x2713; |
| 영역 중복                                   |          | &#x2713; |
| 정적 VIP                                        |          | &#x2713; |
| AKS (Azure Kubernetes Service) 수신 컨트롤러 |          | &#x2713; |
| Azure Key Vault 통합                       |          | &#x2713; |
| HTTP (S) 헤더 다시 작성                           |          | &#x2713; |
| URL 기반 라우팅                                 | &#x2713; | &#x2713; |
| 다중 사이트 호스팅                             | &#x2713; | &#x2713; |
| 트래픽 리디렉션                               | &#x2713; | &#x2713; |
| WAF(웹 애플리케이션 방화벽)                    | &#x2713; | &#x2713; |
| WAF 사용자 지정 규칙                                  |          | &#x2713; |
| SSL(Secure Sockets Layer) 종료            | &#x2713; | &#x2713; |
| 종단 간 SSL 암호화                         | &#x2713; | &#x2713; |
| 세션 선호도                                  | &#x2713; | &#x2713; |
| 사용자 지정 오류 페이지                                | &#x2713; | &#x2713; |
| WebSocket 지원                                 | &#x2713; | &#x2713; |
| HTTP/2 지원                                    | &#x2713; | &#x2713; |
| 연결 드레이닝                               | &#x2713; | &#x2713; |

> [!NOTE]
> 자동 크기 조정 v2 SKU는 이제 [기본 상태 프로브](application-gateway-probe-overview.md#default-health-probe) 를 지원 하 여 백 엔드 풀에 있는 모든 리소스의 상태를 자동으로 모니터링 하 고 비정상으로 간주 되는 백엔드 구성원을 강조 표시 합니다. 기본 상태 프로브는 사용자 지정 프로브 구성이 없는 백 엔드에 대해 자동으로 구성 됩니다. 자세히 알아보려면 [application gateway의 상태 프로브](application-gateway-probe-overview.md)를 참조 하세요.

## <a name="differences-with-v1-sku"></a>V1 SKU와의 차이점

|차이|세부 정보|
|--|--|
|인증 인증서|지원되지 않습니다.<br>자세한 내용은 [Application Gateway의 엔드투엔드 SSL 개요](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)를 참조하세요.|
|동일한 서브넷에서 Standard_v2와 표준 애플리케이션 게이트웨이 혼합|지원되지 않음|
|Application Gateway 서브넷의 UDR(사용자 정의 경로)|지원되지 않음|
|인바운드 포트 범위에 대한 NSG| - 65200 ~ 65535(Standard_v2 SKU)<br>- 65503 ~ 65534(Standard SKU)<br>자세한 내용은 [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)을 참조하세요.|
|Azure 진단의 성능 로그|지원되지 않습니다.<br>Azure 메트릭을 사용해야 합니다.|
|결제|청구는 2019 년 7 월 1 일에 시작 되도록 예약 됩니다.|
|FIPS 모드|현재는 지원되지 않습니다.|
|ILB 전용 모드|현재는 지원되지 않습니다. 공용 및 ILB 모드가 함께 지원됩니다.|
|Netwatcher 통합|지원되지 않습니다.|
|Azure Security Center 통합|아직 사용할 수 없습니다.

## <a name="migrate-from-v1-to-v2"></a>v1에서 v2로 마이그레이션

Azure PowerShell 스크립트는 v1 Application Gateway/WAF에서 v2 자동 크기 조정 SKU로 마이그레이션하는 데 도움이 되는 PowerShell 갤러리에서 사용할 수 있습니다. 이 스크립트는 v1 게이트웨이에서 구성을 복사 하는 데 도움이 됩니다. 트래픽 마이그레이션은 여전히 사용자의 책임입니다. 자세한 내용은 v 1에서 v 2 [로 Azure 애플리케이션 게이트웨이 마이그레이션](migrate-v1-v2.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: Azure 애플리케이션 게이트웨이를 사용 하 여 웹 트래픽 직접 전송-Azure Portal](quick-create-portal.md)
- [Azure PowerShell을 사용하여 예약된 가상 IP 주소로 자동 크기 조정, 영역 중복 애플리케이션 게이트웨이 만들기](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)에 대해 자세히 알아보세요.
- [Azure Firewall](../firewall/overview.md)에 대해 자세히 알아보세요.
