---
title: Xamarin.ios 앱 시작
description: 이 자습서에 따라 Mobile Apps를 사용하여 Xamarin.iOS 개발을 시작할 수 있습니다.
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: 2bb13393a9f348d85ab9fc964b784ba4d4d6a783
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668625"
---
# <a name="create-a-xamarinios-app"></a>Xamarin.iOS 앱 만들기
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발의 중심인 엔드투엔드 통합 서비스를 지원합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용하여 지속적인 통합 및 업데이트 파이프라인을 설정할 수 있습니다. 앱이 배포되면 개발자는 **분석** 및 **진단** 서비스를 사용하여 앱의 상태와 사용 현황을 모니터링하고, **푸시** 서비스를 사용하여 사용자와 소통할 수 있습니다. 또한 개발자는 **인증** 서비스를 사용하여 사용자를 인증하고, **데이터** 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 유지하고 동기화할 수도 있습니다.
>
> 모바일 애플리케이션에서 클라우드 서비스를 통합하려면 지금 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc)에 등록하세요.

## <a name="overview"></a>개요
이 자습서에서는 Azure 모바일 앱 백 엔드를 사용하여 클라우드 기반 백 엔드 서비스를 Xamarin.iOS 모바일 앱에 추가하는 방법을 보여 줍니다.  새 모바일 앱 백 엔드와 앱 데이터를 Azure에 저장하는 간단한 *할 일 모음* Xamarin.iOS 앱을 만듭니다.

이 자습서를 완료해야 Azure App Service에서 Mobile Apps 기능을 사용하는 방법에 대한 다른 모든 Xamarin.iOS 자습서를 진행할 수 있습니다.

## <a name="prerequisites"></a>전제 조건
이 자습서를 완료하려면 다음 필수 구성 요소가 필요합니다.

* 활성 Azure 계정. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 앱을 가져옵니다. 이러한 앱은 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Mac용 Visual Studio. [Mac용 Visual Studio 설치 및 설치를](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019) 참조 하세요.
* Xcode 9.0 이상 버전의 Mac.
  
## <a name="create-an-azure-mobile-app-backend"></a>새 Azure Mobile App 백 엔드 만들기
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>데이터베이스 연결을 만들고 클라이언트 및 서버 프로젝트를 구성 합니다.
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Xamarin.ios 앱 실행
1. Xamarin.ios 프로젝트를 엽니다.

2. [Azure Portal](https://portal.azure.com/) 로 이동 하 여 만든 모바일 앱으로 이동 합니다. `Overview` 블레이드에서 모바일 앱에 대한 공용 엔드포인트 인 URL을 찾습니다. 예-내 앱 이름 "test123"에 대한 sitename이 https://test123.azurewebsites.net 됩니다.

3. 이 폴더의 파일 `QSTodoService.cs`-xamarin.ios/ZUMOAPPNAME을 엽니다. 응용 프로그램 이름이 `ZUMOAPPNAME`입니다.

4. `QSTodoService` 클래스에서 `ZUMOAPPURL` 변수를 위의 공용 엔드포인트으로 바꿉니다.

    `const string applicationURL = @"ZUMOAPPURL";`

    않게
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. F5 키를 눌러 iPhone 에뮬레이터에서 앱을 배포 하 고 실행 합니다.

6. 앱에서 *자습서 완료* 와 같은 의미 있는 텍스트를 입력 한 다음 + 단추를 클릭 합니다.

    ![][10]

    요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 앱 백 엔드에서 반환된 후 데이터가 목록에 나타납니다.

   > [!NOTE]
   > 모바일 앱 백 엔드에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 ToDoActivity.cs C# 파일에 있습니다.
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png