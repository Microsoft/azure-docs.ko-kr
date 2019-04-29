---
ms.openlocfilehash: d249a205c64f4e067f2d81c7e1068c8ad9756958
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130061"
---
### <a name="prerequisites"></a>필수 조건
* [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) 계정  

논리 앱에서 SFTP 계정을 사용하려면 먼저 SFTP 계정에 연결하도록 논리 앱에 권한을 부여해야 합니다. 다행히 Azure Portal의 논리 앱 내에서 이를 쉽게 수행할 수 있습니다.  

SFTP 계정에 연결하도록 논리 앱에 권한을 부여하는 단계는 다음과 같습니다.  

1. 논리 앱 디자이너에서 SFTP에 대한 연결을 만들려면 드롭다운 목록에서 **Microsoft 관리 API 표시**를 선택한 다음 검색 상자에 *SFTP*를 입력합니다. **SFTP - 파일을 추가하거나 수정할 때** 트리거를 선택합니다.  
   ![SFTP 온라인 연결 이미지 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. 이전에 SFTP에 대한 연결을 만들지 않은 경우 SFTP 자격 증명을 제공하라는 메시지가 표시됩니다. 이러한 자격 증명을 사용하여 SFTP 계정의 데이터에 연결하도록 논리 앱에 권한을 부여하고 해당 데이터에 액세스할 수 있습니다.  
   ![SFTP 온라인 연결 이미지 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. 연결이 만들어졌으므로 이제 논리 앱의 다른 단계를 진행할 수 있습니다.   
   ![SFTP 온라인 연결 이미지 3](./media/connectors-create-api-sftp/sftp-3.png) 

