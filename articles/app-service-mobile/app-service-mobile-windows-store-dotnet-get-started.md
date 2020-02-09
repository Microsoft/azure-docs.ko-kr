---
title: UWP 앱 만들기
description: 이 자습서에 따라 Azure 모바일 앱 백 엔드를 사용하여 C#, Visual Basic 또는 JavaScript로 UWP(범용 Windows 플랫폼) 앱 개발을 시작할 수 있습니다.
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: a0f78239406567513c1eb94b48bf1090165d9185
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668694"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Azure 백 엔드를 사용하여 Windows 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발의 중심인 엔드투엔드 통합 서비스를 지원합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용하여 지속적인 통합 및 업데이트 파이프라인을 설정할 수 있습니다. 앱이 배포되면 개발자는 **분석** 및 **진단** 서비스를 사용하여 앱의 상태와 사용 현황을 모니터링하고, **푸시** 서비스를 사용하여 사용자와 소통할 수 있습니다. 또한 개발자는 **인증** 서비스를 사용하여 사용자를 인증하고, **데이터** 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 유지하고 동기화할 수도 있습니다.
>
> 모바일 애플리케이션에서 클라우드 서비스를 통합하려면 지금 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc)에 등록하세요.

## <a name="overview"></a>개요

이 자습서에서는 UWP(범용 Windows 플랫폼) 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [Mobile Apps 정의](app-service-mobile-value-prop.md)를 참조하세요. 다음은 완성된 앱의 화면 캡처입니다.

![완료된 데스크톱 앱](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

먼저 이 자습서를 완료해야만 UWP 앱용 다른 모든 모바일 앱 자습서를 학습할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Windows 10
* Visual Studio Community 2017.
* UWP 앱 개발 경험. [UWP 자습서](https://docs.microsoft.com/windows/uwp/)를 방문하여 UWP 앱을 빌드하기 위한 [설정](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) 방법을 알아보세요.

## <a name="create-a-new-azure-mobile-app-backend"></a>새 Azure 모바일 앱 백 엔드 만들기

다음 단계에 따라 새 모바일 앱 백 엔드를 만드세요.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트 및 서버 프로젝트를 구성 합니다.
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>클라이언트 프로젝트 실행

1. UWP 프로젝트를 엽니다.

2. [Azure Portal](https://portal.azure.com/) 로 이동 하 여 만든 모바일 앱으로 이동 합니다. `Overview` 블레이드에서 모바일 앱에 대한 공용 엔드포인트 인 URL을 찾습니다. 예-내 앱 이름 "test123"에 대한 sitename이 https://test123.azurewebsites.net 됩니다.

3. 이 폴더에서 파일 `App.xaml.cs`를 엽니다.-windows-uwp-cs/ZUMOAPPNAME/. 응용 프로그램 이름이 `ZUMOAPPNAME`입니다.

4. `App` 클래스에서 `ZUMOAPPURL` 매개 변수를 위의 공용 엔드포인트으로 바꿉니다.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    않게
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. F5 키를 눌러 응용 프로그램을 배포 하 고 실행 합니다.

6. 앱에서 **할 일 항목 삽입** 텍스트 상자에 *자습서 완료*와 같은 의미 있는 텍스트를 입력하고 **저장**을 클릭합니다.

    ![Windows 빠른 시작 완료 데스크톱](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Azure에 호스트된 새 모바일 앱 백 엔드에 POST 요청이 전송됩니다.
