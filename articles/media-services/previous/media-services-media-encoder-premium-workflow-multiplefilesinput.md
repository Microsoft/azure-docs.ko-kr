---
title: 프리미엄 인코더를 통한 여러 입력 파일 및 구성 요소 속성 - Azure | Microsoft 문서
description: 이 항목에서는 setRuntimeProperties를 사용하여 여러 입력 파일을 사용하고 사용자 지정 데이터를 미디어 인코더 Premium 워크플로 미디어 프로세서에 전달하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 608ca4bc3b58dd3c718d6239f90260154d2f6c3a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465536"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>프리미엄 인코더로 여러 입력 파일 및 구성 요소 속성 사용
## <a name="overview"></a>개요
**미디어 인코더 프리미엄 워크플로** 미디어 프로세서로 작업을 제출할 때 구성 요소 속성을 사용자 지정하고 클립 목록 XML 콘텐츠를 지정하거나 여러 입력 파일을 전송해야 하는 시나리오가 있습니다. 일부 사례:

* 각 입력 비디오에 대해 런타임에(예: 현재 날짜) 비디오 위에 텍스트 오버레이 및 텍스트 값 설정
* 클립 목록 XML 사용자 지정(잘라내기 등을 사용/사용하지 않고 하나 이상의 소스 파일 지정)
* 비디오를 인코딩하는 동안 입력 비디오에 로고 이미지 오버레이
* 다중 오디오 언어 인코딩

작업을 만들거나 여러 입력 파일을 전송할 때 **미디어 인코더 프리미엄 워크플로**에 사용자가 워크플로의 일부 속성을 변경하고 있음을 알리려면 **setRuntimeProperties** 및/또는 **transcodeSource**를 포함하는 구성 문자열을 사용해야 합니다. 이 항목에서는 이를 사용하는 방법을 설명합니다.

## <a name="configuration-string-syntax"></a>구성 문자열 구문
인코딩 작업에서 설정할 구성 문자열은 다음과 같은 XML 문서를 사용합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

다음은 파일에서 XML 구성을 읽고 적절한 비디오 파일 이름으로 업데이트한 후 해당 작업에 전달하는 C# 코드입니다.

