---
title: Azure Service Fabric 리소스 모델 소개 | Microsoft Docs
description: Service Fabric Mesh 애플리케이션을 정의하는 간단한 방법인 Service Fabric 리소스 모델에 대해 알아봅니다.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810760"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Service Fabric 리소스 모델 소개

Service Fabric 리소스 모델에서는 Service Fabric Mesh 애플리케이션을 구성하는 리소스를 정의하는 간단한 방법을 설명합니다. 개별 리소스는 모든 Service Fabric 환경에 배포할 수 있습니다.  Service Fabric 리소스 모델은 Azure Resource Manager 모델과도 호환됩니다. 다음 형식의 리소스가 현재 이 모델에서 지원됩니다.

- 애플리케이션 및 서비스
- 네트워크
- 게이트웨이
- 비밀 및 비밀/값
- 볼륨

각 리소스는 Mesh 애플리케이션을 설명하는 간단한 YAML 또는 JSON 문서이며 Service Fabric 플랫폼에서 제공되는 리소스 파일에서 선언적으로 설명되어 있습니다.

## <a name="applications-and-services"></a>애플리케이션 및 서비스

애플리케이션 리소스는 배포 단위, 버전 관리 및 Mesh 애플리케이션의 수명입니다. 마이크로서비스를 나타내는 하나 이상의 서비스 리소스로 구성됩니다. 따라서 각 서비스 리소스는 코드 패키지와 관련된 컨테이너 이미지를 실행하는 데 필요한 모든 항목을 설명하는 하나 이상의 코드 패키지로 구성됩니다.

![앱 및 서비스][Image1]

서비스 리소스는 다음을 선언합니다.

- 컨테이너 이름, 버전 및 레지스트리
- 각 컨테이너에 대한 필요한 CPU 및 메모리 리소스
- 네트워크 엔드포인트
- 네트워크, 볼륨 및 비밀과 같은 다른 리소스에 대한 참조 

서비스 리소스의 일부로 정의된 모든 코드 패키지를 배포하고 그룹으로 함께 활성화합니다. 또한 서비스 리소스는 실행할 서비스의 인스턴스 개수를 설명하고 사용하는 기타 리소스(예: 네트워크 리소스)도 참조합니다.

Mesh 애플리케이션이 둘 이상의 서비스로 구성되면 동일한 노드에서 함께 실행하도록 보장되지 않습니다. 또한 애플리케이션을 업그레이드하는 동안 단일 서비스를 업그레이드하는 오류가 발생하면 모든 서비스가 이전 버전으로 롤백됩니다.

앞에서 언급한 대로 각 애플리케이션 인스턴스의 수명 주기를 독립적으로 관리할 수 있습니다. 예를 들어 하나의 애플리케이션 인스턴스는 다른 애플리케이션 인스턴스와 독립적으로 업그레이드될 수 있습니다. 일반적으로 애플리케이션에서 서비스 수를 적게 유지하고 애플리케이션에 서비스가 많을수록 서비스를 독립적으로 관리하기 어렵게 됩니다.

## <a name="networks"></a>네트워크

네트워크 리소스는 개별적으로 배포 가능한 리소스이며 해당 종속성으로 참조할 수 있는 애플리케이션 또는 서비스 리소스와 독립적입니다. 애플리케이션에 대한 네트워크를 만드는 데 사용됩니다. 다른 애플리케이션의 여러 서비스는 동일한 네트워크의 일부일 수 있습니다.  자세한 내용은 [Service Fabric Mesh 애플리케이션에서 네트워킹](service-fabric-mesh-networks-and-gateways.md)을 참조하세요.

> [!NOTE]
> 현재 미리 보기는 애플리케이션과 네트워크 간의 일대일 매핑만을 지원합니다

![네트워크 및 게이트웨이][Image2]

