---
title: Azure Service Fabric에 기존 실행 파일 배포 | Microsoft Docs
description: Service Fabric 클러스터에 배포할 수 있도록 기존 애플리케이션을 게스트 실행 파일로 패키징하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/15/2018
ms.author: aljo
ms.openlocfilehash: b7efeb1b4d83f6a6b372f73a7c0a5ca9bffdc052
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946674"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>기존 실행 파일을 Service Fabric에 배포
Azure Service Fabric에서 Node.js, Java 또는 C++과 같은 모든 종류의 코드를 서비스로 실행할 수 있습니다. Service Fabric에서는 이러한 유형의 서비스를 게스트 실행 파일이라고 합니다.

게스트 실행 파일은 서비스 패브릭에서 상태 비저장 서비스처럼 취급됩니다. 결과적으로 가용성 및 기타 메트릭을 기반으로 클러스터의 노드에 배치됩니다. 이 문서에서는 Visual Studio 또는 명령줄 유틸리티를 사용하여 Service Fabric 클러스터에 게스트 실행 파일을 패키징 및 배포하는 방법을 설명합니다.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>서비스 패브릭에서 게스트 실행 파일을 실행할 때의 이점
Service Fabric 클러스터에서 게스트 실행 파일을 실행하면 다음과 같은 몇 가지 장점이 있습니다.

* 고가용성. Service Fabric에서 실행되는 애플리케이션은 고가용성 모드입니다. Service Fabric은 애플리케이션 인스턴스가 실행 중인지 확인합니다.
* 상태 모니터링. Service Fabric 상태 모니터링은 애플리케이션이 실행 중인지 감지하고 오류가 있으면 진단 정보를 제공합니다.   
* 애플리케이션 수명 주기 관리. Service Fabric은 가동 중지 시간 없이 업그레이드를 제공할 뿐 아니라 업그레이드 중에 나쁜 상태 이벤트가 보고되면 이전 버전으로 자동 롤백을 제공합니다.    
* 밀도. 한 클러스터에서 여러 애플리케이션을 실행할 수 있으므로 애플리케이션을 고유의 하드웨어에서 실행할 필요가 없습니다.
* 검색 기능: REST를 사용 하 여 서비스를 찾는 다른 클러스터의 Service Fabric Naming 서비스를 호출할 수 있습니다. 

## <a name="samples"></a>샘플
* [게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST를 사용하여 이름 지정 서비스를 통해 통신하는 두 게스트 실행 파일(C# 및 nodejs)의 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>애플리케이션 및 서비스 매니페스트 파일 개요
게스트 실행 파일을 배포하는 일환으로 [애플리케이션 모델](service-fabric-application-model.md)에 설명된 Service Fabric 패키징 및 배포 모델을 이해하는 것이 유용합니다. Service Fabric 패키징 모델은 두 XML 파일(애플리케이션 및 서비스 매니페스트)에 의존합니다. ApplicationManifest.xml 및 ServiceManifest.xml에 대한 스키마 정의는 Service Fabric SDK와 함께 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*에 설치됩니다.

* **애플리케이션 매니페스트** 애플리케이션 매니페스트는 애플리케이션을 설명하는 데 사용되며 응용 프로그램을 구성하는 서비스와 하나 이상의 서비스 배포 방법(예: 인스턴스 수)을 정의하는 데 사용되는 기타 매개 변수를 나열합니다.

  Service Fabric에서 애플리케이션은 배포 및 업그레이드 단위입니다. 애플리케이션은 잠재적인 오류 및 잠재적 롤백이 관리되는 하나의 단위로 업그레이드될 수 있습니다. Service Fabric은 업그레이드 프로세스의 성공을 보장하며, 업그레이드가 실패할 경우 애플리케이션을 알 수 없거나 불안정한 상태로 남겨 두지 않습니다.
* **서비스 매니페스트** 서비스 매니페스트는 서비스의 구성 요소를 설명합니다. 서비스의 이름 및 유형과 같은 데이터와 그에 대한 코드 및 구성을 포함합니다. 또한 서비스 매니페스트는 서비스가 배포되면 서비스를 구성하는 데 사용할 수 있는 몇 가지 추가 매개 변수도 포함하고 있습니다.

## <a name="application-package-file-structure"></a>애플리케이션 패키지 파일 구조
애플리케이션을 Service Fabric에 배포하려면 애플리케이션은 미리 정의된 디렉터리 구조를 따라야 합니다. 다음은 해당 구조의 예입니다.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot는 애플리케이션을 정의하는 ApplicationManifest.xml 파일을 포함합니다. 애플리케이션에 포함된 각 서비스에 대한 하위 디렉터리 서비스는 서비스가 필요한 모든 아티팩트를 포함하는 데 사용됩니다. 이러한 하위 디렉터리는 ServiceManifest.xml이며 일반적으로 다음과 같습니다.

* *Code*. 이 디렉터리는 서비스 코드를 포함합니다.
* *Config*. 이 디렉터리는 런타임에 서비스가 액세스하여 특정 구성 설정을 검색할 수 있는 settings.xml 파일(필요한 경우 다른 파일도 포함)을 포함합니다.
* *Data*. 서비스에 필요할 수도 있는 추가 로컬 데이터를 저장하는 추가 디렉터리입니다. Data는 사용 후 삭제되는 데이터를 저장하는 용도로만 사용해야 합니다. 예를 들어 장애 조치 중에 서비스를 다시 배치해야 하는 경우 Service Fabric은 변경 내용을 데이터 디렉터리에 복사 또는 복제하지 않습니다.

> [!NOTE]
> `config` 및 `data` 디렉터리가 필요 없으면 만들지 않아도 됩니다.
>
>

## <a name="next-steps"></a>다음 단계
관련 정보 및 작업에 대해 다음 문서를 참조하세요.
* [게스트 실행 파일 배포](service-fabric-deploy-existing-app.md)
* [여러 개의 게스트 실행 파일 배포](service-fabric-deploy-multiple-apps.md)
* [Visual Studio를 사용하여 처음으로 게스트 실행 가능 애플리케이션 만들기](quickstart-guest-app.md)
* 패키징 도구 시험판의 링크를 포함하여 [게스트 실행 파일을 패키징 및 배포하는 샘플](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [REST를 사용하여 이름 지정 서비스를 통해 통신하는 두 게스트 실행 파일(C# 및 nodejs)의 샘플](https://github.com/Azure-Samples/service-fabric-containers)

