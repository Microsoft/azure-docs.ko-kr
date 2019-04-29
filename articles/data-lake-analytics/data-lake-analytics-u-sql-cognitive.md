---
title: Azure Data Lake Analytics에서 U-SQL 인식 기능 사용
description: U-SQL에서 인텔리전스 인식 기능을 사용하는 방법에 알아봅니다.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: b8d43188c337e0c12db60b51dee053ef7259701a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812894"
---
# <a name="get-started-with-the-cognitive-capabilities-of-u-sql"></a>U-SQL의 인식 기능 시작

## <a name="overview"></a>개요
U-SQL에 대한 인식 기능은 개발자가 빅 데이터 프로그램에서 인텔리전스를 사용하도록 합니다. 

인식 기능을 사용할 수 있는 샘플은 다음과 같습니다.
* 이미징: [얼굴 감지](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 이미징: [감정 감지](https://github.com/Azure-Samples/usql-cognitive-imaging-emotion-detection-hello-world)
* 이미징: [(태그 지정) 하는 개체를 검색 합니다.](https://github.com/Azure-Samples/usql-cognitive-imaging-object-tagging-hello-world)
* 이미징: [OCR(Optical Character Recognition)](https://github.com/Azure-Samples/usql-cognitive-imaging-ocr-hello-world)
* 텍스트: [핵심 구 추출 및 감정 분석](https://github.com/Azure-Samples/usql-cognitive-text-hello-world)

## <a name="registering-cognitive-extensions-in-u-sql"></a>U-SQL에서 인식 확장 등록
시작 하기 전에 u-sql에서 인지 확장을 등록 하려면이 문서의 단계를 수행 합니다. [U-sql에서 인지 확장 등록](/u-sql/objects-and-extensions/cognitive-capabilities-in#registeringExtensions)합니다.

## <a name="next-steps"></a>다음 단계
* [U-SQL/인식 샘플](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Visual Studio용 데이터 레이크 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure 데이터 레이크 분석 작업에 U-SQL 창 함수 사용](data-lake-analytics-use-window-functions.md)
