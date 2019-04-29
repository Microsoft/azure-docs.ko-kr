---
title: Azure Service Fabric 보안 모범 사례 | Microsoft Docs
description: 이 문서에서는 Azure Service Fabric 보안을 위한 여러 모범 사례를 제공합니다.
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
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: 8bafc4a95ca9af4567ed70c190a72f3b351da47c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611530"
---
# <a name="azure-service-fabric-security-best-practices"></a>Azure Service Fabric 보안 모범 사례
Azure에 애플리케이션을 배포하는 것은 빠르고, 쉽고, 비용 효율적입니다. 프로덕션에 클라우드 애플리케이션을 배포하기 전에 애플리케이션에서 보안 클러스터를 구현하기 위한 필수 및 권장 모범 사례의 목록을 검토합니다.

Azure Service Fabric은 손쉽게 패키지하고 배포하며 확장 가능하고 안정성이 뛰어난 마이크로 서비스를 관리하는 분산된 시스템 플랫폼입니다. 또한 서비스 패브릭은 클라우드 애플리케이션 개발 및 관리에서 발생하는 중요한 과제를 해결합니다. 개발자와 관리자가 복잡한 인프라 문제를 피하고 업무 수행에 필수적인 까다로운 워크로드를 확장 가능하고 신뢰할 수 있으며 관리가 가능하도록 구현하는 데 집중할 수 있습니다.

각 모범 사례에 대해 다음과 같이 설명합니다.

-   각 모범 사례.
-   모범 사례를 구현해야 하는 이유.
-   모범 사례를 구현하지 않는 경우 발생할 수 있는 일.
-   모범 사례를 구현하는 방법 학습.

다음과 같은 Azure Service Fabric 보안 모범 사례를 권장합니다.

-   Azure Resource Manager 템플릿 및 Service Fabric PowerShell 모듈을 사용하여 보안 클러스터를 만듭니다.
-   X.509 인증서를 사용합니다.
-   보안 정책 구성.
-   Reliable Actors 보안 구성을 구현합니다.
-   Azure Service Fabric에 대해 SSL을 구성합니다.
-   Azure Service Fabric을 통한 네트워크 격리 및 보안을 사용합니다.
-   보안을 위해 Azure Key Vault를 구성합니다.
-   사용자에게 역할을 할당합니다.


## <a name="best-practices-for-securing-your-clusters"></a>클러스터를 보호하기 위한 모범 사례

다음과 같이 항상 보안 클러스터를 사용합니다.
-   인증서를 사용하여 클러스터 보안을 구현합니다.
-   Azure Active Directory(Azure AD)를 사용하여 클라이언트 액세스(관리자 및 읽기 전용)를 제공합니다.

다음과 같이 자동 배포를 사용합니다.
-   스크립트를 사용하여 암호를 생성, 배포 및 롤오버합니다.
-   Azure Key Vault에 암호를 저장하고 다른 모든 클라이언트 액세스에 대해 Azure AD를 사용합니다.
-   암호에 대한 휴먼 액세스 인증을 요구합니다.

또한 다음 구성 옵션을 고려합니다.
-   Azure Network Security Groups(NSG)를 사용하여 경계 네트워크(완충 지역, DMZ 및 스크린된 서브넷이라고도 함)를 만듭니다.
-   클러스터 가상 머신(VM)를 액세스하거나 원격 데스크톱 연결로 점프 서버를 사용하여 클러스터를 관리합니다.

특히 프로덕션에서 클러스터가 실행 중일 때 권한 없는 사용자가 클러스터에 연결하지 못하도록 하여 클러스터의 보안을 유지해야 합니다. 보안이 되지 않은 클러스터를 만들 수 있지만 클러스터가 공용 인터넷에 관리 엔드포인트를 노출하는 경우 익명 사용자가 클러스터에 연결할 수 있게 됩니다.

다양한 기술을 사용하여 클러스터 보안을 구현하기 위해 세 가지 [시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)가 있습니다.

