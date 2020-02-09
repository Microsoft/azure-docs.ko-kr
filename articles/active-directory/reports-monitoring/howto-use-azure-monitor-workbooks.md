---
title: 보고서용 Azure Monitor 통합 문서 | Microsoft Docs
description: Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법에 대해 알아봅니다.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014372"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Active Directory 보고서에 Azure Monitor 통합 문서를 사용 하는 방법

> [!IMPORTANT]
> 이 통합 문서에서 기본 쿼리를 최적화 하려면 "편집"을 클릭 하 고 설정 아이콘을 클릭 한 다음 쿼리를 실행 하려는 작업 영역을 선택 하세요. 통합 문서는 기본적으로 Azure AD 로그를 라우팅하는 모든 작업 영역을 선택 합니다. 

다음 작업을 수행 하 시겠습니까?

- 사용자의 로그인 환경에 대 한 [조건부 액세스 정책의](../conditional-access/overview.md) 효과를 이해 합니다.

- 로그인 오류 문제를 해결 하 여 조직의 로그인 상태를 더 잘 파악 하 고 문제를 신속 하 게 해결 하 시겠습니까?

- 사용자 환경에 로그인 하는 데 레거시 인증을 사용 하는 사용자를 알고 싶으세요? [레거시 인증을 차단](../conditional-access/block-legacy-authentication.md)하 여 테넌트의 보호를 향상할 수 있습니다.

- 테넌트에서 조건부 액세스 정책의 영향을 이해 해야 하나요?

- 로그인 로그 쿼리를 검토 하는 기능을 원하는 경우 통합 문서는 액세스 권한이 부여 되거나 거부 된 사용자 수 및 리소스에 액세스할 때 조건부 액세스 정책을 바이패스 한 사용자 수를 보고 하나요?

- 보다 심층적으로 이해 하는 데 관심이 있습니다. 장치 플랫폼, 장치 상태, 클라이언트 앱, 로그인 위험, 위치 및 응용 프로그램을 포함 하 여 정책에 대 한 영향이 까닭 될 수 있도록 조건 당 통합 문서 세부 정보를 확인 하세요.

- 로그인 로그 쿼리를 심층적으로 파악 하 고, 통합 문서는 액세스 권한이 부여 되거나 거부 된 사용자 수 및 리소스에 액세스할 때 조건부 액세스 정책을 바이패스 한 사용자 수를 보고 합니다.

- 이러한 질문을 해결 하는 데 도움이 되도록 Active Directory는 모니터링을 위한 통합 문서를 제공 합니다. [Azure Monitor 통합 문서](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) 는 텍스트, 분석 쿼리, 메트릭 및 매개 변수를 풍부한 대화형 보고서로 결합 합니다.



이 문서의 내용:

- [통합 문서 모니터링을 사용 하 여 대화형 보고서를 만드는](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)방법을 잘 알고 있다고 가정 합니다.

- 통합 문서 모니터링을 사용 하 여 조건부 액세스 정책의 영향을 파악 하 고, 로그인 오류 문제를 해결 하 고, 레거시 인증을 식별 하는 방법을 설명 합니다.
 


## <a name="prerequisites"></a>선행 조건

통합 문서 모니터링을 사용 하려면 다음이 필요 합니다.

- Premium (P1 또는 P2) 라이선스가 있는 Active Directory 테넌트. [프리미엄 라이선스를 얻는](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)방법에 대해 알아봅니다.

- [Log Analytics 작업 영역](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)입니다.

- Log analytics 작업 영역에 대 한 [액세스](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions)
- Azure Active Directory의 다음 역할 (Azure Active Directory 포털을 통해 Log Analytics에 액세스 하는 경우)
    - 보안 관리자
    - 보안 판독기
    - 보고서 구독자
    - 전역 관리자

## <a name="roles"></a>역할
다음 역할 중 하나에 있어야 하며 통합 문서를 관리 하기 위해 [기본 Log Analytics 작업 영역에 액세스할](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) 수 있어야 합니다.
-   전역 관리자
-   보안 관리자
-   보안 판독기
-   보고서 구독자
-   애플리케이션 관리자

## <a name="workbook-access"></a>통합 문서 액세스 

통합 문서에 액세스 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **모니터링** > **통합 문서로**이동 합니다. 

1. 보고서 또는 템플릿을 선택 하거나 도구 모음에서 **열기**를 선택 합니다. 

![Azure AD에서 Azure Monitor 통합 문서 찾기](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>로그인 분석

로그인 분석 통합 문서에 액세스 하려면 **사용** 섹션에서 **로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여 줍니다.

- 모든 로그인

- 성공

- 보류 중인 사용자 작업

- 실패

각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 분석](./media/howto-use-azure-monitor-workbooks/43.png)


각 추세에 대해 다음 범주의 분석 결과를 얻을 수 있습니다.

- Location

    ![위치별 로그인](./media/howto-use-azure-monitor-workbooks/45.png)

