---
title: Xamarin.Forms을 사용하여 Mobile Apps 시작
description: 이 자습서에 따라 Mobile Apps를 사용하여 Xamarin.Forms 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
ms.assetid: 5e692220-cc89-4548-96c8-35259722acf5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 2e0a4b51e9140b9b3c0032980b7c6cd5a4d55638
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128943"
---
# <a name="create-a-xamarinforms-app-with-azure"></a>Azure를 사용하여 Xamarin.Forms 앱 만들기

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

이 자습서에서는 Azure App Service의 Mobile Apps 기능을 백 엔드로 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.Forms 모바일 앱에 추가하는 방법을 보여줍니다. 새 Mobile Apps 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 할 일 모음 Xamarin.Forms 앱을 만듭니다.

이 자습서를 완료해야 다른 모든 Xamarin.Forms용 Mobile Apps 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져올 수 있습니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* Visual Studio 2017 또는 Mac용 Visual Studio에서 Xamarin용 Visual Studio Tools 지침은 [Xamarin 설치 페이지][Install Xamarin]를 참조하세요.

* (선택 사항) iOS 앱을 빌드하려면 Xcode 9.0 이상이 있는 Mac이 필요합니다. Mac용 Visual Studio를 사용하여 iOS 앱을 개발하거나 Visual Studio 2017을 사용할 수 있습니다(네트워크에서 Mac을 사용할 수 있는 한).

## <a name="create-a-new-mobile-apps-back-end"></a>새 Mobile Apps 백 엔드 만들기

새 Mobile Apps 백 엔드를 만들려면 다음을 수행합니다.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 애플리케이션에서 사용할 수 있는 모바일 앱 백 엔드를 설정했습니다. 다음으로, 간단한 할 일 목록 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## <a name="configure-the-server-project"></a>서버 프로젝트 구성

Node.js 또는 .NET 백 엔드를 사용하도록 서버 프로젝트를 구성하려면 다음을 수행합니다.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinforms-solution"></a>Xamarin.Forms 솔루션 다운로드 및 실행

Xamarin용 Visual Studio Tools는 솔루션을 열어야 합니다. [Xamarin 설치 지침][Install Xamarin]을 참조하세요. 도구를 이미 설치한 경우 다음 단계에 따라 솔루션을 다운로드하고 엽니다.

### <a name="visual-studio"></a>Visual Studio

1. [Azure Portal]로 이동합니다.

2. Mobile Apps에 대한 설정 블레이드에서 **빠른 시작**(배포 아래) > **Xamarin.Forms**를 차례로 클릭합니다. 3단계 아래에서 **새 앱 만들기**가 선택되어 있지 않으면 클릭합니다.  그런 다음 **다운로드** 단추를 클릭합니다.

   이 작업을 통해 모바일 앱에 연결된 클라이언트 애플리케이션을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

3. 다운로드한 프로젝트를 추출한 다음, Visual Studio 2017에서 엽니다.

   ![Visual Studio에서 추출된 프로젝트][8]

4. 아래 지침을 따라 Android 또는 Windows 프로젝트를 실행합니다. 사용할 수 있는 네트워크로 연결된 Mac 컴퓨터가 있는 경우 iOS 프로젝트입니다.

### <a name="visual-studio-for-mac"></a>Mac용 Visual Studio

1. [Azure Portal]로 이동합니다.

2. Mobile Apps에 대한 설정 블레이드에서 **빠른 시작**(배포 아래) > **Xamarin.Forms**를 차례로 클릭합니다. 3단계 아래에서 **새 앱 만들기**가 선택되어 있지 않으면 클릭합니다.  그런 다음 **다운로드** 단추를 클릭합니다.

   이 작업을 통해 모바일 앱에 연결된 클라이언트 애플리케이션을 포함하는 프로젝트를 다운로드합니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

3. 다운로드한 프로젝트를 추출한 다음, Mac용 Visual Studio에서 엽니다.

   ![Mac용 Visual Studio에서 추출된 프로젝트][9]

4. 아래 지침을 따라 Android 또는 iOS 프로젝트를 실행합니다.



## <a name="optional-run-the-android-project"></a>(선택 사항) Android 프로젝트 실행

이 섹션에서는 Xamarin.Android 프로젝트를 실행합니다. Android 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

1. Android(Droid) 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 Android 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택하고, 공유 코드 프로젝트에 **빌드** 확인란을 선택했는지 확인합니다.

4. 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누르거나 **시작** 단추를 클릭합니다.

### <a name="visual-studio-for-mac"></a>Mac용 Visual Studio

1. Android 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. 프로젝트를 빌드하고 Android 에뮬레이터에서 앱을 시작하려면 **실행** 메뉴를 선택한 다음, **디버깅 시작**을 선택합니다.



앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호(**+**)를 선택합니다.

