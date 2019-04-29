---
title: Azure Service Fabric Mesh 용어 | Microsoft Docs
description: Azure Service Fabric Mesh의 일반적인 용어에 대해 알아봅니다.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 2d2661593ba3d9be2755d81803c8e248a2f7d0e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810595"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh 용어

Azure Service Fabric Mesh는 개발자가 가상 머신, 저장소 또는 네트워킹을 관리하지 않고 마이크로 서비스 애플리케이션을 배포할 수 있는 완전히 관리되는 서비스입니다. 이 문서에서는 문서에 사용된 용어를 이해할 수 있도록 Azure Service Fabric Mesh에서 사용되는 용어를 자세히 설명합니다.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/)은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 오픈 소스 시스템 플랫폼입니다. Service Fabric은 Service Fabric Mesh를 작동하는 오케스트레이터입니다. Service Fabric은 마이크로 서비스 애플리케이션을 빌드하고 실행할 수 있는 방법에 대한 옵션을 제공합니다. 임의 프레임워크를 사용하여 서비스를 작성하고, 여러 환경 선택 항목 중에서 애플리케이션을 실행할 위치를 선택할 수 있습니다.

## <a name="application-and-service-concepts"></a>애플리케이션 및 서비스 개념

**Service Fabric Mesh 애플리케이션**: Service Fabric Mesh 애플리케이션은 [리소스 모델](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)(YAML 및 JSON 리소스 파일)에 의해 설명되며 Service Fabric이 실행되는 모든 환경에 배포될 수 있습니다.

**Service Fabric 네이티브 애플리케이션**: Service Fabric 네이티브 애플리케이션은 [네이티브 애플리케이션 모델](/azure/service-fabric/service-fabric-application-model)(XML 기반 애플리케이션 및 서비스 매니페스트)에 의해 설명됩니다.  Service Fabric 네이티브 애플리케이션은 Service Fabric Mesh에서 실행할 수 없습니다.

**애플리케이션**: Service Fabric Mesh 애플리케이션은 Mesh 애플리케이션 배포, 버전 관리 및 수명의 단위입니다. 각 애플리케이션 인스턴스의 수명 주기를 독립적으로 관리할 수 있습니다.  애플리케이션은 하나 이상의 서비스 코드 패키지 및 설정으로 구성됩니다. 애플리케이션은 Azure RM(Resource Model) 스키마를 사용하여 정의됩니다.  서비스는 RM 템플릿의 애플리케이션 리소스 속성으로 설명됩니다.  애플리케이션이 사용하는 네트워크 및 볼륨이 애플리케이션에서 참조됩니다.  애플리케이션을 만들면 애플리케이션, 서비스, 네트워크 및 볼륨이 Service Fabric 리소스 모델을 사용하여 모델링됩니다.

**서비스**: 애플리케이션의 서비스는 마이크로서비스를 나타내고 완전한 독립 실행형 기능을 수행합니다. 각 서비스는 코드 패키지와 관련된 컨테이너 이미지를 실행하는 데 필요한 모든 항목을 설명하는 하나 이상의 코드 패키지로 구성됩니다.  애플리케이션의 서비스 복제 수를 확대 및 축소할 수 있습니다.

**코드 패키지**: 코드 패키지는 다음을 포함하여 코드 패키지와 연결된 컨테이너 이미지를 실행하는 데 필요한 모든 것을 설명합니다.

* 컨테이너 이름, 버전 및 레지스트리
* 각 컨테이너에 대한 필요한 CPU 및 메모리 리소스
* 네트워크 엔드포인트
* 별도 볼륨 리소스를 참조하는 컨테이너에 탑재할 볼륨

## <a name="deployment-and-application-models"></a>배포 및 애플리케이션 모델 

서비스를 배포하려면 어떻게 실행할지를 설명해야 합니다. Service Fabric은 다음 세 가지 배포 모델을 지원합니다.

### <a name="resource-model"></a>리소스 모델
Service Fabric 리소스는 애플리케이션, 서비스, 네트워크 및 볼륨을 포함하여 Service Fabric에 개별적으로 배포할 수 있는 모든 항목입니다. 리소스는 클러스터 엔드포인트에 배포할 수 있는 JSON 파일을 사용하여 정의됩니다.  Service Fabric Mesh의 경우 Azure 리소스 모델 스키마가 사용됩니다. 또한, YAML 파일 스키마를 사용하여 좀 더 쉽게 정의 파일을 작성할 수도 있습니다. Service Fabric이 실행되기만 하면 어디든지 리소스를 배포할 수 있습니다. 리소스 모델은 Service Fabric 애플리케이션을 설명하는 가장 간단한 방법입니다. 주로 컨테이너화된 서비스의 간단한 배포 및 관리에 중점을 둡니다. 자세한 내용은 [Service Fabric 리소스 모델 소개](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources)를 참조하세요.

