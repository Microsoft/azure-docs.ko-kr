---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 33cc9b52c4b687ca8147867b99dc70e8d71a9223
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88944431"
---
:::row:::
    :::column span="3":::
        Speech SDK는 Windows 10 및 Windows Server 2016 이상 버전을 지원 합니다. 이전 버전은 공식적으로 지원 **되지 않습니다** . 음성 SDK의 일부를 이전 버전의 Windows와 함께 사용할 수 있지만 권장 되지는 않습니다.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Windows" src="https://docs.microsoft.com/media/logos/logo_Windows.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### <a name="system-requirements"></a>시스템 요구 사항

Windows의 Speech SDK를 사용 하려면 시스템에서 <a href="https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads" target="_blank">Visual Studio 2019 <span class="docon docon-navigate-external x-hidden-focus"></span> 에 대 한 Microsoft Visual C++ 재배포 가능 패키지가</a> 필요 합니다.

- <a href="https://aka.ms/vs/16/release/vc_redist.x86.exe" target="_blank">X 86 용 설치 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.x64.exe" target="_blank">X 64 용 설치 <span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://aka.ms/vs/16/release/vc_redist.arm64.exe" target="_blank">ARMx64 설치 <span class="docon docon-navigate-external x-hidden-focus"></span></a>

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

마이크 입력을 위해 미디어 파운데이션 라이브러리를 설치해야 합니다. 이러한 라이브러리는 Windows 10 및 Windows Server 2016에 포함됩니다. 마이크를 오디오 입력 디바이스로 사용하지 않는 경우에는 이러한 라이브러리 없이 Speech SDK를 사용할 수 있습니다.

필요한 Speech SDK 파일은 애플리케이션과 동일한 디렉터리에 배포할 수 있습니다. 이렇게 하면 애플리케이션이 라이브러리에 직접 액세스할 수 있습니다. 응용 프로그램과 일치 하는 올바른 버전 (x86/x64)을 선택 했는지 확인 합니다.

| 속성                                            | 기능                                             |
|-------------------------------------------------|------------------------------------------------------|
| `Microsoft.CognitiveServices.Speech.core.dll`   | 네이티브 및 관리된 배포에 필요한 핵심 SDK |
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 관리된 배포에 필요                      |

> [!NOTE]
> 릴리스부터 릴리스 1.3.0는 파일 `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` (이전 릴리스에서 제공 됨)이 더 이상 필요 하지 않습니다. 이제 기능이 핵심 SDK에 통합 되었습니다.

> [!IMPORTANT]
> Windows Forms App (.NET Framework) c # 프로젝트의 경우 라이브러리가 프로젝트의 배포 설정에 포함 되어 있는지 확인 합니다. 에서이를 확인할 수 있습니다 `Properties -> Publish Section` . 단추를 클릭 `Application Files` 하 고 스크롤 다운 목록에서 해당 라이브러리를 찾습니다. 값이로 설정 되어 있는지 확인 `Included` 합니다. 프로젝트를 게시/배포할 때 Visual Studio에 파일이 포함 됩니다.

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
