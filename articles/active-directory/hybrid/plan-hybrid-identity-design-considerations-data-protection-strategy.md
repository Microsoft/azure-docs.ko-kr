---
title: Azure의 하이브리드 ID 디자인 - 데이터 보호 전략 | Microsoft Docs
description: 하이브리드 ID 솔루션에 대한 데이터 보호 전략을 정의하여 정의한 비즈니스 요구 사항을 충족합니다.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e76fd1f4-340a-492a-84d9-e05f3b7cc396
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bef7de68084ac3084c0b0179a7bbf6b1c9ca951
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182448"
---
# <a name="define-data-protection-strategy-for-your-hybrid-identity-solution"></a>하이브리드 ID 솔루션에 대한 데이터 보호 전략 정의
이 작업에서 하이브리드 ID 솔루션에 대한 데이터 보호 전략을 정의하여 다음에서 정의된 비즈니스 요구 사항을 충족합니다.

* [데이터 보호 요구 사항 결정](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)
* [콘텐츠 관리 요구 사항 확인](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)
* [액세스 제어 요구 사항 확인](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)
* [사고 대응 요구 사항 확인](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="define-data-protection-options"></a>데이터 보호 옵션 정의
[디렉터리 동기화 요구 사항 확인](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)에서 설명한 것처럼 Microsoft Azure AD는 Active Directory Domain Services(AD DS)와 동기화할 수 있습니다. 이 통합을 통해 조직이 회사 리소스에 액세스하려고 할 때 사용자의 자격 증명을 확인하기 위해 Azure AD를 활용할 수 있습니다. 온-프레미스 및 클라우드의 미사용 데이터, 두 시나리오 모두에 대해 이 작업을 수행할 수 있습니다. Azure AD의 데이터에 대한 액세스는 보안 토큰 서비스(STS)를 통해 사용자 인증이 필요합니다.

인증되면 사용자 계정 이름(UPN)을 인증 토큰에서 읽습니다. 그런 다음 권한 부여 시스템이 사용자 도메인에 해당하는 복제된 파티션과 컨테이너를 결정합니다. 사용자의 존재, 활성화된 상태 및 역할에 대한 정보는 권한 부여 시스템에서 사용되어 대상 테넌트에 대한 액세스가 이 세션에서 사용자에 대한 권한이 있는지 확인합니다. 권한이 부여된 특정 작업(특히 사용자, 암호 재설정 만들기)은 규정 준수 또는 조사를 관리하는 테넌트 관리자가 사용할 수 있는 감사 내역을 만듭니다.

인터넷 연결을 통해 온-프레미스 데이터 센터에서 Azure Storage로 데이터를 이동하는 작업은 데이터 볼륨, 대역폭 가용성 또는 기타 고려 사항으로 인해 항상 가능하지는 않습니다. [Azure Storage Import/Export 서비스](../../storage/common/storage-import-export-service.md) 는 Blob 스토리지에 있는 데이터의 큰 볼륨을 배치/검색하는 하드웨어 기반 옵션을 제공합니다. 이렇게 하면 클라우드 연산자가 스토리지 계정에 콘텐츠를 업로드하는 Azure 데이터 센터에 [BitLocker로 암호화된](https://technet.microsoft.com/library/dn306081#BKMK_BL2012R2) 하드 디스크 드라이브를 직접 보낼 수 있거나 반환되도록 Azure 데이터를 드라이브에 다운로드할 수 있습니다. 암호화된 디스크는(작업을 설치하는 동안 서비스 자체에서 생성된 BitLocker 키를 사용하여) 이 프로세스에 허용됩니다. BitLocker 키는 별도로 Azure에 제공되므로 공유할 밴드 키를 벗어나 제공됩니다.

전송 중인 데이터가 다양한 시나리오에 위치할 수 있으므로 또한 Microsoft Azure가 [가상 네트워킹](https://azure.microsoft.com/documentation/services/virtual-network/)을 사용하여 서로에서 테넌트 트래픽을 격리한다는 점을 인식하며 호스트 및 게스트 수준 방화벽, IP 패킷 필터링, 포트 차단 및 HTTPS 엔드포인트와 같은 측정값을 사용합니다. 그러나 인프라간 및 인프라 및 고객(온-프레미스)을 포함하여 Azure의 내부 통신 대부분은 암호화됩니다. 다른 중요한 시나리오는 Azure 데이터 센터 내의 통신입니다. Microsoft는 VM이 다른 IP 주소를 가장하거나 가로챌 수 없도록 네트워크를 관리합니다. TLS/SSL는 Azure Storage 또는 SQL Database에 액세스할 때 또는 Cloud Services에 연결할 때 사용됩니다. 이 경우에 고객 관리자는 TLS/SSL 인증서를 취득하고 테넌트 인프라에 배포하는 일을 담당합니다. Microsoft Azure Virtual Network를 통해 동일한 배포 내의 Virtual Machines 또한 단일 배포의 테넌트 간에 이동하는 데이터 트래픽은 HTTPS, SSL/TLS 또는 다른 사용자와 같은 암호화된 통신 프로토콜을 통해 보호될 수 있습니다.

[데이터 보호 요구 사항 확인](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)에서 질문에 답변하는 방법에 따라 데이터를 보호하는 방법 및 하이브리드 ID 솔루션으로 보조할 방법을 확인할 수 있어야 합니다. 다음 표에서는 각 데이터 보호 시나리오에 사용할 수 있는 Azure에서 지원되는 옵션을 보여줍니다.

| 데이터 보호 옵션 | 클라우드에서 휴지 상태 | 온-프레미스에서 휴지 상태 | 전송 중 |
| --- | --- | --- | --- |
| BitLocker 드라이브 암호화 |X |X | |
| 데이터베이스를 암호화하는 SQL Server |X |X | |
| VM간 암호화 | | |X |
| SSL/TLS | | |X |
| VPN | | |X |

> [!NOTE]
> [Microsoft Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)에서 [기능으로 규정 준수](https://azure.microsoft.com/support/trust-center/services/)를 읽고 각 Azure 서비스와 호환되는 인증에 대해 자세히 알아봅니다.
> 데이터 보호에 대한 옵션은 다중 계층 접근 방법을 사용하기 때문에 이러한 옵션 간의 비교는 이 작업에 적용될 수 없습니다. 데이터가 될 수 있는 각 상태에 사용 가능한 모든 옵션을 활용하도록 합니다.
>
>

## <a name="define-content-management-options"></a>콘텐츠 관리 옵션 정의

하이브리드 ID 인프라를 관리하기 위해 Azure AD를 사용하는 이점은 프로세스가 최종 사용자의 관점에서 완전히 투명하다는 점입니다. 토큰을 얻고 리소스에 액세스 하기 위해 사용자는 공유 리소스에 액세스하려고 하고 리소스에는 인증이 필요하며 사용자는 Azure AD로 인증 요청을 보내야 합니다. 이 전체 프로세스는 사용자 개입 없이 백그라운드에서 발생합니다. 

데이터 정보 보호를 우려하는 조직은 일반적으로 해당 솔루션에 대한 데이터 분류가 필요합니다. 현재 온-프레미스 인프라가 이미 데이터 분류를 사용하고 있는 경우 사용자의 ID에 대한 주 리포지토리로 Azure AD를 활용하는 것이 가능합니다. 데이터 분류 호출에 사용되는 온-프레미스인 일반적인 도구는 Windows Server 2012 R2에 대한 [데이터 분류 도구 키트](https://msdn.microsoft.com/library/Hh204743.aspx) 라고 합니다. 이 도구는 프라이빗 클라우드에서 파일 서버에 데이터를 식별, 분류 및 보호할 수 있습니다. 또한 이 작업을 수행하기 위해 Windows Server 2012에서 [자동 파일 분류](https://technet.microsoft.com/library/hh831672.aspx) 를 활용할 수 있습니다.

조직이 준비된 데이터 분류가 없지만 새 서버 온-프레미스를 추가하지 않고 중요한 파일을 보호해야 하는 경우 Microsoft [Azure Rights Management 서비스](https://technet.microsoft.com/library/JJ585026.aspx)를 사용할 수 있습니다.  Azure RMS는 암호화, ID 및 권한 부여 정책을 사용하여 파일 및 전자 메일을 보호하고 이는 휴대폰, 태블릿 및 PC와 같은 여러 디바이스에서 작동합니다. Azure RMS는 클라우드 서비스이기 때문에 서로 보호된 콘텐츠를 공유할 수 있기 전에 다른 조직과 트러스트를 명시적으로 구성할 필요가 없습니다. Office 365 또는 Azure AD 디렉터리가 이미 있는 경우 조직 간에 협업은 자동으로 지원됩니다. 또한 Azure Active Directory 동기화 서비스(AAD 동기화) 또는 Azure AD Connect을 사용하여 Azure RMS가 온-프레미스 Active Directory 계정에 대한 공용 ID를 지원하기 위해 필요로 하는 디렉터리 특성을 동기화할 수 있습니다.

콘텐츠 관리의 핵심은 누가 어떤 리소스에 액세스하지 이해하는 것입니다. 따라서 풍부한 로깅 기능은 ID 관리 솔루션에서 중요합니다. Azure AD에서는 다음을 포함하여 30일 이상 로그를 제공합니다.

* 역할 멤버 자격의 변경 내용(예: 전역 관리자 역할에 추가된 사용자)
* 자격 증명 업데이트(예: 암호 변경)
* 도메인 관리(예: 사용자 지정 도메인 확인, 도메인 제거)
* 애플리케이션 추가 또는 제거
* 사용자 관리(예: 사용자 추가, 제거 또는 업데이트)
* 라이선스 추가 또는 제거

> [!NOTE]
> [Microsoft Azure 보안 및 감사 로그 관리](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf) 를 읽고 Azure에서 로깅 기능에 대해 자세히 알아봅니다.
> [콘텐츠 관리 요구 사항 확인](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)에서 질문에 답변하는 방법에 따라 하이브리드 ID 솔루션에서 콘텐츠를 관리하는 방법을 확인할 수 있어야 합니다. 테이블 6에 노출된 모든 옵션은 Azure AD와 통합할 수 있는 반면 비즈니스 요구에 적합한 것을 정의하는 데 중요합니다.
>
>

| 콘텐츠 관리 옵션 | 장점 | 단점 |
| --- | --- | --- |
| 중앙 집중화된 온-프레미스(Active Directory Rights Management Server) |데이터의 분류를 담당하는 서버 인프라에 대한 모든 권한 <br> Windows Server의 기본 제공 기능은 추가 라이선스 또는 구독에 필요하지 않습니다. <br> 하이브리드 시나리오에서 Azure AD와 통합될 수 있습니다. <br> Exchange Online, SharePoint Online, Office 365와 같은 Microsoft 온라인 서비스에서 정보 권한 관리(IRM) 기능을 지원합니다. <br>  Exchange Server, SharePoint Server 및 Windows Server 및 파일 분류 인프라 (FCI)를 실행하는 파일 서버와 같은 온-프레미스 Microsoft 서버 제품을 지원합니다. |IT가 서버를 소유하기에 높은 유지 관리(업데이트, 구성 및 잠재적인 업그레이드를 수행) <br> 서버 인프라 온-프레미스가 필요합니다.<br> Azure 기능을 기본적으로 활용하지 않습니다 |
| 클라우드에서 중앙 집중화됨(Azure RMS) |온-프레미스 솔루션에 비해 관리가 쉽습니다 <br> 하이브리드 시나리오에서 AD DS와 통합될 수 있습니다. <br>  Azure AD와 완벽하게 통합 <br> 서비스를 배포하는 데 서버 온-프레미스가 필요하지 않습니다. <br>  Exchange Server, SharePoint Server 및 Windows Server 및 파일 분류 인프라 (FCI)를 실행하는 파일 서버와 같은 온-프레미스 Microsoft 서버 제품을 지원합니다. <br>  IT는 BYOK 기능을 가진 테넌트의 키를 완전히 제어할 수 있습니다. |조직에는 RMS를 지원하는 클라우드 구독이 있어야 합니다 <br>  조직에는 RMS에 대한 사용자 인증을 지원하는 Azure AD 디렉터리가 있어야 합니다 |
| 하이브리드(온-프레미스 Active Directory Rights Management Server와 통합된 Azure RMS) |이 시나리오는 중앙 집중화된 온-프레미스 및 클라우드 모두에서 장점을 누적합니다. |조직에는 RMS를 지원하는 클라우드 구독이 있어야 합니다 <br> 조직에는 RMS에 대한 사용자 인증을 지원하는 Azure AD 디렉터리가 있어야 합니다 <br>  Azure 클라우드 서비스와 온-프레미스 인프라 간의 연결이 필요합니다 |

## <a name="define-access-control-options"></a>액세스 제어 옵션 정의
다음 그림에 표시된 것처럼 사용자와 파트너가 Single Sign-On(SSO)를 사용하는 동안 Azure AD에서 사용할 수 있는 인증, 권한 부여 및 액세스 제어 기능을 활용하여 회사는 중앙 ID 리포지토리를 사용할 수 있게 됩니다.

![중앙 집중식 관리](./media/plan-hybrid-identity-design-considerations/centralized-management.png)

다른 디렉터리르 사용하는 중앙 집중화된 관리 및 완전한 통합

Azure Active Directory는 수천 개의 SaaS 애플리케이션 및 온-프레미스 웹 애플리케이션에 Single-Sign On을 제공합니다. Microsoft에서 테스트된 SSO 타사에 대한 자세한 내용은 [Azure Active Directory 페더레이션 호환성 목록: Single Sign-On을 구현하는 데 사용할 수 있는 타사 ID 공급자](how-to-connect-fed-compatibility.md) 문서를 참조하세요. 이 기능을 사용하면 조직은 ID 및 액세스 관리의 제어를 유지하면서 다양한 B2B 시나리오를 구현할 수 있습니다. 그러나 B2B 중에 프로세스를 설계하는 작업은 파트너에서 사용되는 인증 방법을 이해하고 이 메서드를 Azure에서 지원하는지에 대한 유효성을 검사하는 것이 중요합니다. 현재 다음 메서드가 Azure AD에서 지원됩니다.

* SAML(Security Assertion Markup Language )
* OAuth
* Kerberos
* 토큰
* 인증서

> [!NOTE]
> [Azure Active Directory 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn151124.aspx) 을 읽고 Azure에서 각 프로토콜 및 해당 기능에 대한 자세한 정보를 알아봅니다.
>
>

Azure AD 지원을 사용하여 직원이 해당 회사 Active Directory 자격 증명으로 모바일 애플리케이션에 로그인할 수 있도록 모바일 비즈니스 애플리케이션은 동일하게 쉬운 Mobile Services 인증 환경을 사용할 수 있습니다. 이 기능을 사용하여 Azure AD는 이미 지원한 다른 ID 공급자와 함께 Mobile Services에서 ID 공급자로 지원됩니다(Microsoft 계정, Facebook ID, Google ID 및 Twitter ID 포함). 온-프레미스 앱이 회사의 AD DS에 있는 사용자의 자격 증명을 사용하는 경우 클라우드에서 들어오는 파트너 및 사용자로부터의 액세스는 투명해야 합니다. (클라우드 기반) 웹 응용 프로그램, web API, Microsoft 클라우드 서비스, 타사 SaaS 응용 프로그램 및 네이티브 (모바일) 클라이언트 응용 프로그램에 대 한 사용자의 조건부 액세스 제어를 관리할 수 있으며, 한 곳에서 보안, 감사, 보고의 이점을 누릴 수 있습니다. 그러나 비-프로덕션 환경에서 또는 제한된 양의 사용자로 이 구현의 유효성을 검사하는 것이 좋습니다.

> [!TIP]
> Azure AD에는 AD DS만큼 그룹 정책이 없다는 사실에 주목합니다. 디바이스에 정책을 적용하기 위해 [Microsoft Intune](https://technet.microsoft.com/library/jj676587.aspx)과 같은 모바일 디바이스 관리 솔루션이 필요합니다.
>
>

Azure AD를 사용하여 사용자가 인증되면 사용자를 갖게 된 액세스 수준을 평가하는 것이 중요합니다. 사용자가 리소스에 대해 갖는 액세스 수준은 다를 수 있습니다. 또한 Azure AD가 일부 리소스에 대한 액세스를 제어하여 추가 보안 계층을 추가할 수 있는 반면 리소스 자체는 파일 서버에 있는 파일에 대 한 액세스 제어와 같이 고유한 액세스 제어 목록을 별도로 가질 수 있다는 점에 유의해야 합니다. 다음 그림에서는 하이브리드 시나리오에서 가질 수 있는 액세스 제어의 수준을 요약합니다.

![액세스 제어](./media/plan-hybrid-identity-design-considerations/accesscontrol.png)

그림x에서 보여준 다이어그램에서 각 상호 작용은 Azure AD에서 다룰 수 있는 하나의 액세스 제어 시나리오를 나타냅니다. 아래에서 각 시나리오에 대해 설명합니다.

1. 온-프레미스에서 호스트되는 애플리케이션에 대한 조건부 액세스: Windows Server 2012 R2에서 AD FS를 사용하도록 구성된 애플리케이션에 대한 액세스 정책이 있는 등록된 디바이스를 사용할 수 있습니다.

2. Azure Portal Access Control: azure에서 azure RBAC (역할 기반 액세스 제어)를 사용 하 여 포털에 대 한 액세스를 제어할 수도 있습니다. 이 방법을 사용하면 회사에서 개인이 Azure Portal에서 수행할 수 있는 작업의 수를 제한할 수 있습니다. Azure RBAC를 사용 하 여 포털에 대 한 액세스를 제어 함으로써 IT 관리자는 다음 액세스 관리 방법을 사용 하 여 액세스를 위임할 수 있습니다.

   - 그룹 기반 역할 할당: 로컬 Active Directory에서 동기화될 수 있는 Azure AD 그룹에 대한 액세스를 할당할 수 있습니다. 이렇게 하면 그룹 관리에 대한 도구 및 프로세스에서 조직이 만든 기존 투자를 활용할 수 있습니다. 또한 Azure AD Premium의 위임된 그룹 관리 기능을 사용할 수 있습니다.
   - Azure에서 내장된 역할 활용: 소유자, 참가자 및 판독기 등 세 가지 역할을 사용하여 사용자 및 그룹이 작업 수행에 필요한 작업만 수행할 수 있는 권한이 있도록 할 수 있습니다.
   -  리소스에 대한 세부적인 액세스: 웹사이트 또는 데이터베이스와 같은 특정 구독, 리소스 그룹 또는 개별 Azure 리소스에 대한 사용자 및 그룹에 역할을 할당할 수 있습니다. 이러한 방식으로 사용자는 필요한 모든 리소스에 대해 액세스하고 관리할 필요가 없는 리소스에 액세스하지 않을 수 있습니다.

   > [!NOTE]
   > 애플리케이션을 빌드하고 이에 대한 액세스 제어를 사용자 지정하려는 경우 권한 부여를 위해 Azure AD 애플리케이션 역할을 사용할 수 있습니다. 이 기능을 사용하여 앱을 개발하는 방법에 대해 [WebApp-RoleClaims-DotNet 예제](https://github.com/AzureADSamples/WebApp-RoleClaims-DotNet) 를 검토합니다.


3. Microsoft Intune 사용 하는 Office 365 응용 프로그램에 대 한 조건부 액세스: IT 관리자는 조건부 액세스 장치 정책을 프로 비전 하 여 회사 리소스를 보호 하는 동시에 규격 장치에서 정보 근로자가 서비스에 액세스할 수 있도록 할 수 있습니다. 
  
4. SaaS 앱에 대한 조건부 액세스: [이 기능](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/25/azure-ad-conditional-access-preview-update-more-apps-and-blocking-access-for-users-not-at-work/)을 사용하면 애플리케이션별 Multi-Factor Authentication 액세스 규칙 및 신뢰할 수 있는 네트워크에 없는 사용자에 대한 액세스 차단 기능을 구성할 수 있습니다. 애플리케이션에 할당된 모든 사용자 또는 지정된 보안 그룹 내의 사용자에 제한적으로 다단계 인증 규칙을 적용할 수 있습니다. 조직 네트워크 내부에 있는 IP 주소에서 애플리케이션에 액세스하는 경우에는 다단계 인증 요구 사항에서 제외될 수도 있습니다.

액세스 제어에 대한 옵션은 다중 계층 접근 방법을 사용하기 때문에 이러한 옵션 간의 비교는 이 작업에 적용될 수 없습니다. 리소스에 대한 액세스를 제어하기 위해 필요한 각 시나리오에 사용 가능한 모든 옵션을 활용하도록 합니다.

## <a name="define-incident-response-options"></a>인시던트 대응 옵션 정의
Azure AD는 사용자 활동을 모니터링하여 환경의 잠재적인 보안 위험을 파악하는 데 도움이 될 수 있습니다. Azure AD 액세스 및 사용 보고서를 사용하여 조직 디렉터리의 무결성 및 보안을 볼 수 있습니다 이 정보를 사용하면 IT 관리자는 가능한 보안 위험이 발생할 수 있는 위치를 보다 잘 결정하여 이러한 위험을 적절하게 완화할 수 있습니다.  [Azure AD Premium 구독](../fundamentals/active-directory-get-started-premium.md)에는 IT가 이 정보를 가져올 수 있는 보안 보고서 집합이 있습니다. [Azure AD 보고서](../reports-monitoring/overview-reports.md)는 다음과 같이 분류됩니다.

* **비정상 보고서**: 비정상으로 확인된 로그인 이벤트가 포함됩니다. 이러한 활동을 인식하고 이벤트가 의심스러운지 확인할 수 있게 해 줍니다.
* **통합 애플리케이션 보고서**: 클라우드 애플리케이션이 조직에서 사용되는 방식을 파악할 수 있게 해줍니다. Azure Active Directory는 수천 개의 클라우드 애플리케이션과 통합을 제공합니다.
* **오류 보고서**: 외부 애플리케이션에 계정을 프로비전할 때 발생할 수 있는 오류를 나타냅니다.
* **사용자별 보고서**: 특정 사용자에 대한 디바이스/로그인 활동 데이터를 표시합니다.
* **활동 로그**: 최근 24시간, 최근 7일 또는 최근 30일 이내에 감사된 모든 이벤트의 레코드와 그룹 활동 변경 사항, 암호 재설정 및 등록 활동이 포함됩니다.

> [!TIP]
> 또한 사건을 다루는 인시던트 대응 팀에게 도움이 되는 다른 보고서는 [누수된 자격 증명을 사용하는 사용자](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/15/azure-active-directory-premium-reporting-now-detects-leaked-credentials/) 보고서입니다. 이 보고서는 이러한 누수된 자격 증명 목록과 테넌트 간의 일치 항목을 표시합니다.
>


인시던트 대응 조사 중에 사용될 수 있는 다른 중요한 Azure AD의 기본 제공 보고서는 다음과 같습니다.

* **암호 재설정 작업**: 암호 재설정이 조직에서 얼마나 적극적으로 사용되는지에 대한 통찰력을 관리자에게 제공합니다.
* **암호 재설정 등록 작업**: 어떤 사용자가 암호 재설정을 위해 해당 메서드를 등록했는지와 어떤 방법을 선택했는지에 대한 통찰력을 제공합니다.
* **그룹 작업**: 액세스 패널에서 시작된 그룹에 대한 변경 기록을 제공합니다.(ex: 사용자 추가 또는 제거)

또한 인시던트 응답 조사 과정 중에 활용할 수 있는 Azure AD Premium에서 사용 가능한 핵심 보고 기능 외에도 IT는 감사 보고서를 활용하여 다음과 같은 정보를 얻을 수 있습니다.

* 역할 멤버 자격의 변경 내용(예: 전역 관리자 역할에 추가된 사용자)
* 자격 증명 업데이트(예: 암호 변경)
* 도메인 관리(예: 사용자 지정 도메인 확인, 도메인 제거)
* 애플리케이션 추가 또는 제거
* 사용자 관리(예: 사용자 추가, 제거 또는 업데이트)
* 라이선스 추가 또는 제거

인시던트 대응에 대한 옵션은 다중 계층 접근 방법을 사용하기 때문에 이러한 옵션 간의 비교는 이 작업에 적용될 수 없습니다. 회사의 인시던트 대응 프로세스의 일부로 Azure AD 보고 기능을 사용하도록 요구하는 각 시나리오에 사용 가능한 모든 옵션을 활용하도록 합니다.

## <a name="next-steps"></a>다음 단계
[하이브리드 ID 관리 작업 결정](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

## <a name="see-also"></a>관련 항목
[설계 고려 사항 개요](plan-hybrid-identity-design-considerations-overview.md)
