---
title: Azure AD Domain Services에 대한 관리 개념 Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인을 관리 하는 방법 및 사용자 계정 및 암호의 동작에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 682935fa2324b8de4992ab2f90c7f71e05c4f8ac
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76931576"
---
# <a name="management-concepts-for-user-accounts-passwords-and-administration-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services의 사용자 계정, 암호 및 관리에 대한 관리 개념

Azure Active Directory Domain Services (AD DS) 관리 되는 도메인을 만들고 실행할 때 기존 온-프레미스 AD DS 환경에 비해 동작이 약간 다릅니다. Azure AD DS에서 자체 관리 되는 도메인으로 동일한 관리 도구를 사용할 수 있지만 DC (도메인 컨트롤러)에 직접 액세스할 수는 없습니다. 또한 사용자 계정 생성의 원본에 따라 암호 정책 및 암호 해시에 대한 동작의 차이가 있습니다.

이 개념 문서에서는 Azure AD DS 관리 되는 도메인을 관리 하는 방법과 사용자 계정이 생성 되는 방식에 따라 사용자 계정의 다른 동작에 대해 자세히 설명 합니다.

## <a name="domain-management"></a>도메인 관리

Azure AD DS에서 사용자, 그룹, 자격 증명 및 정책과 같은 모든 리소스를 포함 하는 Dc (도메인 컨트롤러)는 관리 서비스의 일부입니다. 중복성을 위해 두 Dc가 Azure AD DS 관리 되는 도메인의 일부로 생성 됩니다. 이러한 Dc에 로그인 하 여 관리 작업을 수행할 수는 없습니다. 대신 Azure AD DS 관리 되는 도메인에 가입 된 관리 VM을 만든 다음 일반 AD DS 관리 도구를 설치 합니다. 예를 들어 Active Directory 관리 센터 또는 그룹 정책 DNS (Microsoft Management Console) 스냅인과 같은 MMC (Microsoft Management Console) 스냅인을 사용할 수 있습니다.

## <a name="user-account-creation"></a>사용자 계정 만들기

여러 가지 방법으로 Azure AD DS에서 사용자 계정을 만들 수 있습니다. 대부분의 사용자 계정은 Azure AD에서 동기화 되며, 온-프레미스 AD DS 환경에서 동기화 된 사용자 계정을 포함할 수도 있습니다. 수동으로 계정을 Azure AD DS에서 직접 만들 수도 있습니다. 초기 암호 동기화 또는 암호 정책과 같은 일부 기능은 사용자 계정을 만드는 방법 및 위치에 따라 다르게 작동 합니다.

* 사용자 계정은 Azure AD에서 동기화 할 수 있습니다. 여기에는 Azure AD에서 직접 만든 클라우드 전용 사용자 계정과 Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 된 하이브리드 사용자 계정이 포함 됩니다.
    * Azure AD DS의 대부분 사용자 계정은 Azure AD의 동기화 프로세스를 통해 생성 됩니다.
* 사용자 계정은 Azure AD DS 관리 되는 도메인에서 수동으로 만들 수 있으며 Azure AD에 존재 하지 않습니다.
    * Azure AD DS 에서만 실행 되는 응용 프로그램에 대한 서비스 계정을 만들어야 하는 경우 관리 되는 도메인에서 수동으로 만들 수 있습니다. Azure AD에서 동기화는 한 가지 방법으로 azure AD DS에서 만든 사용자 계정은 Azure AD에 다시 동기화 되지 않습니다.

## <a name="password-policy"></a>암호 정책

Azure AD DS에는 계정 잠금, 최대 암호 사용 기간 및 암호 복잡성과 같은 항목에 대한 설정을 정의 하는 기본 암호 정책이 포함 됩니다. 계정 잠금 정책과 같은 설정은 이전 섹션에 설명 된 대로 사용자를 만든 방법에 관계 없이 Azure AD DS의 모든 사용자에 게 적용 됩니다. 최소 암호 길이 및 암호 복잡성과 같은 몇 가지 설정은 Azure AD DS에서 직접 만든 사용자 에게만 적용 됩니다.

사용자 고유의 사용자 지정 암호 정책을 만들어 Azure AD DS의 기본 정책을 재정의할 수 있습니다. 그런 다음 필요에 따라 특정 사용자 그룹에 이러한 사용자 지정 정책을 적용할 수 있습니다.

사용자 생성의 원본에 따라 암호 정책을 적용 하는 방법의 차이점에 대한 자세한 내용은 [관리 되는 도메인의 암호 및 계정 잠금 정책][password-policy]을 참조 하세요.

## <a name="password-hashes"></a>암호 해시

