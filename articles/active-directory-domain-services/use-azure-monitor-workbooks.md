---
title: Azure AD Domain Services에서 Azure Monitor 통합 문서 사용 | Microsoft Docs
description: Azure Monitor 통합 문서를 사용 하 여 보안 감사를 검토 하 고 관리 되는 Azure Active Directory Domain Services 도메인의 문제를 이해 하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 926f4dd94ba5b1f864a002c282004b3a9f438dca
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722741"
---
# <a name="review-security-audit-events-in-azure-active-directory-domain-services-using-azure-monitor-workbooks"></a>Azure Monitor 통합 문서를 사용 하 Azure Active Directory Domain Services의 보안 감사 이벤트 검토

Azure Active Directory Domain Services (Azure AD DS) 관리 되는 도메인의 상태를 이해 하는 데 도움이 되도록 보안 감사 이벤트를 사용 하도록 설정할 수 있습니다. 그런 다음 텍스트, 분석 쿼리 및 매개 변수를 다양 한 대화형 보고서에 결합 하는 Azure Monitor 통합 문서를 사용 하 여 이러한 보안 감사 이벤트를 검토할 수 있습니다. Azure AD DS에는 감사 이벤트를 살펴보고 환경을 관리할 수 있는 보안 개요 및 계정 작업을 위한 통합 문서 템플릿이 포함 되어 있습니다.

