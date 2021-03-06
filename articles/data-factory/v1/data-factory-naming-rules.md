---
title: Azure Data Factory 엔터티 이름 지정 규칙-버전 1
description: Data Factory v1 엔터티에 대 한 명명 규칙을 설명 합니다.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3c68159f20873aeff5938ab21f348be4a922041c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779480"
---
# <a name="rules-for-naming-azure-data-factory-entities"></a>Azure Data Factory 엔터티 명명 규칙
> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [Data Factory의 명명 규칙](../naming-rules.md)을 참조하세요.

다음 테이블에서 데이터 팩터리 아티팩트에 대한 이름 지정 규칙을 제공합니다.

| Name | 이름 고유성 | 유효성 검사 |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. 즉, `MyDF` 및 `mydf`는 같은 데이터 팩터리를 의미합니다. |<ul><li>각 데이터 팩터리는 정확히 하나의 Azure 구독에 연결됩니다.</li><li>개체 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.</li><li>모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다. 컨테이너 이름에서 연속 대시는 허용되지 않습니다.</li><li>이름은 3-63자까지 가능합니다.</li></ul> |
| 연결된 서비스/테이블/파이프라인 |데이터 팩터리에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>테이블 이름에서 최대 문자 수는 260개입니다.</li><li>개체 이름은 문자, 숫자 또는 밑줄(_)로 시작해야 합니다.</li><li>다음 문자는 사용할 수 없습니다. ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li></ul> |
| 리소스 그룹 |Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>최대 문자 수: 1,000개</li><li>이름에는 문자, 숫자 및 "-", "_", "," 및 "." 문자를 사용할 수 있습니다.</li></ul> |

