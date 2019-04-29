---
title: Azure Analysis Services 서버 관리자 역할에 서비스 사용자 추가 | Microsoft Docs
description: 서버 관리자 역할에 자동화 서비스 사용자를 추가하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 701be795ca217c4a2dc5a7dbaa3a3717d16c85bc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61024627"
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
> Azure PowerShell cmdlet을 사용 하 여 서버 작업에 대 한 서비스 주체 실행 중인 스케줄러에도 속해 있어야 합니다 **소유자** 리소스에 대 한 역할 [azure 역할 기반 Access Control (RBAC)](../role-based-access-control/overview.md)합니다. 

## <a name="related-information"></a>관련 정보

* [SQL Server PowerShell 모듈 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


