---
title: Azure Analysis Services에서 데이터베이스 역할 및 사용자 관리 | Microsoft Docs
description: Azure의 Analysis Services 서버에서 데이터베이스 역할 및 사용자를 관리하는 방법을 알아봅니다.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 462625ce61f4538aa0769667648e07cc6307cbb3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023633"
---
# <a name="manage-database-roles-and-users"></a>데이터베이스 역할 및 사용자 관리

model 데이터베이스 수준에서 모든 사용자는 역할에 속해야 합니다. 역할은 model 데이터베이스에 대한 특정 사용 권한이 있는 사용자를 정의합니다. 역할에 추가된 모든 사용자 또는 보안 그룹은 서버와 동일한 구독의 Azure AD 테넌트에 계정이 있어야 합니다. 

역할을 정의하는 방법은 사용하는 도구에 따라 다르지만 결과는 동일합니다.

역할 권한은 다음과 같습니다.
*  **관리자** - 사용자가 데이터베이스에 대한 모든 권한을 갖습니다. 관리자 권한이 있는 데이터베이스 역할은 서버 관리자와 다릅니다.
*  **처리** - 사용자가 데이터베이스에서 프로세스 작업에 연결하고 수행할 수 있으며 model 데이터베이스 데이터를 분석할 수 있습니다.
*  **읽기** - 사용자가 클라이언트 애플리케이션을 사용하여 model 데이터베이스 데이터에 연결하고 분석할 수 있습니다.

테이블 형식 모델 프로젝트를 만들 때 SSDT의 역할 관리자를 사용하여 역할을 만들고 해당 역할에 사용자나 그룹을 추가합니다. 서버에 배포된 경우 SSMS, [Analysis Services PowerShell cmdlet](/sql/analysis-services/powershell/analysis-services-powershell-reference) 또는 TMSL([Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx))(테이블 형식 모델 스크립트 언어)를 사용하여 역할 및 사용자 멤버를 추가하거나 제거합니다.

> [!NOTE]
> 보안 그룹에는 `True`로 설정된 `MailEnabled` 속성이 있어야 합니다.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>SSDT에서 역할 및 사용자를 추가하거나 관리하려면  
  
1.  SSDT > **테이블 형식 모델 탐색기**에서 **역할**을 마우스 오른쪽 단추로 클릭합니다.  
  
2.  **역할 관리자**에서 **새로 만들기**를 클릭합니다.  
  
3.  역할의 이름을 입력합니다.  
  
     기본적으로 기본 역할의 이름은 새 역할을 만들 때마다 증분식으로 번호가 지정됩니다. 따라서 재무 관리자 또는 인적 자원 전문가와 같이 멤버 유형을 분명하게 식별하는 이름을 입력하는 것이 좋습니다.  
  
4.  다음 사용 권한 중 하나를 선택합니다.  
  
    |사용 권한|설명|  
    |----------------|-----------------|  
    |**없음**|멤버는 모델 스키마를 수정할 수 없으며 데이터를 쿼리할 수 없습니다.|  
    |**읽기**|멤버는 행 필터를 기반으로 데이터를 쿼리할 수 있지만 모델 스키마를 수정할 수 없습니다.|  
    |**읽기 및 처리**|멤버는 행 수준 필터를 기반으로 데이터를 쿼리하고 처리 및 모두 처리 작업을 실행할 수 있지만 모델 스키마를 수정할 수 없습니다.|  
    |**프로세스**|멤버는 처리 및 모두 처리 작업을 실행할 수 있습니다. 모델 스키마를 수정할 수 없으며 데이터를 쿼리할 수 없습니다.|  
    |**관리자**|멤버는 모델 스키마를 수정하고 모든 데이터를 쿼리할 수 있습니다.|   
  
5.  만들려는 역할에 읽기 또는 읽기 및 처리 권한이 있는 경우 DAX 수식을 사용하여 행 필터를 추가할 수 있습니다. **행 필터** 탭을 클릭하고 테이블을 선택한 다음 **DAX 필터** 필드를 클릭하고 DAX 수식을 입력합니다.
  
6.  **멤버** > **외부 추가**를 클릭합니다.  
  
