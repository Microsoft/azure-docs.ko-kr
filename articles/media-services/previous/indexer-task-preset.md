---
title: Azure Media Indexer의 미리 설정된 작업
description: 이 항목에서는 Azure Media Indexer의 미리 설정된 작업 개요를 제공합니다.
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
origin.date: 02/08/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 8ce3ea3847e4c8c022f17375676d8415372dec85
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466309"
---
# <a name="task-preset-for-azure-media-indexer"></a>Azure Media Indexer의 미리 설정된 작업 

Azure Media Indexer는 미디어 파일 및 콘텐츠를 검색 가능하도록 만들고, 선택 캡션 트랙 및 키워드를 생성하고, 자산의 일부인 자산 파일을 인덱싱하는 등의 작업을 수행하는 데 사용하는 미디어 프로세서입니다.

이 항목에서는 인덱싱 작업에 전달해야 하는 미리 설정된 작업을 설명합니다. 완전한 예제는 [Azure Media Indexer를 사용하여 미디어 파일 인덱싱](media-services-index-content.md)을 참조하세요.

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer 구성 XML

다음 표에는 구성 XML의 요소 및 특성이 설명되어 있습니다.

|이름|필요|설명|
|---|---|---|
|입력|true|인덱싱할 자산 파일입니다.<br/>Azure Media Indexer는 MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV 형식의 미디어 파일을 지원합니다. <br/><br/>아래에 표시된 것처럼 **input** 요소의 **name** 또는 **list** 특성에 파일 이름을 지정할 수 있습니다. 인덱싱할 자산 파일을 지정하지 않으면 기본 파일이 선택됩니다. 기본 자산 파일이 설정되지 않은 경우 입력 자산의 첫 번째 파일이 인덱싱됩니다.<br/><br/>자산 파일 이름을 명시적으로 지정하려면 다음을 수행합니다.<br/>```<input name="TestFile.wmv" />```<br/><br/>한 번에 여러 자산 파일을 인덱싱할 수도 있습니다(최대 10개 파일). 다음을 수행합니다.<br/>- 텍스트 파일(매니페스트 파일)을 만들고 .lst 확장명을 지정합니다.<br/>- 입력 자산에 있는 모든 자산 파일 이름 목록을 이 매니페스트 파일에 추가합니다.<br/>- 자산에 매니페스트 파일을 추가(업로드)합니다.<br/>- 입력의 목록 특성에 매니페스트 파일의 이름을 지정합니다.<br/>```<input list="input.lst">```<br/><br/>**참고:** 매니페스트 파일에 10개가 넘는 파일을 추가하면 2006 오류 코드로 인해 인덱싱 작업이 실패합니다.|
|metadata|false|지정된 자산 파일에 대한 메타데이터입니다.<br/>```<metadata key="..." value="..." />```<br/><br/>미리 정의된 키의 값을 제공할 수 있습니다. <br/><br/>현재 다음 키가 지원됩니다.<br/><br/>**title** 및 **description** - 음성 인식 정확도를 개선하기 위해 언어 모델을 업데이트하는 데 사용됩니다.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username** 및 **password** - http 또는 https를 통해 인터넷 파일을 다운로드할 때 인증에 사용됩니다.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>username 및 password 값은 입력 매니페스트의 모든 미디어 URL에 적용됩니다.|
|기능<br/><br/> 버전 1.2에 추가되었습니다. 현재 지원되는 유일한 기능은 음성 인식("ASR")입니다.|false|음성 인식 기능에는 다음 설정 키가 포함됩니다.<br/><br/>언어:<br/>- 멀티미디어 파일에서 인식할 자연어<br/>- English, Spanish<br/><br/>CaptionFormats:<br/>- 원하는 출력 캡션 형식의 세미콜론으로 구분된 목록(있는 경우)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>- AIB 파일이 필요한지를 지정하는 부울 플래그(SQL Server 및 고객 인덱서 IFilter와 함께 사용). 자세한 내용은 Using AIB Files with Azure Media Indexer and SQL Server(Azure Media Indexer 및 SQL Server에서 AIB 파일 사용)를 참조하세요.<br/>- True, False<br/><br/>GenerateKeywords:<br/>- 키워드 XML 파일이 필요한지를 지정하는 부울 플래그<br/>- True, False|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer 구성 XML 예제

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>다음 단계

[Azure Media Indexer를 사용하여 미디어 파일 인덱싱](media-services-index-content.md)을 참조하세요.

