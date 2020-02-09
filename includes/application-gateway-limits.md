---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 6ab6c4c2051ccd2fbb22c383b9ca0af53ceb13d3
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77054928"
---
| 리소스 | 기본/최대 제한 | 참고 |
| --- | --- | --- |
| Azure Application Gateway |1000 구독 당 | |
| 프런트 엔드 IP 구성 |2 |공용 1 및 프라이빗 1 |
| 프런트 엔드 포트 |100<sup>1</sup> | |
| 백 엔드 주소 풀 |100<sup>1</sup> | |
| 풀당 백 엔드 서버 |1,200 | |
| HTTP 수신기 |100<sup>1</sup> | |
| HTTP 부하 분산 규칙 |100<sup>1</sup> | |
| 백 엔드 HTTP 설정 |100<sup>1</sup> | |
| 게이트웨이당 인스턴스 |V1 SKU-32<br>V2 SKU-125 | |
| SSL 인증서 |100<sup>1</sup> |HTTP 수신기 당 1 개 |
| 최대 SSL 인증서 크기 |V1 SKU-10 KB<br>V2 SKU-16KB| |
| 인증 인증서 |100 | |
| 신뢰할 수 있는 루트 인증서 |100 | |
| 요청 시간 제한 최소값 |1초 | |
| 요청 시간 제한 최대값 |24시간 | |
| 사이트 수 |100<sup>1</sup> |HTTP 수신기 당 1 개 |
| 수신기당 URL 맵 |1 | |
| URL 맵당 최대 경로 기반 규칙|100||
| 구성 리디렉션 |100<sup>1</sup>| |
| 동시 WebSocket 연결 |중간 게이트웨이 20k<br> 대량 게이트웨이 50k| |
| 최대 URL 길이|32KB| |
| HTTP/2에 대한 최대 헤더 크기 |4KB| |
| 최대 파일 업로드 크기, 표준 |2GB | |
| 최대 파일 업로드 크기 WAF |v1 Medium WAF 게이트웨이, 100 m b<br>v1 Large WAF 게이트웨이, 500 MB<br>v2 WAF, 750 m b| |
| 파일이 없는 WAF 본문 크기 제한|128KB||
| 최대 WAF 사용자 지정 규칙|100||
| 최대 WAF 제외|100||

<sup>1</sup> waf 사용 sku의 경우 최적의 성능을 위해 리소스 수를 40로 제한 하는 것이 좋습니다.
