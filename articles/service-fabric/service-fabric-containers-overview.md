---
title: Service Fabric 및 컨테이너 개요 | Microsoft Docs
description: Service Fabric 및 마이크로 서비스 애플리케이션 배포를 위한 컨테이너 사용에 대한 개요. 이 문서는 컨테이너를 사용하는 방법과 Service Fabric에서 사용할 수 있는 기능에 대한 개요를 제공합니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/8/2018
ms.author: aljo
ms.openlocfilehash: 5a45f14e5ac1da5152f320bd92b1ebb42be1d214
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60881427"
---
# <a name="service-fabric-and-containers"></a>Service Fabric 및 컨테이너

## <a name="introduction"></a>소개

Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 관리하도록 배포된 시스템 플랫폼입니다.

Service Fabric은 머신 클러스터에 마이크로 서비스를 배포하기 위한 Microsoft의 [컨테이너 조정자](service-fabric-cluster-resource-manager-introduction.md)입니다. Service Fabric은 Microsoft에서 대규모로 서비스를 실행하는 기간 동안 학습한 단원을 활용합니다.

마이크로 서비스는 [Service Fabric 프로그래밍 모델](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) 사용부터 [선택한 코드](service-fabric-guest-executables-introduction.md) 배포에 이르는 다양한 방식으로 개발될 수 있습니다. 또는 [컨테이너를 배포 및 관리](service-fabric-containers-overview.md)하려는 경우 Service Fabric이 가장 적합합니다.

기본적으로 Service Fabric은 이러한 서비스를 프로세스로 배포하고 활성화합니다. 프로세스는 클러스터 내에서 리소스의 최고밀도 사용과 가장 신속한 활성화를 제공합니다. Service Fabric도 컨테이너 이미지에 서비스를 배포할 수 있습니다. 동일한 애플리케이션에서 프로세스의 서비스와 컨테이너의 서비스를 혼합할 수 있습니다.

지금 바로 Service Fabric에서 컨테이너를 시험해보려면 빠른 시작, 자습서 또는 샘플을 사용합니다.  

