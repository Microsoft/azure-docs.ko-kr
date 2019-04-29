---
title: Azure Service Fabric에서 매개 변수를 사용하여 서비스의 포트 번호를 지정하는 방법
description: 매개 변수를 사용하여 Service Fabric에서 애플리케이션에 대한 포트를 지정하는 방법을 보여 줍니다.
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: d69e02126564388bf045693b9960e6e574307641
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720249"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Service Fabric에서 매개 변수를 사용하여 서비스의 포트 번호를 지정하는 방법

이 문서에서는 Visual Studio에서 Service Fabric의 매개 변수를 사용하여 서비스의 포트 번호를 지정하는 방법을 보여 줍니다.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>매개 변수를 사용하여 서비스의 포트 번호를 지정하는 절차

이 예제에서는 매개 변수를 사용하여 asp.net core 웹 API에 대한 포트 번호를 설정합니다.

1. Visual Studio를 열고 새 Service Fabric 애플리케이션을 만듭니다.
1. 상태 비저장 ASP.NET Core 템플릿을 선택합니다.
1. 웹 API를 선택합니다.
1. ServiceManifest.xml 파일을 엽니다.
1. 서비스에 대해 지정된 엔드포인트의 이름을 기록해 둡니다. 기본값은 `ServiceEndpoint`입니다.
1. ApplicationManifest.xml 파일 열기
1. ServiceManifest.xml 파일의 `ServiceManifestImport` 요소에서 엔드포인트에 대한 참조를 사용하여 새 `RessourceOverrides` 요소를 추가합니다.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. `Endpoint` 요소에서 이제 매개 변수를 사용하여 모든 특성을 재정의할 수 있습니다. 이 예제에서는 `Port`를 지정하고 대괄호를 사용하여 매개 변수 이름으로 설정합니다(예: `[MyWebAPI_PortNumber]`).

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. ApplicationManifest.xml 파일에서 `Parameters` 요소에 매개 변수를 지정합니다.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. 그런 후 `DefaultValue`를 정의합니다.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. ApplicationParameters 폴더 및 `Cloud.xml` 파일 열기
1. 원격 클러스터에 게시할 때 사용할 다른 포트를 지정하려면 포트 번호를 포함하는 매개 변수를 이 파일에 추가합니다.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Cloud.xml 게시 프로필을 사용하여 Visual Studio에서 애플리케이션을 게시할 경우 서비스는 포트 80을 사용하도록 구성됩니다. MyWebAPI_PortNumber 매개 변수를 지정하지 않고 애플리케이션을 배포하는 경우 서비스는 포트 8080을 사용합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서 설명하는 핵심 개념 중 일부에 대한 자세한 내용은 [여러 환경에 대한 애플리케이션 관리](service-fabric-manage-multiple-environment-app-configuration.md) 문서를 참조하세요.

Visual Studio에서 사용할 수 있는 다른 앱 관리 기능에 대한 정보는 [Visual Studio에서 Service Fabric 애플리케이션 관리](service-fabric-manage-application-in-visual-studio.md)를 참조하세요.
