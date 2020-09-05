---
title: 보고서용 Azure Monitor 통합 문서 | Microsoft Docs
description: Azure Monitor 통합 문서를 Azure Active Directory 보고서에 사용하는 방법을 알아봅니다.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 510991c1afba9a5ffbfd77e855ee25d8b641bdd0
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226984"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Monitor 통합 문서를 Azure Active Directory 보고서에 사용하는 방법

> [!IMPORTANT]
> 이 통합 문서의 기본 쿼리를 최적화하려면 "편집"을 클릭하고, [설정] 아이콘을 클릭하고, 쿼리를 실행하려는 작업 영역을 선택하세요. 통합 문서는 기본적으로 사용자가 Azure AD 로그를 라우팅하는 모든 작업 영역을 선택합니다. 

이 문서에서는 다음과 같은 내용을 다룹니다.

- [조건부 액세스 정책](../conditional-access/overview.md)이 사용자의 로그인 환경에 미치는 영향 이해

- 로그인 오류 문제를 해결하여 조직의 로그인 상태를 보다 정확하게 파악하고 문제를 보다 신속하게 해결

- 레거시 인증을 사용하여 환경에 로그인하는 사람이 누구인지 알아보기 ([레거시 인증을 차단](../conditional-access/block-legacy-authentication.md)하면 테넌트 보안을 강화할 수 있습니다.)

- 테넌트에서 조건부 액세스 정책이 미치는 영향 알아보기

- 로그인 로그 쿼리를 검토하는 기능 알아보기 - 통합 문서는 액세스 권한이 부여되거나 액세스가 거부된 사용자 수와 리소스에 액세스할 때 조건부 액세스 정책을 무시한 사용자 수를 보고합니다.

- 디바이스 플랫폼, 디바이스 상태, 클라이언트 앱, 로그인 위험, 위치 및 애플리케이션을 비롯한 조건에 따라 정책의 영향을 컨텍스트화할 수 있도록 조건에 따른 통합 문서 세부 정보를 자세히 이해

- 로그인 로그 쿼리에 대한 심층적 인사이트 획득 - 통합 문서는 액세스 권한이 부여되거나 액세스가 거부된 사용자 수와 리소스에 액세스할 때 조건부 액세스 정책을 우회한 사용자 수를 보고합니다.

- 이러한 질문을 해결할 수 있도록 Azure Active Directory는 모니터링을 위한 통합 문서를 제공합니다. [Azure Monitor 통합 문서](../../azure-monitor/platform/workbooks-overview.md)는 텍스트, 분석 쿼리, 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합합니다.



이 문서의 내용:

- [Monitor 통합 문서를 사용하여 대화형 보고서를 만드는 방법](../../azure-monitor/platform/workbooks-overview.md)을 잘 알고 있다고 가정합니다.

- Monitor 통합 문서를 사용하여 조건부 액세스 정책의 영향을 이해하고, 로그인 오류 문제를 해결하고, 레거시 인증을 식별하는 방법을 설명합니다.
 


## <a name="prerequisites"></a>사전 요구 사항

Monitor 통합 문서를 사용하려면 다음 항목이 필요합니다.

- 프리미엄(P1 또는 P2) 라이선스를 사용하는 Azure Active Directory 테넌트. [프리미엄 라이선스를 얻는 방법](../fundamentals/active-directory-get-started-premium.md)을 알아보세요.

- [Log Analytics 작업 영역](../../azure-monitor/learn/quick-create-workspace.md)

- Log Analytics 작업 영역에 대한 [액세스](../../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions) 권한
- Azure Active Directory의 다음 역할(Azure Active Directory 포털을 통해 Log Analytics에 액세스하는 경우)
    - 보안 관리자
    - 보안 판독기
    - 보고서 읽기 권한자
    - 전역 관리자

## <a name="roles"></a>역할
통합 문서를 관리하려면 다음 역할 중 하나여야 하고 [기본 Log Analytics 작업 영역에 대한 액세스 권한](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions)이 있어야 합니다.
-   전역 관리자
-   보안 관리자
-   보안 판독기
-   보고서 읽기 권한자
-   애플리케이션 관리자

## <a name="workbook-access"></a>통합 문서 액세스 

통합 문서에 액세스하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **모니터링** > **통합 문서**로 이동합니다. 

1. 보고서 또는 템플릿을 선택하거나, 도구 모음에서 **열기**를 선택합니다. 

![Azure AD에서 Azure Monitor 통합 문서 찾기](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>로그인 분석

로그인 분석 통합 문서에 액세스하려면 **사용** 섹션에서 **로그인**을 선택합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여줍니다.

- 모든 로그인

- Success

- 보류 중인 사용자 작업

- 실패

각 추세를 다음 범주로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 분석](./media/howto-use-azure-monitor-workbooks/43.png)


각 추세를 다음 범주로 분석한 결과가 제공됩니다.

- 위치

    ![위치별 로그인](./media/howto-use-azure-monitor-workbooks/45.png)

