---
title: '자습서: Azure IoT Central을 사용하여 용수 품질 모니터링 앱 만들기'
description: '자습서: Azure IoT Central 애플리케이션 템플릿을 사용하여 용수 품질 모니터링 애플리케이션을 만드는 방법을 알아봅니다.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 77ec999d63175f63c1de6e31fdb3f72c963d228c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82024475"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>자습서: Azure IoT Central에서 용수 품질 모니터링 애플리케이션 만들기



이 자습서에서는 Azure IoT Central에서 용수 품질 모니터링 애플리케이션을 만드는 과정을 안내합니다. Azure IoT Central **용수 품질 모니터링** 애플리케이션 템플릿에서 애플리케이션을 만듭니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * **용수 품질 모니터링** 템플릿을 사용하여 용수 품질 모니터링 애플리케이션을 만듭니다.
> * 운영자 대시보드를 살펴보고 사용자 지정합니다.
> * 용수 품질 모니터링 디바이스 템플릿을 살펴봅니다.
> * 시뮬레이션된 디바이스를 살펴봅니다.
> * 규칙을 살펴보고 구성합니다.
> * 작업을 구성합니다.
> * 흰색 레이블 지정을 사용하여 애플리케이션 브랜딩을 사용자 지정합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 Azure 구독을 보유하는 것이 좋습니다. Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 만들 수 있습니다.

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Azure IoT Central에서 용수 품질 모니터링 애플리케이션 만들기

이 섹션에서는 Azure IoT Central **용수 품질 모니터링** 템플릿을 사용하여 용수 품질 모니터링 애플리케이션을 만듭니다.

