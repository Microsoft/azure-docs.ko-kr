---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 5ba55e339db4c33d1b0d759e4682481e20318938
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122845"
---
### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Linux 서버에서 푸시 설치 준비

1. Linux 컴퓨터와 프로세스 서버 간에 네트워크가 연결되어 있는지 확인합니다.
1. 프로세스 서버가 컴퓨터에 액세스하는 데 사용할 수 있는 계정을 작성합니다. 계정은 원본 Linux 서버의 **루트** 사용자여야 합니다. 푸시 설치 및 업데이트의 경우에만 이 계정을 사용합니다.
1. 원본 Linux 서버의 /etc/hosts 파일에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 있는지 확인합니다.
1. 복제하려는 컴퓨터에 최신 openssh, openssh-server 및 openssl 패키지를 설치합니다.
1. SSH(보안 셸)가 22 포트에서 사용되고 실행 중인지 확인합니다.
1. sshd_config 파일에서 SFTP 하위 시스템 및 암호 인증을 사용하도록 설정합니다. 다음 단계를 수행하세요.

    a. **루트**로 로그인합니다.

    b. **/etc/ssh/sshd_config** 파일에서 **PasswordAuthentication**으로 시작하는 줄을 찾습니다.

    다. 줄의 주석 처리를 제거하고 값을 **예**로 변경합니다.

    d. **Subsystem**으로 시작하는 줄을 찾아서 주석 처리를 제거합니다.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. **sshd** 서비스를 다시 시작합니다.

1. CSPSConfigtool에서 만든 계정을 추가합니다. 다음 단계를 수행하세요.

    a. 구성 서버에 로그인합니다.

    b. **cspsconfigtool.exe**를 엽니다. 바탕 화면에서 바로 가기로 사용할 수 있으며, %ProgramData%\home\svsystems\bin 폴더에 있습니다.

    다. **계정 관리** 탭에서 **계정 추가**를 선택합니다.

    d. 만든 계정을 추가합니다.

    d. 컴퓨터에서 복제를 사용할 때 사용할 자격 증명을 입력합니다.
