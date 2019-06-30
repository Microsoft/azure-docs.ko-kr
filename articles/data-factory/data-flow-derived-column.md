---
title: 파생 열 변환 매핑 데이터 흐름에서-Azure Data Factory | Microsoft Docs
description: 매핑 데이터 흐름 파생 열 변환 사용 하 여 Azure Data Factory에서 대규모로 데이터를 변환 하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312181"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름에서 파생된 열 변환

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

## <a name="derived-column-settings"></a>파생된 열 설정

기존 열을 재정의 하려면 열 드롭다운을 통해 선택 합니다. 이 고, 그렇지 열 선택 필드를 사용 하 여 새 열의 이름 입력으로 텍스트 상자에 붙여넣습니다. 작성 하려면 파생된 열 식, 열어야 '식을 입력' 상자를 클릭 합니다 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)합니다.

![열 설정 파생](media/data-flow/dc1.png "파생 열 설정")

추가 파생된 열을 추가 하려면 기존 위로 마우스를 가져가고 파생 열 및 클릭 '+'. 그런 다음 '열 추가' 또는 '추가 열 패턴'를 선택 합니다. 열 패턴 열 이름이 변수 원본의 경우 유용한 가져올 수 있습니다. 자세한 내용은 [열 패턴](concepts-data-flow-column-pattern.md)합니다.

![새 파생 열 선택](media/data-flow/columnpattern.png "새 파생 열 선택")

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 정보는 [매핑 데이터 흐름 식 언어](data-flow-expression-functions.md)합니다.
