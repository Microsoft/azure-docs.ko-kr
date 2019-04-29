---
title: App Service Mobile Apps로 관리되는 클라이언트 라이브러리 작업 | Microsoft Docs
description: Windows 및 Xamarin 앱에서 Azure App Service Mobile Apps용 .NET 클라이언트 라이브러리를 사용하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: ''
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 0280785c-e027-4e0d-aaf2-6f155e5a6197
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: crdun
ms.openlocfilehash: 8f014f1cb40e1a629d1989f00805fc91015a3ae9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119306"
---
# <a name="how-to-use-the-managed-client-for-azure-mobile-apps"></a>Azure Mobile Apps에 관리되는 클라이언트를 사용하는 방법
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>개요
이 가이드에서는 Windows 및 Xamarin 앱용 Azure App Service Mobile Apps에 관리되는 클라이언트 라이브러리를 사용하는 일반적인 시나리오를 수행하는 방법을 보여 줍니다. Mobile Apps를 처음 접하는 경우 먼저 [Azure Mobile Apps 빠른 시작][1] 자습서를 완료하는 것이 좋습니다. 이 가이드에서는 클라이언트 쪽 관리되는 SDK에 초점을 둡니다. Mobile Apps에 대한 서버 쪽 SDK에 대해 자세히 알아보려면 [.NET 서버 SDK][2] 또는 [Node.js 서버 SDK][3]에 대한 설명서를 참조하세요.

## <a name="reference-documentation"></a>참조 설명서
클라이언트 SDK에 대한 참조 설명서는 [Azure Mobile Apps .NET 클라이언트 참조][4]에 있습니다.
[Azure 샘플 GitHub 리포지토리][5]에서 몇 가지 클라이언트 샘플을 찾을 수 있습니다.

## <a name="supported-platforms"></a>지원되는 플랫폼
.NET 플랫폼은 다음과 같은 플랫폼을 지원합니다.

* API 19-24(Nougat를 통한 KitKat)용 Xamarin Android 릴리스
* iOS 버전 8.0 이상을 위한 Xamarin iOS 릴리스
* 범용 Windows 플랫폼
* Windows Phone 8.1
* Silverlight 애플리케이션을 제외한 Windows Phone 8.0

"서버-흐름" 인증은 표시된 UI에 웹 보기를 사용합니다.  디바이스가 웹 보기 UI를 표시할 수 없는 경우 다른 인증 방법이 필요합니다.  따라서 이 SDK는 Watch 유형 또는 그와 비슷하게 제한된 디바이스에는 적합하지 않습니다.

## <a name="setup"></a>설정 및 필수 조건
하나 이상의 테이블에 포함된 모바일 앱 백 엔드 프로젝트를 이미 만들고 게시했다고 가정합니다.  이 토픽에 사용되는 코드에서, 테이블은 이름이 `TodoItem`(이)고 `Id`, `Text` 및 `Complete` 열이 있습니다. 이 테이블은 [Azure Mobile Apps 빠른 시작 자습서][1]를 완료할 때 만들었던 것과 동일한 테이블입니다.

C#에서 해당하는 형식화된 클라이언트 쪽 형식은 다음 클래스입니다.

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }
}
```

[JsonPropertyAttribute][6]는 클라이언트 필드와 테이블 필드 간의 *PropertyName* 매핑을 정의하는 데 사용됩니다.

Mobile Apps 백 엔드에서 테이블을 만드는 방법을 알아보려면 [.NET 서버 SDK 토픽][7] 또는 [Node.js 서버 SDK 토픽][8]을 참조하세요. 빠른 시작을 사용하여 Azure Portal에서 Mobile App 백 엔드를 만든 경우 [Azure Portal] 에서 **쉬운 테이블**설정을 사용할 수도 있습니다.

### <a name="how-to-install-the-managed-client-sdk-package"></a>방법: 관리형 클라이언트 SDK 패키지 설치
다음 메서드 중 하나를 사용하여 [NuGet][9]에서 Mobile Apps용 관리되는 클라이언트 SDK 패키지를 설치합니다.

* **Visual Studio**에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭한 다음, `Microsoft.Azure.Mobile.Client` 패키지를 검색하고 **설치**를 클릭합니다.
* **Xamarin Studio** 프로젝트를 마우스 오른쪽 단추로 클릭, 클릭 **추가** > **NuGet 패키지 추가**를 검색 합니다 `Microsoft.Azure.Mobile.Client` 패키지를 찾은 다음 클릭 **패키지 추가** .

기본 활동 파일에 다음 **using** 문을 추가합니다.

```csharp
using Microsoft.WindowsAzure.MobileServices;
```

> [!NOTE]
> Android 프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일해야 합니다. SDK에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

### <a name="symbolsource"></a>방법: Visual Studio에서 디버그 작업
Microsoft.Azure.Mobile 네임스페이스의 기호는 [SymbolSource][10]에 있습니다.  SymbolSource를 Visual Studio와 통합하려면 [SymbolSource 지침][11]을 참조하세요.

## <a name="create-client"></a>Mobile Apps 클라이언트 만들기
다음 코드는 모바일 앱 백 엔드에 액세스하는 데 사용되는 [MobileServiceClient][12] 개체를 만듭니다.

```csharp
var client = new MobileServiceClient("MOBILE_APP_URL");
```

위의 코드에서 `MOBILE_APP_URL` 을 모바일 앱 백 엔드의 URL로 대체하며 이는 [Azure Portal]의 모바일 앱 백 엔드에 대한 블레이드에서 찾을 수 있습니다. MobileServiceClient 개체는 단일 항목이어야 합니다.

## <a name="work-with-tables"></a>테이블 작업
다음 섹션에는 레코드를 검색하고 테이블 내에서 데이터를 수정하는 방법을 자세히 설명합니다.  다음 토픽을 다룹니다.

* [테이블 참조 만들기](#instantiating)
* [쿼리 데이터](#querying)
* [반환된 데이터 필터링](#filtering)
* [반환된 데이터 정렬](#sorting)
* [페이지에서 데이터 반환](#paging)
* [특정 열 선택](#selecting)
* [ID로 레코드 조회](#lookingup)
* [형식화되지 않은 쿼리 처리](#untypedqueries)
* [데이터 삽입](#inserting)
* 데이터 업데이트
* [데이터 삭제](#deleting)
* [충돌 해결 및 낙관적 동시성](#optimisticconcurrency)
* [Windows 사용자 인터페이스에 바인딩](#binding)
* [페이지 크기 변경](#pagesize)

### <a name="instantiating"></a>방법: 테이블 참조 만들기
백 엔드 테이블의 데이터에 액세스하거나 데이터를 수정하는 모든 코드는 `MobileServiceTable` 개체의 함수를 호출합니다. 다음과 같이 [GetTable] 메서드를 호출하여 테이블에 대한 참조를 구합니다.

```csharp
IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
```

반환된 개체는 형식화된 직렬화 모델을 사용합니다. 형식화되지 않은 직렬화 모델도 지원됩니다. 다음 예제는 [형식화되지 않은 테이블에 참조를 만듭니다].

```csharp
// Get an untyped table reference
IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");
```

형식화되지 않은 쿼리에서 기본 OData 쿼리 문자열을 지정해야 합니다.

### <a name="querying"></a>방법: 모바일 앱에서 데이터 쿼리
이 섹션에서는 다음 기능을 비롯하여 모바일 앱 백 엔드에 대한 쿼리를 실행하는 방법을 설명합니다.

* [반환된 데이터 필터링](#filtering)
* [반환된 데이터 정렬](#sorting)
* [페이지에서 데이터 반환](#paging)
* [특정 열 선택](#selecting)
* [ID를 기준으로 데이터 조회](#lookingup)

> [!NOTE]
> 모든 행이 반환되는 것을 방지하기 위해 서버 기반 페이지 크기가 적용됩니다.  페이징은 대규모 데이터 집합에 대한 기본 요청이 서비스에 부정적인 영향을 미치지 않게 방지합니다.  50개가 넘는 행을 반환하려면 [페이지에서 데이터 반환](#paging)에서 설명하는 대로 `Skip` 및 `Take` 메서드를 사용하세요.

### <a name="filtering"></a>방법: 반환된 데이터 필터링
다음 코드는 쿼리에 `Where` 절을 포함하여 데이터를 필터링하는 방법을 보여줍니다. 이 코드에서는 `todoTable`에서 해당 `Complete` 속성이 `false`인 모든 항목을 반환합니다. [Where] 함수는 테이블에 대한 쿼리에 행 필터링 조건자를 적용합니다.

```csharp
// This query filters out completed TodoItems and items without a timestamp.
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToListAsync();
```

브라우저 개발자 도구 또는 [Fiddler]와 같은 메시지 검사 소프트웨어를 사용하여 백 엔드에 전송된 요청의 URI를 볼 수 있습니다. 이 요청 URI에서 알 수 있듯이 쿼리 문자열이 수정됩니다.

```csharp
GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
```

이 OData 요청은 서버 SDK에 의해 SQL 쿼리로 변환됩니다.

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
```

