---
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119962"
---
#### <a name="prerequisites"></a>필수 조건
* Azure 계정의 경우 [무료 계정](https://azure.microsoft.com/free)
* [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 계정 

Logic Apps에서 Dynamics 계정을 사용하기 전에 CRM Online 계정에 연결하도록 Logic Apps에 권한을 부여합니다. Azure 포털의 논리 앱 내에서 이 작업을 쉽게 수행할 수 있습니다. 

다음 단계를 사용하여 CRM Online 계정에 연결하도록 Logic Apps에 권한을 부여합니다.

1. 논리 앱을 만듭니다. Logic Apps 디자이너의 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택한 다음 검색 상자에 "dynamics"를 입력합니다. 트리거 또는 동작 중 하나를 선택합니다.  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. 이전에 Dynamics에 대한 연결을 만들지 않은 경우 Dynamics 자격 증명을 사용하여 로그인하라는 메시지가 표시됩니다.  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. **로그인**을 선택하고 사용자 이름 및 암호를 입력합니다. **로그인**을 선택합니다. 
   
    이러한 자격 증명을 사용하여 Dynamics 계정의 데이터에 연결하도록 Logic Apps에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다. 
4. 연결이 만들어졌는지 확인합니다. 이제 논리 앱의 다른 단계를 진행합니다.  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

