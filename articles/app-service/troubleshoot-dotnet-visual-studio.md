---
title: Visual Studio를 사용하여 문제 해결
description: Visual Studio 2013에서 기본 제공되는 원격 디버깅, 추적 및 로깅 도구를 사용하여 App Service 앱 문제를 해결하는 방법에 대해 알아봅니다.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: de39789a45856211421e3ec5638a2df94d49976c
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958714"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Visual Studio를 사용하여 Azure App Service에서 앱 문제 해결
## <a name="overview"></a>개요
이 자습서에서는 원격으로 [디버그 모드](/visualstudio/debugger/)를 실행하거나 애플리케이션 로그 및 웹 서버 로그를 확인하여 [App Service](https://go.microsoft.com/fwlink/?LinkId=529714)에서 앱을 디버그할 수 있는 Visual Studio 도구를 사용하는 방법을 보여줍니다.

다음 내용을 배웁니다.

* Visual Studio에서 사용할 수 있는 앱 관리 기능
* Visual Studio 원격 뷰를 사용하여 원격 앱에서 빠르게 변경하는 방법
* 앱 및 WebJob에 대해 Azure에서 프로젝트를 실행하는 동안 원격으로 디버그 모드를 실행하는 방법
* 애플리케이션 추적 로그를 만드는 방법 및 해당 로그가 생성될 때 이를 보는 방법
* 자세한 오류 메시지 및 실패한 요청 추적을 포함하여 웹 서버 로그를 보는 방법
* 진단 로그를 Azure Storage 계정으로 보내고 해당 위치에서 로그를 보는 방법

Visual Studio Ultimate가 있으면 디버깅에 [IntelliTrace](/visualstudio/debugger/intellitrace) 를 사용할 수도 있습니다. IntelliTrace는 이 자습서에서 다루지 않습니다.

## <a name="prerequisites"></a><a name="prerequisites"></a>필수 조건
이 자습서에서는 [Azure App Service에서 ASP.NET 앱 만들기](quickstart-dotnet-framework.md)에서 설정한 개발 환경, 웹 프로젝트 및 App Service 앱을 사용합니다. WebJobs 섹션의 경우 [Azure WebJobs SDK 시작][GetStartedWJ]에서 만든 애플리케이션이 필요합니다.

이 사용설명서에 제시된 코드 샘플은 C# MVC 웹 애플리케이션용이지만 문제 해결 절차는 Visual Basic 및 Web Forms 애플리케이션에도 동일하게 적용됩니다.

이 자습서에서는 Visual Studio 2019을 사용 하 고 있다고 가정 합니다. 

로그 스트리밍 기능은 .NET Framework 4 이상을 대상으로 하는 애플리케이션에 대해서만 작동합니다.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>앱 구성 및 관리
Visual Studio를 사용하면 [Azure Portal](https://go.microsoft.com/fwlink/?LinkId=529715)에서 사용할 수 있는 앱 관리 기능 및 구성 설정의 일부에 액세스할 수 있습니다. 이 섹션에서는 **서버 탐색기**를 사용 하 여 사용할 수 있는 기능을 확인 합니다. 최신 Azure 통합 기능을 확인하려면 **클라우드 탐색기** 도 사용해 보십시오. **보기** 메뉴에서 두 창을 모두 열 수 있습니다.

1. Visual Studio에서 Azure에 아직 로그인하지 않은 경우 **Azure**를 마우스 오른쪽 단추로 클릭하고 **서버 탐색기**에서 **Microsoft Azure 구독에 연결**을 선택합니다.

    계정에 액세스할 수 있게 해 주는 또 다른 방법은 관리 인증서를 설치하는 것입니다. 인증서를 설치하도록 선택한 경우 **서버 탐색기**에서 **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음 상황에 맞는 메뉴에서 **구독 관리 및 필터링**을 선택합니다. **Microsoft Azure 구독 관리** 대화 상자에서 **인증서** 탭을 클릭한 후 **가져오기**를 클릭합니다. 지침에 따라 Azure 계정에 대한 구독 파일( *.publishsettings* 파일이라고도 함)을 다운로드하고 가져옵니다.

   > [!NOTE]
   > 구독 파일을 다운로드한 경우 소스 코드 디렉터리의 외부 폴더(예: Downloads 폴더)에 구독 파일을 저장한 다음 가져오기가 완료되면 해당 파일을 삭제합니다. 악의적인 사용자가 구독 파일에 액세스할 경우 Azure 서비스를 편집, 생성 및 삭제할 수 있습니다.
   >
   >

    Visual Studio에서 Azure 리소스에 연결하는 방법에 대한 자세한 내용은 [계정, 구독 및 관리 역할 관리](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)를 참조하세요.
2. **서버 탐색기**에서 **Azure**를 확장한 후 **App Service**를 확장합니다.
3. [Azure App Service에서 ASP.NET 앱 만들기](quickstart-dotnet-framework.md)에서 만든 앱을 포함하는 리소스 그룹을 확장한 후 앱 노드를 마우스 오른쪽 단추로 클릭하고 **설정 보기**를 클릭합니다.

    ![서버 탐색기에서 설정 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **Azure 웹앱** 탭이 나타나면 이 탭에서 Visual Studio에서 사용할 수 있는 앱 관리 및 구성 작업이 무엇인지 확인할 수 있습니다.

    ![Azure 웹 앱 창](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    이 사용설명서에서는 로깅 및 추적 드롭다운을 사용합니다. 원격 디버깅도 사용하지만 다른 방법을 사용하여 이를 사용하도록 설정합니다.

    이 창의 앱 설정 및 연결 문자열 상자에 대 한 자세한 내용은 [Azure App Service: 응용 프로그램 문자열 및 연결 문자열 작동 방식](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)을 참조 하세요.

    이 창에서 지원하지 않는 앱 관리 작업을 수행하려는 경우 **관리 포털에서 열기**를 클릭하여 브라우저 창에서 Azure Portal을 엽니다.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>서버 탐색기에서 앱 파일 액세스
일반적으로 Web.config 파일에서 `customErrors` 플래그를 `On` 또는 `RemoteOnly`로 설정한 상태로 웹 프로젝트가 배포되기 때문에 문제가 발생한 경우 유용한 오류 메시지를 받지 못합니다. 받을 수 있는 대부분의 오류는 다음 중 하나와 유사합니다.

**'/' 응용 프로그램에 서버 오류가 있습니다.**

![도움이 되지 않는 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**오류가 발생 했습니다.**

![도움이 되지 않는 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**웹 사이트에서 페이지를 표시할 수 없습니다.**

![도움이 되지 않는 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

오류의 원인을 찾는 가장 쉬운 방법은 주로 자세한 오류 메시지를 사용하도록 설정하는 것입니다. 이전 스크린샷 중 첫 번째 스크린샷에 그 방법이 설명되어 있습니다. 배포된 Web.config 파일의 변경이 필요합니다. 프로젝트에서 *Web.config* 파일을 편집한 후 프로젝트를 다시 배포하거나 [Web.config 변환](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations)을 만들고 디버그 빌드를 배포할 수도 있지만 더 빠른 방법이 있습니다. **솔루션 탐색기**에서 *원격 보기* 기능을 사용하면 원격 앱의 파일을 직접 보고 편집할 수 있습니다.

1. **서버 탐색기**에서 **Azure**, **App Service**, 앱이 있는 리소스 그룹, 앱의 노드를 차례로 확장합니다.

    표시되는 노드를 통해 앱의 콘텐츠 파일 및 로그 파일에 액세스할 수 있습니다.
2. **파일** 노드를 확장하고 *Web.config* 파일을 두 번 클릭합니다.

    ![Web.config 열기](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    원격 앱의 Visual Studio에서 Web.config 파일이 열리고 제목 표시줄의 파일 이름 옆에 [원격]이 표시됩니다.
3. `system.web` 요소에 다음 줄을 추가합니다.

    `<customErrors mode="Off"></customErrors>`

    ![Web.config 편집](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. 도움이 되지 않는 오류 메시지가 표시된 브라우저를 새로 고칩니다. 이제 다음 예와 같이 자세한 오류 메시지가 표시됩니다.

    ![자세한 오류 메시지](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (표시된 오류는 빨간색으로 표시된 줄이 *Views\Home\Index.cshtml*에 추가되어 생성됨)

Web.config 파일을 편집하는 방법은 문제를 더 쉽게 해결할 수 있도록 App Service 앱의 파일 읽기/편집 기능을 사용하는 한 가지 예에 지나지 않습니다.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>앱 원격 디버깅
자세한 오류 메시지에 충분한 정보가 제공되지 않은 경우 로컬로 오류를 다시 만들 수는 없습니다. 이때 문제를 해결하는 또 다른 방법은 원격으로 디버그 모드에서 실행하는 것입니다. 중단점을 설정하고 메모리를 직접 조작하며 코드를 단계별로 실행하고 심지어 코드 경로를 변경할 수 있습니다.

원격 디버깅은 Visual Studio의 Express Edition에서 작동하지 않습니다.

이 섹션에서는 [Azure App Service에서 ASP.NET 앱 만들기](quickstart-dotnet-framework.md)에서 만든 프로젝트를 사용하여 원격으로 디버그하는 방법을 보여 줍니다.

1. [Azure App Service에서 ASP.NET 앱 만들기](quickstart-dotnet-framework.md)에서 만든 웹 프로젝트를 엽니다.

1. *Controllers\HomeController.cs*를 엽니다.

1. `About()` 메서드를 삭제하고 그 자리에 다음 코드를 삽입합니다.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. 줄에 [중단점을 설정](https://docs.microsoft.com/visualstudio/debugger/) `ViewBag.Message` 합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭 하 고 **게시**를 클릭 합니다.

1. **프로필** 드롭다운 목록에서 [Azure App Service에서 ASP.NET 앱 만들기](quickstart-dotnet-framework.md)에서 사용한 것과 동일한 프로필을 선택합니다. 그런 후 설정을 클릭합니다.

1. **게시** 대화 상자에서 **설정** 탭을 클릭한 다음, **구성**을 **디버그**로 변경하고 **저장**을 클릭합니다.

    ![디버그 모드에서 게시](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. **게시**를 클릭합니다. 배포를 마치고 브라우저에 앱의 Azure URL이 열리면 브라우저를 닫습니다.

1. **서버 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭한 다음, **디버거 연결**을 클릭합니다.

    ![디버거 연결](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    브라우저에 Azure에서 실행되는 홈 페이지가 자동으로 열립니다. Azure에서 디버깅용 서버를 설정할 때까지 약 20초 정도 기다려야 할 수 있습니다. 이 지연은 48시간 내 앱에서 디버그 모드를 처음 실행하는 경우에만 발생합니다. 동일한 시간에 디버깅을 다시 시작하면 지연이 발생하지 않습니다

    > [!NOTE] 
    > 디버거를 시작하는 데 문제가 있는 경우 **서버 탐색기** 대신 **클라우드 탐색기**를 사용해 보세요.
    >

1. 메뉴에서 **정보** 를 클릭합니다.

    Visual Studio가 중단점에서 중지되고 코드는 로컬 컴퓨터가 아닌 Azure에서 실행됩니다.

1. `currentTime` 변수를 마우스 포인터로 가리켜 시간 값을 봅니다.

    ![Azure에서 실행 중인 디버그 모드의 변수 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    보이는 시간은 Azure 서버 시간이며 이는 로컬 컴퓨터의 표준 시간대와 다를 수 있습니다.

1. `currentTime` 변수에 "현재 Azure에서 실행 중" 같은 새 값을 입력합니다.

1. F5 키를 눌러 계속 실행합니다.

     Azure에서 실행되는 정보 페이지에는 currentTime 변수에 입력한 새 값이 표시됩니다.

     ![새 값이 표시된 정보 페이지](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> 원격 디버깅 WebJob
이 섹션에서는 [Azure WebJob SDK 시작](https://github.com/Azure/azure-webjobs-sdk/wiki)에서 만든 프로젝트 및 앱을 사용하여 원격으로 디버그하는 방법을 보여 줍니다

이 섹션에 표시된 기능은 Visual Studio 2013 업데이트 4에서만 사용할 수 있습니다.

연속 WebJobs에서 원격 디버깅만 작동합니다. 예약 및 주문형 WebJobs은 디버깅을 지원하지 않습니다.

1. [Azure WebJobs SDK 시작][GetStartedWJ]에서 만든 웹 프로젝트를 엽니다.

2. ContosoAdsWebJob 프로젝트에서 *Functions.cs*를 엽니다.

3. `GnerateThumbnail` 메서드의 첫 번째 문에 [중단점을 설정](https://docs.microsoft.com/visualstudio/debugger/)합니다.

    ![중단점 설정](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. **솔루션 탐색기**에서 웹 프로젝트(WebJob 프로젝트가 아님)를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다.

5. **프로필** 드롭다운 목록에서 [Azure WebJobs SDK 시작](https://github.com/Azure/azure-webjobs-sdk/wiki)에서 사용한 것과 같은 프로필을 선택합니다.

6. **설정** 탭을 클릭하고 **구성**을 **디버그**로 변경한 후 **게시**를 클릭합니다.

    Visual Studio에서 웹 및 WebJob 프로젝트를 배포하며 브라우저에서 앱의 Azure URL이 열립니다.

7. **서버 탐색기**에서 **Azure > App Service > 리소스 그룹 > 앱 > WebJobs > 연속**을 확장하고 **ContosoAdsWebJob**을 마우스 오른쪽 단추로 클릭합니다.

8. **디버거 연결**을 클릭합니다.

    ![디버거 연결](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    브라우저에 Azure에서 실행되는 홈 페이지가 자동으로 열립니다. Azure에서 디버깅용 서버를 설정할 때까지 약 20초 정도 기다려야 할 수 있습니다. 이 지연은 48시간 내 앱에서 디버그 모드를 처음 실행하는 경우에만 발생합니다. 동일한 시간에 디버깅을 다시 시작하면 지연이 발생하지 않습니다

9. Contoso Ads 홈페이지로 열리는 웹 브라우저에서 새 광고를 만듭니다.

    광고를 만들면 큐 메시지가 생성되며, WebJob에서 메시지를 선택하여 처리합니다. WebJobs SDK가 큐 메시지를 처리할 함수를 호출하면 코드가 중단점에 도달합니다.

10. 디버거가 중단점에서 중단되면 프로그램이 클라우드에서 실행되는 동안 변수 값을 검사하고 변경할 수 있습니다. 다음 그림에서 디버거는 `GenerateThumbnail` 메서드로 전달된 blobInfo 개체의 내용을 보여 줍니다.

     ![디버거에서 blobInfo 개체](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. F5 키를 눌러 계속 실행합니다.

     `GenerateThumbnail` 메서드가 미리 보기 만들기를 완료합니다.

12. 브라우저에서 인덱스 페이지를 새로 고치면 미리 보기가 표시됩니다.

13. Visual Studio에서 디버깅을 중지하려면 SHIFT+F5를 누릅니다.

14. **서버 탐색기**에서 ContosoAdsWebJob 노드를 마우스 오른쪽 단추로 클릭하고 **대시보드 보기**를 클릭합니다.

15. Azure 자격 증명을 사용하여 로그인한 다음 WebJob 이름을 클릭하여 WebJob 페이지로 이동합니다.

     ![ContosoAdsWebJob 클릭](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     대시보드에서 `GenerateThumbnail` 함수 대시보드가 최근에 실행되었음을 확인할 수 있습니다.

     (다음에 **대시보드 보기**를 클릭하면 로그인할 필요가 없고 브라우저가 바로 WebJob에 대한 페이지로 이동합니다.)

16. 함수 이름을 클릭하여 함수 실행에 대한 세부 정보를 확인합니다.

     ![함수 정보](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

함수에서 [로그가 작성](https://github.com/Azure/azure-webjobs-sdk/wiki)되었으면 **ToggleOutput**을 클릭하여 확인할 수 있습니다.

## <a name="notes-about-remote-debugging"></a>원격 디버깅 관련 참고 사항

* 프로덕션 사이트에서 디버그 모드로 실행하는 것은 권장되지 않습니다. 프로덕션 애플리케이션이 여러 서버 인스턴스로 확장되지 않은 경우 디버깅으로 인해 웹 서버에서 다른 요청에 응답할 수 없습니다. 여러 웹 서버 인스턴스를 배포하여 이들을 디버거에 연결한 경우 디버깅은 임의 인스턴스에서 실행되므로 후속 브라우저 요청이 동일한 인스턴스로 이동하도록 보장할 수 있는 방법이 없습니다. 또한 일반적으로는 프로덕션 사이트에 디버그 빌드를 배포하지 않는데 이는 릴리스 빌드용 컴파일러 최적화로 인해 현재 진행되는 상황이 소스 코드에 자세히 표시되는 것이 불가능하기 때문입니다. 프로덕션 문제를 해결하는 데는 애플리케이션 추적 및 웹 서버 로그가 최적의 리소스입니다.
* 원격 디버깅 시 중단점에서 장시간 중지하지 않도록 합니다. 몇 분 이상 중지된 프로세스는 Azure에서 응답하지 않는 프로세스로 간주되어 종료되기 때문입니다.
* 디버그하는 동안 서버는 데이터를 Visual Studio로 보내며, 이로 인해 대역폭 사용 요금에 영향을 줄 수 있습니다. 대역폭 요금에 대한 자세한 내용은 [Azure 가격 책정](https://azure.microsoft.com/pricing/calculator/)을 참조하십시오.
* *Web.config* 파일에서 `compilation` 요소의 `debug` 특성이 true로 설정되어 있어야 합니다. 디버그 빌드 구성을 게시하면 기본적으로 true로 설정됩니다.

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* 디버거에서 디버그하려는 코드가 단계별로 실행되지 않는 경우 "내 코드만" 설정을 변경해야 할 수 있습니다.  자세한 내용은 [Visual Studio에서 Just My Code를 사용하여 사용자 코드만 디버그할지 지정](https://docs.microsoft.com/visualstudio/debugger/just-my-code)을 참조하세요.
* 원격 디버깅 기능을 사용하도록 설정하면 서버의 타이머가 시작되고 48시간 후 기능이 자동으로 꺼집니다. 이 48시간 제한은 보안 및 성능상의 이유로 제한됩니다. 원하는 횟수만큼 기능을 쉽게 다시 켤 수 있습니다. 디버깅을 활발히 사용하지 않는 경우 이를 사용하지 않는 상태로 두는 것이 좋습니다.
* 앱 프로세스(w3wp.exe)뿐만 아니라 모든 프로세스에 디버거를 수동으로 연결할 수 있습니다. Visual Studio에서 디버그 모드를 사용하는 방법에 대한 자세한 내용은 [Visual Studio의 디버깅](/visualstudio/debugger/debugging-in-visual-studio)을 참조하십시오.

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>진단 로그 개요
App Service 앱에서 실행하는 ASP.NET 애플리케이션은 다음과 같은 종류의 로그를 생성할 수 있습니다.

* **애플리케이션 추적 로그**<br/>
  애플리케이션은 [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) 클래스의 메서드를 호출하여 이러한 로그를 생성합니다.
* **웹 서버 로그**<br/>
  웹 서버는 앱에 대한 모든 HTTP 요청에 대해 로그 항목을 생성합니다.
* **자세한 오류 메시지 로그**<br/>
  웹 서버는 실패한 HTTP 요청(상태 코드 400 이상을 발생하는 요청)에 대해 일부 추가 정보가 수록된 HTML 페이지를 생성합니다.
* **실패한 요청 추적 로그**<br/>
   웹 서버는 실패한 HTTP 요청에 대한 자세한 추적 정보가 수록된 XML 파일을 생성합니다. 또한 웹 서버는 브라우저에서 XML 파일 형식으로 지정할 수 있도록 XSL 파일도 제공합니다.

로깅 기능을 사용하면 앱 성능에 영향을 미칠 수 있으므로 Azure에서는 필요에 따라 각 로그 유형을 사용 또는 사용하지 않도록 설정할 수 있는 기능이 제공됩니다. 애플리케이션 로그의 경우 특정 심각도 수준 이상의 로그만 작성되도록 지정할 수 있습니다. 새 앱을 만들 때 기본적으로 모든 로깅은 사용하지 않도록 설정됩니다.

로그는 앱의 파일 시스템에 있는 *LogFiles* 폴더의 파일에 기록되며 FTP를 통해 액세스할 수 있습니다. 웹 서버 로그 및 애플리케이션 로그는 또한 Azure Storage 계정에도 기록될 수 있습니다. 스토리지 계정에서는 파일 시스템에서 보존할 수 있는 로그보다 훨씬 많은 볼륨의 로그를 보존할 수 있습니다. 파일 시스템을 사용하는 경우 최대 100MB의 로그로 제한됩니다. 참고로, 파일 시스템 로그는 단기 보존용입니다. 제한값에 도달한 경우 Azure는 새 로그를 위한 공간을 만들기 위해 오래된 로그 파일을 삭제합니다.  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>애플리케이션 추적 로그 만들기 및 보기
이 섹션에서는 다음 작업을 수행합니다.

* [Azure 및 ASP.NET 시작](quickstart-dotnet-framework.md)에서 만든 웹 프로젝트에 추적 문을 추가합니다.
* 프로젝트를 로컬로 실행하는 경우 로그 보기
* Azure에서 실행하는 애플리케이션에서 생성된 로그 보기

WebJob에서 애플리케이션을 만드는 방법에 대한 자세한 내용은 [WebJobs SDK를 사용하여 Azure Queue Storage 작업을 하는 방법 - 로그를 쓰는 방법](https://github.com/Azure/azure-webjobs-sdk/wiki)을 참조하세요. 로그를 보고 Azure에 저장하는 방법을 제어하기 위한 다음 지침은 WebJob에서 만들어진 애플리케이션 로그에도 적용됩니다.

### <a name="add-tracing-statements-to-the-application"></a>애플리케이션에 추적 문 추가
1. *Controllers\ homecontroller.cs*를 열고 `Index` , `About` 및 `Contact` 메서드를 `Trace` `using` 에 대 한 문과 문을 추가 하기 위해 다음 코드로 바꿉니다 `System.Diagnostics` .

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. 파일 맨 위에 `using System.Diagnostics;` 문을 추가합니다.

### <a name="view-the-tracing-output-locally"></a>로컬에서 추적 출력 보기
1. F5 키를 눌러 디버그 모드에서 애플리케이션을 실행합니다.

    기본 추적 수신기는 모든 추적 출력을 다른 디버그 출력과 함께 **출력** 창에 기록합니다. 다음 그림에서는 `Index` 메서드에 추가한 trace 문의 출력을 보여줍니다.

    ![디버그 창의 추적](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    다음 단계는 디버그 모드에서 컴파일하지 않고 웹 페이지에서 추적 출력을 보는 방법을 보여 줍니다.
1. 프로젝트 폴더에 위치한 애플리케이션 Web.config 파일을 열고 파일 끝에 있는 닫는 `<system.diagnostics>` 요소 바로 앞에 `</configuration>` 요소를 추가합니다.

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

`WebPageTraceListener`를 사용하면 `/trace.axd`로 이동하여 추적 출력을 확인할 수 있습니다.
1. Web.config 파일의 `<system.web>` 아래에 <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trace 요소</a>를 다음 예와 같이 추가합니다.

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Ctrl+F5를 눌러 애플리케이션을 실행합니다.
1. 브라우저 창의 주소 표시줄에서 *trace.axd* 를 url에 추가한 후 enter 키를 누릅니다. url은와 유사 `http://localhost:53370/trace.axd` 합니다.
1. **애플리케이션 추적** 페이지에서 BrowserLink 줄이 아니라 첫 번째 줄의 **세부 정보 보기**를 클릭합니다.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **요청 세부 정보** 페이지가 나타나며, **추적 정보** 섹션에서 `Index` 메서드에 추가한 trace 문의 출력을 확인합니다.

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    기본적으로 `trace.axd` 는 로컬에서만 사용할 수 있습니다. 원격 앱에서 사용할 수 있도록 하려면, 다음 예제에 표시된 것처럼 *Web.config* 파일의 `trace` 요소에 `localOnly="false"`를 추가하면 됩니다.

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    그러나 프로덕션 앱에서 `trace.axd`를 사용하도록 설정하는 것은 보안상의 이유로 권장되지 않습니다. 다음 섹션에서는 App Service 앱에서 추적 로그를 읽는 가장 쉬운 방법을 확인할 수 있습니다.

### <a name="view-the-tracing-output-in-azure"></a>Azure에서 추적 출력 보기
1. **솔루션 탐색기**에서 웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
2. **웹 게시** 대화 상자에서 **게시**를 클릭합니다.

    Visual Studio에서 업데이트가 게시된 후 **연결** 탭의 **대상 URL**의 확인란을 선택 취소하지 않은 경우 브라우저 창이 열리고 사용자의 홈 페이지가 표시됩니다.
3. **서버 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 **스트리밍 로그 보기**를 선택합니다.

    ![상황에 맞는 메뉴의 스트리밍 로그 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    **출력** 창에는 현재 로그 스트리밍 서비스에 연결되어 있음이 표시되고, 로그는 표시되지 않은 상태로 매분마다 알림 줄이 추가됩니다.

    ![상황에 맞는 메뉴의 스트리밍 로그 보기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. 애플리케이션 홈 페이지가 표시된 브라우저 창에서 **연락처**를 클릭합니다.

    몇 초 이내에 `Contact` 메서드에 추가한 오류 수준의 추적에 대한 출력이 **출력** 창에 나타납니다.

    ![출력 창의 오류 추적](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    로그 모니터링 서비스를 사용하도록 설정하는 경우 오류 수준의 추적이 기본 설정이므로 Visual Studio에는 오류 수준의 추적만 표시됩니다. 새 App Service 앱을 만드는 경우 사이트 설정에서 확인한 것처럼 모든 로깅은 기본적으로 사용하지 않도록 설정됩니다.

    ![애플리케이션 로깅 끄기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    하지만 **스트리밍 로그 보기**를 선택한 경우 Visual Studio는 자동으로 **애플리케이션 로깅(파일 시스템)** 을 **오류**로 변경합니다. 이는 오류 수준의 로그가 보고된다는 의미입니다. 추적 로그를 모두 보려면 이 설정을 **자세한 정보 표시**로 변경할 수 있습니다. 심각도 수준을 오류보다 낮은 수준으로 선택하면 그보다 더 높은 심각도 수준의 모든 로그가 보고됩니다. 따라서 자세한 정보 표시를 선택하는 경우 정보, 경고 및 오류 로그도 볼 수 있습니다.  

5. **서버 탐색기**에서 앱을 마우스 오른쪽 단추로 클릭하고 이전과 마찬가지로 **설정 보기**를 클릭합니다.
6. **애플리케이션 로깅(파일 시스템)** 을 **자세한 정보 표시**로 변경한 다음, **저장**을 클릭합니다.

    ![추적 수준을 자세한 정보 표시로 설정](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. 이제 **연락처** 페이지가 표시되는 브라우저 창에서 **홈**을 클릭하고 **정보**를 클릭한 후 **연락처**를 클릭합니다.

    몇 초 이내에 **출력** 창에 추적 출력이 모두 표시됩니다.

    ![자세한 정보 표시 추적 출력](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    이 섹션에서는 앱 설정을 사용하여 로깅을 사용 및 사용하지 않도록 설정했습니다. 또한 Web.config 파일을 수정하여 추적 수신기를 사용 또는 사용하지 않도록 설정할 수도 있습니다. 하지만 Web.config 파일을 수정하면 앱 도메인이 재순환할 수 있습니다. 반면에 앱 구성을 통해 로깅을 사용하도록 설정하면 재순환하지 않습니다. 문제를 재현하는 데 오랜 시간이 걸리는 경우 또는 문제가 일시적인 경우 앱 도메인을 재순환하면 문제를 "해결" 할 수도 있지만 문제가 다시 발생할 때까지 강제로 대기해야 합니다. Azure에서 진단을 사용하도록 설정하면 앱 도메인을 재사용하지 않고 즉시 오류 정보를 캡처하기 시작할 수 있습니다.

### <a name="output-window-features"></a>출력 창 기능
**출력** 창의 **Microsoft Azure 로그** 탭에는 여러 단추와 하나의 입력란이 있습니다.

![로그 탭의 단추](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

이들은 다음 기능을 수행합니다.

* **출력** 창의 내용을 지웁니다.
* 자동 줄 바꿈을 사용 또는 사용하지 않도록 설정합니다.
* 로그 모니터링을 시작 또는 중지합니다.
* 모니터링할 로그를 지정합니다.
* 로그를 다운로드합니다.
* 검색 문자열 또는 정규식을 기반으로 로그를 필터링합니다.
* **출력** 창을 닫습니다.

검색 문자열 또는 정규식을 입력하면 Visual Studio에서 클라이언트 측의 로깅 정보가 필터링됩니다. 즉, **출력** 창에 로그가 표시된 후에 조건을 입력할 수 있으며 로그를 다시 생성할 필요 없이 필터링 조건을 변경할 수 있습니다.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>웹 서버 로그 보기
웹 서버 로그는 앱의 모든 HTTP 작업을 기록합니다. **출력** 창에서 웹 서버 로그를 보려면 앱에서 이를 사용하도록 설정하고, Visual Studio에서 이들의 모니터링을 명시적으로 지정해야 합니다.

1. **서버 탐색기**에서 열었던 **Azure Web App 구성** 탭에서 웹 서버 로깅을 **켜기**로 변경하고 **저장**을 클릭합니다.

    ![웹 서버 로깅 사용](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. **출력** 창에서 **모니터링할 Microsoft Azure 로그 지정** 버튼을 클릭합니다.

    ![모니터링할 Azure 로그를 지정합니다.](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. **Microsoft Azure 로깅 옵션** 대화 상자에서 **웹 서버 로그**를 선택한 후 **확인**을 클릭합니다.

    ![웹 서버 로그 모니터링](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. 앱이 표시되는 브라우저 창에서 **홈**을 클릭하고 **정보**를 클릭한 후 **연락처**를 클릭합니다.

    일반적으로 애플리케이션 로그가 먼저 나타나고 뒤에 웹 서버 로그가 나타납니다. 로그가 나타날 때까지 잠시 기다려야 할 수 있습니다.

    ![출력 창의 웹 서버 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

기본적으로 Visual Studio를 사용하여 웹 서버 로그를 사용하도록 처음 설정하는 경우 Azure는 로그를 파일 시스템에 기록합니다. 또는 Azure Portal을 사용하여 웹 서버 로그가 스토리지 계정의 Blob 컨테이너에 기록되도록 지정할 수 있습니다.

포털을 사용하여 Azure Storage 계정에 기록하도록 웹 서버 로깅을 설정한 후 Visual Studio에서 로깅을 사용하지 않도록 설정하는 경우 Visual Studio에서 로깅을 사용하도록 다시 설정하면 스토리지 계정 설정이 복원됩니다.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>자세한 오류 메시지 로그 보기
자세한 오류 로그에서는 오류 응답 코드(400 이상)를 유발한 HTTP 요청과 관련된 일부 추가 정보가 제공됩니다. **출력** 창에서 웹 서버 로그를 보려면 웹 앱에서 이를 사용하도록 설정하고, Visual Studio에서 이들의 모니터링을 명시적으로 지정해야 합니다.

1. **서버 탐색기**에서 열었던 **Azure Web App 구성** 탭에서 **자세한 오류 메시지**를 **켜기**로 변경하고 **저장**을 클릭합니다.

    ![자세한 오류 메시지 사용](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. **출력** 창에서 **모니터링할 Microsoft Azure 로그 지정** 버튼을 클릭합니다.

3. **Microsoft Azure 로깅 옵션** 대화 상자에서 **모든 로그**를 클릭한 후 **확인**을 클릭합니다.

    ![모든 로그 모니터링](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. 404 오류를 유발하도록 브라우저 창의 주소 표시줄에서 추가 문자를 URL에 추가(예: `http://localhost:53370/Home/Contactx`)한 후 Enter를 누릅니다.

    몇 초 후 Visual Studio의 **출력** 창에 자세한 오류 로그가 나타납니다.

    ![출력 창의 자세한 오류 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ctrl 키를 누른 채 링크를 클릭하면 브라우저에 서식이 지정된 로그 출력이 표시됩니다.

    ![브라우저 창의 자세한 오류 로그](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>파일 시스템 로그 다운로드
**출력** 창에서 모니터링할 수 있는 모든 로그는 *.zip* 파일로 다운로드할 수도 있습니다.

1. **출력** 창에서 **스트리밍 로그 다운로드**를 클릭합니다.

    ![로그 탭의 단추](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    파일 탐색기에서 다운로드한 파일이 선택된 상태로 *Downloads* 폴더가 열립니다.

    ![다운로드한 파일](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. *.zip* 파일의 압축을 풀면 다음 폴더 구조가 표시됩니다.

    ![다운로드한 파일](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * 애플리케이션 추적 로그는 *LogFiles\Application* 폴더에 *.txt* 파일로 기록됩니다.
   * 웹 서버 로그는 *LogFiles\http\RawLogs* 폴더에 *.log* 파일로 기록됩니다. [Log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) 같은 도구를 사용하여 이들 파일을 보고 조작할 수 있습니다.
   * 자세한 오류 메시지 로그는 *LogFiles\DetailedErrors* 폴더에 *.html* 파일로 기록됩니다.

     참고로, *deployments* 폴더는 소스 제어 게시로 인해 생성되는 것이며 Visual Studio 게시와는 전혀 관계가 없습니다. *Git* 폴더는 소스 제어 게시 및 로그 파일 스트리밍 서비스와 관련된 추적 로그용입니다.  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>실패한 요청 추적 로그 보기
실패한 요청 추적 로그는 URL 다시 쓰기 또는 인증 문제 등이 발생하는 경우 IIS에서 HTTP 요청이 처리되는 방식을 자세히 이해해야 하는 경우에 유용합니다.

App Service 앱은 IIS 7.0 이상 버전에서 사용할 수 있는 실패한 요청 추적 기능을 똑같이 사용합니다. 하지만 기록된 오류를 구성하는 IIS 설정에 대한 액세스 권한이 없습니다. 실패한 요청 추적을 사용하도록 설정하면 모든 오류가 캡처됩니다.

Visual Studio를 사용하여 실패한 요청 추적을 사용하도록 설정할 수 있지만 Visual Studio에서 해당 로그를 볼 수는 없습니다. 이러한 로그는 XML 파일입니다. 스트리밍 로그 서비스는 일반 텍스트 모드에서 읽을 수 있는 것으로 간주되는 파일인 *.txt*, *.html*, *.log* 파일만을 모니터링합니다.

실패한 요청 추적 로그는 FTP를 통해 브라우저에서 직접 보거나 로컬에서 FTP 도구를 사용하여 로컬 컴퓨터에 다운로드한 후 볼 수 있습니다. 이 섹션에서는 브라우저에서 직접 보겠습니다.

1. **서버 탐색기**에서 열었던 **Azure Web App** 창의 **구성** 탭에서 **실패한 요청 추적**을 **켜기**로 변경하고 **저장**을 클릭합니다.

    ![실패한 요청 추적 사용](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. 앱이 표시된 브라우저 창의 주소 표시줄에서 추가 문자를 URL에 추가하고 Enter 키를 눌러 404 오류를 발생시킵니다.

    그러면 실패한 요청 추적 로그가 만들어집니다. 다은 단계는 로그를 보거나 다운로드하는 방법을 보여 줍니다.

3. Visual Studio의 **Azure Web App** 창에 있는 **구성** 탭에서 **관리 포털에서 열기**를 클릭합니다.

4. 앱의 [Azure Portal](https://portal.azure.com) **설정** 페이지에서 **배포 자격 증명**을 클릭한 다음, 새 사용자 이름 및 암호를 입력합니다.

    ![새 FTP 사용자 이름 및 암호](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > 로그인할 때, 앞에 앱 이름이 있는 상태로 전체 사용자 이름을 사용해야 합니다. 예를 들어, 사용자 이름으로 "myid"를 입력하고 사이트가 "myexample"인 경우, "myexample\myid"로 로그인합니다.
    >

5. 새 브라우저 창에서 앱의 **개요** 페이지의 **FTP 호스트 이름** 또는 **FTPS 호스트 이름** 아래에 표시된 URL로 이동합니다.

6. 이전에 만든 FTP 자격 증명을 사용 하 여 로그인 합니다 (사용자 이름에 대 한 앱 이름 접두사 포함).

    브라우저에 앱의 루트 폴더가 표시됩니다.

7. *LogFiles* 폴더를 엽니다.

    ![LogFiles 폴더 열기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. 이름에 W3SVC와 숫자 값이 있는 폴더를 엽니다.

    ![W3SVC 폴더 열기](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    폴더에는 실패한 요청 추적을 사용하도록 설정한 후에 기록된 모든 로그가 XML 파일로 포함되어 있습니다. 또한 브라우저에서 XML 형식으로 사용할 수 있는 XSL 파일도 있습니다.

    ![W3SVC 폴더](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. 추적 정보를 확인하려는 실패한 요청의 XML 파일을 클릭합니다.

    다음 그림에는 샘플 오류에 대한 추적 정보의 일부가 나와 있습니다.

    ![브라우저의 실패한 요청 추적](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>다음 단계
Visual Studio를 사용하여 App Service 앱에서 생성된 로그를 쉽게 보는 방법을 확인했습니다. 다음 섹션에서는 관련 항목에 대한 기타 리소스 링크를 제공합니다.

* App Service 문제 해결
* Visual Studio의 디버깅
* Azure의 원격 디버깅
* ASP.NET 애플리케이션의 추적
* 웹 서버 로그 분석
* 실패한 요청 로그 분석
* Cloud Services 디버깅

### <a name="app-service-troubleshooting"></a>App Service 문제 해결
Azure App Service에서 앱 문제 해결에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [앱을 모니터링하는 방법](web-sites-monitor.md)
* [Visual Studio 2013을 사용하여 Azure App Service에서 메모리 누수 검사](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/) 관리되는 메모리 문제를 분석하는 데 사용되는 Visual Studio 기능에 대한 Microsoft ALM 블로그 게시물입니다.
* [알아 두면 도움이 되는 Azure App Service 온라인 도구](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) Amit Apple의 블로그 게시물입니다.

특정 문제 해결 질문과 관련하여 도움이 필요한 경우 다음 포럼 중 하나에서 게시물을 작성하십시오.

* [ASP.NET 사이트의 Azure 포럼](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)(영문)
* [Microsoft Q의 Azure 포럼은 A를&](https://docs.microsoft.com/answers/topics/azure-webapps.html)합니다.
* [StackOverflow.com](https://www.stackoverflow.com)(영문)

### <a name="debugging-in-visual-studio"></a>Visual Studio의 디버깅
Visual Studio에서 디버그 모드를 사용하는 방법에 대한 자세한 내용은 [Visual Studio의 디버깅](/visualstudio/debugger/debugging-in-visual-studio)과 [Visual Studio 2010을 사용한 디버깅 팁](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)을 참조하세요.

### <a name="remote-debugging-in-azure"></a>Azure의 원격 디버깅
App Service 앱 및 WebJob의 원격 디버깅에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [Azure App Service의 원격 디버깅 소개.](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)
* [Azure App Service의 원격 디버깅 소개 2부 - 원격 디버깅 세부 정보](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Azure App Service의 원격 디버깅 소개 3부 - 다중 인스턴스 환경 및 GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [Webjob 디버깅(비디오)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

앱에 Azure 웹 API 또는 Mobile Services 백 엔드가 사용되는 경우 이들을 디버그해야 하면 [Visual Studio에서 .NET 백 엔드 디버깅](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio)을 참조하세요.

### <a name="tracing-in-aspnet-applications"></a>ASP.NET 애플리케이션의 추적
현재 인터넷에서 ASP.NET 추적과 관련하여 완벽한 최신 소개 정보를 제공하는 곳이 없습니다. 최상의 방법은 MVC가 아직 출시되지 않은 시점에 Web Forms용으로 작성된 기존 소개 자료를 먼저 참조한 후, 특정 문제에 주력하는 최신 블로그 게시물의 정보로 보충하는 것입니다. 시작하기에 유용한 리소스 위치는 다음과 같습니다.

* [모니터링 및 원격 분석(Azure에서 실제 클라우드 앱 빌드)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)<br>
  Azure 클라우드 애플리케이션에서 추적을 권장하는 eBook 챕터.
* [ASP.NET 추적](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
   오래된 자료이지만 추적의 기초를 소개하는 여전히 유용한 리소스입니다.
* [추적 수신기](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  추적 수신기에 대한 정보를 제공하지만 [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener)를 언급하지 않습니다.
* [연습: ASP.NET 추적을 System.Diagnostics 추적과 통합](/previous-versions/b0ectfxd(v=vs.140))<br/>
  이 문서도 오래되었지만 소개 자료에서 다루지 않은 일부 추가 정보가 포함되어 있습니다.
* [ASP.NET MVC Razor 뷰에서 추적](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Razor 뷰의 추적 정보와 더불어, MVC 애플리케이션의 처리되지 않은 모든 예외를 기록할 수 있도록 오류 필터를 만드는 방법을 설명하는 게시물입니다. Web Forms 애플리케이션의 처리되지 않은 모든 예외를 기록하는 방법에 대한 자세한 내용은 MSDN의 [오류 처리기의 전체 예제](/previous-versions/bb397417(v=vs.140))에서 Global.asax를 참조하세요. MVC 또는 Web Forms 중 하나에서 특정 예외를 기록하되 기본 프레임워크 처리 방식은 그대로 적용하려면 다음 예와 같이 해당 오류를 catch한 후 다시 throw하면 됩니다.

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Azure 명령줄에서 진단 추적 로깅 스트리밍(Glimpse 포함)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
   이 사용설명서에서 설명한 Visual Studio 관련 작업을 명령줄로 수행하는 방법을 설명합니다. [Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) 는 ASP.NET 애플리케이션을 디버그하는 데 사용하는 도구입니다.
* [David Ebbo와 함께 하는 Web Apps 로깅 및 진단](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) 및 [David Ebbo와 함께 하는 Web Apps에서 로그 스트리밍](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  은 Scott Hanselman과 David Ebbo가 제작한 비디오입니다.

오류 로깅과 관련하여, 사용자 자신의 추적 코드를 기록하는 또 다른 방법은 [ELMAH](https://nuget.org/packages/elmah/)같은 오픈 소스 로깅 프레임워크를 사용하는 것입니다. 자세한 내용은 [Scott Hanselman의 ELMAH 관련 블로그 게시물](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)(영문)을 참조하십시오.

또한 Azure에서 스트리밍 로그를 가져오기 위해 ASP.NET이나 `System.Diagnostics` 추적을 사용할 필요가 없습니다. App Service 앱 스트리밍 로그 서비스는 *LogFiles* 폴더에 위치한 *.txt*, *.html* 또는 *.log* 파일을 스트리밍합니다. 따라서 앱의 파일 시스템에 기록하는 사용자 자신의 로깅 시스템을 만들 수 있습니다. 그러면 파일이 자동으로 스트리밍되어 다운로드됩니다. 그러려면 *d:\home\logfiles* 폴더에 파일을 만드는 애플리케이션 코드만 작성하면 됩니다.

### <a name="analyzing-web-server-logs"></a>웹 서버 로그 분석
웹 서버 로그 분석에 대한 자세한 내용은 다음 리소스를 참조하세요.

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  웹 서버 로그(*.log* 파일)의 데이터를 보는 데 사용하는 도구입니다.
* [LogParser를 사용 하 여 IIS 성능 문제 또는 응용 프로그램 오류 문제 해결](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
   웹 서버 로그를 분석하는 데 사용할 수 있는 로그 파서 도구를 소개합니다.
* [Robert McMurray의 LogParser 사용 관련 블로그 게시물](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [IIS 7.0, IIS 7.5 및 IIS 8.0의 HTTP 상태 코드](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>실패한 요청 로그 분석
Microsoft TechNet 웹 사이트에는 이러한 로그를 사용 하는 방법을 이해 하는 데 도움이 될 수 있는 [실패 한 요청 추적 사용](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing) 섹션이 포함 되어 있습니다. 하지만 이 설명서에서는 IIS에서 실패한 요청 추적을 구성하는 방법을 중점적으로 다루며, 이는 Azure App Service에서 수행할 수 없습니다.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki