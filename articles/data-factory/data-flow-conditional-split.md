---
title: Azure Data Factory Mapping Data Flow 조건부 분할 변환
description: Azure Data Factory Data Flow 조건부 분할 변환
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627651"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Azure Data Factory Mapping Data Flow 조건부 분할 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

조건부 분할 변환은 데이터 내용에 따라 데이터 행을 다른 스트림으로 라우팅할 수 있습니다. 조건부 분할 변환의 구현은 프로그래밍 언어의 CASE 결정 구조체와 비슷합니다. 변환은 식을 평가하고, 결과에 따라 데이터 행을 지정된 스트림으로 보냅니다. 이 변환은 기본 출력도 제공하므로, 행과 일치하는 식이 없을 경우 기본 출력으로 보내집니다.

![조건부 분할](media/data-flow/cd1.png "조건부 분할")

조건을 더 추가하려면 맨 아래 구성 창에서 “스트림 추가”를 선택하고 식 작성기 텍스트 상자를 클릭하여 식을 작성합니다.
