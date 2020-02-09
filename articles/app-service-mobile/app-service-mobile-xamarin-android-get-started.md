---
title: Xamarin Android 앱 시작
description: 이 자습서에 따라 Azure Mobile Apps를 사용 하 여 Xamarin Android 개발을 시작할 수 있습니다.
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 1bac9ac03971765f1afc4f15ff3de6cc4b7d3883
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668682"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발의 중심인 엔드투엔드 통합 서비스를 지원합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용하여 지속적인 통합 및 업데이트 파이프라인을 설정할 수 있습니다. 앱이 배포되면 개발자는 **분석** 및 **진단** 서비스를 사용하여 앱의 상태와 사용 현황을 모니터링하고, **푸시** 서비스를 사용하여 사용자와 소통할 수 있습니다. 또한 개발자는 **인증** 서비스를 사용하여 사용자를 인증하고, **데이터** 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 유지하고 동기화할 수도 있습니다.
>
> 모바일 애플리케이션에서 클라우드 서비스를 통합하려면 지금 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc)에 등록하세요.

## <a name="overview"></a>개요
이 자습서에서는 Xamarin Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [Mobile Apps 정의](app-service-mobile-value-prop.md)를 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료해야 다른 모든 Xamarin Android 앱용 Mobile Apps 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>전제 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 활성 Azure 계정. 계정이 아직 없으면 Azure 평가판에 등록하고 최대 10개의 무료 Mobile Apps을 가져옵니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Xamarin이 포함된 Visual Studio입니다. 지침은 [Visual Studio 및 Xamarin을 위한 설치 및 설정](/visualstudio/cross-platform/setup-and-install) 을 참조하세요.

## <a name="create-an-azure-mobile-app-backend"></a>새 Azure Mobile App 백 엔드 만들기
다음 단계에 따라 새 Mobile App 백 엔드를 만드세요.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 클라이언트 애플리케이션에서 사용할 수 있는 Azure 모바일 앱 백 엔드를 프로비저닝했습니다. 다음으로, 간단한 "할 일 목록" 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트 및 서버 프로젝트를 구성 합니다.
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinandroid-app"></a>Xamarin Android 앱 실행
1. Xamarin Android 프로젝트를 엽니다.

2. [Azure Portal](https://portal.azure.com/) 로 이동 하 여 만든 모바일 앱으로 이동 합니다. `Overview` 블레이드에서 모바일 앱에 대한 공용 엔드포인트 인 URL을 찾습니다. 예-내 앱 이름 "test123"에 대한 sitename이 https://test123.azurewebsites.net 됩니다.

3. 이 폴더에서 파일 `ToDoActivity.cs`를 엽니다.-xamarin.ios/ZUMOAPPNAME/Todoactivity.java. 응용 프로그램 이름이 `ZUMOAPPNAME`입니다.

4. `ToDoActivity` 클래스에서 `ZUMOAPPURL` 변수를 위의 공용 엔드포인트으로 바꿉니다.

    `const string applicationURL = @"ZUMOAPPURL";`

    않게
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. F5 키를 눌러 응용 프로그램을 배포 하 고 실행 합니다.

6. 앱에서 *자습서 완료* 등의 의미 있는 텍스트를 입력한 후 **추가** 단추를 클릭합니다.

    ![][10]

    요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 나타납니다.

   > [!NOTE]
   > 모바일 앱 백 엔드에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.
   
## <a name="troubleshooting"></a>문제 해결
솔루션을 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 `Xamarin.Android` 지원 패키지를 업데이트합니다. 빠른 시작 프로젝트는 항상 최신 버전을 포함하지 않을 수 있습니다.

프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png
<!-- URLs. -->
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