- 디바이스

    ![장치에서 로그인](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>레거시 인증을 사용 하 여 로그인 


[레거시 인증](../conditional-access/block-legacy-authentication.md)을 사용 하는 로그인에 대 한 통합 문서에 액세스 하려면 **사용** 섹션에서 **레거시 인증을 사용 하 여 로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여 줍니다.

- 모든 로그인

- 성공


각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

- 프로토콜

![레거시 인증으로 로그인](./media/howto-use-azure-monitor-workbooks/47.png)


각 추세에 대해 앱 및 프로토콜을 기준으로 분석을 받습니다.

![앱 및 프로토콜인 레거시 인증 로그인](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>조건부 액세스로 로그인 


[조건부 액세스 정책의](../conditional-access/overview.md)로그인에 대 한 통합 문서에 액세스 **하려면 조건부 액세스 섹션에서** **조건부 액세스 별 로그인**을 선택 합니다. 

이 통합 문서에서는 비활성화 된 로그인에 대 한 추세를 보여 줍니다. 각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![조건부 액세스를 사용하여 로그인](./media/howto-use-azure-monitor-workbooks/49.png)


비활성화 된 로그인의 경우 조건부 액세스 상태에 대 한 분석을 받게 됩니다.

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>조건부 액세스 인사이트

### <a name="overview"></a>개요

통합 문서에는 IT 관리자가 테넌트의 조건부 액세스 정책의 영향을 모니터링 하는 데 도움이 되는 로그인 로그 쿼리가 포함 되어 있습니다. 액세스 권한이 부여 되거나 거부 된 사용자 수에 대해 보고할 수 있습니다. 통합 문서에는 로그인 시 해당 사용자의 특성을 기반으로 조건부 액세스 정책을 우회 한 사용자 수에 대 한 정보가 포함 되어 있습니다. 장치 플랫폼, 장치 상태, 클라이언트 앱, 로그인 위험, 위치 및 응용 프로그램을 비롯 한 조건에 따라 정책의 영향을 까닭 수 있도록 조건 당 세부 정보를 포함 합니다.

### <a name="instructions"></a>지침 
조건부 액세스 정보에 대 한 통합 문서에 액세스 하려면 조건부 액세스 섹션에서 **조건부 액세스 insights** 통합 문서를 선택 합니다. 이 통합 문서에서는 테넌트의 각 조건부 액세스 정책에 대 한 예상 된 영향을 보여 줍니다. 드롭다운 목록에서 조건부 액세스 정책을 하나 이상 선택 하 고 다음 필터를 적용 하 여 통합 문서의 범위를 좁힙니다. 

- **시간 범위**

- **User**

- **앱**

- **데이터 뷰**

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/access-insights.png)


영향 요약은 선택한 정책에 특정 결과가 발생 한 사용자 또는 로그인의 수를 표시 합니다. Total은 선택한 정책이 선택한 시간 범위에서 평가 된 사용자 또는 로그인의 수입니다. 타일을 클릭 하 여 해당 결과 유형별로 통합 문서의 데이터를 필터링 합니다. 

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

이 통합 문서는 다음 6 가지 조건에 따라 선택한 정책의 영향을 보여 줍니다. 
- **장치 상태**
- **장치 플랫폼**
- **클라이언트 앱**
- **로그인 위험**
- **위치**
- **애플리케이션**

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/device-platform.png)

통합 문서에서 선택한 매개 변수를 기준으로 필터링 된 개별 로그인을 조사할 수도 있습니다. 개별 사용자를 검색 하 고 로그인 빈도를 기준으로 정렬 한 다음 해당 로그인 이벤트를 확인 합니다. 

![조건부 액세스 상태](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>권한 부여 컨트롤을 통한 로그인

[권한 부여 컨트롤](../conditional-access/controls.md)을 사용 하 여 로그인에 대 한 통합 문서에 액세스 하려면 **조건부 액세스** 섹션에서 **권한 부여 컨트롤을 사용 하 여 로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 비활성화 된 로그인 추세를 보여 줍니다.

- MFA 요구
 
- 사용 약관 필요

- 개인정보 취급 방침 필요

- 기타


각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![권한 부여 컨트롤을 통한 로그인](./media/howto-use-azure-monitor-workbooks/50.png)


각 추세에 대해 앱 및 프로토콜을 기준으로 분석을 받습니다.

![최근 로그인 분석](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>로그인 오류 분석

다음을 사용 하 여 오류를 해결 하려면 **로그인 오류 분석** 통합 문서를 사용 합니다.

- 로그인
- 조건부 액세스 정책
- 레거시 인증 


조건부 액세스 데이터를 사용 하 여 로그인에 액세스 하려면 **문제 해결** 섹션에서 **레거시 인증을 사용 하 여 로그인**을 선택 합니다. 

이 통합 문서는 다음과 같은 로그인 추세를 보여 줍니다.

- 모든 로그인

- 성공

- 보류 중인 작업

- 실패


각 추세는 다음 범주로 필터링 할 수 있습니다.

- 시간 범위

- 앱

- 사용자

![로그인 문제 해결](./media/howto-use-azure-monitor-workbooks/52.png)


로그인 문제를 해결 하는 데 도움이 되도록 Azure Monitor는 다음 범주의 분류를 제공 합니다.

- 상위 오류

    ![상위 오류 요약](./media/howto-use-azure-monitor-workbooks/53.png)

- 사용자 작업을 기다리는 로그인

    ![사용자 작업을 기다리는 로그인의 요약](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>다음 단계

[통합 문서 모니터링을 사용 하 여 대화형 보고서를 만듭니다](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