관리되는 도메인에서 사용자를 인증하려면 Azure AD DS에 NTLM(NT LAN Manager) 및 Kerberos 인증에 적합한 형식의 암호 해시가 필요합니다. Azure AD DS를 테넌트에서 사용하도록 설정할 때까지 Azure AD는 NTLM 또는 Kerberos 인증에 필요한 형식의 암호 해시를 생성하거나 저장하지 않습니다. 또한 보안상의 이유로 Azure AD는 암호 자격 증명을 일반 텍스트 형식으로 저장하지 않습니다. 따라서 Azure AD는 사용자의 기존 자격 증명에 따라 이러한 NTLM 또는 Kerberos 암호 해시를 자동으로 생성할 수 없습니다.

클라우드 전용 사용자 계정의 경우 Azure AD DS를 사용하려면 먼저 사용자가 암호를 변경해야 합니다. 이 암호 변경 프로세스로 인해 Kerberos 및 NTLM 인증용 암호 해시가 생성되어 Azure AD에 저장됩니다.

Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 된 사용자의 경우 [암호 해시 동기화를 사용 하도록 설정][hybrid-phs]합니다.

> [!IMPORTANT]
> Azure AD Connect는 azure AD 테넌트에 대해 Azure AD DS를 사용 하도록 설정 하는 경우에만 레거시 암호 해시를 동기화 합니다. Azure AD Connect 사용 하 여 온-프레미스 AD DS 환경을 Azure AD와 동기화 하는 경우에는 레거시 암호 해시가 사용 되지 않습니다.
>
> 레거시 응용 프로그램이 NTLM 인증 또는 LDAP 단순 바인딩을 사용 하지 않는 경우 Azure AD DS에 대해 NTLM 암호 해시 동기화를 사용 하지 않도록 설정 하는 것이 좋습니다. 자세한 내용은 [약한 암호 그룹 및 NTLM 자격 증명 해시 동기화 사용 안 함][secure-domain]을 참조 하세요.

적절히 구성되면 사용 가능한 암호 해시가 Azure AD DS 관리형 도메인에 저장됩니다. Azure AD DS 관리형 도메인을 삭제하면 해당 지점에 저장된 암호 해시도 모두 삭제됩니다. 나중에 Azure AD DS 관리 되는 도메인을 만드는 경우 Azure AD의 동기화 된 자격 증명 정보를 다시 사용할 수 없습니다. 암호 해시를 다시 저장 하려면 암호 해시 동기화를 다시 구성 해야 합니다. 이전에는 도메인 조인 VM 또는 사용자가 즉시 인증할 수 없었으므로 Azure AD에서 암호 해시를 새 Azure AD DS 관리형 도메인에 생성하고 저장해야 합니다. 자세한 내용은 [Azure AD DS 및 Azure AD Connect에 대한 암호 해시 동기화 프로세스][azure-ad-password-sync]를 참조하세요.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 Azure AD DS 관리형 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

## <a name="forests-and-trusts"></a>포리스트 및 트러스트

*포리스트* 는 Active Directory Domain Services (AD DS)에서 하나 이상의 *도메인*을 그룹화 하는 데 사용 되는 논리적 구문입니다. 그런 다음 도메인은 사용자 또는 그룹에 대한 개체를 저장 하 고 인증 서비스를 제공 합니다.

Azure AD DS에서 포리스트는 도메인을 하나만 포함 합니다. 온-프레미스 AD DS 포리스트에는 여러 도메인이 포함 되는 경우가 많습니다. 특히 인수 및 합병 후에 규모가 많은 조직의 경우 여러 온-프레미스 포리스트가 있을 수 있으며, 각 포리스트는 여러 도메인을 포함 합니다.

기본적으로 Azure AD DS 관리 되는 도메인은 *사용자* 포리스트로 생성 됩니다. 이 유형의 포리스트는 온-프레미스 AD DS 환경에서 만든 모든 사용자 계정을 포함하여 Azure AD의 모든 개체를 동기화합니다. 사용자 계정은 도메인에 가입 된 VM에 로그인 하는 등 Azure AD DS 관리 되는 도메인에 대해 직접 인증할 수 있습니다. 사용자 포리스트는 암호 해시를 동기화 할 수 있고 사용자가 스마트 카드 인증과 같은 독점적인 로그인 방법을 사용 하지 않는 경우 작동 합니다.

Azure AD DS *리소스* 포리스트에서는 사용자가 온-프레미스 AD DS에서 단방향 포리스트 *트러스트* 를 통해 인증 합니다. 이 방법을 사용 하면 사용자 개체 및 암호 해시가 Azure AD DS에 동기화 되지 않습니다. 사용자 개체 및 자격 증명은 온-프레미스 AD DS에만 존재 합니다. 이 접근 방식을 통해 기업은 Azure에서 LDAPS, Kerberos 또는 NTLM 등의 클래식 인증에 의존 하는 리소스 및 응용 프로그램 플랫폼을 호스트할 수 있지만 모든 인증 문제나 문제는 제거 됩니다. Azure AD DS 리소스 포리스트는 현재 미리 보기로 제공 됩니다.

