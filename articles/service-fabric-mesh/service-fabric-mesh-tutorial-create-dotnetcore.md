---
title: Service Fabric Mesh에 다중 서비스 앱 생성, 배포
description: 이 자습서에서는 백 엔드 웹 서비스와 통신하고, 로컬로 디버그하고, Azure에 게시하는 ASP.NET Core 웹 사이트로 구성된 다중 서비스 Azure Service Fabric Mesh 애플리케이션을 만듭니다.
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 4a64d1999f13ee09cf92633fd188bfc7ed0e4856
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011212"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>자습서: 다중 서비스 Service Fabric Mesh 앱 만들기, 디버그, 배포 및 업그레이드

이 자습서는 시리즈의 1부입니다. Visual Studio를 사용하여 ASP.NET 웹 프런트 엔드 및 ASP.NET Core 웹 API 백 엔드 서비스가 있는 Azure Service Fabric Mesh 앱을 만드는 방법에 대해 알아봅니다. 그런 다음, 로컬 개발 클러스터에서 앱을 디버그합니다. 앱을 Azure에 게시한 후 구성 및 코드를 변경하고 앱을 업그레이드합니다. 마지막으로 사용되지 않는 Azure 리소스를 정리하여 사용하지 않는 항목에 대한 비용이 청구되지 않도록 합니다.

작업이 완료되면 앱 수명 주기 관리의 대부분 단계를 수행하고 Service Fabric Mesh 앱에서 서비스 간 호출을 보여 주는 앱을 빌드하게 됩니다.