`Where` 메서드에 전달되는 함수는 조건을 임의의 수만큼 가질 수 있습니다.

```csharp
// This query filters out completed TodoItems where Text isn't null
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
    .ToListAsync();
```

이 예는 서버 SDK에 의해 SQL 쿼리로 변환됩니다.

```csharp
SELECT *
    FROM TodoItem
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0
```

이 쿼리는 여러 절로 분할될 수도 있습니다.

```csharp
List<TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .Where(todoItem => todoItem.Text != null)
    .ToListAsync();
```

두 메서드는 동등하며 서로 교환해서 사용할 수 있습니다.  여러 조건자를 하나의 쿼리&mdash;에서 연결하는 첫 번째 옵션&mdash;이 더 간편하며 권장됩니다.

`Where` 절은 OData 하위 집합으로 변환되는 작업을 지원합니다. 작업에는 다음 항목이 포함됩니다.

* 관계형 연산자(==, !=, <, <=, >, >=),
* 산술 연산자(+, -, /, *, %),
* 숫자 정밀도(Math.Floor, Math.Ceiling),
* 문자열 함수(Length, Substring, Replace, IndexOf, StartsWith, EndsWith),
* 날짜 속성(연도, 월, 일, 시, 분, 초),
* 개체의 액세스 속성,
* 이러한 연산자를 결합하는 식.

서버 SDK가 지원하는 것을 고려할 때 [OData v3 설명서]를 고려할 수 있습니다.

### <a name="sorting"></a>방법: 반환된 데이터 정렬
다음 코드는 쿼리에 [OrderBy] 또는 [OrderByDescending] 함수를 포함하여 데이터를 정렬하는 방법을 보여 줍니다. `todoTable`의 항목을 `Text` 필드를 기준으로 오름차순 정렬한 항목을 반환합니다.

```csharp
// Sort items in ascending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderBy(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();

// Sort items in descending order by Text field
MobileServiceTableQuery<TodoItem> query = todoTable
                .OrderByDescending(todoItem => todoItem.Text)
List<TodoItem> items = await query.ToListAsync();
```

### <a name="paging"></a>방법: 페이지에 데이터 반환
기본적으로 백 엔드는 첫 50개 행만 반환합니다. [Take] 메서드를 호출하여 반환 행 수를 늘릴 수 있습니다. `Take` 을(를) [Skip] 메서드와 함께 사용하면 쿼리에서 반환되는 전체 데이터 세트의 특정 "페이지"가 요청됩니다. 다음 쿼리를 실행하면 테이블에서 맨 위에 있는 세 개의 항목을 반환합니다.

```csharp
// Define a filtered query that returns the top 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Take(3);
List<TodoItem> items = await query.ToListAsync();
```

수정된 다음 쿼리는 처음 세 개 결과를 건너뛰고 그 다음 세 개 결과를 반환합니다. 이 쿼리는 두 번째 데이터 "페이지"를 생성하며, 페이지 크기는 세 개 항목입니다.

```csharp
// Define a filtered query that skips the top 3 items and returns the next 3 items.
MobileServiceTableQuery<TodoItem> query = todoTable.Skip(3).Take(3);
List<TodoItem> items = await query.ToListAsync();
```

[IncludeTotalCount] 메서드는 지정된 페이징/제한 절을 무시하고, 반환되었어야 할 *모든* 레코드의 총 개수를 요청합니다.

```csharp
query = query.IncludeTotalCount();
```

실제 앱에서는 Pager 컨트롤이나 그와 비슷한 UI에서 이전 예제와 비슷한 쿼리를 사용하여 페이지를 탐색할 수 있습니다.

> [!NOTE]
> 모바일 앱 백 엔드에서 50행 제한을 재정의하려면 [EnableQueryAttribute] 를 공용 GET 메서드에도 적용하고 페이징 동작을 지정해야 합니다. 메서드에 적용할 때 다음은 최대 반환 행을 1000으로 설정합니다.
>
> `[EnableQuery(MaxTop=1000)]`


