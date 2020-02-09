---
title: 세션 선호도 문제 해결
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure 애플리케이션 게이트웨이에서 세션 선호도 문제를 해결 하는 방법에 대한 정보를 제공 합니다.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: 9f14521c15c3497bed4ffbeba44cb5d78ee4df7b
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74047980"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>게이트웨이 세션 선호도 문제 Azure 애플리케이션 문제 해결

Azure 애플리케이션 Gateway를 사용 하 여 세션 선호도 문제를 진단 하 고 해결 하는 방법을 알아봅니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

쿠키 기반 세션 선호도 기능은 동일한 서버에서 사용자 세션을 유지하려는 경우에 유용합니다. Application Gateway는 게이트웨이 관리형 쿠키를 사용하여 사용자 세션에서 동일한 서버에 후속 트래픽을 처리하도록 지시할 수 있습니다. 이는 세션 상태가 사용자 세션의 서버에 로컬로 저장된 경우에 특히 중요합니다.

## <a name="possible-problem-causes"></a>가능한 문제 원인

쿠키 기반 세션 선호도를 유지 관리 하는 문제는 다음과 같은 주요 이유로 인해 발생할 수 있습니다.

- "쿠키 기반 선호도" 설정이 사용 하도록 설정 되어 있지 않습니다.
- 응용 프로그램에서 쿠키 기반 선호도를 처리할 수 없습니다.
- 응용 프로그램에서 쿠키 기반 선호도를 사용 하 고 있지만 백 엔드 서버 간에 요청을 계속 바운스

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>"쿠키 기반 선호도" 설정이 사용 되는지 확인 합니다.

경우에 따라 "쿠키 기반 선호도" 설정을 사용 하도록 설정 하는 것을 잊은 경우 세션 선호도 문제가 발생할 수 있습니다. Azure Portal의 HTTP 설정 탭에서 "쿠키 기반 선호도" 설정을 사용 하도록 설정 했는지 여부를 확인 하려면 다음 지침을 따르세요.

