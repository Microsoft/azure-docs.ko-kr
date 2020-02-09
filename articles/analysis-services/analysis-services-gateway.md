---
title: Azure Analysis Services에 대한 온-프레미스 데이터 게이트웨이 Microsoft Docs
description: Azure의 Analysis Services 서버가 온-프레미스 데이터 원본에 연결되는 경우 온-프레미스 게이트웨이가 필요합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 648646b6f973762245c344cd2629a874a219b170
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310155"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이를 사용 하 여 온-프레미스 데이터 원본에 연결

온-프레미스 데이터 게이트웨이를 사용하면 온-프레미스 데이터 원본과 클라우드의 Azure Analysis Services 서버 사이에서 데이터를 안전하게 전송할 수 있습니다. 동일한 지역에서 여러 Azure Analysis Services 서버를 사용 하는 것 외에도 최신 버전의 게이트웨이는 Azure Logic Apps, Power BI, Power Apps 및 파워 자동화를 사용 하 여 작동 합니다. 설치한 게이트웨이는 이러한 모든 서비스에서 동일 하지만 Azure Analysis Services 및 Logic Apps에 몇 가지 추가 단계가 있습니다.

여기에서 제공 하는 정보는 Azure Analysis Services 온-프레미스 데이터 게이트웨이와 작동 하는 방식에 따라 다릅니다. 일반적으로 게이트웨이에 대한 자세한 내용 및 다른 서비스와 함께 작동 하는 방법에 대한 자세한 내용은 [온-프레미스 데이터 게이트웨이 란?](/data-integration/gateway/service-gateway-onprem)을 참조 하세요.

Azure Analysis Services의 경우 처음으로 게이트웨이를 사용 하 여 설치 하는 과정은 네 부분으로 구성 된 프로세스입니다.

- **설치 프로그램 다운로드 및 실행** - 이 단계는 조직의 컴퓨터에 게이트웨이 서비스를 설치합니다. 또한 [테넌트](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) Azure AD의 계정을 사용하여 Azure에 로그인합니다. Azure B2B(게스트) 계정은 지원되지 않습니다.

- **게이트웨이 등록** - 이 단계에서는 게이트웨이에 대한 이름 및 복구 키를 지정하고 게이트웨이 클라우드 서비스로 게이트웨이를 등록한 지역을 선택합니다. 모든 지역에 게이트웨이 리소스를 등록할 수 있지만 Analysis Services 서버와 동일한 지역에 있는 것이 좋습니다. 

- **Azure에서 게이트웨이 리소스 만들기** -이 단계에서는 azure에서 게이트웨이 리소스를 만듭니다.

- **게이트웨이 리소스에 서버 연결** -게이트웨이 리소스가 있으면 서버 연결을 시작할 수 있습니다. 동일한 지역에 있는 경우 여러 서버 및 기타 리소스를 연결할 수 있습니다.



## <a name="how-it-works"> </a>작동 방식
조직의 컴퓨터에 설치하는 게이트웨이는 Windows 서비스인 **온-프레미스 데이터 게이트웨이**로 실행됩니다. 이 로컬 서비스는 Azure Service Bus를 통해 게이트웨이 클라우드 서비스로 등록됩니다. 그런 다음 Azure 구독에 대한 온-프레미스 데이터 게이트웨이 리소스를 만듭니다. 그러면 Azure Analysis Services 서버가 Azure 게이트웨이 리소스에 연결 됩니다. 서버의 모델을 쿼리 또는 처리를 위해 온-프레미스 데이터 원본에 연결해야 하는 경우 쿼리 및 데이터 흐름은 게이트웨이 리소스, Azure Service Bus, 로컬 온-프레미스 데이터 게이트웨이 서비스 및 데이터 원본을 통과합니다. 

