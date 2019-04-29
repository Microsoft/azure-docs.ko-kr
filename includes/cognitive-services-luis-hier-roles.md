---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/23/2018
ms.author: diberry
ms.openlocfilehash: 3803bc7f1e0da01fbaa8aa11bc6e8fc5c508b5ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61074292"
---
## <a name="roles-versus-hierarchical-entities"></a>역할 및 계층 구조 엔터티

역할에서 단순 엔터티와 함께 계층 구조 엔터티 또는 패턴을 사용해야 하나요? 

경우에 따라 다릅니다. 패턴 및 발화 예제는 사용자의 발화를 나타낸다는 점에서 서로 비교할 수 있고 의도에 따라 다릅니다.  

발화에 명백한 패턴이 없는 경우 계층 구조 엔터티를 사용합니다. 

|계층 구조 엔터티|역할에서 단순 엔터티|
|--|--|
|의도의 발화 예제 및 패턴에서 사용 가능|패턴에서만 사용 가능|
|레이블이 지정된 자식 엔터티를 사용하여 의도에 예제 발화가 포함되어야 함|의도의 예제 발화에서 역할에 레이블을 지정할 수 없음|
|엔터티를 추출하려면 의도에서 **추가** 예제 발화가 필요할 수 있음|의도에서 **적은** 예제 발화가 필요해야 함|