1. [Azure 포털](https://portal.azure.com/)에 로그온합니다.

2. **왼쪽 탐색** 창에서 **모든 리소스**를 클릭 합니다. 모든 리소스 블레이드에서 응용 프로그램 게이트웨이 이름을 클릭 합니다. 선택한 구독에 이미 여러 개의 리소스가 있는 경우 **이름을 기준으로 필터링 ...** 에 응용 프로그램 게이트웨이 이름을 입력할 수 있습니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. **설정**아래에서 **HTTP 설정** 탭을 선택 합니다.

   ![troubleshoot-session-affinity-issues-1](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 오른쪽에서 **appGatewayBackendHttpSettings** 를 클릭 하 여 쿠키 기반 선호도에 대해 **사용** 을 선택 했는지 여부를 확인 합니다.

   ![troubleshoot-session-affinity-issues-2](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



다음 방법 중 하나를 사용 하 여 "**backendHttpSettingsCollection**"에서 "**CookieBasedAffinity**"의 값이 *사용*으로 설정 된 것을 확인할 수도 있습니다.

- PowerShell에서 [AzApplicationGatewayBackendHttpSetting](https://docs.microsoft.com/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) 실행
- Azure Resource Manager 템플릿을 사용 하 여 JSON 파일을 살펴봅니다.

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>응용 프로그램이 쿠키 기반 선호도를 처리할 수 없습니다.

#### <a name="cause"></a>원인

응용 프로그램 게이트웨이는 쿠키를 사용 하 여 세션 기반 선호도만 수행할 수 있습니다.

#### <a name="workaround"></a>해결 방법

응용 프로그램에서 쿠키 기반 선호도를 처리할 수 없는 경우 외부 또는 내부 azure 부하 분산 장치 또는 다른 타사 솔루션을 사용 해야 합니다.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>응용 프로그램에서 쿠키 기반 선호도를 사용 하 고 있지만 백 엔드 서버 간에 요청을 계속 바운스

#### <a name="symptom"></a>증상

쿠키 기반 선호도 설정을 사용 하도록 설정 했습니다. Internet Explorer에서 짧은 이름 URL을 사용 하 여 Application Gateway에 액세스할 때 (예: [http://website](http://website/) ) 백 엔드 서버 사이에서 요청이 계속 바운스 됩니다.

이 문제를 확인 하려면 다음 지침을 따르세요.

1. Application Gateway 뒤에 응용 프로그램에 연결 하는 "클라이언트"에서 웹 디버거 추적을 사용 합니다 (이 예제에서는 Fiddler 사용).
    **팁** Fiddler를 사용 하는 방법을 모르는 경우 맨 아래에 있는 "**네트워크 트래픽을 수집 하 고 웹 디버거를 사용 하 여 분석 합니다**." 옵션을 선택 합니다.

2. 세션 로그를 확인 하 고 분석 하 여 클라이언트에서 제공한 쿠키에 ARRAffinity 세부 정보가 있는지 여부를 확인 합니다. 쿠키 집합 내에서 "**ARRAffinity =** *ARRAffinityValue*"와 같은 ARRAffinity 세부 정보를 찾지 못하면 클라이언트는 Application Gateway에서 제공 하는 arra 쿠키를 사용 하 여 응답 하지 않습니다.
    예:

    ![troubleshoot-session-affinity-issues-3](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![troubleshoot-session-affinity-issues-4](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

응용 프로그램은 응답을 받을 때까지 각 요청에 대해 쿠키를 설정 하려고 계속 시도 합니다.

#### <a name="cause"></a>원인

Internet Explorer 및 기타 브라우저가 짧은 이름 URL이 포함 된 쿠키를 저장 하거나 사용 하지 않을 수 있기 때문에이 문제가 발생 합니다.

#### <a name="resolution"></a>해결 방법

이 이슈를 해결하려면 FQDN을 사용하여 Application Gateway에 액세스해야 합니다. 예를 들어 [http://website.com](https://website.com/) 또는 [http://appgw.website.com](http://appgw.website.com/) 를 사용 합니다.

## <a name="additional-logs-to-troubleshoot"></a>문제를 해결 하기 위한 추가 로그

추가 로그를 수집 하 고 분석 하 여 관련 된 문제를 해결 하는 문제를 해결할 수 있습니다.

### <a name="analyze-application-gateway-logs"></a>Application Gateway 로그 분석

Application Gateway 로그를 수집 하려면 다음 지침을 따르세요.

Azure Portal을 통한 로깅 사용

1. [Azure Portal](https://portal.azure.com/)에서 리소스를 찾은 다음 **진단 로그**를 클릭 합니다.

   Application Gateway 로그, 성능 로그, 방화벽 로그의 세 가지 로그를 사용할 수 있습니다.

2. 데이터 수집을 시작 하려면 **진단 켜기**를 클릭 합니다.

   ![troubleshoot-session-affinity-issues-5](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **진단 설정** 블레이드에서는 진단 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. **Log Analytics** 에서 **구성** 을 클릭 하 여 작업 영역을 설정 합니다. 또한 이벤트 허브 및 스토리지 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

   ![troubleshoot-session-affinity-issues-6](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 설정을 확인 하 고 **저장**을 클릭 합니다.

   ![troubleshoot-session-affinity-issues-7](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Application Gateway 액세스 로그 보기 및 분석

1. Application Gateway 리소스 뷰의 Azure Portal **모니터링** 섹션에서 **진단 로그** 를 선택 합니다.

   ![troubleshoot-session-affinity-issues-8](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 오른쪽의 로그 범주에 있는 드롭다운 목록에서 "**Applicationgatewayaccesslog**"를 선택 **합니다.**  

   ![troubleshoot-session-affinity-issues-9](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Application Gateway Access 로그 목록에서 분석 및 내보낼 로그를 클릭 한 다음 JSON 파일을 내보냅니다.

4. 3 단계에서 내보낸 JSON 파일을 CSV 파일로 변환 하 고 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

5. 다음 데이터를 확인 합니다.

- **ClientIP**– 연결 하는 클라이언트의 클라이언트 IP 주소입니다.
- **Clientport** -요청에 대한 연결 하는 클라이언트의 원본 포트입니다.
- **Requestquery** – 요청이 수신 되는 대상 서버를 나타냅니다.
- **서버에서 라우트된**: 요청이 수신 된 백 엔드 풀 인스턴스입니다.
- **X-AzureApplicationGateway-LOG-ID**: 요청에 사용된 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. 예: X-y = 0 & 서버-라우트된 = 10.0.2.4.

  - **SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드

  ![troubleshoot-session-affinity-issues-11](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

두 항목이 동일한 ClientIP 및 Client 포트에서 오고 동일한 백 엔드 서버에 전송 되는 것을 확인 하면 Application Gateway 올바르게 구성 된 것입니다.

두 항목이 동일한 ClientIP 및 클라이언트 포트에서 발생 하 고 다른 백 엔드 서버에 전송 되는 경우,이는 백 엔드 서버 간의 요청이 발생 하는 것을 의미 합니다. 즉, "**응용 프로그램에서 쿠키 기반 선호도를 사용 하 고 있지만 백 엔드 서버 간의 요청이 여전히 바운스**"를 선택 하 여 문제를 해결 합니다.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>웹 디버거를 사용 하 여 HTTP 또는 HTTPS 트래픽을 캡처 및 분석

Fiddler와 같은 웹 디버깅 도구는 인터넷 및 테스트 컴퓨터 간의 네트워크 트래픽을 캡처하여 웹 응용 프로그램을 디버그 하는 데 도움이 될 수 있습니다. 이러한 도구를 사용 하면 브라우저에서 들어오고 나가는 데이터를 받거나 보낼 때 해당 데이터를 검사할 수 있습니다. 이 예제에서 Fiddler에는 웹 응용 프로그램과 관련 된 클라이언트 쪽 문제를 해결 하는 데 도움이 되는 HTTP replay 옵션이 있습니다. 특히 인증 종류 문제에 대한 것입니다.

원하는 웹 디버거를 사용 합니다. 이 샘플에서는 Fiddler를 사용 하 여 http 또는 https 트래픽을를 캡처 및 분석 하 고 지침을 따릅니다.

1. <https://www.telerik.com/download/fiddler>에서 Fiddler 도구를 다운로드 합니다.

    > [!NOTE]
    > 캡처 컴퓨터에 .NET 4가 설치 된 경우 Fiddler4를 선택 합니다. 그렇지 않으면 Fiddler2를 선택 합니다.

2. 설치 실행 파일을 마우스 오른쪽 단추로 클릭 하 고 관리자 권한으로를 실행 하 여를 설치 합니다.

    ![troubleshoot-session-affinity-issues-12](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Fiddler를 열면 트래픽 캡처가 자동으로 시작 됩니다 (왼쪽 아래 모서리에서 캡처를 확인). F12 키를 눌러 트래픽 캡처를 시작 하거나 중지 합니다.

    ![troubleshoot-session-affinity-issues-13](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 일반적으로 암호 해독 된 HTTPS 트래픽을 Fiddler, **도구** > **옵션**을 선택 하 여 https 암호 해독을 사용 하도록 설정 하 고 " **https 트래픽 암호 해독**" 확인란을 선택 하면 됩니다.

    ![troubleshoot-session-affinity-issues-14](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. **X** (아이콘)을 클릭 하 여 문제를 재현 하기 전에 관련 없는 이전 세션을 제거할 수 있습니다 > 다음 스크린샷 **모두 제거** 를 클릭 합니다. 

    ![troubleshoot-session-affinity-issues-15](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 문제를 재현 한 후 **파일** >  > **모든 세션** **저장** ...을 선택 하 여 검토를 위해 파일을 저장 합니다. 

    ![troubleshoot-session-affinity-issues-16](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 세션 로그를 확인 하 고 분석 하 여 문제를 확인 합니다.

    예:

- **예 A:** 요청이 클라이언트에서 전송 되 고 Application Gateway의 공용 IP 주소로 이동 하는 세션 로그를 찾은 후이 로그를 클릭 하 여 세부 정보를 확인 합니다.  오른쪽에서 아래쪽 상자의 데이터는 Application Gateway 클라이언트에 반환 되는 데이터입니다. "RAW" 탭을 선택 하 고 클라이언트가 "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*"를 수신 하는지 여부를 확인 합니다. 쿠키가 없거나 세션 선호도가 설정 되지 않았거나 Application Gateway 쿠키를 클라이언트에 다시 적용 하지 않는 경우

   > [!NOTE]
   > 이 ARRAffinity 값은 특정 백 엔드 서버에 클라이언트를 전송 하기 위해 Application Gateway 설정 하는 쿠키 id입니다.

   ![troubleshoot-session-affinity-issues-17](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **예 2:** 다음 세션 로그와 이전 세션 로그는 ARRAAFFINITY 설정 된 Application Gateway에 다시 응답 하는 클라이언트입니다. ARRAffinity 쿠키 id가 일치 하는 경우 이전에 사용 된 것과 같은 백 엔드 서버에 패킷을 보내야 합니다. 다음 여러 줄의 http 통신을 확인 하 여 클라이언트의 ARRAffinity 쿠키가 변경 되는지 확인 합니다.

   ![troubleshoot-session-affinity-issues-18](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 동일한 통신 세션의 경우 쿠키를 변경 하지 않아야 합니다. 오른쪽의 위쪽 상자를 선택 하 고, "쿠키" 탭을 선택 하 여 클라이언트에서 쿠키를 사용 하 고 있는지 여부를 확인 하 고 다시 Application Gateway으로 보냅니다. 그렇지 않은 경우 클라이언트 브라우저는 대화에 대한 쿠키를 유지 하 고 사용 하지 않습니다. 경우에 따라 클라이언트가 있을 수 있습니다.

 

## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