![작동 원리](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

쿼리 및 데이터 흐름:

1. 쿼리는 온-프레미스 데이터 원본의 암호화된 자격 증명을 사용하여 클라우드 서비스에서 생성합니다. 그런 다음 처리할 게이트웨이의 큐로 전송됩니다.
2. 게이트웨이 클라우드 서비스에서 쿼리를 분석하고 [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/)에 대한 요청을 보냅니다.
3. 온-프레미스 데이터 게이트웨이가 보류 중인 요청을 위해 Azure Service Bus를 폴링합니다.
4. 게이트웨이가 쿼리를 가져오고, 자격 증명의 암호를 해독하고, 해당 자격 증명으로 데이터 원본에 연결합니다.
5. 게이트웨이가 실행을 위해 데이터 원본에 쿼리를 보냅니다.
6. 결과가 데이터 원본에서 게이트웨이로 다시 전송된 후 클라우드 서비스 및 서버로 전송됩니다.

## <a name="installing"></a>설치

Azure Analysis Services 환경을 설치 하는 경우 [Azure Analysis Services에 대한 온-프레미스 데이터 게이트웨이 설치 및 구성](analysis-services-gateway-install.md)에 설명 된 단계를 수행 하는 것이 중요 합니다. 이 문서는 Azure Analysis Services에만 적용 됩니다. Azure에서 온-프레미스 데이터 게이트웨이 리소스를 설정 하 고 리소스에 Azure Analysis Services 서버를 연결 하는 데 필요한 추가 단계를 포함 합니다.

## <a name="ports-and-communication-settings"></a>포트 및 통신 설정

게이트웨이는 Azure Service Bus에 대한 아웃바운드 연결을 만듭니다. 아웃바운드 포트 TCP 443(기본값), 5671, 5672, 9350-9354에서 통신합니다.  게이트웨이에는 인바운드 포트가 필요하지 않습니다.

방화벽에 데이터 영역에 대한 IP 주소를 포함 해야 할 수 있습니다. [Microsoft Azure 데이터 센터 IP 목록](https://www.microsoft.com/download/details.aspx?id=56519)을 다운로드할 수 있습니다. 이 목록은 매주 업데이트됩니다. Azure 데이터 센터 IP 목록에 나열된 IP 주소는 CIDR 표기법으로 작성됩니다. 자세한 내용은 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요.

다음은 게이트웨이에서 사용 되는 정규화 된 도메인 이름입니다.

| 도메인 이름 | 아웃바운드 포트 | Description |
| --- | --- | --- |
| *.powerbi.com |80 |설치 프로그램을 다운로드하는 데 사용되는 HTTP입니다. |
| *.powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *. login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| \*.servicebus.windows.net |5671-5672 |AMQP(고급 메시지 큐 프로토콜) |
| \*.servicebus.windows.net |443, 9350-9354 |TCP의 Service Bus Relay에 대한 수신기(Access Control 토큰 획득에 443 필요) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Power BI 서비스에서 게이트웨이에 연결할 수 없는 경우 인터넷 연결을 테스트하는 데 사용합니다. |
| *.microsoftonline-p.com |443 |구성에 따라 인증에 사용합니다. |
| dc.services.visualstudio.com  |443 |AppInsights에서 원격 분석을 수집 하는 데 사용 됩니다. |

### <a name="force-https"></a>Azure Service Bus와의 HTTPS 통신 강제 적용

직접 TCP 대신 HTTPS를 사용하여 Azure Service Bus와 통신하도록 게이트웨이 강제할 수 있지만 성능이 크게 저하될 수 있습니다. 값을 `AutoDetect`에서 `Https`로 변경하여 *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* 파일을 수정할 수 있습니다. 이 파일은 기본적으로 *C:\Program Files\On-premises data gateway*에 위치합니다.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="next-steps"></a>다음 단계 

다음 문서는 게이트웨이에서 지 원하는 모든 서비스에 적용 되는 온-프레미스 데이터 게이트웨이 일반 콘텐츠에 포함 되어 있습니다.

* [온-프레미스 데이터 게이트웨이 FAQ](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)   
* [온-프레미스 데이터 게이트웨이 앱 사용](https://docs.microsoft.com/data-integration/gateway/service-gateway-app)   
* [테넌트 수준 관리](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin)
* [프록시 설정 구성](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)   
* [통신 설정 조정](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)   
* [로그 파일 구성](https://docs.microsoft.com/data-integration/gateway/service-gateway-log-files)   
* [문제 해결](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [게이트웨이 성능 모니터링 및 최적화](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)
