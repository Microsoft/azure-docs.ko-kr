---
title: 클래식 가상 네트워크에서 Azure AD Domain Services 마이그레이션 | Microsoft Docs
description: 기존 Azure AD Domain Services 관리 되는 도메인 인스턴스를 클래식 가상 네트워크 모델에서 리소스 관리자 기반 가상 네트워크로 마이그레이션하는 방법에 대해 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: iainfou
ms.openlocfilehash: bd20bb008c52b7d99416aed7a0599a6e78d2acf2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161650"
---
# <a name="migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>클래식 가상 네트워크 모델에서 리소스 관리자으로 Azure AD Domain Services 마이그레이션

Azure Active Directory Domain Services (AD DS)는 현재 클래식 가상 네트워크 모델을 사용 하는 고객이 리소스 관리자 가상 네트워크 모델에 대해 일회성 이동을 지원 합니다. 리소스 관리자 배포 모델을 사용 하는 Azure AD DS 관리 되는 도메인은 세분화 된 암호 정책, 감사 로그 및 계정 잠금 보호와 같은 추가 기능을 제공 합니다.

이 문서에서는 마이그레이션에 대 한 이점과 고려 사항을 설명 하 고 기존 Azure AD DS 인스턴스를 성공적으로 마이그레이션하는 데 필요한 단계를 설명 합니다.

## <a name="overview-of-the-migration-process"></a>마이그레이션 프로세스 개요

마이그레이션 프로세스는 클래식 가상 네트워크에서 실행 되는 기존 Azure AD DS 인스턴스를 사용 하 여 기존 리소스 관리자 가상 네트워크로 이동 합니다. 마이그레이션은 PowerShell을 사용 하 여 수행 되며 실행 *준비* 및 *마이그레이션의*두 가지 주요 단계를 포함 합니다.

![Azure AD DS에 대 한 마이그레이션 프로세스 개요](media/migrate-from-classic-vnet/migration-overview.png)

*준비* 단계에서 Azure AD DS는 도메인의 백업을 사용 하 여 관리 되는 도메인과 동기화 된 사용자, 그룹 및 암호의 최신 스냅숏을 가져옵니다. 그런 다음 동기화를 사용 하지 않도록 설정 하 고, Azure AD DS 관리 되는 도메인을 호스트 하는 클라우드 서비스가 삭제 됩니다. 준비 단계 중에 Azure AD DS 관리 되는 도메인에서 사용자를 인증할 수 없습니다.

![Azure AD DS 마이그레이션하기 위한 준비 단계](media/migrate-from-classic-vnet/migration-preparation.png)

*마이그레이션* 단계에서 클래식 Azure AD DS 관리 되는 도메인의 도메인 컨트롤러에 대 한 기본 가상 디스크가 복사 되어 리소스 관리자 배포 모델을 사용 하 여 vm을 만듭니다. 그런 다음 Azure AD DS 관리 되는 도메인은 LDAPS 및 DNS 구성을 포함 하는 다시 만들어집니다. Azure AD에 대 한 동기화가 다시 시작 되 고 LDAP 인증서가 복원 됩니다. 모든 컴퓨터를 Azure AD DS 관리 되는 도메인에 다시 가입 시킬 필요가 없습니다. 관리 되는 도메인에 계속 가입 되 고 변경 없이 실행 됩니다.

