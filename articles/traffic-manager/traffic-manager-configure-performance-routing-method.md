---
title: Azure Traffic Manager를 사용한 성능 트래픽 라우팅 방법 구성 | Microsoft Docs
description: 이 문서에서는 가장 낮은 대기 시간으로 트래픽을 엔드포인트로 라우팅하도록 Traffic Manager를 구성하는 방법에 설명합니다.
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 4c948668e355b87026240588c6fac11d86e355b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883970"
---
# <a name="configure-the-performance-traffic-routing-method"></a>성능 트래픽 라우팅 방법 구성

성능 트래픽 라우팅 방법을 통해 클라이언트 네트워크에서 대기 시간이 가장 짧은 엔드포인트로 트래픽을 전송할 수 있습니다. 일반적으로 대기 시간이 가장 짧은 데이터 센터는 지리적 거리가 가장 가까운 데이터 센터입니다. 이 트래픽 라우팅 방법은 네트워크 구성이나 로드의 실시간 변경 내용을 고려할 수 없습니다.

##  <a name="to-configure-performance-routing-method"></a>성능 라우팅 방법을 구성하려면 다음을 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다. 
2. 포털의 검색 창에서 **Traffic Manager 프로필**을 검색한 다음, 라우팅 방법을 구성하려는 프로필 이름을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 사용자 구성에 포함할 클라우드 서비스 및 웹 사이트가 모두 있는지 확인합니다.
4. **설정** 섹션에서 **구성**을 클릭하고 **구성** 블레이드에서 다음과 같이 완료합니다.
    1. **트래픽 라우팅 방법 설정**의 경우 **라우팅 방법**에서 **성능**을 선택합니다.
    2. **엔드포인트 모니터 설정**을 다음과 같이 이 프로필 내의 모든 엔드포인트와 동일하게 설정합니다.
        1. 적절한 **프로토콜**을 선택하고 **포트** 번호를 지정합니다. 
        2. **경로**에서 슬래시 */* 를 입력합니다. 엔드포인트를 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
        3. 페이지 위쪽에서 **저장**을 클릭합니다.
5.  다음과 같이 사용자 구성의 변경 내용을 테스트합니다.
    1.  포털의 검색 창에서 Traffic Manager 프로필 이름을 검색하고 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
    2.  **Traffic Manager** 프로필 블레이드에서 **개요**를 클릭합니다.
    3.  **Traffic Manager 프로필** 블레이드에 사용자의 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이는 라우팅 형식에서 결정된 대로 올바른 엔드포인트로 라우팅되도록 모든 클라이언트가 사용할 수 있습니다(예를 들어 웹 브라우저를 사용하여 이동). 이 경우 모든 요청은 클라이언트의 네트워크에서 가장 낮은 대기 시간으로 엔드포인트로 라우팅됩니다.
6. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

![Traffic Manager를 사용한 성능 트래픽 라우팅 방법 구성][1]

## <a name="next-steps"></a>다음 단계

- [가중치 적용 트래픽 라우팅 방법](traffic-manager-configure-weighted-routing-method.md)에 대해 알아보세요.
- [우선 순위 라우팅 방법](traffic-manager-configure-priority-routing-method.md)에 대해 알아보세요.
- [지리적 라우팅 방법](traffic-manager-configure-geographic-routing-method.md)에 대해 알아보세요.
- [Traffic Manager 설정 테스트](traffic-manager-testing-settings.md)에 대해 알아보세요.

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png