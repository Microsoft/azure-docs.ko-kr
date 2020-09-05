---
title: '자습서: WPF, C#을 사용하여 번역 앱 만들기 - Translator'
titleSuffix: Azure Cognitive Services
description: 이 자습서에서는 단일 구독 키를 사용하여 텍스트 번역, 언어 감지 및 맞춤법 검사를 수행하는 WPF 앱을 만듭니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: devx-track-csharp
ms.openlocfilehash: ef5384abd63dcd9aeb4789dc4955f4b80068d330
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921242"
---
# <a name="tutorial-create-a-translation-app-with-wpf"></a>자습서: WPF를 사용하여 번역 앱 만들기

이 자습서에서는 단일 구독 키를 사용하여 텍스트 번역, 언어 감지 및 맞춤법 검사에서 Azure Cognitive Services를 사용하는 [WPF(Windows Presentation Foundation)](https://docs.microsoft.com/visualstudio/designers/getting-started-with-wpf?view=vs-2019) 앱을 빌드합니다. 특히 앱은 Translator 및 [Bing Spell Check](https://azure.microsoft.com/services/cognitive-services/spell-check/)에서 API를 호출합니다.

WPF란? 데스크톱 클라이언트 앱을 만드는 UI 프레임워크입니다. WPF 개발 플랫폼은 애플리케이션 모델, 리소스, 컨트롤, 그래픽, 레이아웃, 데이터 바인딩, 문서 및 보안을 포함한 광범위한 애플리케이션 개발 기능을 지원합니다. .NET Framework의 하위 집합이므로 이전에 ASP.NET 또는 Windows Forms를 사용하는 .NET Framework를 통해 앱을 빌드한 경우 프로그래밍 환경에 이미 친숙해져 있습니다. WPF는 XAML(Extensible App Markup Language)을 사용하여 앱 프로그래밍을 위한 선언적 모델을 제공하며, 이 모델은 다음 섹션에서 검토하겠습니다.

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Visual Studio에서 WPF 프로젝트 만들기
> * 프로젝트에 어셈블리 및 NuGet 패키지 추가
> * XAML을 사용하여 앱 UI 만들기
> * Translator를 사용하여 언어 가져오기, 텍스트 번역 및 원본 언어 검색
> * Bing Spell Check API를 사용하여 입력 유효성 검사 및 변환 정확도 향상
> * WPF 앱 실행

### <a name="cognitive-services-used-in-this-tutorial"></a>이 자습서에서 사용되는 Cognitive Services

이 목록에는 이 자습서에서 사용되는 Cognitive Services가 나와 있습니다. 링크를 따라 각 기능에 대한 API 참조를 찾아보세요.

| 서비스 | 기능 | Description |
|---------|---------|-------------|
| 변환기 | [언어 가져오기](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages) | 텍스트 번역에 지원되는 전체 언어 목록을 검색합니다. |
| 변환기 | [번역](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) | 텍스트를 70개가 넘는 언어로 번역합니다. |
| 변환기 | [검색](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-detect) | 입력 텍스트의 언어를 감지합니다. 감지에 대한 신뢰도 점수를 포함합니다. |
| Bing 맞춤법 검사 | [맞춤법 검사](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) | 맞춤법 오류를 수정하여 번역 정확도를 향상시킵니다. |

## <a name="prerequisites"></a>사전 요구 사항

계속하기 전에 다음이 필요합니다.

* Azure Cognitive Services 구독. [Cognitive Services 키 가져오기](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#create-a-new-azure-cognitive-services-resource).
* Windows 머신
* [Visual Studio 2019](https://www.visualstudio.com/downloads/) - Community 또는 Enterprise

> [!NOTE]
> 이 자습서에서는 구독을 미국 서부 지역에 만드는 것이 좋습니다. 그렇지 않으면 이 연습을 수행할 때 코드에서 엔드포인트와 지역을 변경해야 합니다.  

## <a name="create-a-wpf-app-in-visual-studio"></a>Visual Studio에서 WPF 앱 만들기

수행해야 하는 첫 번째 작업은 Visual Studio에서 프로젝트를 설정하는 것입니다.

1. Visual Studio를 엽니다. **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기**에서 **WPF 앱(.NET Framework)** 을 찾아 선택합니다. **언어**에서 C#을 선택하여 옵션의 범위를 좁힐 수 있습니다.
1. **다음**을 선택한 다음, 프로젝트 `MSTranslatorDemo`의 이름을 지정합니다.
1. 프레임워크 버전을 **.NET Framework 4.7.2** 이상으로 설정하고, **만들기**를 선택합니다.
   ![Visual Studio에서 이름 및 프레임워크 버전 입력](media/name-wpf-project-visual-studio.png)

프로젝트가 만들어졌습니다. 두 개의 탭, `MainWindow.xaml` 및 `MainWindow.xaml.cs`가 열려 있습니다. 이 자습서에서는 이 두 파일에 코드를 추가합니다. 앱의 사용자 인터페이스에 대한 `MainWindow.xaml`을 수정합니다. Translator 및 Bing Spell Check에 대한 호출에 대해 `MainWindow.xaml.cs`를 수정합니다.
   ![환경 검토](media/blank-wpf-project.png)

다음 섹션에서는 JSON 구문 분석과 같은 추가 기능을 위해 어셈블리와 NuGet 패키지를 프로젝트에 추가합니다.

## <a name="add-references-and-nuget-packages-to-your-project"></a>프로젝트에 참조 및 NuGet 패키지 추가

프로젝트에는 약간의 .NET Framework 어셈블리 및 NuGet 패키지 관리자를 사용하여 설치하는 NewtonSoft.Json이 필요합니다.

### <a name="add-net-framework-assemblies"></a>.NET Framework 어셈블리 추가

개체를 직렬화 및 역직렬화하고 HTTP 요청 및 응답을 관리하는 어셈블리를 프로젝트에 추가해 보겠습니다.

1. Visual Studio의 솔루션 탐색기에서 프로젝트를 찾습니다. 마우스 오른쪽 단추로 프로젝트를 클릭한 다음, **추가 > 참조**를 선택합니다. 그러면 **참조 관리자**가 열립니다.
1. **어셈블리** 탭에는 참조에 사용할 수 있는 모든 .NET Framework 어셈블리가 나열됩니다. 오른쪽 위에 있는 검색 창을 사용하여 참조를 검색합니다.
   ![어셈블리 참조 추가](media/add-assemblies-2019.png)
1. 프로젝트에 대해 다음 참조를 선택합니다.
   * [System.Runtime.Serialization](https://docs.microsoft.com/dotnet/api/system.runtime.serialization)
   * [System.Web](https://docs.microsoft.com/dotnet/api/system.web)
   * System.Web.Extensions
   * [System.Windows](https://docs.microsoft.com/dotnet/api/system.windows)
1. 이러한 참조가 프로젝트에 추가되면 **확인**을 클릭하여 **참조 관리자**를 닫을 수 있습니다.

> [!NOTE]
> 어셈블리 참조에 대한 자세한 내용은 [방법: 참조 관리자를 사용하여 참조 추가 또는 제거](https://docs.microsoft.com/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019)를 참조하세요.

### <a name="install-newtonsoftjson"></a>NewtonSoft.Json 설치

앱에서는 NewtonSoft.Json을 사용하여 JSON 개체를 역직렬화합니다. 다음 지침에 따라 패키지를 설치하세요.

1. Visual Studio의 솔루션 탐색기에서 프로젝트를 찾고 마우스 오른쪽 단추로 프로젝트를 클릭합니다. **NuGet 패키지 관리**를 선택합니다.
1. **찾아보기** 탭을 찾아서 선택합니다.
1. [NewtonSoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)을 검색 창에 입력합니다.

    ![NewtonSoft.Json 찾기 및 설치](media/nuget-package-manager.png)

1. 패키지를 선택하고 **설치**를 클릭합니다.
1. 설치가 완료되면 탭을 닫습니다.

## <a name="create-a-wpf-form-using-xaml"></a>XAML을 사용하여 WPF 양식 만들기

앱을 사용하려면 사용자 인터페이스가 필요합니다. XAML을 사용하여 사용자가 입력 및 번역 언어를 선택하고, 번역할 텍스트를 입력하고, 번역 출력을 표시할 수 있는 양식을 만듭니다.

작성하는 것에 대해 살펴보겠습니다.

![WPF XAML 사용자 인터페이스](media/translator-text-csharp-xaml.png)

사용자 인터페이스에 포함되는 구성 요소는 다음과 같습니다.

| 속성 | Type | Description |
|------|------|-------------|
| `FromLanguageComboBox` | ComboBox | Microsoft Translator에서 텍스트 번역에 지원하는 언어 목록을 표시합니다. 사용자가 번역할 원본 언어를 선택합니다. |
| `ToLanguageComboBox` | ComboBox | `FromComboBox`와 동일한 언어 목록을 표시하지만 사용자가 번역할 대상 언어를 선택하는 데 사용됩니다. |
| `TextToTranslate` | TextBox | 사용자가 번역할 텍스트를 입력할 수 있게 합니다. |
| `TranslateButton` | 단추 | 이 단추를 사용하여 텍스트를 번역합니다. |
| `TranslatedTextLabel` | 레이블 | 번역을 표시합니다. |
| `DetectedLanguageLabel` | 레이블 | 번역할 텍스트(`TextToTranslate`)에서 감지된 언어를 표시합니다. |

> [!NOTE]
> 이 양식은 XAML 소스 코드를 사용하여 만들지만, Visual Studio에서 편집기를 사용하여 만들 수 있습니다.

코드를 프로젝트에 추가해 보겠습니다.

1. Visual Studio에서 `MainWindow.xaml` 탭을 선택합니다.
1. 이 코드를 프로젝트에 복사한 다음, **파일 > MainWindow.xaml 저장**을 선택하여 변경 내용을 저장합니다.
   ```xaml
   <Window x:Class="MSTranslatorDemo.MainWindow"
           xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
           xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
           xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
           xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
           xmlns:local="clr-namespace:MSTranslatorDemo"
           mc:Ignorable="d"
           Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
       <Grid>
           <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
           <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
           <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
           <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

           <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
           <ComboBox x:Name="ToLanguageComboBox"
                   HorizontalAlignment="Left"
                   Margin="306,88,0,0"
                   VerticalAlignment="Top"
                   Width="175" FontSize="14" TabIndex="2">

           </ComboBox>
           <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
           <ComboBox x:Name="FromLanguageComboBox"
               HorizontalAlignment="Left"
               Margin="42,88,0,0"
               VerticalAlignment="Top"
               Width="175" FontSize="14" TabIndex="1"/>
           <Label x:Name="TranslatedTextLabel" Content="Translation is displayed here." HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
           <Label x:Name="DetectedLanguageLabel" Content="Autodetected language is displayed here." HorizontalAlignment="Left" Margin="39,288,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="84" BorderThickness="0"/>
       </Grid>
   </Window>
   ```
이제 Visual Studio에서 앱의 사용자 인터페이스에 대한 미리 보기가 표시됩니다. 위의 이미지와 비슷하게 표시됩니다.

양식이 준비되었습니다. 이제 Translator Text와 Bing Spell Check를 사용하는 코드를 작성해 보겠습니다.

> [!NOTE]
> 이 양식은 자유롭게 조정하거나 직접 만들 수 있습니다.

## <a name="create-your-app"></a>앱 만들기

`MainWindow.xaml.cs`에는 앱을 제어하는 코드가 포함되어 있습니다. 다음 몇 가지 섹션에서는 드롭다운 메뉴를 채우는 코드를 추가하고, Translator 및 Bing Spell Check에서 공개되는 약간의 API를 호출합니다.

* 프로그램이 시작되고 `MainWindow`가 인스턴스화되면 Translator의 `Languages` 메서드가 호출되어 언어 선택 드롭다운을 검색하고 채웁니다. 이는 각 세션이 시작될 때 한 번 발생합니다.
* **번역** 단추를 클릭하면 사용자의 언어 선택 및 텍스트가 검색되고, 입력에 대한 맞춤법 검사가 수행되며, 번역 및 감지된 언어가 사용자에게 표시됩니다.
  * `TextToTranslate`에서 텍스트를 번역하기 위해 Translator의 `Translate` 메서드를 호출합니다. 이 호출에는 드롭다운 메뉴를 사용하여 선택한 `to` 및 `from` 언어도 포함됩니다.
  * `TextToTranslate`의 텍스트 언어를 판별하기 위해 Translator의 `Detect` 메서드를 호출합니다.
  * Bing Spell Check은 `TextToTranslate`의 유효성을 검사하고 맞춤법 오류를 조정하는 데 사용됩니다.

모든 프로젝트가 `MainWindow : Window` 클래스에 캡슐화되어 있습니다. 먼저 구독 키를 설정하는 코드를 추가하고, Translator 및 Bing Spell Check에 대한 엔드포인트를 선언하고, 앱을 초기화하겠습니다.

1. Visual Studio에서 `MainWindow.xaml.cs` 탭을 선택합니다.
1. 미리 채워진 `using` 문을 다음 명령문으로 바꿉니다.  
   ```csharp
   using System;
   using System.Windows;
   using System.Net;
   using System.Net.Http;
   using System.IO;
   using System.Collections.Generic;
   using System.Linq;
   using System.Text;
   using Newtonsoft.Json;
   ```
1. `MainWindow : Window` 클래스를 찾아서 다음 코드로 바꿉니다.
   ```csharp
   {
       // This sample uses the Cognitive Services subscription key for all services. To learn more about
       // authentication options, see: https://docs.microsoft.com/azure/cognitive-services/authentication.
       const string COGNITIVE_SERVICES_KEY = "YOUR_COG_SERVICES_KEY";
       // Endpoints for Translator and Bing Spell Check
       public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
       const string BING_SPELL_CHECK_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/bing/v7.0/spellcheck/";
       // An array of language codes
       private string[] languageCodes;

       // Dictionary to map language codes from friendly name (sorted case-insensitively on language name)
       private SortedDictionary<string, string> languageCodesAndTitles =
           new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

       // Global exception handler to display error message and exit
       private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
       {
           Exception e = (Exception)args.ExceptionObject;
           MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
           System.Windows.Application.Current.Shutdown();
       }
       // MainWindow constructor
       public MainWindow()
       {
           // Display a message if unexpected error is encountered
           AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

           if (COGNITIVE_SERVICES_KEY.Length != 32)
           {
               MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                   "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                   "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
               System.Windows.Application.Current.Shutdown();
           }
           else
           {
               // Start GUI
               InitializeComponent();
               // Get languages for drop-downs
               GetLanguagesForTranslate();
               // Populate drop-downs with values from GetLanguagesForTranslate
               PopulateLanguageMenus();
           }
       }
   // NOTE:
   // In the following sections, we'll add code below this.
   }
   ```
1. Cognitive Services 구독 키를 추가하고 저장합니다.

이 코드 블록에서는 번역에 사용할 수 있는 언어에 대한 정보가 포함된 두 개의 멤버 변수를 선언했습니다.

| 변수 | Type | Description |
|----------|------|-------------|
|`languageCodes` | 문자열 배열 |언어 코드를 캐시합니다. Translator 서비스는 영어를 나타내는 `en`과 같은 짧은 코드를 사용하여 언어를 식별합니다. |
|`languageCodesAndTitles` | 정렬된 사전 | 사용자 인터페이스의 “친숙한” 이름을 API에 사용된 짧은 코드로 다시 매핑합니다. 대/소문자와 관계없이 사전순으로 정렬됩니다. |

다음으로, `MainWindow` 생성자 내에서 `HandleExceptions`를 사용하여 오류 처리를 추가했습니다. 이 오류 처리는 예외가 처리되지 않는 경우 경고가 제공되도록 합니다. 그런 다음, 제공된 구독 키의 길이가 32자인지 확인하는 검사가 실행됩니다. 키의 길이가 32자보다 작거나 크면 오류가 발생합니다.

최소한 적절한 길이의 키가 있으면 `InitializeComponent()` 호출에서 주 애플리케이션 창에 대한 XAML 설명을 찾아 로드하고 인스턴스화하여 사용자 인터페이스를 롤링합니다.

마지막으로, 번역 언어를 감지하고 앱의 사용자 인터페이스에 대한 드롭다운 메뉴를 채우는 메서드를 호출하는 코드를 추가했습니다. 걱정하지 마세요. 곧 이러한 호출 뒤에 코드로 이동하겠습니다.

## <a name="get-supported-languages"></a>지원되는 언어 가져오기

Translator는 현재 70개가 넘는 언어를 지원합니다. 시간이 지남에 따라 새로운 언어 지원이 추가되므로 앱의 언어 목록을 하드 코드하는 대신 Translator에서 공개되는 언어 리소스를 호출하는 것이 좋습니다.

이 섹션에서는 언어 리소스에 대한 `GET` 요청을 만들어 번역에 사용할 수 있는 언어 목록을 지정합니다.

> [!NOTE]
> 언어 리소스를 사용하여 음역, 사전 및 번역 쿼리 매개 변수를 사용하여 언어 지원을 필터링할 수 있습니다. 자세한 내용은 [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)를 참조하세요.

더 진행하기 전에 언어 리소스 호출에 대한 출력 샘플을 살펴보겠습니다.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    }
    // Additional languages are provided in the full JSON output.
}
```

이 출력에서는 특정 언어의 언어 코드와 `name`을 추출할 수 있습니다. 앱에서는 NewtonSoft.Json을 사용하여 JSON 개체([`JsonConvert.DeserializeObject`](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm))를 역직렬화합니다.

마지막 섹션에서 잠시 중단한 부분을 선택하여 지원되는 언어를 가져오는 메서드를 앱에 추가해 보겠습니다.

1. Visual Studio에서 `MainWindow.xaml.cs` 탭을 엽니다.
2. 다음 코드를 프로젝트에 추가합니다.
   ```csharp
   // ***** GET TRANSLATABLE LANGUAGE CODES
   private void GetLanguagesForTranslate()
   {
       // Send request to get supported language codes
       string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
       WebRequest WebRequest = WebRequest.Create(uri);
       WebRequest.Headers.Add("Accept-Language", "en");
       WebResponse response = null;
       // Read and parse the JSON response
       response = WebRequest.GetResponse();
       using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
       {
           var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
           var languages = result["translation"];

           languageCodes = languages.Keys.ToArray();
           foreach (var kv in languages)
           {
               languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
           }
       }
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

`GetLanguagesForTranslate()` 메서드는 HTTP GET 요청을 만들고, 요청 범위를 지원되는 번역 언어로 제한하는 데 사용되는 `scope=translation` 쿼리 문자열 매개 변수를 사용합니다. 지원되는 언어가 영어로 반환되도록 값이 `en`인 `Accept-Language` 헤더가 추가됩니다.

JSON 응답은 구문 분석되어 사전으로 변환됩니다. 그런 다음, 언어 코드가 `languageCodes` 멤버 변수에 추가됩니다. 언어 코드와 친숙한 언어 이름을 포함하는 키/값 쌍이 반복되고 `languageCodesAndTitles` 멤버 변수에 추가됩니다. 양식의 드롭다운 메뉴에는 이름이 표시되지만 번역을 요청하려면 코드가 필요합니다.

## <a name="populate-language-drop-down-menus"></a>언어 드롭다운 메뉴 채우기

사용자 인터페이스는 XAML을 사용하여 정의되므로 `InitializeComponent()` 호출 외에는 많이 설정할 필요가 없습니다. 수행해야 하는 한 가지 작업은 **원래 언어** 및 **번역 대상 언어** 드롭다운 메뉴에 언어 이름을 추가하는 것입니다. `PopulateLanguageMenus()` 메서드는 이름을 추가합니다.

1. Visual Studio에서 `MainWindow.xaml.cs` 탭을 엽니다.
2. 다음 코드를 `GetLanguagesForTranslate()` 메서드 아래의 프로젝트에 추가합니다.
   ```csharp
   private void PopulateLanguageMenus()
   {
       // Add option to automatically detect the source language
       FromLanguageComboBox.Items.Add("Detect");

       int count = languageCodesAndTitles.Count;
       foreach (string menuItem in languageCodesAndTitles.Keys)
       {
           FromLanguageComboBox.Items.Add(menuItem);
           ToLanguageComboBox.Items.Add(menuItem);
       }

       // Set default languages
       FromLanguageComboBox.SelectedItem = "Detect";
       ToLanguageComboBox.SelectedItem = "English";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

이 메서드는 `languageCodesAndTitles` 사전을 반복하며 각 키를 두 메뉴에 추가합니다. 메뉴가 채워지면 기본 원래 언어와 번역 대상 언어가 각각 **Detect** 및 **English**로 설정됩니다.

> [!TIP]
> 메뉴에 대해 기본 선택 사항을 설정하지 않을 경우 사용자는 "대상" 또는 "원본" 언어를 선택하지 않고 **번역**을 클릭할 수 있습니다. 기본값을 사용하면 이 문제를 처리할 필요가 없습니다.

`MainWindow`가 초기화되고 사용자 인터페이스가 만들어졌으므로 **번역** 단추를 클릭해야 이 코드가 실행됩니다.

## <a name="detect-language-of-source-text"></a>원본 텍스트 언어 감지

이제 Translator를 사용하여 원본 텍스트(텍스트 영역에 입력된 텍스트)의 언어를 감지하는 메서드를 만들어 보겠습니다. 이 요청으로 반환된 값은 나중에 번역 요청에 사용됩니다.

1. Visual Studio에서 `MainWindow.xaml.cs` 탭을 엽니다.
2. 다음 코드를 `PopulateLanguageMenus()` 메서드 아래의 프로젝트에 추가합니다.
   ```csharp
   // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
   private string DetectLanguage(string text)
   {
       string detectUri = string.Format(TEXT_TRANSLATION_API_ENDPOINT ,"detect");

       // Create request to Detect languages with Translator
       HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(detectUri);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
       detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
       detectLanguageWebRequest.ContentType = "application/json; charset=utf-8";
       detectLanguageWebRequest.Method = "POST";

       // Send request
       var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
       string jsonText = serializer.Serialize(text);

       string body = "[{ \"Text\": " + jsonText + " }]";
       byte[] data = Encoding.UTF8.GetBytes(body);

       detectLanguageWebRequest.ContentLength = data.Length;

       using (var requestStream = detectLanguageWebRequest.GetRequestStream())
           requestStream.Write(data, 0, data.Length);

       HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

       // Read and parse JSON response
       var responseStream = response.GetResponseStream();
       var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
       dynamic jsonResponse = serializer.DeserializeObject(jsonString);

       // Fish out the detected language code
       var languageInfo = jsonResponse[0];
       if (languageInfo["score"] > (decimal)0.5)
       {
           DetectedLanguageLabel.Content = languageInfo["language"];
           return languageInfo["language"];
       }
       else
           return "Unable to confidently detect input language.";
   }
   // NOTE:
   // In the following sections, we'll add code below this.
   ```

이 메서드는 Detect(감지) 리소스에 대한 HTTP `POST` 요청을 만듭니다. 요청 본문으로 전달되는 `text` 단일 인수를 사용합니다. 나중에 번역 요청을 만들면 언어 감지를 위해 UI에 입력한 텍스트가 이 메서드로 전달됩니다.

또한 이 메서드는 응답의 신뢰도 점수를 평가합니다. 점수가 `0.5`보다 크면 감지된 언어가 사용자 인터페이스에 표시됩니다.

## <a name="spell-check-the-source-text"></a>원본 텍스트 맞춤법 검사

이제 Bing Spell Check API를 사용하여 원본 텍스트의 맞춤법을 검사하는 메서드를 만들어 보겠습니다. 맞춤법 검사를 통해 Translator에서 정확한 번역을 얻을 수 있습니다. **번역** 단추를 클릭하면 원본 텍스트에 대한 모든 수정이 번역 요청에 전달됩니다.

1. Visual Studio에서 `MainWindow.xaml.cs` 탭을 엽니다.
2. 다음 코드를 `DetectLanguage()` 메서드 아래의 프로젝트에 추가합니다.

```csharp
// ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
private string CorrectSpelling(string text)
{
    string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

    // Create a request to Bing Spell Check API
    HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
    spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
    spellCheckWebRequest.Method = "POST";
    spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

    // Create and send the request
    string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
    byte[] data = Encoding.UTF8.GetBytes(body);
    spellCheckWebRequest.ContentLength = data.Length;
    using (var requestStream = spellCheckWebRequest.GetRequestStream())
        requestStream.Write(data, 0, data.Length);
    HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

    // Read and parse the JSON response; get spelling corrections
    var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
    var responseStream = response.GetResponseStream();
    var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
    dynamic jsonResponse = serializer.DeserializeObject(jsonString);
    var flaggedTokens = jsonResponse["flaggedTokens"];

    // Construct sorted dictionary of corrections in reverse order (right to left)
    // This ensures that changes don't impact later indexes
    var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
    for (int i = 0; i < flaggedTokens.Length; i++)
    {
        var correction = flaggedTokens[i];
        var suggestion = correction["suggestions"][0];  // Consider only first suggestion
        if (suggestion["score"] > (decimal)0.7)         // Take it only if highly confident
            corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
    }

    // Apply spelling corrections, in order, from right to left
    foreach (int i in corrections.Keys)
    {
        var oldtext = corrections[i][0];
        var newtext = corrections[i][1];

        // Apply capitalization from original text to correction - all caps or initial caps
        if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
        else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

        text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
    }
    return text;
}
// NOTE:
// In the following sections, we'll add code below this.
```

## <a name="translate-text-on-click"></a>클릭 시 텍스트 번역

수행해야 하는 마지막 작업은 사용자 인터페이스에서 **번역** 단추를 클릭할 때 호출되는 메서드를 만드는 것입니다.

1. Visual Studio에서 `MainWindow.xaml.cs` 탭을 엽니다.
1. 다음 코드를 `CorrectSpelling()` 메서드 아래의 프로젝트에 추가하고 저장합니다.  
   ```csharp
   // ***** PERFORM TRANSLATION ON BUTTON CLICK
   private async void TranslateButton_Click(object sender, EventArgs e)
   {
       string textToTranslate = TextToTranslate.Text.Trim();

       string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
       string fromLanguageCode;

       // auto-detect source language if requested
       if (fromLanguage == "Detect")
       {
           fromLanguageCode = DetectLanguage(textToTranslate);
           if (!languageCodes.Contains(fromLanguageCode))
           {
               MessageBox.Show("The source language could not be detected automatically " +
                   "or is not supported for translation.", "Language detection failed",
                   MessageBoxButton.OK, MessageBoxImage.Error);
               return;
           }
       }
       else
           fromLanguageCode = languageCodesAndTitles[fromLanguage];

       string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

       // spell-check the source text if the source language is English
       if (fromLanguageCode == "en")
       {
           if (textToTranslate.StartsWith("-"))    // don't spell check in this case
               textToTranslate = textToTranslate.Substring(1);
           else
           {
               textToTranslate = CorrectSpelling(textToTranslate);
               TextToTranslate.Text = textToTranslate;     // put corrected text into input field
           }
       }
       // handle null operations: no text or same source/target languages
       if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
       {
           TranslatedTextLabel.Content = textToTranslate;
           return;
       }

       // send HTTP request to perform the translation
       string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
       string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

       System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
       var requestBody = JsonConvert.SerializeObject(body);

       using (var client = new HttpClient())
       using (var request = new HttpRequestMessage())
       {
           request.Method = HttpMethod.Post;
           request.RequestUri = new Uri(uri);
           request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
           request.Headers.Add("Ocp-Apim-Subscription-Key", COGNITIVE_SERVICES_KEY);
           request.Headers.Add("Ocp-Apim-Subscription-Region", "westus");
           request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

           var response = await client.SendAsync(request);
           var responseBody = await response.Content.ReadAsStringAsync();

           var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
           var translation = result[0]["translations"][0]["text"];

           // Update the translation field
           TranslatedTextLabel.Content = translation;
       }
   }
   ```

첫 번째 단계는 "from"(원래) 및 "to"(번역 대상) 언어와 사용자가 양식에 입력한 텍스트를 가져오는 것입니다. 원본 언어가 **Detect**로 설정되면 원본 텍스트의 언어를 판별하기 위해 `DetectLanguage()`이 호출됩니다. 텍스트는 Translator에서 지원하지 않는 언어로 되어 있을 수 있습니다. 이 경우 메시지를 표시하여 사용자에게 알리고, 텍스트를 번역하지 않고 돌아갑니다.

지정 또는 감지되었는지에 관계없이 원본 언어가 영어인 경우 `CorrectSpelling()`을 사용하여 텍스트 맞춤법을 검사하고 수정 사항을 적용합니다. 수정된 텍스트가 텍스트 영역에 다시 추가되므로 사용자가 수정되었음을 알 수 있습니다.

텍스트를 번역하는 코드에 익숙해야 합니다. 이 코드는 URI를 작성하고, 요청을 만들어 보내고, 응답을 구문 분석합니다. JSON 배열에는 둘 이상의 번역 개체가 포함될 수 있지만, 앱에는 하나만 필요합니다.

요청이 성공하면 `TranslatedTextLabel.Content`가 번역된 텍스트를 표시하도록 사용자 인터페이스를 업데이트하는 `translation`으로 바뀝니다.

## <a name="run-your-wpf-app"></a>WPF 앱 실행

모든 작업이 완료되었습니다. 이제는 WPF를 사용하여 작동하는 번역 앱이 있습니다. 앱을 실행하려면 Visual Studio에서 **시작** 단추를 클릭합니다.

## <a name="source-code"></a>소스 코드

이 프로젝트에 대한 소스 코드는 GitHub에서 사용할 수 있습니다.

* [소스 코드 검색](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp-Tutorial)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Microsoft Translator 참조](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
