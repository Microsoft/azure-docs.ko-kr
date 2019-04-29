---
title: 온-프레미스 사이트로 장애 복구(failback)를 위한 Linux 마스터 대상 서버 설치 | Microsoft Docs
description: Azure Site Recovery를 사용한 VMware VM과 Azure 간 재해 복구 중에 온-프레미스 사이트로 장애 복구(failback)를 위한 Linux 마스터 대상 서버를 설치하는 방법을 알아봅니다.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 98718709038d7fd753e5eb3d45c130085c5accd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60600045"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>장애 복구(failback)를 위한 Linux 마스터 대상 서버 설치
Azure에 가상 머신을 장애 조치(failover)한 후 가상 머신을 다시 온-프레미스 사이트에 장애 복구할 수 있습니다. 장애 복구하려면 가상 머신을 Azure에서 온-프레미스 사이트로 다시 보호해야 합니다. 이 프로세스를 수행하려면 트래픽을 수신할 온-프레미스 마스터 대상 서버가 필요합니다. 

보호된 가상 머신이 Windows 가상 머신인 경우 Windows 마스터 대상이 필요합니다. Linux 가상 머신인 경우 Linux 마스터 대상이 필요합니다. 다음 단계를 읽고 Linux 마스터 대상을 만들고 설치하는 방법에 대해 알아보세요.

> [!IMPORTANT]
> 9.10.0 마스터 대상 서버 릴리스부터 최신 마스터 대상 서버는 Ubuntu 16.04 서버에만 설치할 수 있습니다. 새로운 설치는 CentOS6.6 서버에서 허용되지 않습니다. 그러나 9.10.0 버전을 사용하여 이전 마스터 대상 서버를 계속 업그레이드할 수 있습니다.
> LVM의 마스터 대상 서버는 지원되지 않습니다.

## <a name="overview"></a>개요
이 문서에서는 Linux 마스터 대상을 설치하는 방법의 지침을 제공합니다.

이 문서의 마지막 부분 또는 [Azure Recovery Services 포럼](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)에 의견이나 질문을 게시할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* 마스터 대상을 배포해야 하는 호스트를 선택하려면 기존 온-프레미스 가상 머신에 장애 복구를 수행할 것인지 아니면 새 가상 머신에 장애 복구를 수행할 것인지 결정합니다. 
    * 기존 가상 컴퓨터에서 수행하는 경우 마스터 대상의 호스트가 가상 컴퓨터의 데이터 저장소에 액세스할 수 있어야 합니다.
    * 온-프레미스 가상 머신이 없는 경우(대체 위치 복구의 경우) 마스터 대상과 동일한 호스트에 장애 복구(failback) 가상 머신이 생성됩니다. 아무 ESXi 호스트를 선택하여 마스터 대상을 설치할 수 있습니다.
* 마스터 대상은 프로세스 서버 및 구성 서버와 통신할 수 있는 네트워크에 있어야 합니다.
* 마스터 대상의 버전이 프로세스 서버 및 구성 서버의 버전과 같거나 그보다 이전 버전이어야 합니다. 예를 들어 구성 서버의 버전이 9.4인 경우 마스터 대상의 버전이 9.4 또는 9.3인 것은 괜찮지만 9.5는 안 됩니다.
* 마스터 대상은 VMware 가상 머신만 될 수 있고 물리적 서버는 안 됩니다.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>마스터 대상 서버 만들기에 대한 크기 조정 지침

다음 크기 조정 지침에 따라 마스터 대상을 만듭니다.
- **RAM**: 6GB 이상
- **OS 디스크 크기**: 100GB 이상(OS 설치에 필요)
- **보존 드라이브에 대한 추가 디스크 크기**: 1TB
- **CPU 코어**: 4 코어 이상

다음 지원되는 Ubuntu 커널을 사용할 수 있습니다.


|커널 시리즈  |최대 지원  |
|---------|---------|
|4.4.      |4.4.0-81-제네릭         |
|4.8      |4.8.0-56-제네릭         |
|4.10     |4.10.0-24-제네릭        |


