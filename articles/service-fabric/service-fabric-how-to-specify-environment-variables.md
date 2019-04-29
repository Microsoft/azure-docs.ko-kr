---
title: Azure Service Fabric에서 서비스에 대한 환경 변수를 지정하는 방법 | Microsoft Docs
description: Service Fabric에서 애플리케이션에 대한 환경 변수를 사용하는 방법을 보여 줍니다.
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
ms.openlocfilehash: f75de635f08ae06db349387a436c636c149ec9f2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720232"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Service Fabric에서 서비스에 대한 환경 변수를 지정하는 방법

이 문서에서는 Service Fabric에서 서비스 또는 컨테이너에 대한 환경 변수를 지정하는 방법을 보여 줍니다.

## <a name="procedure-for-specifying-environment-variables-for-services"></a>서비스에 대한 환경 변수를 지정하기 위한 절차

이 예제에서는 컨테이너에 대한 환경 변수를 설정합니다. 이 문서에서는 애플리케이션 및 서비스 매니페스트가 이미 있다고 가정합니다.

1. ServiceManifest.xml 파일을 엽니다.
1. `CodePackage` 요소에서 각 환경 변수에 대해 새 `EnvironmentVariables` 요소 및 `EnvironmentVariable` 요소를 추가합니다.

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DefaultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    환경 변수는 애플리케이션 매니페스트에서 재정의할 수 있습니다.

1. 애플리케이션 매니페스트에서 환경 변수를 재정의하려면 `EnvironmentOverrides` 요소를 사용합니다.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>다음 단계
이 문서에서 설명하는 핵심 개념 중 일부에 대한 자세한 내용은 [여러 환경에 대한 애플리케이션 관리](service-fabric-manage-multiple-environment-app-configuration.md) 문서를 참조하세요.

Visual Studio에서 사용할 수 있는 다른 앱 관리 기능에 대한 정보는 [Visual Studio에서 Service Fabric 애플리케이션 관리](service-fabric-manage-application-in-visual-studio.md)를 참조하세요.