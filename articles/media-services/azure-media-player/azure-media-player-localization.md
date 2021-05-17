---
title: Azure Media Player 지역화
description: 영어가 아닌 로캘 사용자를 위한 다국어를 지원합니다.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449845"
---
# <a name="azure-media-player-localization"></a>Azure Media Player 지역화 #

다중 언어 지원을 통해 영어가 아닌 로캘의 사용자가 기본적으로 플레이어와 상호 작용할 수 있습니다. Azure Media Player는 페이지 언어에 따라 오류 메시지를 지역화하는 글로벌 언어 사전으로 인스턴스화됩니다. 개발자는 강제로 설정된 언어로 플레이어 인스턴스를 만들 수도 있습니다. 기본 언어는 영어입니다.

> [!NOTE]
> 이 기능은 아직 몇 가지 테스트를 진행 중이며 버그가 발생할 수 있습니다.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player는 현재 해당 언어 코드를 통해 다음 언어를 지원합니다.

| 언어            | 코드 | 언어                | 코드   | 언어                | 코드         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| 영어{기본값}   | en   | 크로아티아어                | hr     | 루마니아어                | ro           |
| 아랍어              | ar   | 헝가리어               | hu     | 슬로바키아어                  | sk           |
| 불가리아어           | bg   | 인도네시아어              | id     | 슬로베니아어                 | sl           |
| 카탈로니아어             | ca   | 아이슬란드어               | 다음인 경우     | 세르비아어 - 키릴 자모      | sr-cyrl-cs   |
| 체코어               | cs   | 이탈리아어                 | it     | 세르비아어 - 라틴 문자         | sr-latn-rs   |
| 덴마크어              | da   | 일본어                | ja     | 러시아어                 | ru           |
| 독일어              | de   | 카자흐어                  | kk     | 스웨덴어                 | sv           |
| 그리스어               | el   | 한국어                  | ko     | 태국어                    | th           |
| 스페인어             | es   | 리투아니아어              | lt     | 타갈로그어                 | tl           |
| 에스토니아어            | et   | 라트비아어                 | lv     | 터키어                 | tr           |
| 바스크어              | eu   | 말레이시아어               | ms     | 우크라이나어               | uk           |
| 페르시아어               | fa   | 노르웨이어 - BokmÃ¥l     | nb     | 우르두어                    | ur           |
| 핀란드어             | fi   | 네덜란드어                   | nl     | 베트남어              | vi           |
| 프랑스어              | fr   | 노르웨이어 - 니노르스크     | nn     | 중국어 - 간체    | zh-hans      |
| 갈리시아어            | gl   | 폴란드어                  | pl     | 중국어 - 번체   | zh-hant      |
| 히브리어              | he   | 포르투갈어 - 브라질     | pt-br  |                         |              |
| 힌디어               | hi   | 포르투갈어 - 포르투갈   | pt-pt  |                         |              |


> [!NOTE]
> 지역화를 수행하지 않으려면 언어를 영어로 강제 적용해야 합니다.

## <a name="next-steps"></a>다음 단계 ##

- [Azure Media Player 빠른 시작](azure-media-player-quickstart.md)
