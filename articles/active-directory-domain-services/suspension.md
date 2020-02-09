---
title: Azure AD Domain Services에서 일시 중단 된 도메인 | Microsoft Docs
description: Azure AD DS 관리 되는 도메인에 대한 다양 한 상태와 일시 중단 된 도메인을 복원 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95e1d8da-60c7-4fc1-987d-f48fde56a8cb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 106cd870a8ba52917ecabe6266bbb2c376296d79
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74704270"
---
# <a name="understand-the-health-states-and-resolve-suspended-domains-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에서 성능 상태를 이해 하 고 일시 중단 된 도메인을 해결 합니다.

Azure AD DS(Azure Active Directory Domain Services)가 관리되는 도메인을 오랫동안 서비스할 수 없는 경우 관리되는 도메인이 일시 중단됨 상태로 전환됩니다. 그런 다음 관리 되는 도메인이 일시 중단 된 상태로 유지 되 면 자동으로 삭제 됩니다. Azure AD DS 관리 되는 도메인을 정상 상태로 유지 하 고 일시 중단을 방지 하려면 가능 하면 신속 하 게 경고를 해결 하세요.

이 문서에서는 관리 되는 도메인이 일시 중단 된 이유와 일시 중단 된 도메인을 복구 하는 방법을 설명 합니다.

## <a name="overview-of-managed-domain-states"></a>관리 되는 도메인 상태 개요

Azure AD DS 관리 되는 도메인의 수명 주기를 통해 상태를 나타내는 다양 한 상태가 있습니다. 관리 되는 도메인에서 문제를 보고 하는 경우 기본 원인을 신속 하 게 해결 하 여 상태를 계속 저하 되지 않도록 합니다.

![Azure AD DS 관리 되는 도메인에서 일시 중단을 수행 하는 상태가 진행 됨](media/active-directory-domain-services-suspension/suspension-timeline.PNG)

Azure AD DS 관리 되는 도메인은 다음 상태 중 하나일 수 있습니다.

