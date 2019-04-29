---
title: Azure Media Indexer를 사용하여 미디어 파일 인덱싱
description: Azure Media Indexer를 사용하면 미디어 파일 콘텐츠를 검색 가능하게 만들고 선택 자막 및 키워드용 전체 텍스트 기록을 생성할 수 있습니다. 이 항목에서는 Media Indexer를 사용하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.assetid: 827a56b2-58a5-4044-8d5c-3e5356488271
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: adsolank;juliako;johndeu
ms.openlocfilehash: a11ae0414d6737f1588515ec19524bcf499f0c74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61215808"
---
# <a name="indexing-media-files-with-azure-media-indexer"></a>Azure Media Indexer를 사용하여 미디어 파일 인덱싱
Azure Media Indexer를 사용하면 미디어 파일 콘텐츠를 검색 가능하게 만들고 선택 자막 및 키워드용 전체 텍스트 기록을 생성할 수 있습니다. 하나의 미디어 파일 또는 일괄 처리에서 여러 미디어 파일을 처리할 수 있습니다.  

> [!IMPORTANT]
> 콘텐츠를 인덱싱할 때, 음성이 분명한(배경 음악, 소음, 효과음 또는 마이크 소음이 없는) 미디어 파일을 사용해야 합니다. 적절한 콘텐츠의 예로는 녹음된 회의, 강의 또는 프레젠테이션이 있습니다. 인덱싱에 적합하지 않을 수 있는 콘텐츠: 영화, TV 프로그램, 오디오 및 사운드 효과가 혼합된 콘텐츠, 배경 소음(기계 소음)이 들어간 녹음 품질이 좋지 않은 콘텐츠.
> 
> 

인덱싱 작업은 다음 출력을 생성할 수 있습니다.

* 다음 형식의 선택 자막 파일: **SAMI**, **TTML** 및 **WebVTT**.
  
    선택 자막 파일은 Recognizability라는 태그를 포함합니다. 이 태그는 원본 동영상의 음성을 인식할 수 있는 정도에 따라 인덱싱 작업을 평가합니다.  유용성을 위해 화면 출력 파일에 Recognizability 값을 사용할 수 있습니다. 낮은 점수는 오디오 품질로 인해 결과가 좋지 않음을 의미합니다.
* 키워드 파일(XML).
* SQL server에서 사용할 AIB(오디오 인덱싱 Blob) 파일.
  
    자세한 내용은 [Azure Media Indexer 및 SQL Server에서 AIB 파일 사용](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)(영문)을 참조하세요.

이 문서에서는 인덱싱 작업을 만들어 **자산 인덱싱** 및 **여러 파일 인덱싱**을 수행하는 방법을 보여 줍니다.

