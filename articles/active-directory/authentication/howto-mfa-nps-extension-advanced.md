---
title: Azure MFA NPS 확장 구성-Azure Active Directory
description: NPS 확장을 설치한 후에는 허용 되는 IP 목록 및 UPN 교체와 같은 고급 구성에 대해 다음 단계를 사용 합니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7dab1d9e46aec64cc3c0fda9e8e6ba503f696b0
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716761"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Multi-Factor Authentication에 대한 NPS 확장을 위한 고급 구성 옵션

NPS(네트워크 정책 서버) 확장은 온-프레미스 인프라에 클라우드 기반 Azure Multi-Factor Authentication 기능을 확장합니다. 이 문서에서는 이미 확장이 설치되어 있으며 현재 사용자 요구에 따라 확장을 사용자 지정하는 방법에 대해 알기 원한다고 가정합니다. 

## <a name="alternate-login-id"></a>대체 로그인 ID

NPS 확장은 온-프레미스와 클라우드 디렉터리 모두에 연결하므로 온-프레미스 UPN(사용자 계정 이름)이 클라우드의 이름과 일치하지 않는 문제가 발생할 수 있습니다. 이 문제를 해결하려면 대체 로그인 ID를 사용합니다. 

NPS 확장 내에서 Azure Multi-Factor Authentication에 대한 UPN 대신 사용할 Active Directory 특성을 지정할 수 있습니다. 그러면 온-프레미스 UPN을 수정하지 않고 2단계 인증을 사용하여 사용자의 온-프레미스 리소스를 보호할 수 있습니다. 

대체 로그인 ID를 구성하려면 `HKLM\SOFTWARE\Microsoft\AzureMfa`로 이동하여 다음 레지스트리 값을 편집합니다.

| 이름 | Type | 기본값 | Description |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | 문자열 | Empty | UPN 대신 사용하려는 Active Directory 특성의 이름을 지정합니다. 이 특성은 AlternateLoginId 특성으로 사용됩니다. 이 레지스트리 값이 [유효한 Active Directory 특성](/windows/win32/adschema/attributes-all)(예: 메일 또는 displayName)으로 설정되어 있는 경우 인증용 사용자의 UPN 대신 특성의 값이 사용됩니다. 이 레지스트리 값이 비어 있거나 구성되어 있지 않으면 AlternateLoginId가 비활성화되고 사용자의 UPN이 인증에 사용됩니다. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | 거짓 | 이 플래그를 사용하여 AlternateLoginId를 조회할 때 LDAP 검색에 글로벌 카탈로그를 강제 사용합니다. 도메인 컨트롤러를 글로벌 카탈로그로 구성하고, 글로벌 카탈로그에 AlternateLoginId 특성을 추가한 다음, 이 플래그를 사용하도록 설정합니다. <br><br> LDAP_LOOKUP_FORESTS가 구성된 경우(비어 있지 않음), 레지스트리 설정의 값에 관계 없이 **이 플래그는 true로 적용**됩니다. 이 경우 NPS 확장에서 글로벌 카탈로그를 각 포리스트에 대한 AlternateLoginId 특성으로 구성해야 합니다. |
| LDAP_LOOKUP_FORESTS | 문자열 | Empty | 검색할 포리스트의 목록을 세미콜론으로 구분된 형태로 제공합니다. 예를 들어 *contoso.com;foobar.com*과 같습니다. 이 레지스트리 값이 구성된 경우 NPS 확장은 반복적으로 모든 포리스트를 나열된 순서대로 검색하고 첫 번째 성공적인 AlternateLoginId 값을 반환합니다. 이 레지스트리 값이 구성되지 않은 경우 AlternateLoginId 조회는 현재 도메인으로 제한됩니다.|

대체 로그인 ID에 관한 문제를 해결하려면 [대체 로그인 ID 오류](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)를 위한 권장 단계를 사용합니다.

## <a name="ip-exceptions"></a>IP 예외

부하 분산 장치에서 워크로드를 보내기 전에 실행 중인 서버를 확인하는 경우와 같이 서버 가용성을 모니터링해야 하는 경우, 이러한 검사는 확인 요청에 의해 차단되지 않아야 합니다. 대신, 서비스 계정에서 사용되는 것으로 알고 있는 IP 주소의 목록을 만들고 해당 목록에 대한 Multi-Factor Authentication 요구 사항을 사용하지 않도록 설정합니다.

IP 허용 목록을 구성 하려면로 이동 하 여 `HKLM\SOFTWARE\Microsoft\AzureMfa` 다음 레지스트리 값을 구성 합니다.

| 이름 | Type | 기본값 | Description |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | 문자열 | Empty | IP 주소 목록을 세미콜론으로 구분된 형태로 제공합니다. NAS/VPN 서버와 같이 서비스 요청이 발생한 컴퓨터의 IP 주소가 포함됩니다. IP 범위 및 서브넷은 지원 되지 않습니다. <br><br> 예: *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> 이 레지스트리 키는 설치 관리자에 의해 기본적으로 생성 되지 않으며, 서비스가 다시 시작 될 때 AuthZOptCh 로그에 오류가 나타납니다. 로그에서이 오류는 무시 해도 되지만, 필요 하지 않은 경우에는이 레지스트리 키가 비어 있는 경우 빈 상태로 남아 있으면 오류 메시지가 반환 되지 않습니다.

에 있는 IP 주소에서 요청이 들어오면 `IP_WHITELIST` 2 단계 인증을 건너뜁니다. IP 목록은 RADIUS 요청의 *ratNASIPAddress* 특성에 제공 된 ip 주소와 비교 됩니다. RADIUS 요청이 ratNASIPAddress 특성 없이 들어오는 경우 다음과 같은 경고가 로그됩니다. “P_WHITE_LIST_WARNING::원본 IP가 NasIpAddress 특성의 RADIUS 요청에서 누락되어 IP 허용 목록이 무시됩니다.”

## <a name="next-steps"></a>다음 단계

[Azure Multi-factor Authentication용 NPS 확장의 오류 메시지 해결](howto-mfa-nps-extension-errors.md)