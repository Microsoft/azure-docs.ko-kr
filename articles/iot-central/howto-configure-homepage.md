---
title: Azure IoT Central 응용 프로그램 대시보드를 구성 합니다. | Microsoft Docs
description: 작성기를으로 기본 Azure IoT Central 응용 프로그램 대시보드를 구성 하는 방법에 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 3168bbbf70c1ffeb3827482459febbcea256eda6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886986"
---
# <a name="configure-the-application-dashboard"></a>응용 프로그램 대시보드를 구성 합니다.

합니다 **대시보드** 응용 프로그램에 대 한 액세스 권한이 있는 사용자는 응용 프로그램의 URL로 이동 하는 경우 로드 하는 페이지입니다. 중 하나를 선택한 경우에 **샘플 Contoso** 또는 **샘플 Devkits** 응용 프로그램, 응용 프로그램을 만드는 응용 프로그램 템플릿에 미리 정의 된 대시보드. 선택한 경우 합니다 **사용자 지정 응용 프로그램** 응용 프로그램 템플릿을 대시보드가 비어 있습니다. 합니다.

> [!NOTE]
> 사용자 수도 [고유한 개인 대시보드를 만들](howto-personalize-dashboard.md) 대신 기본 응용 프로그램 대시보드를 사용 하도록 합니다.

## <a name="add-tiles"></a>타일 추가

다음 스크린샷은 대시보드에에서 만든 응용 프로그램에는 **샘플 Contoso** 템플릿. 응용 프로그램에 대 한 기본 대시보드를 사용자 지정 하려면 선택 **편집** 맨 위에 있는 페이지의 오른쪽입니다.

!["Contoso 샘플" 템플릿을 기반으로 하는 응용 프로그램에 대 한 대시보드](media/howto-configure-homepage/image1.png)

선택 **편집**, 대시보드 라이브러리 패널이 열립니다. 타일 및 대시보드를 사용자 지정 하 여 대시보드 기본 형식 라이브러리에 포함 되어 있습니다.

![대시보드 라이브러리](media/howto-configure-homepage/image2.png)

예를 들어, 추가할 수 있습니다는 **장치 설정 및 속성** 를 다양 한 장치에 대 한 현재 설정 및 속성 값을 표시 합니다. 이렇게 하려면 먼저 **디바이스 템플릿**을 선택한 다음, **디바이스 인스턴스**를 선택합니다. 타일에 제목을 지정한 후, 표시할 **설정** 또는 **속성**을 선택합니다. 다음 스크린샷에서 설정과 타일에 추가 하기 위해 선택한 속성을 보여 줍니다. 선택 **수행** 대시보드에 변경 내용을 저장 합니다.

![설정 및 속성에 대한 세부 정보가 있는 "디바이스 세부 정보 구성" 양식](media/howto-configure-homepage/image3.png)

사용 하 여 새 타일이 표시 운영자 기본 응용 프로그램 대시보드를 볼 때 이제는 **온도 설정** 장치에 대 한 설정:

![타일에 대한 설정 및 속성이 표시된 “대시보드” 탭](media/howto-configure-homepage/image4.png)

기본 응용 프로그램 대시보드를 추가로 사용자 지정 하는 방법을 알아봅니다 라이브러리의 다른 타일 형식을 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 기본 응용 프로그램 대시보드를 구성 하는 방법을 배웠으므로 다음 작업을 수행할 수 있습니다.

> [!div class="nextstepaction"]
> [이미지 준비 및 업로드하는 방법 알아보기](howto-prepare-images.md)
