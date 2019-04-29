---
title: 원격 모니터링 솔루션 UI-Azure에 서비스 추가 | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기 웹 UI에 새 서비스를 추가하는 방법을 보여 줍니다.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/02/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447049"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 서비스 추가

이 문서에서는 원격 모니터링 솔루션 가속기 웹 UI에 새 서비스를 추가하는 방법을 보여 줍니다. 문서에서는 다음을 설명합니다.

- 로컬 개발 환경을 준비하는 방법.
- 웹 UI에 새 서비스를 추가하는 방법.

이 문서의 예제 서비스는 [원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 그리드 추가](iot-accelerators-remote-monitoring-customize-grid.md) 방법 문서에서 추가 방법을 보여 주는 그리드의 데이터를 제공합니다.

React 애플리케이션에서 서비스는 일반적으로 백 엔드 서비스와 상호 작용합니다. 원격 모니터링 솔루션 가속기의 예제에는 IoT 허브 관리자 및 구성 마이크로 서비스와 상호 작용하는 서비스가 포함됩니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 완료하려면 로컬 개발 머신에 다음과 같은 소프트웨어가 설치되어 있어야 합니다.

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>시작하기 전에

계속하기 전에 [원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 페이지 추가](iot-accelerators-remote-monitoring-customize-page.md) 방법 문서의 단계를 완료해야 합니다.

## <a name="add-a-service"></a>서비스 추가

웹 UI에 서비스를 추가하려면 서비스를 정의하는 원본 파일을 추가하고, 웹 UI가 새 서비스를 인식하도록 일부 기존 파일을 수정해야 합니다.

### <a name="add-the-new-files-that-define-the-service"></a>서비스를 정의하는 새 파일 추가

시작할 수 있도록 **src/walkthrough/services** 폴더에 간단한 서비스를 정의하는 파일이 있습니다.

**exampleService.js**



서비스 구현 방법을 자세히 알아보려면 [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)(이제까지 놓친 반응형 프로그래밍 소개)을 참조하세요.

**model/exampleModels.js**



**exampleService.js**를 **src/services** 폴더에 복사하고 **exampleModels.js**를 **src/services/models** 폴더에 복사합니다.

**src/services** 폴더에서 **index.js** 파일을 업데이트하여 새 서비스를 내보냅니다.

```js
export * from './exampleService';
```

**src/services/models** 폴더에서 **index.js** 파일을 업데이트하여 새 모델을 내보냅니다.

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>스토어에서 서비스 호출 설정

시작할 수 있도록 **src/walkthrough/store/reducers** 폴더에 샘플 reducer가 있습니다.

**exampleReducer.js**



**exampleReducer.js**를 **src/store/reducers** 폴더에 복사합니다.

reducer와 **Epics**에 대한 자세한 내용은 [redux-observable](https://redux-observable.js.org/)을 참조하세요.

### <a name="configure-the-middleware"></a>미들웨어 구성

미들웨어를 구성하려면 **src/store** 폴더의 **rootReducer.js** 파일에 reducer를 추가합니다.

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

**src/store** 폴더의 **rootEpics.js** 파일에 epics를 추가합니다.

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 원격 모니터링 솔루션 가속기의 웹 UI에서 서비스를 추가하거나 사용자 지정하는 데 용이하게 사용할 수 있는 리소스에 대해 알아보았습니다.

서비스를 정의했으므로 그다음 단계로 서비스에서 반환되는 데이터를 표시하는 [원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 서비스를 추가](iot-accelerators-remote-monitoring-customize-grid.md)합니다.

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.
