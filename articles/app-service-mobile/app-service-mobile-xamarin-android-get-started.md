---
title: Xamarin Android 앱용 Azure Mobile Apps 시작
description: 이 자습서에 따라 Azure Mobile Apps를 사용하여 Xamarin Android 개발을 시작할 수 있습니다.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 81649dd3-544f-40ff-b9b7-60c66d683e60
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 29efa963a254913e3d4744ade1d161c5c8ce42e4
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127899"
---
# <a name="create-a-xamarinandroid-app"></a>Xamarin.Android 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>개요
이 자습서에서는 Xamarin Android 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 자세한 내용은 [Mobile Apps 정의](app-service-mobile-value-prop.md)를 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료해야 다른 모든 Xamarin Android 앱용 Mobile Apps 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 활성 Azure 계정. 계정이 아직 없으면 Azure 평가판에 등록하고 최대 10개의 무료 Mobile Apps을 가져옵니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Xamarin이 포함된 Visual Studio입니다. 지침은 [Visual Studio 및 Xamarin을 위한 설치 및 설정](/visualstudio/cross-platform/setup-and-install) 을 참조하세요.

## <a name="create-an-azure-mobile-app-backend"></a>새 Azure Mobile App 백 엔드 만들기
다음 단계에 따라 새 Mobile App 백 엔드를 만드세요.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

이제 모바일 클라이언트 애플리케이션에서 사용할 수 있는 Azure 모바일 앱 백 엔드를 프로비저닝했습니다. 다음으로, 간단한 "할 일 목록" 백 엔드에 대한 서버 프로젝트를 다운로드하고 Azure에 게시합니다.

## <a name="configure-the-server-project"></a>서버 프로젝트 구성
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Xamarin.Android 앱 다운로드 및 실행
1. **Xamarin.Android 프로젝트 다운로드 및 실행**에서 **다운로드** 단추를 클릭합니다.

      압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.
2. **F5** 키를 눌러 프로젝트를 빌드하고 앱을 시작합니다.
3. 앱에서 *자습서 완료* 등의 의미 있는 텍스트를 입력한 후 **추가** 단추를 클릭합니다.

    ![][10]

    요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 나타납니다.

   > [!NOTE]
   > 모바일 앱 백 엔드에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.
   >
   >

## <a name="troubleshooting"></a>문제 해결
솔루션을 빌드하는 데 문제가 발생한 경우 NuGet 패키지 관리자를 실행하고 `Xamarin.Android` 지원 패키지를 업데이트합니다. 빠른 시작 프로젝트는 항상 최신 버전을 포함하지 않을 수 있습니다.

프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

## <a name="next-steps"></a>다음 단계
* [앱에 오프라인 동기화 추가](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [앱에 인증 추가](app-service-mobile-xamarin-android-get-started-users.md)
* [Xamarin.Android 앱에 푸시 알림 추가](app-service-mobile-xamarin-android-get-started-push.md)
* [Azure Mobile Apps에 관리되는 클라이언트를 사용하는 방법](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
