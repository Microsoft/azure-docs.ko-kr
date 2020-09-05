---
title: Application Insights JavaScript SDK의 네이티브 반응 플러그 인
description: JavaScript SDK Application Insights에 대 한 기본 반응 플러그 인을 설치 하 고 사용 하는 방법입니다.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 3dac47aa7cf93ca882c4c1d0d191dabf0eb7178c
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227434"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK의 네이티브 반응 플러그 인

JavaScript SDK Application Insights의 기본 반응 플러그 인은 장치 정보를 수집 합니다. 기본적으로이 플러그 인은 다음을 자동으로 수집 합니다.

- **고유한 장치 id** (설치 id 라고도 함)
- **장치 모델 이름** (예: iPhone X, Samsung Galaxy 접기, Huawei P30 Pro 등)
- **장치 유형** (예: 송수화기, 태블릿 등)

## <a name="requirements"></a>요구 사항

>= 2.0.0의 버전을 사용 해야 합니다 `@microsoft/applicationinsights-web` . 이 플러그 인은 네이티브 앱에 반응 하는 경우에만 작동 합니다. 이러한 작업은 [응용 프로그램을 사용 하 여 응용 프로그램](https://docs.expo.io/)에서 작동 하지 않습니다. 따라서 Create 반응할 네이티브 앱에서 작동 하지 않습니다.

## <a name="getting-started"></a>시작

[기본 장치-정보](https://www.npmjs.com/package/react-native-device-info) 패키지를 설치 하 고 연결 합니다. 앱을 `react-native-device-info` 사용 하 여 최신 장치 이름을 수집 하려면 패키지를 최신 상태로 유지 하세요.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>플러그 인 초기화

이 플러그 인을 사용 하려면 플러그 인을 구성 하 고 `extension` 기존 Application Insights 인스턴스에로 추가 해야 합니다.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>다음 단계

- JavaScript SDK에 대해 자세히 알아보려면 [Application Insights JAVASCRIPT sdk 설명서](javascript.md)를 참조 하세요.
- Kusto 쿼리 언어에 대해 알아보고 Log Analytics에서 데이터를 쿼리 하는 방법에 대 한 자세한 내용은 [로그 쿼리 개요](../../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.
