---
title: Azure AD 애플리케이션 프록시에 대한 PowerShell 샘플
description: Azure AD 애플리케이션 프록시에 대해 이러한 PowerShell 샘플을 사용하여 디렉터리의 애플리케이션 프록시 앱 및 커넥터에 대한 정보를 가져오고, 앱에 사용자 및 그룹을 할당하고, 인증서 정보를 가져옵니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57efd88b9d194442920496107d4c37ba2baf94d3
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85554990"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제

다음 표에서는 Azure AD 애플리케이션 프록시의 PowerShell 스크립트 예제에 대한 링크를 포함합니다. 이러한 샘플에는 별도로 지정되지 않은 경우 [그래프 모듈에 대한 AzureAD V2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) 또는 [그래프 모듈에 대한 AzureAD V2 PowerShell 미리 보기 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)이 필요합니다.


이러한 샘플에 사용된 cmdlet에 대한 자세한 내용은 [애플리케이션 프록시 애플리케이션 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management) 및 [애플리케이션 프록시 커넥터 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)를 참조하세요.

| 링크 | 설명 |
|---|---|
|**애플리케이션 프록시 앱**||
| [모든 애플리케이션 프록시 앱에 대한 기본 정보 나열](scripts/powershell-get-all-app-proxy-apps-basic.md) | 디렉터리의 모든 애플리케이션 프록시 앱에 대한 기본 정보(AppId, DisplayName, ObjId)를 나열합니다. |
| [모든 애플리케이션 프록시 앱에 대한 확장 정보 나열](scripts/powershell-get-all-app-proxy-apps-extended.md) | 디렉터리의 모든 애플리케이션 프록시 앱에 대한 확장 정보(AppId, DisplayName, ExternalUrl, InternalUrl, ExternalAuthenticationType)를 나열합니다.  |
| [커넥터 그룹별로 모든 애플리케이션 프록시 앱 나열](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 디렉터리의 모든 애플리케이션 프록시 앱에 대한 정보 및 앱이 할당된 커넥터 그룹을 나열합니다. |
| [토큰 수명 정책을 사용하여 모든 애플리케이션 프록시 앱 가져오기](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 토큰 수명 정책 및 세부 정보를 사용하여 디렉터리의 모든 애플리케이션 프록시 앱을 나열합니다. 이 샘플을 사용하려면 [그래프 모듈에 대한 AzureAD V2 PowerShell 미리 보기 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)이 필요합니다. |
|**커넥터 그룹**||
| [디렉터리의 모든 커넥터 그룹 및 커넥터 가져오기](scripts/powershell-get-all-connectors.md) | 디렉터리의 커넥터 그룹 및 커넥터를 모두 나열합니다. |
| [커넥터 그룹에 할당된 모든 앱을 다른 커넥터 그룹으로 이동](scripts/powershell-move-all-apps-to-connector-group.md) | 현재 커넥터 그룹에 할당된 모든 애플리케이션을 다른 커넥터 그룹으로 이동합니다. |
|**할당된 사용자 및 그룹**||
| [애플리케이션 프록시 애플리케이션에 할당된 사용자 및 그룹 표시](scripts/powershell-display-users-group-of-app.md) | 특정 애플리케이션 프록시 애플리케이션에 할당된 사용자 및 그룹을 나열합니다. |
| [애플리케이션에 사용자 할당](scripts/powershell-assign-user-to-app.md) | 애플리케이션에 특정 사용자를 할당합니다. |
| [애플리케이션에 그룹 할당](scripts/powershell-assign-group-to-app.md) | 애플리케이션에 특정 그룹을 할당합니다. |
|**외부 URL 구성**||
| [기본 도메인(.msappproxy.net)을 사용하여 모든 애플리케이션 프록시 앱 가져오기](scripts/powershell-get-all-default-domain-apps.md)  | 기본 도메인(.msappproxy.net)을 사용하여 모든 애플리케이션 프록시 애플리케이션을 나열합니다. |
| [와일드카드 게시를 사용하여 모든 애플리케이션 프록시 앱 가져오기](scripts/powershell-get-all-wildcard-apps.md) | 와일드카드 게시를 사용하여 모든 애플리케이션 프록시 앱을 나열합니다. |
|**사용자 지정 도메인 구성**||
| [사용자 지정 도메인 및 인증서 정보를 사용하여 모든 애플리케이션 프록시 앱 가져오기](scripts/powershell-get-all-custom-domains-and-certs.md) | 사용자 지정 도메인 및 사용자 지정 도메인과 연결된 인증서 정보를 사용하여 모든 애플리케이션 프록시 앱을 나열합니다. |
| [인증서를 업로드하지 않고 게시된 모든 Azure AD 프록시 애플리케이션 앱 가져오기](scripts/powershell-get-all-custom-domain-no-cert.md) | 사용자 지정 도메인을 사용하지만 유효한 TLS/SSL 인증서를 업로드하지 않은 모든 애플리케이션 프록시 앱을 나열합니다. |
| [동일한 인증서로 게시된 모든 Azure AD 프록시 애플리케이션 앱 가져오기](scripts/powershell-get-custom-domain-identical-cert.md) | 동일한 인증서를 사용하여 게시된 모든 Azure AD 프록시 애플리케이션 앱을 나열합니다. |
| [동일한 인증서로 게시된 모든 Azure AD 프록시 애플리케이션 앱을 가져와 바꾸기](scripts/powershell-get-custom-domain-replace-cert.md) | 동일한 인증서를 사용하여 게시된 Azure AD 프록시 애플리케이션 앱의 경우 인증서를 대량으로 바꿀 수 있습니다. |
