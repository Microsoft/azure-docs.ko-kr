---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616829"
---
> [!TIP]
> 로그인하면 Azure 계정에 연결된 구독 목록이 나타납니다. `isDefault: true`가 있는 구독 정보는 Azure CLI 명령에 대해 현재 활성화된 구독입니다. 이 구독은 Azure Machine Learning 작업 영역을 포함하는 구독과 동일해야 합니다. 작업 영역에 대한 개요 페이지를 방문하여 [Azure Portal](https://portal.azure.com)에서 구독 ID를 찾을 수 있습니다. SDK를 사용하여 작업 영역 개체에서 구독 ID를 가져올 수도 있습니다. 예들 들어 `Workspace.from_config().subscription_id`입니다.
> 
> 다른 구독을 선택하려면 `az account set -s <subscription name or ID>` 명령을 사용하고 전환할 구독 이름 또는 ID를 지정합니다. 구독 선택에 대한 자세한 내용은 [여러 Azure 구독 사용](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)을 참조하세요.