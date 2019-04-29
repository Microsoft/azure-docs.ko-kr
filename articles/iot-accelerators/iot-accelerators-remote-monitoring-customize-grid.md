---
title: 원격 모니터링 솔루션 UI에 그리드 추가 - Azure | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기 웹 UI의 페이지에 새 그리드를 추가하는 방법을 보여줍니다.
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/04/2018
ms.date: 11/26/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447100"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 그리드 추가

이 문서에서는 원격 모니터링 솔루션 가속기 웹 UI의 페이지에 새 그리드를 추가하는 방법을 보여줍니다. 문서에서는 다음을 설명합니다.

- 로컬 개발 환경을 준비하는 방법.
- 웹 UI의 페이지에 새 그리드를 추가하는 방법.

이 문서의 예제 그리드는 [원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 서비스 추가](iot-accelerators-remote-monitoring-customize-service.md) 방법 문서에서 추가 방법을 보여주는 서비스의 데이터를 표시합니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 완료하려면 로컬 개발 머신에 다음과 같은 소프트웨어가 설치되어 있어야 합니다.

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="before-you-start"></a>시작하기 전에

계속하려면 다음 문서의 단계를 완료해야 합니다.

- [원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 페이지 추가](iot-accelerators-remote-monitoring-customize-page.md)
- [원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 서비스 추가](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>그리드 추가

웹 UI에 그리드를 추가하려면 그리드를 정의하는 원본 파일을 추가하고, 웹 UI가 새 구성 요소를 인식하도록 일부 기존 파일을 수정해야 합니다.

### <a name="add-the-new-files-that-define-the-grid"></a>그리드를 정의하는 새 파일 추가

시작할 수 있도록 **src/walkthrough/components/pages/pageWithGrid/exampleGrid** 폴더에 그리드를 정의하는 파일이 포함되어 있습니다.

**exampleGrid.js**



**exampleGridConfig.js**



**src/walkthrough/components/pages/pageWithGrid/exampleGrid** 폴더를 **src/components/pages/example** 폴더에 복사합니다.

### <a name="add-the-grid-to-the-page"></a>페이지에 그리드 추가

**src/components/pages/example/basicPage.container.js**를 다음과 같이 수정하여 서비스 정의를 가져옵니다.

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

**src/components/pages/example/basicPage.js**를 다음과 같이 수정하여 그리드를 추가합니다.

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

**src/components/pages/example/basicPage.test.js**를 다음과 같이 수정하여 테스트를 업데이트합니다.

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>그리드 테스트

아직 로컬에서 웹 UI가 실행되고 있지 않으면 리포지토리의 로컬 복사본 루트에서 다음 명령을 실행합니다.

```cmd/sh
npm start
```

이전 명령은 [http://localhost:3000/dashboard](http://localhost:3000/dashboard)에서 UI를 로컬로 실행합니다. **예제** 페이지로 이동하여 서비스에서 그리드 표시 데이터를 확인합니다.

## <a name="select-rows"></a>행 선택

사용자가 그리드에서 행을 선택할 수 있는 두 가지 옵션이 있습니다.

### <a name="hard-select-rows"></a>행 하드 선택

사용자가 동시에 여러 행에서 작동해야 하는 경우 행에 있는 확인란을 사용합니다.

1. **checkboxColumn**을 그리드에 제공된 **columnDefs**에 추가하여 행의 하드 선택을 사용하도록 설정합니다. **checkboxColumn**은 **/src/components/shared/pcsGrid/pcsGrid.js**에 정의됩니다.

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. 선택한 항목에 액세스하려면 내부 그리드 API에 대한 참조를 가져옵니다.

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. 그리드의 행이 하드 선택된 경우 페이지에 컨텍스트 단추를 제공합니다.

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. 컨텍스트 단추를 클릭하면 작업을 수행할 수 있도록 하드 선택된 항목을 가져옵니다.

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>행 소프트 선택

사용자가 단일 행에서만 작동해야 하는 경우 **columnDefs**에서 하나 이상의 열에 대해 소프트 선택 링크를 구성합니다.

1. **exampleGridConfig.js**에서 **SoftSelectLinkRenderer**를 **columnDef**에 대해 **cellRendererFramework**로 추가합니다.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. 소프트 선택 링크를 클릭하면 **onSoftSelectChange** 이벤트가 트리거됩니다. 세부 정보 플라이아웃 열기와 같이 해당 행에 대해 필요한 모든 작업을 수행합니다. 이 예제에서는 간단히 콘솔에 작성합니다.

    ```js
    onSoftSelectChange = (rowId, rowEvent) => {
      const { onSoftSelectChange } = this.props;
      const obj = (this.gridApi.getDisplayedRowAtIndex(rowId) || {}).data;
      if (obj) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', obj);
        this.setState({ softSelectedObj: obj });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(obj, rowEvent);
      }
    }
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 원격 모니터링 솔루션 가속기의 웹 UI에서 페이지를 추가하거나 사용자 지정하는 데 용이하게 사용할 수 있는 리소스에 대해 알아보았습니다.

그리드를 정의했으니, 그 다음 단계는 예제 페이지에 표시되는 [사용자 지정 플라이아웃을 원격 모니터링 솔루션 가속기 웹 UI에 추가](iot-accelerators-remote-monitoring-customize-flyout.md)하는 것입니다.

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.
