---
title: 기능 필터를 사용 하 여 사용자의 하위 집합에 대 한 기능 사용
titleSuffix: Azure App Configuration
description: 기능 필터를 사용 하 여 사용자의 하위 집합에 대 한 기능을 사용 하도록 설정 하는 방법을 알아봅니다.
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: lisaguthrie
ms.author: lcozzens
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 5b2eb942581f6e4163012b0f767d04c02689bb7b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88206761"
---
# <a name="use-feature-filters-to-enable-a-feature-for-a-subset-of-users"></a>기능 필터를 사용 하 여 사용자의 하위 집합에 대 한 기능 사용

기능 플래그를 사용 하 여 응용 프로그램의 기능을 활성화 하거나 비활성화할 수 있습니다. 간단한 기능 플래그는 설정 또는 해제입니다. 응용 프로그램은 항상 동일한 방식으로 동작 합니다. 예를 들어 기능 플래그 뒤에 새 기능을 롤아웃할 수 있습니다. 기능 플래그를 사용 하도록 설정 하면 모든 사용자에 게 새 기능이 표시 됩니다. 기능 플래그를 사용 하지 않도록 설정 하면 새 기능이 숨겨집니다.

반면 _조건부 기능 플래그_ 를 사용 하면 기능 플래그를 동적으로 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 응용 프로그램은 기능 플래그 조건에 따라 다르게 작동할 수 있습니다. 처음에 작은 사용자 하위 집합에 새 기능을 표시 하려는 경우를 가정해 보겠습니다. 조건부 기능 플래그를 사용 하면 일부 사용자에 대해 기능 플래그를 사용 하지 않도록 설정할 수 있습니다. _기능 필터_ 는 평가할 때마다 기능 플래그의 상태를 결정 합니다.

라이브러리에는 `Microsoft.FeatureManagement` 다음과 같은 두 가지 기능 필터가 포함 됩니다.

- `PercentageFilter` 백분율을 기준으로 기능 플래그를 사용 하도록 설정 합니다.
- `TimeWindowFilter` 지정 된 시간 동안 기능 플래그를 사용 하도록 설정 합니다.

[Microsoft FeatureManagement. ifeaturefilter 인터페이스](/dotnet/api/microsoft.featuremanagement.ifeaturefilter)를 구현 하는 고유한 기능 필터를 만들 수도 있습니다.

## <a name="registering-a-feature-filter"></a>기능 필터 등록

기능 필터의 이름을 지정 하 여 메서드를 호출 하 여 기능 필터를 등록 `AddFeatureFilter` 합니다. 예를 들어, 다음 코드는를 등록 합니다 `PercentageFilter` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Azure 앱 구성에서 기능 필터 구성

일부 기능 필터에는 추가 설정이 있습니다. 예를 들어은 `PercentageFilter` 백분율을 기반으로 기능을 활성화 합니다. 사용할 백분율을 정의 하는 설정이 있습니다.

Azure 앱 구성에 정의 된 기능 플래그에 대해 이러한 설정을 구성할 수 있습니다. 예를 들어를 사용 하 여 `PercentageFilter` 웹 앱에 대 한 요청의 50%에 대 한 기능 플래그를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [빠른 시작: ASP.NET Core 앱에 기능 플래그 추가](./quickstart-feature-flag-aspnet-core.md) 의 지침에 따라 기능 플래그를 사용 하 여 웹 앱을 만듭니다.

1. Azure Portal에서 구성 저장소로 이동 하 여 **기능 관리자**를 클릭 합니다.

1. 빠른 시작에서 만든 *Beta* 기능 플래그에 대 한 상황에 맞는 메뉴를 클릭 합니다. **편집**을 클릭합니다.

    > [!div class="mx-imgBorder"]
    > ![베타 기능 플래그 편집](./media/edit-beta-feature-flag.png)

1. **편집** 화면에서 아직 선택 하지 않은 경우 **켜기** 라디오 단추를 선택 합니다. 그런 다음 **필터 추가** 단추를 클릭 합니다. **On** 라디오 단추의 레이블은 **조건**읽기로 변경 됩니다.

1. **키** 필드에 *Microsoft. 비율*을 입력 합니다.

    > [!div class="mx-imgBorder"]
    > ![기능 필터 추가](./media/feature-flag-add-filter.png)

1. 기능 필터 키 옆에 있는 상황에 맞는 메뉴를 클릭 합니다. **매개 변수 편집**을 클릭 합니다.

    > [!div class="mx-imgBorder"]
    > ![기능 필터 매개 변수 편집](./media/feature-flag-edit-filter-parameters.png)

1. 텍스트 상자가 표에 나타나도록 **이름** 헤더 아래를 마우스로 가리킵니다. *값* 의 **이름과** **값** 50을 입력 합니다. **값** 필드는 기능 필터를 사용할 요청의 백분율을 나타냅니다.

    > [!div class="mx-imgBorder"]
    > ![기능 필터 매개 변수 설정](./media/feature-flag-set-filter-parameters.png)

1. **적용** 을 클릭 하 여 **기능 플래그 편집** 화면으로 돌아갑니다. 그런 다음 **적용** 을 다시 클릭 하 여 기능 플래그 설정을 저장 합니다.

1. 이제 기능 플래그의 **상태가** *조건*으로 표시 됩니다. 이 상태는 기능 필터가 적용 되는 조건에 따라 요청 별로 기능 플래그를 사용 하거나 사용 하지 않도록 설정 하는 것을 나타냅니다.

    > [!div class="mx-imgBorder"]
    > ![조건부 기능 플래그](./media/feature-flag-filter-enabled.png)

## <a name="feature-filters-in-action"></a>작동 중인 기능 필터

이 기능 플래그의 효과를 확인 하려면 응용 프로그램을 시작 하 고 브라우저에서 **새로 고침** 단추를 여러 번 누릅니다. 50%의 시간 동안 도구 모음에 *베타* 항목이 표시 되는 것을 볼 수 있습니다. 에서 `PercentageFilter` 요청 하위 집합에 대 한 *베타* 기능을 비활성화 하므로 나머지 시간은 숨겨집니다. 다음 비디오에서는 이러한 동작을 보여 줍니다.

> [!div class="mx-imgBorder"]
> ![PercentageFilter 실행](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [기능 관리 개요](./concept-feature-management.md)
