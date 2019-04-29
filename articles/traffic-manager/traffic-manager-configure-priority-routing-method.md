---
title: Azure Traffic Manager를 사용한 우선 순위 트래픽 라우팅 방법 구성 | Microsoft Docs
description: 이 문서에서는 Traffic Manager에서 우선 순위 트래픽 라우팅 방법을 구성하는 방법을 설명합니다.
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
ms.openlocfilehash: 66c5bd9390d6fe0f26af66e18aed22c07a7da3e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60884004"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Traffic Manager에서 우선 순위 트래픽 라우팅 방법 구성

웹 사이트 모드에 관계없이 Azure Websites는 데이터 센터(지역이라고도 함) 내의 웹 사이트에 대해 이미 장애 조치(Failover) 기능을 제공합니다. Traffic Manager는 다른 데이터 센터의 웹 사이트에 대해 장애 조치(Failover)를 제공합니다.

일반적인 서비스 장애 조치(Failover) 패턴은 기본 서비스에 트래픽을 전송하고 장애 조치(Failover)에 대한 일련의 동일한 백업 서비스를 제공하는 것입니다. 다음 단계에서는 Azure Cloud Services 및 Websites를 사용하여 이 우선 순위가 지정된 장애 조치(Failover)를 구성하는 방법을 설명합니다.

## <a name="to-configure-the-priority-traffic-routing-method"></a>우선 순위 트래픽 라우팅 방법을 구성하려면 다음을 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다. 
2. 포털의 검색 창에서 **Traffic Manager 프로필**을 검색한 다음, 라우팅 방법을 구성하려는 프로필 이름을 클릭합니다.
3. **Traffic Manager 프로필** 블레이드에서 사용자 구성에 포함할 클라우드 서비스 및 웹 사이트가 모두 있는지 확인합니다.
4. **설정** 섹션에서 **구성**을 클릭하고 **구성** 블레이드에서 다음과 같이 완료합니다.
    1. **트래픽 라우팅 방법 설정**에서 트래픽 라우팅 방법이 **우선 순위**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **우선 순위**를 클릭합니다.
    2. **엔드포인트 모니터 설정**을 다음과 같이 이 프로필 내의 모든 엔드포인트와 동일하게 설정합니다.
        1. 적절한 **프로토콜**을 선택하고 **포트** 번호를 지정합니다. 
        2. **경로**에서 슬래시 */* 를 입력합니다. 엔드포인트를 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
        3. 페이지 위쪽에서 **저장**을 클릭합니다.
5. **설정** 섹션에서 **엔드포인트**를 클릭합니다.
6. **엔드포인트** 블레이드에서 사용자의 엔드포인트에 대한 우선 순위 순서를 검토합니다. **우선 순위** 트래픽 라우팅 방법을 선택하는 경우 선택한 엔드포인트의 순서가 중요합니다. 엔드포인트의 우선 순위 순서를 확인합니다.  기본 엔드포인트가 맨 위에 있습니다. 표시되는 순서를 다시 확인합니다. 모든 요청이 첫 번째 엔드포인트로 라우팅되고, Traffic Manager에서 이를 비정상으로 감지하면 트래픽은 자동으로 다음 엔드포인트로 장애 조치됩니다. 
7. 엔드포인트 우선 순위 순서를 변경하려면 엔드포인트를 클릭하고 표시되는 **엔드포인트** 블레이드에서 **편집**을 클릭하고 **우선 순위** 값을 필요에 따라 변경합니다. 
8. **저장**을 클릭하여 엔드포인트 설정의 변경 사항을 저장합니다.
9. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장**을 클릭합니다.
10. 다음과 같이 사용자 구성의 변경 내용을 테스트합니다.
    1.  포털의 검색 창에서 Traffic Manager 프로필 이름을 검색하고 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
    2.  **Traffic Manager** 프로필 블레이드에서 **개요**를 클릭합니다.
    3.  **Traffic Manager 프로필** 블레이드에 사용자의 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 이는 라우팅 형식에서 결정된 대로 올바른 엔드포인트로 라우팅되도록 모든 클라이언트가 사용할 수 있습니다(예를 들어 웹 브라우저를 사용하여 이동). 이 경우 모든 요청이 첫 번째 엔드포인트로 라우팅되고 Traffic Manager에서 이를 비정상으로 감지하면 트래픽은 자동으로 다음 엔드포인트로 장애 조치됩니다.
11. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

![Traffic Manager를 사용한 우선 순위 트래픽 라우팅 방법 구성][1]

## <a name="next-steps"></a>다음 단계


- [가중치 적용 트래픽 라우팅 방법](traffic-manager-configure-weighted-routing-method.md)에 대해 알아보세요.
- [성능 라우팅 방법](traffic-manager-configure-performance-routing-method.md)에 대해 알아보세요.
- [지리적 라우팅 방법](traffic-manager-configure-geographic-routing-method.md)에 대해 알아보세요.
- [Traffic Manager 설정 테스트](traffic-manager-testing-settings.md)에 대해 알아보세요.

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png