![Azure AD DS 마이그레이션](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>마이그레이션 이점

이 마이그레이션 프로세스를 사용 하 여 Azure AD DS 관리 되는 도메인으로 이동 하는 경우 컴퓨터를 관리 되는 도메인에 다시 가입 하거나 Azure AD DS 인스턴스를 삭제 하 고 처음부터 새로 만들 필요가 없습니다. Vm은 마이그레이션 프로세스가 끝날 때 Azure AD DS 관리 되는 도메인에 계속 가입 됩니다.

마이그레이션 후 Azure AD DS는 다음과 같은 리소스 관리자 가상 네트워크를 사용 하는 도메인에만 사용할 수 있는 많은 기능을 제공 합니다.

* 세분화 암호 정책 지원.
* AD 계정 잠금 보호.
* Azure AD DS 관리 되는 도메인에서 경고에 대 한 전자 메일 알림
* Azure Monitor를 사용 하 여 감사 로그
* Azure Files 통합
* HD Insights 통합

리소스 관리자 가상 네트워크를 사용 하는 Azure AD DS 관리 되는 도메인은 최신 새로운 기능을 최신 상태로 유지 하는 데 도움이 됩니다. 클래식 가상 네트워크를 사용 하는 Azure AD DS에 대 한 지원은 나중에 더 이상 사용 되지 않습니다.

## <a name="example-scenarios-for-migration"></a>마이그레이션에 대 한 예제 시나리오

Azure AD DS 관리 되는 도메인을 마이그레이션하기 위한 몇 가지 일반적인 시나리오에는 다음 예제가 포함 됩니다.

> [!NOTE]
> 성공적인 마이그레이션을 확인할 때까지 클래식 가상 네트워크를 변환 하지 마십시오. 마이그레이션 및 확인 단계 중에 문제가 발생 하는 경우 가상 네트워크를 변환 하면 Azure AD DS 관리 되는 도메인을 롤백하거나 복원 하는 옵션이 제거 됩니다.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure AD DS을 기존 리소스 관리자 virtual network로 마이그레이션 (권장)

일반적인 시나리오는 이미 다른 기존 리소스를 리소스 관리자 배포 모델 및 가상 네트워크로 이동한 경우입니다. 그런 다음 피어 링은 Azure AD DS를 계속 실행 하는 클래식 가상 네트워크에 대 한 리소스 관리자 가상 네트워크에서 사용 됩니다. 이 접근 방식을 통해 리소스 관리자 응용 프로그램 및 서비스는 클래식 가상 네트워크에서 Azure AD DS 관리 되는 도메인의 인증 및 관리 기능을 사용할 수 있습니다. 마이그레이션된 후에는 모든 리소스가 리소스 관리자 배포 모델 및 가상 네트워크를 사용 하 여 실행 됩니다.

![Azure AD DS을 기존 리소스 관리자 가상 네트워크로 마이그레이션](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

이 예의 마이그레이션 시나리오와 관련 된 개략적인 단계는 다음과 같은 부분으로 구성 됩니다.

1. 클래식 가상 네트워크에 구성 된 기존 VPN 게이트웨이 또는 가상 네트워크 피어 링을 제거 합니다.
1. 이 문서에 설명 된 단계를 사용 하 여 Azure AD DS 관리 되는 도메인을 마이그레이션합니다.
1. 성공적인 마이그레이션을 테스트 하 고 확인 한 다음 클래식 가상 네트워크를 삭제 합니다.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Azure AD DS을 포함 한 여러 리소스 마이그레이션

이 예제 시나리오에서는 Azure AD DS 및 기타 관련 된 리소스를 클래식 배포 모델에서 리소스 관리자 배포 모델로 마이그레이션합니다. Azure AD DS 관리 되는 도메인과 함께 클래식 가상 네트워크에서 일부 리소스를 계속 실행 하는 경우 리소스 관리자 배포 모델로 마이그레이션하는 것이 모든 이점을 누릴 수 있습니다.

![여러 리소스를 리소스 관리자 배포 모델로 마이그레이션](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

이 예의 마이그레이션 시나리오와 관련 된 개략적인 단계는 다음과 같은 부분으로 구성 됩니다.

1. 클래식 가상 네트워크에 구성 된 기존 VPN 게이트웨이 또는 가상 네트워크 피어 링을 제거 합니다.
1. 이 문서에 설명 된 단계를 사용 하 여 Azure AD DS 관리 되는 도메인을 마이그레이션합니다.
1. 클래식 가상 네트워크와 리소스 관리자 네트워크 간에 가상 네트워크 피어 링을 설정 합니다.
1. 성공적으로 마이그레이션을 테스트 하 고 확인 합니다.
1. [Vm과 같은 추가 클래식 리소스를 이동][migrate-iaas]합니다.

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Azure AD DS 마이그레이션 하지만 클래식 가상 네트워크에 대 한 다른 리소스 유지

이 예제 시나리오에서는 한 세션에서 최소 가동 중지 시간이 발생 합니다. Azure AD DS를 리소스 관리자 가상 네트워크로 마이그레이션하고 기존 리소스를 클래식 배포 모델 및 가상 네트워크에 보관 합니다. 다음 유지 관리 기간에는 필요에 따라 클래식 배포 모델 및 virtual network에서 추가 리소스를 마이그레이션할 수 있습니다.

![Azure AD DS 리소스 관리자 배포 모델로 마이그레이션](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

이 예의 마이그레이션 시나리오와 관련 된 개략적인 단계는 다음과 같은 부분으로 구성 됩니다.

1. 클래식 가상 네트워크에 구성 된 기존 VPN 게이트웨이 또는 가상 네트워크 피어 링을 제거 합니다.
1. 이 문서에 설명 된 단계를 사용 하 여 Azure AD DS 관리 되는 도메인을 마이그레이션합니다.
1. 클래식 가상 네트워크와 새 리소스 관리자 가상 네트워크 간의 가상 네트워크 피어 링을 설정 합니다.
1. 나중에 필요에 따라 클래식 가상 네트워크에서 [추가 리소스를 마이그레이션합니다][migrate-iaas] .

## <a name="before-you-begin"></a>시작하기 전에

Azure AD DS 관리 되는 도메인을 준비 하 고 마이그레이션하면 인증 및 관리 서비스의 가용성에 대 한 몇 가지 고려 사항이 있습니다. Azure AD DS 관리 되는 도메인은 마이그레이션하는 동안 일정 시간 동안 사용할 수 없습니다. Azure AD DS을 사용 하는 응용 프로그램 및 서비스는 마이그레이션 중 가동 중지 시간을 경험 합니다.

> [!IMPORTANT]
> 마이그레이션 프로세스를 시작 하기 전에이 마이그레이션 문서와 지침을 모두 읽어보십시오. 마이그레이션 프로세스는 시간 동안 Azure AD DS 도메인 컨트롤러의 가용성에 영향을 줍니다. 사용자, 서비스 및 응용 프로그램은 마이그레이션 프로세스 중에 관리 되는 도메인에 대해 인증할 수 없습니다.

### <a name="ip-addresses"></a>IP 주소

마이그레이션 후 Azure AD DS 관리 되는 도메인 변경에 대 한 도메인 컨트롤러 IP 주소입니다. 이 변경에는 보안 LDAP 끝점에 대 한 공용 IP 주소가 포함 됩니다. 새 IP 주소는 리소스 관리자 가상 네트워크의 새 서브넷에 대 한 주소 범위 내에 있습니다.

롤백하는 경우 IP 주소는 롤백된 후 변경 될 수 있습니다.

Azure AD DS는 일반적으로 주소 범위에서 처음 두 개의 사용 가능한 IP 주소를 사용 하지만이는 보장 되지 않습니다. 현재 마이그레이션 후에 사용할 IP 주소를 지정할 수 없습니다.

### <a name="downtime"></a>가동 중지 시간

마이그레이션 프로세스에는 도메인 컨트롤러가 일정 기간 동안 오프 라인 상태로 유지 됩니다. Azure AD DS는 리소스 관리자 배포 모델 및 가상 네트워크로 마이그레이션되면 도메인 컨트롤러에 액세스할 수 없습니다. 평균적으로 가동 중지 시간은 약 1 ~ 3 시간입니다. 이 기간은 도메인 컨트롤러를 오프 라인 상태로 전환 하는 순간부터 첫 번째 도메인 컨트롤러가 다시 온라인 상태가 되는 시간입니다. 이 평균은 두 번째 도메인 컨트롤러를 복제 하는 데 소요 되는 시간이 나 추가 리소스를 리소스 관리자 배포 모델로 마이그레이션하는 데 소요 되는 시간을 포함 하지 않습니다.

### <a name="account-lockout"></a>계정 잠금

클래식 가상 네트워크에서 실행 되는 Azure AD DS 관리 되는 도메인에는 AD 계정 잠금 정책이 적용 되지 않습니다. Vm이 인터넷에 노출 되는 경우 공격자는 암호-스프레이 메서드를 사용 하 여 계정에 무단으로 적용할 수 있습니다. 이러한 시도를 중지할 계정 잠금 정책이 없습니다. 리소스 관리자 배포 모델 및 가상 네트워크를 사용 하는 Azure AD DS 관리 되는 도메인의 경우 AD 계정 잠금 정책은 이러한 암호-스프레이 공격 으로부터 보호 합니다.

기본적으로 2 분 내에 잘못 된 암호 5 회는 30 분 동안 계정을 잠급니다.

잠긴 계정은 로그인 하는 데 사용할 수 없습니다 .이는 Azure AD DS 관리 되는 도메인 또는 계정으로 관리 되는 응용 프로그램을 관리 하는 기능을 방해할 수 있습니다. Azure AD DS 관리 되는 도메인을 마이그레이션한 후에는 로그인을 반복 해 서 실패 한 시도로 인해 영구 잠금의 원인이 되는 계정이 발생할 수 있습니다. 마이그레이션 후 두 가지 일반적인 시나리오는 다음과 같습니다.

* 만료 된 암호를 사용 하는 서비스 계정입니다.
    * 서비스 계정은 만료 된 암호를 사용 하 여 로그인을 반복적으로 시도 하 여 계정을 잠급니다. 이 문제를 해결 하려면 자격 증명이 만료 된 응용 프로그램 또는 VM을 찾아 암호를 업데이트 합니다.
* 악의적인 엔터티가 무차별 암호 대입 시도를 사용 하 여 계정에 로그인 하 고 있습니다.
    * Vm이 인터넷에 노출 되 면 공격자가 서명 하려고 할 때 일반적인 사용자 이름 및 암호 조합을 시도 하는 경우가 많습니다. 이러한 반복적인 실패 한 로그인 시도는 계정을 잠글 수 있습니다. *관리자 또는* *관리자*와 같은 일반 이름으로 관리자 계정을 사용 하지 않는 것이 좋습니다. 예를 들어 관리 계정이 잠기는 것을 최소화 하는 것이 좋습니다.
    * 인터넷에 노출 되는 Vm의 수를 최소화 합니다. Azure Portal를 사용 하 여 Vm에 안전 하 게 연결 하기 위해 [Azure 방호][azure-bastion] 를 사용할 수 있습니다.

마이그레이션 후 일부 계정이 잠길 수 있는 것으로 의심 되는 경우 최종 마이그레이션 단계에서는 감사를 사용 하도록 설정 하거나 세분화 된 암호 정책 설정을 변경 하는 방법을 간략하게 설명 합니다.

### <a name="roll-back-and-restore"></a>롤백 및 복원

마이그레이션이 실패 한 경우 Azure AD DS 관리 되는 도메인을 롤백하거나 복원 하는 프로세스가 있습니다. 롤백은 마이그레이션 시도 전에 관리 되는 도메인의 상태를 즉시 반환 하는 셀프 서비스 옵션입니다. Azure 지원 엔지니어가 마지막 수단으로 백업에서 관리 되는 도메인을 복원할 수도 있습니다. 자세한 내용은 [실패 한 마이그레이션에서 롤백 또는 복원 하는 방법](#roll-back-and-restore-from-migration)을 참조 하세요.

### <a name="restrictions-on-available-virtual-networks"></a>사용 가능한 가상 네트워크에 대 한 제한 사항

Azure AD DS 관리 되는 도메인을 마이그레이션할 수 있는 가상 네트워크에는 몇 가지 제한 사항이 있습니다. 대상 리소스 관리자 가상 네트워크는 다음 요구 사항을 충족 해야 합니다.

* 리소스 관리자 가상 네트워크는 Azure AD DS 현재 배포 된 클래식 가상 네트워크와 동일한 Azure 구독에 있어야 합니다.
* 리소스 관리자 가상 네트워크는 현재 Azure AD DS 배포 된 클래식 가상 네트워크와 동일한 지역에 있어야 합니다.
* 리소스 관리자 가상 네트워크의 서브넷에는 3-5 이상의 사용 가능한 IP 주소가 있어야 합니다.
* 리소스 관리자 가상 네트워크의 서브넷은 Azure AD DS 전용 서브넷 이어야 하 고 다른 워크 로드를 호스팅하지 않아야 합니다.

가상 네트워크 요구 사항에 대 한 자세한 내용은 [가상 네트워크 디자인 고려 사항 및 구성 옵션][network-considerations]을 참조 하세요.

## <a name="migration-steps"></a>마이그레이션 단계

리소스 관리자 배포 모델 및 가상 네트워크로의 마이그레이션은 5 가지 주요 단계로 분할 됩니다.

| 단계    | 수행한  | 예상 시간  | 가동 중지 시간  | 롤백/복원 할까요? |
|---------|--------------------|-----------------|-----------|-------------------|
| [1 단계-새 가상 네트워크 업데이트 및 찾기](#update-and-verify-virtual-network-settings) | Azure portal | 15분 | 가동 중지 시간 없음 | 해당 없음 |
| [2 단계-마이그레이션을 위해 Azure AD DS 관리 되는 도메인 준비](#prepare-the-managed-domain-for-migration) | PowerShell | 15 ~ 30 분 (평균) | 이 명령이 완료 된 후 Azure AD DS의 가동 중지 시간이 시작 됩니다. | 롤백 및 복원 사용 가능. |
| [3 단계-Azure AD DS 관리 되는 도메인을 기존 가상 네트워크로 이동](#migrate-the-managed-domain) | PowerShell | 1 ~ 3 시간 (평균) | 이 명령이 완료 되 면 하나의 도메인 컨트롤러를 사용할 수 있으며 가동 중지 시간이 종료 됩니다. | 오류가 발생 하면 롤백 (셀프 서비스) 및 복원을 모두 사용할 수 있습니다. |
| [4 단계-복제본 도메인 컨트롤러 테스트 및 대기](#test-and-verify-connectivity-after-the-migration)| PowerShell 및 Azure Portal | 테스트 수에 따라 1 시간 이상 | 두 도메인 컨트롤러를 모두 사용할 수 있으며 정상적으로 작동 해야 합니다. | 해당 없음. 첫 번째 VM이 성공적으로 마이그레이션되면 롤백 또는 복원에 대 한 옵션이 없습니다. |
| [5 단계-선택적 구성 단계](#optional-post-migration-configuration-steps) | Azure Portal 및 Vm | 해당 없음 | 가동 중지 시간 없음 | 해당 없음 |

> [!IMPORTANT]
> 추가 가동 중지 시간을 방지 하려면 마이그레이션 프로세스를 시작 하기 전에이 마이그레이션 문서와 지침을 모두 읽으십시오. 마이그레이션 프로세스는 일정 시간 동안 Azure AD DS 도메인 컨트롤러의 가용성에 영향을 줍니다. 사용자, 서비스 및 응용 프로그램은 마이그레이션 프로세스 중에 관리 되는 도메인에 대해 인증할 수 없습니다.

## <a name="update-and-verify-virtual-network-settings"></a>가상 네트워크 설정 업데이트 및 확인

마이그레이션 프로세스를 시작 하기 전에 다음 초기 검사 및 업데이트를 완료 합니다. 이러한 단계는 마이그레이션 전에 언제 든 지 발생할 수 있으며, Azure AD DS 관리 되는 도메인의 작업에 영향을 주지 않습니다.

1. 로컬 Azure PowerShell 환경을 최신 버전으로 업데이트 합니다. 마이그레이션 단계를 완료 하려면 버전 *2.3.2*이상이 필요 합니다.

    PowerShell 버전을 확인 하 고 업데이트 하는 방법에 대 한 자세한 내용은 [Azure PowerShell 개요][azure-powershell]를 참조 하세요.

1. 기존 리소스 관리자 가상 네트워크를 만들거나 선택 합니다.

    네트워크 설정이 Azure AD DS에 필요한 포트를 차단 하지 않는지 확인 합니다. 포트는 클래식 가상 네트워크와 리소스 관리자 가상 네트워크에서 모두 열어야 합니다. 이러한 설정에는 경로 테이블을 사용 하지 않는 것이 좋지만 경로 테이블은 사용 하지 않는 것이 좋지만 네트워크 보안 그룹도 있습니다.

    필요한 포트를 보려면 [네트워크 보안 그룹 및 필요한 포트][network-ports]를 참조 하세요. 네트워크 통신 문제를 최소화 하려면 마이그레이션이 성공적으로 완료 된 후에 네트워크 보안 그룹 또는 경로 테이블을 대기 하 고 리소스 관리자 가상 네트워크에 적용 하는 것이 좋습니다.

    이 대상 리소스 그룹, 대상 가상 네트워크 및 대상 가상 네트워크 서브넷을 기록해 둡니다. 이러한 리소스 이름은 마이그레이션 프로세스 중에 사용 됩니다.

1. Azure Portal에서 Azure AD DS 관리 되는 도메인 상태를 확인 합니다. 관리 되는 도메인에 대 한 경고가 있는 경우 마이그레이션 프로세스를 시작 하기 전에이를 해결 하십시오.
1. 필요에 따라 다른 리소스를 리소스 관리자 배포 모델 및 가상 네트워크로 이동 하려는 경우 해당 리소스를 마이그레이션할 수 있는지 확인 합니다. 자세한 내용은 [클래식에서 리소스 관리자 IaaS 리소스의 플랫폼 지원 마이그레이션][migrate-iaas]을 참조 하세요.

    > [!NOTE]
    > 클래식 가상 네트워크를 리소스 관리자 가상 네트워크로 변환 하지 마세요. 이렇게 하면 Azure AD DS 관리 되는 도메인을 롤백하거나 복원할 수 있는 옵션이 없습니다.

## <a name="prepare-the-managed-domain-for-migration"></a>관리 되는 도메인의 마이그레이션 준비

Azure PowerShell은 마이그레이션을 위해 Azure AD DS 관리 되는 도메인을 준비 하는 데 사용 됩니다. 이러한 단계는 백업을 수행 하 고, 동기화를 일시 중지 하 고, Azure AD DS를 호스트 하는 클라우드 서비스를 삭제 하는 것입니다. 이 단계가 완료 되 면 Azure AD DS는 일정 시간 동안 오프 라인으로 전환 됩니다. 준비 단계가 실패 하면 [이전 상태로 롤백할](#roll-back)수 있습니다.

마이그레이션을 위해 Azure AD DS 관리 되는 도메인을 준비 하려면 다음 단계를 완료 합니다.

1. [PowerShell 갤러리][powershell-script]에서 `Migrate-Aaads` 스크립트를 설치 합니다. 이 PowerShell 마이그레이션 스크립트는 Azure AD 엔지니어링 팀에서 디지털 서명 하는 것입니다.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. [가져오기-자격][get-credential] 증명 cmdlet을 사용 하 여 마이그레이션 스크립트에서 자격 증명을 저장할 변수를 만듭니다.

    사용자가 지정 하는 사용자 계정에 azure AD 테 넌 트의 *전역 관리자* 권한이 있어야 azure AD DS를 사용 하도록 설정 하 고 azure 구독에서 *참가자* 권한을 사용 하 여 필요한 azure AD DS 리소스를 만들 수 있습니다.

    메시지가 표시 되 면 적절 한 사용자 계정 및 암호를 입력 합니다.

    ```powershell
    $creds = Get-Credential
    ```

1. 이제 *-Prepare* 매개 변수를 사용 하 여 `Migrate-Aadds` cmdlet을 실행 합니다. *Contoso.com*와 같은 사용자 고유의 Azure AD DS 관리 되는 도메인에 대 한 *-ManagedDomainFqdn* 을 제공 합니다.

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>관리 되는 도메인 마이그레이션

Azure AD DS 관리 되는 도메인을 준비 하 고 백업 하면 도메인을 마이그레이션할 수 있습니다. 이 단계에서는 리소스 관리자 배포 모델을 사용 하 여 Azure AD Domain Services 도메인 컨트롤러 Vm을 다시 만듭니다. 이 단계를 완료 하는 데 1 ~ 3 시간이 걸릴 수 있습니다.

*-Commit* 매개 변수를 사용 하 여 `Migrate-Aadds` cmdlet을 실행 합니다. *Contoso.com*와 같은 이전 섹션에서 준비한 Azure AD DS 관리 되는 도메인에 대 한 *-ManagedDomainFqdn* 을 제공 합니다.

Azure AD DS을 마이그레이션할 가상 네트워크를 포함 하는 대상 리소스 그룹 (예: *Myresourcegroup*)을 지정 합니다. *Myvnet*과 같은 대상 가상 네트워크와 *domainservices*와 같은 서브넷을 제공 합니다.

이 명령이 실행 된 후에는 롤백할 수 없습니다.

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

스크립트가 관리 되는 도메인의 마이그레이션을 위해 준비 되었는지 확인 한 후 *Y* 를 입력 하 여 마이그레이션 프로세스를 시작 합니다.

> [!IMPORTANT]
> 마이그레이션 프로세스 중에 클래식 가상 네트워크를 리소스 관리자 가상 네트워크로 변환 하지 마세요. 가상 네트워크를 변환 하는 경우 원래 가상 네트워크가 더 이상 존재 하지 않으므로 Azure AD DS 관리 되는 도메인을 롤백 또는 복원할 수 없습니다.

마이그레이션 프로세스 중 2 분 마다 진행률 표시기는 다음 예제 출력과 같이 현재 상태를 보고 합니다.

![Azure AD DS 마이그레이션의 진행률 표시기](media/migrate-from-classic-vnet/powershell-migration-status.png)

PowerShell 스크립트를 닫는 경우에도 마이그레이션 프로세스는 계속 실행 됩니다. Azure Portal에서 관리 되는 도메인의 상태는 *마이그레이션*중으로 보고 됩니다.

마이그레이션이 성공적으로 완료 되 면 Azure Portal 또는 Azure PowerShell를 통해 첫 번째 도메인 컨트롤러의 IP 주소를 볼 수 있습니다. 사용할 수 있는 두 번째 도메인 컨트롤러의 시간 추정치도 표시 됩니다.

이 단계에서는 필요에 따라 클래식 배포 모델 및 가상 네트워크에서 다른 기존 리소스를 이동할 수 있습니다. 또는 Azure AD DS 마이그레이션이 완료 된 후에 클래식 배포 모델의 리소스를 유지 하 고 가상 네트워크를 서로 연결할 수 있습니다.

## <a name="test-and-verify-connectivity-after-the-migration"></a>마이그레이션 후 연결 테스트 및 확인

두 번째 도메인 컨트롤러를 성공적으로 배포 하 고 Azure AD DS 관리 되는 도메인에서 사용할 수 있는 데 약간의 시간이 걸릴 수 있습니다.

리소스 관리자 배포 모델을 사용 하면 Azure AD DS 관리 되는 도메인에 대 한 네트워크 리소스가 Azure Portal 또는 Azure PowerShell에 표시 됩니다. 이러한 네트워크 리소스에 대해 자세히 알아보려면 [Azure AD DS에서 사용 하는 네트워크 리소스][network-resources]를 참조 하세요.

하나 이상의 도메인 컨트롤러를 사용할 수 있는 경우 Vm과 네트워크 연결에 대 한 다음 구성 단계를 완료 합니다.

* **DNS 서버 설정 업데이트** 리소스 관리자 가상 네트워크의 다른 리소스를 확인 하 고 Azure AD DS 관리 되는 도메인을 사용 하도록 하려면 새 도메인 컨트롤러의 IP 주소로 DNS 설정을 업데이트 합니다. Azure Portal에서 자동으로 이러한 설정을 구성할 수 있습니다. 리소스 관리자 가상 네트워크를 구성 하는 방법에 대 한 자세한 내용은 [Azure virtual network에 대 한 DNS 설정 업데이트][update-dns]를 참조 하세요.
* **도메인에 가입 된 Vm 다시 시작** -Azure AD DS 도메인 컨트롤러에 대 한 DNS 서버 IP 주소가 변경 되 면 도메인에 가입 된 vm을 다시 시작 하 여 새 dns 서버 설정을 사용 합니다. 응용 프로그램 또는 Vm에서 수동으로 DNS 설정을 구성한 경우 Azure Portal에 표시 된 도메인 컨트롤러의 새 DNS 서버 IP 주소를 사용 하 여 수동으로 업데이트 합니다.

이제 가상 네트워크 연결 및 이름 확인을 테스트 합니다. 리소스 관리자 가상 네트워크에 연결 된 VM 또는 피어 링 VM에서 다음 네트워크 통신 테스트를 시도 합니다.

1. 도메인 컨트롤러 중 하나의 IP 주소를 ping 할 수 있는지 확인 합니다 (예: `ping 10.1.0.4`
    * 도메인 컨트롤러의 IP 주소는 Azure Portal의 Azure AD DS 관리 되는 도메인에 대 한 **속성** 페이지에 표시 됩니다.
1. 관리 되는 도메인의 이름 확인 (예: `nslookup contoso.com`)을 확인 합니다.
    * 사용자 고유의 Azure AD DS 관리 되는 도메인에 대 한 DNS 이름을 지정 하 여 DNS 설정이 올바른지 확인 하 고 확인 합니다.

두 번째 도메인 컨트롤러는 마이그레이션 cmdlet이 완료 된 후 1-2 시간 후에 사용할 수 있어야 합니다. 두 번째 도메인 컨트롤러를 사용할 수 있는지 확인 하려면 Azure Portal에서 Azure AD DS 관리 되는 도메인에 대 한 **속성** 페이지를 확인 합니다. 두 개의 IP 주소가 표시 되 면 두 번째 도메인 컨트롤러가 준비 된 것입니다.

## <a name="optional-post-migration-configuration-steps"></a>선택적 마이그레이션 후 구성 단계

마이그레이션 프로세스가 성공적으로 완료 되 면 일부 선택적 구성 단계에서 감사 로그 또는 전자 메일 알림을 사용 하도록 설정 하거나 세분화 된 암호 정책을 업데이트 합니다.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Azure Monitor를 사용 하 여 감사 로그 구독

Azure AD DS는 도메인 컨트롤러에서 문제를 해결 하 고 이벤트를 볼 수 있도록 감사 로그를 노출 합니다. 자세한 내용은 [사용 및 감사 로그 사용][security-audits]을 참조 하세요.

템플릿을 사용 하 여 로그에 노출 되는 중요 한 정보를 모니터링할 수 있습니다. 예를 들어 감사 로그 통합 문서 템플릿은 Azure AD DS 관리 되는 도메인에서 가능한 계정 잠금을 모니터링할 수 있습니다.

### <a name="configure-azure-ad-domain-services-email-notifications"></a>전자 메일 알림 Azure AD Domain Services 구성

Azure AD DS 관리 되는 도메인에서 문제가 검색 되 면 알림을 받으려면 Azure Portal에서 전자 메일 알림 설정을 업데이트 합니다. 자세한 내용은 [알림 설정 구성][notifications]을 참조 하세요.

### <a name="update-fine-grained-password-policy"></a>세분화 암호 정책 업데이트

필요한 경우 세분화 된 암호 정책을 기본 구성 보다 덜 제한적으로 업데이트할 수 있습니다. 감사 로그를 사용 하 여 덜 제한적인 설정이 적합 한지 확인 한 다음 필요에 따라 정책을 구성할 수 있습니다. 다음 개략적인 단계를 사용 하 여 마이그레이션 후 반복적으로 잠긴 계정의 정책 설정을 검토 하 고 업데이트 합니다.

1. Azure AD DS 관리 되는 도메인에 대 한 제한 사항을 줄이고 감사 로그의 이벤트를 관찰 하는 [암호 정책을 구성][password-policy] 합니다.
1. 서비스 계정에서 감사 로그에 확인 된 대로 만료 된 암호를 사용 하는 경우 해당 계정을 올바른 암호로 업데이트 합니다.
1. VM이 인터넷에 노출 되는 경우 로그인을 많이 시도 하는 *관리자*, *사용자*또는 *게스트* 와 같은 일반 계정 이름을 검토 합니다. 가능 하면 보다 일반적으로 명명 된 계정을 사용 하도록 해당 Vm을 업데이트 합니다.
1. VM의 네트워크 추적을 사용 하 여 공격의 원인을 찾고 해당 IP 주소에서 로그인을 시도할 수 없도록 차단 합니다.
1. 잠금 문제가 최소화 되 면 세분화 된 암호 정책을 필요한 만큼 제한적으로 업데이트 합니다.

### <a name="creating-a-network-security-group"></a>네트워크 보안 그룹 만들기

Azure AD DS에는 관리 되는 도메인에 필요한 포트를 보호 하 고 들어오는 모든 트래픽을 차단 하는 네트워크 보안 그룹이 필요 합니다. 이 네트워크 보안 그룹은 관리 되는 도메인에 대 한 액세스를 잠그기 위한 추가 보호 계층 역할을 하며 자동으로 생성 되지 않습니다. 네트워크 보안 그룹을 만들고 필요한 포트를 열려면 다음 단계를 검토 합니다.

1. Azure Portal에서 Azure AD DS 리소스를 선택 합니다. 개요 페이지에는 Azure AD Domain Services와 연결 되어 있지 않은 경우 네트워크 보안 그룹을 만드는 단추가 표시 됩니다.
1. 보안 LDAP를 사용 하는 경우 *TCP* 포트 *636*에 들어오는 트래픽을 허용 하도록 네트워크 보안 그룹에 규칙을 추가 합니다. 자세한 내용은 [보안 LDAP 구성][secure-ldap]을 참조 하세요.

## <a name="roll-back-and-restore-from-migration"></a>마이그레이션에서 롤백 및 복원

마이그레이션 프로세스의 특정 시점까지 Azure AD DS 관리 되는 도메인을 롤백하거나 복원 하도록 선택할 수 있습니다.

### <a name="roll-back"></a>롤백

3 단계에서 마이그레이션을 준비 하기 위해 PowerShell cmdlet을 실행 하는 동안 오류가 발생 하는 경우 Azure AD DS 관리 되는 도메인에서 원래 구성으로 롤백할 수 있습니다. 이 롤백에는 원래 클래식 가상 네트워크가 필요 합니다. 롤백 후에도 IP 주소는 변경 될 수 있습니다.

*-Abort* 매개 변수를 사용 하 여 `Migrate-Aadds` cmdlet을 실행 합니다. *Contoso.com*와 같은 이전 섹션에서 준비한 Azure AD DS 관리 되는 도메인에 대 한 *-ManagedDomainFqdn* 및 *myClassicVnet*와 같은 클래식 가상 네트워크 이름을 제공 합니다.

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds
```

### <a name="restore"></a>복원

마지막 수단으로, Azure AD Domain Services은 마지막으로 사용 가능한 백업에서 복원할 수 있습니다. 마이그레이션의 1 단계에서 백업을 수행 하 여 최신 백업을 사용할 수 있는지 확인 합니다. 이 백업은 30 일 동안 저장 됩니다.

Azure AD DS 관리 되는 도메인을 백업에서 복원 하려면 [Azure Portal를 사용 하 여 지원 사례 티켓을 엽니다][azure-support]. 디렉터리 ID, 도메인 이름 및 복원 이유를 제공 합니다. 지원 및 복원 프로세스를 완료 하는 데 몇 일이 걸릴 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

리소스 관리자 배포 모델로 마이그레이션한 후에 문제가 발생 하는 경우 다음과 같은 일반적인 문제 해결 영역 중 일부를 검토 합니다.

* [도메인 가입 문제 해결][troubleshoot-domain-join]
* [계정 잠금 문제 해결][troubleshoot-account-lockout]
* [계정 로그인 문제 해결][troubleshoot-sign-in]
* [보안 LDAP 연결 문제 해결][tshoot-ldaps]

## <a name="next-steps"></a>다음 단계

Azure AD DS 관리 되는 도메인을 리소스 관리자 배포 모델로 마이그레이션하고 [WINDOWS VM을 만들고 도메인에 가입한][join-windows] 다음 [관리 도구를 설치][tutorial-create-management-vm]합니다.

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/
