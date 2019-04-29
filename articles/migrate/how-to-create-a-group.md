---
title: Azure Migrate를 사용하여 평가할 컴퓨터 그룹화 | Microsoft Docs
description: Azure Migrate 서비스를 사용하여 평가를 실행하기 전에 컴퓨터를 그룹화하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544932"
---
# <a name="group-machines-for-assessment"></a>평가할 컴퓨터 그룹화

이 문서에서는 [Azure Migrate](migrate-overview.md)에서 평가할 컴퓨터 그룹을 만드는 방법에 대해 설명합니다. Azure Migrate는 Azure로 마이그레이션하는 데 적합한지를 확인하기 위해 그룹에 있는 컴퓨터를 평가하고, Azure에서 컴퓨터를 실행하기 위한 크기 조정 및 예상 비용을 예측합니다. 함께 마이그레이션할 머신을 아는 경우에는 다음 방법을 사용하여 Azure Migrate에서 그룹을 수동으로 만들 수 있습니다. 함께 그룹화할 머신에 대해 잘 모르는 경우에는 Azure Migrate의 종속성 시각화 기능을 사용하여 그룹을 만들 수 있습니다. [자세한 정보](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> Azure Government에서는 종속성 시각화 기능을 사용할 수 없습니다.

## <a name="create-a-group"></a>그룹 만들기

1. Azure Migrate 프로젝트의 **개요**에서 관리 아래의  **그룹** > **+그룹**을 차례로 클릭하고 그룹 이름을 지정합니다.
2. 그룹에 컴퓨터를 하나 이상 추가하고  **만들기**를 클릭합니다.
3. 필요에 따라 그룹에 대해 새 평가를 실행하도록 선택할 수 있습니다.

    ![그룹 만들기](./media/how-to-create-a-group/create-group.png)

그룹을 만든 후에는 **그룹** 페이지에서 그룹을 선택하고 머신을 추가하거나 제거하여 그룹을 수정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [컴퓨터 종속성 매핑](how-to-create-group-machine-dependencies.md)을 사용하여 높은 신뢰도 그룹을 만드는 방법을 알아봅니다.
- 평가를 계산하는 방법에 대해 [자세히 알아봅니다](concepts-assessment-calculation.md).
