---
title: Azure Mobile Apps에 iOS SDK를 사용하는 방법
description: Azure Mobile Apps에 iOS SDK를 사용하는 방법
services: app-service\mobile
documentationcenter: ios
author: conceptdev
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: b6f93cc3c35ab18ecd50ccd6b3090985497baabf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122458"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Azure Mobile Apps용 iOS 클라이언트 라이브러리를 사용하는 방법

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 최신 [Azure Mobile Apps iOS SDK][1]를 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure Mobile Apps를 처음 접하는 경우 먼저 [Azure Mobile Apps 빠른 시작] 을 완료하여 백 엔드를 만들고, 테이블을 만든 다음 미리 빌드된 iOS Xcode 프로젝트를 다운로드합니다. 이 가이드에서는 클라이언트 쪽 iOS SDK에 초점을 둡니다. 백 엔드의 서버 쪽 SDK에 대한 자세한 내용은 서버 SDK 사용 방법을 참조하세요.

## <a name="reference-documentation"></a>참조 설명서

iOS 클라이언트 SDK에 대한 참조 설명서는 [Azure Mobile Apps iOS 클라이언트 참조][2]에 있습니다.

## <a name="supported-platforms"></a>지원되는 플랫폼

iOS SDK는 iOS 버전 8.0 이상을 위한 Objective-C 프로젝트, Swift 2.2 프로젝트 및 Swift 2.3 프로젝트를 지원합니다.

"서버-흐름" 인증은 표시된 UI에 웹 보기를 사용합니다.  디바이스가 웹 보기 UI를 표시할 수 없는 경우 제품 범위를 벗어나는 다른 인증 방법이 필요합니다.  
따라서 이 SDK는 Watch 유형 또는 그와 비슷하게 제한된 디바이스에는 적합하지 않습니다.

## <a name="Setup"></a>설정 및 필수 조건

이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다. 또한 이 가이드에서는 코드에서 `MicrosoftAzureMobile.framework`를 참조하고 `MicrosoftAzureMobile/MicrosoftAzureMobile.h`를 가져온다고 가정합니다.

## <a name="create-client"></a>방법: 클라이언트 만들기

프로젝트에서 Azure Mobile Apps 백 엔드에 액세스하려면 `MSClient`를 만듭니다. `AppUrl` 을 앱 URL로 대체합니다. `gatewayURLString` 및 `applicationKey`는 비워둘 수 있습니다. 인증에 대한 게이트웨이를 설정하는 경우 `gatewayURLString` 을 게이트웨이 URL로 채웁니다.

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>방법: 테이블 참조 만들기

데이터에 액세스하거나 데이터를 업데이트하려면 백 엔드 테이블에 대한 참조를 만듭니다. `TodoItem` 을 테이블의 이름으로 바꿉니다.

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>방법: 데이터 쿼리

데이터베이스 쿼리를 만들려면 `MSTable` 개체를 쿼리합입니다. 다음 쿼리는 `TodoItem`의 모든 항목을 가져오고 각 항목의 텍스트를 기록합니다.

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>방법: 반환된 데이터 필터링

결과를 필터링하는 데 사용할 수 있는 많은 옵션이 있습니다.

조건자를 사용하여 필터링하려면 `NSPredicate` 및 `readWithPredicate`을(를) 사용합니다. 다음은 반환된 데이터를 필터링하여 불완전한 할 일 항목만 찾습니다.

**Objective-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>방법: MSQuery 사용