-   노드-노드 보안: 이 시나리오는 VM과 클러스터 내 컴퓨터 간의 통신을 보호합니다. 이러한 보안 형태는 클러스터에 가입하도록 인증된 컴퓨터만 클러스터에서 애플리케이션 및 서비스를 호스팅할 수 있습니다.
이 시나리오에서 Azure에서 실행되는 클러스터 또는 Windows에서 실행되는 독립 실행형 클러스터는 Windows Server 컴퓨터에 대해 [인증서 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) 또는 [Windows 보안](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security)을 사용할 수 있습니다.
-   클라이언트-노드 보안: 이 시나리오는 클라이언트를 인증하고 Service Fabric 클라이언트와 클러스터의 개별 노드 간 통신을 보호합니다.
-   RBAC(역할 기반 액세스 제어): 이 시나리오는 클러스터에 액세스하는 각 관리자와 사용자 클라이언트 역할에 대해 별도의 ID(인증서, Azure AD 인증서 등)를 사용합니다. 클러스터를 만들 때 역할 ID를 지정합니다.

>[!NOTE]
>**Azure 클러스터에 대한 보안 권장 사항:** Azure AD 보안을 사용하여 노드 간 보안에 대해 클라이언트 및 인증서를 인증합니다.

독립 실행형 Windows 클러스터를 구성하려면 [독립 실행형 Windows 클러스터에 대한 설정 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest)을 참조하세요.

Azure Resource Manager 템플릿 및 Service Fabric PowerShell 모듈을 사용하여 보안 클러스터를 만듭니다.
Azure Resource Manager를 사용하여 보안 Service Fabric 클러스터를 만들기 위한 단계별 지침은 [Service Fabric 클러스터 만들기](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)를 참조하세요.

Azure Resource Manager 템플릿을 사용합니다.
-   VM 가상 하드 디스크(VHD)에 대해 관리되는 저장소를 구성하는 템플릿을 사용하여 클러스터를 사용자 지정합니다.
-   손쉬운 구성 관리 및 감사를 위해 템플릿을 사용하여 리소스 그룹을 변경합니다.

클러스터 구성을 코드로 처리합니다.
-   배포 구성을 철저히 확인합니다.
-   리소스를 직접 조정하기 위해 암시적 명령을 사용하지 않습니다.

[Service Fabric 애플리케이션 수명 주기](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) 의 여러 측면을 자동화할 수 있습니다. [Service Fabric PowerShell 모듈](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package)은 Azure Service Fabric 애플리케이션의 배포, 업그레이드, 제거 및 테스트를 위한 일반적인 작업을 자동화합니다. 애플리케이션 관리를 위한 관리형 API 및 HTTP API도 사용 가능합니다.

## <a name="use-x509-certificates"></a>X.509 인증서 사용
항상 X.509 인증서 또는 Windows 보안을 사용하여 클러스터를 보호합니다. 보안은 클러스터를 만들 때에만 구성됩니다. 클러스터를 만든 후에는 보안 기능을 설정할 수 없습니다.

[클러스터 인증서](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security)를 지정하려면 **ClusterCredentialType** 속성 값을 X509로 설정합니다. 외부 연결에 대한 서버 인증서를 지정하려면 **ServerCredentialType** 속성을 X509로 설정합니다.

또한 다음 사례를 따릅니다.
-   올바르게 구성된 Windows Server 인증서 서비스를 사용하여 프로덕션 클러스터에 대한 인증서를 만듭니다. 또한 승인된 인증 기관(CA)으로부터 인증서를 얻을 수 있습니다.
-   MakeCert.exe 또는 유사한 도구를 사용하여 인증서를 만든 경우 프로덕션 클러스터에 임시 또는 테스트 인증서를 사용하지 마세요.
-   자체 서명된 인증서를 테스트 클러스터에 대해 사용하되, 프로덕션 클러스터에 대해서는 사용하지 마세요.

