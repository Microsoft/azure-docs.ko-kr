---
title: 조건부 액세스 What If 도구-Azure Active Directory
description: 사용자 환경에서 조건부 액세스 정책의 영향을 이해 하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: db76ec2bc2fc8640214f962a809fee559b4c4af3
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948201"
---
# <a name="troubleshoot-using-the-what-if-tool-in-conditional-access"></a>조건부 액세스에서 What If 도구를 사용하여 문제 해결

[조건부 액세스](./overview.md) 는 권한 있는 사용자가 클라우드 앱에 액세스 하는 방법을 제어할 수 있는 Azure AD (Azure Active Directory)의 기능입니다. 사용자 환경에서 조건부 액세스 정책에서 발생할 수 있는 작업을 어떻게 알 수 있나요? 이 질문에 대답 하기 위해 **조건부 액세스 What If 도구**를 사용할 수 있습니다.

이 문서에서는이 도구를 사용 하 여 조건부 액세스 정책을 테스트 하는 방법을 설명 합니다.

## <a name="what-it-is"></a>정의

**조건부 액세스 What If 정책 도구** 를 사용 하 여 사용자 환경에 대 한 조건부 액세스 정책의 영향을 이해할 수 있습니다. 여러 번의 로그인을 수동으로 수행하여 정책을 시험 사용해보는 대신, 이 도구를 사용하여 사용자의 시뮬레이트된 로그인을 평가할 수 있습니다. 이 시뮬레이션은 이 로그인이 정책에 미치는 영향을 평가하고, 시뮬레이션 보고서를 생성합니다. 이 보고서에는 적용 된 조건부 액세스 정책 뿐만 아니라 [기존 정책](policy-migration.md#classic-policies) (있는 경우)도 나열 됩니다.    

**What If** 도구는 특정 사용자에 게 적용 되는 정책을 신속 하 게 결정 하는 방법을 제공 합니다. 예를 들어 문제를 해결해야 하는 경우에 이 정보를 사용할 수 있습니다.    

## <a name="how-it-works"></a>작동 방법

**조건부 액세스 What If 도구**에서 먼저 시뮬레이트할 로그인 시나리오의 설정을 구성 해야 합니다. 설정에는 다음이 포함됩니다.

- 테스트하려는 사용자 
- 사용자가 액세스하려고 하는 클라우드 앱
- 클라우드 구성 앱에 대한 액세스가 수행되는 조건
     
다음 단계로, 설정을 평가하는 시뮬레이션 실행을 시작할 수 있습니다. 사용하도록 설정된 정책만 평가 실행에 속합니다.

평가가 완료되면 이 도구는 영향을 받는 정책에 대한 보고서를 생성합니다. 조건부 액세스 정책에 대 한 자세한 정보를 수집 하기 위해 [조건부 액세스 정보 및 보고 통합 문서](howto-conditional-access-insights-reporting.md) 는 보고서 전용 모드의 정책과 현재 사용 하도록 설정 된 정책에 대 한 추가 세부 정보를 제공할 수 있습니다.

## <a name="running-the-tool"></a>도구 실행

**What If** 도구는 Azure Portal의 **[조건부 액세스 정책](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies)** 페이지에서 찾을 수 있습니다.

도구를 시작 하려면 정책 목록 맨 위에 있는 도구 모음에서 **What If**를 클릭 합니다.

![What If](./media/what-if-tool/01.png)

평가를 실행하려면 먼저 설정을 구성해야 합니다.

## <a name="settings"></a>설정

이 섹션에서는 시뮬레이션 실행의 설정에 대한 정보를 제공합니다.

![What If](./media/what-if-tool/02.png)

### <a name="user"></a>사용자

한 명의 사용자만 선택할 수 있습니다. 이 필드는 유일한 필수 필드입니다.

### <a name="cloud-apps"></a>클라우드 앱

이 설정의 기본값은 **모든 클라우드 앱**입니다. 기본 설정은 사용자 환경의 사용 가능한 모든 정책에 대한 평가를 수행합니다. 특정 클라우드 앱에 영향을 주는 정책으로 범위를 좁힐 수 있습니다.

### <a name="ip-address"></a>IP 주소

IP 주소는 [위치 조건](location-condition.md)을 모방하기 위한 단일 IPv4 주소입니다. 이 주소는 사용자가 로그인하는 데 사용하는 디바이스의 인터넷 연결 주소를 나타냅니다. 예를 들어 [What is my IP address](https://whatismyipaddress.com)(내 IP 주소)로 이동하여 디바이스의 IP 주소를 확인할 수 있습니다.    

### <a name="device-platforms"></a>디바이스 플랫폼

이 설정은 [디바이스 플랫폼 조건](concept-conditional-access-conditions.md#device-platforms)을 모방하며, **모든 플랫폼(비지원 플랫폼 포함)** 과 같은 설정을 나타냅니다. 

### <a name="client-apps"></a>클라이언트 앱

이 설정은 [클라이언트 앱 조건](concept-conditional-access-conditions.md#client-apps)을 모방합니다.
기본적으로 이 설정을 사용하면 **브라우저** 또는 **모바일 앱 및 데스크톱 클라이언트**가 따로 또는 둘 다 선택되어 있는 모든 정책이 평가됩니다. 또한 **EAS(Exchange ActiveSync)** 를 적용하는 정책도 감지됩니다. 다음을 선택하여 이 설정의 범위를 좁힐 수 있습니다.

- **브라우저**: 하나 이상의 **브라우저**가 선택된 모든 정책을 평가합니다. 
- **모바일 앱 및 데스크톱 클라이언트**: 적어도 **모바일 앱 및 데스크톱 클라이언트**가 선택된 모든 정책을 평가합니다. 

### <a name="sign-in-risk"></a>로그인 위험

이 설정은 [로그인 위험 조건](concept-conditional-access-conditions.md#sign-in-risk)을 모방합니다.   

## <a name="evaluation"></a>평가 

**What If**를 클릭 하 여 평가를 시작 합니다. 평가 결과는 다음으로 구성된 보고서를 제공합니다. 

![What If](./media/what-if-tool/03.png)

- 클래식 정책이 환경에 있는지 여부 표시
- 사용자에게 적용되는 정책
- 사용자에게 적용되지 않는 정책

선택한 클라우드 앱에 대해 [클래식 정책](policy-migration.md#classic-policies)이 존재하는 경우 해당 표시가 제공됩니다. 이 표시를 클릭하면 클래식 정책 페이지로 리디렉션됩니다. 클래식 정책 페이지에서 클래식 정책을 마이그레이션하거나 사용하지 않도록 설정할 수 있습니다. 이 페이지를 닫고 평가 결과로 돌아갈 수 있습니다.

선택한 사용자에 게 적용 되는 정책 목록에서 사용자가 충족 해야 하는 [권한 부여 컨트롤](concept-conditional-access-grant.md) 및 [세션 컨트롤](concept-conditional-access-session.md) 목록을 찾을 수도 있습니다.

사용자에게 적용되지 않는 정책 목록에서 이러한 정책이 적용되지 않는 이유를 찾을 수도 있습니다. 나열된 각 정책에 대해 이유는 충족되지 않은 첫 번째 조건을 나타냅니다. 적용되지 않는 정책의 가능한 이유는 추가로 평가되지 않기 때문에 사용되지 않도록 설정된 경우입니다.   

## <a name="next-steps"></a>다음 단계

- 조건부 액세스 정책 응용 프로그램에 대 한 자세한 내용은 [조건부 액세스 정보 및 보고](howto-conditional-access-insights-reporting.md)를 사용 하는 정책 보고서 전용 모드를 사용 하 여 찾을 수 있습니다.
- 사용자 환경에 대 한 조건부 액세스 정책을 구성할 준비가 된 경우 [조건부 액세스 공용 정책](concept-conditional-access-policy-common.md)을 참조 하세요.