### <a name="native-model"></a>네이티브 모델
네이티브 애플리케이션 모델은 Service Fabric에 대한 모든 하위 수준 액세스를 애플리케이션에 제공합니다. 애플리케이션 및 서비스는 XML 매니페스트 파일에서 등록된 유형으로 정의됩니다.

네이티브 모델은 C# 및 Java로 작성된 Service Fabric 런타임 API 및 클러스터 관리 API에 대한 액세스를 제공하는 Reliable Services 프레임워크를 지원합니다. 네이티브 모델은 임의 컨테이너 및 실행 파일도 지원합니다.

Service Fabric Mesh 환경에서는 네이티브 모델이 지원되지 않습니다.  자세한 내용은 [프로그래밍 모델 개요](/azure/service-fabric/service-fabric-choose-framework)를 참조하세요.

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/)는 Docker 프로젝트의 일부입니다. Service Fabric은 Docker Compose 모델을 사용한 애플리케이션 배포를 제한적으로 지원합니다.

## <a name="environments"></a>환경

Service Fabric은 여러 서비스 및 제품의 기초가 되는 오픈 소스 플랫폼 기술입니다. Microsoft에서는 다음과 같은 옵션을 제공합니다.

 - **Service Fabric Mesh**: Microsoft Azure에서 Service Fabric 애플리케이션을 실행하기 위한 완전히 관리되는 서비스입니다.
 - **Azure Service Fabric**: Azure 호스팅 Service Fabric 클러스터 제품입니다. Service Fabric 클러스터의 업그레이드 및 구성 관리와 함께 Service Fabric과 Azure 인프라 간의 통합을 제공합니다.
 - **Service Fabric 독립 실행형**: [임의 위치(온-프레미스 또는 임의 클라우드 공급 기업)에 Service Fabric 클러스터를 배포](/azure/service-fabric/service-fabric-deploy-anywhere)하기 위한 설치 및 구성 도구 세트입니다. Azure에서 관리되지 않습니다.
 - **Service Fabric 개발 클러스터**: Service Fabric 애플리케이션 개발을 위해 Windows, Linux 또는 Mac에서 로컬 개발 환경을 제공합니다.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>환경, 프레임워크 및 배포 모델 지원 매트릭스
프레임워크 및 배포 모델에 대한 지원 수준은 환경마다 다릅니다. 다음 표에서는 지원되는 프레임워크 및 배포 모델 조합에 대해 설명합니다.

| 애플리케이션 유형 | 설명 방법 | Azure Service Fabric Mesh | Azure Service Fabric 클러스터(모든 OS)| 로컬 클러스터 | 독립 실행형 클러스터 |
|---|---|---|---|---|---|
| Service Fabric Mesh 애플리케이션 | 리소스 모델(YAML 및 JSON) | 지원됨 |지원되지 않음 | Windows - 지원됨, Linux 및 Mac - 지원되지 않음 | Windows - 지원되지 않음 |
|Service Fabric 네이티브 애플리케이션 | 네이티브 애플리케이션 모델(XML) | 지원되지 않음| 지원됨|지원됨|Windows - 지원됨|

다음 표에서는 다양한 애플리케이션 모델 및 Service Fabric에 대한 애플리케이션 모델의 도구에 대해 설명합니다.

| 애플리케이션 유형 | 설명 방법 | Visual Studio | (예: Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric Mesh 애플리케이션 | 리소스 모델(YAML 및 JSON) | VS 2017 |지원되지 않음 |지원되지 않음 | 지원됨 - Mesh 환경만 | 지원되지 않음|
|Service Fabric 네이티브 애플리케이션 | 네이티브 애플리케이션 모델(XML) | VS 2017 및 VS 2015| 지원됨|지원됨|지원됨|지원됨|

## <a name="next-steps"></a>다음 단계

Service Fabric Mesh에 대한 자세한 내용은 [Service Fabric Mesh 개요](service-fabric-mesh-overview.md)를 참조하세요.

[일반적인 질문](service-fabric-mesh-faq.md)에 대한 답변을 찾습니다.