복잡한 쿼리(정렬 및 페이징 포함)를 수행하려면 직접 또는 조건자를 사용하여 `MSQuery` 개체를 만듭니다.

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` 를 사용하여 여러 쿼리 동작을 제어할 수 있습니다.

* 결과의 순서 지정
* 반환할 필드 제한
* 반환할 레코드 수 제한
* 응답의 총 수 지정
* 요청에서 사용자 지정 쿼리 문자열 매개 변수 지정
* 추가 함수 적용

개체에 `readWithCompletion`을 호출하여 `MSQuery` 쿼리를 실행합니다.

## <a name="sorting"></a>방법: MSQuery를 사용하여 데이터 정렬

결과를 정렬하기 위해 예제를 살펴봅시다. 'text' 필드를 기준으로 오름차순으로 정렬한 다음 'complete' 필드를 기준으로 내림차순으로 정렬하려면 다음과 같이 `MSQuery` 를 호출합니다.

**Objective-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="selecting"></a><a name="parameters"></a>방법: MSQuery를 사용하여 필드 제한 및 쿼리 문자열 매개 변수 확장

쿼리에서 반환되는 필드를 제한하려면 **selectFields** 속성에서 필드의 이름을 지정합니다. 이 예는 텍스트 필드와 완료된 필드만 반환합니다.

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

서버 요청에서 추가 쿼리 문자열 매개 변수를 포함하려면(예: 사용자 지정 서버 쪽 스크립트가 이를 사용하기 때문) 다음과 같이 `query.parameters` 을(를) 채웁니다.

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>방법: 페이지 크기 구성

Azure Mobile Apps의 페이지 크기는 백 엔드 테이블에서 한 번에 가져오는 레코드의 수를 제어합니다. `pull` 데이터에 대한 호출은 끌어올 레코드가 더 이상 없을 때까지 이 페이지 크기에 따라 데이터를 일괄 처리합니다.

아래와 같이 **MSPullSettings**를 사용하여 페이지 크기를 구성할 수 있습니다. 기본 페이지 크기는 50이고 아래 예제에서는 3으로 변경합니다.

성능상의 이유로 다른 페이지 크기를 구성할 수 있습니다. 작은 데이터 레코드 수가 많은 경우 페이지 크기가 크면 서버 왕복 횟수를 줄입니다.

이 설정은 클라이언트 쪽의 페이지 크기만 제어합니다. 클라이언트가 Mobile Apps 백 엔드에서 지원하는 것보다 큰 페이지 크기를 지원하도록 요청하는 경우 백 엔드에서 지원하도록 구성된 최대 페이지 크기로 제한됩니다.

또한 이 설정은 *바이트 크기*가 아니라 데이터 레코드의 *수*입니다.

클라이언트 페이지 크기를 늘리면 서버에서 페이지 크기도 늘려야 합니다. 이 작업을 수행하는 단계는 [“방법: 테이블 페이징 크기 조정”](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>방법: 데이터 삽입

새 테이블 행을 삽입하려면 `NSDictionary`를 만들고 `table insert`를 호출합니다. [동적 스키마]를 사용하는 경우 Azure App Service 모바일 백 엔드는 `NSDictionary`에 따라 새 열을 자동으로 생성합니다.

`id` 이(가) 제공되지 않는 경우 백 엔드는 고유한 새 ID를 자동으로 생성합니다. 고유한 `id` 을(를) 제공하여 이메일 주소, 사용자 이름을 사용하거나 고유한 사용자 지정 값을 ID로 사용합니다. 고유한 ID를 제공하면 조인 및 비즈니스 지향적인 데이터베이스 논리가 쉬을 수 있습니다.

`result` 에는 삽입된 새 항목이 포함되어 있습니다. 서버 논리에 따라, 서버에 전달된 데이터와 비교했을 때 추가된 또는 수정된 데이터가 있을 수도 있습니다.

**Objective-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>방법: 데이터 수정

기존 행을 업데이트하려면 항목을 수정하고 `update`을(를) 호출합니다.

**Objective-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

또는 행 ID와 업데이트된 필드를 제공합니다.

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

최소한 업데이트할 때에는 `id` 특성이 설정되어야 합니다.

## <a name="deleting"></a>방법: 데이터 삭제

항목을 삭제하려면 항목과 함께 `delete` 을(를) 호출합니다.

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

또는 행 ID를 제공하여 삭제합니다.

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

최소한 삭제할 때에는 `id` 특성이 설정되어야 합니다.

## <a name="customapi"></a>방법: 사용자 지정 API 호출

사용자 지정 API를 사용하여 백 엔드 기능을 노출할 수 있습니다. 테이블 작업에 매핑할 필요는 없습니다. 더 효율적으로 메시징을 제어할 수 있으며 헤더의 읽기/설정 및 응답의 본문 형식을 변경할 수도 있습니다. 백 엔드에서 사용자 지정 API를 만드는 방법에 대한 자세한 내용은 [사용자 지정 API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

사용자 지정 API를 호출하려면 `MSClient.invokeAPI`를 호출합니다. 요청 및 응답 콘텐츠는 JSON으로 간주됩니다. 다른 미디어 유형을 사용하려면 [의 다른 오버로드를 사용`invokeAPI`][5]합니다.  `POST` 요청 대신 `GET` 요청을 하려면 GET 요청에는 메시지의 본문이 없기 때문에 매개 변수를 `HTTPMethod`에서 `"GET"` 및 `body`에서 `nil`으로 설정합니다. 사용자 지정 API가 다른 HTTP 동사를 지원하는 경우 `HTTPMethod`을(를) 적절하게 변경합니다.

**Objective-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>방법: 플랫폼 간 알림을 보내기 위해 푸시 템플릿 등록

템플릿을 등록하려면 클라이언트 앱에서 **client.push registerDeviceToken** 메서드를 사용하여 템플릿을 전달합니다.

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

템플릿은 NSDictionary 형식이며 다음 형식으로 여러 템플릿을 포함할 수 있습니다.

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

보안을 위해 요청에서 모든 태그가 제거됩니다.  설치에 태그를 추가하거나 설치 내에 템플릿을 추가하려면 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용][4]을 참조하세요.  이러한 등록된 템플릿을 사용하여 알림을 보내려면 [Notification Hubs API][3]로 작업하세요.

## <a name="errors"></a>방법: 오류 처리

Azure App Service 모바일 백 엔드를 호출할 때 완료 블록에 `NSError` 매개 변수가 포함됩니다. 오류가 발생하면 이 매개 변수는 null이 아닌 값입니다. 앞의 코드 조각에서 보여준 것처럼, 코드에서 이 매개 변수를 확인하고 필요한 경우 오류를 처리해야 합니다.

[`<WindowsAzureMobileServices/MSError.h>`][6] 파일은 `MSErrorResponseKey`, `MSErrorRequestKey` 및 `MSErrorServerItemKey` 상수를 정의합니다. 오류와 관련된 데이터를 더 가져오는 방법은 다음과 같습니다.

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

또한 이 파일은 각 오류 코드에 대한 상수를 정의합니다.

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>방법: Active Directory 인증 라이브러리를 사용하여 사용자 인증

Azure Active Directory를 사용하여 애플리케이션에 사용자가 로그인하려면 Active Directory 인증 라이브러리(ADAL)를 사용할 수 있습니다. ID 공급자 SDK를 사용하는 클라이언트 흐름 인증이 `loginWithProvider:completion:` 메서드보다 선호도가 높습니다.  클라이언트 흐름 인증은 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기 때문입니다.

1. [Active Directory 로그인에 App Service를 구성하는 방법][7] 자습서를 수행하여 AAD 로그인에 모바일 앱 백 엔드를 구성합니다. 네이티브 클라이언트 애플리케이션을 등록하는 선택적 단계를 완료해야 합니다. iOS의 경우 권장하는 리디렉션 URI는 `<app-scheme>://<bundle-id>` 형식입니다. 자세한 내용은 [ADAL iOS 빠른 시작][8]을 참조하세요.
2. Cocoapods를 사용하여 ADAL을 설치합니다. 다음 정의를 포함하도록 Podfile을 편집합니다. 이때 **YOUR-PROJECT**를 Xcode 프로젝트의 이름으로 바꿉니다.

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   및 Pod:

        pod 'ADALiOS'

