---
title: 연결된 팩터리 솔루션 사용자 지정 - Azure | Microsoft Docs
description: 연결된 팩터리 솔루션 가속기의 동작을 사용자 지정하는 방법에 대한 설명입니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: csharp
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: dobett
ms.openlocfilehash: 7d4e95d066e191e1d5b6d083ede65843dbe73f31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450436"
---
# <a name="customize-how-the-connected-factory-solution-displays-data-from-your-opc-ua-servers"></a>연결된 팩터리 솔루션이 OPC UA 서버의 데이터를 표시하는 방식 사용자 지정

연결된 팩터리 솔루션은 솔루션에 연결된 OPC UA 서버의 데이터를 집계하고 표시합니다. 솔루션에서 OPC UA 서버를 찾아 명령을 보낼 수 있습니다. OPC UA에 대한 자세한 내용은 [연결된 팩터리 FAQ](iot-accelerators-faq-cf.md)를 참조하세요.

솔루션에서 집계된 데이터의 예로 팩터리, 라인 및 스테이션 수준의 대시보드에서 볼 수 있는 OEE(Overall Equipment Efficiency) 및 KPI(핵심 성과 지표)를 들 수 있습니다. 다음 스크린샷은 **Munich** 공장, **Production line 1**의 **Assembly** 스테이션에 대한 OEE 및 KPI 값을 보여 줍니다.

![솔루션의 OEE 및 KPI 값의 예][img-oee-kpi]

솔루션을 사용하면 *스테이션*이라는 OPC UA 서버에서 특정 데이터 항목의 자세한 정보를 볼 수 있습니다. 다음 스크린샷은 특정 스테이션에서 제조된 항목 수의 그림을 보여 줍니다.

![제조된 항목 수 그림][img-manufactured-items]

그래프 중 하나를 클릭하면 TSI(Time Series Insights)를 사용하여 데이터를 추가로 탐색할 수 있습니다.

![Time Series Insights를 사용하여 데이터 탐색][img-tsi]

이 문서에서는 다음을 설명합니다.

- 솔루션의 다양한 보기에 데이터가 제공되도록 하는 방법
- 솔루션이 데이터를 표시하는 방식을 사용자 지정할 수 있는 방법

## <a name="data-sources"></a>데이터 원본

연결된 팩터리 솔루션은 솔루션에 연결된 OPC UA 서버의 데이터를 표시합니다. 기본 설치는 공장 시뮬레이션을 실행하는 여러 OPC UA 서버를 포함합니다. [게이트웨이 통해 솔루션에 연결][lnk-connect-cf]하는 사용자 고유의 OPC UA 서버를 추가할 수 있습니다.

대시보드에서 연결된 OPC UA 서버가 솔루션에 보낼 수 있는 데이터 항목을 찾아볼 수 있습니다.

1. **브라우저**를 선택하여 **OPC UA 서버 선택** 보기로 이동합니다.

    ![OPC UA 서버 선택 보기로 이동][img-select-server]

1. 서버를 선택하고 **연결**을 클릭합니다. 보안 경고가 표시되면 **계속**을 클릭합니다.

    > [!NOTE]
    > 이 경고는 각 서버에 대해 한 번만 표시되며 솔루션 대시보드와 서버 간에 트러스트 관계를 설정합니다.

1. 이제 서버가 솔루션에 보낼 수 있는 데이터 항목을 찾아볼 수 있습니다. 솔루션에 전송되는 항목에는 다음과 같이 확인 표시가 있습니다.

    ![게시된 항목][img-published]

1. 솔루션의 *관리자*는 데이터 항목을 게시하여 연결된 팩터리 솔루션에 제공되도록 선택할 수 있습니다. 관리자는 OPC UA 서버에서 데이터 항목의 값을 변경하고 메서드를 호출할 수도 있습니다.

## <a name="map-the-data"></a>데이터 매핑

연결된 팩터리 솔루션은 OPC UA 서버의 게시된 데이터 항목을 솔루션의 다양한 보기에 매핑하고 집계합니다. 솔루션을 프로비전하면 연결된 팩터리 솔루션이 Azure 계정에 배포됩니다. Visual Studio 연결된 팩터리 솔루션의 JSON 파일은 이 매핑 정보를 저장합니다. 연결된 팩터리 Visual Studio 솔루션에서 이 JSON 구성 파일을 보고 수정할 수 있습니다. 변경을 수행한 후 솔루션을 다시 배포할 수 있습니다.

구성 파일을 사용하여 다음을 수행할 수 있습니다.

- 시뮬레이션된 기존 공장, 생산 라인 및 스테이션을 편집합니다.
- 연결하는 실제 OPC UA 서버의 데이터를 솔루션에 매핑합니다.

특정 요구 사항에 맞게 데이터를 매핑하고 집계하는 방법에 대한 자세한 내용은 [연결된 팩터리 솔루션 가속기를 구성하는 방법](iot-accelerators-connected-factory-configure.md)을 참조하세요.

## <a name="deploy-the-changes"></a>변경 내용 배포

**ContosoTopologyDescription.json** 파일에 대한 변경이 끝나면 연결된 팩터리 솔루션을 Azure 계정에 다시 배포해야 합니다.

**azure-iot-connected-factory** 리포지토리에는 솔루션을 다시 빌드하고 배포하는 데 사용할 수 있는 **build.ps1** PowerShell 스크립트가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 연결된 팩터리 솔루션 가속기에 대해 자세히 알아봅니다.

* [연결된 팩터리 솔루션 가속기 연습][lnk-rm-walkthrough]
* [연결된 팩터리에 대한 게이트웨이 배포][lnk-connect-cf]
* [Azureiotsolutions.com 사이트에 대 한 권한][lnk-permissions]
* [연결된 팩터리 FAQ](iot-accelerators-faq-cf.md)
* [FAQ][lnk-faq]


[img-oee-kpi]: ./media/iot-accelerators-connected-factory-customize/oeenadkpi.png
[img-manufactured-items]: ./media/iot-accelerators-connected-factory-customize/manufactured.png
[img-tsi]: ./media/iot-accelerators-connected-factory-customize/tsi.png
[img-select-server]: ./media/iot-accelerators-connected-factory-customize/selectserver.png
[img-published]: ./media/iot-accelerators-connected-factory-customize/published.png


[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md