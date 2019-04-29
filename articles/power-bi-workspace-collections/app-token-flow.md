---
title: Power BI 작업 영역 컬렉션으로 인증 및 권한 부여 | Microsoft Docs
description: Power BI 작업 영역 컬렉션으로 인증 및 권한 부여.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-workspace-collections
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 5fbe08bb9ebc355074a790f7732c9de10eaad859
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62110685"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Power BI 작업 영역 컬렉션으로 인증 및 권한 부여

Power BI 작업 영역 컬렉션 서비스는 명시적인 최종 사용자 인증 대신 인증 및 권한 부여에 대한 **키**와 **앱 토큰**을 사용합니다. 이 모델에서 애플리케이션이 최종 사용자에 대한 인증 및 권한 부여를 관리합니다. 필요한 경우 앱이 서비스에 요청된 보고서를 렌더링하라고 지시하는 앱 토큰을 만들어 보냅니다. 이 디자인에서는 앱이 사용자 인증 및 권한 부여에 Azure Active Directory를 사용할 수는 있지만 그럴 필요가 없습니다.

> [!IMPORTANT]
> Power BI 작업 영역 컬렉션은 2018년 6월 또는 계약에서 명시한 때까지만 사용할 수 있으며 이후에는 사용되지 않습니다. 애플리케이션에서 중단을 방지하기 위해 Power BI Embedded에 대한 마이그레이션을 계획하는 것이 좋습니다. Power BI Embedded에 데이터를 마이그레이션하는 방법에 대한 자세한 내용은 [Power BI Embedded에 Power BI 작업 영역 컬렉션 콘텐츠를 마이그레이션하는 방법](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/)을 참조하세요.

## <a name="two-ways-to-authenticate"></a>인증에 대한 두 가지 방법

**키** - 모든 Power BI 작업 영역 컬렉션 REST API 호출에 키를 사용할 수 있습니다. 키는 **Microsoft Azure Portal**에서 **모든 설정**, **액세스 키**를 차례로 선택하여 찾을 수 있습니다. 키는 항상 암호처럼 다루어야 합니다. 특정 작업 영역 컬렉션에서 모든 REST API 호출을 수행하는 권한이 이러한 키에 있습니다.

REST 호출에서 키를 사용하려면 다음 권한 부여 헤더를 추가합니다.

    Authorization: AppKey {your key}

**앱 토큰** - 앱 토큰은 모든 포함 요청에 사용됩니다. 클라이언트 쪽에서 실행되도록 설계되었습니다. 토큰은 단일 보고서 및 만료 시간을 설정하는 모범 사례로 제한됩니다.

앱 토큰은 사용자 키 중 하나로 서명된 JWT(JSON Web Token)입니다.

앱 토큰은 다음 클레임을 포함할 수 있습니다.

| 클레임 | 설명 |    
| --- | --- |
| **ver** |앱 토큰의 버전으로 현재 버전은 0.2.0입니다. |
| **aud** |토큰의 의도한 수신자입니다. Power BI 작업 영역 컬렉션의 경우 *https:\//analysis.windows.net/powerbi/api*를 사용합니다. |
| **iss** |토큰을 발급한 애플리케이션을 나타내는 문자열입니다. |
| **type** |생성되는 앱 토큰의 유형입니다. 현재 지원되는 유일한 유형은 **embed**입니다. |
| **wcn** |토큰이 발급되는 대상 작업 영역 컬렉션 이름입니다. |
| **wid** |토큰이 발급되는 대상 작업 영역 ID입니다. |
| **rid** |토큰이 발급되는 보고서 ID입니다. |
| **username** (선택 사항) |username은 RLS에 사용되며 RLS 규칙을 적용할 때 사용자를 식별하는 데 사용할 수 있는 문자열입니다. |
| **roles** (선택 사항) |행 수준 보안 규칙을 적용할 때 선택할 역할이 들어 있는 문자열입니다. 둘 이상의 역할을 전달하는 경우 문자열 배열로 전달해야 합니다. |
| **scp**(선택 사항) |사용 권한 범위를 포함하는 문자열입니다. 둘 이상의 역할을 전달하는 경우 문자열 배열로 전달해야 합니다. |
| **exp** (선택 사항) |토큰이 만료될 시간을 나타냅니다. Unix 타임스탬프로 전달되어야 합니다. |
| **nbf** (선택 사항) |토큰이 유효해지는 시작 시간을 나타냅니다. Unix 타임스탬프로 전달되어야 합니다. |

샘플 앱 토큰은 다음과 같습니다.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

디코딩되면 다음과 같은 모양입니다.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

앱 토큰을 쉽게 생성하는 SDK 내에서 사용할 수 있는 방법이 있습니다. 예를 들어, .NET의 경우 [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) 클래스 및 [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) 메서드를 살펴볼 수 있습니다.

