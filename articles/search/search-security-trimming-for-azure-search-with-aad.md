---
title: Active Directory를 사용하여 결과를 자르는 보안 필터 - Azure Search
description: 보안 필터 및 AAD(Azure Active Directory) ID를 사용하는 Azure Search 콘텐츠에 대한 액세스 제어입니다.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 410727022b092e2dd8ab8b05e628e25fd60ab833
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282215"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Active Directory ID를 사용하여 Azure Search 결과를 자르는 보안 필터

이 문서에서는 Azure Search에서 필터와 함께 AAD(Azure Active Directory)를 사용하여 사용자 그룹 멤버 자격에 따라 검색 결과를 자르는 방법을 보여줍니다.

이 문서에서 다루는 작업은 다음과 같습니다.
> [!div class="checklist"]
> - AAD 그룹 및 사용자 만들기
> - 만든 그룹과 사용자 연결
> - 새 그룹 캐시
> - 연결된 그룹을 사용하여 문서 인덱싱
> - 그룹 식별자 필터를 사용하여 검색 요청 실행
> 
> [!NOTE]
> 이 문서의 샘플 코드 조각은 C#으로 작성되었습니다. 전체 소스 코드는 [GitHub](https://aka.ms/search-dotnet-howto)를 참조하세요. 

## <a name="prerequisites"></a>필수 조건

Azure Search의 인덱스에는 문서에 대한 읽기 권한이 있는 그룹 ID 목록을 저장하는 [보안 필드](search-security-trimming-for-azure-search.md)가 있어야 합니다. 이 사용 사례에서는 보안 개체 항목(예: 개인의 대학 지원서) 및 해당 항목에 대한 액세스 권한이 있는 사람을 지정하는 보안 필드(입학 담당자) 간의 일대일 대응을 가정합니다.

이 연습에서는 AAD에서 사용자, 그룹 및 연결을 만들려면 AAD 관리자 권한이 있어야 합니다.

또한 다음, 절차에 설명된 대로 애플리케이션을 AAD에 등록해야 합니다.

### <a name="register-your-application-with-aad"></a>AAD에 애플리케이션 등록

이 단계에서는 사용자 및 그룹 계정 로그인을 수락하기 위해 애플리케이션을 AAD와 통합합니다. 조직의 AAD 관리자가 아닌 경우 다음 단계를 수행할 [새 테넌트를 만들어야](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) 할 수도 있습니다.

