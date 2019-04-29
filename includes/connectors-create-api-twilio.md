---
ms.openlocfilehash: 845b27b8efd66de87ec08e0b5b81bcc332dffdfb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129969"
---
### <a name="prerequisites"></a>필수 조건
* Twilio 계정
* SMS를 받을 수 있다고 확인된 Twilio 전화 번호
* SMS를 보낼 수 있다고 확인된 Twilio 전화 번호

> [!NOTE]
> Twilio 체험 계정을 사용하는 경우 **확인된** 전화 번호에만 SMS를 보낼 수 있습니다.  
> 
> 

논리 앱에서 Twilio 계정을 사용하려면 먼저 Twilio 계정에 연결하도록 논리 앱에 권한을 부여해야 합니다. 다행히 Azure Portal의 논리 앱 내에서 이를 쉽게 수행할 수 있습니다. 

Twilio 계정에 연결하도록 논리 앱에 권한을 부여하는 단계는 다음과 같습니다.

1. 논리 앱 디자이너에서 Twilio에 대한 연결을 만들려면 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택한 다음 검색 상자에 *Twilio*를 입력합니다. 사용할 트리거 또는 동작을 선택합니다.  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. 이전에 Twilio에 대한 연결을 만들지 않은 경우 Twilio 자격 증명을 제공하라는 메시지가 표시됩니다. 이러한 자격 증명을 사용하여 Twilio 계정의 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다.  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Twilio의 대시보드에서는 **Twilio 계정 ID** 및 **Twilio 액세스 토큰**이 필요하므로 이제 Twilio 계정에 로그인하여 두 가지 정보를 가져옵니다.  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio 및 논리 앱에서는 서로 다른 이름을 사용하여 이러한 두 가지 정보를 식별합니다. 이러한 정보를 Logic Apps 대화 상자에 매핑하는 방법은 다음과 같습니다. ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. **연결 만들기** 단추를 선택합니다.  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. 연결이 만들어졌으므로 이제 논리 앱의 다른 단계를 진행할 수 있습니다.  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