클러스터가 보안되지 않은 경우 누구든지 익명으로 연결하여 관리 작업을 수행할 수 있습니다. 이러한 이유로 항상 X.509 인증서 또는 Windows 보안을 사용하여 프로덕션 클러스터를 보호합니다.

X.509 인증서를 사용하는 방법에 대한 자세한 내용은 [Service Fabric 클러스터에 대한 인증서 추가 또는 제거](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)를 참조하세요.

## <a name="configure-security-policies"></a>보안 정책 구성
Service Fabric은 애플리케이션에서 사용되는 리소스를 보호합니다. 파일, 디렉터리 및 인증서와 같은 리소스는 애플리케이션을 배포할 때 사용자 계정에 저장됩니다. 이 기능은 공유된 호스팅 환경에서도 서로 더욱 안전하게 애플리케이션이 실행되도록 합니다.

-   Active Directory 도메인 그룹 또는 사용자 사용: Active Directory 사용자 또는 그룹 계정에 대한 자격 증명에서 서비스를 실행합니다. 도메인 내의 Active Directory 온-프레미스를 사용하고 Azure Active Directory는 사용하지 않습니다. 도메인 사용자 또는 그룹을 사용하여 권한이 부여된 도메인의 다른 리소스를 액세스합니다. 예를 들어, 파일 공유와 같은 리소스입니다.

-   HTTP 및 HTTPS 엔드포인트에 대한 보안 액세스 정책 할당: 서비스 매니페스트가 HTTP를 통해 엔드포인트 리소스를 선언하는 경우 **SecurityAccessPolicy** 속성을 지정하여 **RunAs** 정책을 서비스에 적용합니다. HTTP 엔드포인트에 할당된 포트는 서비스가 실행되는 RunAs 사용자 계정에 대해 올바르게 액세스 제어된 목록입니다. 정책이 설정되지 않은 경우 http.sys는 서비스에 액세스할 수 없고 클라이언트의 호출과 함께 오류가 발생할 수 있습니다.

Service Fabric에서 보안 정책을 사용하는 방법에 대한 자세한 내용은 [애플리케이션에 대한 보안 정책 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security)을 참조하세요.

## <a name="implement-the-reliable-actors-security-configuration"></a>Reliable Actors 보안 구성 구현
Service Fabric Reliable Actors는 행위자 디자인 패턴을 구현한 것입니다. 소프트웨어 디자인 패턴처럼 특정 패턴을 사용하겠다는 결정은 소프트웨어 문제가 패턴에 맞는지 여부에 기반합니다.

일반적으로 다음과 같은 소프트웨어 문제 또는 보안 시나리오에 대한 모델 솔루션을 위해 행위자 디자인 패턴을 사용합니다.
-   문제가 되는 공간이 1,000개 이상의 많은 수의 작고 독립적이며 격리된 상태 및 논리 단위를 포함하는 경우
-   일련의 행위자에 걸친 상태를 쿼리하는 등 외부 구성 요소에서 중요한 상호 작용을 필요로 하지 않는 단일 스레드 개체로 작업하려고 합니다.
-   행위자 인스턴스는 I/O 작업을 실행하여 예측할 수 없는 지연으로 호출자를 차단하지 않습니다.

Service Fabric의 경우 행위자는 Reliable Actors 애플리케이션 프레임워크에서 구현됩니다. 이 프레임워크는 행위자 패턴을 기반으로 하며 [Service Fabric Reliable Services](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction) 위에 구축됩니다. 작성한 신뢰할 수 있는 서비스는 각각 분할된 상태 저장 신뢰할 수 있는 서비스입니다.

.NET 개체가 .NET 형식의 인스턴스인 것과 동일하게 모든 행위자는 행위자 형식의 인스턴스로 정의됩니다. 예를 들어, 계산기의 기능을 구현하는 **행위자 형식**에는 클러스터 전체에 걸쳐 다양한 노드에 배포되는 해당 형식의 여러 행위자가 있을 수 있습니다. 분산된 각각의 행위자는 행위자 식별자에 의해 고유하게 특징 지어집니다.

