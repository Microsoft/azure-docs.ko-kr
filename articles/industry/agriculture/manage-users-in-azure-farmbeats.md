---
title: Azure FarmBeats에서 사용자 관리
description: 이 문서에서는 Azure FarmBeats에서 사용자를 관리 하는 방법을 설명 합니다.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 208a302a0702a5c4de4d194c9e72f562aaf758c4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482297"
---
# <a name="manage-users"></a>사용자 관리

Azure FarmBeats에는 Azure Active Directory (Azure AD) 인스턴스에 속한 사용자를 위한 사용자 관리가 포함 되어 있습니다. Azure FarmBeats 인스턴스에 사용자를 추가 하 여 Api에 액세스 하 고, 생성 된 맵을 확인 하 고, 팜에서 센서 원격 분석에 액세스할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- Azure FarmBeats를 설치 해야 합니다. 자세한 내용은 [Azure FarmBeats 설치](install-azure-farmbeats.md)를 참조 하세요.
- Azure FarmBeats 인스턴스에서 추가 하거나 제거할 사용자의 전자 메일 Id입니다.

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats 사용자 관리

Azure FarmBeats는 인증, 액세스 제어 및 역할에 Azure AD를 사용 합니다. Azure FarmBeats의 사용자로 Azure AD 테넌트의 사용자를 추가할 수 있습니다.

> [!NOTE]
> Azure FarmBeats 사용자로 추가 하려는 사용자가 Azure AD 테넌트에 없는 경우 "Azure AD 사용자 추가" 섹션의 지침에 따라 설치를 완료 합니다.

Azure FarmBeats는 두 가지 유형의 사용자 역할을 지원 합니다.

 - **관리자**: Azure FarmBeats Datahub api에 대 한 모든 권한입니다. 이 역할의 사용자는 모든 Azure FarmBeats Datahub 개체를 쿼리하고 FarmBeats Accelerator에서 모든 작업을 수행할 수 있습니다.
 - **읽기 전용**: FarmBeats Datahub api에 대 한 읽기 전용 액세스입니다. 사용자는 Datahub Api, 액셀러레이터 키 대시보드 및 맵을 볼 수 있습니다. 읽기 전용 액세스 권한이 있는 사용자는 맵 생성, 장치 연결, 팜 만들기 등의 작업을 수행할 수 없습니다.

## <a name="add-users-to-azure-farmbeats"></a>Azure FarmBeats에 사용자 추가

Azure FarmBeats에 사용자를 추가 하려면:

1. 액셀러레이터에 로그인 한 후 **설정** 아이콘을 선택 합니다.
2. **Access Control**를 선택 합니다.

    ![팜 설정 창](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. 액세스 권한을 부여 하려는 사용자의 전자 메일 ID를 입력 합니다.
4. 원하는 역할 ( **관리자** 또는 읽기 전용 **)** 을 선택 합니다.
5. **역할 추가**를 선택 합니다.

추가 된 사용자는 이제 Azure FarmBeats (Datahub 및 Accelerator 모두)에 액세스할 수 있습니다.

## <a name="delete-users-from-azure-farmbeats"></a>Azure FarmBeats에서 사용자 삭제

Azure FarmBeats 시스템에서 사용자를 제거 하려면 다음을 수행 합니다.

1. 액셀러레이터에 로그인 한 후 **설정** 아이콘을 선택 합니다.
2. **Access Control**를 선택 합니다.
3. **삭제**를 선택합니다.

   사용자가 시스템에서 삭제 됩니다. 다음과 같은 확인 메시지가 표시 됩니다.

   ![Azure FarmBeats 확인 메시지](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD 사용자 추가

> [!NOTE]
> Azure FarmBeats 사용자는 Azure AD 테넌트에 있어야 응용 프로그램 및 역할에 할당할 수 있습니다. Azure FarmBeats에 추가 하려는 사용자가 Azure AD 테넌트에 아직 없는 경우이 섹션의 지침을 따르세요. 사용자가 Azure AD 테넌트에 있는 경우 이러한 지침을 건너뛸 수 있습니다.

Azure AD에 사용자를 추가 하려면 다음을 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 오른쪽 위에서 계정을 선택 하 고 FarmBeats와 연결 된 Azure AD 테넌트로 전환 합니다.
3. **Azure Active Directory** > **사용자**를 선택 합니다.

    Azure AD 사용자의 목록이 표시 됩니다.

4. 디렉터리에 사용자를 추가 하려면 **새 사용자**를 선택 합니다. 외부 사용자를 추가 하려면 **새 게스트 사용자**를 선택 합니다.

    !["모든 사용자" 창](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 새 사용자의 이름을 선택 하 고 해당 사용자에 대 한 필수 필드를 완료 합니다.
6. **만들기**를 선택합니다.

Azure AD 사용자를 관리 하는 방법에 대 한 자세한 내용은 [AZURE ad에서 사용자 추가 또는 삭제](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure FarmBeats 인스턴스에 사용자를 추가 했습니다. 이제 [팜을 만들고 관리](manage-farms-in-azure-farmbeats.md#create-farms)하는 방법에 대해 알아봅니다.
