---
title: 클라우드 솔루션 공급자에 대한 Azure AD Domain Services | Microsoft Docs
description: Azure 클라우드 솔루션 공급자의 Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754432"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Azure CSP(클라우드 솔루션 공급자)의 Azure AD(Active Directory) Domain Services
이 문서에서는 Azure CSP 구독에서 Azure AD Domain Services를 사용하는 방법을 설명합니다.

## <a name="overview-of-azure-csp"></a>Azure CSP 개요
Azure CSP는 Microsoft 파트너에 대한 프로그램이며 다양한 Microsoft 클라우드 서비스에 대한 라이선스 채널을 제공합니다. Azure CSP를 사용하면 파트너가 영업을 관리하고, 청구 관계를 소유하고, 기술 및 요금 청구 지원을 제공하고, 고객의 단일 연락 지점이 될 수 있습니다. 또한 Azure CSP는 셀프 서비스 포털을 포함하고 API를 함께 제공하는 도구의 전체 집합을 제공합니다. 이러한 도구를 사용하면 CSP 파트너가 Azure 리소스를 쉽게 프로비전하고 관리할 수 있으며 고객 및 해당 구독에 대한 요금을 청구합니다.

[파트너 센터 포털](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview)은 모든 Azure CSP 파트너에 대한 진입점으로 작동합니다. 이 기능은 풍부한 고객 관리 기능, 자동화된 처리 등을 제공합니다. Azure CSP 파트너는 웹 기반 UI를 사용하거나 PowerShell 및 다양한 API 호출을 사용하여 파트너 센터 기능을 사용할 수 있습니다.

다음 다이어그램에서는 CSP 모델이 작동하는 방법을 대략적으로 보여줍니다. Contoso에는 Azure AD Active Directory가 있습니다. CSP Azure 구독에서 리소스를 배포하고 관리하는 CSP와 파트너 관계를 갖습니다. Contoso에는 일반(직접) Azure 구독이 있으며 Contoso에 직접 청구됩니다.

![CSP 모델 개요](./media/csp/csp_model_overview.png)

CSP 파트너의 테넌트에는 관리 에이전트, 기술 지원팀 에이전트 및 영업 에이전트와 같은 세 가지 특별한 에이전트 그룹이 있습니다. 관리 에이전트 그룹은 Contoso의 Azure AD 디렉터리에서 테넌트 관리자 역할에 할당됩니다. 결과적으로 CSP 파트너의 관리 에이전트 그룹에 속한 사용자에게는 Contoso의 Azure AD 디렉터리에서 테넌트 관리자 권한이 있습니다. CSP 파트너가 Contoso에 대한 CSP Azure 구독을 프로비전하는 경우 해당 관리 에이전트 그룹은 해당 구독에 대한 소유자 역할에 할당됩니다. 결과적으로 CSP 파트너의 관리 에이전트에는 Contoso를 대신하여 가상 머신, 가상 네트워크 및 Azure AD Domain Services와 같은 Azure 리소스를 프로비전하는 데 필요한 권한이 있습니다.

자세한 내용은 [Azure CSP 개요](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)를 참조하세요.

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Azure CSP 구독에서 Azure AD Domain Services를 사용하는 이점
Azure AD Domain Services는 LDAP, Kerberos/NTLM 인증, 도메인 가입, 그룹 정책 및 DNS 등 Azure에서 Windows Server AD 호환 가능 서비스를 제공합니다. 시간이 지나면서 이러한 기능을 사용하여 AD에 대해 작동하도록 여러 애플리케이션이 빌드되었습니다. 여러 ISV(독립 소프트웨어 공급 업체)가 고객의 프레미스에서 애플리케이션을 빌드하고 배포했습니다. 종종 이러한 애플리케이션이 배포되는 다양한 환경에 대한 액세스 권한이 필요하기 때문에 해당 애플리케이션은 지원하기가 번거롭습니다. Azure CSP 구독을 사용하면 Azure의 규모 및 유연성으로 간단한 방법을 찾을 수 있습니다.

