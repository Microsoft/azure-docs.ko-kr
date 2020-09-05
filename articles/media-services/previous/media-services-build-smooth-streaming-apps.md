---
title: 부드러운 스트리밍 Windows 스토어 앱 자습서 | Microsoft 문서
description: Azure Media Services를 사용하여 부드러운 스트림 콘텐츠를 재생하기 위해 XML MediaElement 컨트롤이 포함된 C# Windows 스토어 애플리케이션을 만드는 방법에 대해 알아봅니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b616b03698e83d256a7b81e4548edbf21dba058
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264643"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>부드러운 스트리밍 Windows 스토어 애플리케이션을 빌드하는 방법

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Smooth Streaming Client SDK for Windows 8을 사용하면 개발자가 주문형 및 Live Smooth Streaming 콘텐츠를 재생할 수 있는 Windows 스토어 애플리케이션을 빌드할 수 있습니다. 부드러운 스트리밍 콘텐츠의 기본 재생뿐 아니라 SDK는 Microsoft PlayReady 보호, 품질 수준 제한, Live DVR, 오디오 스트림 전환, 상태 업데이트(예: 품질 수준 변경) 수신 대기, 오류 이벤트 등의 풍부한 기능도 제공합니다. 지원되는 기능에 대한 자세한 내용은 [릴리스 정보](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)를 참조하십시오. 자세한 내용은 [Windows 8용 플레이어 프레임워크](https://playerframework.codeplex.com/)를 참조하세요. 

이 자습서에는 4개 단원이 포함되어 있습니다.

1. 기본 부드러운 스트리밍 스토어 애플리케이션 만들기
2. 미디어 진행을 제어하는 슬라이더 막대 추가
3. 부드러운 스트리밍 스트림 선택
4. 부드러운 스트리밍 트랙 선택

## <a name="prerequisites"></a>필수 구성 요소
> [!NOTE]
> Windows 스토어 프로젝트 버전 8.1 및 이전 버전은 Visual Studio 2017에서 지원되지 않습니다.  자세한 내용은 [Visual Studio 2017 플랫폼 대상 지정 및 호환성](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)을 참조하세요.

* Windows 8 32비트 또는 64비트.
* Visual Studio 버전 2012~2015.
* [Microsoft Smooth Streaming Client SDK for Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home)

각 단원에 대해 완성된 솔루션은 MSDN 개발자 코드 샘플(코드 갤러리)에서 다운로드할 수 있습니다. 

* [단원 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) - 간단한 Windows 8 부드러운 스트리밍 Media Player 
* [단원 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) - 슬라이더 막대 컨트롤이 있는 간단한 Windows 8 부드러운 스트리밍 Media Player 
* [단원 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) - 스트림 선택 항목이 있는 Windows 8 부드러운 스트리밍 Media Player  
* [단원 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907) - 트랙 선택 항목이 있는 Windows 8 부드러운 스트리밍 Media Player

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>단원 1: 기본 부드러운 스트리밍 스토어 애플리케이션 만들기

이 단원에서는 부드러운 스트림 콘텐츠를 재생하기 위해 MediaElement 컨트롤이 포함된 Windows 스토어 애플리케이션을 만듭니다.  실행 중인 애플리케이션은 다음과 같이 표시됩니다.

![부드러운 스트리밍 Windows 스토어 애플리케이션 예][PlayerApplication]

Windows 스토어 애플리케이션 개발에 대한 자세한 내용은 [유용한 Windows 8용 앱 개발](https://developer.microsoft.com/en-us/windows/)을 참조하십시오. 이 단원에는 다음 절차가 포함되어 있습니다.

1. Windows 스토어 프로젝트 만들기
2. 사용자 인터페이스 디자인(XAML)
3. 코드 숨김 파일 수정
4. 애플리케이션 컴파일 및 테스트

### <a name="to-create-a-windows-store-project"></a>Windows 스토어 프로젝트를 만들려면

1. Visual Studio 실행(2012~2015 버전이 지원됨).
1. **파일** 메뉴에서 **새로 만들기**를 클릭 한 다음 **프로젝트**를 클릭 합니다.
1. [새 프로젝트] 대화 상자에서 다음 값을 입력하거나 선택합니다.

    | Name | 값 |
    | --- | --- |
    | 템플릿 그룹 |Installed/Templates/Visual C#/Windows Store |
    | 템플릿 |새 응용 프로그램(XAML) |
    | Name |SSPlayer |
    | 위치 |C:\SSTutorials |
    | 솔루션 이름 |SSPlayer |
    | 솔루션에 대한 디렉터리 만들기 |(선택됨) |

1. **확인**을 클릭합니다.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>부드러운 스트리밍 클라이언트 SDK에 대한 참조를 추가하려면

1. [솔루션 탐색기]에서 **SSPlayer**를 마우스 오른쪽 단추로 클릭하고 **참조 추가**를 클릭합니다.
1. 다음 값을 입력하거나 선택합니다.

    | Name | 값 |
    | --- | --- |
    | 참조 그룹 |Windows/Extensions |
    | 참조 |Microsoft Smooth Streaming Client SDK for Windows 8 및 Microsoft Visual C++ 런타임 패키지 선택 |

1. **확인**을 클릭합니다. 

참조를 추가한 후 대상 플랫폼(x64 또는 x86)을 선택해야 합니다. 임의 CPU 플랫폼 구성에서는 참조 추가가 작동하지 않습니다.  솔루션 탐색기에서 추가된 이 참조에 대해 노란색 경고 표시가 나타납니다.

### <a name="to-design-the-player-user-interface"></a>플레이어 사용자 인터페이스를 디자인하려면

1. 솔루션 탐색기에서 **MainPage.xaml** 을 두 번 클릭하여 디자인 보기에서 엽니다.
2. ** &lt; 표 &gt; ** 및/또는 ** &lt; 표 &gt; ** 태그를 찾아서 XAML 파일에 붙여 넣고 다음 코드를 두 태그 사이에 붙여넣습니다.

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   MediaElement 컨트롤은 미디어를 재생하는 데 사용됩니다. sliderProgress라는 슬라이더 컨트롤은 다음 단원에서 미디어 진행을 제어하는 데 사용됩니다.
3. **Ctrl + S** 를 눌러 파일을 저장 합니다.

MediaElement 컨트롤은 기본적으로 부드러운 스트리밍 콘텐츠를 지원하지 않습니다. 부드러운 스트리밍 지원을 사용하도록 설정하려면 파일 이름 확장명과 MIME 형식으로 부드러운 스트리밍 바이트 스트림 처리기를 등록해야 합니다.  등록하려면 Windows.Media 네임스페이스의 MediaExtensionManager.RegisterByteStreamHandler 메서드를 사용합니다.

이 XAML 파일에서는 일부 이벤트 처리기가 컨트롤과 연결되어 있습니다.  해당 이벤트 처리기를 정의해야 합니다.

### <a name="to-modify-the-code-behind-file"></a>코드 숨김 파일을 수정하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. 파일 맨 위에 다음 using 문을 추가합니다.

    ```csharp
        using Windows.Media;
    ```

3. **MainPage** 클래스의 시작 부분에 다음 데이터 멤버를 추가합니다.

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. **MainPage** 생성자의 끝에 다음 두 줄을 추가합니다.

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. **MainPage** 클래스의 끝에 다음 코드를 붙여 넣습니다.
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   여기에는 sliderProgress_PointerPressed 이벤트 처리기가 정의되어 있습니다.  작동하는 데 필요한 추가 작업이 있으며, 이 자습서의 다음 단원에서 설명합니다.
6. **Ctrl + S** 를 눌러 파일을 저장 합니다.

완성된 코드 숨김 파일은 다음과 같이 표시됩니다.

![부드러운 스트리밍 Windows 스토어 애플리케이션에 대한 Visual Studio의 Codeview][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>애플리케이션을 컴파일 및 테스트하려면

1. **빌드** 메뉴에서 **구성 관리자**를 클릭합니다.
2. 개발 플랫폼과 일치하도록 **활성 솔루션 플랫폼** 을 변경합니다.
3. **F6** 키를 눌러 프로젝트를 컴파일합니다. 
4. **F5** 키를 눌러 애플리케이션을 실행합니다.
5. 애플리케이션 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다. 
6. **원본 설정**을 클릭합니다. 기본적으로 **자동 재생** 이 사용되기 때문에 미디어가 자동으로 재생됩니다.  **재생**, **일시 중지** 및 **중지** 단추를 사용하여 미디어를 제어할 수 있습니다.  세로 슬라이더를 사용하여 미디어 볼륨을 제어할 수 있습니다.  그러나 미디어 진행을 제어하기 위한 가로 슬라이더는 아직 완전히 구현되지 않았습니다. 

lesson1을 완성했습니다.  이 단원에서는 MediaElement 컨트롤을 사용하여 부드러운 스트리밍 콘텐츠를 재생합니다.  다음 단원에서는 부드러운 스트리밍 콘텐츠 진행을 제어하는 슬라이더를 추가합니다.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>단원 2: 미디어 진행을 제어하는 슬라이더 막대 추가

단원 1에서는 부드러운 스트리밍 미디어 콘텐츠를 재생하기 위해 MediaElement XAML 컨트롤이 포함된 Windows 스토어 애플리케이션을 만들었습니다.  시작, 중지, 일시 중지 등의 기본 미디어 기능이 제공됩니다.  이 단원에서는 애플리케이션에 슬라이더 막대 컨트롤을 추가합니다.

이 자습서에서는 타이머를 사용하여 MediaElement 컨트롤의 현재 위치에 따라 슬라이더 위치를 업데이트합니다.  라이브 콘텐츠의 경우 슬라이더 시작 및 종료 시간도 업데이트해야 합니다.  이 작업은 적응 원본 업데이트 이벤트를 통해 더 효율적으로 처리할 수 있습니다.

미디어 원본은 미디어 데이터를 생성하는 개체입니다.  원본 확인자는 URL 또는 바이트 스트림을 사용하고 해당 콘텐츠에 적합한 미디어 원본을 만듭니다.  원본 확인자는 애플리케이션에서 미디어 원본을 만드는 표준 방법입니다. 

이 단원에는 다음 절차가 포함되어 있습니다.

1. 부드러운 스트리밍 처리기 등록 
2. 적응 원본 관리자 수준 이벤트 처리기 추가
3. 적응 원본 수준 이벤트 처리기 추가
4. MediaElement 이벤트 처리기 추가
5. 슬라이더 막대 관련 코드 추가
6. 애플리케이션 컴파일 및 테스트

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>부드러운 스트리밍 바이트 스트림 처리기를 등록하고 propertyset를 전달하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. 파일의 시작 부분에 다음 using 문을 추가합니다.

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. MainPage 클래스의 시작 부분에 다음 데이터 멤버를 추가합니다.

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. **MainPage** 생성자 내에서 **this.Initialize Components();** 줄과 이전 단원에서 작성한 등록 코드 줄 뒤에 다음 코드를 추가합니다.

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. **MainPage** 생성자 내에서 RegisterByteStreamHandler 메서드 두 개를 수정하여 네 번째 매개 변수를 추가합니다.

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. **Ctrl + S** 를 눌러 파일을 저장 합니다.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>적응 원본 관리자 수준 이벤트 처리기를 추가하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. **MainPage** 클래스 내에 다음 데이터 멤버를 추가합니다.

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. **MainPage** 클래스의 끝에 다음 이벤트 처리기를 추가합니다.

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. **MainPage** 생성자의 끝에 다음 줄을 추가하여 적응 원본 열기 이벤트를 구독합니다.

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. **Ctrl + S** 를 눌러 파일을 저장 합니다.

### <a name="to-add-adaptive-source-level-event-handlers"></a>적응 원본 수준 이벤트 처리기를 추가하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. **MainPage** 클래스 내에 다음 데이터 멤버를 추가합니다.

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. **MainPage** 클래스의 끝에 다음 이벤트 처리기를 추가합니다.

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. **mediaElement AdaptiveSourceOpened** 메서드의 끝에 다음 코드를 추가하여 이벤트를 구독합니다.

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. **Ctrl + S** 를 눌러 파일을 저장 합니다.

앱의 모든 미디어 요소에 공통된 기능을 처리하는 데 사용할 수 있는 적응 원본 관리자 수준에서도 동일한 이벤트를 사용할 수 있습니다. 각 AdaptiveSource에는 고유한 이벤트가 포함되어 있으며 모든 AdaptiveSource 이벤트가 AdaptiveSourceManager 아래에 계단식으로 표시됩니다.

### <a name="to-add-media-element-event-handlers"></a>미디어 요소 이벤트 처리기를 추가하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. **MainPage** 클래스의 끝에 다음 이벤트 처리기를 추가합니다.

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. **MainPage** 생성자의 끝에 다음 코드를 추가하여 이벤트를 구독합니다.

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. **Ctrl + S** 를 눌러 파일을 저장 합니다.

### <a name="to-add-slider-bar-related-code"></a>슬라이더 막대 관련 코드를 추가하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. 파일의 시작 부분에 다음 using 문을 추가합니다.

   ```csharp
        using Windows.UI.Core;
   ```
3. **MainPage** 클래스 내에 다음 데이터 멤버를 추가합니다.

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. **MainPage** 생성자의 끝에 다음 코드를 추가합니다.

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. **MainPage** 클래스의 끝에 다음 코드를 추가합니다.

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher는 비UI 스레드에서 UI 스레드를 변경하는 데 사용됩니다. 디스패처 스레드에 병목 현상이 발생 하는 경우 개발자는 업데이트 하려는 UI 요소에서 제공 하는 디스패처를 사용 하도록 선택할 수 있습니다.  예를 들면 다음과 같습니다.

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. **mediaElement_AdaptiveSourceStatusUpdated** 메서드의 끝에 다음 코드를 추가합니다.

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. **MediaOpened** 메서드의 끝에 다음 코드를 추가합니다.

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. **Ctrl + S** 를 눌러 파일을 저장 합니다.

### <a name="to-compile-and-test-the-application"></a>애플리케이션을 컴파일 및 테스트하려면

1. **F6** 키를 눌러 프로젝트를 컴파일합니다. 
2. **F5** 키를 눌러 애플리케이션을 실행합니다.
3. 애플리케이션 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다. 
4. **원본 설정**을 클릭합니다. 
5. 슬라이더 막대를 테스트합니다.

단원 2를 완료했습니다.  이 단원에서는 애플리케이션에 슬라이더를 추가했습니다. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>단원 3: 부드러운 스트리밍 스트림 선택
부드러운 스트리밍은 사용자가 선택할 수 있는 여러 언어 오디오 트랙으로 콘텐츠를 스트림할 수 있습니다.  이 단원에서는 사용자가 스트림을 선택할 수 있도록 설정합니다. 이 단원에는 다음 절차가 포함되어 있습니다.

1. XAML 파일 수정
2. 코드 숨김 파일 수정
3. 애플리케이션 컴파일 및 테스트

### <a name="to-modify-the-xaml-file"></a>XAML 파일을 수정하려면

1. 솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 클릭합니다.
2. &lt;Grid.RowDefinitions&gt;를 찾아 RowDefinitions를 다음과 같이 수정합니다.

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. 사용 가능한 스트림 목록을 보고 스트림을 선택할 수 있도록 &lt;Grid&gt;&lt;/Grid&gt; 태그 안에 다음 코드를 추가하여 목록 상자 컨트롤을 정의합니다.

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. **Ctrl+S** 를 눌러 변경 내용을 저장합니다.

### <a name="to-modify-the-code-behind-file"></a>코드 숨김 파일을 수정하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. SSPlayer 네임스페이스 내에 새 클래스를 추가합니다.

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. MainPage 클래스의 시작 부분에 다음 변수 정의를 추가합니다.

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. MainPage 클래스 내에 다음 지역을 추가합니다.
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. mediaElement_ManifestReady 메서드를 찾은 후 함수의 끝에 다음 코드를 추가합니다.
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    따라서 MediaElement 매니페스트가 준비되면 코드가 사용 가능한 스트림 목록을 가져오고 UI 목록 상자에 목록을 채웁니다.
6. MainPage 클래스 내에서 UI 단추 클릭 이벤트 지역을 찾은 후 다음 함수 정의를 추가합니다.
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>애플리케이션을 컴파일 및 테스트하려면

1. **F6** 키를 눌러 프로젝트를 컴파일합니다. 
2. **F5** 키를 눌러 애플리케이션을 실행합니다.
3. 애플리케이션 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다. 
4. **원본 설정**을 클릭합니다. 
5. 기본 언어는 audio_eng입니다. audio_eng와 audio_es 간에 전환해 보세요. 새 스트림을 선택할 때마다 제출 단추를 클릭해야 합니다.

단원 3을 완료했습니다.  이 단원에서는 스트림을 선택하는 기능을 추가합니다.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>4 단원: 부드러운 스트리밍 트랙 선택

부드러운 스트리밍 프레젠테이션에는 여러 품질 수준(비트 전송률) 및 해상도로 인코딩된 여러 비디오 파일이 포함될 수 있습니다. 이 단원에서는 사용자가 트랙을 선택할 수 있도록 설정합니다. 이 단원에는 다음 절차가 포함되어 있습니다.

1. XAML 파일 수정
2. 코드 숨김 파일 수정
3. 애플리케이션 컴파일 및 테스트

### <a name="to-modify-the-xaml-file"></a>XAML 파일을 수정하려면

1. 솔루션 탐색기에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **디자이너 보기**를 클릭합니다.
2. 이름이 **gridStreamAndBitrateSelection**인 &lt;Grid&gt; 태그를 찾아 그 끝에 다음 코드를 추가합니다.
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. **Ctrl + S** 를 눌러 변경 내용을 저장 합니다.

### <a name="to-modify-the-code-behind-file"></a>코드 숨김 파일을 수정하려면

1. [솔루션 탐색기]에서 **MainPage.xaml**을 마우스 오른쪽 단추로 클릭하고 **코드 보기**를 클릭합니다.
2. SSPlayer 네임스페이스 내에 새 클래스를 추가합니다.
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. MainPage 클래스의 시작 부분에 다음 변수 정의를 추가합니다.
   ```csharp
        private List<Track> availableTracks;
   ```
4. MainPage 클래스 내에 다음 지역을 추가합니다.
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. mediaElement_ManifestReady 메서드를 찾은 후 함수의 끝에 다음 코드를 추가합니다.
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. MainPage 클래스 내에서 UI 단추 클릭 이벤트 지역을 찾은 후 다음 함수 정의를 추가합니다.
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>애플리케이션을 컴파일 및 테스트하려면

1. **F6** 키를 눌러 프로젝트를 컴파일합니다. 
2. **F5** 키를 눌러 애플리케이션을 실행합니다.
3. 애플리케이션 맨 위에 기본 부드러운 스트리밍 URL을 사용하거나 다른 URL을 입력할 수 있습니다. 
4. **원본 설정**을 클릭합니다. 
5. 기본적으로 비디오 스트림의 모든 트랙이 선택됩니다. 비트 전송률 변경을 실험하려면 사용 가능한 가장 낮은 비트 전송률을 선택한 후 사용 가능한 가장 높은 비트 전송률을 선택합니다. 각 변경 후에 제출을 클릭해야 합니다.  비디오 품질 변경을 확인할 수 있습니다.

단원 4를 완료했습니다.  이 단원에서는 트랙을 선택하는 기능을 추가합니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>기타 리소스:
* [고급 기능이 포함된 부드러운 스트리밍 Windows 8 JavaScript 애플리케이션을 빌드하는 방법](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [부드러운 스트리밍 기술 개요](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
