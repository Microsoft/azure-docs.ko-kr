---
title: 앱과 Azure AD 통합 시작 | Microsoft Docs
description: 이 문서는 온-프레미스 애플리케이션 및 클라우드 애플리케이션과 Azure Active Directory(AD)를 통합하는 시작 가이드입니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc691148723e610f0885f7c20fd8684f11a81dc0
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182329"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>애플리케이션과 Azure Active Directory 통합 시작 가이드

이 항목에서는 Azure AD(Active Directory)와 애플리케이션을 통합하기 위한 프로세스를 간단히 설명합니다. 아래의 각 섹션에는 더 자세한 항목의 요약이 포함되므로 이 시작 가이드의 어떤 부분이 사용자와 관련 있는지 식별할 수 있습니다.

자세한 배포 계획을 다운로드하려면 [다음 단계](#next-steps)를 참조하세요.

## <a name="take-inventory"></a>인벤토리 가져오기
애플리케이션을 Azure AD와 통합하기 전에 현재 위치와 목표를 아는 것이 중요합니다.  다음 질문은 Azure AD 애플리케이션 통합 프로젝트에 대해 생각해볼 수 있도록 하기 위한 용도로 제공됩니다.

### <a name="application-inventory"></a>애플리케이션 인벤토리
* 애플리케이션은 모두 어디에 있습니까? 누가 소유합니까?
* 애플리케이션에 어떤 종류의 인증이 필요합니까?
* 누가 어떤 애플리케이션에 액세스하려 합니까?
* 새 애플리케이션을 배포하시겠습니까?
  * 사내에 구축하고 Azure 컴퓨팅 인스턴스에 배포하시겠습니까?
  * Azure 애플리케이션 갤러리에서 사용할 수 있는 애플리케이션을 사용하시겠습니까?

### <a name="user-and-group-inventory"></a>사용자 및 그룹 인벤토리
* 사용자 계정은 어디에 있습니까?
  * 온-프레미스 Active Directory
  * Azure AD
  * 사용자가 소유한 별도 애플리케이션 데이터베이스 내에서
  * 허용되지 않은 애플리케이션에서
  * 위 항목 모두
* 개별 사용자는 현재 어떤 사용 권한 및 역할 할당을 가지고 있습니까? 액세스를 검토해야 하거나 사용자 액세스 및 역할 할당이 적절하다고 생각합니까?
* 그룹은 온-프레미스 Active Directory 내에 만들어 집니까?
  * 그룹을 어떻게 구성합니까?
  * 그룹 멤버는 누구입니까?
  * 그룹은 현재 어떤 사용 권한/역할 할당을 가지고 있습니까?
* 통합하기 전에 사용자/그룹 데이터베이스를 정리해야 합니까?  (매우 중요한 질문입니다. 쓰레기를 넣고 쓰레기를 얻는 현상.)

### <a name="access-management-inventory"></a>액세스 관리 인벤토리
* 애플리케이션에 대한 사용자 액세스를 현재 어떻게 관리합니까? 변경해야 합니까?  예를 들어 [AZURE RBAC](../../role-based-access-control/role-assignments-portal.md) 를 사용 하는 등의 다른 방법으로 액세스를 관리할 수 있나요?
* 누구에게 어떤 액세스가 필요합니까?

아마 이러한 모든 질문에 대한 답변은 없겠지만 괜찮습니다.  이 가이드는 이러한 일부 질문에 대답하고 합리적인 결정을 내릴 수 있습니다.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Cloud Discovery를 사용하여 허용되지 않은 클라우드 애플리케이션 찾기

위에서 설명한 대로 지금까지 조직에서 관리하지 않은 애플리케이션이 있을 수 있습니다.  인벤토리 프로세스의 일부로 허용되지 않은 클라우드 애플리케이션을 찾을 수 있습니다. [Cloud Discovery 설정](/cloud-app-security/set-up-cloud-discovery)을 참조하세요.

## <a name="integrating-applications-with-azure-ad"></a>Azure AD와 애플리케이션 통합
다음 문서에서는 애플리케이션을 Azure AD와 통합하는 여러 가지 방법을 설명하고 일부 지침을 제공합니다.

* [사용할 Active Directory 결정](../fundamentals/active-directory-administer.md)
* [Azure 애플리케이션 갤러리에서 애플리케이션 사용](what-is-single-sign-on.md)
* [SaaS 애플리케이션 통합 자습서 목록](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>인증 유형
애플리케이션에는 각자 다른 인증 요구 사항이 있을 수 있습니다. Azure AD과 함께 인증서 서명은 SAML 2.0, WS-페더레이션 또는 OpenID 연결 프로토콜 뿐만 아니라 암호 Single Sign-on을 사용하는 애플리케이션을 사용하는 애플리케이션과 사용될 수 있습니다. Azure AD와 함께 사용할 애플리케이션 인증 형식에 대한 자세한 내용은 [Azure Active Directory에서 페더레이션된 Single Sign-on에 대한 인증서 관리](manage-certificates-for-federated-single-sign-on.md) 및 [암호 기반 Single Sign On](what-is-single-sign-on.md)을 참조하세요.

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Azure AD 앱 프록시를 사용하는 SSO 사용
Microsoft Azure AD 애플리케이션 프록시를 사용하여 어디서든 어떤 디바이스에서든 안전하게 프라이빗 네트워크 내부에 위치한 애플리케이션에 액세스를 제공할 수 있습니다. 환경 내에서 애플리케이션 프록시 커넥터를 설치한 후에 Azure AD를 이용하여 쉽게 구성될 수 있습니다.

### <a name="integrating-custom-applications"></a>사용자 지정 애플리케이션 통합
새 응용 프로그램을 작성 하 고 개발자가 Azure AD의 기능을 활용 하도록 지원 하려면 [개발자 가이드](../active-directory-applications-guiding-developers-for-lob-applications.md)를 참조 하세요.

Azure 애플리케이션 갤러리에 사용자 지정 애플리케이션을 추가하려는 경우 [Azure AD 셀프 서비스 SAML 구성을 사용하여 "앱 가져오기"](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/)를 참조하세요.

## <a name="managing-access-to-applications"></a>애플리케이션에 대한 액세스 관리
다음 문서는 애플리케이션이 Azure AD와 통합되면 Azure AD 커넥터 및 Azure AD를 사용하여 애플리케이션에 대한 액세스를 관리할 수 있는 방법을 설명합니다.

* [Azure AD를 사용하는 앱에 대한 액세스 관리](what-is-access-management.md)
* [Azure AD 커넥터를 사용하여 자동화](../app-provisioning/user-provisioning.md)
* [애플리케이션에 사용자 지정](../active-directory-applications-guiding-developers-assigning-users.md)
* [응용 프로그램에 그룹 할당](../active-directory-applications-guiding-developers-assigning-groups.md)
* [계정 공유](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>다음 단계
자세한 내용은 [GitHub](https://aka.ms/deploymentplans)에서 Azure Active Directory 배포 계획을 다운로드할 수 있습니다. 갤러리 응용 프로그램의 경우 [Azure Portal](https://portal.azure.com)를 통해 Single Sign-On, 조건부 액세스 및 사용자 프로 비전을 위한 배포 계획을 다운로드할 수 있습니다. 

Azure Portal에서 배포 계획을 다운로드하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **엔터프라이즈 응용 프로그램**선택  |  **앱**  |  **배포 계획**을 선택 합니다.

[배포 계획 조사](https://aka.ms/DeploymentPlanFeedback)를 수행하여 배포 계획에 대한 피드백을 제공하세요.