[복제자 보안 구성](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration)은 복제하는 동안 사용되는 통신 채널을 보호하는 데 사용됩니다. 이 구성은 서비스가 다른 사용자의 복제 트래픽을 보지 못하도록 하고 항상 사용 가능한 데이터를 보호합니다. 기본적으로 빈 보안 구성 섹션에서는 복제 보안이 되지 않습니다.
복제자 구성은 행위자 상태 제공자의 상태를 매우 안정적으로 만드는 작업을 담당하는 복제자를 구성합니다.

## <a name="configure-ssl-for-azure-service-fabric"></a>Azure Service Fabric에 대해 SSL 구성
서버 인증 프로세스는 관리 클라이언트에 대해 클러스터 관리 엔드포인트를 [인증](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)합니다. 그러면 관리 클라이언트가 실제 클러스터와 통신하고 있음을 인식합니다. 이 인증은 HTTPS 관리 API 및 HTTPS를 통한 Service Fabric Explorer용 [SSL](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)도 제공합니다.
클러스터에 대한 사용자 지정 도메인 이름을 획득해야 합니다. 인증 기관으로부터 인증서를 요청하는 경우 인증서의 주체 이름이 클러스터에 사용되는 사용자 지정 도메인 이름과 일치해야 합니다.

애플리케이션에 대해 SSL을 구성하려면 먼저 CA에서 서명한 SSL 인증서를 얻어야 합니다. CA가 SSL 보안 목적을 위해 인증서를 발급하는 신뢰할 수 있는 타사입니다. SSL 인증서가 아직 없는 경우 SSL 인증서를 판매하는 회사에서 구입해야 합니다.

인증서는 Azure의 SSL 인증서에 대한 다음 요구 사항을 충족해야 합니다.
-   인증서에 개인 키가 포함되어 있어야 합니다.

-   인증서는 키 교환을 위해 만들어야 하며 개인 정보 교환(.pfx) 파일로 내보낼 수 있어야 합니다.

-   인증서의 주체 이름은 클라우드 서비스 액세스에 사용되는 도메인 이름과 일치해야 합니다.

    - 클라우드 서비스에 액세스할 때 사용할 사용자 지정 도메인 이름을 획득합니다.
    - 서비스의 사용자 지정 도메인 이름과 일치하는 주체 이름을 가진 CA로부터 인증서를 요청합니다. 예를 들어 사용자 지정 도메인 이름이 __contoso__**.com**인 경우 CA로부터 받은 인증서에 **.contoso.com** 또는 __www__**.contoso.com**의 주체 이름이 있어야 합니다.

    >[!NOTE]
    >__cloudapp__**.net** 도메인에 사용되는 SSL 인증서는 CA에서 얻을 수 없습니다.

-   인증서는 최소한 2,048비트 암호화를 사용해야 합니다.

HTTP 프로토콜은 보안되지 않으며 도청 공격을 받기 쉽습니다. HTTP를 통해 전송된 데이터는 웹 브라우저에서 웹 서버로 또는 다른 엔드포인트 간에 일반 텍스트로 전송됩니다. 공격자는 신용카드 정보와 계정 로그인 같이 HTTP를 통해 보내진 중요 데이터를 가로채 볼 수 있습니다. 데이터가 HTTPS를 통한 브라우저를 통해 전송 또는 게시될 경우 SSL이 중요한 정보를 암호화하여 가로채기로부터 보호합니다.

SSL 인증서 사용에 대한 자세한 내용은 [Azure 애플리케이션에 대 한 SSL 구성](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate)을 참조하세요.

## <a name="use-network-isolation-and-security-with-azure-service-fabric"></a>Azure Service Fabric을 통한 네트워크 격리 및 보안 사용
[Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)을 샘플로 사용하여 3 nodetype 보안 클러스터를 설정합니다. 템플릿 및 네트워크 보안 그룹을 사용하여 인바운드 및 아웃바운드 네트워크 트래픽을 제어합니다.

