---
title: Azure Mobile Apps에서 오프라인 데이터 동기화 | Microsoft Docs
description: Azure Mobile Apps에 대한 오프라인 데이터 동기화 기능의 개념 참조 및 개요
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 982fb683-8884-40da-96e6-77eeca2500e3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: ab8fb4a567e4c4a7bf1e884999a4e403a98547a0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128018"
---
# <a name="offline-data-sync-in-azure-mobile-apps"></a>Azure Mobile Apps에서 오프라인 데이터 동기화
## <a name="what-is-offline-data-sync"></a>오프라인 데이터 동기화 정의
오프라인 데이터 동기화는 개발자가 네트워크 연결 없이 작동하는 앱을 쉽게 만들 수 있는 Azure Mobile Apps의 클라이언트 및 서버 SDK 기능입니다.

앱이 오프라인 모드일 때 여전히 데이터를 만들고 수정할 수 있으며 이는 로컬 저장소에 저장됩니다. 앱이 다시 온라인 상태가 되면 로컬 변경 내용을 Azure 모바일 앱 백 엔드와 동기화할 수 있습니다. 동일한 레코드가 클라이언트와 백 엔드 모두에서 변경될 때 기능은 충돌을 감지하는 지원을 포함합니다. 충돌을 서버 또는 클라이언트에서 처리할 수 있습니다.

오프라인 동기화의 몇 가지 혜택은 다음과 같습니다.

* 서버 데이터를 디바이스에 로컬로 캐시하여 앱 응답성 향상
* 네트워크 문제가 있는 경우 유용한 강력한 앱 만들기
* 최종 사용자가 네트워크에 액세스할 수 없는 경우에도 데이터를 만들고 수정할 수 있도록 허용하여 네트워크에 연결되지 않은 시나리오까지 지원
* 여러 디바이스 간에 데이터를 동기화하고 동일한 레코드를 두 개의 디바이스에서 수정할 때 충돌 감지
* 지연 시간이 길거나 요금제인 네트워크에 대한 네트워크 사용 제한

다음 자습서는 Azure Mobile Apps를 사용하여 모바일 클라이언트에 오프라인 동기화를 추가하는 방법을 보여줍니다.

