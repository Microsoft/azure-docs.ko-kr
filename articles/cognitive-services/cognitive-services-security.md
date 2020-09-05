---
title: Azure Cognitive Services 보안
titleSuffix: Azure Cognitive Services
description: Cognitive Services 사용에 대 한 다양 한 보안 고려 사항에 대해 알아봅니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: erhopf
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp
ms.openlocfilehash: d74a2c45c1d2b2b03c35e7235994980a64cba979
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072114"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services 보안

모든 응용 프로그램을 개발할 때는 보안을 최우선 순위로 고려해 야 합니다. 인공 지능을 사용 하는 응용 프로그램의 하기 시작 하면 보안은 훨씬 더 중요 합니다. 이 문서에서는 전송 계층 보안, 인증, 중요 한 데이터를 안전 하 게 구성 하는 방법, 고객 데이터 액세스를 위한 고객 Lockbox 등 Azure Cognitive Services 보안의 다양 한 측면을 간략하게 설명 합니다.

## <a name="transport-layer-security-tls"></a>TLS(전송 계층 보안)

HTTP를 통해 노출 되는 모든 Cognitive Services 끝점은 TLS 1.2을 적용 합니다. 적용 되는 보안 프로토콜을 사용 하 여 Cognitive Services 끝점을 호출 하려는 소비자는 다음 지침을 준수 해야 합니다.

* 클라이언트 운영 체제 (OS)는 TLS 1.2를 지원 해야 합니다.
* HTTP 호출을 수행 하는 데 사용 되는 언어 (및 플랫폼)는 요청의 일부로 TLS 1.2을 지정 해야 합니다.
  * 언어 및 플랫폼에 따라 TLS를 암시적으로 지정 하거나 명시적으로 지정 합니다.

.NET 사용자의 경우 <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">전송 계층 보안 모범 사례 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>를 고려 합니다.

## <a name="authentication"></a>인증

인증에 대해 논의할 때 몇 가지 일반적인 오해 있습니다. 인증 및 권한 부여는 서로 혼동 되는 경우가 많습니다. 또한 id는 보안의 주요 구성 요소입니다. Id는 <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">보안 주체 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>에 대 한 정보 컬렉션입니다. IdP (id 공급자)는 인증 서비스에 id를 제공 합니다. 인증은 사용자의 id를 확인 하는 동작입니다. 권한 부여는 지정 된 id에 대 한 리소스에 대 한 액세스 권한 및 권한을 지정 하는 것입니다. 몇 가지 Cognitive Services 제공에는 RBAC (역할 기반 액세스 제어)가 포함 됩니다. RBAC는 수동으로 보안 주체를 관리 하는 것과 관련 된 일부 공식 절차를 간소화 하는 데 사용할 수 있습니다. 자세한 내용은 [Azure 리소스에 대 한 역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조 하세요.

구독 키, 액세스 토큰 및 AAD (Azure Active Directory) 인증에 대 한 자세한 내용은 <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">Azure Cognitive Services <span class="docon docon-navigate-external x-hidden-focus"></span> 에 대 한 요청 인증</a>을 참조 하세요.

## <a name="environment-variables-and-application-configuration"></a>환경 변수 및 응용 프로그램 구성

환경 변수는 특정 환경 내에 저장 된 이름-값 쌍입니다. 중요 한 데이터에 하드 코드 된 값을 사용 하는 대신 환경 변수를 사용 하는 것이 더 안전 합니다. 하드 코드 된 값은 안전 하지 않으므로 피해 야 합니다.

> [!CAUTION]
> 중요 한 데이터에는 하드 코드 된 값을 사용 **하지** 마십시오 .이는 중요 한 보안 취약점입니다.

> [!NOTE]
> 환경 변수는 일반 텍스트로 저장 되지만 환경에 격리 됩니다. 환경이 손상 된 경우에도 환경에서 변수를 사용할 수 있습니다.

### <a name="set-environment-variable"></a>환경 변수 설정

환경 변수를 설정 하려면 다음 명령 중 하나를 사용 합니다. 여기서은 `ENVIRONMENT_VARIABLE_KEY` 명명 된 키이 고 `value` 는 환경 변수에 저장 된 값입니다.

# <a name="command-line"></a>[명령줄](#tab/command-line)

값이 지정 된 경우 지속형 환경 변수를 만들고 할당 합니다.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

**명령 프롬프트**의 새 인스턴스에서 환경 변수를 읽습니다.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