3. 터미널을 사용하여 프로젝트를 포함하는 디렉터리에서 `pod install`을 실행한 다음 생성된 Xcode 작업 영역(프로젝트 아님)을 엽니다.
4. 사용하는 언어에 따라 애플리케이션에 다음 코드를 추가합니다. 각 코드에서 다음과 같이 값을 바꿉니다.

   * **INSERT-AUTHORITY-HERE**를 애플리케이션이 프로비전된 테넌트의 이름으로 바꿉니다. 형식은 https://login.microsoftonline.com/contoso.onmicrosoft.com이어야 합니다. 이 값은 [Azure Portal]의 Azure Active Directory에 있는 도메인 탭에서 복사할 수 있습니다.
   * **INSERT-RESOURCE-ID-HERE** 를 모바일 앱 백 엔드에 대한 클라이언트 ID로 바꿉니다. 포털의 Azure **Active Directory 설정**에 있는 **고급** 탭에서 클라이언트 ID를 가져올 수 있습니다.
   * **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 애플리케이션에서 복사한 클라이언트 ID로 바꿉니다.
   * HTTPS 체계를 사용하여 **INSERT-REDIRECT-URI-HERE** 를 사이트의 */.auth/login/done* 엔드포인트로 바꿉니다. 이 값은 *https://contoso.azurewebsites.net/.auth/login/done*과 비슷해야 합니다.

**Objective-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="facebook-sdk"></a>방법: iOS용 Facebook SDK를 사용하여 사용자 인증

Facebook을 사용하여 애플리케이션에 사용자를 로그인하도록 iOS용 Facebook SDK를 사용할 수 있습니다.  클라이언트 흐름 인증이 `loginWithProvider:completion:` 메서드보다 선호도가 높습니다.  클라이언트 흐름 인증은 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기 때문입니다.

