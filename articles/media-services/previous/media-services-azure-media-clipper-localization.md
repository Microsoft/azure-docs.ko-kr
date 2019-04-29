---
title: Azure Media Clipper 지역화 구성 | Microsoft Docs
description: Azure Media Clipper 지원 언어 및 지역화 지원에 대해 알아봅니다.
services: media-services
keywords: 클립, 하위 클립, 인코딩, 미디어
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 41fd483a0b78619db6d018cf4b2ab7933a7c3031
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465799"
---
# <a name="configure-localization"></a>지역화 구성 

Azure Media Clipper는 18개 언어로 제공됩니다. 위젯 언어를 설정하려면 초기화 중에 `language` 매개 변수를 정의해야 합니다. 다음 목록에서 원하는 언어 코드 문자열에서 전달합니다.
- 중국어(간체): zh-hans
- 중국어(번체): zh-hant
- 체코어: cs
- 네덜란드어, 플라망어: nl
- 영어: en
- 프랑스어: fr
- 독일어: de
- 헝가리어: hu
- 이탈리아어: it
- 일본어: ja
- 한국어: ko
- 폴란드어: pl
- 포르투갈어(브라질): pt-br
- 포르투갈어(포르투갈): pt-pt
- 러시아어: ru
- 스페인어: es
- 스웨덴어: sv
- 터키어: tr

사용자 지정 언어 사전을 설정하거나 기본 언어 사전을 확장하려면 각각 `languages` 또는 `extraLanguages` 매개 변수를 정의해야 합니다. 다음 JSON 형식을 사용하여 사용자 지정 사전에서 전달합니다.

```javascript
{
    '{language-code}': {
        '{message-id}': '{message}',
        ...
    }
    ...
}
```

예를 들어, 다음 샘플은 지역화된 영어 문자열을 정의합니다.

