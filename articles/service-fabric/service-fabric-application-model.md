---
title: Azure Service Fabric 애플리케이션 모델 | Microsoft Docs
description: 서비스 패브릭에서 애플리케이션 및 서비스를 모델링 및 설명하는 방법
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621454"
---
# <a name="model-an-application-in-service-fabric"></a>서비스 패브릭에서 애플리케이션 모델링
이 문서에서는 Azure Service Fabric 애플리케이션 모델의 개요 및 매니페스트 파일을 통해 애플리케이션과 서비스를 정의하는 방법에 대해 설명합니다.

## <a name="understand-the-application-model"></a>애플리케이션 모델의 이해
애플리케이션은 특정 기능을 수행하는 구성 서비스 컬렉션입니다. 서비스는 완전한 독립 실행형 기능을 수행하며 다른 서비스와 독립적으로 시작할 수 있습니다.  서비스는 코드, 구성 및 데이터로 이루어집니다. 각 서비스에 대해 코드는 실행 가능한 이진으로 구성되고, 구성은 런타임에 로드할 수 있는 서비스 설정으로 이루어지고, 데이터는 서비스에서 사용할 임의의 정적 데이터로 구성됩니다. 이 계층형 애플리케이션 모델의 각 구성 요소를 독립적으로 버전 지정 및 업그레이드할 수 있습니다.

![서비스 패브릭 애플리케이션 모델][appmodel-diagram]

애플리케이션 유형은 애플리케이션에 대한 분류이며 여러 서비스 유형으로 구성됩니다. 서비스 유형은 서비스에 대한 분류입니다. 분류에는 다양한 설정과 구성이 포함될 수 있지만 핵심 기능은 동일하게 유지됩니다 서비스 인스턴스는 같은 서비스 유형의 다른 서비스 구성 변형입니다.  

애플리케이션 및 서비스의 클래스(또는 "형식")는 XML 파일(애플리케이션 매니페스트 및 서비스 매니페스트)을 통해 설명됩니다.  매니페스트는 애플리케이션 및 서비스에 대해 설명하며 클러스터의 이미지 저장소에서 대상 애플리케이션을 인스턴스화할 수 있는 템플릿입니다.  매니페스트는 [애플리케이션 및 서비스 매니페스트](service-fabric-application-and-service-manifests.md)에서 자세히 다룹니다. ServiceManifest.xml 및 ApplicationManifest.xml에 대한 스키마 정의는 Service Fabric SDK 및 도구와 함께 *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*에 설치됩니다. XML 스키마는 [ServiceFabricServiceModel.xsd 스키마 설명서](service-fabric-service-model-schema.md)에 설명되어 있습니다.

서로 다른 애플리케이션 인스턴스에 대한 코드는 동일한 Service Fabric 노드에 의해 호스팅되는 경우에도 별도의 프로세스로 실행됩니다. 뿐만 아니라 각 애플리케이션 인스턴스의 수명 주기를 독립적으로 관리(예: 업그레이드)할 수 있습니다. 다음 다이어그램에서는 애플리케이션 유형이 어떻게 서비스 유형으로 구성되고, 다시 서비스 유형이 코드, 구성 및 데이터 패키지로 구성되는지를 보여줍니다. 다이어그램을 간소화하려면 `ServiceType4`에 대한 코드/구성/데이터 패키지만을 표시하지만 각 서비스 형식은 패키지 형식의 일부 또는 모두를 포함합니다.

![Service Fabric 애플리케이션 유형 및 서비스 유형][cluster-imagestore-apptypes]

클러스터에 활성 상태의 서비스 유형 인스턴스가 하나 이상 있을 수 있습니다. 예를 들어 상태 저장 서비스 인스턴스 또는 복제본의 경우 클러스터의 서로 다른 노드에 있는 복제본 간에 상태를 복제하여 높은 안정성을 달성합니다. 복제는 클러스터의 한 노드에 장애가 발생해도 서비스가 제공되도록 중복성을 제공합니다. [분할된 서비스](service-fabric-concepts-partitioning.md) 는 클러스터의 노드에서 상태(및 해당 상태에 대한 액세스 패턴)를 더욱 세분화합니다.

다음 다이어그램에서는 애플리케이션 및 서비스 인스턴스, 파티션, 복제본 간의 관계를 보여 줍니다.

![서비스 내의 파티션 및 복제본][cluster-application-instances]

> [!TIP]
> http://&lt;yourclusteraddress&gt;:19080/Explorer에서 제공되는 Service Fabric Explorer 도구를 사용하여 클러스터에서 애플리케이션의 레이아웃을 볼 수 있습니다. 자세한 내용은 [Service Fabric Explorer를 사용하여 클러스터 시각화](service-fabric-visualizing-your-cluster.md)를 참조하세요.
> 
> 


## <a name="next-steps"></a>다음 단계
- [애플리케이션 확장성](service-fabric-concepts-scalability.md)에 대해 알아봅니다.
- 서비스 [상태](service-fabric-concepts-state.md), [분할](service-fabric-concepts-partitioning.md) 및 [가용성](service-fabric-availability-services.md)에 대해 알아봅니다.
- [애플리케이션 및 서비스 매니페스트](service-fabric-application-and-service-manifests.md)에 애플리케이션 및 서비스가 어떻게 정의되어 있는지 읽어 봅니다.
- [애플리케이션 호스팅 모델](service-fabric-hosting-model.md)에서는 배포된 서비스 및 서비스 호스트 프로세스 복제본(또는 인스턴스) 간의 관계에 대해 설명합니다.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