```csharp
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>구성 요소 속성 사용자 지정
### <a name="property-with-a-simple-value"></a>간단한 값이 있는 속성
일부 경우 구성 요소 속성을 미디어 인코더 Premium 워크플로에서 실행할 워크플로 파일과 함께 사용자 지정하는 것이 좋습니다.

비디오에 텍스트를 오버레이하는 워크플로를 지정했고 텍스트(예: 현재 날짜)가 런타임에 설정된다고 가정합니다. 인코딩 작업에서 오버레이 구성 요소의 텍스트 속성에 대한 새 값으로 설정할 텍스트를 전송하여 이 작업을 수행할 수 있습니다. 이 메커니즘을 사용하여 워크플로 구성 요소의 다른 속성(예: 오버레이의 위치 또는 색, AVC 인코더의 비트 전송률 등)을 변경할 수 있습니다.

**setRuntimeProperties** 가 사용됩니다.

예제:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>XML 값이 있는 속성
XML 값이 예상되는 속성을 설정하려면 `<![CDATA[ and ]]>`를 사용하여 캡슐화하세요.

예제:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> `<![CDATA[` 바로 뒤에 캐리지 리턴을 두지 마세요.

### <a name="propertypath-value"></a>propertyPath 값
이전 예제에서 propertyPath는 "/Media File Input/filename", "/inactiveTimeout", "clipListXml"였습니다.
일반적으로 구성 요소의 이름과 속성의 이름입니다. 경로는 "/primarySourceFile"(속성이 워크플로의 루트에 있으므로) 또는 "/Video Processing/Graphic Overlay/Opacity"(오버레이가 그룹에 있으므로)처럼 더 많거나 적은 수준을 포함할 수 있습니다.    

경로 및 속성 이름을 확인하려면 각 속성 바로 옆의 동작 버튼을 사용합니다. 이 동작 버튼을 클릭하고 **편집**을 선택합니다. 그러면 속성의 실제 이름이 네임스페이스 바로 위에 표시됩니다.

![작업/편집](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![자산](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>여러 입력 파일
**미디어 인코더 Premium 워크플로** 에 제출하는 각 작업에는 두 개의 자산이 필요합니다.

* 첫 번째 자산은 워크플로 파일을 포함하는 *워크플로 자산* 입니다. [워크플로 디자이너](media-services-workflow-designer.md)를 사용하여 워크플로 파일을 디자인할 수 있습니다.
* 두 번째 자산은 인코딩할 미디어 파일을 포함하는 *미디어 자산* 입니다.

여러 미디어 파일을 **미디어 인코더 Premium 워크플로** 인코더에 전송하는 경우 다음 제약 조건이 적용됩니다.

* 모든 미디어 파일이 동일한 *미디어 자산*이어야 합니다. 여러 미디어 자산의 사용은 지원되지 않습니다.
* 이 미디어 자산에서 기본 파일을 설정해야 합니다(이상적으로 인코더가 처리를 요청하는 기본 비디오 파일).
* **setRuntimeProperties** 및/또는 **transcodeSource** 요소를 포함하는 구성 데이터를 프로세서에 전달하는 데 필요합니다.
  * **setRuntimeProperties** 가 사용됩니다.
  * **transcodeSource** 가 사용됩니다.

워크플로에서 연결:

* 하나 이상의 미디어 파일 입력 구성 요소를 사용하고 파일 이름을 지정하는 데 **setRuntimeProperties** 를 사용할 계획인 경우 여기에 기본 파일 구성 요소 PIN을 연결하지 마세요. 기본 파일 개체와 미디어 파일 입력 간에 연결이 없는지 확인하세요.
* 클립 목록 XML과 하나의 미디어 원본 구성 요소를 사용하려는 경우 둘 다를 함께 연결할 수 있습니다.

![기본 원본 파일에서 미디어 파일 입력으로 연결 없음](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*파일 이름 속성을 설정하기 위해 setRuntimeProperties를 사용하는 경우 기본 파일에서 미디어 파일 입력 구성 요소로 연결이 없습니다.*

![클립 목록 XML에서 클립 목록 원본으로 연결](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*클립 목록 XML을 미디어 원본에 연결하고 transcodeSource를 사용할 수 있습니다.*

### <a name="clip-list-xml-customization"></a>클립 목록 XML 사용자 지정
구성 문자열 XML에서 **transcodeSource** 를 사용하여 워크플로에서 런타임에 클립 목록 XML을 지정할 수 있습니다. 이를 위해서는 클립 목록 XML 핀을 워크플로에서 미디어 원본 구성 요소에 연결해야 합니다.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

'Expressions'를 사용하여 출력 파일의 이름을 지정하는 데 이 속성을 사용하도록 /primarySourceFile을 지정하는 경우, 클립 목록이 /primarySourceFile 설정으로 재정의되지 않도록 클립 목록 XML을 /primarySourceFile 속성 *이후의* 속성으로 전달하는 것이 좋습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

추가 프레임 정확한 트리밍 사용:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>예제 1: 비디오 위에 이미지 오버레이

### <a name="presentation"></a>프레젠테이션
비디오를 인코딩하는 동안 로고 이미지를 입력 비디오에 오버레이하는 예를 살펴보겠습니다. 이 예제에서 입력 비디오의 이름은 "Microsoft_HoloLens_Possibilities_816p24.mp4", 로고 이름은 "logo.png"로 지정합니다. 다음 단계를 수행해야 합니다.

* 워크플로 파일로 워크플로 자산 만들기(아래 예제 참조)
* 다음 두 파일을 포함하는 미디어 자산 만들기: MyInputVideo.mp4(기본 파일) 및 MyLogo.png.
* 위의 입력 자산과 함께 미디어 인코더 Premium 워크플로 미디어 프로세서에 작업 보내기 및 다음 구성 문자열 지정

구성:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

위 예제에서 비디오 파일의 이름이 미디어 파일 입력 구성 요소 및 primarySourceFile 속성으로 전송됩니다. 로고 파일의 이름은 그래픽 오버레이 구성 요소에 연결되어 있는 다른 미디어 파일 입력으로 전송됩니다.

> [!NOTE]
> 비디오 파일 이름은 primarySourceFile 속성에 전송됩니다. 그 이유는 예를 들어 Expressions를 사용하여 올바른 출력 파일 이름을 작성하기 위해 워크플로에서 이 속성을 사용해야 하기 때문입니다.

### <a name="step-by-step-workflow-creation"></a>단계별 워크플로 만들기
입력으로 두 개의 파일(비디오 및 이미지)을 사용하는 워크플로를 만드는 단계는 다음과 같습니다. 비디오 맨 위에서 이미지를 오버레이합니다.

**워크플로 디자이너**를 열고 **파일** > **새 작업 영역** > **청사진 트랜스코딩**을 선택합니다.

새 워크플로는 3개의 요소를 보여 줍니다.

* 기본 원본 파일
* 클립 목록 XML
* 출력 파일/자산  

![새 Encoding 워크플로](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*새 Encoding 워크플로*

입력 미디어 파일을 허용하기 위해 미디어 파일 입력 구성 요소를 추가하기 시작합니다. 워크플로에 구성 요소를 추가하려면 리포지토리 검색 상자에서 구성 요소를 찾고 디자이너 창으로 원하는 항목을 끕니다.

다음으로, 워크플로 디자인에 사용할 비디오 파일을 추가합니다. 이렇게 하려면 워크플로 디자이너에서 배경 창을 클릭하고 오른쪽 속성 창에서 기본 원본 파일 속성을 찾습니다. 폴더 아이콘을 클릭하고 적절한 비디오 파일을 선택합니다.

![기본 파일 원본](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*기본 파일 원본*

다음으로, 미디어 파일 입력 구성 요소에서 비디오 파일을 지정합니다.   

![미디어 파일 입력 원본](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*미디어 파일 입력 원본*

이 작업이 완료되는 즉시 미디어 파일 입력 구성 요소는 파일을 검사하고 해당 출력 핀을 채워서 검사한 파일을 반영합니다.

다음 단계는 "비디오 데이터 형식 업데이터"를 추가하여 색 공간을 Rec.709로 지정하는 것입니다. 데이터 레이아웃/레이아웃 형식 = 구성 가능한 평면으로 설정된 "비디오 형식 변환기"를 추가합니다. 그러면 비디오 스트림이 오버레이 구성 요소의 원본으로 사용할 수 있는 형식으로 변환됩니다.

![비디오 데이터 형식 업데이터 및 형식 변환기](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*비디오 데이터 형식 업데이터 및 형식 변환기*

![레이아웃 형식 = 구성 가능한 평면](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*구성 가능한 평면의 레이아웃 형식*

다음으로, 비디오 오버레이 구성 요소를 추가하고 (압축되지 않은) 비디오 핀을 미디어 파일 입력의 (압축되지 않은) 비디오 핀에 연결합니다.

다른 미디어 파일 입력(로고 파일 로드)을 추가하고 이 구성 요소를 클릭한 후 "미디어 파일 입력 로고"로 이름을 변경하고 파일 속성에서 이미지(예를 들어 png 파일)를 선택합니다. 압축되지 않은 이미지 핀을 오버레이의 압축되지 않은 이미지 핀에 연결합니다.

![오버레이 구성 요소 및 이미지 파일 원본](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*오버레이 구성 요소 및 이미지 파일 원본*

비디오에서 로고의 위치를 수정하려면(예를 들어 비디오의 왼쪽 상단에서 10% 떨어진 위치로 지정할 수 있음) "수동 입력" 확인란의 선택을 취소합니다. 오버레이 구성 요소에 로고 파일을 제공하는 데 미디어 파일 입력을 사용하고 있으므로 이와 같은 동작이 가능합니다.

![오버레이 위치](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*오버레이 위치*

비디오 스트림을 H.264로 인코딩하려면 AVC 비디오 인코더 및 AAC 인코더 구성 요소를 디자이너 화면에 추가합니다. 핀을 연결합니다.
AAC 인코더를 설정하고 오디오 형식 변환/사전 설정 선택: 2.0(L, R)을 선택합니다.

![오디오 및 비디오 인코더](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*오디오 및 비디오 인코더*

이제 **ISO Mpeg-4 멀티플렉서** 및 **파일 출력** 구성 요소를 추가하고 표시된 것과 같이 핀을 연결합니다.

![MP4 멀티플렉서 및 파일 출력](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 멀티플렉서 및 파일 출력*

출력 파일의 이름을 설정해야 합니다. **파일 출력** 구성 요소를 클릭하고 파일에 대한 식을 다음과 같이 편집합니다.

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![파일 출력 이름](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*파일 출력 이름*

로컬에서 워크플로를 실행하여 올바르게 실행되고 있는지 확인할 수 있습니다.

완료되면 Azure Media Services에서 실행할 수 있습니다.

먼저 Azure Media Services에서 안에 두 파일(비디오 파일 및 로고)이 있는 자산을 준비합니다. .NET 또는 REST API를 사용하여 이 작업을 수행할 수 있습니다. 또한 Azure Portal이나 [AMSE(Azure Media Services 탐색기)](https://github.com/Azure/Azure-Media-Services-Explorer) 를 사용하여 수행할 수도 있습니다.

이 자습서에는 AMSE를 사용하여 자산을 관리하는 방법을 보여 줍니다. 두 가지 방법으로 자산에 파일을 추가합니다.

* 로컬 폴더를 만들고, 안에 두 개의 파일을 복사하고 해당 폴더를 **자산** 탭으로 끌어서 놓습니다.
* 비디오 파일을 자산으로 업로드한 다음 자산 정보를 표시하고 파일 탭으로 이동하여 추가 파일(로고)을 업로드합니다.

> [!NOTE]
> 자산에서 기본 파일을 설정해야 합니다(기본 비디오 파일).

![AMSE에서 자산 파일](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*AMSE에서 자산 파일*

자산을 선택하고 프리미엄 인코더를 사용하여 인코딩하도록 선택합니다. 워크플로를 업로드하고 선택합니다.

프로세서에 데이터를 전달하는 버튼을 클릭하고 다음 XML을 추가하여 런타임 속성을 설정합니다.

![AMSE에서 프리미엄 인코더](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*AMSE에서 프리미엄 인코더*

그런 다음, 다음 XML 데이터를 붙여 넣습니다. 미디어 파일 입력 및 primarySourceFile 모두에 대한 비디오 파일의 이름을 지정해야 합니다. 로고에 대한 파일 이름도 지정합니다.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

.NET SDK를 사용하여 작업을 만들고 실행한 경우 이 XML 데이터를 구성 문자열로 전달해야 합니다.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

작업이 완료되면 출력 자산의 MP4 파일이 오버레이를 표시합니다.

![비디오에서 오버레이](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*비디오에서 오버레이*

[GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/)에서 샘플 워크플로를 다운로드할 수 있습니다.

## <a name="example-2--multiple-audio-language-encoding"></a>예제 2: 다중 오디오 언어 인코딩

다중 오디오 언어 인코딩 워크플로의 예는 [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding)에서 제공합니다.

이 폴더에는 MXF 파일을 여러 오디오 트랙이 있는 다중 MP4 파일 자산으로 인코딩하는 데 사용할 수 있는 샘플 워크플로가 포함되어 있습니다.

이 워크플로는 MXF 파일에 오디오 트랙 하나가 포함된 것으로 가정하며, 추가 오디오 트랙은 별도의 오디오 파일(WAV 또는 MP4)로 전달해야 합니다.

인코딩하려면 다음 단계를 따릅니다.

* MXF 파일 및 오디오 파일(0~18개 오디오 파일)을 사용하여 Media Services 자산을 만듭니다.
* MXF 파일을 기본 파일로 설정합니다.
* Premium 워크플로 인코더 프로세서를 사용하여 작업 및 태스크를 만듭니다. 제공된 워크플로(MultiMP4-1080p-19audio-v1.workflow)를 사용합니다.
* setruntime.xml 데이터를 작업에 전달합니다(Azure Media Services 탐색기를 사용하는 경우 “xml 데이터를 워크플로에 전달” 단추를 사용).
  * XML 데이터를 업데이트하여 올바른 파일 이름 및 언어 태그를 지정합니다.
  * 워크플로에는 Audio 1부터 Audio 18까지 오디오 구성 요소가 있습니다.
  * RFC5646은 언어 태그에 지원됩니다.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* 인코딩된 자산은 다중 언어 오디오 트랙을 포함하며 이러한 트랙을 Azure Media Player에서 선택할 수 있습니다.

## <a name="see-also"></a>참고 항목
* [Azure Media Services의 프리미엄 Encoding 소개](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Azure Media Services의 프리미엄 Encoding 사용 방법](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Azure Media Services로 주문형 콘텐츠 인코딩](media-services-encode-asset.md#media-encoder-premium-workflow)
* [미디어 인코더 Premium 워크플로 형식 및 코덱](media-services-premium-workflow-encoder-formats.md)
* [샘플 워크플로 파일](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services 탐색기 도구](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
