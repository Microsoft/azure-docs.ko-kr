---
title: Azure Functions 런타임 설치 | Microsoft Docs
description: Azure Functions 런타임 미리 보기 2를 설치하는 방법
services: functions
author: apwestgarth
manager: stefsch
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/28/2017
ms.author: anwestg
ms.openlocfilehash: aae6bc41f3c2fc2c5f8cf63d07f6b4d79bb3564a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61023122"
---
# <a name="install-the-azure-functions-runtime-preview-2"></a>Azure Functions 런타임 미리 보기 2 설치

[!INCLUDE [intro](../../includes/functions-runtime-preview-note.md)]

Azure Functions 런타임 미리 보기 2를 설치하려는 경우 다음 단계를 수행합니다.

1. 컴퓨터가 최소 요구 사항을 충족하는지 확인
1. [Azure Functions 런타임 미리 보기 설치 관리자](https://aka.ms/azafrv2) 다운로드
1. Azure Functions 런타임 미리 보기 1 제거
1. Azure Functions 런타임 미리 보기 2 설치
1. Azure Functions 런타임 미리 보기 2의 구성 완료
1. Azure Functions 런타임 미리 보기에서 첫 번째 함수 만들기

## <a name="prerequisites"></a>필수 조건

Azure Functions 런타임 미리 보기를 설치하기 전에 다음 리소스를 사용할 수 있어야 합니다.

1. Microsoft Windows Server 2016 또는 Microsoft Windows 10 작성자 업데이트(Professional 또는 Enterprise Edition)를 실행하는 컴퓨터
1. 네트워크 내에서 실행되는 SQL Server 인스턴스.  최소 버전 요구 사항은 SQL Server Express입니다.

## <a name="uninstall-previous-version"></a>이전 버전 제거

Azure Functions 런타임 미리 보기를 이전에 설치한 경우 최신 릴리스를 설치하기 전에 제거해야 합니다.  Windows의 프로그램 추가/제거에서 프로그램을 제거하여 Azure Functions 런타임 미리 보기를 제거합니다.

## <a name="install-the-azure-functions-runtime-preview"></a>Azure Functions 런타임 미리 보기 설치

Azure Functions 런타임 미리 보기 설치 관리자는 Azure Functions 런타임 미리 보기 관리 및 작업자 역할의 설치 과정을 안내합니다.  동일한 컴퓨터에 관리 및 작업자 역할을 설치할 수 있습니다.  그러나 함수 앱을 더 추가하는 경우 여러 작업자에 함수를 확장하기 위해 추가 컴퓨터에 더 많은 작업자 역할을 배포해야 합니다.

## <a name="install-the-management-and-worker-role-on-the-same-machine"></a>동일한 컴퓨터에 관리 및 작업자 역할 설치

1. Azure Functions 런타임 미리 보기 설치 관리자를 실행합니다.

    ![Azure Functions 런타임 미리 보기 설치 관리자][1]

1. **다음**을 클릭합니다.
1. **EULA**의 약관을 읽은 후에 **확인란을 선택**하여 조건에 동의하고 **다음 클릭**하여 계속 진행합니다.
1. **함수 관리 역할** 및/또는 **함수 작업자 역할** 중에서 이 컴퓨터에 설치하려는 역할을 선택하고 **다음**을 클릭합니다.

    ![Azure Functions 런타임 미리 보기 설치 관리자 - 역할 선택][3]

    > [!NOTE]
    > 많은 다른 컴퓨터에 **Functions Worker 역할**을 설치할 수 있습니다. 이렇게 하려면 다음 지침에 따르고 설치 관리자에서 **Functions Worker 역할**을 선택합니다.

1. **다음**을 클릭하면 **Azure Functions Runtime Setup Wizard**가 컴퓨터에서 설치 프로세스를 시작합니다.
1. 완료되면 설정 마법사가 **Azure Functions Runtime** 구성 도구를 시작합니다.

    ![Azure Functions 런타임 미리 보기 설치 관리자 완료][6]

    > [!NOTE]
    > **Windows 10**을 설치하려고 하며 **컨테이너** 기능을 이전에 사용하도록 설정하지 않은 경우 **Azure Functions Runtime Setup**에서 설치를 완료하기 위해 컴퓨터를 다시 부팅하라는 메시지를 표시합니다.

## <a name="configure-the-azure-functions-runtime"></a>Azure Functions 런타임 구성

Azure Functions 런타임 설치를 완료하려면 구성을 완료해야 합니다.

1. **Azure Functions Runtime** 구성 도구는 컴퓨터에 설치된 역할을 보여 줍니다.

    ![Azure Functions 런타임 미리 보기 구성 도구][7]

1. **데이터베이스** 탭을 클릭하고 [데이터베이스 마스터 키](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-and-database-encryption-keys-database-engine) 지정을 비롯한 SQL Server 인스턴스에 대한 연결 세부 정보를 입력한 후 **적용**을 클릭합니다.  Azure Functions 런타임이 런타임을 지원할 데이터베이스를 만들려면 SQL Server 인스턴스에 연결되어야 합니다.

    ![Azure Functions 런타임 미리 보기 데이터베이스 구성][8]

1. **자격 증명** 탭을 클릭합니다.  여기에서 모든 함수 앱을 호스트하기 위해 파일 공유에서 사용할 2개의 새 자격 증명을 만들어야 합니다.  **파일 공유 소유자** 및 **파일 공유 사용자** 에 대해 **사용자 이름** 및 **암호** 조합을 지정하고 **적용**을 클릭합니다.

    ![Azure Functions 런타임 미리 보기 자격 증명][9]

1. **파일 공유** 탭을 클릭합니다.  여기에서 파일 공유 위치의 세부 정보를 지정해야 합니다.  파일 공유가 자동으로 생성될 수도 있고 기존 파일 공유를 사용하고 **적용**을 클릭할 수도 있습니다.  새 파일 공유 위치를 선택하는 경우 Azure Functions 런타임에서 사용할 디렉터리를 지정해야 합니다.

    ![Azure Functions 런타임 미리 보기 파일 공유][10]

1. **IIS** 탭을 클릭합니다.  이 탭에는 Azure Functions 런타임 구성 도구가 만들 IIS의 웹 사이트 세부 정보가 표시됩니다.  Azure Functions 런타임 미리 보기 포털에 대한 사용자 지정 DNS 이름을 여기에 지정할 수 있습니다.  **적용**을 클릭하여 완료합니다.

    ![Azure Functions 런타임 미리 보기 IIS][11]

1. **서비스** 탭을 클릭합니다.  이 탭에는Azure Functions Runtime 구성 도구의 서비스 상태가 표시됩니다.  초기 구성 후에 **Azure Functions Host Activation Service**가 실행되지 않을 경우 **서비스 시작**을 클릭합니다.

    ![Azure Functions 런타임 미리 보기 구성 완료][12]

1. `https://<machinename>.<domain>/`을 통해 **Azure Functions 런타임 포털**로 이동합니다.

    ![Azure Functions 런타임 미리 보기 포털][13]

## <a name="create-your-first-function-in-azure-functions-runtime-preview"></a>Azure Functions 런타임 미리 보기에서 첫 번째 함수 만들기

Azure Functions 런타임 미리 보기에서 첫 번째 함수를 만들려면

1. 로 이동 합니다 **Azure Functions 런타임 포털** 으로 `https://<machinename>.<domain>` 예를 들어 `https://mycomputer.mydomain.com`합니다.

1. **로그인**하라는 메시지가 표시됩니다. 도메인에 배포된 경우 도메인 계정 사용자 이름 및 암호를 사용하고, 그렇지 않은 경우 로컬 계정 사용자 이름 및 암호를 사용하여 포털에 로그인합니다.

    ![Azure Functions 런타임 미리 보기 포털 로그인][14]

1. 함수 앱을 만들려면 구독을 만들어야 합니다.  포털의 왼쪽 위 모서리를 클릭 합니다 **+** 구독 옆에 있는 옵션입니다.

    ![Azure Functions 런타임 미리 보기 포털 구독][15]

1. **DefaultPlan**을 선택하고 구독 이름을 입력한 후 **만들기**를 클릭합니다.

    ![Azure Functions 런타임 미리 보기 포털 구독 계획 및 이름][16]

1. 모든 함수 앱이 포털의 왼쪽 창에 나열됩니다.  새 Function App을 만들려면 제목 **Function App**을 선택하고 **+** 옵션을 클릭합니다.

1. 함수 앱의 이름을 입력하고 올바른 구독을 선택한 후 프로그래밍할 Azure Functions 런타임 버전을 선택하고 **만들기**를 클릭합니다.

    ![Azure Functions 런타임 미리 보기 포털 새 함수 앱][17]

1. 새 함수 앱이 포털의 왼쪽 창에 나열됩니다.  함수를 선택한 다음 포털의 가운데 창 위쪽에서 **새 함수**를 클릭합니다.

    ![Azure Functions 런타임 미리 보기 템플릿][18]

1. 함수의 오른쪽 플라이 아웃 이름에서 Timer Trigger 함수를 선택하고 일정을 `*/5 * * * * *`(이 cron 식은 타이머함 수가 5초마다 실행되도록 함)로 변경하고 **만들기**를 클릭합니다.

    ![Azure Functions 런타임 미리 보기 새 타이머 함수 구성][19]

1. 이제 함수가 만들어졌습니다.  포털 아래쪽에 있는 **로그** 창을 확장하여 함수 앱의 실행 로그를 볼 수 있습니다.

    ![Azure Functions 런타임 미리 보기 함수 실행][20]

<!--Image references-->
[1]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer1.png
[2]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer2-EULA.png
[3]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer3-ChooseRoles.png
[4]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer4-Install.png
[5]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer5-Progress.png
[6]: ./media/functions-runtime-install/AzureFunctionsRuntime_Installer6-InstallComplete.png
[7]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration1.png
[8]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration2_SQL.png
[9]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration3_Credentials.png
[10]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration4_Fileshare.png
[11]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration5_IIS.png
[12]: ./media/functions-runtime-install/AzureFunctionsRuntime_Configuration6_Services.png
[13]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal.png
[14]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Login.png
[15]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions.png
[16]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_Subscriptions1.png
[17]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewFunctionApp.png
[18]: ./media/functions-runtime-install/AzureFunctionsRuntime_v1FunctionsTemplates.png
[19]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_NewTimerFunction.png
[20]: ./media/functions-runtime-install/AzureFunctionsRuntime_Portal_RunningV2Function.png
