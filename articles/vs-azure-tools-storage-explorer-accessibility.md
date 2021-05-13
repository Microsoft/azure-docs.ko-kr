---
title: Azure Storage Explorer 접근성 | Microsoft Docs
description: Azure Storage Explorer의 접근성에 대해 알아봅니다. 사용 가능한 화면 읽기 프로그램, 확대/축소 기능, 고대비 테마, 바로 가기 키에 대해 검토합니다.
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88035488"
---
# <a name="storage-explorer-accessibility"></a>Storage Explorer 접근성

## <a name="screen-readers"></a>화면 읽기 프로그램

Storage Explorer는 Windows 및 Mac에서의 화면 읽기 프로그램 사용을 지원합니다. 각 플랫폼에 대해 다음 화면 읽기 프로그램을 사용하는 것이 좋습니다.

플랫폼 | 화면 읽기 프로그램
---------|--------------
Windows  | NVDA
Mac      | 음성
Linux    | (Linux에서는 화면 읽기 프로그램이 지원되지 않음)

Storage Explorer를 실행할 때 접근성 문제가 발생하면 [GitHub에서 문제를 제기](https://github.com/Microsoft/AzureStorageExplorer/issues)하세요.

## <a name="zoom"></a>확대/축소

확대/축소를 통해 Storage Explorer에서 텍스트를 확대할 수 있습니다. 확대하려면 [도움말] 메뉴에서 **확대/축소** 를 클릭합니다. [도움말] 메뉴를 사용하여 확대/축소 수준을 다시 기본 수준으로 축소하고 재설정할 수 있습니다.

![[도움말] 메뉴의 [확대/축소] 옵션][0]

확대/축소 설정을 UI 요소 대부분의 크기를 늘립니다. OS에서 큰 텍스트 및 확대/축소 설정을 사용하여 모든 UI 요소의 크기가 올바르게 조정되도록 하는 것이 좋습니다.

## <a name="high-contrast-themes"></a>고대비 테마

Storage Explorer에는 두 개의 고대비 테마(**고대비 밝게**, **고대비 어둡게**)가 있습니다. 도움말 > 테마 메뉴에서 선택하여 테마를 변경할 수 있습니다.

![테마 하위 메뉴][1]

테마 설정은 UI 요소 대부분의 색상을 변경합니다. OS에 맞는 고대비 테마를 사용하여 모든 UI 요소의 색이 적절하게 지정되도록 하는 것이 좋습니다.

## <a name="shortcut-keys"></a>바로 가기 키

### <a name="window-commands"></a>Window 명령

명령       | 바로 가기 키
--------------|--------------------
새 창    | **Control+Shift+N**
편집기 닫기  | **Control+F4**
Quit          | **Control+Shift+W**

### <a name="navigation-commands"></a>탐색 명령

명령                | 바로 가기 키
-----------------------|----------------------
다음 패널에 포커스       | **F6**
이전 패널에 포커스   | **Shift+F6**
Explorer               | **Control+Shift+E**
계정 관리     | **Control+Shift+A**
사이드바 설정/해제        | **Control+B**
활동 로그           | **Control+Shift+L**
작업 및 속성 | **Control+Shift+P**
현재 편집기         | **Control+Home**
다음 편집기            | **Control+Page Down**
이전 편집기        | **Control+Page Up**

### <a name="zoom-commands"></a>확대/축소 명령

명령  | 바로 가기 키
---------|------------------
확대  | **Control+=**
축소 | **Control+-**

### <a name="blob-and-file-share-editor-commands"></a>Blob 및 파일 공유 편집기 명령

명령 | 바로 가기 키
--------|--------------------
뒤로    | **Alt+왼쪽 화살표**
앞으로 | **Alt+오른쪽 화살표**
위로      | **Alt+위쪽 화살표**

### <a name="editor-commands"></a>편집기 명령

명령 | 바로 가기 키
--------|------------------
복사    | **Ctrl+C**
잘라내기     | **Ctrl+X**
붙여넣기   | **Ctrl+V**
새로 고침  | **Control+R**

### <a name="other-commands"></a>기타 명령

명령                | 바로 가기 키
-----------------------|------------------
개발자 도구 설정/해제 | **F12**
다시 로드                 | **Alt+Control+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png