- 디바이스

    ![디바이스별 로그인](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>레거시 인증을 사용한 로그인 


[레거시 인증](../conditional-access/block-legacy-authentication.md)을 사용하는 로그인에 대한 통합 문서에 액세스하려면 **사용** 섹션에서 **레거시 인증을 사용한 로그인**을 선택합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여줍니다.

- 모든 로그인

- Success


각 추세를 다음 범주로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

- 프로토콜

![레거시 인증을 사용한 로그인](./media/howto-use-azure-monitor-workbooks/47.png)


각 추세를 앱 및 프로토콜 기준으로 분석한 결과가 제공됩니다.

![앱 및 프로토콜별 레거시 인증 로그인](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>조건부 액세스를 사용한 로그인 


[조건부 액세스 정책](../conditional-access/overview.md)별 로그인에 대한 통합 문서에 액세스하려면 **조건부 액세스** 섹션에서 **조건부 액세스를 사용한 로그인**을 선택합니다. 

이 통합 문서는 사용되지 않는 로그인에 대한 추세를 보여줍니다. 각 추세를 다음 범주로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![조건부 액세스를 사용하여 로그인](./media/howto-use-azure-monitor-workbooks/49.png)


사용되지 않는 로그인의 경우 조건부 액세스 상태별로 분석한 결과가 제공됩니다.

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>조건부 액세스 인사이트

### <a name="overview"></a>개요

통합 문서에는 IT 관리자가 테넌트의 조건부 액세스 정책의 영향을 모니터링하는 데 도움이 되는 로그인 로그 쿼리가 포함되어 있습니다. 액세스 권한이 부여되거나 거부된 사용자 수를 보고하는 기능이 있습니다. 통합 문서에는 로그인 시 사용자의 특성을 기반으로 조건부 액세스 정책을 우회한 사용자 수에 대한 인사이트가 포함되어 있습니다. 여기에는 디바이스 플랫폼, 디바이스 상태, 클라이언트 앱, 로그인 위험, 위치 및 애플리케이션을 비롯한 조건에 따라 정책의 영향을 컨텍스트화할 수 있도록 조건에 따른 세부 정보가 포함되어 있습니다.

### <a name="instructions"></a>Instructions 
조건부 액세스 인사이트에 대한 통합 문서에 액세스하려면 [조건부 액세스] 섹션에서 **조건부 액세스 인사이트**를 선택합니다. 이 통합 문서는 테넌트의 각 조건부 액세스 정책이 미칠 것으로 예상되는 영향을 보여줍니다. 드롭다운 목록에서 조건부 액세스 정책을 하나 이상 선택하고, 다음 필터를 적용하여 통합 문서의 범위를 좁힙니다. 

- **시간 범위**

- **사용자**

- **앱**

- **데이터 뷰**

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/access-insights.png)


[영향 요약]은 선택한 정책이 특정 결과를 가져온 사용자 또는 로그인 수를 보여줍니다. [합계]는 선택한 시간 범위에서 선택한 정책을 평가한 사용자 또는 로그인의 수입니다. 타일을 클릭하면 통합 문서의 데이터를 해당 결과 유형별로 필터링할 수 있습니다. 

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

이 통합 문서는 선택한 정책의 영향을 다음 6개 조건별로 나누어서 보여줍니다. 
- **디바이스 상태**
- **디바이스 플랫폼**
- **클라이언트 앱**
- **로그인 위험**
- **위치**
- **애플리케이션**

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/device-platform.png)

통합 문서에서 선택한 매개 변수를 기준으로 필터링된 개별 로그인을 조사할 수도 있습니다. 개별 사용자를 검색하고(로그인 빈도를 기준으로 정렬됨), 해당하는 로그인 이벤트를 확인합니다. 

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>권한 부여 컨트롤별 로그인

[권한 부여 컨트롤](../conditional-access/controls.md)별 로그인에 대한 통합 문서에 액세스하려면 **조건부 액세스** 섹션에서 **권한 부여 컨트롤별 로그인**을 선택합니다. 

이 통합 문서는 다음과 같이 사용되지 않는 로그인 추세를 보여줍니다.

- MFA 요구
 
- 사용 약관 필요

- 개인정보처리방침 필요

- 기타


각 추세를 다음 범주로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![권한 부여 컨트롤별 로그인](./media/howto-use-azure-monitor-workbooks/50.png)


각 추세를 앱 및 프로토콜 기준으로 분석한 결과가 제공됩니다.

![최근 로그인 분석](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>로그인 실패 분석

**로그인 실패 분석** 통합 문서를 사용하여 다음 항목의 오류를 해결할 수 있습니다.

- 로그인
- 조건부 액세스 정책
- 레거시 인증 


조건부 액세스별 로그인 데이터에 액세스하려면 **문제 해결** 섹션에서 **레거시 인증을 사용한 로그인**을 선택합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여줍니다.

- 모든 로그인

- Success

- 보류 중인 작업

- 실패


각 추세를 다음 범주로 필터링할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 문제 해결](./media/howto-use-azure-monitor-workbooks/52.png)


로그인 문제 해결을 도와주기 위해 Azure Monitor에서는 다음과 같은 범주를 기준으로 분석한 정보를 제공합니다.

- 상위 오류

    ![상위 오류 요약](./media/howto-use-azure-monitor-workbooks/53.png)

- 사용자 작업을 기다리는 로그인

    ![사용자 작업을 기다리는 로그인 요약](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>다음 단계

[Monitor 통합 문서를 사용하여 대화형 보고서를 작성합니다](../../azure-monitor/platform/workbooks-overview.md).