* [실행 중](#running-state)
* [주의가 필요 함](#needs-attention-state)
* [일시](#suspended-state)
* [삭제됨](#deleted-state)

## <a name="running-state"></a>실행 상태

올바르게 구성 되 고 문제 없이 실행 되는 Azure AD DS 관리 되는 도메인이 *실행 중* 상태입니다. 이는 관리 되는 도메인에 대한 원하는 상태입니다.

### <a name="what-to-expect"></a>예상 프로그램

* Azure 플랫폼은 관리 되는 도메인의 상태를 정기적으로 모니터링할 수 있습니다.
* 관리 되는 도메인의 도메인 컨트롤러는 패치 및 정기적으로 업데이트 됩니다.
* Azure Active Directory 변경 내용은 관리 되는 도메인에 정기적으로 동기화 됩니다.
* 정기 백업은 관리 되는 도메인에 대해 수행 됩니다.

## <a name="needs-attention-state"></a>주의 상태 필요

해결 해야 하는 하나 이상의 문제를 포함 하는 Azure AD DS 관리 되는 도메인은 *주의 필요* 상태입니다. 관리 되는 도메인의 상태 페이지에 경고가 표시 되 고 문제가 발생 한 위치가 표시 됩니다. 일부 경고는 일시적 이며 Azure 플랫폼에서 자동으로 해결 됩니다. 다른 경고의 경우 제공 된 해결 단계를 수행 하 여 문제를 해결할 수 있습니다. 중요 한 경고가 발생 하 고 추가 문제 해결 지원을 위해 [Azure 지원 요청을 여세요][azure-support] .

경고의 한 가지 예는 제한적인 네트워크 보안 그룹이 있는 경우입니다. 이 구성에서 Azure 플랫폼은 관리 되는 도메인을 업데이트 하 고 모니터링 하지 못할 수 있습니다. 경고가 생성 되 고 상태가 *주의가 필요*로 변경 됩니다.

자세한 내용은 [Azure AD DS 관리 되는 도메인에 대한 경고 문제를 해결 하는 방법][resolve-alerts]을 참조 하세요.

### <a name="what-to-expect"></a>예상 프로그램

Azure AD DS 관리 되는 도메인이 *주의 필요* 상태에 있는 경우 azure 플랫폼에서 정기적으로 데이터를 모니터링, 패치, 업데이트 또는 백업 하지 못할 수 있습니다. 잘못 된 네트워크 구성을 사용 하는 경우와 같이 관리 되는 도메인에 대한 도메인 컨트롤러에 연결할 수 없는 경우가 있습니다.

* 관리 되는 도메인은 비정상 상태 이며 경고가 해결 될 때까지 진행 중인 상태 모니터링이 중지 될 수 있습니다.
* 관리 되는 도메인에 대한 도메인 컨트롤러를 패치 또는 업데이트할 수 없습니다.
* Azure Active Directory 변경 내용은 관리 되는 도메인에 동기화 되지 않을 수 있습니다.
* 관리 되는 도메인에 대한 백업을 수행할 수 없습니다.
* 관리 되는 도메인에 영향을 주는 중요 하지 않은 경고를 해결 하는 경우 상태가 *실행 중* 상태로 돌아옵니다.
* Azure 플랫폼에서 도메인 컨트롤러에 연결할 수 없는 구성 문제에 대한 중요 한 알림이 트리거됩니다. 이러한 중요 한 경고가 15 일 내에 해결 되지 않으면 관리 되는 도메인이 *일시 중단* 상태가 됩니다.

## <a name="suspended-state"></a>일시 중단 상태

Azure AD DS 관리 되는 도메인은 다음 이유 중 하나로 인해 **일시 중단** 된 상태가 됩니다.

* 15일 이내에 해결되지 않은 중요한 경고가 하나 이상 있습니다.
    * 중요한 경고는 Azure AD DS에 필요한 리소스에 대한 액세스를 차단하는 잘못된 구성으로 인해 발생할 수 있습니다. 관리되는 도메인에서 [AADDS104: 네트워크 오류][alert-nsg] 경고가 16일 이상 해결되지 않은 경우를 예로 들 수 있습니다.
* Azure 구독에 대한 청구 문제가 있거나 Azure 구독이 만료 되었습니다.

관리 되는 도메인은 Azure 플랫폼에서 도메인을 관리, 모니터링, 패치 또는 백업할 수 없을 때 일시 중단 됩니다. 관리 되는 도메인은 15 일 동안 *일시 중단* 된 상태로 유지 됩니다. 관리 되는 도메인에 대한 액세스를 유지 하려면 중요 한 경고를 즉시 해결 합니다.

### <a name="what-to-expect"></a>예상 프로그램

Azure AD DS 관리 되는 도메인이 *일시 중단* 된 상태에 있는 경우 다음과 같은 동작이 발생 합니다.

* 관리 되는 도메인의 도메인 컨트롤러가 프로 비전 해제 되 고 가상 네트워크 내에서 도달할 수 없습니다.
* 인터넷을 통해 관리 되는 도메인에 대한 액세스를 보안 LDAP (사용 하도록 설정 된 경우) 작동이 중지 됩니다.
* 관리 되는 도메인에 대한 인증, 도메인에 가입 된 Vm에 로그온 또는 LDAP/LDAPS를 통한 연결에 오류가 발생 했습니다.
* 관리 되는 도메인에 대한 백업은 더 이상 수행 되지 않습니다.
* Azure AD와 동기화가 중지됩니다.

### <a name="how-do-you-know-if-your-managed-domain-is-suspended"></a>관리되는 도메인이 일시 중단되었는지 확인하려면 어떻게 하나요?

Azure Portal의 Azure AD DS 상태 페이지에는 도메인이 일시 중단 되었음을 나타내는 [경고가][resolve-alerts] 표시 됩니다. 도메인의 상태에도 *일시 중단 됨*이 표시 됩니다.

### <a name="restore-a-suspended-domain"></a>일시 중단된 도메인 복원

*일시 중단* 된 상태에 있는 Azure AD DS 관리 되는 도메인의 상태를 복원 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **도메인 서비스**를 검색 하 고 선택 합니다.
1. 목록에서 Azure AD DS 관리 되는 도메인 (예: *aadds.contoso.com*)을 선택 하 고 **상태**를 선택 합니다.
1. 일시 중단 원인에 따라 경고 (예: *AADDS503* 또는 *AADDS504*)를 선택 합니다.
1. 경고에 제공 된 해상도 링크를 선택 하 고 단계에 따라 문제를 해결 합니다.

관리 되는 도메인은 마지막 백업 날짜 까지만 복원할 수 있습니다. 마지막 백업 날짜는 관리 되는 도메인의 **상태** 페이지에 표시 됩니다. 마지막 백업 이후에 발생한 변경 내용은 복원되지 않습니다. 관리되는 도메인의 백업은 최대 30일 동안 저장됩니다. 30일이 지난 백업은 삭제됩니다.

관리 되는 도메인이 *일시 중단* 된 상태에 있을 때 경고를 해결 한 후에는 [Azure 지원 요청을 열고][azure-support] 정상 상태로 돌아갑니다. 30 일 이내에 백업이 있는 경우 Azure 지원에서 관리 되는 도메인을 복원할 수 있습니다.

## <a name="deleted-state"></a>삭제 된 상태

Azure AD DS 관리 되는 도메인은 15 일 동안 *일시 중단* 된 상태로 유지 되는 경우 삭제 됩니다. 이 프로세스는 복구할 수 없습니다.

### <a name="what-to-expect"></a>예상 프로그램

Azure AD DS 관리 되는 도메인이 *삭제 됨* 상태로 들어가면 다음 동작이 표시 됩니다.

* 관리되는 도메인에 대한 모든 리소스 및 백업이 삭제됩니다.
* 관리 되는 도메인을 복원할 수 없으며 대체 관리 되는 도메인을 만들어 Azure AD DS를 다시 사용 해야 합니다.
* 관리되는 도메인이 삭제된 후에는 요금이 청구되지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS 관리 되는 도메인을 정상 상태로 유지 하 고 일시 중단 될 위험을 최소화 하려면 [관리 되는 도메인에 대한 경고를 해결][resolve-alerts]하는 방법을 알아보세요.

<!-- INTERNAL LINKS -->
[alert-nsg]: alert-nsg.md
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[resolve-alerts]: troubleshoot-alerts.md
