---
title: Azure AD Domain Services에서 계정 잠금 문제 해결 | Microsoft Docs
description: Azure Active Directory Domain Services에서 사용자 계정을 잠그기 위해 발생 하는 일반적인 문제를 해결 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 29789f299f266c86d719d56cfbf8e262907f7264
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827091"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인의 계정 잠금 문제 해결

악의적인 로그인을 반복적으로 시도 하지 않도록 하기 위해 Azure AD DS는 정의 된 임계값 이후에 계정을 잠급니다. 이 계정 잠금은 로그인 공격 인시던트 없이 우연히도 발생할 수 있습니다. 예를 들어 사용자가 반복 해 서 잘못 된 암호를 입력 하거나 서비스에서 이전 암호를 사용 하려고 하면 계정이 잠깁니다.

이 문제 해결 문서에서는 계정 잠금이 발생 하는 이유와 동작을 구성 하는 방법과 보안 감사를 검토 하 여 잠금 이벤트 문제를 해결 하는 방법을 설명 합니다.

## <a name="what-is-an-account-lockout"></a>계정 잠금 이란?

실패 한 로그인 시도에 대해 정의 된 임계값이 충족 되 면 Azure AD DS의 사용자 계정이 잠깁니다. 이 계정 잠금 동작은 자동 디지털 공격을 나타낼 수 있는 무작위 로그인 시도를 반복 해 서 방지 하도록 설계 되었습니다.

**기본적으로 2 분 내에 잘못 된 암호를 5 번 시도 하면 계정이 30 분 동안 잠깁니다.**

기본 계정 잠금 임계값은 세분화 된 암호 정책을 사용 하 여 구성 됩니다. 특정 요구 사항 집합이 있는 경우 이러한 기본 계정 잠금 임계값을 재정의할 수 있습니다. 그러나 계정 잠금의 수를 줄이기 위해 임계값 제한을 늘리는 것은 권장 되지 않습니다. 먼저 계정 잠금 동작의 원본 문제를 해결 합니다.

### <a name="fine-grained-password-policy"></a>세분화 되는 암호 정책

Fgpp입니다 (세분화 된 암호 정책)를 사용 하면 암호 및 계정 잠금 정책에 대한 특정 제한을 도메인의 다른 사용자에 게 적용할 수 있습니다. FGPP는 Azure AD DS에서 만든 사용자 에게만 영향을 줍니다. Azure AD에서 Azure AD DS 관리 되는 도메인으로 동기화 되는 클라우드 사용자 및 도메인 사용자는 암호 정책의 영향을 받지 않습니다.

정책은 Azure AD DS 관리 되는 도메인의 그룹 연결을 통해 배포 되 고 변경 내용은 다음 사용자 로그인 시 적용 됩니다. 정책을 변경 하면 이미 잠겨 있는 사용자 계정의 잠금이 해제 되지 않습니다.

세분화 되는 암호 정책에 대한 자세한 내용은 [암호 및 계정 잠금 정책 구성][configure-fgpp]을 참조 하세요.

## <a name="common-account-lockout-reasons"></a>일반적인 계정 잠금 이유

악의적인 의도 나 요인을 제외 하 고 계정이 잠기는 가장 일반적인 이유는 다음과 같습니다.

* **사용자가 자신을 잠 궜 습니다.**
    * 최근 암호를 변경한 후 사용자가 이전 암호를 계속 사용 하 고 있습니까? 사용자가 실수로 이전 암호를 다시 시도 하 여 5 분 이내에 실패 한 5 번의 기본 계정 잠금 정책이 발생할 수 있습니다.
* **이전 암호를 포함 하는 응용 프로그램 또는 서비스가 있습니다.**
    * 응용 프로그램 또는 서비스에서 계정을 사용 하는 경우 해당 리소스는 이전 암호를 사용 하 여 반복적으로 로그인을 시도할 수 있습니다. 이 동작으로 인해 계정이 잠깁니다.
    * 여러 다른 응용 프로그램 또는 서비스에서 계정 사용을 최소화 하 고 자격 증명이 사용 되는 위치를 기록 합니다. 계정 암호가 변경 된 경우 연결 된 응용 프로그램 또는 서비스를 적절 하 게 업데이트 합니다.
* **다른 환경에서 암호가 변경 되었으며 새 암호가 아직 동기화 되지 않았습니다.**
    * 프레미스 AD DS 환경에서와 같이 Azure AD DS 외부에서 계정 암호를 변경 하는 경우 Azure AD를 통해 azure AD DS를 통해 암호 변경을 동기화 하는 데 몇 분 정도 걸릴 수 있습니다.
    * 암호 동기화 프로세스가 완료 되기 전에 Azure AD DS를 통해 리소스에 로그인 하려고 시도 하는 사용자의 계정이 잠깁니다.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>보안 감사를 사용 하 여 계정 잠금 문제 해결

계정 잠금 이벤트가 발생 하는 경우와 이러한 이벤트가 발생 하는 위치를 해결 하려면 [Azure AD DS에 대한 보안 감사를 사용 하도록 설정 합니다 (현재 미리 보기 상태)][security-audit-events]. 감사 이벤트는 기능을 사용 하도록 설정한 시간에만 캡처됩니다. 문제를 해결 하기 위해 계정 잠금 문제가 발생 *하기 전에* 보안 감사를 사용 하도록 설정 하는 것이 가장 좋습니다. 사용자 계정에 잠금 문제가 반복적으로 발생 하는 경우 다음에 상황이 발생할 때 보안 감사를 준비 하도록 설정할 수 있습니다.

보안 감사를 사용 하도록 설정 하면 다음 샘플 쿼리에서 *계정 잠금 이벤트*, 코드 *4740*을 검토 하는 방법을 보여 줍니다.

지난 7 일간의 모든 계정 잠금 이벤트를 표시 합니다.

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

*Driley*이라는 계정에 대해 지난 7 일간의 모든 계정 잠금 이벤트를 표시 합니다.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

2019 년 6 월 26 일 오전 9 시 사이의 모든 계정 잠금 이벤트 보기 날짜 및 시간을 기준으로 하 여 2019 년 7 월 1 일에서 내림차순으로 정렬 됨:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

## <a name="next-steps"></a>다음 단계

계정 잠금 임계값을 조정 하는 세분화 되는 암호 정책에 대한 자세한 내용은 [암호 및 계정 잠금 정책 구성][configure-fgpp]을 참조 하세요.

Azure AD DS 관리 되는 도메인에 VM을 가입 하는 데 문제가 있는 경우 [도움말을 찾아 Azure Active Directory에 대한 지원 티켓을 여세요][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
