---
title: Application gateway의 작동 원리
description: 이 문서에서는 application gateway의 작동 원리에 대 한 정보를 제공 합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 2f27105aed940f0411abaa534cb09adf0be34bfe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60830338"
---
# <a name="how-an-application-gateway-works"></a>Application gateway의 작동 원리

이 문서에서는 application gateway 들어오는 요청을 수락 하 고 라우팅하는 백 엔드에 하는 방법을 설명 합니다.

![Application gateway의 요청을 수락 하는 방법](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Application gateway의 요청을 수락 하는 방법

1. 클라이언트 응용 프로그램 게이트웨이에 요청을 보내면 전에 도메인 이름 시스템 (DNS) 서버를 사용 하 여 application gateway의 도메인 이름을 확인 합니다. Azure는 모든 application gateway azure.com 도메인에 있기 때문에 DNS 항목을 제어 합니다.

2. Azure DNS는 application gateway의 프런트 엔드 IP 주소는 클라이언트에 IP 주소를 반환 합니다.

3. Application gateway에는 하나 이상의 수신기에서 들어오는 트래픽을 허용합니다. 수신기는 연결 요청을 확인 하는 논리 엔터티입니다. 프런트 IP 주소, 프로토콜 및 application gateway에 대 한 클라이언트에서 연결에 대 한 포트 번호를 사용 하 여 구성 됩니다.

4. WAF 규칙에 대해 있는 경우 웹 응용 프로그램 방화벽 (WAF)를 사용 하 여 application gateway 요청 헤더 및 본문을 확인 합니다. 이 작업 유효한 요청 또는 보안 위협이 요청 인지 여부를 확인 합니다. 요청이 유효 하면 백 엔드에 라우팅됩니다. 요청이 유효 하지 않으면 보안 위협으로 차단 됩니다.

Azure Application Gateway는 인터넷 연결 응용 프로그램 부하 분산 장치 또는 내부 응용 프로그램 부하 분산 장치로 사용할 수 있습니다. 인터넷 응용 프로그램 게이트웨이 공용 IP 주소를 사용합니다. 인터넷 응용 프로그램 게이트웨이의 DNS 이름을 해당 공용 IP 주소에 공개적으로 확인 가능한 경우 결과적으로, 인터넷 응용 프로그램 게이트웨이 인터넷 클라이언트 요청을 라우팅할 수 있습니다.

내부 응용 프로그램 게이트웨이 개인 IP 주소를 사용합니다. 내부 응용 프로그램 게이트웨이의 DNS 이름을 해당 개인 IP 주소에 공개적으로 확인 가능한 경우 따라서 내부 부하 분산 장치는 application gateway에 대 한 가상 네트워크에 대 한 액세스를 사용 하 여 클라이언트의 요청을 라우팅할 수 있습니다.

인터넷 연결 및 내부 응용 프로그램 게이트웨이 개인 IP 주소를 사용 하 여 백 엔드 서버로 요청을 라우팅합니다. 백 엔드 서버의 공용 IP 주소 요청 내부에서 또는 인터넷 연결 응용 프로그램 게이트웨이를 받이 필요가 없습니다.

## <a name="how-an-application-gateway-routes-a-request"></a>Application gateway가 요청을 라우팅하는 방법

요청이 유효 또는 WAF를 사용 하지 않을 경우 application gateway는 수신기와 연결 된 요청 라우팅 규칙을 평가 합니다. 이 작업에 요청을 라우팅하는 백 엔드 풀을 확인 합니다.

규칙 포털에 나열 된 순서 대로 처리 됩니다. 요청 라우팅 규칙에 따라 응용 프로그램 게이트웨이에 여부를 결정 경로 요청 URL 경로 또는 다른 포트로 요청 리디렉션에 따라 또는 외부의 다른 백 엔드 풀을 특정 백 엔드 풀에 수신기에서 모든 요청을 라우팅하기 위해 사이트입니다.

Application gateway가 백 엔드 풀을 선택 하면 요청 (y.y.y.y) 풀의 정상 백 엔드 서버 중 하나에 보냅니다. 서버의 상태는 상태 프로브에 따라 결정 됩니다. 백 엔드 풀에 여러 서버가 있는 경우 application gateway는 정상 서버 간에 요청을 라우팅하는 라운드 로빈 알고리즘을 사용 합니다. 이 부하 분산 서버에 요청 합니다.

Application gateway 백 엔드 서버를 결정 한 후 HTTP 설정에 따라 백 엔드 서버를 사용 하 여 새 TCP 세션을 엽니다. HTTP 설정에는 프로토콜, 포트 및 백 엔드 서버를 사용 하 여 새 세션을 설정 하는 데 필요한 다른 라우팅 관련 설정을 지정 합니다.

포트 및 HTTP 설정에 사용 되는 프로토콜 간의 응용 프로그램 게이트웨이와 백 엔드 서버 트래픽은 (따라서 수행 되는 종단 간 SSL) 암호화는 암호화 되지 하는지 여부를 결정 합니다.

응용 프로그램 게이트웨이 백 엔드 서버에 원래 요청을 보내면, 호스트 이름, 경로 및 프로토콜을 재정의 하려면 관련 HTTP 설정에 대 한 사용자 지정 구성을 준수 합니다. 이 작업에는 쿠키 기반 세션 선호도, 백 엔드의 경우와 등 연결 드레이닝, 호스트 이름 선택 유지 관리합니다.

내부 응용 프로그램 게이트웨이 개인 IP 주소를 사용합니다. 내부 응용 프로그램 게이트웨이의 DNS 이름을 해당 개인 IP 주소로 확인할 수 있습니다. 결과적으로, 내부 부하 분산 장치는 application gateway에 대 한 가상 네트워크에 대 한 액세스를 사용 하 여 클라이언트의 요청을 라우팅할 수 있습니다.

 >[!NOTE]
 >인터넷 연결 및 내부 응용 프로그램 게이트웨이 모두 개인 IP 주소를 사용 하 여 백 엔드 서버로 요청을 라우팅합니다. 이 작업에는 백 엔드 풀 리소스에 개인 IP 주소, VM NIC 구성 또는 내부적으로 확인할 수 있는 주소를 포함 하는 경우 발생 합니다. 하는 경우 백 엔드 풀:
> - **공용 끝점은**, application gateway의 프런트 엔드 공용 IP를 사용 하 여 서버에 도달 하도록 합니다. 프런트 엔드 공용 IP 주소가 없으면 하나 아웃 바운드 외부 연결에 대 한 할당 됩니다.
> - **내부적으로 확인 가능한 FQDN 또는 개인 IP 주소를 포함**, application gateway 인스턴스 개인 IP 주소를 사용 하 여 백 엔드 서버로 요청을 라우팅합니다.
> - **외부 끝점 또는 외부에서 확인 가능한 FQDN 포함**, application gateway 프런트 엔드 공용 IP 주소를 사용 하 여 백 엔드 서버로 요청을 라우팅합니다. DNS 확인 또는 기반으로 개인 DNS 영역 또는 사용자 지정 DNS 서버를 구성 하는 경우 기본 Azure 제공 DNS 사용 합니다. 프런트 엔드 공용 IP 주소가 없으면 하나 아웃 바운드 외부 연결에 대 한 할당 됩니다.

### <a name="modifications-to-the-request"></a>요청 수정

응용 프로그램 게이트웨이 백 엔드에 요청 전달 하기 전에 모든 요청에 4 개의 추가 헤더를 삽입 합니다. 이러한 헤더는 x-전달 기능에 대 한, x 전달 proto, x-전달-포트 및 x 원래 호스트는 합니다. X-전달 기능에 대 한 헤더 형식은 쉼표로 구분 된 목록을 ip: port입니다.

X 전달 프로토콜에 대 한 유효한 값은 HTTP 또는 HTTPS입니다. X 전달 된 포트 요청 응용 프로그램 게이트웨이에 도달 하는 위치를 지정 합니다. X 원래 호스트 헤더는 요청이 도착 한 원래 호스트 헤더를 포함 합니다. 이 헤더는 수신 호스트 헤더를 백 엔드에 트래픽이 라우팅되는 하기 전에 수정 되는 위치는 Azure 웹 사이트 통합을 유용 합니다. 세션 선호도 옵션으로 설정 되어, 게이트웨이 관리 선호도 쿠키를 추가 합니다.

사용 하 여 헤더를 수정 하려면 응용 프로그램 게이트웨이 구성할 수 있습니다 [재작성 HTTP 헤더](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) 또는 경로 재정의 설정을 사용 하 여 URI 경로 수정 합니다. 그러나 이렇게 하려면 구성 하지 않으면 모든 들어오는 요청은 백 엔드에 프록시입니다.

## <a name="next-steps"></a>다음 단계

[응용 프로그램 게이트웨이 구성 요소에 알아봅니다](application-gateway-components.md)
