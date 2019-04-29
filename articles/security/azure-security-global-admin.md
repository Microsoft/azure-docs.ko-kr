---
title: 모든 Azure 관리자에 대한 MFA 활성화
description: 전역 관리자 활성화에 대한 지침
ms.service: security
author: barclayn
manager: barbkess
editor: TomSh
ms.topic: article
ms.date: 03/20/2018
ms.author: barclayn
ms.openlocfilehash: d0479b834f814616e44a1888ab8b958990610820
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60611711"
---
# <a name="enforce-multi-factor-authentication-mfa-for-subscription-administrators"></a>구독 관리자에 대해 MFA(Multi-Factor Authentication) 적용

전역 관리자 계정을 포함하여 관리자를 만들 때 반드시 매우 강력한 인증 메서드를 사용해야 합니다.

필요에 따라 IT 직원의 사용자 계정에 관리자 또는 암호 관리자 변경과 같은 특정 관리자 역할을 할당하여 일상적인 관리를 수행할 수 있습니다.
또한 관리자에 대한 [Azure MFA(Multi-Factor Authentication)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication)를 활성화하면 사용자 로그인 및 트랜잭션에 두 번째 보안 계층을 추가합니다. 또한 Azure MFA를 사용하면 IT는 손상된 자격 증명이 조직의 데이터에 액세스하는 가능성을 줄일 수 있습니다.

예: 사용자에 대해 Azure MFA를 적용하고 전화 통화 또는 문자 메시지를 검증 수단으로 사용하도록 구성합니다. 사용자의 자격 증명이 손상된 경우 공격자는 사용자의 전화에 대한 액세스 권한이 없으므로 리소스에 액세스할 수 없습니다. 추가적인 ID 보호 계층을 추가하지 않는 조직은 자격 증명 도난 공격에 취약하며, 이로 인해 데이터가 손상될 수 있습니다.

전체 인증 제어를 온-프레미스에 유지하려는 조직이 선택할 수 있는 대안 중 하나는 "MFA 온-프레미스"라고도 하는 [Azure Multi-Factor Authentication 서버](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-server)를 사용하는 것입니다. 이 방법을 사용하면 여전히 Multi-factor Authentication을 적용하면서도 MFA 서버를 온-프레미스에 유지할 수 있습니다.

조직에서 관리자 권한이 있는 사용자를 확인하려면 Microsoft Azure AD V2 PowerShell 명령을 따라 확인할 수 있습니다.

```azurepowershell-interactive
Get-AzureADDirectoryRole | Where { $_.DisplayName -eq "Company Administrator" } | Get-AzureADDirectoryRoleMember | Ft DisplayName
```

## <a name="enabling-mfa"></a>MFA 활성화

계속 진행하기 전에 [MFA](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-whats-next) 작동 방법을 확인합니다.

사용자가 Azure Multi-Factor Authentication을 포함하는 라이선스를 가지고 있는 한 Azure MFA를 설정하도록 수행할 필요가 없습니다. 개별 사용자 단위로 2단계 인증 요구를 시작할 수 있습니다. Azure MFA를 활성화하는 라이선스는 다음과 같습니다.

- Azure Multi-Factor Authentication
- Azure Active Directory Premium
- Enterprise Mobility + Security

## <a name="turn-on-two-step-verification-for-users"></a>사용자에 대한 2단계 확인을 설정합니다.

사용자 또는 그룹에 대해 [2단계 인증을 요구하는 방법](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)에 나열된 절차 중 하나를 사용하여 Azure MFA 사용을 시작합니다. 모든 로그인에 대해 2단계 인증을 적용하거나 문제가 될 때만 2단계 인증을 요구하는 조건부 액세스 정책을 만들도록 선택할 수 있습니다.

