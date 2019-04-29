---
title: Azure Service Fabric 애플리케이션 보안에 대해 자세히 알아보기 | Microsoft Docs
description: Service Fabric에서 마이크로 서비스 애플리케이션을 안전하게 실행하는 방법의 개요 다른 보안 계정에서 서비스 및 시작 스크립트를 실행하고, 사용자를 인증하고 권한을 부여하고, 애플리케이션 암호를 관리하고, 서비스 통신의 보안을 유지하고, API 게이트웨이를 사용하고, 미사용 애플리케이션 데이터의 보안을 유지하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: aljo
ms.openlocfilehash: b4d3699c0327bb2771a358d3e3c2921bdc39ee5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621563"
---
# <a name="service-fabric-application-and-service-security"></a>Service Fabric 애플리케이션 및 서비스 보안
마이크로 서비스 아키텍처는 [많은 이점](service-fabric-overview-microservices.md)을 제공할 수 있습니다. 그러나 마이크로 서비스의 보안을 관리하는 일은 기존의 모놀리식 애플리케이션 보안을 관리하는 것과는 다르며 어려울 수 있습니다. 

모놀리식을 사용할 경우 애플리케이션은 일반적으로 네트워크 내에 있는 하나 이상의 서버에서 실행되며, 노출된 포트와 API 및 IP 주소를 식별하는 것이 더 쉽습니다. 종종 하나의 경계 네트워크와 하나의 데이터베이스만 보호하면 될 수 있습니다. 시스템이 보안 위반이나 공격으로 인해 손상되면 시스템 내의 모든 항목을 공격자가 사용할 수 있게 됩니다. 마이크로 서비스를 사용하면 시스템은 좀 더 복잡해집니다.  서비스는 많은 호스트에 걸쳐 분산되고, 호스트 간에 마이그레이션됩니다.  적절한 보안을 사용하여 하나의 서비스를 손상시킴으로써 공격자가 얻을 수 있는 권한과 단일 공격으로 사용할 수 있는 데이터 양을 제한할 있습니다.  통신은 내부적으로 진행되지 않고 네트워크를 통해 발생하며, 서비스 간에 많은 포트 및 상호 작용이 노출됩니다. 이러한 서비스 상호 작용과 발생 시기를 아는 것은 애플리케이션 보안에 매우 중요합니다.

이 문서는 마이크로 서비스 보안에 대한 지침서가 아니며, 이러한 많은 리소스를 온라인으로 사용할 수 있지만, Service Fabric에서 이행될 수 있는 보안의 다양한 측면을 설명합니다.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여
종종 서비스에 의해 노출되는 리소스 및 API를 신뢰할 수 있는 특정 사용자 또는 클라이언트로 제한하는 것도 필요합니다. 인증은 사용자의 ID를 안정적으로 확인하는 프로세스입니다.  권한 부여는 API 또는 서비스를 인증된 일부 사용자만 사용할 수 있게 하는 프로세스입니다.

### <a name="authentication"></a>인증
API 수준 신뢰를 결정하는 첫 번째 단계는 인증입니다. 인증은 사용자의 ID를 안정적으로 확인하는 프로세스입니다.  마이크로 서비스 시나리오에서 인증은 일반적으로 중앙에서 처리됩니다. API 게이트웨이를 사용하는 경우 게이트웨이로 [인증을 오프로드](/azure/architecture/patterns/gateway-offloading)할 수 있습니다. 이 방법을 사용하는 경우 메시지가 게이트웨이에서 온 것인지를 확인하기 위한 추가 보안 과정이 없으면 개별 서비스에 직접(API 게이트웨이 없이) 연결될 수 없도록 합니다.

서비스에 직접 액세스할 수 있는 경우 Azure Active Directory 또는 STS(보안 토큰 서비스)로 작동하는 전용 인증 마이크로 서비스와 같은 인증 서비스를 사용하여 사용자를 인증할 수 있습니다. 신뢰 결정은 보안 토큰 또는 쿠키와 함께 서비스 간에 공유됩니다. 

