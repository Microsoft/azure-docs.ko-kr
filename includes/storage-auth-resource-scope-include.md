---
title: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/07/2021
ms.author: tamram
ms.openlocfilehash: 32f2d1a5533c48b3b7c78d9e66b08cafdde09a8f
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111901903"
---
Azure RBAC 역할을 보안 주체에 할당하기 전에 보안 주체에게 부여해야 하는 액세스 범위를 결정합니다. 모범 사례에 따르면 항상 가능한 가장 좁은 범위만 부여하는 것이 가장 좋습니다. 더 광범위한 범위에서 정의된 Azure RBAC 역할은 그 아래에 있는 리소스에 상속됩니다.

다음 목록에서는 Azure Blob 및 큐 리소스에 대한 액세스 범위를 가장 좁은 범위에서 시작하여 지정할 수 있는 수준을 설명합니다.

- **개별 컨테이너.** 이 범위의 역할 할당은 컨테이너의 모든 Blob과 컨테이너 속성 및 메타데이터에 적용됩니다.
- **개별 큐.** 이 범위의 역할 할당은 큐의 메시지와 큐 속성 및 메타데이터에 적용됩니다.
- **스토리지 계정.** 이 범위의 역할 할당은 모든 컨테이너와 해당 Blob에 적용되거나 모든 큐와 해당 메시지에 적용됩니다.
- **리소스 그룹.** 이 범위의 역할 할당은 리소스 그룹의 모든 스토리지 계정에 있는 모든 컨테이너 또는 큐에 적용됩니다.
- **구독.** 이 범위의 역할 할당은 구독의 모든 리소스 그룹에 있는 모든 스토리지 계정의 모든 컨테이너 또는 큐에 적용됩니다.
- **관리 그룹.** 이 범위의 역할 할당은 관리 그룹의 모든 구독에 있는 모든 리소스 그룹의 모든 스토리지 계정에 있는 모든 컨테이너 또는 큐에 적용됩니다.

Azure 역할 할당 및 범위에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../articles/role-based-access-control/overview.md)을 참조하세요.