* [Android: 오프 라인 동기화 사용]
* [Apache Cordova: 오프 라인 동기화 사용](app-service-mobile-cordova-get-started-offline-data.md)
* [iOS: 오프 라인 동기화 사용]
* [Xamarin iOS: 오프 라인 동기화 사용]
* [Xamarin Android: 오프 라인 동기화 사용]
* [Xamarin.Forms: 오프 라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [유니버설 Windows 플랫폼: 오프 라인 동기화 사용]

## <a name="what-is-a-sync-table"></a>동기화 테이블 정의
"/tables" 엔드포인트에 액세스하는 데 Azure 모바일 클라이언트 SDK가 `IMobileServiceTable`(.NET 클라이언트 SDK) 또는 `MSTable`(iOS 클라이언트)와 같은 인터페이스를 제공합니다. 이러한 API는 Azure 모바일 앱 백 엔드에 직접 연결하고 클라이언트 디바이스에 네트워크 연결이 없는 경우 실패합니다.

오프라인 사용을 지원하려면 앱은 `IMobileServiceSyncTable`(.NET 클라이언트 SDK) 또는 `MSSyncTable`(iOS 클라이언트)과 같은 *동기화 테이블* API를 대신 사용해야 합니다. 같은 CRUD 작업 모두(만들기, 읽기, 업데이트, 삭제)가 *로컬 저장소*에 읽거나 쓰는 것을 제외하고 동기화 테이블 API에 대해 작동합니다. 모든 동기화 테이블 작업을 수행하려면 먼저 로컬 저장소를 초기화해야 합니다.

## <a name="what-is-a-local-store"></a>로컬 저장소 정의
로컬 저장소는 클라이언트 디바이스의 데이터 지속성 계층입니다. Azure Mobile Apps 클라이언트 SDK는 기본 로컬 저장소 구현을 제공합니다. Windows, Xamarin 및 Android에서는 SQLite에 기반합니다. iOS에서는 코어 데이터에 기반합니다.

Windows Phone 또는 Microsoft Store에서 SQLite 기반 구현을 사용하려면 SQLite 확장을 설치해야 합니다. 자세한 내용은 참조 하세요. [유니버설 Windows 플랫폼: 오프 라인 동기화 사용]합니다. Android 및 iOS은 디바이스 운영 체제 자체에서 SQLite의 버전으로 제공되므로 SQLite의 고유한 버전을 참조하는 데 필요하지 않습니다.

또한 개발자는 자신의 로컬 저장소를 구현할 수 있습니다. 예를 들어 모바일 클라이언트에서 암호화된 형식으로 데이터를 저장하려는 경우 암호화에 SQLCipher를 사용하는 로컬 저장소를 정의할 수 있습니다.

## <a name="what-is-a-sync-context"></a>동기화 컨텍스트 정의
*동기화 컨텍스트*는 모바일 클라이언트 개체와 연결되고(예: `IMobileServiceClient` 또는 `MSClient`) 동기화 테이블에 적용된 변경 내용을 추적합니다. 동기화 컨텍스트는 나중에 서버로 보내지는 정렬된 CUD(만들기, 업데이트, 삭제) 작업 목록을 관리하는 *작업 큐*를 포함합니다.

로컬 저장소는 [.NET 클라이언트 SDK]에서 `IMobileServicesSyncContext.InitializeAsync(localstore)`와 같은 초기화 메서드를 사용하여 동기화 컨텍스트와 연결됩니다.

## <a name="how-sync-works"></a>오프라인 동기화 작동 방법
동기화 테이블을 사용할 경우 클라이언트 코드는 로컬 변경이 Azure 모바일 앱 백 엔드와 동기화하는 시기를 제어합니다. 로컬 변경을 *푸시*하는 호출이 있을 때까지 백 엔드에 아무 것도 보내지 않습니다. 마찬가지로 로컬 저장소는 데이터 *끌어오기* 호출이 있을 때 새 데이터로 채워집니다

* **푸시**: 푸시는 동기화 컨텍스트에 작업이 며 마지막 푸시 이후로 모든 CUD 변경을 보냅니다. 그렇지 않으면 작업이 잘못된 순서로 전송되기 때문에 개별 테이블의 변경 내용만을 보낼 수 없습니다. 푸시는 Azure 모바일 앱 백 엔드에 일련의 REST 호출을 실행하여 차례로 서버 데이터베이스를 수정합니다.
* **끌어오기**: 끌어오기는 테이블 당 단위로 수행 되 고 서버 데이터의 하위 집합만 검색 하는 쿼리를 사용 하 여 사용자 지정할 수 있습니다. 그런 다음 Azure 모바일 클라이언트 SDK는 로컬 저장소에 발생한 데이터를 삽입합니다.
* **암시적 푸시**: 끌어오기가 로컬 업데이트를 보류 중인 테이블에 대해를 실행 하는 경우 먼저 실행을 `push()` 동기화 컨텍스트에 있습니다. 이미 큐에 대기 중인 변경 내용과 서버의 새 데이터 간에 충돌을 최소화하는 데 이 푸시가 도움이 됩니다.
* **증분 동기화**: 가져오기 작업의 첫 번째 매개 변수는 클라이언트에만 사용되는 *쿼리 이름* 입니다. Null이 아닌 쿼리 이름을 사용할 경우 Azure 모바일 SDK는 *증분 동기화*를 수행합니다. 가져오기 작업이 결과 집합을 반환할 때마다 해당 결과 집합에서 최신 `updatedAt` timestamp는 SDK 로컬 시스템 테이블에 저장됩니다. 후속 끌어오기 작업은 해당 timestamp 이후의 레코드만 검색합니다.

  증분 동기화를 사용하려면 서버가 의미 있는 `updatedAt` 값을 반환하고 이 필드의 정렬을 지원해야 합니다. 그러나 SDK가 updatedAt 필드에서 자체 정렬를 추가하므로 고유의 `orderBy` 절을 가진 끌어오기 쿼리를 사용할 수 없습니다.

  쿼리 이름에 모든 문자열을 선택할 수 있지만 앱에서 각 논리 쿼리에 대해 고유해야 합니다.
  그렇지 않은 경우 다른 끌어오기 작업이 동일한 증분 동기화 timestamp를 덮어쓰고 쿼리는 잘못된 결과를 반환할 수 있습니다.

  쿼리에 매개 변수가 있으면 고유한 쿼리 이름을 만드는 한 가지 방법은 매개 변수 값을 통합하는 것입니다.
  예를 들어 userid를 필터링할 경우 사용자 쿼리 이름은 C#으로 다음과 같을 수 있습니다.

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  증분 동기화를 옵트아웃(opt out)하려면 `null`을 쿼리 ID로 전달합니다. 이 경우 `PullAsync`에 대한 모든 호출에서 모든 레코드가 검색되므로 이는 잠재적으로 비효율적입니다.
* **제거**: 사용 하 여 로컬 저장소의 내용을 지울 수 있습니다 `IMobileServiceSyncTable.PurgeAsync`합니다.
  제거는 클라이언트 데이터베이스에서 오래된 데이터가 있는 경우 또는 모든 보류 중인 변경 내용을 취소하려는 경우에 필요할 수 있습니다.

  제거는 로컬 저장소에서 테이블을 지웁니다. 서버 데이터베이스와 동기화 보류 중인 작업이 있는 경우 *강제 제거* 매개 변수를 설정하지 않으면 제거가 예외를 throw합니다.

  클라이언트에서 오래된 데이터의 예로 “todo 목록” 예에서 디바이스1이 완료되지 않은 항목을 끌어온다고 가정합니다. todoitem "우유 구매"가 다른 디바이스에 의해 서버에서 완료되었다고 표시됩니다. 그러나 Device1은 완료 표시된 항목을 가져오기 때문에 아직 "우유 구매" todoitem을 로컬 저장소에 가지고 있습니다. 제거는 이 오래된 항목을 지웁니다.

## <a name="next-steps"></a>다음 단계
* [iOS: 오프 라인 동기화 사용]
* [Xamarin iOS: 오프 라인 동기화 사용]
* [Xamarin Android: 오프 라인 동기화 사용]
* [유니버설 Windows 플랫폼: 오프 라인 동기화 사용]

<!-- Links -->
[.NET 클라이언트 SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android: 오프 라인 동기화 사용]: app-service-mobile-android-get-started-offline-data.md
[iOS: 오프 라인 동기화 사용]: app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: 오프 라인 동기화 사용]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: 오프 라인 동기화 사용]: app-service-mobile-xamarin-android-get-started-offline-data.md
[유니버설 Windows 플랫폼: 오프 라인 동기화 사용]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md
