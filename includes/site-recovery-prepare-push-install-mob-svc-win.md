---
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122796"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Windows 컴퓨터에서 푸시 설치 준비

1. Windows 컴퓨터와 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다.
1. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 작성합니다. 계정에는 관리자 권한(로컬 또는 도메인)이 있어야 합니다. 강제 설치 및 에이전트 업데이트의 경우에만 이 계정을 사용합니다.

   > [!NOTE]
   > 도메인 계정을 사용하지 않는 경우 로컬 컴퓨터에서 원격 사용자 액세스 제어를 사용하지 않도록 설정합니다. HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 레지스트리 키 아래에 있는 원격 사용자 액세스 제어를 사용 하지 않도록 설정 하려면 새 DWORD를 추가 합니다. **LocalAccountTokenFilterPolicy**를 추가합니다. 값을 **1**로 설정합니다. 명령 프롬프트에서 이 태스크를 수행하려면 다음 명령을 실행합니다.  
   > `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. 보호하려는 컴퓨터의 Windows 방화벽에서 **방화벽을 통해 앱 또는 기능 허용**을 선택합니다. **파일 및 프린터 공유**와 **WMI(Windows Management Instrumentation)** 를 사용하도록 설정합니다. 도메인에 속하는 컴퓨터의 경우 GPO(그룹 정책 개체)를 사용하여 방화벽 설정을 구성할 수 있습니다.

   ![방화벽 설정](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. CSPSConfigtool에서 만든 계정을 추가합니다. 다음 단계를 수행하세요.

    a. 구성 서버에 로그인합니다.

    b. **cspsconfigtool.exe**를 엽니다. 바탕 화면에서 바로 가기로 사용할 수 있으며, %ProgramData%\home\svsystems\bin 폴더에 있습니다.

    다. **계정 관리** 탭에서 **계정 추가**를 선택합니다.

    d. 만든 계정을 추가합니다.

    e. 컴퓨터에서 복제를 사용할 때 사용할 자격 증명을 입력합니다.
<!--Update_Description: wording update-->
<!--ms.date: 09/17/2018-->