---
title: Azure AD에서 조건부 액세스를 사용 하 여 Azure 관리에 대 한 액세스 관리
description: Azure AD에서 조건부 액세스를 사용 하 여 Azure 관리에 대 한 액세스를 관리 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: f3341f1c30a1581b8507652c322c00581e3972aa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137402"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>조건부 액세스를 사용 하 여 Azure 관리에 대 한 액세스 관리

> [!CAUTION]
> Azure 관리에 대 한 액세스를 관리 하는 정책을 설정 하기 전에 조건부 액세스가 작동 하는 방식을 이해 해야 합니다. 포털에 대한 자신의 액세스를 차단할 수 있는 조건은 만들지 않아야 합니다.

Azure AD (Azure Active Directory)의 조건부 액세스는 지정 된 특정 조건에 따라 클라우드 앱에 대 한 액세스를 제어 합니다. 액세스를 허용 하려면 정책의 요구 사항이 충족 되었는지 여부에 따라 액세스를 허용 하거나 차단 하는 조건부 액세스 정책을 만듭니다. 

일반적으로 조건부 액세스를 사용 하 여 클라우드 앱에 대 한 액세스를 제어 합니다. 특정 조건(예: 로그인 위험, 위치 또는 디바이스)을 기준으로 Azure Management에 대한 액세스를 제어하고 다단계 인증 등의 요구 사항을 시행하는 정책을 설정할 수도 있습니다.

Azure Management에 대한 정책을 만들려면 정책을 적용할 앱을 선택할 때 **클라우드 앱**에서 **Microsoft Azure Management**를 선택합니다.

![Azure 관리에 대한 조건부 액세스](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

만든 정책은 다음을 비롯 한 모든 Azure 관리 끝점에 적용 됩니다.

- Azure 포털
- Azure Resource Manager 공급자
- 클래식 서비스 관리 Api
- Azure PowerShell
- Visual Studio 구독 관리자 포털
- Azure DevOps
- Azure Data Factory 포털

정책은 Azure Resource Manager API를 호출하는 Azure PowerShell에 적용됩니다. Microsoft Graph를 호출하는 [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2)에는 적용되지 않습니다.


조건부 액세스를 설정 하 고 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory의 조건부 액세스](../active-directory/active-directory-conditional-access-azure-portal.md)를 참조 하세요.
