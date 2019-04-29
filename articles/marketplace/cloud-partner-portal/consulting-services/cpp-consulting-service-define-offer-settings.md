---
title: Azure 및 Dynamics 365 컨설팅 서비스 제품 - 제품 설정 정의 | Microsoft Docs
description: Cloud 파트너 포털에서 Azure 또는 Dynamics 365 컨설팅 서비스 제품의 제품 설정 정의에 대한 가이드.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749656"
---
# <a name="offer-settings-tab"></a>제품 설정 탭

**새 제품** 화면에서 처음으로 수행하는 단계는 제품 ID 만들기입니다. 제품 ID는 다음의 세 부분으로 구성됩니다. **제품 ID**, **게시자 ID** 및 **이름**. 다음 섹션에서 이러한 각 부분에 대해 설명합니다.

![새 컨설팅 서비스 제품 만들기 - 제품 설정 탭](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>제품 ID

이 식별자는 제품을 처음 제출할 때 만드는 고유한 이름입니다. 제품 ID에는 소문자 영숫자 문자, 대시 또는 밑줄만 사용해야 합니다. **제품 ID**는 URL에 표시되며 검색 엔진 결과에 영향을 줍니다. *yourcompanyname_exampleservice* 등을 예로 들 수 있습니다.

예제에 나와 있는 것처럼 **제품 ID**가 게시자 ID에 추가되어 고유한 식별자가 생성됩니다. 이 고유 식별자는 검색 엔진에서 인덱싱되며 예약 가능한 고정 링크로 표시됩니다.

>[!Note]
>제품이 라이브로 표시된 후에는 식별자를 업데이트할 수 없습니다.

### <a name="publisher-id"></a>게시자 ID

이 식별자는 계정과 관련이 있습니다. 조직 계정으로 로그인하고 나면 **게시자 ID**가 드롭다운 메뉴에 표시됩니다.

### <a name="name"></a>이름

이 문자열은 AppSource 또는 Azure Marketplace에서 제품 이름으로 표시됩니다. **이름** 상자에는 50자까지만 입력할 수 있습니다. 검토자는 제목을 편집하여 제품 이름에 기간 및 제품 유형을 추가해야 할 수 있습니다.

다음 예제는 제품 이름이 조합되는 방식을 보여 줍니다. 

![새 컨설팅 서비스 제품 만들기](media/cppsampleconsultingoffer.png)

제품 이름은 다음의 네 부분으로 구성됩니다.

-   **기간:** 편집기의 **상점 정보** 탭에서 정의합니다. 기간은 시간, 일 또는 주 단위로 표시할 수 있습니다.
-   **서비스 유형:** 편집기의 **상점 정보** 탭에서 정의합니다. 서비스의 유형은 `Assessment`, `Briefing`, `Implementation`, `Proof of concept` 및 `Workshop`입니다.
-   **전치사:** 검토자가 삽입합니다.
-   **이름:** **제품 설정** 페이지에서 정의합니다.

>[!Note]
>**이름** 상자에는 50자까지만 입력할 수 있습니다. 검토자는 제목을 편집하여 제품 이름에 기간 및 제품 유형을 추가해야 할 수 있습니다.

다음 목록에 적절하게 지정된 몇 가지 제품 이름이 나와 있습니다.

-   전문 서비스 필수 사항: 1시간 브리핑
-   클라우드 마이그레이션 플랫폼: 1시간 브리핑
-   PowerApps 및 Microsoft Flow: 1일 워크샵
-   Azure Machine Learning Services: 3주 PoC
-   Brick and Click Retail 솔루션: 1시간 브리핑
-   사용자 데이터 가져오기: 1주 워크샵
-   클라우드 분석: 3일 워크샵
-   Power BI 학습: 3일 워크샵
-   판매 관리 솔루션: 1주 구현
-   CRM 빠른 시작: 1일 워크샵
-   Dynamics 365 for Sales: 2일 평가

**제품 설정** 탭에 필요한 정보를 입력한 후에 제출 내용을 저장합니다. 이제 제품 이름이 편집기 위에 표시되며 **모든 제품**에서 이름을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 [상점 정보를 입력하고 게시할 위치(Azure Marketplace 또는 AppSource)를 결정](./cpp-consulting-service-storefront-details.md)할 수 있습니다.
