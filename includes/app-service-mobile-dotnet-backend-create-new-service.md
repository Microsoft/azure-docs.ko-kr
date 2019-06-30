---
title: 포함 파일
description: 포함 파일
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325798"
---
1. [Azure Portal]에 로그인합니다.

2. **리소스 만들기**를 클릭합니다.

3. 검색 상자에 입력 **웹 앱**합니다.
    
4. 결과 목록에서 선택 **웹 앱** Marketplace에서.

5. 선택 하 **구독** 하 고 **리소스 그룹** (기존 리소스 그룹을 선택 _또는_ (앱 같은 이름 사용)를 새로 만듭니다).

6. 고유한 선택 **이름을** 웹 앱입니다.

7. 기본 선택 **게시** 옵션이 **코드**합니다.

8. 에 **런타임 스택을**, 아래에서 버전을 선택 해야 **ASP.NET** 또는 **노드**합니다. .NET 백 엔드를 구축 하는 경우 ASP.NET에서 사용 중인 버전을 선택 합니다. 그렇지 않으면 노드 기반 응용 프로그램을 대상으로 하는 경우 중 하나를 선택 버전 노드에서 합니다.

9. 오른쪽 선택 **운영 체제**, Linux 또는 Windows입니다. 

10. 선택 된 **지역** 이 앱을 배포 하려는 합니다. 

11. 적절 한 선택 **App Service 계획** 누릅니다 **검토를 만들어**합니다. 

12. **리소스 그룹** 아래에서 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다(앱과 동일한 이름 사용).

13. **만들기**를 클릭합니다. 계속 진행하기 전에 서비스가 성공적으로 배포되도록 몇 분 동안 기다립니다. 상태 업데이트를 확인하려면 포털 머리글에 있는 알림(벨) 아이콘을 지켜봅니다.

14. 배포가 완료 되 면 클릭 합니다 **배포 세부 정보** 섹션 및 리소스의 유형을 클릭 **microsoft.web/sites**합니다. 것으로 이동 하 여 방금 만든 App Service 웹 앱입니다. 

15. 클릭는 **구성** 블레이드의 **설정** 및는 **응용 프로그램 설정**를 클릭 합니다 **새 응용 프로그램 설정** 단추입니다.

16. 에 **응용 프로그램 설정 추가/편집** 페이지에서 입력 **이름** 으로 **MobileAppsManagement_EXTENSION_VERSION** 값 **최신** 및 확인을 누릅니다.

설정이 모두 새로 만든 모바일 앱으로 App Service 웹 앱이 사용 하도록 합니다.

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/