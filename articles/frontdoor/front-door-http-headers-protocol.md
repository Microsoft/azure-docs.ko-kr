---
title: Azure 프런트 도어 서비스의 HTTP 헤더에 대 한 지원 프로토콜 | Microsoft Docs
description: 이 문서에서는 프런트 도어 서비스에서 지 원하는 HTTP 헤더 프로토콜을 설명 합니다.
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
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736646"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Azure 프런트 도어 서비스의 HTTP 헤더에 대 한 프로토콜 지원
이 문서에서는 호출 경로 부분을 사용 하 여 첫 번째 관문 서비스가 지 원하는 프로토콜을 설명 (이미지 참조). 다음 섹션에서는 첫 번째 관문 서비스에서 지 원하는 HTTP 헤더에 대 한 자세한 정보를 제공 합니다.

![Azure Front Door Service HTTP 헤더 프로토콜][1]

>[!IMPORTANT]
>첫 번째 관문 서비스 여기 문서화 되지 않은 모든 HTTP 헤더를 인증 하지 않습니다.

## <a name="client-to-front-door-service"></a>첫 번째 관문 서비스에 대 한 클라이언트
첫 번째 관문 서비스 수정 하지 않고 들어오는 요청에서 대부분의 헤더를 허용 합니다. X가 있는 헤더를 포함 하 여 들어오는 요청을 보내는 경우 일부 예약 된 헤더 제거-FD-* 접두사입니다.

## <a name="front-door-service-to-backend"></a>백 엔드에 프런트 도어 서비스

첫 번째 관문 서비스 제한 사항으로 인해 제거 되지 않은 경우 들어오는 요청에서 헤더를 포함 합니다. 첫 번째 관문에는 다음 헤더를 추가합니다.

| 헤더  | 예제 및 설명 |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> 뒤에 클라이언트의 HTTP 버전을 추가 하는 첫 번째 관문 *Azure* Via 헤더 값으로. 클라이언트의 HTTP 버전 나타내며 해당 첫 번째 관문에서 중간 수신자 클라이언트와 백 엔드 요청에 대 한 되었습니다.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> 처리 중인 요청과 연결 된 클라이언트 IP 주소를 나타냅니다. 예를 들어, 프록시에서 들어오는 요청이를 원래 호출자의 IP 주소를 나타내는 X-전달 기능에 대 한 헤더를 추가할 수 있습니다. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> 현재 요청에서 시작 된 TCP 연결과 연결 된 소켓 IP 주소를 나타냅니다. 사용자가 임의로 덮어쓸 수 있으므로 클라이언트 IP 주소는 요청을 해당 소켓 IP 주소와 같고 수 없습니다.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> 첫 번째 관문에서 제공 하는 요청을 식별 하는 고유한 참조 문자열입니다. 액세스 로그 검색에 사용 및 문제 해결을 위해 중요 합니다.|
| X-Azure-RequestChain |  X-Azure-RequestChain: 홉 = 1 </br> 헤더 프런트 도어 사용 요청 루프를 검색 하 고 사용자에 종속성을 사용 하지 않아야 합니다. |
| X-Forwarded-For | X-전달 기능에 대 한 합니다. 127.0.0.1 </br> X-Forwarded-For (XFF) HTTP 헤더 필드는 종종 HTTP 프록시 또는 부하 분산 장치를 통해 웹 서버에 연결 하는 클라이언트의 원래 IP 주소를 식별 합니다. 기존 XFF 머리글의 경우 첫 번째 관문 클라이언트 소켓 IP를 추가 하거나 클라이언트 소켓 IP 사용 하 여 XFF 헤더를 추가 합니다. |
| X 전달 호스트 | X 전달 호스트: contoso.azurefd.net </br> X 전달 호스트 HTTP 헤더 필드는 호스트 HTTP 요청 헤더에서 클라이언트가 요청한 원래 호스트를 식별 하는 데 사용 하는 일반적인 메서드입니다. 즉, 요청을 처리 하는 백 엔드 서버에 대 한 첫 번째 관문에서 호스트 이름이 다를 수 있습니다. |
| X-Forwarded-Proto | X 전달 프로토콜: http </br> X 전달 Proto HTTP 헤더 필드는 대개 첫 번째 관문에서 구성에 따라 HTTPS를 사용 하 여 백 엔드를 사용 하 여 통신할 수 있으므로 HTTP 요청의 원래 프로토콜을 식별 합니다. 역방향 프록시 요청이 HTTP 경우에 마찬가지입니다. |

## <a name="front-door-service-to-client"></a>클라이언트에 프런트 도어 서비스

백 엔드에서 프런트 도어에 전송 되는 모든 헤더는 또한 클라이언트를 통해 전달 됩니다. 첫 번째 관문에서 클라이언트로 전송 되는 헤더는 다음과 같습니다.

| 헤더  | 예 |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Front Door에서 제공하는 요청을 참조하는 고유한 참조 문자열입니다. 이 액세스 로그 검색에 사용 되는 문제 해결에 중요 합니다.|

## <a name="next-steps"></a>다음 단계

- [프런트 도어 만들기](quickstart-create-front-door.md)
- [첫 번째 관문의 작동 원리](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png