### <a name="selecting"></a>방법: 특정 열 선택
쿼리에 [Select] 절을 추가하면 결과에 포함할 속성 집합을 지정할 수 있습니다. 예를 들어 다음 코드는 하나의 필드만 선택하는 방법 및 여러 필드를 선택하고 형식을 지정하는 방법을 보여 줍니다.

```csharp
// Select one field -- just the Text
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => todoItem.Text);
List<string> items = await query.ToListAsync();

// Select multiple fields -- both Complete and Text info
MobileServiceTableQuery<TodoItem> query = todoTable
                .Select(todoItem => string.Format("{0} -- {1}",
                    todoItem.Text.PadRight(30), todoItem.Complete ?
                    "Now complete!" : "Incomplete!"));
List<string> items = await query.ToListAsync();
```

지금까지 설명한 모든 함수는 가산적이므로 계속해서 연결 상태를 유지할 수 있습니다. 연결된 각 호출은 더 많은 쿼리에 영향을 줍니다. 한 가지 예를 더 들면 다음과 같습니다.

```csharp
MobileServiceTableQuery<TodoItem> query = todoTable
                .Where(todoItem => todoItem.Complete == false)
                .Select(todoItem => todoItem.Text)
                .Skip(3).
                .Take(3);
List<string> items = await query.ToListAsync();
```

### <a name="lookingup"></a>방법: ID를 기준으로 데이터 조회
[LookupAsync] 함수를 사용하여 데이터베이스에서 특정 ID를 가진 개체를 조회할 수 있습니다.

```csharp
// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");
```

### <a name="untypedqueries"></a>방법: 형식화되지 않은 쿼리 실행
형식화되지 않은 테이블 개체를 사용하여 쿼리를 실행하는 경우 다음 예제와 같이 [ReadAsync]를 호출하여 OData 쿼리 문자열을 명시적으로 지정해야 합니다.

```csharp
// Lookup untyped data using OData
JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");
```

속성 모음처럼 사용할 수 있는 JSON 값이 반환됩니다. JToken 및 Newtonsoft Json.NET에 대한 자세한 내용은 [Json.NET] 사이트를 참조하세요.

### <a name="inserting"></a>방법: 모바일 앱 백 엔드에 데이터 삽입
모든 클라이언트 형식은 **ID**라는 멤버를 포함해야 하며 이는 기본적으로 문자열입니다. 이 **ID** 는 CRUD 작업 및 오프라인 동기화를 수행하는 데 필요합니다. 다음 코드는 [InsertAsync] 메서드를 사용하여 테이블에 새 행을 삽입하는 방법을 보여 줍니다. 매개 변수에는 .NET 개체로 삽입할 데이터가 포함되어 있습니다.

```csharp
await todoTable.InsertAsync(todoItem);
```

삽입하는 동안 고유한 사용자 지정 ID 값이 `todoItem` 에 포함되어 있지 않으면 서버에서 GUID를 생성합니다.
호출이 반환된 후 개체를 검사하여 생성된 Id를 검색할 수 있습니다.

형식화되지 않은 데이터를 삽입하려는 경우 Json.NET을 활용할 수 있습니다.

```csharp
JObject jo = new JObject();
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

다음은 메일 주소를 고유 문자열 id로 사용하는 예제입니다.

```csharp
JObject jo = new JObject();
jo.Add("id", "myemail@emaildomain.com");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.InsertAsync(jo);
```

### <a name="working-with-id-values"></a>ID 값으로 작업
Mobile Apps는 테이블의 **id** 열에 대한 고유한 사용자 지정 문자열 값을 지원합니다. 문자열 값은 애플리케이션에서 전자 메일 주소 또는 사용자 이름과 같은 사용자 지정 값을 ID에 사용할 수 있습니다.  문자열 ID는 다음과 같은 이점을 제공합니다.

* ID는 데이터베이스에 대한 왕복 없이도 생성됩니다.
* 여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
* 애플리케이션의 논리를 통해 ID 값이 더 효율적으로 통합될 수 있습니다.

문자열 ID 값이 삽입된 레코드에 설정되지 않은 경우 모바일 앱 백 엔드는 해당 ID에 대한 고유한 값을 생성합니다. [Guid.NewGuid] 메서드를 사용하여 클라이언트나 백엔드에서 고유한 ID 값을 생성할 수 있습니다.

```csharp
JObject jo = new JObject();
jo.Add("id", Guid.NewGuid().ToString("N"));
```

### <a name="modifying"></a>방법: 모바일 앱 백 엔드의 데이터 수정
다음 코드는 [UpdateAsync] 메서드를 사용하여 새로운 정보가 포함된 같은 ID로 기존 기록을 업데이트하는 방법을 보여줍니다. 매개 변수에는 .NET 개체로 업데이트할 데이터가 포함되어 있습니다.

```csharp
await todoTable.UpdateAsync(todoItem);
```

형식화되지 않은 데이터를 업데이트하려는 경우 다음과 같이 [Json.NET] 을 활용할 수 있습니다.

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
jo.Add("Text", "Hello World");
jo.Add("Complete", false);
var inserted = await table.UpdateAsync(jo);
```

업데이트할 때 `id` 필드를 지정해야 합니다. 백 엔드는 `id` 필드를 사용하여 업데이트할 행을 식별합니다. `id` 필드는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다. `id` 값을 제공하지 않고 항목을 업데이트하려고 할 때 `ArgumentException`이(가) 발생합니다.

### <a name="deleting"></a>방법: 모바일 앱 백 엔드의 데이터 삭제
다음 코드는 [DeleteAsync] 메서드를 사용하여 기존 인스턴스를 삭제하는 방법을 보여 줍니다. 인스턴스는 `todoItem`에 설정된 `id` 필드로 식별됩니다.

```csharp
await todoTable.DeleteAsync(todoItem);
```

형식화되지 않은 데이터를 삭제하려면 다음과 같이 Json.NET을 이용할 수 있습니다.

```csharp
JObject jo = new JObject();
jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
await table.DeleteAsync(jo);
```

삭제를 요청할 때 ID를 지정해야 합니다. 다른 속성은 서비스에 전달되지 않거나 서비스에서 무시됩니다. `DeleteAsync` 호출의 결과는 일반적으로 `null`입니다. 전달할 ID는 `InsertAsync` 호출의 결과에서 가져올 수 있습니다. `id` 필드를 지정하지 않고 항목을 삭제하려고 할 때 `MobileServiceInvalidOperationException`이(가) 발생합니다.