## <a name="deploy-the-master-target-server"></a>마스터 대상 서버 배포

### <a name="install-ubuntu-16042-minimal"></a>Ubuntu 16.04.2 최소 설치

다음 단계를 통해 Ubuntu 16.04.2 64비트 운영 체제를 설치합니다.

1.   로 이동 합니다 [다운로드 링크](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), 가장 가까운 미러를 선택 하 고 Ubuntu 16.04.2 최소 64 비트 ISO를 다운로드 합니다.
DVD 드라이브에서 Ubuntu 16.04.2 최소 64비트 ISO를 유지하고 시스템을 시작합니다.

1.  기본 설정 언어로 **영어**를 선택하고 **Enter** 키를 선택합니다.
    
    ![언어 선택](./media/vmware-azure-install-linux-master-target/image1.png)
1. **Ubuntu 서버 설치**를 선택하고 **Enter** 키를 선택합니다.

    ![Ubuntu Server 설치 선택](./media/vmware-azure-install-linux-master-target/image2.png)

1.  기본 설정 언어로 **영어**를 선택하고 **Enter** 키를 선택합니다.

    ![원하는 언어로 영어 선택](./media/vmware-azure-install-linux-master-target/image3.png)

1. **표준 시간대** 옵션 목록에서 적절한 옵션을 선택하고 **Enter** 키를 선택합니다.

    ![올바른 표준 시간대 선택](./media/vmware-azure-install-linux-master-target/image4.png)

1. **아니요**(기본 옵션)를 선택하고 **Enter** 키를 선택합니다.

     ![키보드 구성](./media/vmware-azure-install-linux-master-target/image5.png)
1. 키보드의 원산지로 **영어(미국)** 를 선택하고 **Enter** 키를 선택합니다.

1. 키보드 레이아웃으로 **영어(미국)** 를 선택하고 **Enter** 키를 선택합니다.

1. **호스트 이름** 상자에 서버의 호스트 이름을 입력하고 **계속**을 선택합니다.

1. 사용자 계정을 만들려면 사용자 이름을 입력하고 **계속**을 선택합니다.

      ![사용자 계정 만들기](./media/vmware-azure-install-linux-master-target/image9.png)

1. 새 사용자 계정의 암호를 입력하고 **계속**을 선택합니다.

1.  새 사용자의 암호를 확인하고 **계속**을 선택합니다.

    ![암호 확인](./media/vmware-azure-install-linux-master-target/image11.png)

1.  홈 디렉터리를 암호화하기 위한 다음 선택 영역에서 **아니요**(기본 옵션)를 선택하고 **Enter** 키를 선택합니다.

1. 표시되는 표준 시간대가 올바르면 **예**(기본 옵션)를 선택하고 **Enter** 키를 선택합니다. 표준 시간대를 다시 구성하려면 **아니요**를 선택합니다.

1. 분할 방법 옵션에서 **단계별 - 전체 디스크 사용** 옵션을 선택하고 **Enter** 키를 선택합니다.

     ![분할 방법 옵션 선택](./media/vmware-azure-install-linux-master-target/image14.png)

1.  **분할할 디스크 선택** 옵션에서 적절한 디스크를 선택하고 **Enter** 키를 선택합니다.

    ![디스크 선택](./media/vmware-azure-install-linux-master-target/image15.png)