1. [**애플리케이션 등록 포털**](https://apps.dev.microsoft.com) >  **수렴형 앱** > **앱 추가**로 이동합니다.
2. 애플리케이션의 이름을 입력한 다음, **만들기**를 클릭합니다. 
3. [내 애플리케이션] 페이지에서 새로 등록한 애플리케이션을 선택합니다.
4. [애플리케이션 등록] 페이지 &gt; **플랫폼** > **플랫폼 추가**에서 **웹 API**를 선택합니다.
5. 계속해서 [애플리케이션 등록] 페이지에서 **Microsoft Graph 권한** > **추가**로 이동합니다.
6. [권한 선택]에서 다음 위임된 권한을 추가하고 **확인**을 클릭합니다.

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph는 REST API를 통해 AAD에 프로그래밍 방식으로 액세스할 수 있는 API를 제공합니다. 이 연습의 코드 샘플에서는 권한을 사용하여 그룹, 사용자 및 연결을 만들기 위한 Microsoft Graph API를 호출합니다. 또한 그룹 식별자를 캐시하여 필터링 속도를 높이기 위해 API를 사용합니다.

## <a name="create-users-and-groups"></a>사용자 및 그룹 만들기

기존 애플리케이션에 검색을 추가하려는 경우 AAD에 기존 사용자 및 그룹 식별자가 있을 수도 있습니다. 이 경우 다음 세 단계를 건너뛸 수 있습니다. 

그러나 기존 사용자가 없는 경우에는 Microsoft Graph API를 사용하여 보안 주체를 만들 수 있습니다. 다음 코드 조각은 Azure Search 인덱스의 보안 필드에 대한 데이터 값이 되는 식별자를 생성하는 방법을 보여줍니다. 앞에서 예로 든 대학 입학 지원서에서는 입학 담당의 보안 식별자가 됩니다.

사용자 및 그룹 멤버 자격은 유동적이며, 조직 규모가 클수록 더욱 그렇습니다. 조직 멤버 자격의 변경 내용을 선택할 수 있을 만큼 사용자 및 그룹 ID를 빌드하는 코드를 충분히 자주 실행해야 합니다. 마찬가지로, Azure Search 인덱스에는 허용된 사용자 및 리소스의 현재 상태를 반영하도록 비슷한 업데이트 일정이 필요합니다.

### <a name="step-1-create-aad-grouphttpsdocsmicrosoftcomgraphapigroup-post-groupsviewgraph-rest-10"></a>1단계: [AAD 그룹](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 만들기 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdocsmicrosoftcomgraphapiuser-post-usersviewgraph-rest-10"></a>2단계: [AAD 사용자](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0) 만들기
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>3단계: 사용자 및 그룹 연결
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>4단계: 그룹 식별자 캐시
필요에 따라 네트워크 대기 시간을 줄이기 위해, 검색 요청이 실행되면 캐시에서 그룹이 반환되어 AAD로의 왕복 시간을 단축하도록 사용자 그룹 연결을 캐시할 수 있습니다. [AAD Batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching)를 사용하여 여러 사용자가 있는 단일 Http 요청을 보내고 캐시를 빌드할 수 있습니다.

Microsoft Graph는 많은 양의 요청을 처리하도록 설계되었습니다. 요청이 너무 많이 발생하면 Microsoft Graph가 HTTP 상태 코드 429와 함께 요청을 실패합니다. 자세한 내용은 [Microsoft Graph 제한](https://developer.microsoft.com/graph/docs/concepts/throttling)을 참조하세요.

## <a name="index-document-with-their-permitted-groups"></a>그룹이 허용된 인덱스 문서

Azure Search에서 쿼리 작업은 Azure Search 인덱스를 통해 실행됩니다. 이 단계에서 인덱싱 작업은 보안 필터로 사용되는 식별자를 포함하여 검색 가능한 데이터를 인덱스로 가져옵니다. 

Azure Search는 사용자 ID를 인증하거나 사용자가 볼 권한이 있는 콘텐츠를 설정하는 논리를 제공하지 않습니다. 보안 조정에 대한 사용 사례에서는 중요한 문서와 해당 문서에 대한 액세스 권한을 가진 그룹 식별자를 연결하고 온전한 상태로 검색 인덱스로 가져온 것으로 가정합니다. 

가상의 예제에서 Azure Search 인덱스의 PUT 요청 본문에는 지원자의 대학 에세이 또는 성적증명서와 함께 해당 콘텐츠를 볼 권한이 있는 그룹 식별자가 포함됩니다. 

이 연습의 코드 샘플에 사용된 일반적인 예에서는 인덱스 작업이 다음과 같이 표시될 수 있습니다.

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>검색 요청 실행

보안 조정을 위해 인덱스의 보안 필드 값은 검색 결과에 문서를 포함하거나 제외하는 데 사용되는 고정적인 값입니다. 예를 들어 입학의 그룹 식별자가 "A11B22C33D44-E55F66G77-H88I99JKK"이면 보안 필드에 이 식별자가 있는 Azure Search 인덱스의 모든 문서는 요청자에게 반환되는 검색 결과에 포함(또는 제외)됩니다.

요청을 실행하는 사용자 그룹을 기반으로 검색 결과에 반환되는 문서를 필터링하려면 다음 단계를 검토합니다.

### <a name="step-1-retrieve-users-group-identifiers"></a>1단계: 사용자의 그룹 식별자 검색

사용자 그룹이 아직 캐시되지 않았거나 캐시가 만료된 경우 [그룹](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) 요청을 실행합니다.
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>2단계: 검색 요청 작성

사용자의 그룹 멤버 자격을 갖고 있다면 적절한 필터 값이 포함된 검색 요청을 실행할 수 있습니다.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>3단계: 결과 처리

응답에는 사용자가 볼 권한이 있는 문서로 구성된 필터링된 목록이 포함됩니다. 검색 결과 페이지를 작성하는 방법에 따라 필터링된 결과 집합을 반영하는 시각적 신호를 포함할 수 있습니다.

## <a name="conclusion"></a>결론

이 연습에서는 AAD 로그인을 사용하여 Azure Search 결과의 문서를 필터링하고, 요청에서 제공한 필터와 일치하지 않는 문서 결과를 잘라내는 기술을 배웠습니다.

## <a name="see-also"></a>참고 항목

+ [Azure Search 필터를 사용하여 ID 기반 액세스 제어](search-security-trimming-for-azure-search.md)
+ [Azure Search의 필터](search-filters.md)
+ [Azure Search 작업의 데이터 보안 및 액세스 제어](search-security-overview.md)