### <a name="optimisticconcurrency"></a>방법: 충돌 해결에 낙관적 동시성 사용
두 개 이상의 클라이언트가 동시에 동일 항목의 변경 내용을 작성할 수 있습니다. 충돌 검색 없이, 마지막으로 쓴 내용이 이전 업데이트를 덮어씁니다. **낙관적 동시성 제어** 에서는 각 트랜잭션이 커밋할 수 있으므로 리소스 잠금을 사용하지 않는다고 가정합니다.  트랜잭션을 커밋하기 전에 낙관적 동시성 제어는 다른 트랜잭션에서 데이터를 수정하지 않았음을 확인합니다. 데이터가 수정된 경우에는 커밋 중인 트랜잭션이 롤백됩니다.

Mobile Apps는 Mobile App 백 엔드의 각 테이블에 대해 정의된 `version` 시스템 속성 열을 사용하는 각 항목의 변경 내용을 추적하여 낙관적 동시성 제어를 지원합니다. 레코드가 업데이트될 때마다 Mobile Apps는 해당 레코드의 `version` 속성을 새 값으로 설정합니다. 각 업데이트 요청 중에 요청에 포함된 레코드의 `version` 속성이 서버에 있는 레코드의 동일 속성과 비교됩니다. 요청과 함께 전달된 버전이 백 엔드와 일치하지 않는 경우 클라이언트 라이브러리는 `MobileServicePreconditionFailedException<T>` 예외를 발생시킵니다. 예외에 포함된 형식은 백 엔드의 레코드이며 서버 버전의 레코드를 포함하고 있습니다. 그러면 애플리케이션은 이 정보를 사용하여 변경을 커밋하기 위해 백 엔드의 올바른 `version` 값으로 업데이트 요청을 다시 실행할지 여부를 결정할 수 있습니다.

낙관적 동시성을 사용하기 위해 `version` 시스템 속성의 테이블 클래스에 대해 열을 정의합니다. 예를 들면 다음과 같습니다.

```csharp
public class TodoItem
{
    public string Id { get; set; }

    [JsonProperty(PropertyName = "text")]
    public string Text { get; set; }

    [JsonProperty(PropertyName = "complete")]
    public bool Complete { get; set; }

    // *** Enable Optimistic Concurrency *** //
    [JsonProperty(PropertyName = "version")]
    public string Version { set; get; }
}
```

형식화되지 않은 테이블을 사용하는 애플리케이션은 다음과 같이 테이블의 `SystemProperties`에 대해 `Version` 플래그를 설정하여 낙관적 동시성을 사용합니다.

```csharp
//Enable optimistic concurrency by retrieving version
todoTable.SystemProperties |= MobileServiceSystemProperties.Version;
```

낙관적 동시성을 사용하는 것 외에도 [UpdateAsync]를 호출할 때 코드에서 `MobileServicePreconditionFailedException<T>` 예외를 검색해야 합니다.  업데이트된 레코드에 올바른 `version` 을(를) 적용하여 충돌을 해결하고 해결된 레코드로 [UpdateAsync] 를 호출합니다. 다음 코드는 감지된 쓰기 충돌을 해결하는 방법을 보여 줍니다.

```csharp
private async void UpdateToDoItem(TodoItem item)
{
    MobileServicePreconditionFailedException<TodoItem> exception = null;

    try
    {
        //update at the remote table
        await todoTable.UpdateAsync(item);
    }
    catch (MobileServicePreconditionFailedException<TodoItem> writeException)
    {
        exception = writeException;
    }

    if (exception != null)
    {
        // Conflict detected, the item has changed since the last query
        // Resolve the conflict between the local and server item
        await ResolveConflict(item, exception.Item);
    }
}


private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
{
    //Ask user to choose the resolution between versions
    MessageDialog msgDialog = new MessageDialog(
        String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
        serverItem.Text, localItem.Text),
        "CONFLICT DETECTED - Select a resolution:");

    UICommand localBtn = new UICommand("Commit Local Text");
    UICommand ServerBtn = new UICommand("Leave Server Text");
    msgDialog.Commands.Add(localBtn);
    msgDialog.Commands.Add(ServerBtn);

    localBtn.Invoked = async (IUICommand command) =>
    {
        // To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        // catching a MobileServicePreConditionFailedException.
        localItem.Version = serverItem.Version;

        // Updating recursively here just in case another change happened while the user was making a decision
        UpdateToDoItem(localItem);
    };

    ServerBtn.Invoked = async (IUICommand command) =>
    {
        RefreshTodoItems();
    };

    await msgDialog.ShowAsync();
}
```

자세한 내용은 [Azure Mobile Apps에서 오프라인 데이터 동기화] 토픽을 참조하세요.

### <a name="binding"></a>방법: Mobile Apps 데이터를 Windows 사용자 인터페이스에 바인딩
이 섹션에서는 반환된 데이터 개체를 Windows 앱의 UI 요소를 사용해서 표시하는 방법을 보여 줍니다.  다음 예제 코드는 완료되지 않은 항목에 대한 쿼리를 사용하여 목록의 소스에 바인딩합니다. [MobileServiceCollection]은 Mobile Apps 인식 바인딩 컬렉션을 만듭니다.

```csharp
// This query filters out completed TodoItems.
MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
    .Where(todoItem => todoItem.Complete == false)
    .ToCollectionAsync();

// itemsControl is an IEnumerable that could be bound to a UI list control
IEnumerable itemsControl  = items;

// Bind this to a ListBox
ListBox lb = new ListBox();
lb.ItemsSource = items;
```

관리되는 런타임의 일부 컨트롤은 [ISupportIncrementalLoading]이라는 인터페이스를 지원합니다. 이 인터페이스에서는 사용자가 스크롤할 때 컨트롤이 추가 데이터를 요청할 수 있습니다. 컨트롤에서 발생하는 호출을 자동으로 처리하는 [MobileServiceIncrementalLoadingCollection]을 통해 유니버설 Windows 앱용으로 이 인터페이스를 기본적으로 지원합니다. 다음과 같이 Windows 앱에서 `MobileServiceIncrementalLoadingCollection` 을 사용합니다.

```csharp
MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

ListBox lb = new ListBox();
lb.ItemsSource = items;
```

Windows Phone 8 및 "Silverlight" 앱에서 새 컬렉션을 사용하려면 `IMobileServiceTableQuery<T>` 및 `IMobileServiceTable<T>`에서 `ToCollection` 확장 메서드를 사용합니다. 데이터를 로드하려면 `LoadMoreItemsAsync()`를 호출합니다.

```csharp
MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
await items.LoadMoreItemsAsync();
```