1. [Facebook 로그인에 App Service를 구성하는 방법][9] 자습서를 수행하여 Facebook 로그인에 모바일 앱 백 엔드를 구성합니다.
2. [iOS용 Facebook SDK - 시작][10] 설명서에 따라 iOS용 Facebook SDK를 설치합니다. 앱을 만드는 대신 기존 등록에 iOS 플랫폼을 추가할 수 있습니다.
3. Facebook의 설명서는 앱 대리자에서 일부 Objective-C 코드를 포함합니다. **Swift**를 사용 중인 경우 AppDelegate.swift에 다음 번역을 사용할 수 있습니다.

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. 또한 프로젝트에 `FBSDKCoreKit.framework`을 추가하는 것 외에도 같은 방식으로 `FBSDKLoginKit.framework`에 참조를 추가합니다.
5. 사용하는 언어에 따라 애플리케이션에 다음 코드를 추가합니다.

    **Objective-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="twitter-fabric"></a>방법: iOS용 Twitter Fabric을 사용하여 사용자 인증

Twitter를 사용하여 애플리케이션에 사용자를 로그인하도록 iOS용 Fabric을 사용할 수 있습니다. 클라이언트 흐름 인증은 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기에 `loginWithProvider:completion:` 메서드보다 선호도가 높습니다.

1. 다음으로 [Twitter 로그인에 App Service를 구성하는 방법](../app-service/configure-authentication-provider-twitter.md) 자습서를 수행하여 Twitter 로그인에 모바일 앱 백 엔드를 구성합니다.
2. [iOS용 Fabric - 시작] 설명서를 수행하고 TwitterKit를 설정하여 프로젝트에 Fabric을 추가합니다.

   > [!NOTE]
   > 기본적으로 패브릭은 사용자를 위해 Twitter 애플리케이션을 만듭니다. 다음 코드 조각을 사용하여 이전에 만든 소비자 키 및 소비자 암호를 등록하면 애플리케이션을 만들지 않아도 됩니다.    또는 [패브릭 대시보드]에 표시되는 값으로 App Service를 제공하는 소비자 키 및 소비자 암호 값을 바꿀 수 있습니다. 이 옵션을 선택하면 콜백 URL을 `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`와 같은 자리 표시자 값으로 설정해야 합니다.

    이전에 만든 암호를 사용하도록 선택한 경우 앱 대리자에 다음 코드를 추가합니다.

    **Objective-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. 사용하는 언어에 따라 애플리케이션에 다음 코드를 추가합니다.

    **Objective-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="google-sdk"></a>방법: iOS용 Google 로그인 SDK를 사용하여 사용자 인증

Google 로그인을 사용하여 애플리케이션에 사용자를 로그인하도록 iOS용 Google 로그인 SDK를 사용할 수 있습니다.  최근에 Google에서 OAuth 보안 정책 변경 소식을 발표했습니다.  정책이 변경됨에 따라 향후에는 Google SDK를 사용해야 할 것입니다.

1. 다음으로 [Google 로그인에 App Service를 구성하는 방법](../app-service/configure-authentication-provider-google.md) 자습서를 수행하여 Google 로그인에 모바일 앱 백 엔드를 구성합니다.
2. [iOS용 Google 로그인 - 통합 시작](https://developers.google.com/identity/sign-in/ios/start-integrating) 설명서에 따라 iOS용 Google SDK를 설치합니다. "백 엔드 서버를 사용하여 인증" 섹션은 건너뛰어도 됩니다.
3. 사용하는 언어에 따라 대리자의 `signIn:didSignInForUser:withError:` 메서드에 다음을 추가합니다.

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. 또한 다음을 앱 대리자의 `application:didFinishLaunchingWithOptions:`에 추가하여 "SERVER_CLIENT_ID"를 1단계에서 App Service를 구성하는 데 사용된 동일한 ID로 바꿉니다.

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. 사용하는 언어에 따라 다음 코드를 `GIDSignInUIDelegate` 프로토콜을 구현하는 UIViewController의 애플리케이션에 추가합니다.  로그아웃되었다가 다시 로그인되며, 자격 증명을 다시 입력할 필요는 없지만 동의 대화 상자가 표시됩니다.  세션 토큰이 만료된 경우에만 이 메서드를 호출합니다.

   **Objective-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Azure Mobile Apps 빠른 시작]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[동적 스키마]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[패브릭 대시보드]: https://www.fabric.io/home
[iOS용 Fabric - 시작]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
