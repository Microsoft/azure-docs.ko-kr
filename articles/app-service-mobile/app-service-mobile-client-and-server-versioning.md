---
title: 클라이언트 및 서버 SDK 버전 관리
description: Mobile Services 및 Azure Mobile Apps에 대한 클라이언트 Sdk 및 서버 SDK 버전과의 호환성을 나열 합니다.
ms.assetid: 35b19672-c9d6-49b5-b405-a6dcd1107cd5
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4d0b301dee363c2338cb13a9fc09ee17549467eb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668852"
---
# <a name="client-and-server-versioning-in-mobile-apps-and-mobile-services"></a>Mobile Apps 및 Mobile Services에서 클라이언트 및 서버 버전 관리
> [!NOTE]
> Visual Studio App Center는 모바일 앱 개발의 중심인 엔드투엔드 통합 서비스를 지원합니다. 개발자는 **빌드**, **테스트** 및 **배포** 서비스를 사용하여 지속적인 통합 및 업데이트 파이프라인을 설정할 수 있습니다. 앱이 배포되면 개발자는 **분석** 및 **진단** 서비스를 사용하여 앱의 상태와 사용 현황을 모니터링하고, **푸시** 서비스를 사용하여 사용자와 소통할 수 있습니다. 또한 개발자는 **인증** 서비스를 사용하여 사용자를 인증하고, **데이터** 서비스를 사용하여 클라우드에서 애플리케이션 데이터를 유지하고 동기화할 수도 있습니다.
>
> 모바일 애플리케이션에서 클라우드 서비스를 통합하려면 지금 [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc)에 등록하세요.

Azure Mobile Services의 최신 버전은 Azure App Service의 **Mobile Apps** 기능입니다.

Mobile Apps 클라이언트 및 서버 SDK는 원래 Mobile Services를 기반으로 하지만 서로 호환되지 *않습니다* .
즉, *Mobile Apps* 서버 SDK 및 마찬가지로 *Mobile Services*를 사용하는 *Mobile Apps* 클라이언트 SDK를 사용해야 합니다. 이 계약은 클라이언트 및 서버 SDK인 `ZUMO-API-VERSION`에서 사용하는 특별한 헤더 값을 통해 적용됩니다.

참고: 이 문서가 *Mobile Services* 백 엔드를 참조할 때마다 반드시 Mobile Services에서 호스팅해야 할 필요는 없습니다. 이제 코드를 변경하지 않고 App Service에서 실행되도록 Mobile Services를 마이그레이션할 수 있지만 서비스는 *Mobile Services* SDK 버전을 사용합니다.

## <a name="header-specification"></a>헤더 사양
키 `ZUMO-API-VERSION` 는 HTTP 헤더 또는 쿼리 문자열에 지정될 수 있습니다. 값은 **x.y.z**형식의 버전 문자열입니다.

다음은 그 예입니다.

GET https://service.azurewebsites.net/tables/TodoItem

HEADERS: ZUMO-API-VERSION: 2.0.0

POST https://service.azurewebsites.net/tables/TodoItem?ZUMO-API-VERSION=2.0.0

## <a name="opting-out-of-version-checking"></a>버전 확인 건너뛰기
앱 설정 **MS_SkipVersionCheck**에 대한 **true** 값을 설정하여 버전 확인을 건너뛸 수 있습니다. Web.config 또는 Azure Portal의 애플리케이션 설정 섹션에서 이를 지정합니다.

> [!NOTE]
> 오프라인 동기화, 인증 및 푸시 알림 영역에서 특히 Mobile Services와 Mobile Apps 간의 많은 동작 변경 사항이 있습니다. 이러한 동작 변경이 앱의 기능을 중단하지 않도록 테스트를 완료한 후에 버전 확인을 옵트아웃해야 합니다.

## <a name="2.0.0"></a>Azure Mobile Apps 클라이언트 및 서버
### <a name="MobileAppsClients"></a> Mobile *Apps* 클라이언트 SDK
버전 확인은 **Azure Mobile Apps**에 대한 클라이언트 SDK의 다음 버전부터 도입됩니다.

| 클라이언트 플랫폼 | 버전 | 버전 헤더 값 |
| --- | --- | --- |
| 관리된 클라이언트(Windows, Xamarin) |[2.0.0](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/2.0.0) |2.0.0 |
| iOS |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=529823) |2.0.0 |
| Android |[3.0.0](https://go.microsoft.com/fwlink/?LinkID=717033&clcid=0x409) |3.0.0 |

### <a name="mobile-apps-server-sdks"></a>Mobile *Apps* 서버 SDK
버전 검사는 다음 서버 SDK 버전에 포함됩니다.

| 서버 플랫폼 | SDK | 수락된 버전 헤더 |
| --- | --- | --- |
| .NET |[Microsoft.Azure.Mobile.Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) |2.0.0 |
| Node.js |[azure-mobile-apps)](https://www.npmjs.com/package/azure-mobile-apps) |2.0.0 |

### <a name="behavior-of-mobile-apps-backends"></a>Mobile Apps 백 엔드의 동작
| ZUMO-API-VERSION | MS_SkipVersionCheck의 값 | 응답 |
| --- | --- | --- |
| x.y.z 또는 Null |참 |200 - 확인 |
| Null |False/지정되지 않음 |400 - 잘못된 요청 |
| 1.x.y |False/지정되지 않음 |400 - 잘못된 요청 |
| 2.0.0-2.x.y |False/지정되지 않음 |200 - 확인 |
| 3.0.0-3.x.y |False/지정되지 않음 |400 - 잘못된 요청 |

[Mobile Services clients]: #MobileServicesClients
[Mobile Apps clients]: #MobileAppsClients
[Mobile App Server SDK]: https://www.nuget.org/packages/microsoft.azure.mobile.server