8.  **외부 멤버 추가**에서 Azure AD 테넌트의 사용자 또는 그룹을 메일 주소로 입력합니다. [확인]을 클릭하고 [역할 관리자]를 닫으면 역할 및 역할 멤버가 [테이블 형식 모델 탐색기]에 나타납니다. 
 
     ![테이블 형식 모델 탐색기의 역할 및 사용자](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Azure Analysis Services 서버에 배포합니다.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>SSMS에서 역할 및 사용자를 추가하거나 관리하려면

배포된 model 데이터베이스에 역할 및 사용자를 추가하려면 서버 관리자로 서버에 연결되어 있거나 관리자 권한이 있는 데이터베이스 역할이 이미 있어야 합니다.

1. 개체 탐색기에서 **역할**을 마우스 오른쪽 단추로 클릭 > **새 역할**을 클릭합니다.

2. **역할 만들기**에서 역할 이름 및 설명을 입력합니다.

3. 사용 권한을 선택합니다.

   |사용 권한|설명|  
   |----------------|-----------------|  
   |**모든 권한(관리자)**|멤버는 모델 스키마, 프로세스를 수정할 수 있으며 모든 데이터를 쿼리할 수 있습니다.| 
   |**데이터베이스 처리**|멤버는 처리 및 모두 처리 작업을 실행할 수 있습니다. 모델 스키마를 수정할 수 없으며 데이터를 쿼리할 수 없습니다.|  
   |**읽기**|멤버는 행 필터를 기반으로 데이터를 쿼리할 수 있지만 모델 스키마를 수정할 수 없습니다.|  
  
4. **멤버 자격**을 클릭한 다음 Azure AD 테넌트의 사용자나 그룹을 메일 주소로 입력합니다.

     ![사용자 추가](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. 만들려는 역할에 읽기 권한이 있는 경우 DAX 수식을 사용하여 행 필터를 추가할 수 있습니다. **행 필터**를 클릭하고 테이블을 선택한 다음 **DAX 필터** 필드에 DAX 수식을 입력합니다. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>TMSL 스크립트를 사용하여 역할 및 사용자를 추가하려면

SSMS에서 또는 PowerShell을 사용하여 XMLA 창에서 TMSL 스크립트를 실행할 수 있습니다. [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) 명령 및 [Roles](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) 개체를 사용합니다.

**샘플 TMSL 스크립트**

이 샘플에서는 B2B 외부 사용자 및 그룹을 SalesBI 데이터베이스에 대한 읽기 권한이 있는 분석가 역할에 추가합니다. 외부 사용자와 그룹이 모두 동일한 Azure AD 테넌트에 있어야 합니다.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>PowerShell을 사용하여 역할 및 사용자를 추가하려면

[SqlServer](/sql/analysis-services/powershell/analysis-services-powershell-reference) 모듈은 TMSL(테이블 형식 모델 스크립트 언어) 쿼리 또는 스크립트를 허용하는 범용 Invoke-ASCmd cmdlet 및 작업 관련 데이터베이스 관리 cmdlet을 제공합니다. 다음 cmdlet은 데이터베이스 역할 및 사용자 관리에 사용됩니다.
  
|Cmdlet|설명|
|------------|-----------------| 
|[Add-RoleMember](/sql/analysis-services/powershell/analysis-services-powershell-reference)|데이터베이스 역할에 구성원을 추가합니다.| 
|[Remove-RoleMember](/sql/analysis-services/powershell/analysis-services-powershell-reference)|데이터베이스 역할에서 구성원을 제거합니다.|   
|[Invoke-ASCmd](/sql/analysis-services/powershell/analysis-services-powershell-reference)|TMSL 스크립트를 실행합니다.|

## <a name="row-filters"></a>행 필터  

행 필터는 특정 역할의 멤버가 쿼리할 수 있는 테이블의 행을 정의합니다. 행 필터는 DAX 수식을 사용하여 모델의 각 테이블에 대해 정의됩니다.  
  
행 필터는 읽기와 읽기 및 처리 권한이 있는 역할에 대해서만 정의할 수 있습니다. 기본적으로 특정 테이블에 대해 행 필터가 정의되지 않은 경우 다른 테이블에서 교차 필터링을 적용하지 않는 한 멤버는 테이블의 모든 행을 쿼리할 수 있습니다.
  
 해당 특정 역할의 멤버가 쿼리할 수 있는 행을 정의하려면 행 필터에 DAX 수식이 필요하며, 이 수식은 TRUE/FALSE 값으로 계산되어야 합니다. DAX 수식에 포함되지 않은 행은 쿼리할 수 없습니다. 예를 들어 *=Customers [Country] = “USA”* 같은 행 필터 식을 사용하는 Customers 테이블에서 Sales 역할의 멤버는 USA의 고객만 볼 수 있습니다.  
  
행 필터는 지정된 행과 관련 행에 적용됩니다. 테이블에 여러 관계가 있는 경우 필터는 활성 관계에 대한 보안을 적용합니다. 행 필터는 관련 테이블에 대해 정의된 다른 행 필터와 교차됩니다. 예를 들면 다음과 같습니다.  
  
|테이블|DAX 식|  
|-----------|--------------------|  
|지역|=Region[Country]="USA"|  
|ProductCategory|=ProductCategory[Name]="Bicycles"|  
|트랜잭션|=Transactions[Year]=2016|  
  
 결과적으로 멤버는 고객이 USA에 있고, 제품 범주는 bicycles이며, 연도는 2016년인 데이터 행을 쿼리할 수 있습니다. 사용자는 이러한 권한을 부여하는 다른 역할의 멤버가 아닌 한 USA 외부의 거래, bicycles가 아닌 거래 또는 2016년에 수행되지 않은 거래를 쿼리할 수 없습니다.
  
 *=FALSE()* 필터를 사용하여 전체 테이블의 모든 행에 대한 액세스를 거부할 수 있습니다.

## <a name="next-steps"></a>다음 단계

  [서버 관리자 관리](analysis-services-server-admins.md)   
  [PowerShell을 사용하여 Azure Analysis Services 관리](analysis-services-powershell.md)  
  [TMSL(테이블 형식 모델 스크립트 언어) 참조](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