템플릿에는 각 가상 머신 확장 집합(VMSS)에 대한 NSG가 있으며, 이 집합을 드나드는 트래픽을 제어하는 데 사용됩니다. 규칙은 기본적으로 템플릿에서 지정된 시스템 서비스와 애플리케이션 포트에 필요한 모든 트래픽을 허용하도록 구성됩니다. 이러한 규칙을 검토하고 애플리케이션에 대한 새 규칙을 추가하는 등 필요에 따라 변경합니다.

자세한 내용은 [Azure Service Fabric의 일반적인 네트워킹 시나리오](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking)를 참조하세요.

## <a name="set-up-azure-key-vault-for-security"></a>보안을 위해 Azure Key Vault 설정
Service Fabric은 인증서를 사용하여 클러스터와 해당 애플리케이션에 인증 및 암호화를 제공합니다.

Service Fabric은 클러스터에 보안을 적용하고 애플리케이션 보안 기능을 제공하기 위해 X.509 인증서를 사용합니다. Azure Key Vault는 Azure의 Service Fabric 클러스터에 대한 [인증서를 관리](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure)하는 데 사용됩니다. 클러스터를 만드는 Azure 리소스 공급자는 키 자격 증명 모음에서 인증서를 가져옵니다. 그런 다음 공급자는 Azure에서 클러스터를 배포할 때 인증서를 VM에 설치합니다.

인증서 관계는 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), Service Fabric 클러스터, 인증서를 사용하는 리소스 공급자 사이에 존재합니다. 클러스터가 생성될 때 인증서 관계에 관한 정보는 키 자격 증명 모음에 저장됩니다.

키 자격 증명 모음을 설정하는 데는 다음과 같은 두 가지 기본적인 단계가 있습니다.
1. 키 자격 증명 모음 전용 리소스 그룹을 만듭니다.

    자체 리소스 그룹에 키 자격 증명 모음을 배치하는 것이 좋습니다. 이렇게 하면 스토리지, 컴퓨팅 또는 클러스터를 포함하는 그룹 등의 다른 리소스 그룹을 제거하는 경우 키와 비밀이 분실되는 것을 방지할 수 있습니다. 사용자 Key Vault를 포함하는 리소스 그룹은 해당 그룹을 사용하는 클러스터와 동일한 지역에 있어야 합니다.

2. 새 리소스 그룹에 키 자격 증명 모음을 만듭니다.

    배포 시 키 자격 증명 모음을 사용해야 합니다. 그러면 계산 리소스 공급자가 자격 증명 모음에서 인증서를 가져와서 VM 인스턴스에 설치할 수 있습니다.

키 자격 증명 모음 설정 방법에 대한 자세한 내용은 [Azure Key Vault란?](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)을 참조하세요.

## <a name="assign-users-to-roles"></a>역할에 사용자 할당
클러스터를 나타내는 애플리케이션을 만들었으면 사용자를 Service Fabric에서 지원하는 역할(읽기 전용 및 관리자)에 할당합니다. Azure Portal을 사용하여 역할을 할당할 수 있습니다.

>[!NOTE]
> Service Fabric에서 역할 사용에 대한 자세한 내용은 [Service Fabric 클라이언트의 역할 기반 액세스 제어](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles)를 참조하세요.

Azure Service Fabric은 [Service Fabric 클러스터](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)에 연결된 클라이언트에 대해 두 가지 액세스 제어 유형(관리자 및 사용자)을 지원합니다. 클러스터 관리자는 다른 사용자 그룹의 특정 클러스터 작업에 대한 액세스를 제한하기 위해 액세스 제어를 사용할 수 있습니다. 액세스 제어는 클러스터의 보안을 강화합니다.

## <a name="next-steps"></a>다음 단계

- [Service Fabric 보안 검사 목록](azure-service-fabric-security-checklist.md)
- Service Fabric [개발 환경](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)을 설정합니다.
- [Service Fabric 지원 옵션](https://docs.microsoft.com/azure/service-fabric/service-fabric-support)에 대해 알아봅니다.
