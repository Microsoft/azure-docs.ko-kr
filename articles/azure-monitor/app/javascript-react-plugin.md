---
title: Application Insights JavaScript SDK 용 플러그 인 반응
description: JavaScript SDK Application Insights에 대 한 응답 플러그 인을 설치 하 고 사용 하는 방법입니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 677810c21f9ea6151e2ffe7a4e2b9cdc8473a09f
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227431"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK 용 플러그 인 반응

Application Insights JavaScript SDK에 대 한 플러그 인에 대응 하 여 다음을 사용 합니다.

- 경로 변경 내용 추적
- 구성 요소 사용 통계에 반응

## <a name="getting-started"></a>시작

Npm 패키지를 설치 합니다.

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>기본적인 사용 방법

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>구성

| 속성    | 기본값 | Description                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | null    | 라우터 기록에 반응 합니다. 자세한 내용은 [라우터 패키지 응답 설명서](https://reactrouter.com/web/api/history)를 참조 하세요. 구성 요소 외부에서 기록 개체에 액세스 하는 방법을 알아보려면 [라우터 응답 FAQ](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components) 를 참조 하세요.    |

### <a name="react-components-usage-tracking"></a>구성 요소 사용 추적에 대응

다양 한 반응 구성 요소 사용 추적을 계측 하려면 `withAITracking` 고차 구성 요소 기능을 적용 합니다.

이벤트를 통해 이벤트에서 시간을 측정 합니다 `ComponentDidMount` `ComponentWillUnmount` . 그러나이를 보다 정확 하 게 하기 위해 사용자가 유휴 상태 였던 시간을 빼는 것 `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` 입니다.

Azure Portal에서 Application Insights 리소스로 이동 해야 하는이 메트릭을 확인 하려면 "메트릭" 탭을 선택 하 고 "메트릭" 탭을 선택 하 여 빈 차트를 구성 하 고, 사용자 지정 메트릭 이름 "구성 요소에 반응 하는 시간 (초)"을 표시 하 고, 메트릭의 집계 (합계, 평균 등)를 선택 하 고, 분할을 "Component Name"으로 적용

!["구성 요소 참여 시간 (초)" "구성 요소 이름으로 분할"의 사용자 지정 메트릭을 표시 하는 차트의 스크린샷](./media/javascript-react-plugin/chart.png)

사용자 지정 쿼리를 실행 하 여 요구 사항에 따라 보고서 및 시각화를 생성 하 Application Insights 데이터를 분할할 수도 있습니다. Azure Portal에서 Application Insights 리소스로 이동 하 여 개요 탭의 상단 메뉴에서 "분석"을 선택 하 고 쿼리를 실행 합니다.

![사용자 지정 메트릭 쿼리 결과의 스크린샷](./media/javascript-react-plugin/query.png)

> [!NOTE]
> 새 사용자 지정 메트릭이 Azure Portal에 표시 되는 데 최대 10 분이 걸릴 수 있습니다.

## <a name="sample-app"></a>샘플 앱

[Application Insights에 반응](https://github.com/Azure-Samples/application-insights-react-demo)하는 데모를 확인 하세요.

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대해 자세히 알아보려면 [Application Insights JAVASCRIPT sdk 설명서](javascript.md)를 참조 하세요.
- Kusto 쿼리 언어에 대해 알아보고 Log Analytics에서 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [로그 쿼리 개요](../../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.