ASP.NET Core의 경우 [사용자를 인증](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/)하는 기본 메커니즘이 ASP.NET Core ID 멤버 자격 시스템입니다. ASP.NET Core ID는 개발자가 구성한 데이터 저장소에 사용자 정보(로그인 정보, 역할 및 클레임 포함)를 저장합니다. ASP.NET Core ID는 2단계 인증을 지원합니다.  외부 인증 공급자도 지원되므로, 사용자는 Microsoft, Google, Facebook 또는 Twitter와 같은 공급자의 기존 인증 프로세스를 사용하여 로그인할 수 있습니다. 

### <a name="authorization"></a>권한 부여
인증 후에, 서비스는 사용자 액세스를 허가하거나 사용자가 수행할 수 있는 작업을 결정해야 합니다. 이 프로세스를 통해 전체가 아닌 일부 인증된 사용자만 서비스의 API를 사용할 수 있게 됩니다. 권한 부여는 인증과 일치하는 부분도 있고 독립된 부분도 있으며, 사용자가 누군지 확인하는 프로세스입니다. 인증으로 현재 사용자에 대해 하나 이상의 ID가 만들어질 수 있습니다.

[ASP.NET Core 권한 부여](/dotnet/standard/microservices-architecture/secure-net-microservices-web-applications/authorization-net-microservices-web-applications)는 사용자의 역할 또는 사용자 지정 정책을 기준으로 수행될 수 있으며, 클레임 또는 기타 추론 검사가 포함될 수 있습니다.

## <a name="restrict-and-secure-access-using-an-api-gateway"></a>API 게이트웨이를 사용하여 액세스 제한 및 보안 유지
일반적으로 클라우드 애플리케이션에는 사용자, 장치 또는 기타 애플리케이션 수신을 위한 단일 지점을 제공하는 프런트 엔드 게이트웨이가 필요합니다. [API 게이트웨이](/azure/architecture/microservices/gateway)는 클라이언트와 서비스 간에 위치하며, 애플리케이션이 제공하는 모든 서비스에 대한 진입점이 됩니다. 역방향 프록시로 작동하면서 클라이언트에서 서비스로 요청을 라우팅합니다. 또한 인증, 권한 부여, SSL 종료 및 속도 제한과 같은 다양한 교차 작업을 수행할 수도 있습니다. 게이트웨이를 배포하지 않으면 클라이언트는 프런트 엔드 서비스로 직접 요청을 보내야 합니다.

