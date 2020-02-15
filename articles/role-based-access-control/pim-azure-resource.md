---
title: Azure AD 및 PIM을 사용 하 여 Azure 리소스에 대 한 액세스 관리
description: Azure Active Directory PIM(Privileged Identity Management) 및 RBAC(역할 기반 액세스 제어)를 사용하여 Azure 리소스에 대한 액세스를 관리하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: ba06b8dd-4a74-4bda-87c7-8a8583e6fd14
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/30/2018
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 53c6375ce04425eae27f4c935e613cc27d058dc8
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138045"
---
# <a name="manage-access-to-azure-resources-with-azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management를 사용하여 Azure 리소스에 대한 액세스 관리

권한 있는 계정을 악의적인 사이버 공격으로부터 보호하기 위해 Azure Active Directory PIM(Privileged Identity Management)를 사용하여 권한 노출 시간을 줄이고 보고서 및 경고를 통해 해당 사용에 대한 가시성을 증가시킬 수 있습니다. 사용자가 해당 권한 JIT("just in time")만을 사용하도록 제한하거나 권한이 자동으로 해지되는 짧은 기간 동안 권한을 할당하여 PIM에서 이 작업을 수행합니다. 

이제 RBAC(역할 기반 액세스 제어)와 PIM을 사용하여 Azure 리소스에 대한 액세스를 관리하고 제어하고 모니터링할 수 있습니다. PIM은 기본 제공 사용자 지정 역할의 멤버 자격을 관리하여 다음을 수행할 수 있습니다. 

- Azure 리소스에 "just-in-time" 주문형 액세스 사용
- 할당된 사용자 및 그룹에 대한 리소스 액세스 권한 자동 만료
- 빠른 작업 또는 대기 중 일정의 Azure 리소스에 대한 임시 액세스 권한 할당
- 새 사용자 또는 그룹에 리소스 액세스 권한이 할당될 때 및 적격 할당을 활성화할 때 경고 받기

자세한 내용은 [Azure AD Privileged Identity Management란?](../active-directory/privileged-identity-management/pim-configure.md)을 참조하세요.