---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 63c54f8af91b6b4a76ba49d5e6fc7b3cda9f5b98
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66240335"
---
1. **Android Studio**에서 **프로젝트(Eclipse ADT, Gradle 등) 가져오기**를 사용하여 프로젝트를 엽니다. 모든 JDK 오류를 방지하려면 이 가져오기를 선택해야 합니다.

2. 파일을 열고 `ToDoActivity.java` ZUMOAPPNAME/app/src/main/java/com/예제/zumoappname-이 폴더에 있습니다. 응용 프로그램 이름이 `ZUMOAPPNAME`합니다.

3. 로 이동 합니다 [Azure portal](https://portal.azure.com/) 만든 모바일 앱으로 이동 합니다. 에 `Overview` 블레이드에서 모바일 앱에 대한 공용 엔드포인트 URL 찾습니다. 예제-내 앱 이름 "test123"에 대한 sitename 됩니다 https://test123.azurewebsites.net 합니다.

4. `onCreate()` 메서드를 대체 `ZUMOAPPURL` 위의 공용 엔드포인트을 사용 하 여 매개 변수입니다.
    
    `new MobileServiceClient("ZUMOAPPURL", this).withFilter(new ProgressFilter());` 
    
    됩니다.
    
    `new MobileServiceClient("https://test123.azurewebsites.net", this).withFilter(new ProgressFilter());`
    
5. **'앱' 실행** 단추를 눌러 프로젝트를 빌드하고 Android 시뮬레이터에서 앱을 시작합니다.

4. 앱에서 *자습서 완료* 등의 의미 있는 텍스트를 입력한 후 '추가' 단추를 클릭합니다. 그러면 이전에 배포한 Azure 백 엔드에 POST 요청을 보냅니다. 요청의 백 엔드 데이터가 TodoItem SQL 테이블로 삽입되고 새로 저장된 항목에 대한 정보를 모바일 앱으로 다시 반환합니다. 모바일 앱이 이 데이터를 목록에 표시합니다.
    ![Android 빠른 시작](./media/app-service-mobile-android-quickstart/mobile-quickstart-startup-android.png)