값이 지정 된 경우 지속형 환경 변수를 만들고 할당 합니다.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

**Windows PowerShell**의 새 인스턴스에서 환경 변수를 읽습니다.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

값이 지정 된 경우 지속형 환경 변수를 만들고 할당 합니다.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

**Bash**의 새 인스턴스에서 환경 변수를 읽습니다.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> 환경 변수를 설정한 후에는 IDE (통합 개발 환경)를 다시 시작 하 여 새로 추가 된 환경 변수를 사용할 수 있는지 확인 합니다.

### <a name="get-environment-variable"></a>환경 변수 가져오기

환경 변수를 가져오려면 메모리를 읽어야 합니다. 사용 중인 언어에 따라 다음 코드 조각을 고려 합니다. 이러한 코드 조각은가 지정 된 환경 변수를 가져오고 이라는 변수에 할당 하는 방법을 보여 줍니다 `ENVIRONMENT_VARIABLE_KEY` `value` .

# <a name="c"></a>[C#](#tab/csharp)

자세한 내용은 <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank">`Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

자세한 내용은 <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank">`getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

자세한 내용은 <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank">`System.getenv` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.JS](#tab/node-js)

자세한 내용은 <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank">`process.env` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

자세한 내용은 <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank">`os.environ` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

자세한 내용은 <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank">`environment` <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 참조하세요.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="customer-lockbox"></a>고객 Lockbox

[Microsoft Azure에 대 한 고객 Lockbox](../security/fundamentals/customer-lockbox-overview.md) 고객 데이터 액세스 요청을 검토 하 고 승인 하거나 거부할 수 있는 인터페이스를 제공 합니다. 지원 요청 시 Microsoft 엔지니어가 고객 데이터에 액세스 해야 하는 경우에 사용 됩니다. 이후 검토 및 감사를 위해 고객 Lockbox 요청을 시작, 추적 및 저장 하는 방법에 대 한 자세한 내용은 [고객 Lockbox](../security/fundamentals/customer-lockbox-overview.md)을 참조 하세요. 

이 인식 서비스에 대해 고객 Lockbox를 사용할 수 있습니다.

* Translator

다음 서비스의 경우 Microsoft 엔지니어가 E0 계층의 고객 데이터에 액세스 하지 않습니다. 

* Language Understanding
* Face
* Content Moderator
* Personalizer

> [!IMPORTANT]
> **양식 인식기**의 경우 Microsoft 엔지니어가 2020 년 7 월 10 일 이후에 생성 된 리소스에서 고객 데이터에 액세스 하지 않습니다.

E0 SKU를 사용 하는 기능을 요청 하려면이 [요청 양식을](https://aka.ms/cogsvc-cmk)작성 하 고 제출 합니다. 요청 상태를 다시 들으려면 영업일 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. LUIS에서 E0 SKU를 사용 하도록 승인 되 면 Azure Portal에서 새 리소스를 만들고이를 가격 책정 계층으로 선택 해야 합니다. 사용자는 F0에서 새 E0 SKU로 업그레이드할 수 없습니다.

음성 서비스는 현재 고객 Lockbox을 지원 하지 않습니다. 그러나 사용자 고유의 저장소 (BYOS)를 사용 하 여 고객 데이터를 저장할 수 있으므로 사용자는 비슷한 데이터 컨트롤을 사용 하 여 고객 Lockbox 수 있습니다. 음성 서비스 데이터는 음성 리소스가 생성 된 지역에서 유지 되 고 처리 됩니다. 이는 미사용 데이터 및 전송 중인 데이터에 적용 됩니다. 사용자 지정 기능을 사용 하는 경우 (예: Custom Speech 및 사용자 지정 음성) 모든 고객 데이터는 BYOS (사용 되는 경우) 및 음성 서비스 리소스가 있는 동일한 지역에서 전송, 저장 및 처리 됩니다.

> [!IMPORTANT]
> Microsoft **는** 고객 데이터를 사용 하 여 음성 모델을 개선 하지 않습니다. 또한 끝점 로깅을 사용 하지 않도록 설정 하 고 사용자 지정을 사용 하지 않은 경우에는 고객 데이터가 저장 되지 않습니다. 

## <a name="next-steps"></a>다음 단계

* 다양 한 [Cognitive Services](welcome.md) 살펴보기
* [Cognitive Services 가상 네트워크](cognitive-services-virtual-networks.md) 에 대 한 자세한 정보