최신 Azure Media Indexer 업데이트는 [Media Services 블로그](#preset)(영문)를 참조하세요.

## <a name="using-configuration-and-manifest-files-for-indexing-tasks"></a>인덱싱 태스크에 대한 구성 및 매니페스트 파일 사용
태스크 구성을 사용하여 인덱싱 태스크에 대한 세부 사항을 지정할 수 있습니다. 예를 들어, 미디어 파일에 사용할 메타데이터를 지정할 수 있습니다. 이 메타데이터는 그 언어의 어휘를 확장하고 음성 인식 정확성을 크게 향상하기 위해 언어 엔진이 사용합니다.  또한 원하는 출력 파일을 지정할 수 있습니다.

매니페스트 파일을 사용하여 여러 미디어 파일을 한 번에 처리할 수도 있습니다.

자세한 내용은 [Azure Media Indexer의 작업 기본 설정](https://msdn.microsoft.com/library/dn783454.aspx)(영문)을 참조하세요.

## <a name="index-an-asset"></a>자산 인덱스
다음 메서드는 미디어 파일을 자산으로 업로드하고 자산을 인덱스하기 위해 작업을 만듭니다.

구성 파일을 지정하지 않으면 미디어 파일이 모두 기본 설정으로 인덱싱됩니다.

```csharp
    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }  
```

<!-- __ -->
### <a id="output_files"></a>출력 파일
기본적으로 인덱싱 작업은 다음 출력 파일을 생성합니다. 파일은 첫 번째 출력 자산에 저장됩니다.

둘 이상의 입력 미디어 파일이 있을 때, 인덱서는 ‘JobResult.txt’라는 작업 출력을 위한 매니페스트 파일을 생성합니다. 각각의 입력 미디어 파일에 대한 결과로 생성되는 AIB, SAMI, TTML, WebVTT 및 키워드 파일은 순차적으로 번호가 매겨지고 "별칭"을 사용하여 이름이 지정됩니다.

| 파일 이름 | 설명 |
| --- | --- |
| **InputFileName.aib** |Audio indexing blob file. <br/><br/> AIB(Audio Indexing Blob) 파일은 전체 텍스트 검색을 사용하는 Microsoft SQL에서 검색할 수 있는 이진 파일입니다.  AIB 파일은 더 풍부한 검색 환경을 허용하여 각각의 단어에 대체 항목을 포함하기 때문에 단일 캡션 파일보다 훨씬 강력합니다. <br/> <br/>Microsoft SQL server 2008 이상을 실행하는 컴퓨터에서 Indexer SQL 추가 기능을 설치해야 합니다. Microsoft SQL server의 전체 텍스트 검색을 사용하는 AIB 검색은 WAMI에 의해 생성된 선택 자막 파일을 검색할 때보다 훨씬 정교한 검색 결과를 제공합니다. 선택 자막 파일은 오디오의 각 세그먼트에 가장 신뢰성이 높은 단어를 포함하지만 AIB는 사운드가 유사한 대체 단어를 포함하기 때문입니다. 음성 단어 검색이 가장 중요한 경우, Microsoft SQL Server와 함께 AIB를 사용하는 것이 좋습니다.<br/><br/> 추가 기능을 다운로드하려면 <a href="https://aka.ms/indexersql">Azure Media Indexer SQL 추가 기능</a>을 클릭합니다. <br/><br/>선택 자막에 기반한 비디오 및 키워드 XML 파일을 간단하게 인덱스하기 위해 Apache Lucene/Solr와 같은 기타 검색 엔진을 활용할 수도 있습니다. 하지만 이는 검색 결과의 정확성이 떨어집니다. |
| **InputFileName.smi**<br/>**InputFileName.ttml**<br/>**InputFileName.vtt** |SAMI, TTML 및 WebVTT 형식의 CC(선택 자막) 파일.<br/><br/>청각 장애가 있는 사용자가 액세스할 수 있는 오디오 및 비디오 파일을 만드는 데 사용할 수 있습니다.<br/><br/>선택 자막 파일에는 <b>Recognisability</b>라는 태그가 포함되어 있으며, 원본 비디오의 음성 인식 정도에 따라 인덱싱 작업에 점수를 매깁니다.  유용성을 위해 화면 출력 파일에 <b>Recognizability</b> 값을 사용할 수 있습니다. 낮은 점수는 오디오 품질로 인해 결과가 좋지 않음을 의미합니다. |
| **InputFileName.kw.xml<br/>InputFileName.info** |키워드 및 정보 파일입니다. <br/><br/>키워드 파일은 빈도 및 오프셋 정보를 포함하며 음성 콘텐츠에서 추출된 키워드를 포함하는 XML 파일입니다. <br/><br/>정보 파일은 인식된 각 용어에 대한 세부적인 정보를 포함하는 일반 텍스트 파일입니다. 첫 번째 줄은 특수하며 Recognizability 점수를 포함합니다. 이후 각 줄은 탭으로 구분된 데이터 목록이며, 다음과 같습니다. 시작 시간, 종료 시간, 단어/구, 신뢰도 시간은 초 단위로 제공되며 신뢰도는 0-1의 숫자로 지정됩니다. <br/><br/>예제 줄: "1.20    1.45    word    0.67" <br/><br/>음성 분석을 수행하거나, Bing, Google 또는 Microsoft SharePoint와 같은 검색 엔진에 노출하여 미디어 파일을 보다 검색하기 쉽게 하거나, 보다 관련 있는 광고를 제공하는 데에도 사용하는 등의 여러 가지 목적을 위해 이들 파일을 사용할 수 있습니다. |
| **JobResult.txt** |여러 파일을 인덱싱할 때만 표시되는 출력 매니페스트에는 다음 정보가 포함됩니다.<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>오류</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/> |

모든 입력 미디어 파일이 성공적으로 인덱싱되지 않으면, 오류 코드 4000으로 인덱싱 작업이 실패합니다. 자세한 내용은 [오류 코드](#error_codes)를 참조하세요.

## <a name="index-multiple-files"></a>여러 파일을 인덱싱
다음 메서드는 여러 파일을 한 자산으로 업로드하고 이러한 모든 파일을 일괄로 인덱스하기 위해 작업을 만듭니다.

확장명이 ".lst"인 매니페스트 파일이 만들어지고 자산에 업로드됩니다. 매니페스트 파일은 모든 자산 파일 목록을 포함합니다. 자세한 내용은 [Azure Media Indexer의 작업 기본 설정](https://msdn.microsoft.com/library/dn783454.aspx)(영문)을 참조하세요.

```csharp
    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);

        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);

        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");

        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);

        // Read configuration.
        string configuration = File.ReadAllText("batch.config");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }

        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);

        return true;
    }

    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);

        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }

        return asset;
    }
```

### <a name="partially-succeeded-job"></a>부분적으로 성공된 작업
모든 입력 미디어 파일이 성공적으로 인덱스되지 않은 경우, 오류 코드 4000으로 인덱싱 작업이 실패합니다. 자세한 내용은 [오류 코드](#error_codes)를 참조하세요.

동일한 출력(성공된 작업)이 생성됩니다. 출력 매니페스트 파일을 참조하여 오류 열 값에 따라 실패한 입력 파일을 알아볼 수 있습니다. 실패한 입력 파일의 경우 AIB, SAMI, TTML, WebVTT 및 키워드 파일이 결과로 생성되지 않습니다.

### <a id="preset"></a> Azure Media Indexer의 태스크 미리 설정
태스크와 함께 태스크 미리 설정을 제공하여 Azure Media Indexer의 처리를 사용자 지정할 수 있습니다.  다음은 이 구성 xml의 형식을 설명합니다.

| 이름 | 필요 | 설명 |
| --- | --- | --- |
| **input** |false |인덱싱할 자산 파일입니다.</p><p>Azure Media Indexer에서 지원되는 미디어 파일 형식은 MP4, WMV, MP3, M4A, WMA, AAC, WAV입니다.</p><p>아래에서 보여 주듯이 **입력** 요소의 **name** 또는 **list** 특성에 파일 이름을 지정할 수 있습니다. 인덱싱할 파일을 지정하지 않으면 기본 파일이 선택됩니다. 기본 자산 파일이 설정되지 않은 경우 입력 자산의 첫 번째 파일이 인덱싱됩니다.</p><p>자산 파일 이름을 명시적으로 지정하려면 다음을 수행합니다.<br/>`<input name="TestFile.wmv">`<br/><br/>한 번에 여러 자산 파일을 인덱싱할 수도 있습니다(최대 10개 파일). 다음을 수행합니다.<br/><br/><ol class="ordered"><li><p>텍스트 파일(매니페스트 파일)을 만들고 .lst 확장명을 지정합니다. </p></li><li><p>입력 자산에 있는 모든 자산 파일의 이름 목록을 이 매니페스트 파일에 추가합니다. </p></li><li><p>자산에 매니페스트 파일을 추가(업로드)합니다.  </p></li><li><p>입력의 목록 특성에 매니페스트 파일의 이름을 지정합니다.<br/>`<input list="input.lst">`</li></ol><br/><br/>참고: 매니페스트 파일에 10개를 초과하는 파일을 추가하면 인덱싱 작업이 실패하며 2006 오류 코드가 표시됩니다. |
| **metadata** |false |어휘 적응에 사용되는 지정된 자산 파일에 대한 메타데이터입니다.  적절한 명사와 같은 비표준 어휘 단어를 인식하는 인덱서를 준비하는 경우 유용합니다.<br/>`<metadata key="..." value="..."/>` <br/><br/>미리 정의된 **키**에 대해 **값**을 제공할 수 있습니다. 현재 다음 키가 지원됩니다.<br/><br/>"title" 및 "description" - 어휘 적응에서 작업에 대한 언어 모델을 조정하고 음성 인식 정확도를 향상하는 데 사용됩니다.  값 시드 인터넷으로 인덱싱 태스크 기간 동안 내부 디렉터리를 보강할 콘텐츠를 사용하여 문맥적으로 관련된 텍스트 문서를 검색하여 찾습니다.<br/>`<metadata key="title" value="[Title of the media file]" />`<br/>`<metadata key="description" value="[Description of the media file] />"` |
| **features** <br/><br/>  버전 1.2에 추가되었습니다. 현재 지원되는 유일한 기능은 음성 인식("ASR")입니다. |false |음성 인식 기능에는 다음 설정 키가 포함됩니다.<table><tr><th><p>키</p></th>        <th><p>설명</p></th><th><p>예제 값</p></th></tr><tr><td><p>언어</p></td><td><p>멀티미디어 파일에서 인식되는 자연 언어입니다.</p></td><td><p>English, Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>원하는 출력 자막 형식의 세미콜론으로 구분된 목록(있는 경우)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>AIB 파일이 필요한지 여부를 지정하는 부울 플래그입니다(SQL Server 및 고객 인덱서 IFilter와 함께 사용).  자세한 내용은 <a href="https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">Azure Media Indexer 및 SQL Server에서 AIB 파일 사용</a>(영문)을 참조하세요.</p></td><td><p>True; False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>키워드 XML 파일이 필요한지 여부를 지정하는 부울 플래그입니다.</p></td><td><p>True; False. </p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>신뢰 수준에 관계없이 전체 캡션을 강제로 적용할지 여부를 지정하는 부울 플래그입니다.  </p><p>기본값은 false이고 신뢰 수준이 50% 미만인 단어 및 구는 최종 캡션 출력에서 생략되고 줄임표("...")로 대체됩니다.  줄임표는 캡션 품질 제어 및 감사에 유용합니다.</p></td><td><p>True; False. </p></td></tr></table> |

### <a id="error_codes"></a>오류 코드
오류 발생 시 Azure Media Indexer는 다음 오류 코드 중 하나를 다시 보고해야 합니다.

| 코드 | 이름 | 가능한 이유 |
| --- | --- | --- |
| 2000 |유효하지 않은 구성 |유효하지 않은 구성 |
| 2001 |유효하지 않은 입력 자산 |입력 자산 유실 또는 빈 자산. |
| 2002 |유효하지 않은 매니페스트 |매니페스트가 비어 있거나 유효하지 않은 항목을 포함합니다. |
| 2003 |미디어 파일 다운로드에 실패 |매니페스트 파일에 유효하지 않은 URL. |
| 2004 |지원되지 않는 프로토콜 |미디어 URL 프로토콜이 지원되지 않습니다. |
| 2005 |지원되지 않는 파일 유형 |입력 미디어 파일 유형이 지원되지 않습니다. |
| 2006 |너무 많은 입력 파일 |입력 매니페스트에 10개 이상의 파일이 있습니다. |
| 3000 |미디어 파일 디코딩 실패 |지원되지 않는 미디어 코덱 <br/>또는<br/> 손상된 미디어 파일 <br/>또는<br/> 입력 미디어에 오디오 스트림 없음. |
| 4000 |인덱싱 일괄 처리 부분적으로 성공 |일부 입력 미디어 파일 인덱싱에 실패했습니다. 자세한 내용은 <a href="#output_files">출력 파일</a>을 참조하세요. |
| 기타 |내부 오류 |지원 팀에 문의하시기 바랍니다. indexer@microsoft.com |

## <a id="supported_languages"></a>지원되는 언어
현재 영어와 스페인어가 지원됩니다. 자세한 내용은 [v1.2 릴리스 블로그 게시물](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)을 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>관련 링크
[Azure Media Services 분석 개요](media-services-analytics-overview.md)

[Azure Media Indexer 및 SQL Server에서 AIB 파일 사용(영문)](https://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)

[Azure Media Indexer 2 미리 보기를 사용하여 미디어 파일 인덱싱](media-services-process-content-with-indexer2.md)