할 일 애플리케이션을 수동으로 만들지 않으려면 완성된 애플리케이션에 대한 [소스 코드를 다운로드](https://github.com/azure-samples/service-fabric-mesh)하고 [로컬로 애플리케이션을 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)하도록 건너뛸 수 있습니다.

시리즈 1부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Visual Studio를 사용하여 ASP.NET 웹 프런트 엔드로 구성된 Service Fabric Mesh 앱을 만듭니다.
> * 할 일 항목을 나타내는 모델을 만듭니다.
> * 백 엔드 서비스를 만들고 이 서비스에서 데이터를 검색합니다.
> * 백 엔드 서비스에 대한 모델 뷰 컨트롤러 패턴의 일부로 컨트롤러와 DataContext를 추가합니다.
> * 할 일 항목을 표시할 웹 페이지를 만듭니다.
> * 백 엔드 서비스를 식별하는 환경 변수를 만듭니다.

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * Visual Studio에서 Service Fabric Mesh 앱 만들기
> * [로컬 개발 클러스터에서 실행 중인 Service Fabric Mesh 앱 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh 앱 배포](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh 앱 업그레이드](service-fabric-mesh-tutorial-upgrade.md)
> * [Service Fabric Mesh 리소스 정리](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 시작하기 전에:

* Azure 구독이 아직 없으면 시작하기 전에 [무료 계정을 만들](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 수 있습니다.

* Service Fabric 런타임, SDK, Docker 및 Visual Studio 2017 설치가 포함된 [개발 환경을 설정](service-fabric-mesh-howto-setup-developer-environment-sdk.md)했는지 확인합니다.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Visual Studio에서 Service Fabric Mesh 프로젝트 만들기

Visual Studio를 열고 **파일** > **새로 만들기** > **프로젝트...** 를 차례로 선택합니다.

**새 프로젝트** 대화 상자의 맨 위에 있는 **검색** 상자에 `mesh`를 입력합니다. **Service Fabric Mesh 애플리케이션** 템플릿을 선택합니다. 템플릿이 표시되지 않는 경우, [개발 환경 설정](service-fabric-mesh-howto-setup-developer-environment-sdk.md)에 설명된 대로 Mesh SDK 및 VS 도구 미리 보기를 설치했는지 확인합니다.  

**이름** 상자에서 `todolistapp`을 입력하고, **위치** 상자에서 폴더 경로를 프로젝트에 대한 파일을 저장하려는 위치로 설정합니다.

**솔루션 디렉터리 만들기**가 선택되어 있는지 확인하고 **확인**을 클릭하여 Service Fabric Mesh 프로젝트를 만듭니다.

![Visual Studio의 새 Service Fabric Mesh 프로젝트 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

그러면 **새 Service Fabric 서비스** 대화 상자가 표시됩니다.

### <a name="create-the-web-front-end-service"></a>웹 프런트 엔드 서비스 만들기

**새 Service Fabric 서비스** 대화 상자에서 **ASP.NET Core** 프로젝트 형식을 선택하고, **컨테이너 OS**가 **Windows**로 설정되어 있는지 확인합니다.

**서비스 이름**을 **WebFrontEnd**로 설정합니다. **확인**을 눌러 ASP.NET Core 서비스를 만듭니다.

![Visual Studio의 새 Service Fabric Mesh 프로젝트 대화 상자](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

그러면, 새 ASP.NET Core 웹 애플리케이션 대화 상자가 표시됩니다. **웹 애플리케이션**을 선택하고 **확인**을 클릭합니다.

![Visual Studio의 새 ASP.NET Core 애플리케이션](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

이제 Service Fabric Mesh 애플리케이션이 만들어졌습니다. 다음으로, 할 일 정보에 대한 모델을 만들겠습니다.

## <a name="create-the-to-do-items-model"></a>할 일 항목 모델 만들기

간단히 하기 위해 할 일 항목은 메모리의 목록에 저장됩니다. 할 일 항목에 대한 클래스 라이브러리와 이러한 항목을 보관할 목록을 만듭니다. 현재 **todolistapp** 솔루션이 로드되어 있는 Visual Studio에서 **파일** > **추가** > **새 프로젝트**를 차례로 선택합니다.

**새 프로젝트 추가** 대화 상자 위쪽의 **검색** 상자에서 `C# .net core class`를 입력합니다. **클래스 라이브러리(.NET Core)** 템플릿을 선택합니다.

**이름** 상자에서 `Model`을 입력합니다. **확인**을 클릭하여 클래스 라이브러리를 만듭니다.

[솔루션 탐색기]의 **모델** 아래에서 **Class1.cs**를 마우스 오른쪽 단추로 클릭하고 **이름 바꾸기**를 선택합니다. **ToDoItem.cs** 클래스의 이름을 바꿉니다. 모든 참조의 이름을 바꿀 것인지 묻는 메시지가 표시되면 **예**를 클릭합니다.

비어 있는 `class ToDoItem`의 내용을 다음으로 바꿉니다.

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

이 클래스는 할 일 항목을 나타냅니다.

Visual Studio에서 **모델** 클래스 라이브러리를 마우스 오른쪽 단추로 클릭하고, 할 일 항목을 보관할 목록을 만들기 위해 **추가** > **클래스...** 를 차례로 선택합니다. **새 항목 추가** 대화 상자가 표시됩니다. **이름**을 `ToDoList.cs`로 설정하고 **추가**를 클릭합니다.

**ToDoList.cs**에서 비어 있는 `class ToDoList`를 다음으로 바꿉니다.

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

다음으로, 할 일 항목을 추적할 서비스 패브릭 서비스를 만들겠습니다.

## <a name="create-the-back-end-service"></a>백 엔드 서비스 만들기

Visual Studio **솔루션 탐색기** 창에서 **todolistapp**을 마우스 오른쪽 단추로 클릭하고, **추가** > **새 Service Fabric 서비스...** 를 차례로 클릭합니다.

**새 Service Fabric 서비스** 대화 상자가 표시됩니다. **ASP.NET Core** 프로젝트 형식을 선택하고, **컨테이너 OS**가 **Windows**로 설정되어 있는지 확인합니다. **서비스 이름**을 **ToDoService**로 설정합니다. **확인**을 클릭하여 ASP.NET Core 서비스를 만듭니다.

그러면 **새 ASP.NET Core 웹 애플리케이션** 대화 상자가 표시됩니다. 이 대화 상자에서 **API**, **확인**을 차례로 선택하면 서비스 프로젝트가 솔루션에 추가됩니다.

![Visual Studio의 새 ASP.NET Core 애플리케이션](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

백 엔드 서비스는 UI를 제공하지 않으므로 서비스가 시작되면 브라우저 시작을 해제합니다. **솔루션 탐색기**에서 **ToDoService**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다. 표시되는 속성 창에서 왼쪽에 있는 **디버그** 탭을 선택하고 **브라우저 시작**의 선택을 취소합니다. **Ctrl+S** 를 눌러 변경 내용을 저장합니다.

이 서비스는 할 일 정보를 유지 관리하므로 Model 클래스 라이브러리에 대한 참조를 추가합니다. [솔루션 탐색기]에서 **ToDoService**를 마우스 오른쪽 단추로 클릭한 다음, **추가** > **참조...** 를 차례로 선택합니다. **참조 관리자** 대화 상자가 표시됩니다.

**참조 관리자**에서 **모델**에 대한 확인란을 선택하고 **확인**을 클릭합니다.

### <a name="add-a-data-context"></a>데이터 컨텍스트 추가

다음으로, 데이터 모델의 데이터 처리를 조정하는 데이터 컨텍스트를 만듭니다.

데이터 컨텍스트 클래스를 추가하려면 [솔루션 탐색기]에서 **ToDoService**를 마우스 오른쪽 단추로 클릭한 다음, **추가** > **클래스**를 차례로 클릭합니다.
표시되는 **새 항목 추가** 대화 상자에서 **클래스**가 선택되어 있는지 확인하고, **이름**을 `DataContext.cs`로 설정하고, **추가**를 클릭합니다.

**DataContext.cs**에서 비어 있는 `class DataContext`의 내용을 다음으로 바꿉니다.

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

최소한의 이 데이터 컨텍스트는 일부 할 일 항목 샘플을 채우고 이러한 항목에 대한 액세스를 제공합니다.

### <a name="add-a-controller"></a>컨트롤러 추가

**ToDoService** 프로젝트를 만들 때 HTTP 요청을 처리하고 HTTP 응답을 만드는 기본 컨트롤러가 템플릿에서 제공되었습니다. **솔루션 탐색기**의 **ToDoService** 아래에서 **컨트롤러** 폴더를 열어 **ValuesController.cs** 파일을 확인합니다. 

**ValuesController.cs**를 마우스 오른쪽 단추로 클릭한 다음, **이름 바꾸기**를 클릭합니다. 파일의 이름을 `ToDoController.cs`로 변경합니다. 모든 참조의 이름을 바꾸라는 메시지가 표시되면 **예**를 클릭합니다.

**ToDoController.cs** 파일을 열고 `class ToDoController`의 내용을 다음으로 바꿉니다.

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

이 자습서에서는 다른 서비스와의 통신에 집중하기 위해 추가, 삭제 등은 구현하지 않습니다.

## <a name="create-the-web-page-that-displays-to-do-items"></a>할 일 항목을 표시하는 웹 페이지 만들기

백 엔드 서비스가 구현된 상태에서 제공하는 할 일 항목을 표시할 웹 사이트를 코딩합니다. 다음에 나오는 단계는 **WebFrontEnd** 프로젝트 내에서 수행됩니다.

할 일 항목을 표시하는 웹 페이지에는 **ToDoItem** 클래스 및 목록에 대한 액세스가 필요합니다.
**솔루션 탐색기**에서 **WebFrontEnd**를 마우스 오른쪽 단추로 클릭하고 **추가** > **참조...** 를 차례로 선택하여 모델 프로젝트에 대한 참조를 추가합니다. **참조 관리자** 대화 상자가 표시됩니다.

**참조 관리자**에서 **모델**에 대한 확인란, **확인**을 차례로 클릭합니다.

**솔루션 탐색기**에서 **WebFrontEnd** > **페이지** > **Index.cshtml**로 차례로 이동하여 Index.cshtml 페이지를 엽니다. **Index.cshtml**이 열립니다.

파일 전체의 내용을 할 일 항목이 표시될 간단한 테이블을 정의하는 다음 HTML로 바꿉니다.

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

**솔루션 탐색기**에 있는 **Index.cshtml** 파일의 드롭다운 아이콘을 클릭한 다음, **Index.cshtml.cs**를 엽니다.

**Index.cshtml.cs**의 맨 위쪽에 `using System.Net.Http;`를 추가합니다.

`public class IndexModel`의 내용을 다음으로 바꿉니다.

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>환경 변수 만들기

백 엔드 서비스에 대한 URL은 해당 서비스와 통신하는 데 필요합니다. 이 자습서의 목적을 위해, IndexModel의 일부로 위에서 정의된 코드에서 발췌한 다음 부분은 환경 변수를 읽어 URL을 작성합니다.

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

URL은 서비스 이름과 포트로 구성됩니다. 이 모든 정보는 **ToDoService** 프로젝트의 service.yaml 파일에 있습니다.

> [!IMPORTANT]
> 다음 단계에서는 YAML 파일을 수정합니다.
> service.yaml 파일에서 탭이 아닌 공백은 변수 들여쓰기에 사용해야 합니다. 그렇지 않으면 컴파일되지 않습니다. 환경 변수를 만들 때 Visual Studio에서 탭을 삽입할 수 있습니다. 모든 탭을 공백으로 바꾸세요. **빌드** 디버그 출력에 오류가 표시되더라도 앱은 계속 실행되지만 탭을 공백으로 변환하고 다시 빌드할 때까지는 실행되지 않습니다. service.yaml 파일에 탭이 없는지 확인하려면 Visual Studio 편집기에서 **편집**  > **고급**  > **공백 보기**를 차례로 사용하여 공백이 표시되도록 할 수 있습니다.
> service.yaml 파일은 영어 로캘을 사용하여 처리됩니다. 소수 구분 기호를 사용해야 하는 경우 예를 들어, 쉼표가 아닌 마침표를 사용합니다.

**솔루션 탐색기**에서 **ToDoService** 프로젝트로 이동하고 **서비스 리소스** > **service.yaml**을 차례로 엽니다.

![그림 1 - ToDoService service.yaml 파일](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 `ToDoService` 서비스 이름은 `services:` 아래에 있습니다. 위 그림 (1)을 참조하세요.

* `80` 포트는 `endpoints:` 아래에 있습니다. 위 그림 (2)를 참조하세요. 프로젝트의 포트 번호가 다를 수 있습니다.

다음으로, 서비스 이름과 포트 번호를 나타내는 환경 변수가 백 엔드 서비스를 호출할 수 있도록 WebFrontEnd 프로젝트에 정의해야 합니다.

**솔루션 탐색기**에서 **WebFrontEnd** > **서비스 리소스** > **service.yaml**로 차례로 이동하여 백 엔드 서비스 주소를 지정하는 변수를 정의합니다.

service.yaml 파일에서 `environmentVariables:` 아래에 다음 변수를 추가합니다(`environmentVariables:`의 주석을 제거하려면 먼저 `#`을 제거해야 함). 간격이 중요하므로 추가한 변수를 `environmentVariables:` 아래의 다른 변수와 정렬합니다. ApiHostPort 값이 ToDoService의 service.yaml 파일에서 이전에 보았던 ToDoServiceListener의 포트 값과 일치하는 것이 매우 중요합니다.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> `ToDoServiceName`에 대한 값을 지정하는 방법은 두 가지입니다. 
> - Azure Service Fabric Mesh에 서비스를 배포할 때뿐만 아니라 Windows 10의 디버깅 시나리오에서 확인할 서비스 이름으로 지정합니다.
> - servicename.appname으로 정규화하여 지정합니다. 이것은 Windows 10에서 디버깅하는 경우에만 작동합니다.
> 서비스 확인을 위해 서비스 이름만 사용하는 것이 좋습니다.

`ApiHostPort` 값이 다를 수도 있지만 **WebFrontEnd** 프로젝트의 **service.yaml** 파일은 다음과 같을 수 있습니다.

![WebFrontEnd 프로젝트의 Service.yaml](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


이제 백 엔드 웹 서비스와 함께 Service Fabric Mesh 애플리케이션의 이미지를 로컬 클러스터에 빌드하고 배포할 준비가 되었습니다.

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * ASP.NET 웹 프런트 엔드로 구성된 Service Fabric Mesh 앱을 만듭니다.
> * 할 일 항목을 나타내는 모델을 만듭니다.
> * 백 엔드 서비스를 만들고 이 서비스에서 데이터를 검색합니다.
> * 백 엔드 서비스에 대한 모델 뷰 컨트롤러 패턴의 일부로 컨트롤러와 DataContext를 추가합니다.
> * 할 일 항목을 표시할 웹 페이지를 만듭니다.
> * 백 엔드 서비스를 식별하는 환경 변수를 만듭니다.

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [로컬 개발 클러스터에서 실행 중인 Service Fabric Mesh 애플리케이션 디버그](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)