Azure AD DS의 포리스트 형식에 대한 자세한 내용은 [리소스 포리스트 란 무엇 인가요?][concepts-forest] [azure AD DS에서 포리스트 트러스트는 어떻게 작동 하나요?][concepts-trust] 를 참조 하세요.

## <a name="azure-ad-ds-skus"></a>Azure AD DS Sku

Azure AD DS에서 사용 가능한 성능 및 기능은 SKU를 기반으로 합니다. 관리 되는 도메인을 만들 때 SKU를 선택 하 고 관리 되는 도메인이 배포 된 후 비즈니스 요구 사항이 변경 되 면 Sku를 전환할 수 있습니다. 다음 표에서는 사용 가능한 Sku와 해당 Sku 간의 차이점을 간략하게 설명 합니다.

| SKU name   | 최대 개체 수 | Backup 주기 | 최대 아웃 바운드 포리스트 트러스트 수 |
|------------|----------------------|------------------|----|
| Standard   | 제한 없음            | 7 일 마다     | 0  |
| 엔터프라이즈 | 제한 없음            | 3 일 마다     | 5  |
| 프리미엄    | 제한 없음            | 매일            | 10 |

이러한 Azure AD DS Sku 이전에는 Azure AD DS 관리 되는 도메인의 개체 (사용자 및 컴퓨터 계정) 수를 기반으로 하는 청구 모델이 사용 되었습니다. 관리 되는 도메인의 개체 수를 기반으로 하는 변수 가격은 더 이상 없습니다.

자세한 내용은 [Azure AD DS 가격 책정 페이지][pricing]를 참조 하세요.

### <a name="managed-domain-performance"></a>관리 되는 도메인 성능

도메인 성능은 응용 프로그램에 대해 인증을 구현 하는 방법에 따라 달라 집니다. 추가 계산 리소스는 쿼리 응답 시간을 향상 시키고 동기화 작업에 소요 되는 시간을 줄이는 데 도움이 될 수 있습니다. SKU 수준이 늘어나면 관리 되는 도메인에 사용할 수 있는 계산 리소스가 늘어납니다. 응용 프로그램의 성능을 모니터링 하 고 필요한 리소스를 계획 합니다.

비즈니스 또는 응용 프로그램 요구 사항이 변경 되 고 Azure AD DS 관리 되는 도메인에 대한 추가 계산 성능이 필요한 경우 다른 SKU로 전환할 수 있습니다.

### <a name="backup-frequency"></a>Backup 주기

백업 빈도는 관리 되는 도메인의 스냅숏을 만드는 빈도를 결정 합니다. 백업은 Azure 플랫폼에서 관리 하는 자동화 된 프로세스입니다. 관리 되는 도메인에 문제가 발생 하는 경우 Azure 지원은 백업에서 복원 하는 데 도움이 될 수 있습니다. 동기화는 azure AD *에서* 한 가지 방법으로 수행 되므로 azure AD DS 관리 되는 도메인의 모든 문제는 azure ad 또는 온-프레미스 AD DS 환경 및 기능에 영향을 주지 않습니다.

SKU 수준이 늘어나면 이러한 백업 스냅숏 빈도가 높아집니다. 비즈니스 요구 사항 및 RPO (복구 지점 목표)를 검토 하 여 관리 되는 도메인에 필요한 백업 빈도를 결정 합니다. 비즈니스 또는 응용 프로그램 요구 사항이 변경 되 고 더 자주 백업 해야 하는 경우 다른 SKU로 전환할 수 있습니다.

### <a name="outbound-forests"></a>아웃 바운드 포리스트

이전 섹션에서는 Azure AD DS 관리 되는 도메인에서 온-프레미스 AD DS 환경 (현재 미리 보기로 제공)으로 트러스트 하는 방법에 대해 자세히 설명 합니다. SKU는 Azure AD DS 관리 되는 도메인에 대해 만들 수 있는 최대 포리스트 트러스트 수를 결정 합니다. 비즈니스 및 응용 프로그램 요구 사항을 검토 하 여 실제로 필요한 신뢰의 수를 확인 하 고 적절 한 Azure AD DS SKU를 선택 합니다. 비즈니스 요구 사항이 변경 되 고 추가 포리스트 트러스트를 만들어야 하는 경우에는 다른 SKU로 전환할 수 있습니다.

## <a name="next-steps"></a>다음 단계

시작 하려면 [Azure AD DS 관리 되는 도메인을 만듭니다][create-instance].

<!-- INTERNAL LINKS -->
[password-policy]: password-policy.md
[hybrid-phs]: tutorial-configure-password-hash-sync.md#enable-synchronization-of-password-hashes
[secure-domain]: secure-your-domain.md
[azure-ad-password-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[create-instance]: tutorial-create-instance.md
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
