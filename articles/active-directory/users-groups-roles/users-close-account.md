---
title: 관리 되지 않는 Azure AD 조직에서 회사 또는 학교 계정 닫기
description: 관리 되지 않는 Azure Active Directory에서 회사 또는 학교 계정을 종료 하는 방법입니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: how-to
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87e03214e2a00a16064763f35f38bdd3ea0e6123
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798332"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-azure-ad-organization"></a>관리 되지 않는 Azure AD 조직에서 회사 또는 학교 계정 닫기

관리 되지 않는 Azure Active Directory (Azure AD) 조직의 사용자가 더 이상 해당 조직의 앱을 사용 하거나 해당 조직의 연결을 유지 관리할 필요가 없는 경우 언제 든 지 계정을 종료할 수 있습니다. 관리 되지 않는 조직에 전역 관리자가 없습니다. 관리 되지 않는 조직의 사용자는 관리자에 게 문의 하지 않고도 자신의 계정을 종료할 수 있습니다.

관리 되지 않는 조직의 사용자는 셀프 서비스 등록 중에 생성 되는 경우가 많습니다. 예는 무료 서비스에 등록 하는 조직의 정보 근로자 일 수 있습니다. 셀프 서비스 등록에 대 한 자세한 내용은 [Azure Active Directory에 대 한 셀프 서비스 등록 이란?](directory-self-service-signup.md)을 참조 하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>시작하기 전에

계정을 닫으려면 먼저 다음 항목을 확인 해야 합니다.

* 관리 되지 않는 Azure AD 조직의 사용자 인지 확인 합니다. 관리 되는 조직에 속해 있는 경우 계정을 종료할 수 없습니다. 관리 되는 조직에 속하고 계정을 종결 하려면 관리자에 게 문의 해야 합니다. 관리 되지 않는 조직에 속해 있는지 여부를 확인 하는 방법에 대 한 자세한 내용은 [관리 되지 않는 테 넌 트에서 사용자 삭제](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)를 참조 하세요.

* 유지 하려는 모든 데이터를 저장 합니다. 내보내기 요청을 제출 하는 방법에 대 한 자세한 내용은 [관리 되지 않는 테 넌 트에 대 한 시스템 생성 로그 액세스 및 내보내기](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)를 참조 하세요.

> [!WARNING]
> 계정을 닫는 것은 취소할 수 없습니다. 계정을 닫으면 모든 개인 데이터가 제거 됩니다. 계정 및 계정과 연결 된 데이터에 대 한 액세스 권한이 더 이상 없습니다.

## <a name="close-your-account"></a>계정 닫기

관리 되지 않는 회사 또는 학교 계정을 닫으려면 다음 단계를 수행 합니다.

1. 종료 하려는 계정을 사용 하 여 [계정을 닫으려면](https://go.microsoft.com/fwlink/?linkid=873123)로그인 합니다.

1. **내 데이터 요청**에서 **계정 닫기**를 선택 합니다.

    ![내 데이터 요청-계정 닫기](./media/users-close-account/close-account.png)

1. 확인 메시지를 검토 한 다음 **예**를 선택 합니다.

    ![내 데이터 요청-닫기 확인](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory의 셀프 서비스 등록이란?](directory-self-service-signup.md)
- [관리되지 않는 테넌트에서 사용자 삭제](/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [관리되지 않는 테넌트에 대한 시스템 생성 로그 액세스 및 내보내기](/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)