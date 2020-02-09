---
title: 지역 가용성 및 데이터 상주
titleSuffix: Azure AD B2C
description: 지역 가용성, 데이터 상주 및 Azure Active Directory B2C 미리 보기 테넌트에 대한 정보입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 532136be69356b634959a762f4eeb1c1c4af5a82
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849176"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: 지역 가용성 및 데이터 상주

지역 가용성 및 데이터 상주는 나머지 Azure에서 Azure AD B2C에 다르게 적용되는 두 가지 매우 다른 개념입니다. 이 문서에서는 이러한 두 개념 간의 차이점을 설명 하 고 Azure와 Azure AD B2C에 적용 되는 방식을 비교 합니다.

Azure AD B2C는 **미국, 유럽 또는 아시아 태평양**의 **데이터 상주** 옵션을 사용 하 여 **전 세계에서 일반적으로 사용할 수 있습니다** .

[지역 가용성](#region-availability)은 서비스를 사용할 수 있는 위치를 나타냅니다.

[데이터 상주](#data-residency)는 사용자 데이터가 저장된 위치를 나타냅니다.

## <a name="region-availability"></a>리전 가용성

Azure AD B2C는 Azure 퍼블릭 클라우드를 통해 전 세계에서 사용할 수 있습니다.

이는 일반적으로 *데이터 상주*을 사용 하는 몇 가지 다른 Azure 서비스 *와 모델* 의 차이가 있습니다. Azure의 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/) 페이지 및 [Azure Active Directory B2C 가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/)에서 이와 같은 예제를 확인할 수 있습니다.

## <a name="data-residency"></a>데이터 상주

Azure AD B2C는 미국, 유럽 또는 아시아 태평양 지역에 사용자 데이터를 저장 합니다.

데이터 상주는 [Azure AD B2C 테넌트를 만들](tutorial-create-tenant.md)때 선택 하는 국가/지역에 따라 결정 됩니다.

![미리 보기 테넌트의 스크린샷](./media/data-residency/data-residency-b2c-tenant.png)

데이터는 **미국** 에 거주 하는 국가/지역에 있습니다.

> 미국, 캐나다, 코스타리카, 도미니카 공화국, 엘살바도르, 과테말라, 멕시코, 파나마, 푸에르토리코, 트리니다드토바고

데이터는 다음 국가/지역에 대해 **유럽** 에 상주 합니다.

> 알제리, 오스트리아, 아제르바이잔, 바레인, 벨로루시, 벨기에, 불가리아, 크로아티아, 키프로스, 체코, 덴마크, 이집트, 에스토니아, 핀란드, 프랑스, 독일, 그리스, 헝가리, 아이슬란드, 아일랜드, 이스라엘, 이탈리아, 요르단, 카자흐스탄, 케냐, 쿠웨이트, 라트비아, 레바논, 리히텐슈타인, 리투아니아, 룩셈부르크, 북마케도니아, 몰타, 몬테네그로, 모로코, 네덜란드, 나이지리아, 노르웨이, 오만, 파키스탄, 폴란드, 포르투갈, 카타르, 루마니아, 러시아, 사우디아라비아, 세르비아, 슬로바키아, 슬로베니아, 남아프리카 공화국, 스페인, 스웨덴, 스위스, 튀니지, 터키, 우크라이나, 아랍에미리트, 영국.

데이터는 다음 국가/지역에 대한 **아시아 태평양** 에 있습니다.

> 아프가니스탄, 홍콩 특별 행정구, 인도, 인도네시아, 일본, 한국, 말레이시아, 필리핀, 싱가포르, 스리랑카, 대만, 태국

다음 국가/지역은 목록에 추가 되 고 있습니다. 이제 위의 국가/지역을 선택하여 Azure AD B2C를 사용할 수 있습니다.

> 아르헨티나, 오스트레일리아, 브라질, 칠레, 콜롬비아, 에콰도르, 이라크, 뉴질랜드, 파라과이, 페루, 우루과이 및 베네수엘라.

## <a name="preview-tenant"></a>미리 보기 테넌트

Azure AD B2c의 미리 보기 기간 중에 B2C 테넌트를 만든 경우 테넌트 **형식이** **미리 보기 테 넌**트 라고 표시 될 수 있습니다.

이 경우 테넌트를 개발 및 테스트 목적 으로만 사용 해야 합니다. 프로덕션 응용 프로그램에 미리 보기 테넌트를 사용 하지 마세요.

Preview B2C 테넌트에서 프로덕션 규모 B2C 테넌트로의 **마이그레이션 경로가 없습니다** . 프로덕션 응용 프로그램에 대한 새 B2C 테넌트를 만들어야 합니다.

미리 보기 B2C 테넌트를 삭제 하 고 동일한 도메인 이름으로 프로덕션 규모 B2C 테넌트를 만들 때 알려진 문제가 있습니다. *다른 도메인 이름으로 프로덕션 규모 B2C 테넌트를 만들어야*합니다.

![미리 보기 테넌트의 스크린샷](./media/data-residency/preview-b2c-tenant.png)