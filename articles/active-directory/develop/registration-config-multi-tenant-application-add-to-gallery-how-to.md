---
title: Azure AD 응용 프로그램 갤러리에 다중 테넌트 앱 추가 | Microsoft Docs
description: 사용자 지정 개발된 다중 테넌트 애플리케이션을 Azure AD 애플리케이션 갤러리에서 나열하는 방법을 설명합니다.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: be660ad42c1336d479f1793b20d2994682db1225
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702762"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리에 다중 테넌트 애플리케이션 추가

## <a name="what-is-the-azure-ad-application-gallery"></a>Azure AD 애플리케이션 갤러리란?

Azure AD(Azure Active Directory)는 클라우드 기반 ID 서비스입니다. [Azure AD 애플리케이션 갤러리](https://azure.microsoft.com/marketplace/active-directory/all/)는 모든 애플리케이션 커넥터가 SSO(Single Sign-On) 및 사용자 프로비전을 위해 게시되는 Azure Marketplace 앱 스토어에 위치합니다. Azure AD를 ID 공급자로 사용하는 고객은 여기에서 게시된 다른 SaaS 애플리케이션 커넥터를 찾습니다. IT 관리자는 앱 갤러리의 커넥터를 추가한 다음, SSO(Single Sign-On) 및 프로비전을 위해 커넥터를 구성하고 사용합니다. Azure AD는 SSO(Single Sign-On)에 대해 SAML 2.0, OpenID Connect, OAuth 및 WS-Fed와 같은 주요 페더레이션 프로토콜을 모두 지원합니다. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>애플리케이션이 SAML 또는 OpenIDConnect를 지원하는 경우
Azure AD 애플리케이션 갤러리에 나열하려는 다중 테넌트 애플리케이션이 있는 경우 먼저 애플리케이션이 다음 Single Sign-On 기술 중 하나를 지원하는지 확인해야 합니다.

- **OpenID Connect**: 앱을 나열하려면 Azure AD에서 다중 테넌트 애플리케이션을 만들고 애플리케이션에 [Azure AD 승인 프레임워크](https://docs.microsoft.com/azure/active-directory/develop/consent-framework)를 구현합니다. 모든 고객이 애플리케이션에 동의를 제공할 수 있도록 공통 엔드포인트에 로그인 요청을 보냅니다. 토큰에 수신된 테넌트 ID 및 사용자의 UPN을 기반으로 사용자의 액세스를 제어할 수 있습니다. [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)에 설명된 프로세스를 사용하여 애플리케이션을 제출합니다.

- **SAML**: 애플리케이션이 SAML 2.0을 지원하는 경우 앱을 갤러리에 나열할 수 있습니다. [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)의 지침을 따릅니다.

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>애플리케이션이 SAML 또는 OpenIDConnect를 지원하지 않는 경우
SAML 또는 OpenIDConnect를 지원하지 않는 애플리케이션이라도 암호 Single Sign-On 기술을 통해 애플리케이션 갤러리에 통합할 수 있습니다.

암호 Single Sign-On('암호 저장'이라고도 함)을 사용하면 ID 페더레이션을 지원하지 않는 웹 애플리케이션에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에도 유용합니다. 

이 기술을 사용하여 애플리케이션을 나열하려면 다음을 수행합니다.
1. HTML 로그인 페이지가 있는 웹 애플리케이션을 만들어서 [암호 SSO(Single Sign-On)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 구성합니다. 
2. [Azure Active Directory 애플리케이션 갤러리에 애플리케이션 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)에 설명된 대로 요청을 제출합니다.

## <a name="escalations"></a>에스컬레이션

모든 에스컬레이션은 [Azure AD SSO 통합 팀](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)으로 이메일을 보내주시면 최대한 신속히 연락드리겠습니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory 애플리케이션 갤러리에 애플리케이션을 나열](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)하는 방법을 알아봅니다.
