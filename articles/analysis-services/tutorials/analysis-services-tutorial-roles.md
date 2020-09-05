---
title: 자습서 - Azure Analysis Services 역할 구성 | Microsoft Docs
description: Azure Portal 또는 SQL Server Management Studio를 사용하여 Azure Analysis Services 관리자 및 사용자 역할을 구성하는 방법에 대해 알아봅니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 6e757260c7cd8945ff75dd0e760f3afeffd992f9
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "84016392"
---
# <a name="tutorial-configure-server-administrator-and-user-roles"></a>자습서: 서버 관리자 및 사용자 역할 구성

 이 자습서에서는 SSMS(SQL Server Management Studio)를 사용하여 Azure에서 서버에 연결한 다음, 서버 관리자 및 모델 데이터베이스 역할을 구성합니다. [TMSL(테이블 형식 모델 스크립팅 언어)](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)에 대해서도 소개합니다. TMSL은 1200 이상 호환성 수준의 테이블 형식 모델용 JSON 기반 스크립팅 언어입니다. 이는 많은 테이블 형식 모델링 작업을 자동화하는 데 사용할 수 있습니다. TMSL은 PowerShell에서 자주 사용되지만, 이 자습서에서는 SSMS에서 XMLA 쿼리 편집기를 사용합니다. 이 자습서에서 수행하는 작업은 다음과 같습니다. 
  
> [!div class="checklist"]
> * 포털에서 서버 이름 가져오기
> * SSMS를 사용하여 서버에 연결
> * 서버 관리자 역할에 사용자 또는 그룹 추가 
> * 모델 데이터베이스 관리자 역할에 사용자 또는 그룹 추가
> * 새 모델 데이터베이스 역할 추가 및 사용자 또는 그룹 추가

Azure Analysis Services의 사용자 보안에 대한 자세한 내용은 [인증 및 사용자 권한](../analysis-services-manage-users.md)을 참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항

