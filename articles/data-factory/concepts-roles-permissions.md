---
title: Azure Data Factory용 역할 및 권한 | Microsoft Docs
description: 데이터 팩터리를 만들고 하위 리소스를 사용하는 데 필요한 역할과 권한에 대해 설명합니다.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 19666eb668dd120c1705c6a62a8ba1abd2321026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261835"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Azure Data Factory용 역할 및 권한

이 문서에서는 Azure Data Factory 리소스를 만들고 관리하는 데 필요한 역할 및 이러한 역할을 통해 부여되는 권한에 대해 설명합니다.

## <a name="roles-and-requirements"></a>역할 및 요구 사항

데이터 팩터리 인스턴스를 만들려면 Azure에 로그인하는 데 사용할 사용자 계정이 *참여자* 또는 *소유자* 역할의 구성원이거나, Azure 구독의 *관리자*여야 합니다. 구독에 있는 권한을 보려면 Azure Portal에서 오른쪽 위 모서리에 있는 사용자 이름을 선택한 다음, **권한**을 선택합니다. 여러 구독에 액세스할 수 있는 경우 적절한 구독을 선택합니다. 

데이터 세트, 연결된 서비스, 파이프라인, 트리거 및 통합 런타임을 포함하여 Data Factory에 대한 자식 리소스를 만들고 관리하려면 다음 요구 사항을 적용해야 합니다.
- Azure Portal에서 자식 리소스를 만들고 관리하려면 리소스 그룹 수준 이상의 **Data Factory 기여자** 역할에 속해야 합니다.
- PowerShell 또는 SDK를 사용하여 자식 리소스를 만들고 관리하려면 리소스 수준 이상의 **기여자** 역할만으로도 충분합니다.

사용자를 역할에 추가하는 방법에 대한 지침 샘플은 [역할 추가](../billing/billing-add-change-azure-subscription-administrator.md) 문서를 참조하세요.

## <a name="set-up-permissions"></a>권한 설정

작성한 데이터 팩터리는 다른 사용자가 사용 가능하도록 설정할 수 있습니다. 다른 사용자에게 데이터 팩터리 액세스 권한을 제공하려는 경우 데이터 팩터리가 포함된 리소스 그룹의 기본 제공 **데이터 팩터리 참가자** 역할에 해당 사용자를 추가해야 합니다.

### <a name="scope-of-the-data-factory-contributor-role"></a>데이터 팩터리 참가자 역할의 범위

**데이터 팩터리 참가자** 역할의 멤버 자격이 있는 사용자는 다음 작업을 수행할 수 있습니다.
- 데이터 팩터리 및 하위 리소스(데이터 세트, 연결된 서비스, 파이프라인, 트리거 및 통합 런타임 포함)를 만들고 편집하고 삭제할 수 있습니다.
- Resource Manager 템플릿을 배포할 수 있습니다. Resource Manager 배포는 Azure Portal의 데이터 팩터리에서 사용되는 배포 방법입니다.
- 데이터 팩터리에 대한 App Insights 경고를 관리할 수 있습니다.
- 지원 티켓을 작성할 수 있습니다.

이 역할에 대한 자세한 내용은 [데이터 팩터리 참가자 역할](../role-based-access-control/built-in-roles.md#data-factory-contributor)을 참조하세요.

### <a name="resource-manager-template-deployment"></a>Resource Manager 템플릿 배포

리소스 그룹 이상 수준에서 **데이터 팩터리 참가자** 역할이 있는 사용자는 Resource Manager 템플릿을 배포할 수 있습니다. 즉, 해당 역할의 구성원은 Resource Manager 템플릿을 사용하여 데이터 팩터리와 해당 하위 리소스(데이터 세트, 연결된 서비스, 파이프라인, 트리거, 통합 런타임 포함)를 모두 배포할 수 있습니다. 하지만 이 역할의 멤버 자격이 있는 사용자가 다른 리소스를 만들 수 있는 것은 아닙니다.

Azure 리포지토리 및 GitHub에 대한 사용 권한은 Data Factory 사용 권한과 독립적입니다. 결과적으로, 독자 역할의 구성원으로서 리포지토리 사용 권한을 가진 사용자는 Data Factory 자식 리소스를 편집하고 리포지토리에 변경을 커밋할 수 있지만 이러한 변경 내용을 게시할 수는 없습니다.

> [!IMPORTANT]
> **데이터 팩터리 참가자** 역할을 통해 Resource Manager 템플릿을 배포하더라도 권한이 상승되지는 않습니다. 예를 들어 가상 머신 생성 권한이 없는데 Azure Virtual Machine을 만드는 템플릿을 배포하면 배포가 실패하며 권한 부여 오류가 표시됩니다.

### <a name="custom-scenarios-and-custom-roles"></a>사용자 지정 시나리오 및 사용자 지정 역할

데이터 팩터리 사용자에게 각기 다른 액세스 수준을 부여해야 하는 경우도 있습니다. 예를 들면 다음과 같습니다.
- 사용자에게 특정 데이터 팩터리에 대한 권한만 있는 그룹이 필요할 수 있습니다.
- 사용자가 데이터 팩터리를 하나 이상 모니터링할 수는 있지만 수정할 수는 없는 그룹이 필요할 수도 있습니다.

사용자 지정 역할을 만들고 해당 역할에 사용자를 할당하면 이러한 사용자 지정 시나리오를 수행할 수 있습니다. 사용자 지정 역할에 대한 내용은 [Azure의 사용자 지정 역할](..//role-based-access-control/custom-roles.md)을 참조하세요.

아래에는 사용자 지정 역할을 사용하여 수행할 수 있는 작업을 보여 주는 몇 가지 예가 나와 있습니다.

- 사용자가 Azure Portal에서 리소스 그룹의 모든 데이터 팩터리를 작성, 편집 또는 삭제할 수 있도록 설정합니다.

  사용자에 대해 리소스 그룹 수준에서 기본 제공 **데이터 팩터리 참가자** 역할을 할당합니다. 구독의 모든 데이터 팩터리 액세스를 허용하려는 경우 구독 수준에서 역할을 할당합니다.

- 사용자가 데이터 팩터리를 확인(읽기) 및 모니터링할 수는 있지만 편집하거나 변경하지는 못하도록 설정합니다.

  사용자에게 데이터 팩터리 리소스에 대한 기본 제공 **독자** 역할을 할당합니다.

- 사용자가 Azure Portal에서 데이터 팩터리 하나를 편집할 수 있도록 설정합니다.

  이 시나리오에는 역할 두 개를 할당해야 합니다.

  1. 데이터 팩터리 수준에서 기본 제공 **참가자** 역할을 할당합니다.
  2. 권한이 **Microsoft.Resources/deployments/** 인 사용자 지정 역할을 만듭니다. 리소스 그룹 수준에서 사용자에게 이 사용자 지정 역할을 할당합니다.

- 사용자가 PowerShell이나 SDK에서는 데이터 팩터리를 업데이트할 수 있지만 Azure Portal에서는 업데이트할 수 없도록 설정합니다.

  사용자에게 데이터 팩터리 리소스에 대한 기본 제공 **참가자** 역할을 할당합니다. 이 역할이 있는 사용자는 Azure Portal에서 리소스를 확인할 수는 있지만 **게시** 및 **모두 게시** 단추는 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Azure - [역할 정의 이해](../role-based-access-control/role-definitions.md)에서 역할에 대해 자세히 알아봅니다.

- [데이터 팩터리 참가자 역할](../role-based-access-control/built-in-roles.md#data-factory-contributor)에서 **Data Factory 참가자** 역할에 대해 자세히 알아봅니다.
