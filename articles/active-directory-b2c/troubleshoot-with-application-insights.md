---
title: Application Insights를 사용 하 여 사용자 지정 정책 문제 해결
titleSuffix: Azure AD B2C
description: Application Insights를 설정 하 여 사용자 지정 정책의 실행을 추적 하는 방법
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3aeef1614f22563e0fd348c5bc6ae7ff1e7b0b03
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848149"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Application Insights를 사용 하 여 Azure Active Directory B2C 로그 수집

이 문서에서는 사용자 지정 정책을 사용 하 여 문제를 진단할 수 있도록 Active Directory B2C (Azure AD B2C)에서 로그를 수집 하는 단계를 제공 합니다. Application Insights는 예외를 진단하고 애플리케이션 성능 문제를 시각화하는 방법을 제공합니다. Azure AD B2C에는 Application Insights 데이터를 전송 하기 위한 기능이 포함 되어 있습니다.

여기서 설명 하는 자세한 활동 로그는 사용자 지정 정책을 개발 하는 동안에 **만** 사용 하도록 설정 해야 합니다.

> [!WARNING]
> 프로덕션 환경에서 개발 모드를 사용 하지 마십시오. 로그는 id 공급자와 주고 받는 모든 클레임을 수집 합니다. 개발자가 Application Insights 로그에 수집 된 개인 데이터를 담당 하 고 있다고 가정 합니다. 이러한 자세한 로그는 정책이 **개발자 모드로**배치 된 경우에만 수집 됩니다.

## <a name="set-up-application-insights"></a>Application Insights 설정

아직 없는 경우 구독에 Application Insights의 인스턴스를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure 구독을 포함 하는 디렉터리 (Azure AD B2C 디렉터리가 아님)를 선택 합니다.
1. 왼쪽 탐색 메뉴에서 **리소스 만들기** 를 선택 합니다.
1. **Application Insights**를 검색 하 고 선택한 다음 **만들기**를 선택 합니다.
1. 양식을 작성 하 고 **검토 + 만들기**를 선택한 다음 **만들기**를 선택 합니다.
1. 배포가 완료 되 면 **리소스로 이동**을 선택 합니다.
1. Application Insights에서 **구성** 메뉴에서 **속성**을 선택 합니다.
1. 이후 단계에서 사용할 **계측 키** 를 기록 합니다.

## <a name="configure-the-custom-policy"></a>사용자 지정 정책 구성

1. RP (신뢰 당사자) 파일을 엽니다 (예: *Signuto signin .xml*).
1. 다음 속성을 `<TrustFrameworkPolicy>` 요소에 추가합니다.

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 아직 존재 하지 않는 경우 `<UserJourneyBehaviors>` 자식 노드를 `<RelyingParty>` 노드에 추가 합니다. `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`바로 뒤에 위치 해야 합니다.
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다. `{Your Application Insights Key}`를 앞에서 기록한 Application Insights **계측 키** 로 바꿔야 합니다.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`는 ApplicationInsights에서 처리 파이프라인을 통해 원격 분석을 신속 하 게 처리 하도록 지시 합니다. 개발에 좋지만 고용량으로 제한 됩니다.
    * `ClientEnabled="true"`는 추적 페이지 보기 및 클라이언트 쪽 오류에 대한 ApplicationInsights 클라이언트 쪽 스크립트를 보냅니다. Application Insights 포털의 **Browsertimings** 테이블에서 볼 수 있습니다. `ClientEnabled= "true"`를 설정 하 여 페이지 스크립트에 Application Insights를 추가 하 고 페이지 로드 및 AJAX 호출의 타이밍, 브라우저 예외 및 AJAX 오류에 대한 세부 정보, 사용자 및 세션 수를 가져옵니다. 이 필드는 **선택 사항이**며 기본적으로 `false`로 설정 됩니다.
    * `ServerEnabled="true"`는 Application Insights에 기존 UserJourneyRecorder JSON을 사용자 지정 이벤트로 보냅니다.

    예:

    ```XML
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. 정책을 업로드합니다.

## <a name="see-the-logs-in-application-insights"></a>Application Insights에서 로그를 참조하세요.

Application Insights에서 새 로그를 볼 수 있으려면 일반적으로 5 분 이내에 짧은 지연이 발생 합니다.

1. [Azure Portal](https://portal.azure.com)에서 만든 Application Insights 리소스를 엽니다.
1. **개요** 메뉴에서 **분석**을 선택 합니다.
1. Application Insights에서 새 탭을 엽니다.

로그를 확인 하는 데 사용할 수 있는 쿼리 목록은 다음과 같습니다.

| 쿼리 | Description |
|---------------------|--------------------|
`traces` | Azure AD B2C에서 생성된 모든 로그를 참조하세요. |
`traces | where timestamp > ago(1d)` | 마지막 날에 Azure AD B2C에서 생성된 모든 로그를 참조하세요.

항목이 길어질 수 있습니다. 자세히 보기 위해 CSV로 내보냅니다.

쿼리에 대한 자세한 내용은 Azure Monitor의 [로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

커뮤니티는 ID 개발자에게 도움을 주는 사용자 경험 뷰어를 개발했습니다. Application Insights 인스턴스에서 읽고 사용자 경험 이벤트의 올바른 구조 보기를 제공합니다. 소스 코드를 가져오고 고유한 솔루션에 배포합니다.

사용자 경험 플레이어는 Microsoft에서 지원 되지 않으며 그대로 그대로 사용할 수 있습니다.

GitHub의 Application Insights에서 이벤트를 읽는 뷰어 버전은 다음 위치에서 확인할 수 있습니다.

[Azure-샘플/b2c-고급-정책](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