```javascript
{
    'en': {
        'VideoPlayer.noPreview': 'No video preview',
        'VideoPlayer.loadAsset': 'You must provide a valid asset',
        'AssetsPanel.name': 'Name',
        'AssetsPanel.type': 'Asset type',
        'AssetsPanel.actions': 'Actions',
        'AssetsPanel.loading': 'Loading...',
        'AssetsPanel.duration': 'Duration',
        'AssetsPanel.resolution': 'Resolution',
        'AssetsPanel.pluralFiles': '{0} assets',
        'AssetsPanel.searchFiles': 'Search assets',
        'AssetsPanel.showTypes': 'Show:',
        'AssetsPanel.typesInfo': 'Rendered assets are actual MP4 files. Dynamic manifest filters are filters applied to a parent asset\'s video segment playlist.',
        'AssetsPanel.filterTypes': 'Filters',
        'AssetsPanel.assetTypes': 'Assets',
        'AssetsPanel.assetsAll': 'All',
        'AssetsPanel.addAsset': 'Add asset to the end',
        'AssetsPanel.addFilter': 'Add filter to the timeline',
        'AssetsPanel.invalidAsset': 'The metadata of this asset is not compatible with the other assets in the timeline',
        'AssetsPanel.addAssetWarning': 'Subclipping on assets with different resolutions may cause resolution autoscaling.',
        'AssetsPanel.live': 'LIVE',
        'AssetsPanel.unknown': 'UNKNOWN',
        'AssetsPanel.minimGapNotMet': 'The asset duration must be greater than the minimum clip duration ({0} seconds)',
        'VideoPlayer.openAdvancedSettings': 'Advanced settings',
        'VideoPlayer.singleBitrate': 'Single-bitrate MP4 (rendered)',
        'VideoPlayer.multiBitrate': 'Multi-bitrate MP4 (rendered)',
        'VideoPlayer.dynamicManifest': 'Dynamic manifest filter',
        'VideoPlayer.ErrorWithMessage': 'There was an error in the video player, code {0}, message: {1}',
        'Common.cancel': 'Cancel',
        'Common.OK': 'OK',
        'AdvancedSettings.framerate': 'Frame rate',
        'Dropdown.select': 'Select an option...',
        'InputAsset.RemoveInput': 'Remove source',
        'Zoom.startTime': 'Start time',
        'Zoom.endTime': 'End time',
        'VideoPlayer.subclips': 'Subclips:',
        'VideoPlayer.length': 'Clip length:',
        'Accordion.scrollLeft': 'Scroll to the left',
        'Accordion.scrollRight': 'Scroll to the right',
        'AdvancedSettings.title': 'Advanced settings',
        'AdvancedSettings.subclipName': 'Subclip name',
        'AdvancedSettings.subclipType': 'Subclipping mode',
        'AdvancedSettings.includeAudioTracks': 'Include audio tracks',
        'AdvancedSettings.subclipTypeInfo': 'Single-bitrate and multi-bitrate MP4s are frame accurate rendered assets. Dynamic manifest filters are group-of-pictures (GOP) accurate filters applied to a parent asset. Creating filters does not create a new asset and does not require encoding. Subclipping jobs on live assets are valid as long as their mark times are within the archive window of the parent asset. Filters are valid as long as the parent asset exists and mark times are within its archive window.',
        'AdvancedSettings.frameRateInfo': 'We autodetect frame rate under most scenarios. however, If we cannot autodetect, choose a frame rate from the dropdown for the selected asset(s).',
        'AdvancedSettings.frameRateError': 'Unable to determine frame rate',
        'AdvancedSettings.subclipNameInfo': 'Choose a name for your subclip.',
        'AdvancedSettings.singleAudioTrack': '1 audio track selected',
        'AdvancedSettings.allAudioTracks': 'All audio tracks selected',
        'AdvancedSettings.someAudioTracks': '{0} audio tracks selected',
        'AdvancedSettings.includeAllAudioTracks': 'Include all audio tracks',
        'AssetsPanel.loadingError': 'Failed to retrieve assets from server.',
        'AssetsPanel.retry': 'Retry?',
        'CommandBar.prevFrameTitle': 'Move one frame backwards',
        'CommandBar.prevKeyFrameTitle': 'Move one GOP backwards',
        'CommandBar.cleanJob': 'Remove all assets',
        'CommandBar.cleanJobTitle': 'Remove all assets from timeline',
        'CommandBar.cleanJobMessage': 'This will empty all video clips from your timeline.',
        'CommandBar.update': 'Update filter',
        'CommandBar.createFilter': 'Create filter',
        'CommandBar.submit': 'Submit subclipper job',
        'CommandBar.jobErrorTitle': 'Operation failed',
        'CommandBar.jobErrorMessage': 'Your subclip failed to submit. Please try again.',
        'CommandBar.markInTitle': 'Set in at playhead',
        'CommandBar.markInPosition': 'Mark in timecode',
        'CommandBar.markOutTitle': 'Set out at playhead',
        'CommandBar.markOutPosition': 'Mark out timecode',
        'CommandBar.nextFrameTitle': 'Move one frame forward',
        'CommandBar.nextKeyFrameTitle': 'Move one GOP forward',
        'CommandBar.play': 'Play video',
        'CommandBar.pause': 'Pause video',
        'CommandBar.playPreviewTitle': 'Play subclip preview',
        'CommandBar.pausePreviewTitle': 'Pause subclip preview',
        'CommandBar.redoTitle': 'Redo last action',
        'CommandBar.removeAsset': 'Remove current asset',
        'CommandBar.undoTitle': 'Undo last action',
        'VideoPlayer.errorTitle': 'Error',
        'VideoPlayer.errorMessage': 'There was an error loading the selected asset.',
        'Timeline.markIn': 'Mark in bracket',
        'Timeline.markOut': 'Mark out bracket',
        'Timeline.playHead': 'Play head'
    }
}
```

## <a name="next-steps"></a>다음 단계
Azure Media Clipper 구성을 위한 다음 단계를 참조하세요.
- [Azure Media Clipper에 자산 로드](media-services-azure-media-clipper-load-assets.md)
- [사용자 지정 바로 가기 키 구성](media-services-azure-media-clipper-keyboard-shortcuts.md)
- [Clipper에서 클리핑 작업 제출](media-services-azure-media-clipper-submit-job.md)