- 구독에 Azure Active Directory가 있어야 합니다.
- 구독에 만든 [Azure Analysis Services 서버](../analysis-services-create-server.md)가 있어야 합니다.
- [서버 관리자](../analysis-services-server-admins.md) 권한이 있어야 합니다.
- 서버에 [adventureworks 샘플 모델을 추가](../analysis-services-create-sample-model.md)합니다.
- [최신 버전의 SSMS(SQL Server Management Studio)를 설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[포털](https://portal.azure.com/)에 로그인합니다.

## <a name="get-server-name"></a>서버 이름 가져오기
SSMS에서 서버에 연결하려면 먼저 서버 이름이 필요합니다. 포털에서 서버 이름을 가져올 수 있습니다.

**Azure Portal** > 서버 > **개요** > **서버 이름**에서 서버 이름을 복사합니다.
   
   ![Azure에서 서버 이름 가져오기](./media/analysis-services-tutorial-roles/aas-copy-server-name.png)

## <a name="connect-in-ssms"></a>SSMS에서 연결

나머지 작업에서는 SSMS를 사용하여 서버에 연결하고 관리합니다.

1. SSMS > **개체 탐색기**에서 **연결** > **Analysis Services**를 클릭합니다.

    ![연결](./media/analysis-services-tutorial-roles/aas-ssms-connect.png)

2. **서버에 연결** 대화 상자의 **서버 이름**에 포털에서 복사한 서버 이름을 붙여넣습니다. **인증**에서 **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택하고, 사용자 계정을 입력한 다음, **연결**을 누릅니다.
   
    ![SSMS에서 연결](./media/analysis-services-tutorial-roles/aas-connect-ssms-auth.png)

    > [!TIP]
    > [Active Directory - MFA 지원을 통한 유니버설 인증]을 선택하는 것이 좋습니다. 이 인증 유형은 [비대화형 및 다단계 인증](../../azure-sql/database/authentication-mfa-ssms-overview.md)을 지원합니다. 

3. **개체 탐색기**에서 서버 개체를 펼쳐서 살펴봅니다. 서버 속성을 마우스 오른쪽 단추를 클릭하여 확인합니다.
   
    ![SSMS에서 연결](./media/analysis-services-tutorial-roles/aas-connect-ssms-objexp.png)

## <a name="add-a-user-account-to-the-server-administrator-role"></a>서버 관리자 역할에 사용자 계정 추가

이 작업에서는 Azure AD의 사용자 또는 그룹 계정을 서버 관리자 역할에 추가합니다. 보안 그룹을 지정하는 경우 `obj:groupid@tenantid`를 사용합니다.

1. **개체 탐색기**에서 서버 이름을 마우스 오른쪽 단추로 클릭한 다음, **속성**을 클릭합니다. 
2. **Analysis Server 속성** 창에서 **보안** > **추가**를 차례로 클릭합니다.
3. **사용자 또는 그룹 선택** 창에서 Azure AD의 사용자 또는 그룹 계정을 입력한 다음, **추가**를 클릭합니다. 
   
     ![서버 관리자 추가](./media/analysis-services-tutorial-roles/aas-add-server-admin.png)

4. **확인**을 클릭하여 **Analysis Server 속성**을 닫습니다.

    > [!TIP]
    > 포털에서 **Analysis Services 관리자**를 사용하여 서버 관리자를 추가할 수도 있습니다. 

## <a name="add-a-user-to-the-model-database-administrator-role"></a>모델 데이터베이스 관리자 역할에 사용자 추가

이 작업에서는 모델에 이미 있는 Internet Sales Administrator(인터넷 판매 관리자) 역할에 사용자 또는 그룹 계정을 추가합니다. 이 역할에는 adventureworks 샘플 모델 데이터베이스에 대한 모든 권한(관리자) 권한이 있습니다. 이 작업에서는 만든 스크립트에서 [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) TMSL 명령을 사용합니다.

1. **개체 탐색기**에서 **데이터베이스** > **adventureworks** > **역할**을 차례로 펼칩니다. 
2. **Internet Sales Administrator**를 마우스 오른쪽 단추로 클릭한 다음, **역할 스크립팅** > **만들기 또는 다음으로 바꾸기** > **새 쿼리 편집기 창**을 차례로 클릭합니다.

    ![새 쿼리 편집기 창](./media/analysis-services-tutorial-roles/aas-add-db-admin.png)

3. **XMLAQuery**에서 **"memberName":** 값을 Azure AD의 사용자 또는 그룹 계정으로 변경합니다. 로그인한 계정은 기본적으로 포함됩니다. 그러나 이미 서버 관리자이기 때문에 사용자 고유의 계정을 추가할 필요는 없습니다.

    ![XMLA 쿼리의 TMSL 스크립트](./media/analysis-services-tutorial-roles/aas-add-db-admin-script.png)

4. **F5** 키를 눌러 스크립트를 실행합니다.


## <a name="add-a-new-model-database-role-and-add-a-user-or-group"></a>새 모델 데이터베이스 역할 추가 및 사용자 또는 그룹 추가

이 작업에서는 TMSL 스크립트에서 [Create](https://docs.microsoft.com/analysis-services/tmsl/create-command-tmsl) 명령을 사용하여 새 Internet Sales Global(인터넷 판매 전역) 역할을 만들고, 역할에 대한 *읽기* 권한을 지정하고, Azure AD에서 사용자 또는 그룹 계정을 추가합니다.

1. **개체 탐색기**에서 **adventureworks**를 마우스 오른쪽 단추로 클릭한 다음, **새 쿼리** > **XMLA**를 차례로 클릭합니다. 
2. 다음 TMSL 스크립트를 복사하여 쿼리 편집기에 붙여넣습니다.

    ```JSON
    {
    "create": {
      "parentObject": {
        "database": "adventureworks",
       },
       "role": {
         "name": "Internet Sales Global",
         "description": "All users can query model data",
         "modelPermission": "read",
         "members": [
           {
             "memberName": "globalsales@adventureworks.com",
             "identityProvider": "AzureAD"
           }
         ]
       }
      }
    }
    ```

3. `"memberName": "globalsales@adventureworks.com"` 개체 값을 Azure AD의 사용자 또는 그룹 계정으로 변경합니다.
4. **F5** 키를 눌러 스크립트를 실행합니다.

## <a name="verify-your-changes"></a>변경 내용 확인

1. **개체 탐색기**에서 서버 이름을 클릭한 다음, **새로 고침**을 클릭하거나 **F5** 키를 누릅니다.
2. **데이터베이스** > **adventureworks** > **역할**을 차례로 펼칩니다. 이전 작업에서 추가한 사용자 계정 및 새 역할 변경 내용이 표시되는지 확인합니다.   

    ![개체 탐색기에서 확인](./media/analysis-services-tutorial-roles/aas-connect-ssms-verify.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 사용자 또는 그룹 계정 및 역할을 삭제합니다. 이렇게 하려면 **역할 속성** > **멤버 자격**을 차례로 사용하여 사용자 계정을 제거하거나, 역할을 마우스 오른쪽 단추로 클릭하고 **삭제**를 클릭합니다.


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure AS 서버에 연결하고 SSMS에서 adventureworks 샘플 모델 데이터베이스 및 속성을 탐색하는 방법을 알아보았습니다. 또한 SSMS 및 TMSL 스크립트를 사용하여 기존 또는 새 역할에 사용자 또는 그룹을 추가하는 방법도 알아보았습니다. 이제 서버 및 샘플 모델 데이터베이스에 대해 구성된 사용자 권한이 있으므로 Power BI와 같은 클라이언트 애플리케이션을 사용하여 사용자와 다른 사용자가 연결할 수 있습니다. 자세한 내용은 다음 자습서로 계속 진행하세요. 

> [!div class="nextstepaction"]
> [자습서: Power BI Desktop을 사용하여 연결](analysis-services-tutorial-pbid.md)

