---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 8d7731480b6239c572d39f52b6a0217d2ac48d25
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240254"
---
1. (.Sln) 클라이언트 프로젝트에서 솔루션 파일을 찾아 Visual Studio를 사용 하 여 엽니다.

2. Visual Studio에서 시작 화살표 옆에 있는 드롭다운 목록에서 솔루션 플랫폼(Android, iOS 또는 Windows)을 선택합니다. 녹색 화살표의 드롭다운을 클릭하여 특정 배포 디바이스 또는 에뮬레이터를 선택합니다. 기본 Android 플랫폼 및 Ripple 에뮬레이터를 사용할 수 있습니다. 더 많은 고급 자습서(예: 푸시 알림)를 보려면 지원되는 디바이스 또는 에뮬레이터를 선택해야 합니다.

3. 파일을 열고 `ToDoActivity.java` ZUMOAPPNAME/app/src/main/java/com/예제/zumoappname-이 폴더에 있습니다. 응용 프로그램 이름이 `ZUMOAPPNAME`합니다.

4. 로 이동 합니다 [Azure portal](https://portal.azure.com/) 만든 모바일 앱으로 이동 합니다. 에 `Overview` 블레이드에서 모바일 앱에 대한 공용 엔드포인트 URL 찾습니다. 예제-내 앱 이름 "test123"에 대한 sitename 됩니다 https://test123.azurewebsites.net 합니다.

5. 로 이동 합니다 `index.js` ZUMOAPPNAME/www/js/index.js 및 파일 `onDeviceReady()` 메서드를 대체 `ZUMOAPPURL` 위의 공용 엔드포인트을 사용 하 여 매개 변수입니다.

    `client = new WindowsAzure.MobileServiceClient('ZUMOAPPURL');`
    
    됩니다.
    
    `client = new WindowsAzure.MobileServiceClient('https://test123.azurewebsites.net');`
    
6. Cordova 앱을 빌드하고 실행하려면 F5 키를 누르거나 녹색 화살표를 클릭합니다. 네트워크에 대한 액세스를 요청하는 에뮬레이터에서 보안 대화 상자가 표시되는 경우, 이를 수락합니다.

7. 장치 또는 에뮬레이터에서 앱 시작 된 후에 의미 있는 텍스트를 입력 **새 텍스트 입력**와 같은 *자습서를 완료* 클릭 하 고는 **추가** 단추입니다.

요청의 백 엔드 데이터가 SQL Database의 TodoItem 테이블에 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.

다른 플랫폼에 대해 3-5단계를 반복할 수 있습니다.