Azure AD Domain Services는 이제 Azure CSP 구독을 지원합니다. 이제 고객의 Azure AD 디렉터리에 연결된 Azure CSP 구독에서 애플리케이션을 배포할 수 있습니다. 결과적으로 직원(지원 스태프)은 해당 조직의 회사 자격 증명을 사용하여 애플리케이션을 배포한 가상 머신을 관리하고 제공할 수 있습니다. 또한 고객의 Azure AD 디렉터리에 Azure AD Domain Services 관리되는 도메인을 제공할 수 있습니다. 애플리케이션은 고객의 관리되는 도메인에 연결됩니다. 따라서 LDAP, Kerberos/NTLM 또는 [System.DirectoryServices API](/dotnet/api/system.directoryservices)를 사용하는 애플리케이션 내의 기능은 고객의 디렉터리에 대해 원활하게 작동합니다. 최종 고객은 애플리케이션이 배포된 인프라를 유지 관리할 방법에 대해 걱정할 필요 없이 애플리케이션을 서비스로 사용하여 큰 이익을 얻습니다.

Azure AD Domain Services를 비롯한 해당 구독에서 사용하는 Azure 리소스에 대한 요금은 다시 사용자에게 청구됩니다. 판매, 청구, 기술 지원 등을 통해 고객과의 관계에 대한 모든 권한을 유지 합니다. Azure CSP 플랫폼의 유연성을 제공 하는 소규모 지원 에이전트 팀은 응용 프로그램 인스턴스를 배포 하는 여러 고객에 게 서비스를 제공 합니다.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Azure AD Domain Services의 CSP 배포 모델
두 가지 방법으로 Azure CSP 구독에서 Azure AD Domain Services를 사용할 수 있습니다. 고객이 가진 보안 및 간결성 고려 사항에 따라 적합한 것을 선택합니다.

### <a name="direct-deployment-model"></a>직접 배포 모델
이 배포 모델에서 Azure AD Domain Services는 Azure CSP 구독에 속한 가상 네트워크 내에서 사용됩니다. CSP 파트너의 관리 에이전트에는 다음 권한이 있습니다.
* 고객의 Azure AD 디렉터리에 대한 전역 관리자 권한
* Azure CSP 구독에 대한 구독 소유자 권한

![직접 배포 모델](./media/csp/csp_direct_deployment_model.png)

이 배포 모델에서 CSP 공급자의 관리 에이전트는 고객에 대한 ID를 관리할 수 있습니다. 이러한 관리 에이전트는 새 사용자, 그룹, 고객의 Azure AD 디렉터리 내에 응용 프로그램을 추가 하는 등의 기능을 제공 합니다. 이 배포 모델은 전용 id 관리자가 없거나 CSP 파트너가 대신 id를 관리 하는 것을 선호 하는 소규모 조직에 적합할 수 있습니다.


### <a name="peered-deployment-model"></a>피어링된 배포 모델
이 배포 모델에서 Azure AD Domain Services는 고객에게 속한 가상 네트워크, 즉, 고객별로 지불되는 직접 Azure 구독 내에서 사용됩니다. 그런 다음 CSP 파트너는 고객의 CSP 구독에 속한 가상 네트워크 내에서 애플리케이션을 배포할 수 있습니다. 가상 네트워크는 Azure Virtual Network 피어링을 사용하여 연결할 수 있습니다. 결과적으로 Azure CSP 구독에서 CSP 파트너에 의해 배포된 워크로드/애플리케이션은 고객의 직접 Azure 구독에서 프로비전된 고객의 관리되는 도메인에 연결할 수 있습니다.

![피어링된 배포 모델](./media/csp/csp_peered_deployment_model.png)

이 배포 모델은 권한을 구별하고 CSP 파트너의 기술 지원팀이 Azure 구독을 관리하고 내부의 리소스를 배포하고 관리할 수 있습니다. 그러나 CSP 파트너의 기술 지원팀은 고객의 Azure AD 디렉터리에 대한 전역 관리자 권한이 필요하지 않습니다. 고객의 ID 관리자는 해당 조직에 대한 ID를 계속 관리할 수 있습니다.

