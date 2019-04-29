---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60419900"
---
1. [Azure Portal][Azure portal]에 로그인합니다.
2. 왼쪽 메뉴에서 **+ 리소스 만들기**를 선택합니다. 그런 다음, **통합** > **릴레이**를 선택합니다. 목록에서 **릴레이**를 찾을 수 없으면 오른쪽 위 모서리에서 **모두 보기**를 선택합니다. 
3. **네임스페이스 만들기** 아래에서 네임스페이스 이름을 입력합니다. 시스템에서 사용 가능한 이름인지 즉시 확인합니다.
4. **구독** 상자에서 네임스페이스를 만들 Azure 구독을 선택합니다.
5. [리소스 그룹](../articles/azure-resource-manager/manage-resource-groups-portal.md) 상자에서 네임스페이스를 배치할 기존 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.  
6. **위치**에서 네임스페이스가 호스팅되어야 하는 국가 또는 지역을 선택합니다.
   
    ![네임스페이스 만들기][create-namespace]
7. **만들기**를 선택합니다. 시스템에서 네임스페이스를 만들고 이를 사용하도록 설정합니다. 몇 분 후 시스템이 계정에 대한 리소스를 프로비전합니다.

### <a name="get-management-credentials"></a>관리 자격 증명 가져오기

1. **모든 리소스**를 선택하고, 새로 만든 네임스페이스 이름을 선택합니다.
2. Relay 네임스페이스 아래에서 **공유 액세스 정책**을 선택합니다.  
3. **공유 액세스 정책** 아래에서 **RootManageSharedAccessKey**를 선택합니다.
   
    ![connection-info][connection-info]
4. **정책: RootManageSharedAccessKey** 아래에서 **연결 문자열 - 기본 키** 옆에 있는 **복사** 단추를 선택합니다. 이렇게 하면 나중에 사용할 연결 문자열을 클립보드에 복사합니다. 메모장이나 기타 다른 위치에 임시로 이 값을 붙여 넣습니다.
   
    ![connection-string][connection-string]

5. 앞의 단계를 반복하여 나중에 사용할 **기본 키**의 값을 복사하여 임시 위치에 붙여넣습니다.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
