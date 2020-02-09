---
title: 다중 테넌트 상호 작용의 특징-Azure AD | Microsoft Docs
description: 완전히 독립적인 리소스로 테넌트를 파악하여 Azure Active Directory 테넌트 관리
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ce791ee3536b9ab07605787209e59b7e5d60126
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026302"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>여러 Azure Active Directory 테넌트 간의 상호 작용 방식 이해

Azure AD(Azure Active Directory)의 각 테넌트는 완전히 독립된 리소스로, 관리하는 다른 테넌트와 논리적으로 독립된 피어입니다. 또한 테넌트 간에는 부모-자식 관계가 없습니다. 테넌트 간 독립성에는 리소스 독립성, 관리 독립성 및 동기화 독립성이 포함됩니다.

## <a name="resource-independence"></a>리소스 독립성
* 한 테넌트에서 리소스를 만들거나 삭제해도 다른 테넌트의 리소스에 영향을 주지 않습니다. 단, 외부 사용자는 부분적으로 제외됩니다. 
* 단일 테넌트에서 도메인 이름 중 하나를 사용하는 경우 다른 테넌트를 사용할 수 없습니다.

## <a name="administrative-independence"></a>관리 독립성
'Contoso' 테넌트의 관리자가 아닌 사용자가 테스트 테넌트 'Test'를 만들면 다음과 같이 수행됩니다.

* 기본적으로 테넌트를 만드는 사용자는 해당하는 새 테넌트에서 외부 사용자로 추가되고 해당 테넌트의 전역 관리자 역할이 할당됩니다.
* 'Test'의 관리자가 'Test' 테넌트에 대한 관리 권한을 특별히 부여하지 않으면 'Contoso' 테넌트의 관리자는 이 디렉터리에 대한 직접 관리 권한이 없습니다. 그러나 'Contoso'의 관리자가 'Test'를 만든 사용자 계정을 제어하는 경우 'Test' 테넌트에 대한 액세스를 제어할 수 있습니다.
* 단일 테넌트의 사용자에 관리자 역할을 추가/제거하는 경우 변경은 다른 테넌트에서 사용자가 가진 관리자 역할에 영향을 주지 않습니다.

## <a name="synchronization-independence"></a>동기화 독립성
각 Azure AD 테넌트를 독립적으로 구성하여 다음 중 하나의 단일 인스턴스에서 데이터를 동기화할 수 있습니다.

* 데이터를 단일 AD 포리스트와 동기화하는 Azure AD Connect 도구
* 데이터를 하나 이상의 온-프레미스 포리스트 및/또는 Azure AD 이외의 데이터 원본과 동기화하는 Forefront Identity Manager용 Azure Active 테넌트 커넥터

## <a name="add-an-azure-ad-tenant"></a>Azure AD 테넌트 추가
Azure Portal에서 Azure AD 테넌트를 추가하려면 Azure AD 전역 관리자인 계정으로 [Azure Portal](https://portal.azure.com)에 로그인하고 왼쪽에서 **새로 만들기**를 선택합니다.

> [!NOTE]
> 다른 Azure 리소스와 달리 테넌트는 Azure 구독의 자식 리소스가 아닙니다. Azure 구독이 취소 되거나 만료 된 경우에도 Azure PowerShell, Azure Graph API 또는 Microsoft 365 관리 센터를 사용 하 여 테넌트 데이터에 액세스할 수 있습니다. [다른 구독을 테넌트와 연결](../fundamentals/active-directory-how-subscriptions-associated-directory.md)할 수도 있습니다.
>

## <a name="next-steps"></a>다음 단계
Azure AD 라이선스 문제 및 모범 사례에 대한 광범위한 개요는 [Azure Active 테넌트 라이선스란?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)을 참조하세요.