.NET SDK의 경우 [범위](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes)를 참조할 수 있습니다.

## <a name="scopes"></a>범위

Embed 토큰으로 사용하는 경우 액세스 권한을 제공하는 리소스 사용을 제한하려고 할 수 있습니다. 이러한 이유로 범위가 지정된 사용 권한으로 토큰을 생성할 수 있습니다.

Power BI 작업 영역 컬렉션에 사용 가능한 범위는 다음과 같습니다.

|범위|설명|
|---|---|
|Dataset.Read|지정된 데이터 세트를 읽을 수 있는 사용 권한을 제공합니다.|
|Dataset.Write|지정된 데이터 세트를 쓸 수 있는 사용 권한을 제공합니다.|
|Dataset.ReadWrite|지정된 데이터 세트를 읽고 쓸 수 있는 사용 권한을 제공합니다.|
|Report.Read|지정된 보고서를 볼 수 있는 사용 권한을 제공합니다.|
|Report.ReadWrite|지정된 보고서를 보고 편집할 수 있는 사용 권한을 제공합니다.|
|Workspace.Report.Create|지정된 작업 영역 내에서 새 보고서를 만들 수 있는 사용 권한을 제공합니다.|
|Workspace.Report.Copy|지정된 작업 영역 내에서 기존 보고서를 복제할 수 있는 사용 권한을 제공합니다.|

다음과 같은 범위 간의 공백을 사용하여 여러 범위를 제공할 수 있습니다.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**필요한 클레임 - 범위**

scp: {scopesClaim} scopesClaim은 문자열 또는 문자열의 배열로서 작업 영역 리소스(예: 보고서, 데이터 세트 등)에 허용되는 사용 권한을 확인할 수 있습니다

정의된 범위를 사용하여 디코딩된 토큰은 다음과 비슷합니다.

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>작업 및 범위

|작업(Operation)|대상 리소스|토큰 사용 권한|
|---|---|---|
|데이터 세트를 기반으로 새 메모리 내 보고서를 만듭니다.|데이터 세트|Dataset.Read|
|데이터 세트를 기반으로 새 메모리 내 보고서를 만들고 보고서를 저장합니다.|데이터 세트|* Dataset.Read<br>* Workspace.Report.Create|
|기존 메모리 내 보고서를 보고 탐색/편집합니다. Report.Read는 Dataset.Read를 의미합니다. Report.Read는 편집 내용 저장을 허용하지 않습니다.|보고서|Report.Read|
|기존 보고서를 편집하고 저장합니다.|보고서|Report.ReadWrite|
|보고서(다른 이름으로 저장)의 복사본을 저장합니다.|보고서|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>다음은 흐름 방식입니다.
1. 애플리케이션에 API 키 복사 **Azure Portal**에서 키를 가져올 수 있습니다.
   
    ![Azure Portal에서 API 키를 찾을 수 있는 곳](media/get-started-sample/azure-portal.png)
1. 토큰이 클레임을 어설션하며 만료 시간이 있습니다.
   
    ![앱 토큰 흐름 - 토큰 클레임 어설션](media/get-started-sample/token-2.png)
1. 토큰이 API 액세스 키로 서명됩니다.
   
    ![앱 토큰 흐름 - 토큰이 서명됨](media/get-started-sample/token-3.png)
1. 사용자가 보고서 보기를 요청합니다.
   
    ![앱 토큰 흐름 - 사용자가 보고서 보기를 요청](media/get-started-sample/token-4.png)
1. API 액세스 키로 토큰의 유효성이 검사됩니다.
   
   ![앱 토큰 흐름 - 토큰이 검사됨](media/get-started-sample/token-5.png)
1. Power BI 작업 영역 컬렉션이 사용자에게 보고서를 보냅니다.
   
   ![앱 토큰 흐름 - 서비스가 사용자에게 보고서를 보냄](media/get-started-sample/token-6.png)

**Power BI 작업 영역 컬렉션**에서 사용자에게 보고서를 보낸 후 사용자는 사용자 지정 앱에서 보고서를 볼 수 있습니다. 예를 들어 [판매 데이터 PBIX 분석 샘플](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix)을 가져온 경우 샘플 웹앱이 다음과 같이 보입니다.

![애플리케이션에 포함된 보고서 샘플](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>관련 항목

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Microsoft Power BI 작업 영역 컬렉션 시작 샘플](get-started-sample.md)  
[일반적인 Microsoft Power BI 작업 영역 컬렉션 시나리오](scenarios.md)  
[Microsoft Power BI 작업 영역 컬렉션 시작](get-started.md)  
[PowerBI-CSharp Git 리포지토리](https://github.com/Microsoft/PowerBI-CSharp)

궁금한 점이 더 있나요? [Power BI 커뮤니티를 이용하세요.](https://community.powerbi.com/)
