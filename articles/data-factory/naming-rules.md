---
title: Azure Data Factory 엔터티 이름 지정 규칙 | Microsoft Docs
description: 데이터 팩터리 엔터티에 대한 이름 지정 규칙을 설명합니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: c02a9393de72b827b7e38b52d06589f042d581b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787005"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory - 이름 지정 규칙
다음 테이블에서 데이터 팩터리 아티팩트에 대한 이름 지정 규칙을 제공합니다.

| 이름 | 이름 고유성 | 유효성 검사 |
|:--- |:--- |:--- |
| Data Factory |Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. 즉, `MyDF` 및 `mydf`는 같은 데이터 팩터리를 의미합니다. |<ul><li>각 데이터 팩터리는 정확히 하나의 Azure 구독에 연결됩니다.</li><li>개체 이름은 문자 또는 숫자로 시작해야 하며 문자, 숫자 및 대시(-) 문자를 포함할 수 있습니다.</li><li>모든 대시(-) 문자는 앞뒤에 문자 또는 숫자가 와야 합니다. 컨테이너 이름에서 연속 대시는 허용되지 않습니다.</li><li>이름은 3-63자까지 가능합니다.</li></ul> |
| 연결된 서비스/데이터 집합/파이프라인 |데이터 팩터리에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. |<ul><li>개체 이름은 문자, 숫자 또는 밑줄(_)로 시작해야 합니다.</li><li>사용할 수 없는 문자: “.”, “+”, “?”, “/”, “<”, ”>”,” * ”,”%”,”&”,”:”,”\\”</li><li>대시("-")는 연결된 서비스 및 데이터 세트의 이름에서만 허용되지 않습니다.</li></ul>  |
| 리소스 그룹 |Microsoft Azure에서 고유합니다. 이름은 대/소문자를 구분하지 않습니다. | 자세한 내용은 [Azure 명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions)을 참조하세요. |

## <a name="next-steps"></a>다음 단계
[빠른 시작: 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md) 문서의 단계별 지침에 따라 데이터 팩터리를 만드는 방법을 알아봅니다. 
