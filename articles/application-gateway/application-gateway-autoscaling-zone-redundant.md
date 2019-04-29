---
title: Azure의 자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기)
description: 이 문서에서는 자동 크기 조정 및 영역 중복 기능을 포함하는 Azure Application v2 SKU를 소개합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/6/2019
ms.author: victorh
ms.openlocfilehash: 95b14a0028134e522206f3595bc3b9ebf9aaf396
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128485"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>자동 크기 조정 및 영역 중복 Application Gateway(공개 미리 보기)

Application Gateway 및 WAF(웹 응용 프로그램 방화벽)는 새 v2 SKU에서 성능을 향상하고 자동 크기 조정, 영역 중복, 정적 VIP 지원 등의 중요한 새 기능을 지원하는 공개 미리 보기로 제공됩니다. 일반 공급 SKU의 기존 기능은 새 v2 SKU에서 계속 지원되며, 알려진 제한 사항 섹션에 몇 가지 예외가 나열되어 있습니다. 새 v2 SKU에는 다음과 같은 향상된 기능이 포함되어 있습니다.

- **자동 크기 조정**: 자동 크기 조정 SKU의 Application Gateway 또는 WAF 배포는 트래픽 부하 패턴의 변화에 따라 강화하거나 축소할 수 있습니다. 또한 자동 크기 조정을 사용하면 프로비전 시 배포 크기 또는 인스턴스 수를 선택할 필요가 없습니다. 이 SKU는 true 탄력성을 제공합니다. 새 SKU에서 Application Gateway는 고정 용량(자동 크기 조정이 해제됨) 및 자동 크기 조정이 설정된 모드에서 모두 작동할 수 있습니다. 고정 용량 모드는 워크로드가 일관적이고 예측 가능한 시나리오에 유용합니다. 자동 크기 조정 모드는 애플리케이션 트래픽이 자주 변하는 애플리케이션에 유용합니다.

- **영역 중복**: Application Gateway 또는 WAF 배포 하나가 여러 가용성 영역을 포함할 수 있으므로 Traffic Manager를 사용하여 각 영역에 별도의 Application Gateway 인스턴스를 프로비전하고 작동할 필요가 없습니다. Application Gateway 인스턴스가 배포되는 단일 영역 또는 여러 영역을 선택할 수 있기 때문에 영역 장애 복원력이 보장됩니다. 애플리케이션에 대한 백 엔드 풀을 가용성 영역 전반에 유사하게 배포할 수 있습니다.
- **성능 향상**: 자동 크기 조정 SKU는 일반 공급 SKU보다 최대 5배 높은 SSL 오프로드 성능을 제공합니다.
- **신속한 배포 및 업데이트 시간**: 자동 크기 조정 SKU는 일반적으로 제공되는 SKU보다 빠른 배포 및 업데이트 시간을 제공합니다.
- **정적 VIP**: Application Gateway VIP는 이제 정적 VIP 유형만 독점적으로 지원합니다. 이렇게 하면 응용 프로그램 게이트웨이에 연결 된 VIP를 다시 시작 후에 변경 되지 않는지 합니다.

> [!IMPORTANT]
> 자동 크기 조정 및 영역 중복 애플리케이션 게이트웨이 SKU는 현재 공개 미리 보기로 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

> [!NOTE]
> 자동 크기 조정 및 영역 중복 응용 프로그램 게이트웨이 SKU는 이제 [기본 상태 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe) 를 자동으로 해당 백 엔드 풀의 모든 리소스의 상태를 모니터링 하 고 간주 되는 백 엔드 멤버만 강조 표시 비정상입니다. 모든 사용자 지정 프로브 구성을 설정 하지 않은 한 이러한 모든 백 엔드에 대 한 기본 상태 프로브에서 자동으로 구성 합니다. 자세한 내용은 참조 하세요 [application gateway의 상태 프로브](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview)합니다.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>SKU v1 및 v2 SKU 기능 비교

다음 표에서 각 SKU를 사용 하 여 사용할 수 있는 기능을 비교 합니다.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| 자동 확장                                       |          | &#x2713; |
| 영역 중복                                   |          | &#x2713; |
| &nbsp;정적 VIP&nbsp;&nbsp;                      |          | &#x2713; |
| URL 기반 라우팅                                 | &#x2713; | &#x2713; |
| 다중 사이트 호스팅                             | &#x2713; | &#x2713; |
| 트래픽 리디렉션                               | &#x2713; | &#x2713; |
| WAF(웹 애플리케이션 방화벽)                    | &#x2713; | &#x2713; |
| SSL(Secure Sockets Layer) 종료            | &#x2713; | &#x2713; |
| 종단 간 SSL 암호화                         | &#x2713; | &#x2713; |
| 세션 선호도                                  | &#x2713; | &#x2713; |
| 사용자 지정 오류 페이지                                | &#x2713; | &#x2713; |
| HTTP (S) 헤더를 다시 작성                           |          | &#x2713; |
| WebSocket 지원                                 | &#x2713; | &#x2713; |
| HTTP/2 지원                                    | &#x2713; | &#x2713; |
| 연결 드레이닝                               | &#x2713; | &#x2713; |
| Azure Kubernetes Service (AKS) 수신 컨트롤러 |          | &#x2713; |

## <a name="supported-regions"></a>지원되는 지역

자동 크기 조정 SKU는 다음 하위 지역에서 사용할 수 있습니다. 미국 중북부, 미국 중남부, 미국 서부, 미국 서부 2, 미국 동부, 미국 동부 2, 미국 중부, 북유럽, 서유럽, 동남 아시아, 프랑스 중부, 영국 서부, 일본 동부, 일본 서부

## <a name="pricing"></a>가격

미리 보기 중에 무료입니다. Application gateway의 경우 Key Vault, virtual machines 처럼 이외의 리소스에 대 한 청구 되며, 등.

## <a name="known-issues-and-limitations"></a>알려진 문제 및 제한 사항

|문제|세부 정보|
|--|--|
|인증 인증서|지원되지 않습니다.<br>자세한 내용은 [Application Gateway의 엔드투엔드 SSL 개요](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)를 참조하세요.|
|동일한 서브넷에서 Standard_v2와 표준 애플리케이션 게이트웨이 혼합|지원되지 않음|
|Application Gateway 서브넷의 UDR(사용자 정의 경로)|지원되지 않음|
|인바운드 포트 범위에 대한 NSG| - 65200 ~ 65535(Standard_v2 SKU)<br>- 65503 ~ 65534(Standard SKU)<br>자세한 내용은 [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)을 참조하세요.|
|Azure 진단의 성능 로그|지원되지 않습니다.<br>Azure 메트릭을 사용해야 합니다.|
|결제|현재는 요금이 청구되지 않습니다.|
|FIPS 모드|현재는 지원되지 않습니다.|
|ILB 전용 모드|현재는 지원되지 않습니다. 공용 및 ILB 모드가 함께 지원됩니다.|
|Netwatcher 통합|공개 미리 보기에서는 지원되지 않습니다.|

## <a name="next-steps"></a>다음 단계
- [Azure PowerShell을 사용하여 예약된 가상 IP 주소로 자동 크기 조정, 영역 중복 애플리케이션 게이트웨이 만들기](tutorial-autoscale-ps.md)
- [Application Gateway](overview.md)에 대해 자세히 알아보세요.
- [Azure Firewall](../firewall/overview.md)에 대해 자세히 알아보세요.