`ToCollectionAsync` 또는 `ToCollection`을 호출하여 만들어진 컬렉션을 사용하는 경우 UI 컨트롤에 바인딩할 수 있는 컬렉션을 얻게 됩니다.  이 컬렉션은 페이징을 인식합니다.  이 컬렉션은 네트워크에서 데이터를 로드하므로 가끔 로드가 실패합니다. 이 오류를 처리하려면 `LoadMoreItemsAsync` 호출의 결과로 발생한 예외를 처리하도록 `MobileServiceIncrementalLoadingCollection`에 대한 `OnException` 메서드를 재정의합니다.

테이블에 여러 필드가 있지만 그 중 일부만 컨트롤에 표시하려는 경우를 가정하겠습니다. 앞에서 나온 “[특정 열 선택](#selecting)” 섹션의 지침에 따라 UI에 표시할 특정 열을 선택할 수 있습니다.

### <a name="pagesize"></a>페이지 크기 변경
Azure Mobile Apps는 기본적으로 요청당 최대 50개의 항목을 반환합니다.  클라이언트와 서버 모두에서 최대 페이지 크기를 늘려 페이징 크기를 변경할 수 있습니다.  요청된 페이지 크기를 늘리려면 `PullAsync()`를 사용하는 경우 `PullOptions`를 지정합니다.

```csharp
PullOptions pullOptions = new PullOptions
    {
        MaxPageSize = 100
    };
```

서버 내에서 `PageSize` 를 100보다 크거나 같게 지정했다고 가정할 경우 각 요청에서 최대 100개의 항목을 반환합니다.

## <a name="#offlinesync"></a>오프라인 데이터 작업
오프 라인 테이블은 오프 라인일 때 사용 하기 위해 로컬 SQLite 저장소에서 저장소 데이터를 사용 합니다.  모든 테이블 작업은 원격 서버 저장소 대신 로컬 SQLite 저장소에 대해 수행 됩니다.  오프라인 테이블을 만들려면, 먼저 프로젝트를 준비합니다.

1. Visual Studio에서 솔루션 > **솔루션에 대한 NuGet 패키지 관리...** 를 마우스 오른쪽 단추로 클릭한 후 솔루션의 모든 프로젝트에서 **Microsoft.Azure.Mobile.Client.SQLiteStore** NuGet 패키지를 검색하고 설치합니다.
2. (선택 사항) Windows 디바이스를 지원하려면 다음 SQLite 런타임 패키지 중 하나를 설치합니다.

   * **Windows 8.1 런타임:** [Windows 8.1용 SQLite][3]를 설치합니다.
   * **Windows Phone 8.1:** [Windows Phone 8.1용 SQLite][4]를 설치합니다.
   * **범용 Windows 플랫폼** [범용 Windows용 SQLite][5]를 설치합니다.
3. (선택 사항). Windows 디바이스의 경우, **참조** > **참조 추가...**, **Windows** 폴더 &gt; **확장**을 펼친 후, **Visual C++ 2013 Runtime for Windows** SDK와 함께 해당 **SQLite for Windows** SDK를 사용하도록 설정합니다.
    SQLite SDK 이름은 Windows 플랫폼마다 약간 다릅니다.

테이블 참조를 만들기 전에, 로컬 저장소를 준비해야 합니다.

```csharp
var store = new MobileServiceSQLiteStore(Constants.OfflineDbPath);
store.DefineTable<TodoItem>();

//Initializes the SyncContext using the default IMobileServiceSyncHandler.
await this.client.SyncContext.InitializeAsync(store);
```

클라이언트를 만든 후 저장소 초기화는 일반적으로 즉시 이루어집니다.  **OfflineDbPath**는 모든 플랫폼에서 사용하기에 적합한 이름이어야 합니다.  경로가 정규화 된 경로인 경우 (즉, 슬래시로 시작), 해당 경로를 사용 합니다.  경로가 정규화되지 않은 경우 파일은 플랫폼 특정 위치에 배치됩니다.

* IOS 및 Android 디바이스에 대한 기본 경로는 "개인 파일" 폴더입니다.
* Windows 장치에 대한 기본 경로는 애플리케이션별 "AppData" 폴더입니다.

`GetSyncTable<>` 메서드를 사용하여 테이블 참조를 가져올 수 있습니다.

```csharp
var table = client.GetSyncTable<TodoItem>();
```

오프라인 테이블을 사용하여 인증할 필요가 없습니다.  백 엔드 서비스와 통신하는 경우 인증 해야 합니다.

### <a name="syncoffline"></a>오프라인 테이블 동기화
기본적으로 오프라인 테이블은 백 엔드와 동기화되지 않습니다.  동기화는 두 부분으로 분할됩니다.  새 항목 다운로드에서 별도로 변경 내용을 푸시할 수 있습니다.  다음은 일반적인 동기화 메서드입니다.

```csharp
public async Task SyncAsync()
{
    ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

    try
    {
        await this.client.SyncContext.PushAsync();

        await this.todoTable.PullAsync(
            //The first parameter is a query name that is used internally by the client SDK to implement incremental sync.
            //Use a different query name for each unique query in your program
            "allTodoItems",
            this.todoTable.CreateQuery());
    }
    catch (MobileServicePushFailedException exc)
    {
        if (exc.PushResult != null)
        {
            syncErrors = exc.PushResult.Errors;
        }
    }

    // Simple error/conflict handling. A real application would handle the various errors like network conditions,
    // server conflicts and others via the IMobileServiceSyncHandler.
    if (syncErrors != null)
    {
        foreach (var error in syncErrors)
        {
            if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
            {
                //Update failed, reverting to server's copy.
                await error.CancelAndUpdateItemAsync(error.Result);
            }
            else
            {
                // Discard local change.
                await error.CancelAndDiscardItemAsync();
            }

            Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.", error.TableName, error.Item["id"]);
        }
    }
}
```

`PullAsync`에 대한 첫 번째 인수가 null인 경우, 증분 동기화가 사용되지 않습니다.  각 동기화 작업은 모든 레코드를 가져옵니다.

SDK는 레코드를 끌어오기 전에 암시적 `PushAsync()`을(를) 수행합니다.

`PullAsync()` 메서드에서 충돌 처리가 발생합니다.  온라인 테이블과 같은 방식으로 충돌을 처리할 수 있습니다.  충돌은 삽입, 업데이트 또는 삭제하는 동안 이를 대신하여 `PullAsync()`이 호출될 때 충돌이 생성됩니다. 여러 충돌이 발생하는 경우 단일 MobileServicePushFailedException에 함께 포함됩니다.  각 오류를 개별적으로 처리합니다.

## <a name="#customapi"></a>사용자 지정 API 작업
사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 엔드포인트를 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

클라이언트에서 [InvokeApiAsync] 메서드 중 하나를 호출하여 사용자 지정 API를 호출합니다. 예를 들어 다음 코드 줄은 백 엔드에서 **completeAll** API로 POST 요청을 보냅니다.

```javascript
var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);
```

이 양식은 형식화된 메서드 호출이며 **MarkAllResult** 반환 형식을 정의해야 합니다. 형식화된 메서드와 형식화되지 않은 메서드가 모두 지원됩니다.

InvokeApiAsync() 메소드는 API가 '/'로 시작하는 경우를 제외하고 호출하려는 API 앞에 '/api/'를 추가합니다.
예를 들면 다음과 같습니다.

* `InvokeApiAsync("completeAll",...)` - 백 엔드에서 /api/completeAll 호출
* `InvokeApiAsync("/.auth/me",...)` - 백 엔드에서 /.auth/me 호출

InvokeApiAsync를 사용하여 Azure Mobile Apps로 정의되지 않은 WebAPI를 포함한 WebAPI를 호출할 수 있습니다.  InvokeApiAsync()를 사용할 경우 인증 헤더를 포함한 적합한 헤더가 요청과 함께 전송됩니다.

## <a name="authentication"></a>사용자 인증
Mobile Apps는 다음과 같이 다양한 외부 ID 공급자를 사용하여 앱 사용자의 인증 및 권한 부여를 지원합니다. Facebook, Google, Microsoft 계정, Twitter 및 Azure Active Directory. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다. 자세한 내용은 [앱에 인증 추가]자습서를 참조하세요.

두 가지의 인증 흐름이 지원 됩니다: *클라이언트 관리* 및 *서버 관리* 흐름입니다. 서버 관리 흐름의 경우 공급자의 웹 인증 인터페이스를 사용하므로 인증 경험이 가장 단순합니다. 클라이언트 관리 흐름의 경우 공급자 특정 디바이스별 SDK를 사용하므로 디바이스 특정 기능을 통해 더욱 강력한 통합이 가능합니다.

> [!NOTE]
> 프로덕션 앱에서 클라이언트 관리 흐름을 사용하는 것이 좋습니다.

인증을 설정하려면 하나 이상의 ID 공급자로 앱을 등록해야 합니다.  ID 공급자는 앱의 클라이언트 ID 및 클라이언트 암호를 생성합니다.  그러면 Azure App Service 인증/권한 부여를 사용할 수 있도록 백 엔드에서 이러한 값이 설정됩니다.  자세한 내용은 [앱에 인증 추가]자습서의 자세한 지침을 따르세요.

이 섹션에서 다루는 토픽은 다음과 같습니다.

* [클라이언트 관리 인증](#clientflow)
* [서버 관리 인증](#serverflow)
* [인증 토큰 캐시](#caching)

### <a name="clientflow"></a>클라이언트 관리 인증
앱이 독립적으로 ID 공급자에 연결한 후 반환된 토큰을 백 엔드로 로그인하는 동안 제공할 수도 있습니다. 이 클라이언트 흐름을 사용하면 단일 로그온 환경을 사용자에게 제공하거나 ID 공급자로부터 더 많은 사용자 데이터를 검색할 수 있습니다. 클라이언트 흐름 인증은 ID 공급자 SDK가 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기에 서버 흐름보다 선호도가 높습니다.

다음 클라이언트 흐름 인증 패턴에 대한 예제가 제공됩니다.

* [Active Directory 인증 라이브러리](#adal)
* [Facebook 또는 Google](#client-facebook)

#### <a name="adal"></a>Active Directory 인증 라이브러리를 사용하여 사용자 인증
Azure Active Directory 인증을 사용하여 클라이언트에서 사용자 인증을 시작하려면 Active Directory 인증 라이브러리(ADAL)를 사용할 수 있습니다.

1. 다음으로 [Active Directory 로그인에 대한 App Service를 구성하는 방법] 자습서를 수행하여 AAD 로그인에 모바일 앱 백 엔드를 구성합니다. 네이티브 클라이언트 애플리케이션을 등록하는 선택적 단계를 완료해야 합니다.
2. Visual Studio 또는 Xamarin Studio에서 프로젝트를 열고 `Microsoft.IdentityModel.Clients.ActiveDirectory` NuGet 패키지에 참조를 추가합니다. 검색할 때 시험판 버전을 포함합니다.
3. 사용하는 플랫폼에 따라 애플리케이션에 다음 코드를 추가합니다. 각각에서 다음과 같이 대체합니다.

   * **INSERT-AUTHORITY-HERE**를 애플리케이션이 프로비전된 테넌트의 이름으로 바꿉니다. 형식은 https://login.microsoftonline.com/contoso.onmicrosoft.com이어야 합니다. 이 값은 [Azure Portal]의 Azure Active Directory에 있는 도메인 탭에서 복사할 수 있습니다.
   * **INSERT-RESOURCE-ID-HERE** 를 모바일 앱 백 엔드에 대한 클라이언트 ID로 바꿉니다. 포털의 Azure **Active Directory 설정**에 있는 **고급** 탭에서 클라이언트 ID를 가져올 수 있습니다.
   * **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 애플리케이션에서 복사한 클라이언트 ID로 바꿉니다.
   * HTTPS 체계를 사용하여 **INSERT-REDIRECT-URI-HERE** 를 사이트의 */.auth/login/done* 엔드포인트로 바꿉니다. 이 값은 *https://contoso.azurewebsites.net/.auth/login/done*과 비슷해야 합니다.

     각 플랫폼에 필요한 코드는 다음과 같습니다.

     **Windows:**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                    new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(
                    MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
     }
     ```

     **Xamarin.iOS**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync(UIViewController view)
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
     }
     ```

     **Xamarin.Android**

     ```csharp
     private MobileServiceUser user;
     private async Task AuthenticateAsync()
     {

        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId,
                new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(
                MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
     }
     protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
     {

        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
     }
     ```

#### <a name="client-facebook"></a>Facebook 또는 Google의 토큰을 사용하는 Single Sign-On
다음과 같은 Facebook 또는 Google용 코드 조각에 나온 대로 클라이언트 흐름을 사용할 수 있습니다.

```csharp
var token = new JObject();
// Replace access_token_value with actual value of your access token obtained
// using the Facebook or Google SDK.
token.Add("access_token", "access_token_value");

private MobileServiceUser user;
private async Task AuthenticateAsync()
{
    while (user == null)
    {
        string message;
        try
        {
            // Change MobileServiceAuthenticationProvider.Facebook
            // to MobileServiceAuthenticationProvider.Google if using Google auth.
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
            message = string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="serverflow"></a>서버 관리 인증
ID 공급자를 등록하고 나면, 공급자의 [LoginAsync] 값을 사용하여 MobileServiceClient의 [LoginAsync] 메서드를 호출합니다. 예를 들어 다음 코드는 Facebook을 사용한 서버 흐름 로그인을 시작합니다.

```csharp
private MobileServiceUser user;
private async System.Threading.Tasks.Task Authenticate()
{
    while (user == null)
    {
        string message;
        try
        {
            user = await client
                .LoginAsync(MobileServiceAuthenticationProvider.Facebook);
            message =
                string.Format("You are now logged in - {0}", user.UserId);
        }
        catch (InvalidOperationException)
        {
            message = "You must log in. Login Required";
        }

        var dialog = new MessageDialog(message);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

Facebook 이외의 ID 공급자를 사용하는 경우, [LoginAsync] 값을 공급자에 대한 값으로 변경합니다.

서버 흐름에서 Azure App Service는 선택한 공급자의 로그인 페이지를 표시하여 OAuth 인증 흐름을 관리합니다.  ID 공급자가 결과를 반환하면 Azure App Service가 App Service 인증 토큰을 생성합니다. [LoginAsync] 메서드는 [MobileServiceUser]를 반환하며, 여기서 인증된 사용자의 [UserId] 및 [MobileServiceAuthenticationToken]이 JWT(JSON web token)로 제공됩니다. 이 토큰은 캐시했다가 만료될 때까지 다시 사용할 수 있습니다. 자세한 내용은 [인증 토큰 캐시](#caching)를 참조하십시오.

### <a name="caching"></a>인증 토큰 캐시
경우에 따라 공급자의 인증 토큰을 저장하여 첫 번째 인증 후 login 메서드에 대한 호출을 방지할 수 있습니다.  Microsoft Store 및 UWP 앱은 [PasswordVault] 를 사용하여 다음과 같이 성공적인 로그인 후 현재 인증 토큰을 캐시할 수 있습니다.

```csharp
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook);

PasswordVault vault = new PasswordVault();
vault.Add(new PasswordCredential("Facebook", client.currentUser.UserId,
    client.currentUser.MobileServiceAuthenticationToken));
```

UserId 값은 자격 증명의 사용자 이름으로 저장되며 토큰은 암호로 저장됩니다. 이후 시작 시 캐시된 자격 증명에 대한 **PasswordVault** 를 확인할 수 있습니다. 다음 예제는 검색될 때 캐시된 자격 증명을 사용하고 그렇지 않으면 백 엔드로 인증을 다시 시도합니다.

```csharp
// Try to retrieve stored credentials.
var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
if (creds != null)
{
    // Create the current user from the stored credentials.
    client.currentUser = new MobileServiceUser(creds.UserName);
    client.currentUser.MobileServiceAuthenticationToken =
        vault.Retrieve("Facebook", creds.UserName).Password;
}
else
{
    // Regular login flow and cache the token as shown above.
}
```

사용자를 로그아웃할 때 다음과 같이 저장된 자격 증명도 삭제해야 합니다.

```csharp
client.Logout();
vault.Remove(vault.Retrieve("Facebook", client.currentUser.UserId));
```

Xamarin 앱은 [Xamarin.Auth] API를 사용하여 **Account** 개체에 자격 증명을 안전하게 저장합니다. 이러한 API 사용의 예제는 [ContosoMoments 사진 공유 샘플](https://github.com/azure-appservice-samples/ContosoMoments)에서 [AuthStore.cs] 코드 파일을 참조하세요.

클라이언트 관리 인증을 사용하는 경우 Facebook 또는 Twitter와 같은 공급자로부터 얻은 액세스 토큰을 캐시할 수도 있습니다. 다음과 같이 백 엔드에서 새 인증 토큰을 요청하기 위해 이 토큰을 제공할 수 있습니다.

```csharp
var token = new JObject();
// Replace <your_access_token_value> with actual value of your access token
token.Add("access_token", "<your_access_token_value>");

// Authenticate using the access token.
await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
```

## <a name="pushnotifications"></a>푸시 알림
다음 토픽에서는 푸시 알림에 대해 다룹니다.

* [푸시 알림 등록](#register-for-push)
* [Microsoft Store 패키지 SID 가져오기](#package-sid)
* [플랫폼 간 템플릿을 사용하여 등록](#register-xplat)

### <a name="register-for-push"></a>방법: 푸시 알림 등록
Mobile Apps 클라이언트를 사용하면 Azure Notification Hubs로 푸시 알림을 등록할 수 있습니다. 등록할 때 플랫폼 특정 푸시 알림 서비스(PNS)에서 구하는 핸들을 가져옵니다. 그런 다음 등록을 만들 때 태그와 함께 이 값을 제공합니다. 다음 코드는 Windows 알림 서비스(WNS)를 통한 푸시 알림에 Windows 앱을 등록합니다.

```csharp
private async void InitNotificationsAsync()
{
    // Request a push notification channel.
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // Register for notifications using the new channel.
    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
}
```

WNS에 푸시하는 경우, [Microsoft Store 패키지 SID를 가져와야](#package-sid) 합니다.  템플릿 등록 방법을 비롯하여 Windows 앱에 대한 자세한 내용은 [앱에 푸시 알림 추가]를 참조하세요.

클라이언트에서 태그 요청은 지원되지 않습니다.  태그 요청은 등록에서 자동으로 삭제됩니다.
태그로 디바이스를 등록하려는 경우 사용자를 대신해 Notification Hubs API를 사용하여 등록을 수행하는 사용자 지정 API를 만듭니다.  `RegisterNativeAsync()` 메서드 대신에 사용자 지정 API를 호출합니다.

### <a name="package-sid"></a>방법: Microsoft Store 패키지 SID 가져오기
Microsoft Store 앱에서 푸시 알림 사용에 패키지 SID가 필요합니다.  패키지 SID를 수신하려면 Microsoft Store에 애플리케이션을 등록합니다.

이 값을 가져오려면

1. Visual Studio 솔루션 탐색기에서 Microsoft Store 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고, **스토어** > **스토어와 앱을 연결...** 을 클릭합니다.
2. 마법사에서 **다음**을 클릭하고, Microsoft 계정으로 로그인하고, **새로운 앱 이름 예약**에서 앱 이름을 입력한 후 **예약**을 클릭합니다.
3. 앱을 등록한 후에는 앱 이름을 선택하고 **다음**, **연결**을 차례로 클릭합니다.
4. Microsoft 계정을 사용하여 [Windows 개발자 센터] 에 로그인합니다. **내 앱**에서 방금 만든 앱 등록을 클릭합니다.
5. **앱 관리** > **앱 ID**를 클릭한 다음, 아래로 스크롤하여 **패키지 SID**를 찾습니다.

패키지 SID를 URI로 처리하여 다양하게 사용할 수 있으며, 이 경우 *ms-app://* 를 스키마로 사용해야 합니다. 이 값을 접두사로 연결하여 만든 패키지 SID의 버전을 기록합니다.

iOS 또는 Android 플랫폼에서 실행되는 앱을 등록하려면 Xamarin 앱에 추가 코드가 필요합니다. 자세한 내용은 사용하는 플랫폼에 대한 토픽을 참조하세요.

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push-notifications-to-your-app)

### <a name="register-xplat"></a>방법: 플랫폼 간 알림을 보내기 위해 푸시 템플릿 등록
템플릿을 등록하려면 다음과 같이 템플릿으로 `RegisterAsync()` 메서드를 사용합니다.

```csharp
JObject templates = myTemplates();
MobileService.GetPush().RegisterAsync(channel.Uri, templates);
```

템플릿은 `JObject` 형식이어야 하며 다음 JSON 형식으로 여러 템플릿을 포함할 수 있습니다.

```csharp
public JObject myTemplates()
{
    // single template for Windows Notification Service toast
    var template = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(message)</text></binding></visual></toast>";

    var templates = new JObject
    {
        ["generic-message"] = new JObject
        {
            ["body"] = template,
            ["headers"] = new JObject
            {
                ["X-WNS-Type"] = "wns/toast"
            },
            ["tags"] = new JArray()
        },
        ["more-templates"] = new JObject {...}
    };
    return templates;
}
```

또한 **RegisterAsync()** 메서드는 보조 타일을 허용할 수 있습니다.

```csharp
MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);
```

보안을 위해 등록하는 동안 모든 태그가 제거됩니다. 설치에 태그를 추가하거나 설치 내에 템플릿을 추가하려면 [Azure Mobile Apps에 대해 .NET 백 엔드 서버 SDK로 작업]을 참조하세요.

이러한 등록된 템플릿을 활용하여 알림을 보내려면 [Notification Hubs API]를 참조하세요.

## <a name="misc"></a>기타 토픽
### <a name="errors"></a>방법: 오류 처리
백 엔드에서 오류가 발생하는 경우 클라이언트 SDK가 `MobileServiceInvalidOperationException`을 발생시킵니다.  다음 예제에서는 백 엔드에서 반환되는 예외를 처리하는 방법을 보여 줍니다.

```csharp
private async void InsertTodoItem(TodoItem todoItem)
{
    // This code inserts a new TodoItem into the database. When the operation completes
    // and App Service has assigned an Id, the item is added to the CollectionView
    try
    {
        await todoTable.InsertAsync(todoItem);
        items.Add(todoItem);
    }
    catch (MobileServiceInvalidOperationException e)
    {
        // Handle error
    }
}
```

오류 조건을 처리하는 또 다른 예는 [Mobile Apps 파일 샘플]에서 찾을 수 있습니다. [LoggingHandler] 예제는 백 엔드에 대해 생성되는 요청을 기록하는 로깅 대리자 처리기를 제공합니다.

### <a name="headers"></a>방법: 요청 헤더 사용자 지정
특정 앱 시나리오를 지원하려면 모바일 앱 백 엔드와의 통신을 사용자 지정해야 할 수 있습니다. 예를들어, 모든 보내는 요청이나 변경 응답 상태 코드에 사용자 지정 헤더를 추가하고자 할 수 있습니다. 다음 예제와 같이 사용자 지정 [DelegatingHandler]를 사용할 수 있습니다.

```csharp
public async Task CallClientWithHandler()
{
    MobileServiceClient client = new MobileServiceClient("AppUrl", new MyHandler());
    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
    var newItem = new TodoItem { Text = "Hello world", Complete = false };
    await todoTable.InsertAsync(newItem);
}

public class MyHandler : DelegatingHandler
{
    protected override async Task<HttpResponseMessage>
        SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
    {
        // Change the request-side here based on the HttpRequestMessage
        request.Headers.Add("x-my-header", "my value");

        // Do the request
        var response = await base.SendAsync(request, cancellationToken);

        // Change the response-side here based on the HttpResponseMessage

        // Return the modified response
        return response;
    }
}
```


<!-- Anchors. -->


<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-windows-store-dotnet-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[4]: https://msdn.microsoft.com/library/azure/mt419521(v=azure.10).aspx
[5]: https://github.com/Azure-Samples
[6]: https://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[7]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[8]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[9]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/
[10]: https://github.com/SymbolSource/SymbolSource
[11]: http://www.symbolsource.org/Public/Wiki/Using
[12]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx

[앱에 인증 추가]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Azure Mobile Apps에서 오프라인 데이터 동기화]: app-service-mobile-offline-data-sync.md
[앱에 푸시 알림 추가]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Register your app to use a Microsoft account login]: ../app-service/configure-authentication-provider-microsoft.md
[Active Directory 로그인에 대한 App Service를 구성하는 방법]: ../app-service/configure-authentication-provider-aad.md

<!-- Microsoft URLs. -->
[MobileServiceCollection]: https://msdn.microsoft.com/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/library/azure/dn268408(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/library/azure/jj554275(v=azure.10).aspx
[형식화되지 않은 테이블에 참조를 만듭니다]: https://msdn.microsoft.com/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/library/azure/mt691741(v=azure.10).aspx
[take]: https://msdn.microsoft.com/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/library/azure/dn250569(v=azure.10).aspx
[skip]: https://msdn.microsoft.com/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/library/azure/dn250536.(v=azure.10)aspx
[UserID]: https://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/library/azure/dn250579(v=azure.10).aspx
[Azure Portal]: https://portal.azure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: https://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows 개발자 센터]: https://dev.windows.com/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[PasswordVault]: https://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: https://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs API]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps 파일 샘플]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63

<!-- External URLs -->
[OData v3 설명서]: https://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: https://www.telerik.com/fiddler
[Json.NET]: https://www.newtonsoft.com/json
[Xamarin.Auth]: https://components.xamarin.com/view/xamarin.auth/
[AuthStore.cs]: https://github.com/azure-appservice-samples/ContosoMoments
[ContosoMoments photo sharing sample]: https://github.com/azure-appservice-samples/ContosoMoments
