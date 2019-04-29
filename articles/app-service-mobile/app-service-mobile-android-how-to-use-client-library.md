---
title: Android용 Azure Mobile Apps SDK를 사용하는 방법 | Microsoft Docs
description: Android용 Azure Mobile Apps SDK를 사용하는 방법
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/07/2019
ms.author: crdun
ms.openlocfilehash: 45b5ac0c9b3535e5cc5efdc6827d694b41e0b8dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859395"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Android용 Azure Mobile Apps SDK를 사용하는 방법

이 가이드에서는 Mobile Apps용 Android 클라이언트 SDK를 사용하여 다음과 같은 일반적인 시나리오를 구현하는 방법을 보여줍니다.

* 데이터 쿼리(삽입, 업데이트 및 삭제).
* 인증.
* 오류 처리.
* 클라이언트 사용자 지정.

이 가이드는 클라이언트 쪽 Android SDK에 중점을 둡니다.  Mobile Apps용 서버 쪽 SDK에 대해 자세히 알아보려면 [.NET 백 엔드 SDK로 작업][10] 또는 [Node.js 백 엔드 SDK를 사용하는 방법][11]을 참조하세요.

## <a name="reference-documentation"></a>참조 설명서

Android 클라이언트 라이브러리용 [Javadocs API 참조][12]는 GitHub에서 확인할 수 있습니다.

## <a name="supported-platforms"></a>지원되는 플랫폼

Android용 Azure Mobile Apps SDK는 휴대폰 및 태블릿 폼 팩터용 API 레벨 19 ~24(KitKat ~ Nougat)를 지원합니다.  특히 인증은 일반적인 웹 프레임워크 방식을 활용하여 자격 증명을 수집합니다.  서버 흐름 인증은 시계와 같은 소형 폼 팩터 디바이스에서는 작동하지 않습니다.

## <a name="setup-and-prerequisites"></a>설정 및 필수 조건

[Mobile Apps 빠른 시작](app-service-mobile-android-get-started.md) 자습서를 완료합니다.  자습서를 통해 Azure Mobile Apps를 개발하기 위한 모든 필수 구성 요소를 충족할 수 있습니다.  또한 빠른 시작 자습서를 참조하여 계정을 구성하고 첫 번째 모바일 앱 백 엔드를 만들 수 있습니다.

빠른 시작 자습서를 완료하지 않으려는 경우에는 다음 작업을 완료하세요.

