---
title: '빠른 시작: Azure Maps를 사용하여 대화형 맵 검색'
description: 검색 가능한 대화형 맵을 만드는 방법을 알아봅니다. Azure Maps 계정을 만들고, 기본 키를 가져오고, 웹 SDK를 사용하여 지도 애플리케이션을 설정하는 방법을 참조하세요.
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: fa5cab35416f2629d39ec04803f90fae1a2e5f6d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031050"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>빠른 시작: Azure Maps를 사용하여 대화형 검색 맵 만들기

이 문서에서는 사용자에게 대화형 검색 환경을 제공하는 지도를 만드는 Azure Maps를 사용하는 방법을 보여줍니다. 여기서는 다음 기본 단계를 단계별로 안내합니다.

* 사용자 고유의 Azure Maps 계정을 만듭니다.
* 데모 웹 애플리케이션에서 사용할 기본 키를 가져옵니다.
* 데모 맵 애플리케이션을 다운로드하여 엽니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* [Azure Portal](https://portal.azure.com)에 로그인합니다.

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Azure Maps 계정 만들기

다음 단계에 따라 새 Azure Maps 계정을 만듭니다.

1. [Azure Portal](https://portal.azure.com)의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.
2. *Marketplace 검색* 상자에서 **Azure Maps**를 입력합니다.
3. *결과*에서 **Azure Maps**를 선택합니다. 맵 아래에 나타나는 **만들기** 단추를 클릭합니다.
4. **Maps 계정 만들기** 페이지에서 다음 값을 입력합니다.
    * 이 계정에 사용하려는 *구독*.
    * 이 계정에 대한 *리소스 그룹* 이름. *새로 만들기* 또는 *기존* 리소스 그룹 사용을 선택할 수도 있습니다.
    * 새 계정의 *이름*.
    * 이 계정에 대한 *가격 책정 계층*입니다.
    * *라이선스* 및 *개인정보처리방침*을 읽고 조건에 동의하는 확인란을 선택합니다.
    * **만들기** 단추를 클릭합니다.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="포털에서 Maps 계정 만들기":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>사용자 계정에 대한 기본 키 가져오기

Maps 계정이 성공적으로 만들어지면 Maps API를 쿼리할 수 있는 기본 키를 검색합니다.

1. 포털에서 Maps 계정을 엽니다.
2. 설정 섹션에서 **인증**을 선택합니다.
3. **기본 키**를 클립보드로 복사합니다. 이 자습서의 뒷부분에서 사용하기 위해 로컬로 저장합니다.

>[!NOTE]
> 기본 키 대신 구독 키를 사용하면 맵이 제대로 렌더링되지 않습니다. 또한 보안을 위해 기본 키와 보조 키 사이를 회전하는 것이 좋습니다. 키를 회전하려면 보조 키를 사용하도록 앱을 업데이트하고 배포한 다음, 기본 키 옆에 있는 주기/새로 고침 단추를 눌러 새 기본 키를 생성합니다. 이전 기본 키는 사용할 수 없습니다. 키 회전에 대한 자세한 내용은 [키 회전 및 감사를 사용하여 Azure Key Vault 설정](https://docs.microsoft.com/azure/key-vault/secrets/key-rotation-log-monitoring)을 참조하세요.

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Azure Portal에서 기본 Key Azure Maps 키 가져오기":::

## <a name="download-the-demo-application"></a>데모 애플리케이션 다운로드

1. [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html)로 이동합니다. 파일 내용을 복사합니다.
2. 이 파일의 콘텐츠를 **AzureMapDemo.html**에 로컬로 저장합니다. 텍스트 편집기에서 엽니다.
3. `<Your Azure Maps Key>` 문자열을 검색합니다. 이 문자열을 이전 섹션의 **기본 키** 값으로 바꿉니다.

## <a name="open-the-demo-application"></a>데모 애플리케이션 열기

1. 원하는 브라우저에서 **AzureMapDemo.html** 파일을 엽니다.
2. 로스앤젤레스 시의 지도를 관찰합니다. 확대 및 축소하여 확대/축소 수준에 따라 지도가 더 많거나 더 적은 정보로 자동으로 렌더링되도록 하는 방법을 확인합니다.
3. 지도의 기본 센터를 변경합니다. **AzureMapDemo.html** 파일에서 **center**라는 변수를 검색합니다. 이 변수에 대한 위도, 경도 쌍 값을 새 값인 **[-74.0060, 40.7128]** 로 바꿉니다. 파일을 저장하고 브라우저를 새로 고칩니다.
4. 대화형 검색 환경을 사용해 봅니다. 웹 애플리케이션 데모의 왼쪽 위 모서리에 있는 검색 상자에서 **식당**을 검색합니다.
5. 검색 상자 아래에 표시되는 주소와 위치의 목록 위로 마우스를 이동합니다. 지도의 해당 핀이 해당 위치에 대한 정보를 팝업하는 상태를 확인합니다. 프라이빗 비즈니스의 정보 보호를 위해 가상의 이름과 주소가 표시됩니다.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="대화형 맵 검색 웹 애플리케이션":::


## <a name="clean-up-resources"></a>리소스 정리

>[!WARNING]
>[다음 단계](#next-steps) 섹션에 나열된 이 자습서에서는 계정에서 Azure Maps를 사용하고 구성하는 방법에 대해 자세히 설명되어 있습니다. 자습서를 계속 진행하려면 이 빠른 시작에서 만든 리소스를 정리하지 마세요.

자습서를 계속 진행하지 않으려면 다음 단계에 따라 리소스를 정리합니다.

1. **AzureMapDemo.html** 웹 애플리케이션을 실행하는 브라우저를 닫습니다.
2. Azure Portal 페이지로 이동합니다. 기본 포털 페이지에서 **모든 리소스**를 선택합니다. 또는 왼쪽 위 모서리에 있는 메뉴 아이콘을 클릭합니다. **모든 리소스**를 선택합니다.
3. Azure Maps 계정을 클릭합니다. 페이지 위쪽에서 **삭제**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Maps 계정을 만들고 데모 애플리케이션을 만들었습니다. Azure Maps에 대해 자세히 알아보려면 다음 자습서를 살펴보세요.

> [!div class="nextstepaction"]
> [Azure Maps를 사용하여 주변 관심 지점 검색](tutorial-search-location.md)

더 많은 코드 예제 및 대화형 코딩 환경은 다음 가이드를 참조하세요.

> [!div class="nextstepaction"]
> [Azure Maps 검색 서비스를 사용하여 주소 찾기](how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Azure Maps 맵 컨트롤 사용](how-to-use-map-control.md)