Service Fabric에서 게이트웨이는 상태 비저장 서비스(예: [ASP.NET Core 애플리케이션](service-fabric-reliable-services-communication-aspnetcore.md)) 또는 트래픽을 수신하도록 설계된 다른 서비스(예: [Traefik](https://docs.traefik.io/), [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/) 또는 [Azure API Management](https://docs.microsoft.com/azure/api-management))일 수 있습니다.

API Management는 Service Fabric과 직접 통합되므로 다양한 라우팅 규칙 집합을 사용하여 백 엔드 Service Fabric 서비스에 API를 게시할 수 있습니다.  백 엔드 서비스에 대한 액세스 보안을 유지하거나, 제한을 사용하여 DOS 공격을 방지하거나, API 키, JWT 토큰, 인증서 및 기타 자격 증명을 확인할 수 있습니다. 자세히 알아보려면 [Service Fabric 및 API Management 개요](service-fabric-api-management-overview.md)를 읽어보세요.

## <a name="manage-application-secrets"></a>애플리케이션 비밀 관리
저장소 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다. 이 문서에서는 Azure Key Vault를 사용하여 키와 비밀을 관리합니다. 하지만 애플리케이션에서 비밀을 *사용* 하는 것은 클라우드 플랫폼에 구애를 받지 않으므로 그 어디에 호스트된 클러스터에도 애플리케이션을 배포할 수 있습니다.

[서비스 구성 패키지][config-package]를 통해 서비스 구성 설정을 관리하는 방법이 권장됩니다. 구성 패키지는 관리되는 상태 유효성 검사 및 자동 롤백을 사용하여 롤링 업그레이드를 통해 버전이 관리되며 업데이트할 수 있습니다. 이 방법은 전역 서비스 중단 가능성을 줄이기 때문에 기본 설정으로 사용됩니다. 암호화된 비밀도 마찬가지입니다. 서비스 패브릭에는 인증서 암호화를 사용하여 구성 패키지 Settings.xml 파일의 값을 암호화 및 해독하는 기본 기능이 포함되어 있습니다.

다음 다이어그램은 서비스 패브릭 애플리케이션에서 비밀 관리가 이루어지는 기본 흐름을 보여 줍니다.

![비밀 관리 개요][overview]

이 흐름은 주요 네 단계로 구성됩니다.

1. 데이터 암호화 인증서를 가져옵니다.
2. 클러스터에 인증서를 설치합니다.
3. 인증서를 사용하여 애플리케이션을 배포할 때 비밀 값을 암호화하여 서비스의 Settings.xml 구성 파일에 삽입합니다.
4. 동일한 암호화 인증서로 암호를 해독하여 Settings.xml 파일에서 암호화된 값을 읽습니다. 

[Azure Key Vault][key-vault-get-started]는 인증서에 대한 안전한 저장소 위치이자 Azure의 Service Fabric 클러스터에 설치된 인증서를 가져오는 수단으로 사용됩니다. Azure에 배포하지 않는 경우 서비스 패브릭 애플리케이션의 비밀을 관리하기 위해 주요 자격 증명 모음을 사용할 필요가 없습니다.

예제를 보려면 [애플리케이션 비밀 관리](service-fabric-application-secret-management.md)를 참조하세요.

## <a name="secure-the-hosting-environment"></a>호스팅 환경 보안 유지
Azure Service Fabric을 사용하여 다른 사용자 계정으로 클러스터에서 실행 중인 애플리케이션을 보호할 수 있습니다. 또한 Service Fabric으로 배포 시 파일, 디렉터리, 인증서 등과 같은 사용자 계정을 통해 애플리케이션에서 사용하는 리소스도 보호합니다. 따라서 공유되는 호스티드 환경에서도 서로 더욱 안전하게 애플리케이션을 실행할 수 있습니다.

애플리케이션 매니페스트는 서비스를 실행하고 리소스의 보안을 유지하는 데 필요한 보안 주체(사용자 및 그룹)를 선언합니다.  이러한 보안 주체는 실행, 엔드포인트 바인딩, 패키지 공유 또는 보안 액세스 정책 등에서 참조됩니다.  그런 후 애플리케이션 매니페스트의 **ServiceManifestImport** 섹션에서 서비스 리소스에 정책이 적용됩니다.

보안 주체를 선언할 때 각 그룹에 사용자를 한 명 이상 추가하여 한꺼번에 관리할 수 있도록 사용자 그룹을 정의하고 만들 수도 있습니다. 이 기능은 여러 서비스 진입점에 대한 사용자가 여러 명 있고 그 사용자들에게 그룹 수준에서 특정 공통 권한을 부여해야 하는 경우 유용합니다.

기본적으로 서비스 패브릭 애플리케이션은 Fabric.exe 프로세스가 실행하는 계정을 통해 실행됩니다. 또한 Service Fabric은 애플리케이션의 매니페스트 내에 지정된 로컬 사용자 계정 또는 로컬 시스템 계정을 통해 애플리케이션을 실행하는 기능을 제공합니다. 자세한 내용은 [로컬 사용자 계정 또는 로컬 시스템 계정으로 서비스 실행](service-fabric-application-runas-security.md)을 참조하세요.  [로컬 사용자 또는 시스템 계정으로 서비스 시작 스크립트를 실행](service-fabric-run-script-at-service-startup.md)할 수도 있습니다.

Windows 독립 실행형 클러스터에서 Service Fabric을 실행할 경우 [Active Directory 도메인 계정](service-fabric-run-service-as-ad-user-or-group.md) 또는 [그룹 관리 서비스 계정](service-fabric-run-service-as-gmsa.md)에서 서비스를 실행할 수 있습니다.

## <a name="secure-containers"></a>컨테이너 보안
Service Fabric은 컨테이너 내부에 Windows 또는 Linux 클러스터(5.7 이상 버전)의 노드에 설치된 인증서에 액세스하는 서비스의 메커니즘을 제공합니다. 이 PFX 인증서는 애플리케이션이나 서비스를 인증하거나 다른 서비스와 통신을 보호하는 데 사용될 수 있습니다. 자세한 내용은 [컨테이너에 인증서 가져오기](service-fabric-securing-containers.md)를 참조하세요.

또한 Service Fabric은 Windows 컨테이너에 gMSA(그룹 관리 서비스 계정)도 지원합니다. 자세한 내용은 [Windows 컨테이너용 gMSA 설정](service-fabric-setup-gmsa-for-windows-containers.md)을 참조하세요.

## <a name="secure-service-communication"></a>서비스 통신 보안 유지
서비스 패브릭에서 서비스는 일반적으로 여러 VM에 배포된 서비스 패브릭 클러스터의 임의 위치에서 실행됩니다. Service Fabric은 서비스 통신의 보안을 유지하기 위한 몇 가지 옵션을 제공합니다.

[ASP.NET Core 또는 Java](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) 웹 서비스에서 HTTPS 엔드포인트를 사용하도록 설정할 수 있습니다.

역방향 프록시와 서비스 간에 보안 연결을 설정하여 종단 간 보안 채널을 사용하도록 설정할 수 있습니다. 보안 서비스에 연결하는 것은 역방향 프록시가 HTTPS에서 수신하도록 구성된 경우에만 지원됩니다. 역방향 프록시 구성에 대한 내용은 [Azure Service Fabric의 역방향 프록시](service-fabric-reverseproxy.md)를 참조하세요.  [보안 서비스에 연결](service-fabric-reverseproxy-configure-secure-communication.md)에서는 역방향 프록시와 서비스 간에 보안 연결을 설정하는 방법을 설명합니다.

Reliable Services 애플리케이션 프레임워크는 보안을 향상시키는 데 사용할 수 있는 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다. 원격 서비스([C#](service-fabric-reliable-services-secure-communication.md) 또는 [Java](service-fabric-reliable-services-secure-communication-java.md))를 사용하거나 [WCF](service-fabric-reliable-services-secure-communication-wcf.md)를 사용하는 경우 보안을 향상시키는 방법을 알아봅니다.

## <a name="encrypt-application-data-at-rest"></a>미사용 애플리케이션 데이터 암호화
Azure에서 실행되는 Service Fabric 클러스터의 각 [노드 형식](service-fabric-cluster-nodetypes.md)은 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)을 통해 백업됩니다. Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터를 구성하는 확장 집합에 데이터 디스크를 연결할 수 있습니다.  서비스가 연결된 데이터 디스크에 데이터를 저장하는 경우 [해당 데이터 디스크를 암호화](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md)하여 애플리케이션 데이터를 보호할 수 있습니다.

<!--TO DO: Enable BitLocker on Windows standalone clusters?
TO DO: Encrypt disks on Linux clusters?-->


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계
* [서비스 시작 시 설치 스크립트 실행](service-fabric-run-script-at-service-startup.md)
* [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)
* [애플리케이션 배포](service-fabric-deploy-remove-applications.md)
* [클러스터 보안에 대해 알아보기](service-fabric-cluster-security.md)

<!-- Links -->
[key-vault-get-started]:../key-vault/key-vault-overview.md
[config-package]: service-fabric-application-and-service-manifests.md
[service-fabric-cluster-creation-via-arm]: service-fabric-cluster-creation-via-arm.md

<!-- Images -->
[overview]:./media/service-fabric-application-and-service-security/overview.png