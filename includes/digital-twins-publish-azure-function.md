---
author: baanders
description: Visual Studio의 Azure 함수를 게시하는 프로세스를 위한 파일 포함
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480751"
---
Azure의 함수 앱에 프로젝트를 게시하려면 솔루션 탐색기에서 시작합니다. 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **게시** 를 선택합니다.

> [!IMPORTANT] 
> Azure의 함수 앱에 게시하면 Azure Digital Twins와 관계없이 구독에 추가 요금이 발생합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="마우스 오른쪽 단추 클릭 솔루션 메뉴를 표시하는 Visual Studio의 스크린샷. 메뉴에서 게시가 강조 표시됩니다.":::

다음에 나오는 **게시** 페이지에서 기본 대상 선택 항목인 **Azure** 를 그대로 둡니다. 그런 후 **다음** 을 선택합니다. 

특정 대상에 대해 **Azure Function 앱(Windows)** 을 선택하고 **다음** 을 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Azure 함수 게시 대화 상자를 보여주는 Visual Studio의 스크린샷. 특정 대상 페이지에서 선택 항목은 Azure Function 앱(Windows)입니다.":::

**함수 인스턴스** 탭에서 구독을 선택합니다. 그런 다음, 더하기(+) 아이콘을 선택하여 새 함수를 만듭니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Azure 함수 게시 대화 상자를 표시하는 Visual Studio의 스크린샷. 더하기 아이콘이 강조 표시됩니다.":::

**함수 앱(Windows) - 새로 만들기** 창에서 다음 필드를 입력합니다.
* **이름** 은 Azure에서 Azure Functions 앱을 호스트하는 데 사용하는 소비 계획의 이름입니다. 이 이름은 실제 함수를 보유하는 함수 앱에도 적용됩니다. 고유 값을 선택하거나 기본 제안을 그대로 둘 수 있습니다.
* **구독** 이 사용하려는 구독과 일치하는지 확인합니다. 
* **리소스 그룹** 이 사용하려는 것과 일치하는지 확인합니다.
* **계획 유형** 선택을 **소비** 로 둡니다.
* 리소스 그룹의 **위치** 를 선택합니다.
* **새로 만들기** 링크를 사용하여 새 **Azure Storage** 리소스를 만듭니다. 리소스 그룹에 맞게 위치를 설정하고, 다른 기본값을 사용한 다음, **확인** 을 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Azure 함수 게시 대화 상자를 표시하는 Visual Studio의 스크린샷. 이름, 구독, 리소스 그룹, 계획 유형, 위치 및 Azure Storage를 포함하여 새 함수 앱의 세부 정보가 채워집니다.":::

그런 다음 **만들기** 를 선택합니다.

앱 서비스를 만들면 **함수 인스턴스** 탭이 열립니다. 새 함수 앱이 리소스 그룹 아래의 **함수 앱** 영역에 나타납니다. **마침** 을 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Azure 함수 게시 대화 상자를 표시하는 Visual Studio의 스크린샷. 함수 인스턴스 탭이 선택됩니다. 새 함수 앱이 리소스 그룹 아래에 나타납니다.":::

주 Visual Studio 창에서 열린 **게시** 창의 모든 정보가 올바른지 확인합니다. 그런 다음, **게시** 를 선택합니다.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="게시 창을 표시하는 Visual Studio의 스크린샷. 게시 단추가 강조 표시됩니다.":::

> [!NOTE]
> 다음 예제와 같은 팝업 창이 표시되면 **Azure에서 자격 증명 검색 시도** 를 선택한 다음, **저장** 을 선택합니다.
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="자격 증명 게시라는 팝업 창을 표시하는 Visual Studio의 스크린샷. 여기에는 사용자 이름 및 암호 필드가 포함되었습니다. 또한 Azure에서 자격 증명 검색 시도 단추가 포함되어 있습니다." border="false":::
>
> 다음 경고 중 하나가 표시되면 프롬프트에 따라 최신 Azure Functions 런타임 버전으로 업그레이드합니다.
> * "Azure의 함수 버전을 업그레이드하세요."
> * "함수 런타임 버전이 Azure에서 실행 중인 버전과 일치하지 않습니다."
>
> 이전 버전의 Visual Studio를 사용하는 경우 이러한 경고가 나타날 수 있습니다.

이제 함수 앱이 Azure에 게시됩니다.
