---
title: Azure Analysis Services 관리자 역할에 서비스 주체 추가 | Microsoft Docs
description: Azure Analysis Services 서버 관리자 역할에 automation 서비스 주체를 추가 하는 방법에 대해 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0a3a86283c8ec9876fbec049a2a1a110eb1a80f3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573608"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>서버 관리자 역할에 서비스 사용자 추가 

 무인 PowerShell 태스크를 자동화하려면 서비스 사용자가 관리할 Analysis Services 서버에 대해 **서버 관리자** 권한이 있어야 합니다. 이 문서에서는 Azure AS 서버에서 서버 관리자 역할에 서비스 사용자를 추가하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에
이 태스크를 완료하기 전에 Azure Active Directory에 등록된 서비스 사용자가 있어야 합니다.

[서비스 사용자 만들기 - Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[서비스 사용자 만들기 - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>필요한 사용 권한
이 태스크를 완료하려면 Azure AS 서버에서 [서버 관리자](analysis-services-server-admins.md) 권한이 있어야 합니다. 

## <a name="add-service-principal-to-server-administrators-role"></a>서버 관리자 역할에 서비스 사용자 추가

1. SSMS에서 Azure AS 서버에 연결합니다.
2. **서버 속성** > **보안**에서 **추가**를 클릭합니다.
3. **사용자 또는 그룹 선택**에서 등록된 앱을 이름으로 검색하고 **추가**를 클릭합니다.

    ![서비스 사용자 계정 검색](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. 서비스 사용자 계정 ID를 확인한 다음, **확인**을 클릭합니다.
    
    ![서비스 사용자 계정 검색](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Azure PowerShell cmdlet을 사용 하는 서버 작업의 경우 스케줄러를 실행 하는 서비스 주체는 [AZURE RBAC (역할 기반 Access Control)](../role-based-access-control/overview.md)의 리소스에 대한 **소유자** 역할에도 속해야 합니다. 

## <a name="related-information"></a>관련 정보

* [SQL Server PowerShell 모듈 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