1. [Azure IoT Central 홈 페이지](https://aka.ms/iotcentral)로 이동합니다.

    Azure 구독이 있으면 액세스하는 데 사용하는 자격 증명을 사용하여 로그인합니다. 그렇지 않으면 Microsoft 계정을 사용하여 로그인합니다.

    ![조직 계정에 로그인](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Azure IoT Central의 맨 왼쪽 창에서 **빌드**, **정부** 탭을 차례로 선택합니다. 정부 창에는 몇 가지 정부 애플리케이션 템플릿이 표시됩니다.

    ![정부 애플리케이션 템플릿](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. **용수 품질 모니터링** 애플리케이션 템플릿을 선택합니다. 이 애플리케이션 템플릿에는 용수 품질 디바이스 템플릿, 시뮬레이션된 디바이스, 운영자 대시보드 및 미리 구성된 모니터링 규칙이 포함되어 있습니다.

1. **앱 만들기**를 선택합니다. **새 애플리케이션** 창이 열리고 다음 요소가 표시됩니다.

    * **애플리케이션 이름**: 기본적으로 애플리케이션 이름은 **용수 품질 모니터링**이며, Azure IoT Central에서 생성하는 고유한 ID 문자열이 뒤에 붙습니다. 원하는 경우 표시 이름을 입력하거나 나중에 애플리케이션 이름을 변경할 수 있습니다.
    * **URL**: 원하는 URL을 입력하거나 나중에 URL 값을 변경할 수 있습니다.
    * Azure 구독이 있으면 **디렉터리**, **Azure 구독** 및 **지역**에 대한 값을 입력합니다. 구독이 없으면 **7일 평가판**을 설정하고 필요한 연락처 정보를 작성할 수 있습니다.

    디렉터리 및 구독에 대한 자세한 내용은 [애플리케이션 만들기](../core/quick-deploy-iot-central.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) 빠른 시작을 참조하세요.

1. 페이지의 왼쪽 아래 부분에서 **만들기** 단추를 선택합니다.

    ![Azure IoT Central 새 애플리케이션 페이지](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Azure IoT Central 새 애플리케이션 청구 정보](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

이제 Azure IoT Central **용수 품질 모니터링** 템플릿을 사용하여 용수 품질 모니터링 애플리케이션이 만들어졌습니다.

새 애플리케이션에서 제공하는 미리 구성된 구성 요소는 다음과 같습니다.

* 운영자 대시보드
* 용수 품질 모니터링 디바이스 템플릿
* 시뮬레이션된 용수 품질 모니터링 디바이스
* 규칙 및 작업
* 흰색 레이블 지정을 사용하는 브랜딩

애플리케이션은 언제든지 수정할 수 있습니다.

다음으로, 애플리케이션을 살펴보고 일부 사용자 지정을 수행합니다.

## <a name="explore-and-customize-the-operator-dashboard"></a>운영자 대시보드 살펴보기 및 사용자 지정

애플리케이션이 만들어지면 **Wide World 용수 품질 대시보드** 창이 열립니다.

   ![용수 품질 모니터링 대시보드](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

작성자는 대시보드에서 운영자가 사용할 보기를 만들고 사용자 지정할 수 있습니다. 그러나 사용자 지정을 수행하기 전에 먼저 대시보드를 살펴보세요.

대시보드에 표시되는 모든 데이터는 시뮬레이션된 디바이스 데이터를 기반으로 하며, 다음 섹션에서 설명합니다.

대시보드에 포함된 타일의 종류는 다음과 같습니다.

* **Wide World 용수 유틸리티 이미지 타일**: 대시보드의 왼쪽 위 모서리에 있는 첫 번째 타일은 Wide World라는 가상 유틸리티를 보여주는 이미지입니다. 사용자 고유의 이미지를 사용할 수 있도록 타일을 사용자 지정하거나 타일을 제거할 수 있습니다.

* **평균 pH KPI 타일**: **지난 30분 동안 평균 pH**와 같은 KPI 타일은 대시보드 창의 위쪽에 있습니다. KPI 타일을 사용자 지정하고, 각 타일을 다른 유형 및 시간 범위로 설정할 수 있습니다.

* **용수 모니터링 영역 지도**: Azure IoT Central은 애플리케이션에서 직접 설정하여 디바이스 위치를 표시할 수 있는 Azure Maps를 사용합니다. 또한 애플리케이션의 위치 정보를 디바이스에 매핑한 다음, Azure Maps를 사용하여 정보를 지도에 표시할 수도 있습니다. 마우스로 지도 위를 가리키며 컨트롤을 사용해 보세요.

* **평균 pH 분포 열 지도 차트**: 다양한 시각화 차트를 선택하여 디바이스 원격 분석을 애플리케이션에 가장 적합한 방식으로 표시할 수 있습니다.

* **중요 품질 지표 꺾은선형 차트**: 디바이스 원격 분석을 시간 범위에 걸친 꺾은선형 차트로 그려서 시각화할 수 있습니다.  

* **화학 작용제 농도 막대형 차트**: 디바이스 원격 분석을 막대형 차트로 시각화할 수 있습니다.

* **작업 단추**: 대시보드에는 운영자가 모니터링 대시보드에서 직접 시작할 수 있는 작업에 대한 타일이 포함되어 있습니다. 디바이스의 속성을 다시 설정하는 것은 이러한 작업의 예입니다.

* **속성 목록 타일**: 대시보드에는 임계값 정보, 디바이스 상태 정보 및 유지 관리 정보를 나타내는 여러 속성 타일이 있습니다.

### <a name="customize-the-dashboard"></a>대시보드 사용자 지정

작성자는 대시보드에서 운영자가 사용할 보기를 사용자 지정할 수 있습니다.

1. **편집**을 선택하여 **Wide World 용수 품질 대시보드** 창을 사용자 지정합니다. **편집** 메뉴에서 명령을 선택하여 대시보드를 사용자 지정할 수 있습니다. 대시보드가 편집 모드에 있으면 새 타일을 추가하거나 기존 파일을 구성할 수 있습니다.

    ![대시보드 편집](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. **+ 새로 만들기**를 선택하여 구성할 수 있는 새 대시보드를 만듭니다. 여러 대시보드를 사용할 수 있으며, 대시보드 메뉴에서 서로 간에 탐색할 수 있습니다.

## <a name="explore-a-water-quality-monitoring-device-template"></a>용수 품질 모니터링 디바이스 템플릿 살펴보기

Azure IoT Central의 디바이스 템플릿은 디바이스 기능을 정의합니다. 사용 가능한 기능은 원격 분석, 속성 및 명령입니다. 작성자는 Azure IoT Central에서 연결된 디바이스의 기능을 나타내는 디바이스 템플릿을 정의할 수 있습니다. 또한 시뮬레이션된 디바이스를 만들어 디바이스 템플릿과 애플리케이션을 테스트할 수도 있습니다.

만든 용수 품질 모니터링 애플리케이션에는 용수 품질 모니터링 디바이스 템플릿이 제공됩니다.

디바이스 템플릿을 보려면 다음을 수행합니다.

1. Azure IoT Central에서 애플리케이션의 맨 왼쪽 창에 있는 **디바이스 템플릿**을 선택합니다.
1. 디바이스 템플릿 목록에서 **용수 품질 모니터**를 선택합니다. 해당 디바이스 템플릿이 열립니다.

    ![디바이스 템플릿](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>디바이스 템플릿 사용자 지정

다음 디바이스 템플릿 설정을 사용자 지정하는 방법을 연습합니다.

1. 디바이스 템플릿 메뉴에서 **사용자 지정**을 선택합니다.
1. **온도** 원격 분석 유형으로 이동합니다.
1. **표시 이름** 값을 **보고된 온도**로 변경합니다.
1. 측정 단위를 변경하거나 **최솟값** 및 **최댓값**을 설정합니다.
1. **저장**을 선택합니다.

#### <a name="add-a-cloud-property"></a>클라우드 속성을 추가합니다.

1. 디바이스 템플릿 메뉴에서 **클라우드 속성**을 선택합니다.
1. 새 클라우드 속성을 추가하려면 **+ 클라우드 속성 추가**를 선택합니다. Azure IoT Central에서는 디바이스와 관련이 있지만 디바이스에서 보낼 필요가 없는 속성을 추가할 수 있습니다. 이러한 속성의 한 가지 예로 설치 영역, 자산 정보 또는 유지 관리 정보와 관련된 경고 임계값이 있습니다.
1. **저장**을 선택합니다.

### <a name="explore-views"></a>보기 살펴보기

용수 품질 모니터링 디바이스 템플릿에는 미리 정의된 보기가 제공됩니다. 보기는 운영자가 디바이스 데이터를 보고 클라우드 속성을 설정하는 방법을 정의합니다. 보기를 살펴보고 변경하는 방법을 연습합니다.

  ![디바이스 템플릿 보기](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>디바이스 템플릿 게시

변경한 경우 **게시**를 선택하여 디바이스 템플릿을 게시해야 합니다.

### <a name="create-a-new-device-template"></a>새 디바이스 템플릿 만들기

1. **+ 새로 만들기**를 선택하여 새 디바이스 템플릿을 만들고 만들기 프로세스를 수행합니다.
1. 사용자 지정 디바이스 템플릿을 만들거나, Azure IoT 디바이스 카탈로그에서 디바이스 템플릿을 선택합니다.

## <a name="explore-simulated-devices"></a>시뮬레이션된 디바이스 살펴보기

애플리케이션 템플릿에서 만든 용수 품질 모니터링 애플리케이션에는 두 개의 시뮬레이션된 디바이스가 있습니다. 이러한 디바이스는 용수 품질 모니터링 디바이스 템플릿에 매핑됩니다.

### <a name="view-the-devices"></a>디바이스 보기

1. 애플리케이션의 맨 왼쪽 창에서 **디바이스**를 선택합니다.

   ![디바이스](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. 하나의 시뮬레이션된 디바이스를 선택합니다.

    ![디바이스 1 선택](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. **클라우드 속성** 탭에서 **산도(pH) 임계값**을 **8**에서 **9**로 변경합니다.
1. **디바이스 속성** 탭 및 **디바이스 대시보드** 탭을 살펴봅니다.

> [!NOTE]
> 모든 탭은 **디바이스 템플릿 보기**에서 구성되었습니다.

### <a name="add-new-devices"></a>새 디바이스 추가

**디바이스** 탭에서 **+ 새로 만들기**를 선택하여 새 디바이스를 추가합니다.

## <a name="explore-and-configure-rules"></a>규칙 살펴보기 및 구성

Azure IoT Central에서는 디바이스 원격 분석을 자동으로 모니터링하는 규칙을 만들 수 있습니다. 이러한 규칙은 조건 중 하나라도 충족되면 작업을 트리거합니다. 가능한 작업 중 하나는 이메일 알림을 보내는 것입니다. 다른 가능한 작업으로 데이터를 다른 서비스에 보내는 Microsoft Flow 작업 또는 웹후크 작업이 있습니다.

만든 용수 품질 모니터링 애플리케이션에는 미리 구성된 두 가지 규칙이 있습니다.

### <a name="view-rules"></a>규칙 보기

1. 애플리케이션의 맨 왼쪽 창에서 **규칙**을 선택합니다.

   ![규칙](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. 애플리케이션의 미리 구성된 규칙 중 하나인 **높은 pH 경고**를 선택합니다.

   ![높은 pH 경고 규칙](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   **높은 pH 경고** 규칙은 8보다 큰 산도(pH) 조건인지 확인하도록 구성되어 있습니다.

다음으로, 이메일 작업을 규칙에 추가합니다.

1. **+메일**을 선택합니다.
1. **표시 이름** 상자에서 **높은 pH 경고**를 입력합니다.
1. **받는 사람** 상자에서 Azure IoT Central 계정과 연결된 이메일 주소를 입력합니다.
1. 필요에 따라 이메일 텍스트에 포함할 메모를 입력합니다.
1. **완료**를 선택하여 작업을 완료합니다.
1. **저장**을 선택하여 새 규칙을 저장하고 활성화합니다.

구성된 조건이 충족되면 이메일을 몇 분 내에 받게 됩니다.

> [!NOTE]
> 조건이 충족될 때마다 애플리케이션에서 이메일을 보냅니다. 해당 규칙에서 자동 이메일 받기를 중지하려면 **사용 안 함**을 선택하세요.
  
새 규칙을 만들려면 애플리케이션의 맨 왼쪽 창에서 **규칙**을 선택한 다음, **+ 새로 만들기**를 선택합니다.

## <a name="configure-jobs"></a>작업 구성

Azure IoT Central 작업을 사용하면 여러 디바이스에서 디바이스 또는 클라우드 속성에 대한 업데이트를 트리거할 수 있습니다. 또한 작업을 사용하여 여러 디바이스에서 디바이스 명령을 트리거할 수도 있습니다. Azure IoT Central에서는 워크플로를 자동화합니다.

1. 애플리케이션의 맨 왼쪽 창에서 **작업**을 선택합니다.
1. **+ 새로 만들기**를 선택하고 하나 이상의 작업을 구성합니다.

## <a name="customize-your-application"></a>애플리케이션 사용자 지정

빌더는 여러 설정을 변경하여 애플리케이션에서 사용자 환경을 사용자 지정할 수 있습니다.

1. **관리** > **애플리케이션 사용자 지정**을 차례로 선택합니다.
1. **애플리케이션 로고** 아래에서 **변경**을 선택하여 로고로 업로드할 이미지를 선택합니다.
1. **브라우저 아이콘** 아래에서 **변경**을 선택하여 브라우저 탭에 나타나는 이미지를 선택합니다.
1. **브라우저 색** 아래에서 기본값을 HTML 16진수 색 코드로 바꿀 수 있습니다.
1. **설정**을 선택하여 **테마** 값을 변경합니다.

   ![애플리케이션 사용자 지정](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>애플리케이션 이미지 업데이트

1. **관리** > **애플리케이션 설정**을 차례로 선택합니다.

1. **이미지 선택** 단추를 사용하여 애플리케이션 이미지로 업로드할 이미지를 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션을 계속 사용하지 않으려면 다음 단계를 사용하여 애플리케이션을 삭제합니다.

1. 애플리케이션의 맨 왼쪽 창에서 **관리** 탭을 엽니다.
1. **애플리케이션 설정**, **삭제** 단추를 차례로 선택합니다.

    ![애플리케이션 삭제](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>다음 단계

* [용수 품질 모니터링 개념](./concepts-waterqualitymonitoring-architecture.md)에 대해 자세히 알아보세요.
