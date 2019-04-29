---
title: 원격 모니터링 솔루션 UI-Azure에 페이지 추가 | Microsoft Docs
description: 이 문서에서는 원격 모니터링 솔루션 가속기 웹 UI에 새 페이지를 추가하는 방법을 보여 줍니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: 95830cdffb232e16f9fbae51cfa11fbd18172c3c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447083"
---
# <a name="add-a-custom-page-to-the-remote-monitoring-solution-accelerator-web-ui"></a>원격 모니터링 솔루션 가속기 웹 UI에 사용자 지정 페이지 추가

이 문서에서는 원격 모니터링 솔루션 가속기 웹 UI에 새 페이지를 추가하는 방법을 보여 줍니다. 문서에서는 다음을 설명합니다.

- 로컬 개발 환경을 준비하는 방법.
- 웹 UI에 새 페이지를 추가하는 방법.

다른 방법 가이드에서는 이 시나리오를 확장하여 추가하는 페이지에 더 많은 기능을 추가합니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 완료하려면 로컬 개발 머신에 다음과 같은 소프트웨어가 설치되어 있어야 합니다.

- [Git](https://git-scm.com/downloads)
- [Node.JS](https://nodejs.org/download/)

## <a name="prepare-a-local-development-environment-for-the-ui"></a>UI에 대한 로컬 개발 환경 준비

원격 모니터링 솔루션 가속기 UI 코드는 [React](https://reactjs.org/) JavaScript 프레임워크를 사용하여 구현됩니다. [원격 모니터링 웹 UI](https://github.com/Azure/pcs-remote-monitoring-webui) GitHub 리포지토리에서 소스 코드를 찾을 수 있습니다.

UI를 변경하고 UI 변경 내용을 테스트하려면 로컬 개발 머신에서 UI를 실행하면 됩니다. 필요한 경우 로컬 복사본을 솔루션 가속기의 배포된 인스턴스에 연결하면 로컬 복사본이 실제 또는 시뮬레이트된 디바이스와 상호 작용할 수 있습니다.

로컬 개발 환경을 준비하려면 Git을 사용하여 [원격 모니터링 웹 UI](https://github.com/Azure/pcs-remote-monitoring-webui) 리포지토리를 로컬 머신에 복제합니다.

```cmd/sh
git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
```

## <a name="add-a-page"></a>페이지 추가

웹 UI에 페이지를 추가하려면 페이지를 정의하는 원본 파일을 추가하고, 웹 UI가 새 페이지를 인식하도록 일부 기존 파일을 수정해야 합니다.

### <a name="add-the-new-files-that-define-the-page"></a>페이지를 정의하는 새 파일 추가

시작할 수 있도록 **src/walkthrough/components/pages/basicPage** 폴더에 간단한 페이지를 정의하는 4개의 파일이 있습니다.

**basicPage.container.js**

[!code-javascript[Page container source](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.container.js?name=container "Page container source")]

**basicPage.js**

[!code-javascript[Basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.js?name=page "Basic page")]

**basicPage.scss**

[!code-javascript[Page styling](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.scss?name=styles "Page styling")]

**basicPage.test.js**

[!code-javascript[Test code for basic page](~/remote-monitoring-webui/src/walkthrough/components/pages/basicPage/basicPage.test.js?name=test "Test code for basic page")]

새 폴더 **src/components/pages/example**을 만들고 이 4개의 파일을 폴더에 복사합니다.

### <a name="add-the-new-page-to-the-web-ui"></a>웹 UI에 새 페이지 추가

웹 UI에 새 페이지를 추가하려면 기존 파일을 다음과 같이 변경합니다.

1. 새 페이지 컨테이너를 **src/components/pages/index.js** 파일에 추가합니다.

    ```js
    export * from './example/basicPage.container';
    ```

1. (선택 사항) 새 페이지의 SVG 아이콘을 추가합니다. 자세한 내용은 [webui/src/utilities/README.md](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/utilities/README.md)를 참조하세요. 기존 SVG 파일을 사용할 수 있습니다.

1. 페이지 이름을 번역 파일, **public/locales/en/translations.json**에 추가합니다. 웹 UI는 국제화에 [i18next](https://www.i18next.com/)를 사용합니다.

    ```json
    "tabs": {
      "basicPage": "Example",
    },
    ```

1. 최상위 애플리케이션 페이지를 정의하는 **src/components/app.js** 파일을 엽니다. 가져오기 목록에 새 페이지를 추가합니다.

    ```javascript
    // Page Components
    import  {
      //...
      BasicPageContainer
    } from './pages';
    ```

1. 동일한 파일에서 `pagesConfig` 배열에 새 페이지를 추가합니다. 경로의 `to` 주소를 설정하고, 이전에 추가된 SVG 아이콘과 번역을 참조하고, 페이지의 컨테이너로 `component`를 설정합니다.

    ```js
    const pagesConfig = [
      //...
      {
        to: '/basicpage',
        exact: true,
        svg: svgs.tabs.example,
        labelId: 'tabs.basicPage',
        component: BasicPageContainer
      },
      //...
    ];
    ```

1. `crumbsConfig` 배열에 새 이동 경로를 추가합니다.

    ```js
    const crumbsConfig = [
      //...
      {
        path: '/basicpage', crumbs: [
          { to: '/basicpage', labelId: 'tabs.basicPage' }
        ]
      },
      //...
    ];
    ```

    이 예제 페이지에는 하나의 이동 경로만 있지만 일부 페이지에는 더 많은 이동 경로가 있을 수 있습니다.

변경 내용을 모두 저장합니다. 새 페이지가 추가된 웹 UI를 실행할 준비가 되었습니다.

### <a name="test-the-new-page"></a>새 페이지 테스트

명령 프롬프트에서 리포지토리 로컬 복사본의 루트로 이동하고 다음 명령을 실행하여 필요한 라이브러리를 설치하고 웹 UI를 로컬로 실행합니다.

```cmd/sh
npm install
npm start
```

이전 명령은 [http://localhost:3000/dashboard](http://localhost:3000/dashboard)에서 UI를 로컬로 실행합니다.

웹 UI의 로컬 인스턴스를 솔루션 가속기의 배포된 인스턴스에 연결하지 않으면 대시보드에 오류가 표시됩니다. 이러한 오류는 새 페이지를 테스트하는 기능에 영향을 주지 않습니다.

이제 사이트가 로컬로 실행되는 동안 코드를 편집하고 웹 UI 업데이트를 동적으로 확인할 수 있습니다.

## <a name="optional-connect-to-deployed-instance"></a>[선택 사항] 배포된 인스턴스에 연결

필요한 경우 웹 UI의 로컬 실행 복사본을 클라우드의 원격 모니터링 솔루션 가속기에 연결할 수 있습니다.

1. **pcs** CLI를 사용하여 솔루션 가속기의 **기본** 인스턴스를 배포합니다. 가상 머신에 대해 제공한 배포 및 자격 증명의 이름을 기록해 둡니다. 자세한 내용은 [CLI를 사용하여 배포](iot-accelerators-remote-monitoring-deploy-cli.md)를 참조하세요.

1. Azure Portal 또는 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 솔루션에서 마이크로 서비스를 호스트하는 가상 머신에 대한 SSH 액세스를 활성화합니다. 예를 들면 다음과 같습니다.

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    테스트 및 개발하는 동안 SSH 액세스만 활성화해야 합니다. SSH를 활성화하는 경우 [가능한 한 빨리 비활성화해야 합니다](../security/azure-security-network-security-best-practices.md).

1. Azure Portal 또는 [az CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 사용하여 가상 머신의 이름 및 공용 IP 주소를 찾습니다. 예를 들면 다음과 같습니다.

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. SSH를 사용하여 이전 단계의 IP 주소 및 솔루션을 배포하기 위해 **pcs**를 실행했을 때 제공한 자격 증명을 사용하여 가상 머신에 연결합니다.

1. 로컬 UX를 연결하도록 허용하려면 가상 머신의 bash 셸에서 다음 명령을 실행합니다.

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. 명령이 완료되고 웹 사이트가 시작하는 것을 확인한 후 가상 머신에서 연결을 끊을 수 있습니다.

1. [원격 모니터링 웹 UI](https://github.com/Azure/pcs-remote-monitoring-webui) 리포지토리의 로컬 복사본에서 **.env** 파일을 편집하여 배포된 솔루션의 URL을 추가합니다.

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

## <a name="next-steps"></a>다음 단계

이 문서에서는 원격 모니터링 솔루션 가속기에서 웹 UI를 사용자 지정하는 데 사용할 수 있는 리소스에 대해 알아보았습니다.

페이지를 정의했으므로 그다음 단계로 UI에 표시할 데이터를 검색하는 [사용자 지정 서비스를 원격 모니터링 솔루션 가속기에 추가](iot-accelerators-remote-monitoring-customize-service.md)합니다.

원격 모니터링 솔루션 가속기에 대한 자세한 개념 정보는 [원격 모니터링 아키텍처](iot-accelerators-remote-monitoring-sample-walkthrough.md)를 참조하세요.