[빠른 시작: Service Fabric Linux 컨테이너 응용 프로그램 배포](service-fabric-quickstart-containers-linux.md)  
[빠른 시작: Service Fabric Windows 컨테이너 응용 프로그램 배포](service-fabric-quickstart-containers.md)  
[기존 .NET 앱 컨테이너화](service-fabric-host-app-in-a-container.md)  
[Service Fabric 컨테이너 샘플](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>컨테이너란?

컨테이너는 실행할 애플리케이션에 대한 변경이 불가능한 환경을 제공하여 안정적으로 다양한 컴퓨팅 환경에서 애플리케이션을 실행하는 문제를 해결합니다. 컨테이너는 컨테이너 내에서 소프트웨어를 실행하는 데 필요한 모든 것이 포함된 자체 격리된 '상자'에 라이브러리 및 구성 파일 같은 애플리케이션과 해당 종속성 모두를 래핑합니다. 컨테이너가 어디에서 실행되든 컨테이너 내부의 애플리케이션에는 올바른 버전의 해당 종속 라이브러리, 모든 구성 파일 및 실행에 필요한 다른 항목 등 실행에 필요한 모든 항목이 항상 포함돼 있습니다.

컨테이너는 커널 바로 위에서 실행되며 파일 시스템 및 기타 리소스에 대한 격리된 뷰를 갖습니다. 컨테이너의 애플리케이션에는 다른 애플리케이션 또는 해당 컨테이너의 외부 프로세스에 대한 정보가 없습니다. 각 애플리케이션, 해당 런타임, 종속성, 시스템 라이브러리는 운영 체제의 자체적으로 격리된 뷰에 대해 전체, 개인 액세스 권한이 있는 컨테이너 내에서 실행됩니다. 다양한 컴퓨팅 환경에서 실행하는 데 필요한 모든 애플리케이션의 종속성을 쉽게 제공하는 것 외에 Service Fabric에서 컨테이너를 사용하며, 그렇지 않은 경우 프로세스에서 서비스를 실행하는 이점 중 중요한 것은 보안 및 리소스 격리입니다.

가상 머신에 비해 컨테이너는 다음과 같은 이점이 있습니다.

* **소형**: 컨테이너는 효율성을 높이기 위해 단일 저장소 공간, 레이어 버전 및 업데이트를 사용 합니다.
* **빠른**: 컨테이너를 시작할 수 있도록 훨씬 더 빠르게-일반적으로 초에서 전체 운영 체제를 부팅할 필요가 없습니다.
* **이식성**: 컨테이너 화 된 응용 프로그램 이미지는 클라우드, 온-프레미스로 가상 컴퓨터 내에서 또는 실제 컴퓨터에서 직접 실행에 이식할 수 있습니다.
* **리소스 거 버 넌 스**: 컨테이너는 호스트에서 사용할 수 있는 물리적 리소스를 제한할 수 있습니다.

### <a name="container-types-and-supported-environments"></a>컨테이너 유형 및 지원되는 환경

Service Fabric은 Linux와 Windows 모두에서 컨테이너를 지원하고 Windows의 Hyper-V 격리 모드를 지원합니다.

#### <a name="docker-containers-on-linux"></a>Linux의 Docker 컨테이너

Docker는 API를 제공하여 Linux 커널 컨테이너 위에서 컨테이너를 만들고 관리합니다. Docker 허브는 컨테이너 이미지를 저장하고 검색하기 위한 중앙 리포지토리를 제공합니다.
Linux 기반 자습서는 [Linux에서 첫 번째 Service Fabric 컨테이너 애플리케이션 만들기](service-fabric-get-started-containers-linux.md)를 참조하세요.

#### <a name="windows-server-containers"></a>Windows Server 컨테이너

Windows Server 2016은 격리 수준이 다른 두 가지 유형의 컨테이너를 제공합니다. Windows Server 컨테이너와 Docker 컨테이너는 둘 다 네임스페이스와 파일 시스템 격리가 있다는 점에서 유사하지만 이들 컨테이너가 실행되는 호스트와 커널을 공유합니다. Linux에서, 이러한 격리는 일반적으로 cgroups 및 네임스페이스에 의해 제공되었으며 Windows Server 컨테이너도 유사하게 작동합니다.

Hyper-V 지원을 사용하는 Windows 컨테이너는 컨테이너가 다른 컨테이너 또는 호스트와 운영 체제 커널을 공유하지 않기 때문에 격리 및 보안이 강화됩니다. 이렇게 보안 격리 수준이 높은 Hyper-V 사용 컨테이너는 잠재적으로 적대적인 다중 테넌트 시나리오를 대상으로 합니다.
Windows 기반 자습서는 [ Windows에서 첫 번째 Service Fabric 컨테이너 애플리케이션 만들기](service-fabric-get-started-containers.md)를 참조하세요.

다음 그림은 사용 가능한 다양한 유형의 가상화 및 격리 수준을 보여줍니다.
![Service Fabric 플랫폼][Image1]

## <a name="scenarios-for-using-containers"></a>컨테이너 사용 시나리오

다음은 컨테이너가 적합한 일반적인 예입니다.

* **IIS 리프트 앤 시프트**: 기존 넣을 수 있습니다 [ASP.NET MVC](https://www.asp.net/mvc) 마이그레이션하는 대신 컨테이너에서 앱이 ASP.NET Core로 합니다. 이러한 ASP.NET MVC 앱은 IIS(인터넷 정보 서비스)에 따라 다릅니다. 이러한 애플리케이션을 미리 만든 IIS 이미지의 컨테이너 이미지로 패키징하고 Service Fabric을 사용하여 배포할 수 있습니다. Windows 컨테이너에 대한 자세한 내용은 [Windows Server의 컨테이너 이미지](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)를 참조하세요.

* **컨테이너 및 Service Fabric 마이크로 서비스 혼합**: 기존 컨테이너 이미지를 애플리케이션의 일부에 사용합니다. 예를 들어, 애플리케이션의 웹 프런트 엔드에 [NGINX 컨테이너](https://hub.docker.com/_/nginx/) 를 사용하고 상태 저장 서비스를 보다 집약적인 백 엔드 계산에 사용합니다.

* **"시끄러운 이웃" 서비스의 영향을 줄이는**: 서비스 호스트에서 사용 하는 리소스를 제한 하려면 컨테이너의 리소스 거 버 넌 스 기능을 사용할 수 있습니다. 서비스는 많은 리소스를 소비하고 다른 서비스의 성능에 영향을 미칠 수 있는 경우(예: 장기적으로 실행되는 쿼리 같은 연산), 리소스 관리를 사용하는 컨테이너에 이 서비스를 넣는 것이 좋습니다.

## <a name="service-fabric-support-for-containers"></a>컨테이너에 대한 Service Fabric 지원

Service Fabric은 Linux에서 Docker 컨테이너의 배포를 지원하고 Hyper-V 격리 모드에 대한 지원과 함께 Windows Server 2016에서 Windows Server 컨테이너의 배포를 지원합니다. 

Service Fabric은 컨테이너가 다수의 서비스 복제본이 배치되는 애플리케이션 호스트를 나타내는 [애플리케이션 모델](service-fabric-application-model.md)을 제공합니다. 또한 Service Fabric은 기본 제공 Service Fabric 프로그래밍 모델을 사용하지 않고 대신 모든 언어 또는 프레임워크를 사용하여 컨테이너 내에서 작성된 기존 애플리케이션을 패키지하는 [게스트 실행 파일 시나리오](service-fabric-guest-executables-introduction.md)를 지원합니다. 이 시나리오는 컨테이너에 대한 일반 사용 사례입니다.

[컨테이너 내에서 Service Fabric 서비스](service-fabric-services-inside-containers.md)를 실행할 수도 있습니다. 컨테이너 내에서 Service Fabric 서비스 실행에 대한 지원은 현재 제한됩니다.

Service Fabric은 다음과 같은 컨테이너화된 마이크로 서비스로 구성된 애플리케이션을 빌드하는 데 도움이 되는 몇 가지 컨테이너 기능을 제공합니다.

* 컨테이너 이미지 배포 및 활성화
* Azure 클러스터에서 리소스 값 설정을 기본적으로 포함하는 리소스 관리
* 리포지토리 인증
* 컨테이너 포트와 호스트 포트 간 매핑
* 컨테이너 간 검색 및 통신
* 환경 변수를 구성하고 설정할 수 있는 기능
* 컨테이너에 보안 자격 증명을 설정하는 기능
* 컨테이너에 대한 다른 네트워킹 모드의 선택

Azure Kubernetes Service를 사용하여 Kubernetes 클러스터 만드는 방법, Azure Container Registry에서 개인 Docker 레지스트리 만드는 방법 등 Azure에서 컨테이너 지원에 대한 포괄적인 개요는 [컨테이너용 Azure](https://docs.microsoft.com/azure/containers/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Service Fabric이 컨테이너를 실행하기 위해 제공하는 지원에 대해 알아보았습니다. 다음으로, 컨테이너를 사용하는 방법을 보여주는 각 기능의 사례를 살펴보겠습니다.

[Linux에서 첫 번째 Service Fabric 컨테이너 애플리케이션 만들기](service-fabric-get-started-containers-linux.md)  
[Windows에서 첫 번째 Service Fabric 컨테이너 애플리케이션 만들기](service-fabric-get-started-containers.md)  
[Windows 컨테이너에 대한 자세한 내용](https://docs.microsoft.com/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png