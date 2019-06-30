---
title: Azure Machine Learning 작업 영역에서 역할 관리
titleSuffix: Azure Machine Learning service
description: 역할 기반 액세스 제어 (RBAC)를 사용 하 여 Azure Machine Learning 서비스 작업 영역에 액세스 하는 방법에 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 02/20/2019
ms.custom: seodec18
ms.openlocfilehash: e062fd73f2baeb4948430b13e0caa1f5c0b3f066
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341105"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning 작업 영역에 대 한 액세스 관리

이 문서에서는 Azure Machine Learning 작업 영역에 대 한 액세스를 관리 하는 방법을 알아봅니다. [역할 기반 액세스 제어 (RBAC)](/azure/role-based-access-control/overview) Azure 리소스에 대 한 액세스를 관리 하는 데 사용 됩니다. Azure Active Directory에서 사용자가 리소스에 대 한 액세스 권한을 부여 하는 특정 역할을 할당 됩니다. Azure 기본 제공 역할 및 사용자 지정 역할을 만드는 기능을 제공 합니다.

## <a name="default-roles"></a>기본 역할

Azure Machine Learning 작업 영역을 Azure 리소스입니다. 다른 Azure 리소스와 마찬가지로 새 Azure Machine Learning 작업 영역을 만들면 있어 세 가지 기본 역할을 사용 하 여 합니다. 작업 영역에 사용자를 추가 하 고 이러한 기본 제공 역할 중 하나에 할당할 수 있습니다.

| 역할 | 액세스 수준 |
| --- | --- |
| **판독기** | 작업 영역에서 읽기 전용 작업입니다. 판독기 수 목록 및 자산을 작업 영역에서 보기 하지만 만들거나 업데이트할 수 없습니다 이러한 자산입니다. |
| **기여자** | 보기, 만들기, 편집 또는 삭제 (있는 경우) 작업 영역에서 자산입니다. 예를 들어, 참가자 수 실험, 또는 계산 클러스터를 연결, 실행을 제출할 만들고 웹 서비스를 배포 합니다. |
| **소유자** | 보기, 만들기, 편집 또는 삭제 (있는 경우) 하는 기능을 포함 하는 작업 영역에 대 한 모든 작업 영역에서 자산입니다. 또한 역할 할당을 변경할 수 있습니다. |

> [!IMPORTANT]
> Azure에서 여러 수준으로 범위 역할 액세스를 수 있습니다. 예를 들어,이 작업 영역에 대 한 소유자 액세스를 사용 하 여 사용자가 작업 영역을 포함 하는 리소스 그룹에 소유자 액세스를 권한이 없을 수입니다. 자세한 내용은 [RBAC 어떻게 작동](/azure/role-based-access-control/overview#how-rbac-works)합니다.

특정 기본 제공 역할에 대 한 자세한 내용은 참조 하세요. [Azure에 대 한 기본 제공 역할](/azure/role-based-access-control/built-in-roles)입니다.

## <a name="manage-workspace-access"></a>작업 영역 액세스 관리

작업 영역의 소유자 인 경우에 추가 하 고 작업 영역에 대 한 역할을 제거할 수 있습니다. 또한 사용자에 게 역할을 할당할 수 있습니다. 다음 링크를 사용 하 여 액세스를 관리 하는 방법을 알아봅니다.
- [Azure 포털 UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure 리소스 관리자 템플릿](/azure/role-based-access-control/role-assignments-template)

설치한 후 합니다 [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), 사용자에 게 역할을 할당 하는 CLI 명령을 사용할 수도 있습니다.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` 필드는 기존 작업 영역 부모 구독 거주 하는 Azure Active Directory 인스턴스의 사용자의 전자 메일 주소입니다. 이 명령을 사용 하는 방법의 예는 다음과 같습니다.

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>사용자 지정 역할 만들기

기본 제공 역할이 충분 하지 않은 경우에 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할 있을 읽기, 쓰기, 삭제 및 해당 작업 영역에 리소스 권한을 계산 합니다. 특정 작업 영역 수준, 특정 리소스 그룹 수준에서 또는 특정 구독 수준에서 역할을 사용할 수 있습니다.

> [!NOTE]
> 해당 리소스 내에서 사용자 지정 역할을 만들려면 해당 수준에서 리소스의 소유자 여야 합니다.

사용자 지정 역할을 만들려면 먼저 권한 및 역할에 대 한 범위를 지정 하는 역할 정의 JSON 파일을 생성 합니다. 다음 예제에서는 특정 작업 영역 수준에서 범위가 지정 하는 "데이터 과학자" 라는 사용자 지정 역할을 정의 합니다.

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

변경할 수는 `AssignableScopes` 구독 수준, 리소스 그룹 수준 또는 특정 작업 영역 수준에서이 사용자 지정 역할의 범위를 설정 하는 필드입니다.

이 사용자 지정 역할은 다음 작업을 제외 하 고 작업 영역에서 모든 작업을 수행할 수 있습니다.

- 해당 만들거나 업데이트할 수 없습니다는 계산 리소스입니다.
- 계산 리소스를 삭제할 수 없는 합니다.
- 추가, 삭제 또는 역할 할당을 변경할 수 없습니다. 해당 합니다.
- 이 작업 영역을 삭제할 수 없습니다.

이 사용자 지정 역할을 배포 하려면 다음 Azure CLI 명령을 사용 합니다.

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

이 역할 배포 후 지정된 된 작업 영역에서 사용할 수 있는 됩니다. 이제 추가 하 고 Azure portal에서이 역할을 할당할 수 있습니다. 사용 하 여 사용자에 게이 역할을 할당할 수 있습니다 또는 `az ml workspace share` CLI 명령:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


자세한 내용은 [Azure 리소스에 대 한 사용자 지정 역할](/azure/role-based-access-control/custom-roles)입니다.

## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 보안 개요](concept-enterprise-security.md)
- [실험 및 가상 네트워크 내에서 유추 점수 매기기를 안전 하 게 실행](how-to-enable-virtual-network.md)
- [자습서: 모델 학습](tutorial-train-models-with-aml.md)