이 배포 모델은 ISV(독립 소프트웨어 공급 업체)에서 온-프레미스 애플리케이션의 호스트된 버전을 제공하는 시나리오에 적합할 수 있습니다. 또한 고객의 AD에 연결해야 합니다.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>CSP 구독에서 Azure AD Domain Services 관리되는 도메인 관리
Azure CSP 구독에서 관리되는 도메인을 관리하는 경우 다음과 같은 중요한 고려 사항이 적용됩니다.

* **CSP 관리 에이전트는 해당 자격 증명을 사용하여 관리되는 도메인을 프로비전할 수 있습니다:** Azure AD Domain Services에서는 Azure CSP 구독을 지원합니다. 따라서 CSP 파트너의 관리 에이전트 그룹에 속한 사용자는 새로운 Azure AD Domain Services 관리되는 도메인을 구성할 수 있습니다.

* **CSP는 PowerShell을 사용하여 고객에 대해 새로운 관리되는 도메인 생성을 스크립팅할 수 있습니다:** 자세한 내용은 [PowerShell에서 Azure AD Domain Services를 사용하는 방법](powershell-create-instance.md)을 참조하세요.

* **CSP 관리 에이전트는 해당 자격 증명을 사용하여 관리되는 도메인에서 지속적인 관리 작업을 수행할 수 없습니다:** CSP 관리 사용자는 해당 자격 증명을 사용하여 관리되는 도메인 내에서 일상적인 관리 태스크를 수행할 수 없습니다. 이러한 사용자는 고객의 Azure AD 디렉터리의 외부에 위치하며 해당 자격 증명은 고객의 Azure AD 디렉터리 내에서 사용할 수 없습니다. 따라서 Azure AD Domain Services에는 이러한 사용자의 Kerberos 및 NTLM 암호 해시에 대한 액세스 권한이 없습니다. 결과적으로 Azure AD Domain Services 관리되는 도메인에서 이러한 사용자를 인증할 수 없습니다.

  > [!WARNING]
  > **관리되는 도메인에서 지속적인 관리 작업을 수행하기 위해 고객의 디렉터리 내에서 사용자 계정을 만들어야 합니다.**
  > CSP 관리 사용자의 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 없습니다. 이렇게 하려면 고객의 Azure AD 디렉터리에 속한 사용자 계정의 자격 증명을 사용합니다. 가상 머신을 관리되는 도메인에 가입하고, DNS를 관리하고, 그룹 정책 관리하는 등의 작업에 이러한 자격 증명이 필요합니다.
  >

* **진행 중인 관리를 위해 만든 사용자 계정은 'AAD DC 관리자' 그룹에 추가되어야 합니다:** 'AAD DC 관리자' 그룹은 관리되는 도메인에서 위임된 특정 관리 작업을 수행할 권한을 가집니다. 이러한 작업에는 DNS 구성, 조직 구성 단위 만들기, 그룹 정책 관리 등이 포함 됩니다. CSP 파트너가 관리 되는 도메인에서 이러한 작업을 수행 하려면 고객의 Azure AD 디렉터리 내에서 사용자 계정을 만들어야 합니다. 이 계정에 대한 자격 증명은 CSP 파트너의 관리 에이전트와 공유되어야 합니다. 또한 이 사용자 계정은 'AAD DC 관리자' 그룹에 추가되어 해당 사용자 계정을 사용하여 수행할 관리되는 도메인의 구성 작업을 사용해야 합니다.


## <a name="next-steps"></a>다음 단계
* [Azure CSP 프로그램에 등록](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program)하고 Azure CSP를 통해 비즈니스를 만듭니다.
* [Azure CSP에서 사용할 수 있는 Azure 서비스](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services) 목록을 검토합니다.
* [PowerShell을 사용하여 Azure AD Domain Services 사용](powershell-create-instance.md)
* [Azure AD 도메인 서비스 시작](tutorial-create-instance.md)
