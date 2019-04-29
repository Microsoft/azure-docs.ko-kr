---
title: Azure Traffic Manager를 사용한 가중치 적용 라운드 로빈 트래픽 라우팅 방법 구성 | Microsoft Docs
description: 이 문서에서는 Traffic Manager에서 라운드 로빈 메서드를 사용하여 트래픽 부하를 분산하는 방법을 설명합니다
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
ms.openlocfilehash: ef39c09d4fc411937fdd6f4b5b5aec491efd0c5f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113290"
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Traffic Manager에서 가중치 적용 트래픽 라우팅 방법 구성

일반적인 트래픽 라우팅 방법 패턴은 클라우드 서비스와 웹 사이트를 포함하는 일련의 동일한 엔드포인트를 제공하고 각 엔드포인트에 똑같이 트래픽을 전송하는 것입니다. 다음 단계에서는 이 유형의 트래픽 라우팅 방법을 구성하는 방법을 간략하게 설명합니다.

> [!NOTE]
> Azure 웹앱은 Azure 지역 내 웹 사이트에 대해 이미 라운드 로빈 부하 분산 기능을 제공합니다(여러 데이터 센터로 구성될 수 있음). Traffic Manager를 사용하면 여러 데이터 센터의 웹 사이트에 트래픽을 분산할 수 있습니다.

## <a name="to-configure-the-weighted-traffic-routing-method"></a>가중치 적용 트래픽 라우팅 방법을 구성하려면 다음을 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다. 
2. 포털의 검색 창에서 **Traffic Manager 프로필**을 검색한 다음, 라우팅 방법을 구성하려는 프로필 이름을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 사용자 구성에 포함할 클라우드 서비스 및 웹 사이트가 모두 있는지 확인합니다.
4. **설정** 섹션에서 **구성**을 클릭하고 **구성** 블레이드에서 다음과 같이 완료합니다.
    1. **트래픽 라우팅 방법 설정**에서 트래픽 라우팅 방법이 **가중치 적용**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **가중치 적용**을 클릭합니다.
    2. **엔드포인트 모니터 설정**을 다음과 같이 이 프로필 내의 모든 엔드포인트와 동일하게 설정합니다.
        1. 적절한 **프로토콜**을 선택하고 **포트** 번호를 지정합니다. 
        2. **경로**에서 슬래시 */* 를 입력합니다. 엔드포인트를 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
        3. 페이지 위쪽에서 **저장**을 클릭합니다.
5. 다음과 같이 사용자 구성의 변경 내용을 테스트합니다.
    1.  포털의 검색 창에서 Traffic Manager 프로필 이름을 검색하고 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
    2.  **Traffic Manager** 프로필 블레이드에서 **개요**를 클릭합니다.
    3.  **Traffic Manager 프로필** 블레이드에 사용자의 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이는 라우팅 형식에서 결정된 대로 올바른 엔드포인트로 라우팅되도록 모든 클라이언트가 사용할 수 있습니다(예를 들어 웹 브라우저를 사용하여 이동). 이 경우 모든 요청이 라운드 로빈 방식으로 각 엔드포인트에 라우팅됩니다.
6. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

![Traffic Manager를 사용한 가중치 적용 트래픽 라우팅 방법 구성][1]

## <a name="next-steps"></a>다음 단계

- [우선 순위 트래픽 라우팅 방법](traffic-manager-configure-priority-routing-method.md)에 대해 알아보세요.
- [성능 트래픽 라우팅 방법](traffic-manager-configure-performance-routing-method.md)에 대해 알아보세요.
- [지리적 라우팅 방법](traffic-manager-configure-geographic-routing-method.md)에 대해 알아보세요.
- [Traffic Manager 설정 테스트](traffic-manager-testing-settings.md)에 대해 알아보세요.

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
