---
title: Azure Service Fabric 보안 개요 | Microsoft Docs
description: 이 문서는 Azure Service Fabric 보안의 개요를 제공합니다.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.openlocfilehash: c5b5f80a43530fe6d0b90e65c3aef89a815157e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610898"
---
# <a name="azure-service-fabric-security-overview"></a>Azure Service Fabric 보안 개요
[Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. Service Fabric은 클라우드 애플리케이션 개발 및 관리에서 발생하는 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있도록 구현하는 데 집중할 수 있습니다.

이 문서는 Service Fabric 배포를 위한 보안 고려 사항에 대한 개요입니다.

## <a name="secure-your-cluster"></a>클러스터 보호
Azure Service Fabric은 컴퓨터 클러스터에 걸쳐있는 서비스를 오케스트레이션합니다. 프로덕션 작업을 실행 중일 때 권한 없는 사용자가 클러스터에 연결하지 못하도록 하여 클러스터의 보안을 유지해야 합니다. 보안이 되지 않은 클러스터를 만들 수 있지만, 이렇게 하면 익명 사용자가 클러스터에 연결할 수 있게 됩니다(공용 인터넷에 관리 엔드포인트가 노출될 경우).

독립 실행형 또는 Azure에서 실행되는 클러스터의 경우 고려해야 할 두 시나리오는 노드 간 보안 및 클라이언트-노드 보안입니다. 이러한 시나리오를 구현하기 위해 다양한 기술을 사용할 수 있습니다.

### <a name="node-to-node-security"></a>노드 간 보안
노드 간 보안은 클러스터의 VM 또는 컴퓨터 간 통신에 적용됩니다. 노드 간 보안을 통해 클러스터에 가입하도록 인증된 컴퓨터만 호스팅 애플리케이션 및 클러스터의 서비스에 참여할 수 있습니다.

Azure에서 실행되는 클러스터 또는 Windows에서 실행되는 독립 실행형 클러스터는 Windows Server 컴퓨터에 대한 [인증서 보안](https://msdn.microsoft.com/library/ff649801.aspx) 또는 [Windows 보안](https://msdn.microsoft.com/library/ff649396.aspx)을 사용할 수 있습니다.

#### <a name="node-to-node-certificate-security"></a>노드 간 인증서 보안

Service Fabric은 클러스터를 만들 때 지정하는 X.509 서버 인증서를 사용합니다. 인증서 정보 및 인증서 획득 또는 생성 방법에 대한 빠른 개요는 [인증서 작업](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates)을 참조하세요.

Azure Portal, Azure Resource Manager 템플릿 또는 독립 실행형 JSON 템플릿을 통해 클러스터를 만들 때 인증서 보안을 구성합니다. 기본 인증서 및 인증서 롤오버에 사용되는 선택적 보조 인증서를 지정할 수 있습니다. 지정한 기본 및 보조 인증서는 관리 클라이언트 및 [클라이언트-노드 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)에 대해 지정한 읽기 전용 클라이언트 인증서와 다릅니다.

### <a name="client-to-node-security"></a>클라이언트-노드 보안
클라이언트 ID를 사용하여 클라이언트-노드 보안을 구성합니다. 클라이언트와 클러스터 간에 트러스트를 설정하기 위해 신뢰할 수 있는 클라이언트 ID를 파악하도록 클러스터를 구성해야 합니다.

Service Fabric은 Service Fabric 클러스터에 연결된 클라이언트에 대해 두 가지 액세스 제어 형식을 지원합니다.

-   **관리자**: 읽기/쓰기 기능을 포함한 관리 기능에 대한 모든 권한을 가집니다.
-   **사용자**: 기본적으로 관리 기능(예: 쿼리 기능)에 대한 읽기 권한 및 애플리케이션과 서비스를 확인하는 기능만 갖습니다.

액세스 제어를 사용하여 클러스터 관리자는 특정 유형의 클러스터 작업에 대한 액세스를 제한할 수 있습니다. 이렇게 하면 클러스터의 보안을 강화합니다.

#### <a name="client-to-node-certificate-security"></a>클라이언트-노드 인증서 보안

Azure Portal, Resource Manager 템플릿 또는 독립 실행형 JSON 템플릿을 통해 클러스터를 만들 때 클라이언트-노드 인증서 보안을 구성합니다. 관리 클라이언트 인증서 및/또는 사용자 클라이언트 인증서를 지정해야 합니다. 이러한 인증서는 노드 간 보안에 대해 지정한 기본 및 보조 인증서와 다른지 확인합니다.

관리 인증서를 사용하여 클러스터에 연결하는 클라이언트에는 관리 기능에 대한 모든 권한이 있습니다. 읽기 전용 사용자 클라이언트 인증서를 사용하여 클러스터에 연결하는 클라이언트는 관리 기능에 대한 읽기 전용 액세스 권한만 있습니다. 즉, 이러한 인증서는 역할 기반 액세스 제어(RBAC)에 사용됩니다.

클러스터에서 인증서 보안을 구성하는 방법을 알아보려면 [Azure Resource Manager 템플릿을 사용하여 클러스터 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 참조하세요.

#### <a name="client-to-node-azure-active-directory-security"></a>클라이언트-노드 Azure Active Directory 보안

Azure에서 실행되는 클라이언트는 Azure AD(Azure Active Directory)를 사용하여 관리 엔드포인트에 안전하게 액세스할 수 있습니다. 필요한 Azure Active Directory 아티팩트를 만드는 방법, 클러스터를 만드는 동안 이러한 아티팩트를 채우는 방법 및 나중에 해당 클러스터에 연결하는 방법에 대한 자세한 내용은 [Azure Resource Manager 템플릿을 사용하여 클러스터 설정](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)을 참조하세요.

조직(테넌트)에서는 Azure AD를 사용하여 애플리케이션에 대한 사용자 액세스를 관리할 수 있습니다. 웹 기반 로그인 UI를 갖는 애플리케이션과 네이티브 클라이언트 환경을 갖는 애플리케이션이 있습니다.

Service Fabric 클러스터는 웹 기반 Service Fabric Explorer 및 Visual Studio를 포함하여 관리 기능에 대한 여러 진입점을 제공합니다. 결과적으로 두 개의 Azure AD 애플리케이션(웹 애플리케이션과 네이티브 애플리케이션)을 만들어 클러스터에 대한 액세스를 제어합니다.

Azure 클러스터의 경우 Azure AD 보안을 사용하여 노드 간 보안에 대해 클라이언트 및 인증서를 인증하는 것이 좋습니다.

Windows Server 2012 R2 및 Active Directory가 있는 독립 실행형 Windows Server 클러스터의 경우 그룹 관리 서비스 계정(gMSA)으로 Windows 보안을 사용하는 것이 좋습니다. 그렇지 않은 경우 Windows 계정으로 Windows 보안을 사용합니다.

## <a name="understand-monitoring-and-diagnostics-in-service-fabric"></a>Service Fabric에서 모니터링 및 진단 이해
[모니터링 및 진단](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview)은 모든 환경에서 애플리케이션 및 서비스를 개발, 테스트 및 배포하는 데 중요합니다. Service Fabric 솔루션은 로컬 개발 환경 또는 프로덕션 환경에서 애플리케이션과 서비스가 예상대로 작동하는지 확인하는 모니터링 및 진단을 구현할 때 가장 효과적입니다.

보안의 관점에서 볼 때 모니터링 및 진단의 주요 목표는 다음과 같습니다.

-   보안 이벤트에 기인한 것일 수 있는 하드웨어 및 인프라 문제를 검색 및 진단합니다.
-   IoC(손상의 지표)가 될 수 있는 소프트웨어와 앱 문제를 검색합니다.
-   우발적인 서비스 거부를 방지할 수 있게 리소스 사용량을 파악합니다.

모니터링 및 진단의 워크플로는 다음 세 단계로 구성됩니다.

1.  **이벤트 생성**: 이벤트 생성에는 인프라(클러스터) 수준 및 애플리케이션/서비스 수준의 이벤트(로그, 추적, 사용자 지정 이벤트)가 모두 포함됩니다. 제공되는 계측 및 계측을 추가하는 방법을 이해하려면 [인프라 수준 이벤트](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) 및 [애플리케이션 수준 이벤트](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app)를 참조하세요.

2.  **이벤트 집계**: 생성된 이벤트를 표시하려면 먼저 수집하고 집계해야 합니다. 일반적으로 [Azure Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad)(에이전트 기반 로그 수집과 유사) 또는 [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow)(프로세스 내 로그 수집)를 사용하는 것이 좋습니다.

3.  **분석**: 분석하고 표시할 수 있도록 이벤트를 특정 형식으로 시각화하고 액세스할 수 있어야 합니다. 데이터 모니터링 및 진단의 분석 및 시각화에 대한 여러 가지 플랫폼이 있습니다. 것이 좋습니다 [Azure Monitor 로그](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) 하 고 [Azure Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) 도 Service Fabric과 통합 하기 때문에 있습니다.

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)로도 Service Fabric 클러스터의 기반이 되는 다양한 Azure 리소스를 모니터링할 수 있습니다.

Watchdog은 서비스 전체에서 상태와 부하를 감시하고 상태 모델 계층 구조 상의 모든 항목에 대한 상태를 보고할 수 있는 별도의 서비스입니다. Watchdog을 사용하면 단일 서비스 보기를 기반으로 하여 감지되지 않는 오류를 방지할 수 있습니다. 

Watchdog는 사용자 상호 작용 없이 교정 작업을 수행하는 코드를 호스트하는 적합한 장소입니다. 예제는 특정 시간 간격으로 저장소에서 로그 파일을 정리합니다. [Azure Service Fabric watchdog 샘플](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/)에서 샘플 watchdog 서비스 구현을 찾을 수 있습니다.

## <a name="secure-communication-by-using-certificates"></a>인증서를 사용하여 통신 보호
인증서를 통해 독립 실행형 Windows 클러스터의 다양한 노드 간 통신을 보호합니다. X.509 인증서를 사용하여 이 클러스터에 연결하는 클라이언트를 인증할 수도 있습니다. 이렇게 하면 권한이 있는 사용자만 클러스터에 액세스할 수 있습니다. 만들 때 클러스터에서 인증서를 활성화하는 것이 좋습니다.

X.509 디지털 인증서는 클라이언트 및 서버를 인증하는 데 일반적으로 사용됩니다. 또한 암호화하고 디지털로 메시지에 서명하는 데 사용됩니다.

다음 테이블에서는 클러스터 설치에 필요한 인증서를 나열합니다.

|인증서 정보 설정 |설명|
|-------------------------------|-----------|
|ClusterCertificate|    이 인증서는 클러스터에서 노드 간의 통신을 보호해야 합니다. 기본 인증서 및 보조 인증서 등 두 개의 클러스터 인증서를 사용하여 업그레이드할 수 있습니다.|
|ServerCertificate| 이 인증서가 클러스터에 연결하려고 시도할 때 클라이언트에 표시됩니다. 기본 인증서 및 보조 인증서 등 두 개의 서버 인증서를 사용하여 업그레이드할 수 있습니다.|
|ClientCertificateThumbprints|  인증된 클라이언트에 설치하는 인증서의 집합입니다.|
|ClientCertificateCommonNames|  CertificateCommonName에 대한 첫 번째 클라이언트 인증서의 일반 이름입니다. CertificateIssuerThumbprint는 이 인증서의 발급자 지문입니다.|
|ReverseProxyCertificate|   이는 [역방향 프록시](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy)를 보호하도록 지정할 수 있는 선택적인 인증서입니다.|

인증서 보안에 대한 자세한 내용은 [X.509 인증서를 사용하여 Windows에서 독립 실행형 클러스터 보호](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)를 참조하세요.

## <a name="understand-role-based-access-control"></a>역할 기반 Access Control 이해
클러스터를 만들 때 각각에 대해 개별 ID(인증서, AAD 등 포함)를 제공하여 관리자 및 사용자 클라이언트 역할을 지정합니다. 기본 Access Control 설정 및 기본 설정을 변경하는 방법에 대한 자세한 내용은 [Service Fabric 클라이언트에 대한 역할 기반 액세스 제어](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)를 참조하세요.

## <a name="secure-standalone-clusters-by-using-windows-security"></a>Windows 보안을 사용하여 독립 실행형 클러스터 보안
Service Fabric 클러스터에 대한 무단 액세스를 방지하려면 클러스터를 보호해야 합니다. 클러스터에서 프로덕션 작업을 실행하는 경우 특히 보안이 중요합니다. ClusterConfig.JSON 파일에서 Windows 보안을 사용하여 노드 간 및 클라이언트-노드 간 보안을 구성합니다.

Service Fabric이 gMSA에서 실행되어야 하는 경우 [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security)를 설정하여 노드 간 보안을 구성합니다. 노드 간의 신뢰 관계를 구축하려면 서로를 인식하도록 만들어야 합니다.

Active Directory 도메인 내에서 컴퓨터 그룹을 사용하려는 경우 ClusterIdentity를 설정하여 노드 간 보안을 구성합니다. 자세한 내용은 [Active Directory에 컴퓨터 그룹 만들기](https://msdn.microsoft.com/library/aa545347)를 참조하세요.

클라이언트 ID를 사용하여 클라이언트-노드 보안을 구성합니다. 신뢰할 수 있는 클라이언트 ID를 인식하도록 클러스터를 구성해야 합니다. 다음 두 가지 방법으로 트러스트를 설정할 수 있습니다.

-   연결할 수 있는 도메인 그룹 사용자를 지정합니다.
-   연결할 수 있는 도메인 노드 사용자를 지정합니다.

## <a name="configure-application-security-in-service-fabric"></a>Service Fabric에서 애플리케이션 보안 구성
### <a name="manage-secrets-in-service-fabric-applications"></a>Service Fabric 애플리케이션에서 비밀 관리
저장소 연결 문자열, 암호, 일반 텍스트로 처리하면 안 되는 값 등 모든 민감한 정보를 비밀로 처리할 수 있습니다.

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)를 사용하여 키와 비밀을 관리할 수 있습니다. 그러나 애플리케이션에서 암호 사용은 특정 클라우드 플랫폼을 필요로 하지 않습니다. 어디서나 호스팅되는 클러스터에 애플리케이션을 배포할 수 있습니다. 이 흐름은 주요 네 단계로 구성됩니다.

1.  데이터 암호화 인증서를 가져옵니다.
2.  클러스터에 인증서를 설치합니다.
3.  인증서를 사용하여 애플리케이션을 배포할 때 비밀 값을 암호화하여 서비스의 Settings.xml 구성 파일에 삽입합니다.
4.  동일한 암호화 인증서로 암호를 해독하여 Settings.xml 파일에서 암호화된 값을 읽습니다.

자세한 내용은 [Service Fabric 애플리케이션의 암호 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management)를 참조하세요.

### <a name="configure-security-policies-for-an-application"></a>애플리케이션에 대한 보안 정책 구성
Azure Service Fabric 보안을 사용하여 다른 사용자 계정으로 클러스터에서 실행 중인 애플리케이션을 보호할 수 있습니다. 또한 Service Fabric 보안은 배포 시 파일, 디렉터리, 인증서 등과 같은 사용자 계정을 통해 애플리케이션에서 사용하는 리소스도 보호합니다. 따라서 공유되는 호스티드 환경에서도 더욱 안전하게 애플리케이션을 실행할 수 있습니다.

보안 정책 구성을 위한 작업은 다음과 같습니다.

-   서비스 설치 진입점에 대한 정책 구성
-   설치 진입점에서 PowerShell 명령 시작
-   로컬 디버깅에 대한 콘솔 리디렉션 사용
-   서비스 코드 패키지에 대한 정책 구성
-   HTTP 및 HTTPS 엔드포인트에 보안 액세스 정책 할당

## <a name="secure-communication-for-services"></a>서비스에 대한 통신 보호
통신의 가장 중요한 측면 중 하나는 보안입니다. Reliable Services 애플리케이션 프레임워크는 보안을 향상시키는 데 사용할 수 있는 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다. 자세한 내용은 [서비스에 대한 서비스 원격 통신 보호](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication)를 참조하세요.

## <a name="next-steps"></a>다음 단계
- 클러스터 보안에 대한 개념 정보는 [Azure Resource Manager를 사용하여 Service Fabric 클러스터 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) 및 [Azure Portal을 사용하여 Service Fabric 클러스터 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal)를 참조하세요.
- Service Fabric에서 클러스터 보안에 대해 자세히 알아보려면 [Service Fabric 클러스터 보안 시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)를 참조하세요.