이 문서에서는 Azure Monitor 통합 문서를 사용 하 여 Azure AD DS에서 보안 감사 이벤트를 검토 하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료 하 여 [관리 되는 Azure Active Directory Domain Services 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* Log Analytics 작업 영역에 데이터를 스트리밍하는 관리 되는 도메인에 대해 사용 하도록 설정 된 보안 감사 이벤트입니다.
    * 필요한 경우 [Azure AD DS에 대 한 보안 감사를 사용 하도록 설정][enable-security-audits]합니다.

## <a name="azure-monitor-workbooks-overview"></a>Azure Monitor 통합 문서 개요

Azure AD DS에서 보안 감사 이벤트가 설정 되 면 관리 되는 도메인의 문제를 분석 하 고 식별 하기 어려울 수 있습니다. Azure Monitor를 사용 하 여 이러한 보안 감사 이벤트를 집계 하 고 데이터를 쿼리할 수 있습니다. Azure Monitor 통합 문서를 사용 하면이 데이터를 시각화 하 여 더 빠르고 쉽게 문제를 식별할 수 있습니다.

통합 문서 템플릿은 여러 사용자 및 팀에서 유연 하 게 다시 사용할 수 있도록 설계 된 큐 레이트 보고서입니다. 통합 문서 템플릿을 열면 Azure Monitor 환경의 데이터가 로드 됩니다. 조직의 다른 사용자에 게 영향을 주지 않고 템플릿을 사용할 수 있으며 템플릿에 따라 고유한 통합 문서를 저장할 수 있습니다.

Azure AD DS에는 다음과 같은 두 개의 통합 문서 템플릿이 포함 되어 있습니다.

* 보안 개요 보고서
* 계정 활동 보고서

통합 문서를 편집 하 고 관리 하는 방법에 대 한 자세한 내용은 [Azure Monitor 통합 문서 개요](../azure-monitor/platform/workbooks-overview.md)를 참조 하세요.

## <a name="use-the-security-overview-report-workbook"></a>보안 개요 보고서 통합 문서 사용

사용을 보다 잘 이해 하 고 잠재적 보안 위협을 식별 하는 데 도움이 되도록 보안 개요 보고서는 로그인 데이터를 요약 하 고 확인할 수 있는 계정을 식별 합니다. 특정 날짜 범위의 이벤트를 확인 하 고 잘못 된 암호 시도 또는 계정이 사용 하지 않도록 설정 된 경우와 같은 특정 로그인 이벤트를 드릴 다운할 수 있습니다.

보안 개요 보고서의 통합 문서 템플릿에 액세스 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure Active Directory Domain Services** 을 검색 하 고 선택 합니다.
1. 관리 되는 도메인 (예: *aaddscontoso.com* )을 선택 합니다.
1. 왼쪽의 메뉴에서 **> 통합 문서 모니터링** 을 선택 합니다.

    ![Azure Portal에서 통합 문서 메뉴 옵션을 선택 합니다.](./media/use-azure-monitor-workbooks/select-workbooks-in-azure-portal.png)

1. **보안 개요 보고서**를 선택 합니다.
1. 통합 문서의 맨 위에 있는 드롭다운 메뉴에서 Azure 구독을 선택 하 고 Azure Monitor 작업 영역을 선택 합니다.

    다음 예제 스크린샷에 표시 된 것 처럼 **시간 범위**(예: *지난 7 일)* 를 선택 합니다.

    ![Azure Portal에서 통합 문서 메뉴 옵션을 선택 합니다.](./media/use-azure-monitor-workbooks/select-query-filters.png)

    원하는 대로 데이터를 분석 하 고 시각화할 수 있도록 **타일 보기** 및 **차트 보기** 옵션을 변경할 수도 있습니다.

1. 특정 이벤트 유형을 드릴 다운 하려면 다음 예와 같이 *계정 잠금*등의 **로그인 결과** 카드 중 하나를 선택 합니다.

    ![Azure Monitor 통합 문서에 시각화 되어 있는 보안 개요 보고서 데이터 예제](./media/use-azure-monitor-workbooks/example-security-overview-report.png)

1. 차트 아래에 있는 보안 개요 보고서의 아래쪽 부분은 선택한 작업 유형을 분할 합니다. 다음 예제 보고서에 표시 된 것 처럼 오른쪽에 포함 된 사용자 이름을 기준으로 필터링 할 수 있습니다.

    [![Azure Monitor 통합 문서에서 계정 잠금에 대 한 세부 정보입니다.](./media/use-azure-monitor-workbooks/account-lockout-details-cropped.png)](./media/use-azure-monitor-workbooks/account-lockout-details.png#lightbox)

## <a name="use-the-account-activity-report-workbook"></a>계정 활동 보고서 통합 문서 사용

특정 사용자 계정에 대 한 문제를 해결 하는 데 도움이 되도록 계정 활동 보고서는 자세한 감사 이벤트 로그 정보를 세분화 합니다. 로그인 중에 잘못 된 사용자 이름 또는 암호가 제공 된 경우와 로그인 시도의 원본을 검토할 수 있습니다.

계정 활동 보고서에 대 한 통합 문서 템플릿에 액세스 하려면 다음 단계를 완료 합니다.

1. Azure Portal에서 **Azure Active Directory Domain Services** 을 검색 하 고 선택 합니다.
1. 관리 되는 도메인 (예: *aaddscontoso.com* )을 선택 합니다.
1. 왼쪽의 메뉴에서 **> 통합 문서 모니터링** 을 선택 합니다.
1. **계정 활동 보고서**를 선택 합니다.
1. 통합 문서의 맨 위에 있는 드롭다운 메뉴에서 Azure 구독을 선택 하 고 Azure Monitor 작업 영역을 선택 합니다.

    **시간 범위**(예: *지난 30 일)* 를 선택 하 고, **타일 보기** 에서 데이터를 표시 하는 방법을 선택 합니다.

    다음 예제 보고서에 표시 된 것 처럼 **계정 사용자 이름**(예: *felix*)을 기준으로 필터링 할 수 있습니다.

    [![Azure Monitor 통합 문서에 있는 계정 활동 보고서](./media/use-azure-monitor-workbooks/account-activity-report-cropped.png)](./media/use-azure-monitor-workbooks/account-activity-report.png#lightbox)

    차트 아래 영역에는 작업 결과 및 원본 워크스테이션과 같은 정보와 함께 개별 로그인 이벤트가 표시 됩니다. 이 정보는 계정 잠금을 유발 하거나 잠재적인 공격을 나타낼 수 있는 로그인 이벤트의 반복 되는 원본을 확인 하는 데 도움이 됩니다.

보안 개요 보고서와 마찬가지로 보고서 위쪽의 다른 타일로 드릴 다운 하 여 필요에 따라 데이터를 시각화 하 고 분석할 수 있습니다.

## <a name="save-and-edit-workbooks"></a>통합 문서 저장 및 편집

Azure AD DS에서 제공 하는 두 개의 템플릿 통합 문서는 사용자 고유의 데이터 분석을 시작 하는 데 적합 합니다. 데이터 쿼리 및 조사에서 더 세부적인 정보를 얻으려면 사용자 고유의 통합 문서를 저장 하 고 쿼리를 편집 하면 됩니다.

1. 통합 문서 템플릿 중 하나의 복사본을 저장 하려면 > 편집을 선택 하 **> 공유 보고서로 저장**한 다음 이름을 입력 하 고 저장 합니다.
1. 템플릿의 고유한 복사본에서 **편집** 을 선택 하 여 편집 모드를 시작 합니다. 보고서의 모든 부분 옆에 있는 파란색 **편집** 단추를 선택 하 고 변경할 수 있습니다.

Azure Monitor 통합 문서에 있는 모든 차트와 테이블은 Kusto 쿼리를 사용 하 여 생성 됩니다. 사용자 고유의 쿼리를 만드는 방법에 대 한 자세한 내용은 [Azure Monitor 로그 쿼리][azure-monitor-queries] 및 [kusto 쿼리 자습서][kusto-queries]를 참조 하세요.

## <a name="next-steps"></a>다음 단계

암호 및 잠금 정책을 조정 해야 하 [는 경우 관리 되는 도메인에서 암호 및 계정 잠금 정책][password-policy]을 참조 하세요.

사용자와 관련 된 문제를 해결 하려면 [계정 로그인 문제][troubleshoot-sign-in] 또는 [계정 잠금 문제][troubleshoot-account-lockout]를 해결 하는 방법을 알아보세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-security-audits]: security-audit-events.md
[password-policy]: password-policy.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[azure-monitor-queries]: ../azure-monitor/log-query/query-language.md
[kusto-queries]: /azure/kusto/query/tutorial?pivots=azuredataexplorer