![Android 할 일 앱][11]

이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

> [!NOTE]
> Mobile Apps 백 엔드에 액세스하는 코드는 솔루션의 공유 코드 프로젝트의 **TodoItemManager.cs** C# 파일에 있습니다.
>

## <a name="optional-run-the-ios-project"></a>(선택 사항) iOS 프로젝트 실행

이 섹션에서는 iOS 디바이스용 Xamarin.iOS 프로젝트를 실행합니다. iOS 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 iOS 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택하고, 공유 코드 프로젝트에 **빌드** 확인란을 선택했는지 확인합니다.

4. 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누릅니다.

### <a name="visual-studio-for-mac"></a>Mac용 Visual Studio

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 선택합니다.

2. **실행** 메뉴에서 **디버깅 시작**을 선택하여 프로젝트를 빌드하고 iPhone 에뮬레이터에서 앱을 시작합니다.



앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호(**+**)를 선택합니다.

![iOS 할 일 앱][10]

이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

> [!NOTE]
> 솔루션의 공유 코드 프로젝트의 **TodoItemManager.cs** C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
>

## <a name="optional-run-the-windows-project"></a>(선택 사항) Windows 프로젝트 실행

이 섹션에서는 Windows 디바이스에 대한 Xamarin.Forms UWP(유니버설 Windows 플랫폼) 프로젝트를 실행합니다. Windows 디바이스를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

### <a name="visual-studio"></a>Visual Studio

1. UWP 프로젝트를 마우스 오른쪽 단추로 누른 다음, **시작 프로젝트로 설정**을 선택합니다.

2. **빌드** 메뉴에서 **구성 관리자**를 선택합니다.

3. **구성 관리자** 대화 상자에서 선택한 Windows 프로젝트 옆에 있는 **빌드** 및 **배포** 확인란을 선택하고, 공유 코드 프로젝트에 **빌드** 확인란을 선택했는지 확인합니다.

4. 프로젝트를 빌드하고 Windows 에뮬레이터에서 앱을 시작하려면 **F5** 키를 누르거나 **시작** 단추를 클릭합니다(**로컬 컴퓨터**를 읽어야 함).

> [!NOTE]
> Windows 프로젝트를 macOS에서 실행할 수 없습니다.



앱에서 *Learn Xamarin*과 같은 의미 있는 텍스트를 입력한 후 더하기 기호(**+**)를 선택합니다.

이 작업을 통해 Azure에서 호스트된 새 Mobile Apps 백 엔드에 게시 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. 테이블에 저장된 항목이 Mobile Apps 백 엔드에서 반환되고 데이터가 목록에 표시됩니다.

![UWP 할 일 앱][12]

> [!NOTE]
> 솔루션의 이식 가능한 클래스 라이브러리 프로젝트의 **TodoItemManager.cs** C# 파일에서 Mobile Apps 백 엔드에 액세스하는 코드를 찾을 수 있습니다.
>

## <a name="troubleshooting"></a>문제 해결

솔루션을 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 최신 버전의 `Xamarin.Forms`로 업데이트하고, Android 프로젝트에서 `Xamarin.Android` 지원 패키지를 업데이트합니다. 빠른 시작 프로젝트는 항상 최신 버전을 포함하지 않을 수 있습니다.

Android 프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

## <a name="next-steps"></a>다음 단계

* [앱에 인증 추가](app-service-mobile-xamarin-forms-get-started-users.md) ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가](app-service-mobile-xamarin-forms-get-started-push.md) 앱에 푸시 알림 지원을 추가하고 Mobile Apps 백 엔드를 구성하여 푸시 알림을 보내는 Azure Notification Hubs를 사용하는 방법을 알아봅니다.

* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md) Mobile Apps 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 네트워크에 연결되어 있지 않은 경우에도 모바일 앱의 데이터를 보거나, 추가하거나, 수정할 수 있습니다.

* [Mobile Apps에 관리되는 클라이언트 사용](app-service-mobile-dotnet-how-to-use-client-library.md) Xamarin 앱에서 관리되는 클라이언트 SDK로 작업하는 방법을 알아봅니다.

* [Xamarin.Forms를 사용하여 다른 Azure 서비스 사용](https://docs.microsoft.com/xamarin/xamarin-forms/data-cloud/) 검색, 저장소 및 인식 서비스와 같은 추가 Azure 기능을 Xamarin.Forms 앱에 추가합니다.

<!-- Anchors. -->
[Get started with Mobile Apps back ends]:#getting-started
[Create a new Mobile Apps back end]:#create-new-service
[Next steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png

<!-- URLs. -->
[Install Xamarin]: https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/
[Mobile app SDK]: https://go.microsoft.com/fwlink/?LinkId=257545
[Azure Portal]: https://portal.azure.com/