* Android 앱과 함께 사용할 [모바일 앱 백 엔드를 만듭니다][13].
* Android Studio에서 [Gradle 빌드 파일을 업데이트](#gradle-build)합니다.
* [인터넷 권한을 사용합니다](#enable-internet).

### <a name="gradle-build"></a>Gradle 빌드 파일 업데이트

**build.gradle** 파일을 모두 변경합니다.

1. 이 코드를 추가 합니다 *프로젝트* 수준 **build.gradle** 파일:

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. *dependencies* 태그 내의 *모듈 앱* 수준 **build.gradle** 파일에 이 코드를 추가합니다.

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    현재 최신 버전은 3.4.0입니다. 지원되는 버전은 [bintray][14]에 나열되어 있습니다.

### <a name="enable-internet"></a>인터넷 권한 사용

Azure에 액세스하려면 앱은 인터넷 사용 권한을 사용하도록 설정해야 합니다. 아직 사용하지 않는 경우 코드의 다음 줄을 **AndroidManifest.xml** 파일에 추가합니다.

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>클라이언트 연결 만들기

Azure Mobile Apps는 모바일 애플리케이션에 네 가지 함수를 제공합니다.

* Azure Mobile Apps Service를 통한 데이터 액세스 및 오프라인 동기화.
* Azure Mobile Apps 서버 SDK로 작성된 사용자 지정 API 호출.
* Azure App Service 인증 및 권한 부여를 통한 인증.
* Notification Hubs를 통한 푸시 알림 등록.

이러한 각각의 함수는 먼저 `MobileServiceClient` 개체를 생성해야 합니다.  모바일 클라이언트 내에 하나의 `MobileServiceClient` 개체만 만들어야 합니다(즉 Singleton 패턴이어야 합니다).  `MobileServiceClient` 개체를 만들려면:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>`은 모바일 백 엔드를 가리키는 문자열 또는 URL 개체입니다.  Azure App Service를 사용하여 모바일 백 엔드를 호스트하는 경우 보안 `https://` 버전의 URL을 사용해야 합니다.

클라이언트는 활동 또는 컨텍스트(예제의 `this` 매개 변수)에 대한 액세스도 필요합니다.  MobileServiceClient 생성은 `AndroidManifest.xml` 파일에 언급된 활동의 `onCreate()` 메서드 내에서 수행되어야 합니다.

가장 좋은 방법은 서버 통신을 자체(singleton 패턴) 클래스로 추상화하는 것입니다.  이런 경우 서비스를 적절히 구성하려면 활동을 생성자 내에서 전달해야 합니다.  예를 들면 다음과 같습니다.

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

이제 기본 활동의 `onCreate()` 메서드에서 `AzureServiceAdapter.Initialize(this);`를 호출할 수 있습니다.  클라이언트에 대한 액세스가 필요한 다른 모든 메서드는 `AzureServiceAdapter.getInstance();`를 사용하여 서비스 어댑터에 대한 참조를 확보합니다.

## <a name="data-operations"></a>데이터 작업

Azure Mobile Apps SDK의 핵심은 Mobile App 백 엔드의 SQL Azure 내에 저장된 데이터에 대한 액세스를 제공하는 것입니다.  이러한 데이터는 강력한 형식의 클래스(권장됨) 또는 형식화되지 않은 쿼리(권장하지 않음)를 사용하여 액세스할 수 있습니다.  이 섹션의 대부분은 강력한 형식의 클래스 사용을 설명합니다.

### <a name="define-client-data-classes"></a>클라이언트 데이터 클래스 정의

SQL Azure 테이블의 데이터에 액세스하려면 모바일 앱 백 엔드에 있는 테이블에 해당하는 클라이언트 데이터 클래스를 정의합니다. 이 항목의 예에서는 이름이 **MyDataTable**이고 다음 열이 포함된 테이블을 가정합니다.

* id
* text
* complete

해당하는 형식의 클라이언트 쪽 개체는 **MyDataTable.java**라는 파일에 있습니다.

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

추가하는 각 필드에 getter 및 setter 메서드를 추가합니다.  SQL Azure 테이블이 더 많은 열을 포함하는 경우 이 클래스에 해당하는 필드를 추가합니다.  예를 들어 DTO(데이터 전송 개체)에 정수 우선 순위 열이 있다면 getter 및 setter 메서드와 함께 이 필드를 추가할 수 있습니다.

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Mobile Apps 백 엔드에 추가 테이블을 만드는 방법을 알아보려면 [방법: 테이블 컨트롤러 정의][15](.NET 백 엔드) 또는 [동적 스키마를 사용하여 테이블 정의][16](Node.js 백 엔드)를 참조하세요.

Azure Mobile Apps 백 엔드 테이블은 5개의 특수 필드를 정의하며 이 중 4개는 클라이언트에서 사용할 수 있습니다.

* `String id`: 레코드의 전역 고유 ID입니다.  가장 좋은 방법은 ID를 [UUID][17] 개체의 문자열 표현으로 만드는 것입니다.
* `DateTimeOffset updatedAt`: 마지막 업데이트 날짜/시간입니다.  updatedAt 필드는 서버에 의해 설정되며 클라이언트 코드로 설정하지 말아야 합니다.
* `DateTimeOffset createdAt`: 개체가 만들어진 날짜/시간입니다.  createdAt 필드는 서버에 의해 설정되며 클라이언트 코드로 설정하지 말아야 합니다.
* `byte[] version`: 일반적으로 문자열로 표시되며 버전도 서버에 의해 설정됩니다.
* `boolean deleted`: 레코드가 삭제되었지만 아직 제거되지 않았음을 나타냅니다.  `deleted`를 클래스에서 속성으로 사용하지 마십시오.

`id` 필드는 필수입니다.  `updatedAt` 필드 및 `version` 필드는 오프라인 동기화에(각각 증분 동기화 및 충돌 해결을 위해) 사용됩니다.  `createdAt` 필드는 참조 필드이며 클라이언트에서 사용되지 않습니다.  이름은 속성의 "across-the-wire" 이름이며 조정할 수 없습니다.  하지만 [gson][3] 라이브러리를 사용하여 개체와 "across-the-wire" 이름 사이에 매핑을 만들 수 있습니다.  예를 들면 다음과 같습니다.

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>테이블 참조 만들기

테이블에 액세스하려면 우선 [MobileServiceClient][9]에서 **getTable** 메서드를 호출하여 [MobileServiceTable][8] 개체를 만듭니다.  이 메서드에는 두 가지 오버로드가 있습니다.

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

다음 코드에서 **mClient** 는 MobileServiceClient 개체에 대한 참조입니다.  첫 번째 오버로드는 클래스 이름과 테이블 이름이 같을 때 사용하며 빠른 시작에서 사용되는 것입니다.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

두 번째 오버로드는 테이블 이름이 형식 이름과 다를 때 사용하고 첫 번째 매개 변수는 테이블 이름입니다.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a> 엔드 테이블 쿼리

먼저 테이블 참조를 가져옵니다.  그런 다음 테이블 참조에 쿼리를 실행합니다.  쿼리는 다음의 조합입니다.

* `.where()` [필터 절](#filtering).
* `.orderBy()` [ordering 절](#sorting).
* `.select()` [필드 선택 절](#selection).
* [페이징 결과](#paging)에 대한 `.skip()` 및 `.top()`.

절은 선행 순으로 제공해야 합니다.

### <a name="filter"></a> 결과 필터링

쿼리의 일반적인 형식은 다음과 같습니다.

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

앞의 예제는 모든 결과(서버가 설정한 최대 페이지 크기까지)를 반환합니다.  `.execute()` 메서드는 백 엔드에서 쿼리를 실행합니다.  쿼리는 Mobile Apps 백 엔드로 전송되기 전에 [OData v3][19] 쿼리로 변환됩니다.  수신되면 Mobile Apps 백 엔드는 SQL Azure 인스턴스에서 쿼리를 실행하기 전에 SQL 문으로 변환합니다.  네트워크 작업에 시간이 걸리므로 `.execute()` 메서드는 [`ListenableFuture<E>`][18]를 반환합니다.

### <a name="filtering"></a>반환된 데이터 필터링

다음 쿼리 실행은 **완료**가 **false**와 같은 **ToDoItem** 테이블에서 모든 항목을 반환합니다.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable**은 이전에 만든 모바일 서비스 테이블에 대한 참조입니다.

테이블 참조에 대한 **where** 메서드 호출을 사용하여 필터를 정의합니다. **where** 메서드 뒤에는 **field** 메서드를 사용하고 그 뒤에 논리 조건자를 지정하는 메서드를 사용합니다. 가능한 조건자 메서드에는 **eq**(같음), **ne**(같지 않음), **gt**(보다 큼), **ge**(보다 크거나 같음), **lt**(보다 작음), **le**(작거나 같음)가 포함됩니다. 이러한 메서드를 사용하면 숫자 및 문자열 필드를 특정 값과 비교할 수 있습니다.

날짜를 기준으로 필터링할 수 있습니다. 다음 메서드를 사용하면 전체 날짜 필드 또는 날짜의 부분을 비교할 수 있습니다. **년**, **월**, **일**, **시간**, **분** 및 **초**. 다음 예제는 *기한*이 2013과 같은 항목에 해당하는 필터를 추가합니다.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

**startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim**, **length**와 같은 메서드는 문자열 필드에 복합 필터를 지원합니다. 다음 예제는 *텍스트* 열이 "PRI0"으로 시작되는 테이블 행을 필터링합니다.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

연산자 메서드, **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** 및 **round**는 숫자 필드에 지원됩니다. 다음 예제는 **기간**이 짝수인 테이블 행을 필터링합니다.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

조건자를 **and**, **or**, **not** 등의 논리 메서드와 결합할 수 있습니다. 다음 예제는 앞의 예 중 두 가지를 결합합니다.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

그룹 및 중첩 논리 연산자:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

필터링에 대한 자세한 내용과 예를 보려면 [Android 클라이언트 쿼리 모델의 다양한 기능 알아보기][20]를 참조하세요.

### <a name="sorting"></a>반환된 데이터 정렬

다음 코드는 **ToDoItems** 테이블에서 *텍스트* 필드를 기준으로 오름차순 정렬된 모든 항목을 반환합니다. *mToDoTable* 은 이전에 만든 백 엔드 테이블에 대한 참조입니다.

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

**orderBy** 메서드의 첫 번째 매개 변수는 정렬 기준이 되는 필드의 이름과 동일한 문자열입니다. 두 번째 매개 변수는 **QueryOrder** 열거형을 사용하여 오름차순이나 내림차순으로 정렬할지를 지정합니다.  ***where*** 메서드를 사용하여 필터링하려는 경우 ***orderBy*** 메서드 이전에 ***where*** 메서드를 호출해야 합니다.

### <a name="selection"></a>특정 열 선택

다음 코드는 **ToDoItems** 테이블에서 모든 항목을 반환하지만 **완료** 및 **텍스트** 필드만 표시하는 방법을 보여 줍니다.  **mToDoTable** 은 이전에 만든 백 엔드 테이블에 대한 참조입니다.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

select 함수의 매개 변수는 반환하려는 테이블 열의 문자열 이름입니다.  **select** 메서드는 **where**나 **orderBy** 같은 메서드의 뒤에 나와야 합니다. 그 뒤에 **skip** 및 **top**등의 메서드를 페이징하여 나올 수 있습니다.

### <a name="paging"></a>페이지에 데이터 반환

데이터는 **항상** 페이지에 반환됩니다.  반환되는 레코드의 최대 수는 서버에 의해 설정됩니다.  클라이언트가 더 많은 레코드를 요청하면 서버는 최대 레코드 수를 반환합니다.  기본적으로 서버의 최대 페이지 크기는 50개 레코드입니다.

첫 번째 예는 테이블에서 상위 5개 항목을 선택하는 방법을 보여 줍니다. 쿼리는 **ToDoItems**테이블에서 항목을 반환합니다. **mToDoTable** 은 이전에 만든 백 엔드 테이블에 대한 참조입니다.

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

다음은 첫 5개 항목을 건너뛴 후 다음 5개를 반환하는 쿼리입니다.

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

하나의 테이블에 모든 레코드를 가져오려면 모든 페이지에서 반복하는 코드를 구현합니다.

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

이 메서드를 사용하여 모든 레코드를 요청하면 Mobile Apps 백 엔드에 대한 요청이 최소 두 개 생성됩니다.

> [!TIP]
> 올바른 페이지 크기를 선택하면 요청이 발생하는 동안의 메모리 사용량과 데이터를 완전히 수신하는 데 걸리는 시간 및 대역폭 사용량 사이에 균형이 유지됩니다.  기본값(레코드 50개)은 모든 디바이스에 적합합니다.  대형 메모리 디바이스에서 독점적으로 작동하는 경우 최대 500개까지 늘립니다.  페이지 크기를 레코드 500개를 초과하도록 늘리면 허용되지 않는 지연과 큰 메모리 문제가 발생합니다.

### <a name="chaining"></a>방법: 쿼리 메서드 연결

백 엔드 테이블을 쿼리하는 데 사용되는 메서드를 연결할 수 있습니다. 쿼리 메서드를 연결하면 정렬 및 페이징되는 필터링된 행의 특정 열을 선택할 수 있습니다. 상당히 복잡한 논리 필터를 만들 수 있습니다.  각 쿼리 메서드는 쿼리 개체를 반환합니다. 일련의 메서드를 종료하고 실제로 쿼리를 실행하려면 **execute** 메서드를 호출합니다. 예를 들면 다음과 같습니다.

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

연결된 쿼리 메서드의 순서는 다음과 같아야 합니다.

1. 필터링(**where**) 메서드.
2. 정렬(**orderBy**) 메서드.
3. 선택(**select**) 메서드.
4. 페이징(**skip** 및 **top**) 메서드.

## <a name="binding"></a>사용자 인터페이스에 데이터 바인딩

데이터 바인딩에는 세 가지 구성 요소가 필요합니다.

* 데이터 원본
* 화면 레이아웃
* 두 요소를 연결하는 어댑터

샘플 코드에서는 Mobile Apps SQL Azure 테이블 **ToDoItem** 의 데이터를 배열로 반환합니다. 이 활동은 데이터 애플리케이션에서 매우 흔한 패턴입니다.  데이터베이스 쿼리는 종종 클라이언트에서 목록 또는 배열로 가져오는 행 컬렉션을 반환합니다. 이 샘플에서 배열은 데이터 원본입니다.  코드는 디바이스에 나타나는 데이터 뷰를 정의하는 화면 레이아웃을 지정합니다.  이 두 요소는 어댑터(이 코드에서 **ArrayAdapter&lt;ToDoItem&gt;** 클래스의 확장)를 통해 바인딩됩니다.

#### <a name="layout"></a>레이아웃 정의

레이아웃은 다수의 XML 코드 조각으로 정의됩니다. 기존 레이아웃을 고려할 때 다음 코드는 서버 데이터로 채울 **ListView**를 나타냅니다.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

위의 코드에서 *listitem* 특성은 목록의 개별 행에 대한 레이아웃의 ID를 지정합니다. 이 코드는 확인란 및 관련된 텍스트를 지정하고 목록의 각 항목에 대해 한 번씩 인스턴스화됩니다. 이 레이아웃은 **ID** 필드를 표시하지 않고 더 복잡한 레이아웃은 디스플레이에서 추가 필드를 지정합니다. 이 코드는 **row_list_to_do.xml** 파일에 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>어댑터 정의
이 뷰의 데이터 원본은 **ToDoItem**의 배열이기 때문에, **ArrayAdapter&lt;ToDoItem&gt;** 클래스에서 어댑터의 서브클래스를 지정합니다. 이 서브클래스는 **row_list_to_do** 레이아웃을 사용하는 모든 **ToDoItem**의 뷰를 생성합니다.  코드에는 **ArrayAdapter&lt;E&gt;** 클래스의 확장인 다음 클래스를 정의합니다.

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

어댑터의 **getView** 메서드를 다시 정의합니다. 예를 들면 다음과 같습니다.

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

작업에서 다음과 같이 이 클래스의 인스턴스를 만듭니다.

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

ToDoItemAdapter 생성자의 두 번째 매개 변수는 레이아웃에 대한 참조입니다. 이제 **ListView**를 인스턴스화하고 **ListView**에 어댑터를 할당할 수 있습니다.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>어댑터를 사용하여 UI에 바인딩

이제 데이터 바인딩을 사용할 준비가 되었습니다. 다음 코드는 테이블의 항목을 가져오고 로컬 어댑터를 반환된 항목으로 채우는 방법을 보여줍니다.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

**ToDoItem** 테이블을 수정할 때 언제든지 어댑터를 호출하세요. 수정은 레코드별로 이루어지기 때문에 사용자는 컬렉션이 아닌 단일 행을 다루게 됩니다. 항목을 삽입할 때는 어댑터에 대해 **add** 메서드를 호출하고, 삭제할 때는 **remove** 메서드를 호출합니다.

전체 예제는 [Android 빠른 시작 프로젝트][21]에서 찾을 수 있습니다.

## <a name="inserting"></a>백 엔드에 데이터 삽입

*ToDoItem* 클래스 인스턴스를 인스턴스화하고 해당 속성을 설정합니다.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

그런 다음 **insert()** 를 사용하여 개체를 삽입합니다.

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

반환된 엔터티는 백 엔드 테이블에 삽입된 데이터와 일치하며 이는 ID 및 백 엔드에 설정된 다른 값(예: `createdAt`, `updatedAt` 및 `version` 필드)을 포함합니다.

Mobile Apps 테이블에는 **id**라고 하는 기본 키 열이 필요합니다. 이 열은 문자열이어야 합니다. ID 열의 기본값은 GUID입니다.  전자 메일 주소나 사용자 이름처럼 다른 고유한 값을 입력해도 됩니다. 삽입된 레코드에 대한 문자열 ID 값을 입력하지 않으면 백 엔드에서 새 GUID를 생성합니다.

문자열 ID 값은 다음과 같은 이점을 제공합니다.

* 데이터베이스에 대한 왕복 없이도 ID를 생성할 수 있습니다.
* 여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
* 애플리케이션의 논리를 통해 ID 값이 더 효율적으로 통합됩니다.

오프라인 동기화를 지원하려면 문자열 ID 값이 **필수** 입니다.  ID가 백 엔드 데이터베이스에 저장되고 나면 ID를 변경할 수 없습니다.

## <a name="updating"></a>모바일 앱의 데이터 업데이트

테이블의 데이터를 업데이트하려면 새 개체를 **update()** 메서드에 전달합니다.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

이 예제에서 *item*은 일부 변경된 *ToDoItem* 테이블의 행에 대한 참조입니다.  **id** 가 같은 행이 업데이트됩니다.

## <a name="deleting"></a>모바일 앱의 데이터 삭제

다음 코드는 데이터 개체를 지정하여 테이블에서 데이터를 삭제하는 방법을 보여줍니다.

```java
mToDoTable
    .delete(item);
```

또한 삭제할 행의 **ID** 필드를 지정하여 항목을 삭제할 수 있습니다.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>ID로 특정 항목 조회

**lookUp()** 메서드를 사용하여 특정 **id** 필드 값을 가진 항목을 조회합니다.

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>방법: 형식화되지 않은 데이터 작업

형식화되지 않은 프로그래밍 모델은 JSON 직렬화를 정확하게 제어할 수 있습니다.  형식화되지 않은 프로그래밍 모델을 사용하는 것이 좋은 몇 가지 일반적인 시나리오가 있습니다. 백 엔드 테이블에 여러 열이 있는데 그 중 일부만 참조해야 하는 경우를 예로 들 수 있습니다.  형식화된 모델을 사용하려면 데이터 클래스의 Mobile Apps 백 엔드에 정의된 모든 열을 정의해야 합니다.  데이터에 액세스하는 대부분의 API 호출은 형식화된 프로그래밍 호출과 유사합니다. 주요 차이점은 형식화되지 않은 모델에서는 **MobileServiceTable** 개체 대신 **MobileServiceJsonTable** 개체에 대해 메서드를 호출한다는 점입니다.

### <a name="json_instance"></a>형식화되지 않은 테이블 인스턴스 만들기

형식화된 모델과 유사하게 테이블 참조를 가져와서 시작합니다. 하지만 이 경우에는 이 참조가 **MobileServicesJsonTable** 개체입니다. 클라이언트의 인스턴스에 대해 **getTable** 메서드를 호출하여 참조를 가져옵니다.

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

**MobileServiceJsonTable**의 인스턴스를 만들면 형식화된 프로그래밍 모델을 사용할 때와 거의 동일한 API를 사용할 수 있습니다. 경우에 따라 메서드가 형식화된 매개 변수 대신 형식화되지 않은 매개 변수를 가져오기도 합니다.

### <a name="json_insert"></a>형식화되지 않은 테이블에 삽입
다음 코드는 삽입하는 방법을 보여 줍니다. 첫 번째 단계는 [gson][3] 라이브러리의 일부인 [JsonObject][1]를 만드는 것입니다.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

그런 다음 **insert()** 를 사용하여 형식화되지 않은 개체를 테이블에 삽입합니다.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

삽입된 개체의 ID를 가져와야 하는 경우 **getAsJsonPrimitive()** 메서드를 사용합니다.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>형식화되지 않은 테이블에서 삭제
다음 코드는 인스턴스를 삭제하는 방법을 보여 줍니다. 이 경우에는 앞의 **insert** 예에서 만들어진 것과 동일한 *JsonObject* 인스턴스를 삭제합니다. 코드는 형식화된 경우와 같지만 메서드가 **JsonObject**를 참조하기 때문에 메서드의 서명이 다릅니다.

```java
mToDoTable
    .delete(insertedItem);
```

ID를 사용하여 직접 인스턴스를 삭제할 수도 있습니다.

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>형식화되지 않은 테이블에서 모든 행 반환
다음 코드는 전체 테이블을 검색하는 방법을 보여 줍니다. Json 테이블을 사용하기 때문에 테이블 열의 일부만 선택적으로 검색할 수 있습니다.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

형식화된 모델에 제공되는 것과 동일한 필터링 및 페이징 메서드가 형식화되지 않은 모델에 제공됩니다.

## <a name="offline-sync"></a>오프라인 동기화 구현

Azure Mobile Apps 클라이언트 SDK는 SQLite 데이터베이스를 사용하여 서버 데이터의 복사본을 로컬에 저장하여 데이터의 오프라인 동기화를 구현합니다.  오프라인 테이블에 수행되는 작업에는 모바일 연결이 필요하지 않습니다.  오프라인 동기화는 충돌 해결을 위해보다 복잡한 논리를 사용하는 대신 복원력과 성능을 향상시킵니다.  Azure Mobile Apps 클라이언트 SDK는 다음 기능을 구현합니다.

* 증분 동기화: 업데이트된 레코드와 새 레코드만 다운로드되기 때문에 대역폭과 메모리 사용량이 절약됩니다.
* 낙관적 동시성: 작업이 성공한 것으로 간주됩니다.  충돌 해결은 서버에서 업데이트가 수행될 때까지 지연됩니다.
* 충돌 해결: SDK는 서버에서 충돌하는 변경이 발생하면 이를 탐지하고 사용자에게 경고하기 위해 후크를 제공합니다.
* 일시 삭제: 삭제된 레코드가 삭제된 것으로 표시되기 때문에 다른 디바이스가 오프라인 캐시를 업데이트할 수 있습니다.

### <a name="initialize-offline-sync"></a>오프라인 동기화 초기화

각 오프라인 테이블은 사용 전에 오프라인 캐시에 정의되어야 합니다.  일반적으로 테이블 정의는 클라이언트 생성 직후에 수행됩니다.

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>오프라인 캐시 테이블에 대한 참조 얻기

온라인 테이블의 경우 `.getTable()`을 사용합니다.  오프라인 테이블의 경우 `.getSyncTable()`을 사용합니다.

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

온라인 테이블에서 사용할 수 있는 모든 메서드(필터링, 정렬, 페이징, 데이터 삽입, 데이터 업데이트 및 데이터 삭제 포함)는 온라인 및 오프라인 테이블에서 동일하게 작동합니다.

### <a name="synchronize-the-local-offline-cache"></a>로컬 오프라인 캐시 동기화

동기화는 앱의 제어 범위 내에 있습니다.  다음은 동기화 메서드의 예입니다.

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

쿼리 이름이 `.pull(query, queryname)` 메서드에 제공되면 증분 동기화를 사용하여 마지막으로 완료된 끌어오기 이후에 생성되거나 변경된 레코드만 반환합니다.

### <a name="handle-conflicts-during-offline-synchronization"></a>오프라인 동기화 중 충돌 처리

`.push()` 작업 중에 충돌이 발생하면 `MobileServiceConflictException`이 throw됩니다.   서버에서 발급한 항목은 예외에 포함되며 예외에서 `.getItem()`으로 검색될 수 있습니다.  MobileServiceSyncContext 개체에서 다음 항목을 호출하여 푸시를 조정합니다.

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

모든 충돌이 원하는 대로 표시되면 `.push()`를 다시 호출하여 모든 충돌을 해결합니다.

## <a name="custom-api"></a>사용자 지정 API 호출

사용자 지정 API는 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능을 노출하는 사용자 지정 엔드포인트를 정의할 수 있게 합니다. 사용자 지정 API를 사용하면 HTTP 메시지 헤더 읽기와 설정 및 JSON 이외의 메시지 본문 형식 정의를 비롯하여 더 효율적으로 메시징을 제어할 수 있습니다.

Android 클라이언트에서 **invokeApi** 메서드를 호출하여 사용자 지정 API 엔드포인트를 호출합니다. 다음 예제에서는 **completeAll**이라는 API 엔드포인트를 호출하는 방법을 보여주며 이는 **MarkAllResult**라는 컬렉션 클래스를 반환합니다.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

**invokeApi** 메서드가 클라이언트에서 호출되어 POST 요청을 새 사용자 지정 API로 보냅니다. 사용자 지정 API에서 반환하는 결과는 오류와 마찬가지로 메시지 대화 상자에 표시됩니다. 다른 버전의 **invokeApi** 를 사용하면 필요에 따라 요청 본문에 개체를 보내고 HTTP 메서드를 지정하며 요청으로 쿼리 매개 변수를 보낼 수 있습니다. **invokeApi** 의 형식화되지 않은 버전도 제공됩니다.

## <a name="authentication"></a>앱에 인증 추가

자습서는 이러한 기능을 추가하는 방법을 이미 자세히 설명합니다.

App Service는 다음과 같이 다양한 외부 ID 공급자를 사용하여 [앱 사용자의 인증](app-service-mobile-android-get-started-users.md)을 지원합니다. Facebook, Google, Microsoft 계정, Twitter 및 Azure Active Directory. 테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 ID를 사용하여 서버 스크립트에 인증 규칙을 구현할 수도 있습니다.

두 가지의 인증 흐름, 즉 **서버** 흐름과 **클라이언트** 흐름이 지원됩니다. 서버 흐름의 경우 ID 공급자의 웹 인터페이스를 사용하므로 인증 경험이 가장 단순합니다.  서버 흐름 인증을 구현하기 위해 추가 SDK가 필요하지는 않습니다. 서버 흐름 인증은 모바일 디바이스와 긴밀하게 통합되지 않으므로 개념 증명 시나리오에만 권장됩니다.

클라이언트 흐름의 경우 ID 공급자가 제공한 SDK를 사용하므로 Single Sign-On 같은 디바이스별 기능과 심도 깊은 통합이 가능합니다.  예를 들어 Facebook SDK를 모바일 애플리케이션에 통합할 수 있습니다.  모바일 클라이언트는 Facebook 앱으로 전환한 후 다시 모바일 앱으로 전환하기 전에 사용자의 로그온을 확인합니다.

앱에서 인증을 사용하도록 설정하려면 다음 네 단계가 필요합니다.

* ID 공급자로 인증할 수 있도록 앱을 등록합니다.
* App Service 백 엔드를 구성합니다.
* App Service 백 엔드에서 테이블 사용 권한을 인증된 사용자로 제한합니다.
* 앱에 인증 코드 추가

테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수 있습니다. 인증된 사용자의 SID를 사용하여 요청을 수정할 수도 있습니다.  자세한 내용은 [인증 시작] 및 서버 SDK 사용 방법 문서를 참조하세요.

### <a name="caching"></a>인증: 서버 흐름

다음 코드는 Google 공급자를 사용하여 서버 흐름 로그인 프로세스를 시작합니다.  Google 공급자에 대한 보안 요구 사항으로 인해 추가 구성이 필요합니다.

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

또한 기본 활동 클래스에 다음 메서드를 추가합니다.

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

기본 활동에 정의된 `GOOGLE_LOGIN_REQUEST_CODE`는 `login()` 메서드와 `onActivityResult()` 메서드에 내에 사용됩니다.  `login()` 메서드와 `onActivityResult()` 메서드에 동일한 번호가 사용되는 한 고유 번호를 선택할 수 있습니다.  클라이언트 코드를 서비스 어댑터로 추상화하는 경우(앞서 설명한 것처럼) 서비스 어댑터에서 적절한 메서드를 호출해야 합니다.

또한 customtab을 위해 프로젝트를 구성해야 합니다.  먼저 리디렉션 URL을 지정합니다.  다음 조각을 `AndroidManifest.xml`에 추가합니다.

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

**redirectUriScheme**을 애플리케이션의 `build.gradle` 파일에 추가합니다.

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

마지막으로 `com.android.support:customtabs:28.0.0`을 `build.gradle` 파일의 종속성 목록에 추가합니다.

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

**getUserId** 메서드를 사용하여 **MobileServiceUser**에서 로그인한 사용자의 ID를 가져옵니다. 미래를 사용하여 비동기 로그인 API를 호출하는 방법의 예제는 [인증 시작]을 참조하세요.

> [!WARNING]
> 언급한 URL 구성표는 대/소문자를 구분합니다.  `{url_scheme_of_you_app}`의 모든 경우가 대/소문자가 일치해야 합니다.

### <a name="caching"></a>인증 토큰 캐시

인증 토큰을 캐시하려면 사용자 ID 및 인증 토큰을 디바이스에 로컬로 저장해야 합니다. 다음에 앱이 시작될 때 캐시를 확인하여 이 값이 있는 경우 로그인 절차를 건너뛰고 이 데이터로 클라이언트를 리하이드레이션할 수 있습니다. 하지만 이 데이터는 중요하므로 휴대폰을 분실하는 경우 안전을 위해 암호화하여 저장해야 합니다.  인증 토큰을 캐시하는 방법의 전체 예제는 [캐시 인증 토큰 섹션][7]에서 볼 수 있습니다.

만료된 토큰을 사용하려고 하면 *401 권한 없음* 응답을 받습니다. 필터를 사용하여 인증 오류를 처리할 수 있습니다.  필터가 App Service 백 엔드에 대한 요청을 가로챕니다. 필터 코드가 401에 대한 응답을 테스트하고, 로그인 프로세스를 트리거한 후 401을 생성한 요청을 다시 시작합니다.

### <a name="refresh"></a>토큰 새로 고침 사용

Azure App Service 인증 및 권한 부여에서 반환된 토큰에는 정의된 수명이 1시간 있습니다.  이 기간이 지나면 사용자를 다시 인증해야 합니다.  클라이언트 흐름 인증을 통해 수신한 수명이 긴 토큰을 사용하는 경우 동일한 토큰을 사용하여 Azure App Service 인증 및 권한 부여로 다시 인증할 수 있습니다.  또 다른 Azure App Service 토큰이 새로운 수명으로 생성됩니다.

토큰 새로 고침을 사용하도록 공급자를 등록할 수도 있습니다.  토큰 새로 고침을 항상 사용할 수는 없습니다.  추가 구성이 필요합니다.

* **Azure Active Directory**의 경우 Azure Active Directory 앱에 대한 클라이언트 비밀을 구성합니다.  Azure Active Directory 인증을 구성할 때 Azure App Service에 클라이언트 비밀을 지정합니다.  `.login()`을 호출하면 `response_type=code id_token`을 매개 변수로 전달합니다.

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Google**의 경우 `access_type=offline`을 매개 변수로 전달합니다.

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Microsoft 계정**은 `wl.offline_access` 범위를 선택합니다.

토큰을 새로 고치려면 `.refreshUser()`를 호출합니다.

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

가장 좋은 방법은 서버에서 401 응답을 감지하고 사용자 토큰을 새로 고치려고 시도하는 필터를 만드는 것입니다.

## <a name="log-in-with-client-flow-authentication"></a>클라이언트 흐름 인증으로 로그인

클라이언트 흐름 인증을 사용한 일반적인 로그인 프로세스는 다음과 같습니다.

* 서버 흐름 인증과 마찬가지로 Azure App Service 인증 및 권한 부여를 구성합니다.
* 인증을 위해 인증 공급자 SDK를 통합하여 액세스 토큰을 생성합니다.
* 다음과 같이 `.login()` 메서드를 호출합니다(`result`는 `AuthenticationResult`여야 함).

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

다음 섹션에서 전체 코드 예제를 참조하세요.

`onSuccess()` 메서드를 성공적인 로그인에 사용할 코드로 대체합니다.  `{provider}` 문자열은 유효한 공급자 즉 **aad**(Azure Active Directory), **facebook**, **google**, **microsoftaccount** 또는 **twitter**입니다.  사용자 지정 인증을 구현하면 사용자 지정 인증 공급자 태그를 사용할 수도 있습니다.

### <a name="adal"></a>Active Directory 인증 라이브러리(ADAL)를 사용하여 사용자 인증

Azure Active Directory를 사용하여 애플리케이션에 사용자가 로그인하려면 Active Directory 인증 라이브러리(ADAL)를 사용할 수 있습니다. 클라이언트 흐름 로그인은 UX 느낌을 그대로 제공하고 추가 사용자 지정이 가능하기 때문에 `loginAsync()` 메서드보다 선호도가 높습니다.

1. [Active Directory 로그인에 App Service를 구성하는 방법][22] 자습서를 수행하여 AAD 로그인에 모바일 앱 백 엔드를 구성합니다. 네이티브 클라이언트 애플리케이션을 등록하는 선택적 단계를 완료해야 합니다.
2. 다음 정의를 포함하도록 build.gradle 파일을 수정하여 ADAL을 설치합니다.

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. 애플리케이션에 아래 코드를 추가하여 다음과 같이 대체합니다.

    * **INSERT-AUTHORITY-HERE**를 애플리케이션이 프로비전된 테넌트의 이름으로 바꿉니다. 형식은 https://login.microsoftonline.com/contoso.onmicrosoft.com이어야 합니다.
    * **INSERT-RESOURCE-ID-HERE** 를 모바일 앱 백 엔드에 대한 클라이언트 ID로 바꿉니다. 포털의 Azure **Active Directory 설정**에 있는 **고급** 탭에서 클라이언트 ID를 가져올 수 있습니다.
    * **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 애플리케이션에서 복사한 클라이언트 ID로 바꿉니다.
    * HTTPS 체계를 사용하여 **INSERT-REDIRECT-URI-HERE** 를 사이트의 */.auth/login/done* 엔드포인트로 바꿉니다. 이 값은 *https://contoso.azurewebsites.net/.auth/login/done*과 비슷해야 합니다.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>클라이언트-서버 통신 조정

클라이언트 연결은 일반적으로 Android SDK와 함께 제공되는 기본 HTTP 라이브러리를 사용하는 기본 HTTP 연결입니다.  변경하려는 이유는 여러 가지입니다.

* 시간 제한을 조정하기 위해 대체 HTTP 라이브러리를 사용하려고 합니다.
* 진행률 표시줄을 제공하려고 합니다.
* API 관리 기능을 지원하기 위해 사용자 지정 헤더를 추가하려고 합니다.
* 다시 인증을 구현할 수 있도록 실패한 응답을 가로채려고 합니다.
* 분석 서비스에 백엔드 요청을 기록하려고 합니다.

### <a name="using-an-alternate-http-library"></a>대체 HTTP 라이브러리 사용

클라이언트 참조를 만든 직후 `.setAndroidHttpClientFactory()` 메서드를 호출합니다.  예를 들어 연결 시간 제한을 60초(기본값 10초 대신)로 설정하려면 다음을 수행합니다.

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>진행률 필터 구현

`ServiceFilter`를 구현하여 모든 요청에 대한 가로채기를 구현할 수 있습니다.  예를 들어, 다음은 미리 만든 진행률 표시줄을 업데이트합니다.

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

다음과 같이 이 필터를 클라이언트에 연결할 수 있습니다.

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>요청 헤더 사용자 지정

다음 `ServiceFilter`를 사용하고 `ProgressFilter`와 같은 방식으로 필터를 연결합니다.

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>자동 직렬화 구성

[gson][3] API를 사용하여 모든 열에 적용되는 변환 전략을 지정할 수 있습니다. Android 클라이언트 라이브러리는 배후에서 [gson][3]을 사용하여 Java 개체를 JSON 데이터로 직렬화한 후 Azure App Service로 데이터를 전송합니다.  다음은 **setFieldNamingStrategy()** 메서드를 사용하여 전략을 설정하는 코드입니다. 이 예에서는 시작 문자("m")를 삭제한 후 각 필드 이름에 대해 다음 문자를 소문자로 처리하도록 지시합니다. 예를 들어 "mId"를 "id"로 변환합니다.  대부분의 필드에서 `SerializedName()` 주석의 필요성을 줄이려면 변환 전략을 구현합니다.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

**MobileServiceClient**를 사용하여 모바일 클라이언트 참조를 만들기 전에 이 코드를 실행해야 합니다.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[인증 시작]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
