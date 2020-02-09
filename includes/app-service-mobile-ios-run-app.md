---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a5bde1a56bf6a1f5fca4b775c7c8e9bb7477eb6b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240263"
---
1. Xcode를 사용 하 여 다운로드 한 클라이언트 프로젝트를 엽니다.

2. 로 이동 합니다 [Azure portal](https://portal.azure.com/) 만든 모바일 앱으로 이동 합니다. 에 `Overview` 블레이드에서 모바일 앱에 대한 공용 엔드포인트 URL 찾습니다. 예제-내 앱 이름 "test123"에 대한 sitename 됩니다 https://test123.azurewebsites.net 합니다.

3. Swift 프로젝트 파일을 열고 `ToDoTableViewController.swift` ZUMOAPPNAME/ZUMOAPPNAME/ToDoTableViewController.swift-이 폴더에 있습니다. 응용 프로그램 이름이 `ZUMOAPPNAME`합니다.

4. `viewDidLoad()` 메서드를 대체 `ZUMOAPPURL` 위의 공용 엔드포인트을 사용 하 여 매개 변수입니다.

    `let client = MSClient(applicationURLString: "ZUMOAPPURL")`

    됩니다.
    
    `let client = MSClient(applicationURLString: "https://test123.azurewebsites.net")`
    
5. Objective C 프로젝트 파일을 열고 `QSTodoService.m` ZUMOAPPNAME/ZUMOAPPNAME-이 폴더에 있습니다. 응용 프로그램 이름이 `ZUMOAPPNAME`합니다.

6. `init` 메서드를 대체 `ZUMOAPPURL` 위의 공용 엔드포인트을 사용 하 여 매개 변수입니다.

    `self.client = [MSClient clientWithApplicationURLString:@"ZUMOAPPURL"];`

    됩니다.
    
    `self.client = [MSClient clientWithApplicationURLString:@"https://test123.azurewebsites.net"];`

7. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 시뮬레이터에서 앱을 시작합니다.

8. 앱에서 더하기 아이콘( **+** )을 클릭하고, 의미 있는 텍스트(예: *Complete the tutorial*)를 입력한 다음, 저장 단추를 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.

   ![iOS에서 실행 중인 빠른 시작 앱](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure portal]: https://portal.azure.com/