## <a name="gateways"></a>게이트웨이
게이트웨이 리소스는 두 네트워크를 연결하고 트래픽을 라우팅합니다.  게이트웨이는 서비스가 외부 클라이언트와 통신할 수 있도록 허용하고 서비스로의 수신을 제공합니다.  게이트웨이는 Mesh 애플리케이션을 사용자 고유의 기존 가상 네트워크와 연결하는 데에도 사용할 수 있습니다. 자세한 내용은 [Service Fabric Mesh 애플리케이션에서 네트워킹](service-fabric-mesh-networks-and-gateways.md)을 참조하세요.

![네트워크 및 게이트웨이][Image2]

## <a name="secrets"></a>비밀

비밀 리소스는 배포 가능하며 해당 종속성으로 참조할 수 있는 애플리케이션 또는 서비스 리소스와 독립적입니다. 비밀을 애플리케이션에 안전하게 제공하는 데 사용됩니다. 서로 다른 애플리케이션의 여러 서비스가 동일한 비밀의 값을 참조할 수 있습니다.

## <a name="volumes"></a>볼륨

컨테이너는 종종 임시 디스크를 제공합니다. 그러나 임시 디스크는 사용 후 삭제되므로 컨테이너가 충돌하면 새 임시 디스크를 가져와 정보를 잃게 됩니다. 임시 디스크에 대한 정보를 다른 컨테이너와 공유하는 것도 어렵습니다. 볼륨은 상태를 유지하는 데 사용할 수 있는 컨테이너 인스턴스 내에 탑재된 디렉터리입니다. 볼륨은 범용 파일 스토리지를 제공하며 이를 통해 일반 디스크 I/O 파일 API를 사용하여 파일을 읽고 쓸 수 있습니다. 볼륨 리소스는 디렉터리를 탑재하는 방법 및 이에 대한 지원 스토리지를 설명하는 선언적 방법입니다(Azure Files 볼륨 또는 Service Fabric 신뢰할 수 있는 볼륨).  자세한 내용은 [상태 저장](service-fabric-mesh-storing-state.md#volumes)을 참조하세요.

![볼륨][Image3]

## <a name="programming-models"></a>프로그래밍 모델
서비스 리소스는 컨테이너 이미지를 실행해야 합니다. 이것은 리소스와 연결된 코드 패키지에서 참조됩니다. Service Fabric Mesh 특정 API를 이해하거나 사용하지 않고도 컨테이너 내에서 프레임워크를 사용하여 모든 언어로 작성된 코드를 실행할 수 있습니다. 

애플리케이션 코드가 Service Fabric Mesh 외부에서도 이동 가능한 상태를 유지하고 애플리케이션 배포가 서비스를 구현하는 데 사용되는 언어 또는 프레임워크에 관계 없이 일관성을 유지합니다. 애플리케이션이 ASP.NET Core, Go 또는 프로세스 및 스크립트 집합인지에 관계 없이 Service Fabric Mesh 리소스 배포 모델은 동일하게 유지됩니다. 

## <a name="packaging-and-deployment"></a>패키징 및 배포

리소스 모델을 기반으로 하는 Service Fabric Mesh 애플리케이션은 Docker 컨테이너로 패키징됩니다.  Service Fabric Mesh는 공유되는 다중 테넌트 환경이며 컨테이너는 높은 수준의 격리를 제공합니다.  이러한 애플리케이션은 JSON 형식 또는 YAML 형식(이후 JSON으로 변환됨)을 사용하여 설명됩니다. Azure Service Fabric 메시 망상 조직 응용 프로그램을 배포 하는 경우 응용 프로그램에 설명 하는 데 JSON은는 Azure Resource Manager 템플릿입니다. 리소스는 Azure 리소스에 매핑됩니다.  망상 조직 응용 프로그램을 Service Fabric 클러스터를 배포 하는 경우 (독립 실행형 또는 Azure 호스팅), 응용 프로그램에 설명 하는 데 JSON은 Azure Resource Manager 템플릿과 유사 합니다.  배포가 완료되면 Mesh 애플리케이션은 HTTP 인터페이스 또는 Azure CLI를 통해 관리할 수 있습니다. 


## <a name="next-steps"></a>다음 단계 
Service Fabric Mesh에 대한 자세한 내용은 다음 개요를 참조하세요.
- [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
