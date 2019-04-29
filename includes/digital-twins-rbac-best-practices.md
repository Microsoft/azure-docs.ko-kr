---
title: 포함 파일
description: 포함 파일
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/28/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: e81b8fb06240d566e46ca0b45a0910e014dee329
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60534118"
---
역할 기반 액세스 제어는 액세스, 권한 및 역할을 관리하기 위한 상속 중심 보안 전략입니다. 하위 역할은 부모 역할에서 권한을 상속합니다. 권한은 부모 역할에서 상속하지 않고도 할당될 수 있습니다. 필요에 따라 권한을 할당하여 역할을 사용자 지정할 수도 있습니다.

예를 들어 공간 관리자는 지정된 공간에 대한 모든 작업을 실행하는 글로벌 액세스가 필요할 수 있습니다. 액세스는 공간 내에서 또는 아래에 있는 모든 노드를 포함합니다. 디바이스 설치 관리자는 디바이스 및 센서에 대한 *읽기* 및 *업데이트* 권한만 필요할 수 있습니다.

항상 최소 권한 원칙에 따라 작업 수행에 *꼭 필요한 액세스 권한만* 역할에 할당해야 합니다. 이 원칙에 따라 ID에 다음 권한*만* 부여합니다.

* 작업을 완료하는 데 필요한 액세스 권한.
* 작업 수행에 꼭 필요한 만큼 적절하게 제한된 역할.

>[!IMPORTANT]
> 항상 최소 권한 원칙을 준수합니다.

다음은 두 가지 중요한 역할 기반 액세스 제어 모범 사례입니다.

> [!div class="checklist"]
> * 역할 할당을 정기적으로 감사하여 각 역할에 올바른 권한이 있는지 확인합니다.
> * 개인이 역할 또는 할당을 변경할 때 역할 및 할당을 정리합니다.