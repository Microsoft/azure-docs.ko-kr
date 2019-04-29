---
title: JavaScript API를 사용하여 보고서와 상호 작용 | Microsoft Docs
description: Power BI JavaScript API를 사용하면 애플리케이션에 Power BI 보고서를 쉽게 포함할 수 있습니다.
services: power-bi-embedded
author: markingmyname
ROBOTS: NOINDEX
ms.assetid: bdd885d3-1b00-4dcf-bdff-531eb1f97bfb
ms.service: power-bi-embedded
ms.topic: conceptual
ms.workload: powerbi
origin.date: 09/26/2018
ms.date: 03/05/2019
ms.author: v-junlch
ms.openlocfilehash: 252296af8b2065ae22bed8b421d4d00718b78287
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110466"
---
# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>JavaScript API를 사용하여 Power BI 보고서와 상호 작용

Power BI JavaScript API를 사용하면 애플리케이션에 Power BI 보고서를 쉽게 포함할 수 있습니다. 애플리케이션에서 API를 사용하여 프로그래밍 방식으로 페이지 및 필터와 같은 다른 보고서 요소와 상호 작용할 수 있습니다. 이러한 상호 작용은 Power BI 보고서가 애플리케이션에 더욱 통합되게 합니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

애플리케이션의 일부로 호스팅되는 iframe을 사용하여 애플리케이션에 Power BI 보고서를 포함합니다. iframe은 다음 이미지에 표시된 대로 애플리케이션 및 보고서 간에 경계의 역할을 합니다.

![Javascript API가 없는 Power BI 작업 영역 컬렉션 iframe](./media/interact-with-reports/iframe-without-javacript.png)

iframe을 통해 포함 프로세스가 훨씬 쉬워지지만 JavaScript API 없이 보고서와 애플리케이션은 서로 상호 작용할 수 없습니다. 상호 작용이 부족하면 보고서가 실제로 애플리케이션의 일부가 아닌 것처럼 느낄 수 있습니다. 보고서와 애플리케이션은 실제로 다음 이미지와 같이 서로 통신해야 합니다.

![Javascript API가 있는 Power BI 작업 영역 컬렉션 iframe](./media/interact-with-reports/iframe-with-javascript.png)

Power BI JavaScript API를 사용하면 안전하게 iframe 경계를 통과할 수 있는 코드를 작성할 수 있습니다. 이를 통해 애플리케이션은 프로그래밍 방식으로 보고서에서 작업을 수행하고 보고서 내에서 사용자가 만든 작업으로부터의 이벤트를 수신 대기할 수 있습니다.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>Power BI JavaScript API를 사용하여 할 수 있는 작업은 무엇인가요?

JavaScript API를 통해 보고서를 관리하고 보고서의 페이지를 탐색하고 보고서를 필터링하고 포함 이벤트를 처리할 수 있습니다. 다음 다이어그램은 API의 구조를 보여 줍니다.

![Power BI JavaScript API 다이어그램](./media/interact-with-reports/javascript-api-diagram.png)

### <a name="manage-reports"></a>보고서 관리
Javascript API를 통해 보고서 및 페이지 수준에서 동작을 관리할 수 있습니다.

- 애플리케이션에 특정 Power BI 보고서를 안전하게 포함 - [데모 애플리케이션 포함](https://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - 액세스 토큰 설정
- 보고서 구성
  - 필터 창 및 페이지 탐색 창 사용 및 사용 안 함 - [설정 데모 애플리케이션 업데이트](https://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - 페이지 및 필터에 대한 기본값 설정 - [기본값 데모 설정](https://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- 전체 화면 모드 시작 및 종료

[포함 보고서에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)

### <a name="navigate-to-pages-in-a-report"></a>보고서의 페이지로 이동
JavaScript API를 통해 보고서의 모든 페이지를 검색하고 현재 페이지를 설정할 수 있습니다. [탐색 데모 애플리케이션](https://azure-samples.github.io/powerbi-angular-client/#/scenario3)을 사용해 보세요.

[페이지 탐색에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>보고서 필터링
JavaScript API는 포함된 보고서 및 보고서 페이지에 대한 기본 및 고급 필터링 기능을 제공합니다. [데모 애플리케이션 필터링](https://azure-samples.github.io/powerbi-angular-client/#/scenario4)을 사용해 보고 여기에서 소개 코드를 검토합니다.

#### <a name="basic-filters"></a>기본 필터
기본 필터는 열 또는 계층 수준에 배치되어 포함하거나 제외할 값의 목록을 포함합니다.

```typescript
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "https://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```

#### <a name="advanced-filters"></a>고급 필터
고급 필터는 논리 연산자 AND 또는 OR을 사용하고 고유한 해당 연산자 및 값과 함께 각각 하나 또는 두 개의 조건을 수용합니다. 지원되는 조건은 다음과 같습니다.

- 없음
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- 포함
- DoesNotContain
- StartsWith
- DoesNotStartWith
- Is
- IsNot
- IsBlank
- IsNotBlank

```typescript
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "https://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```

[필터링에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)

### <a name="handling-events"></a>이벤트 처리

애플리케이션은 iframe에 정보를 보낼 뿐만 아니라 iframe에서 가져온 다음 이벤트에 대한 정보를 받을 수 있습니다.

- Embed
  - loaded
  - error
- 보고서
  - pageChanged
  - dataSelected(출시 예정)

[이벤트를 처리하는 방법에 대해 자세히 알아보기](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)

## <a name="next-steps"></a>다음 단계

Power BI JavaScript API에 대한 자세한 내용은 다음 링크를 확인하세요.

- [JavaScript API Wiki](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [개체 모델 참조](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- [라이브 데모](https://microsoft.github.io/PowerBI-JavaScript/demo/)

<!-- Update_Description: update metedata properties -->