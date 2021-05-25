---
author: baanders
description: Azure Digital Twins 자습서에 대한 파일 포함 - 샘플 프로젝트 구성
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 1eceb842947abd8975036c3145c1da755ef59526
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108766775"
---
## <a name="configure-the-sample-project"></a>샘플 프로젝트 구성

다음으로, Azure Digital Twins 인스턴스와 상호 작용하는 샘플 클라이언트 애플리케이션을 설정합니다.

머신에서 이전에 [Azure Digital Twins 엔드투엔드 샘플](/samples/azure-samples/digital-twins-samples/digital-twins-samples)에서 다운로드한 파일로 이동합니다(아직 압축을 풀지 않았다면 압축을 풉니다).

폴더 내에서 _AdtSampleApp_ 으로 이동합니다. Visual Studio 2019에서 _**AdtE2ESample.sln**_ 을 엽니다. 

Visual Studio에서 _SampleClientApp > **appsettings.json**_ 파일을 선택하여 편집 창에서 엽니다. 이 파일은 프로젝트를 실행하는 데 필요한 구성 변수와 함께 미리 설정된 JSON 파일 역할을 합니다.

파일 본문에서 `instanceUrl`을 Azure Digital Twins 인스턴스 *호스트 이름 URL* 로 변경합니다(아래에 표시된 대로 *호스트 이름* 앞에 **_https://_** 추가).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-host-name>"
}
```

파일을 저장하고 닫습니다. 

다음으로, *appsettings.json* 파일을 구성하여 *SampleClientApp* 을 빌드할 때 출력 디렉터리로 복사되도록 합니다. 이렇게 하려면 *appsettings.json* 파일을 마우스 오른쪽 단추로 선택하고 **속성** 을 선택합니다. **속성** 검사기에서 *출력 디렉터리로 복사* 속성을 찾습니다. 아직 설정되어 있지 않은 경우 **새 버전이면 복사** 로 값을 변경합니다.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="appsettings.json이 강조 표시된 솔루션 탐색기 창과 '출력 디렉터리로 복사' 속성이 '변경된 내용만 복사'로 설정된 속성 창을 보여주는 Visual Studio 창에서 발췌" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

_**AdtE2ESample**_ 프로젝트를 자습서에서 계속 사용해야 하므로 Visual Studio에서 열어 둡니다.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