1.  **예**를 선택하여 디스크에 변경 내용을 쓰고 **Enter** 키를 선택합니다.

    ![기본 옵션 선택](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  구성 프록시 선택 영역에서 기본 옵션을 선택하고 **계속**을 선택한 다음, **Enter** 키를 선택합니다.
     
     ![업그레이드를 관리하는 방법 선택](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  시스템 업그레이드를 관리하기 위한 선택 영역에서 **자동 업데이트 없음** 옵션을 선택하고 **Enter** 키를 선택합니다.

     ![업그레이드를 관리하는 방법 선택](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Azure Site Recovery 마스터 대상 서버에 Ubuntu의 매우 구체적인 버전이 필요하기 때문에 가상 머신에서 커널 업그레이드를 비활성화해야 합니다. 활성화한 경우 일반 업그레이드로 인해 마스터 대상 서버에 오작동이 발생합니다. **자동 업데이트 없음** 옵션을 선택했는지 확인합니다.

1.  기본 옵션을 선택합니다. SSH 연결에 openSSH를 설정하려는 경우 **OpenSSH 서버** 옵션을 선택한 다음 **계속**을 선택합니다.

    ![소프트웨어 선택](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. GRUB 부팅 로더를 설치하기 위한 선택 영역에서 **Yes**를 선택하고 **Enter** 키를 누릅니다.
     
    ![GRUB 부팅 설치 관리자](./media/vmware-azure-install-linux-master-target/image20.png)


1. 부팅 로더를 설치할 적절한 디바이스(가급적 **/dev/sda**)를 선택하고 **Enter** 키를 선택합니다.
     
    ![해당 디바이스 선택](./media/vmware-azure-install-linux-master-target/image21.png)

1. **계속**을 선택한 다음, **Enter** 키를 선택하여 설치를 완료합니다.

    ![설치 완료](./media/vmware-azure-install-linux-master-target/image22.png)

1. 설치가 완료된 후에 새 사용자 자격 증명을 사용하여 VM에 로그인합니다. (자세한 정보는 **10단계**를 참조하세요.)

1. 루트 사용자 암호를 설정하려면 다음 스크린샷에 설명된 단계를 사용합니다. 그런 다음 루트 사용자로 로그인합니다.

    ![루트 사용자 암호 설정](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>컴퓨터를 마스터 대상 서버로 구성

Linux 가상 머신에 있는 각 SCSI 하드 디스크의 ID를 가져오려면 **disk.EnableUUID = TRUE** 매개 변수를 사용하도록 설정해야 합니다. 이 매개 변수를 사용하려면 다음 단계를 따르세요.

1. 가상 머신을 종료합니다.

2. 왼쪽 창에서 가상 머신의 항목을 마우스 오른쪽 단추로 클릭한 다음 **편집 설정**을 선택합니다.

3. **옵션** 탭을 선택합니다.

4. 왼쪽 창에서 **고급** > **일반**을 선택한 다음 화면의 오른쪽 아래에서 **구성 매개 변수** 단추를 선택합니다.

    ![구성 매개 변수 열기](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    **구성 매개 변수** 옵션은 컴퓨터가 실행 중인 동안에는 사용할 수 없습니다. 이 탭을 활성화하려면 가상 머신을 종료합니다.

5. **disk.EnableUUID**가 있는 행이 있는지 확인합니다.

   - 값이 있고 **False**로 설정되어 있으면 **True**로 변경합니다. (이 값은 대/소문자를 구분하지 않습니다.)

   - 값이 있고 **True**로 설정되어 있으면 **취소**를 선택합니다.

   - 값이 없으면 **행 추가**를 선택합니다.

   - 이름 열에서 **disk.EnableUUID**를 추가하고 값을 **TRUE**로 설정합니다.

     ![disk.EnableUUID가 있는지 확인](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>커널 업그레이드 비활성화

Azure Site Recovery 마스터 대상 서버에 Ubuntu의 매우 구체적인 버전이 필요합니다. 가상 머신에 커널 업그레이드를 비활성화합니다. 커널 업그레이드를 사용하도록 설정하면 마스터 대상 서버가 오작동할 수 있습니다.

#### <a name="download-and-install-additional-packages"></a>추가 패키지를 다운로드하여 설치

> [!NOTE]
> 추가 패키지를 다운로드하여 설치할 수 있도록 인터넷에 연결되어 있는지 확인합니다. 인터넷에 연결되지 않으면 해당 Deb 패키지를 수동으로 찾아서 설치해야 합니다.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>설치를 위한 설치 프로그램 가져오기

마스터 대상이 인터넷에 연결된 경우 다음 단계에 따라 설치 프로그램을 다운로드할 수 있습니다. 인터넷에 연결되지 않은 경우 프로세스 서버에서 설치 프로그램을 복사하여 설치할 수 있습니다.

#### <a name="download-the-master-target-installation-packages"></a>마스터 대상 설치 패키지 다운로드

[최신 Linux 마스터 대상 설치 비트를 다운로드합니다](https://aka.ms/latestlinuxmobsvc).

Linux를 사용하여 다운로드하려면 다음을 입력합니다.

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> 설치 관리자를 다운로드하고 홈 디렉터리에 압축을 풉니다. **/usr/Local**에 압축을 풀면 설치가 실패합니다.


#### <a name="access-the-installer-from-the-process-server"></a>프로세스 서버에서 설치 프로그램 액세스

1. 프로세스 서버에서 **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**로 이동합니다.

2. 프로세스 서버에서 필요한 설치 프로그램 파일을 복사하고 홈 디렉터리에 **latestlinuxmobsvc.tar.gz**로 저장합니다.


### <a name="apply-custom-configuration-changes"></a>사용자 지정 구성 변경 내용 적용

사용자 지정 구성 변경 내용을 적용하려면 다음 단계를 따릅니다.


1. 다음 명령을 실행하여 바이너리를 untar합니다.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![실행할 명령 스크린샷](./media/vmware-azure-install-linux-master-target/image16.png)

2. 다음 명령을 실행하여 권한을 부여합니다.

    `chmod 755 ./ApplyCustomChanges.sh`


3. 다음 명령을 사용하여 스크립트를 실행합니다.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> 서버에서 스크립트를 한 번만 실행합니다. 서버를 종료합니다. 다음 섹션에 설명된 대로 디스크를 추가한 후에 서버를 다시 시작합니다.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>보존 디스크를 Linux 마스터 대상 가상 머신에 추가

다음 단계에 따라 보존 디스크를 만듭니다.

1. Linux 마스터 대상 가상 머신에 새로운 1TB 디스크를 연결하고 컴퓨터를 시작합니다.

2. **multipath -ll** 명령을 사용하여 보존 디스크의 다중 경로 ID를 확인합니다. **multipath -ll**

    ![다중 경로 ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. 드라이브를 포맷한 다음, 새 드라이브에서 파일 시스템을 만듭니다. **mkfs.ext4 /dev/mapper/<Retention disk's multipath id>**
    
    ![파일 시스템](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. 파일 시스템을 만든 후 보존 디스크를 탑재합니다.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. 시스템을 시작할 때마다 보존 드라이브를 탑재하도록 **fstab** 항목을 만듭니다.
    
    `vi /etc/fstab`
    
    **Insert** 키를 눌러 파일을 편집하기 시작합니다. 새 줄을 만들고 다음 텍스트를 삽입합니다. 이전 명령에서 강조 표시된 다중 경로 ID에 따라 디스크 다중 경로 ID를 편집합니다.

    **/dev/mapper/<Retention disks multipath id> /mnt/retention ext4 rw 0 0**

    **Esc** 키를 선택하고 **:wq**(쓰기 및 종료)를 입력하여 편집기 창을 닫습니다.

### <a name="install-the-master-target"></a>마스터 대상 설치

> [!IMPORTANT]
> 마스터 대상 서버의 버전이 프로세스 서버 및 구성 서버의 버전과 같거나 그보다 이전 버전이어야 합니다. 이 조건이 충족되지 않으면 다시 보호에 성공하지만 복제는 실패합니다.


> [!NOTE]
> 마스터 대상 서버를 설치하기 전에 로컬 호스트 이름을 모든 네트워크 어댑터와 연결된 IP 주소에 매핑하는 항목이 가상 머신의 **/etc/hosts** 파일에 포함되어 있는지 확인합니다.

1. 구성 서버의 **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase**에서 암호를 복사합니다. 그리고 다음 명령을 실행하여 같은 로컬 디렉터리에서 **passphrase.txt**로 저장합니다.

    `echo <passphrase> >passphrase.txt`

    예제: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. 구성 서버의 IP 주소를 적어둡니다. 다음 명령을 실행하여 마스터 대상 서버를 설치하고 이 서버를 구성 서버에 등록합니다.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    예제: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

스크립트가 완료될 때까지 기다립니다. 마스터 대상이 성공적으로 등록되면 해당 마스터 대상이 포털의 **Site Recovery 인프라** 페이지에 나열됩니다.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>대화식 설치를 사용하여 마스터 대상 설치

1. 다음 명령을 실행하여 마스터 대상을 설치합니다. 에이전트 역할의 경우 **마스터 대상**을 선택합니다.

    ```
    ./install
    ```

2. 설치할 기본 위치를 선택하고 **Enter** 키를 선택하여 진행합니다.

    ![마스터 대상의 기본 설치 위치 선택](./media/vmware-azure-install-linux-master-target/image17.png)

설치가 완료된 후에 명령줄을 사용하여 구성 서버를 등록합니다.

1. 구성 서버의 IP 주소를 적어둡니다. 다음 단계에서 필요합니다.

2. 다음 명령을 실행하여 마스터 대상 서버를 설치하고 이 서버를 구성 서버에 등록합니다.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    예제: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     스크립트가 완료될 때까지 기다립니다. 마스터 대상이 성공적으로 등록되면 해당 마스터 대상이 포털의 **Site Recovery 인프라** 페이지에 나열됩니다.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>마스터 대상 서버에 VMware 도구/open-vm-tools 설치

VMware 도구 또는 open-vm-tools가 데이터 저장소를 찾을 수 있도록 마스터 대상에 설치해야 합니다. 도구가 설치되지 않으면 다시 보호 화면이 데이터 저장소에서 나열되지 않습니다. VMware 도구를 설치한 후에 다시 부팅해야 합니다.

### <a name="upgrade-the-master-target-server"></a>마스터 대상 서버 업그레이드

설치 관리자를 실행합니다. 마스터 대상에 에이전트가 설치되어 있는지를 자동으로 검색합니다. 업그레이드하려면 **Y**를 선택합니다.  설치가 완료된 후에 다음 명령을 사용하여 설치된 마스터 대상의 버전을 확인할 수 있습니다.

`cat /usr/local/.vx_version`


**버전** 필드에서 제공된 마스터 대상의 버전 번호를 볼 수 있습니다.

## <a name="common-issues"></a>일반적인 문제

* 마스터 대상 같은 관리 구성 요소에서 Storage vMotion을 설정하지 않아야 합니다. 마스터 대상이 다시 보호 후에 이동되면 VMDK(가상 머신 디스크)를 분리할 수 없습니다. 이 경우, 장애 복구에 실패합니다.

* 마스터 대상에는 가상 머신에 대한 스냅숏이 없어야 합니다. 스냅숏이 있으면 장애 복구에 실패합니다.

* 일부 사용자 지정 NIC 구성 때문에 시작하는 동안 네트워크 인터페이스를 사용할 수 없으며 마스터 대상 에이전트를 초기화할 수 없습니다. 다음 속성이 올바르게 설정되어 있는지 확인합니다. 이더넷 카드 파일의 /etc/sysconfig/network-scripts/ifcfg-eth*에서 다음 속성을 확인합니다.
    * BOOTPROTO=dhcp
    * ONBOOT=yes


## <a name="next-steps"></a>다음 단계
마스터 대상의 설치 및 등록이 완료되면 구성 서버 개요 아래에 있는 **Site Recovery 인프라**의 **마스터 대상** 섹션에 마스터 대상이 표시된 것을 확인할 수 있습니다.

이제 [다시 보호](vmware-azure-reprotect.md)와 장애 복구를 계속 진행할 수 있습니다.

