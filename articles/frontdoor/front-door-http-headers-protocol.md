---
title: Azure Front 도어 서비스에서 HTTP 헤더에 대한 프로토콜 지원 | Microsoft Docs
description: 이 문서에서는 Front 도어 서비스에서 지 원하는 HTTP 헤더 프로토콜에 대해 설명 합니다.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 7c77527b7300c1149e96c94a4dbe122da226ac6d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720438"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Azure Front 도어 서비스에서 HTTP 헤더에 대한 프로토콜 지원
이 문서에서는 프런트 도어 서비스에서 호출 경로의 일부를 지 원하는 프로토콜에 대해 간략하게 설명 합니다 (이미지 참조). 다음 섹션에서는 Front 도어 서비스에서 지 원하는 HTTP 헤더에 대한 자세한 정보를 제공 합니다.

![Azure Front Door Service HTTP 헤더 프로토콜][1]

>[!IMPORTANT]
>Front 도어 서비스는 여기에 설명 되지 않은 HTTP 헤더를 인증 하지 않습니다.

## <a name="client-to-front-door-service"></a>클라이언트와 프런트 도어 서비스
프런트 도어 서비스는 들어오는 요청의 헤더를 수정 하지 않고 대부분 허용 합니다. 헤더를 포함 하 여 송신 하는 경우 들어오는 요청에서 X-FD-* 접두사가 포함 된 일부 예약 된 헤더가 제거 됩니다.

## <a name="front-door-service-to-backend"></a>프런트 도어 서비스에서 백 엔드

프런트 도어 서비스에는 제한 사항으로 인해 제거 되지 않는 한 들어오는 요청의 헤더가 포함 됩니다. 또한 앞 도어는 다음 헤더를 추가 합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> 프런트 도어는 클라이언트의 HTTP 버전을 Via 헤더에 대한 *값으로 추가* 합니다. 이는 클라이언트의 HTTP 버전을 나타내고 앞 도어는 클라이언트와 백 엔드 간의 요청을 위한 중간 수신자 였습니다.  |
| X-Azure-ClientIP | X-y: 127.0.0.1 </br> 처리 중인 요청과 연결 된 클라이언트 IP 주소를 나타냅니다. 예를 들어 프록시에서 들어오는 요청은 원본 호출자의 IP 주소를 나타내기 위해 X로 전달 된 헤더를 추가할 수 있습니다. |
| X-Azure-SocketIP |  SocketIP: 127.0.0.1 </br> 현재 요청이 시작 된 TCP 연결과 관련 된 소켓 IP 주소를 나타냅니다. 요청의 클라이언트 IP 주소는 사용자가 임의로 덮어쓸 수 있으므로 해당 소켓 IP 주소와 다를 수 있습니다.|
| X-Azure-Ref |  X-y-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Front 도어가 제공 하는 요청을 식별 하는 고유한 참조 문자열입니다. 액세스 로그를 검색 하는 데 사용 되며 문제 해결을 위해 중요 합니다.|
| X-Azure-RequestChain |  X-Azure-RequestChain: 홉 = 1 </br> 프런트 도어가 요청 루프를 검색 하는 데 사용 하는 헤더 이며, 사용자가이에 대한 종속성을 사용 하지 않아야 합니다. |
| X-Forwarded-For | X 전달-의 경우: 127.0.0.1 </br> X로 전달 된 (XFF) HTTP 헤더 필드는 HTTP 프록시 또는 부하 분산 장치를 통해 웹 서버에 연결 하는 클라이언트의 원래 IP 주소를 식별 하는 경우가 많습니다. 기존 XFF 헤더가 있는 경우 Front 도어가 클라이언트 소켓 IP를 추가 하거나 클라이언트 소켓 IP를 사용 하 여 XFF 헤더를 추가 합니다. |
| X 전달-호스트 | X 전달-호스트: contoso.azurefd.net </br> X 전달 된 호스트 HTTP 헤더 필드는 호스트 HTTP 요청 헤더에서 클라이언트에 의해 요청 된 원래 호스트를 식별 하는 데 사용 되는 일반적인 방법입니다. 이는 프런트 도어의 호스트 이름이 요청을 처리 하는 백 엔드 서버에 대해 다를 수 있기 때문입니다. |
| X-Forwarded-Proto | X 전달-프로토콜: http </br> 구성에 따라 프런트 도어가 HTTPS를 사용 하 여 백 엔드와 통신할 수 있기 때문에 X-전달 된 프로토콜 HTTP 헤더 필드는 HTTP 요청의 원래 프로토콜을 식별 하는 데 종종 사용 됩니다. 역방향 프록시에 대한 요청이 HTTP 인 경우에도 마찬가지입니다. |
| X FD-HealthProbe | HealthProbe HTTP 헤더 필드는 전면 도어에서 상태 프로브를 식별 하는 데 사용 됩니다. 이 헤더가 1로 설정 되 면 요청이 상태 프로브입니다. X 전달-호스트 헤더 필드를 사용 하 여 특정 전방 도어에서 엄격한 액세스를 원할 때를 사용할 수 있습니다. |

## <a name="front-door-service-to-client"></a>프런트 도어 서비스-클라이언트

백 엔드에서 프런트 도어로 전송 되는 모든 헤더도 클라이언트에 전달 됩니다. 다음은 프런트 도어에서 클라이언트로 전송 되는 헤더입니다.

| 헤더  | 예제 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-y-Ref: 0zxV + XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 액세스 로그를 검색 하는 데 사용 되므로 문제를 해결 하는 데 중요 합니다.|

## <a name="next-steps"></a>다음 단계

- [전면 도어 만들기](quickstart-create-front-door.md)
- [프런트 도어 작동 방법](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png
