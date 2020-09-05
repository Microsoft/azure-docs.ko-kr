---
title: Linux 컴퓨터에 Log Analytics 에이전트 설치
description: 이 문서에서는 다른 클라우드 또는 온-프레미스에서 호스트 되는 Linux 컴퓨터를 Linux 용 Log Analytics 에이전트와 Azure Monitor 연결 하는 방법을 설명 합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 997064ad030d22531277f1c412add6916eb7733f
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230469"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Linux 컴퓨터에 Log Analytics 에이전트 설치
이 문서에서는 다음 방법을 사용 하 여 Linux 컴퓨터에 Log Analytics 에이전트를 설치 하는 방법에 대해 자세히 설명 합니다.

* GitHub에 호스트 된 [래퍼 스크립트를 사용 하 여 Linux 용 에이전트를 설치](#install-the-agent-using-wrapper-script) 합니다. 컴퓨터가 인터넷에 직접 연결 되어 있거나 프록시 서버를 통해 연결 되어 있는 경우 에이전트를 설치 하 고 업그레이드 하는 데 권장 되는 방법입니다.
* 에이전트를 [수동으로 다운로드 하 여 설치](#install-the-agent-manually) 합니다. 이는 Linux 컴퓨터가 인터넷에 액세스할 수 없고 Azure Monitor 또는 Azure Automation [Log Analytics 게이트웨이](gateway.md)를 통해 통신 하는 경우에 필요 합니다. 

>[!IMPORTANT]
> 이 문서에서 설명 하는 설치 방법은 일반적으로 온-프레미스 또는 다른 클라우드의 가상 컴퓨터에 사용 됩니다. Azure virtual machines에 사용할 수 있는 보다 효율적인 옵션은 [설치 옵션](log-analytics-agent.md#installation-options) 을 참조 하세요.



## <a name="supported-operating-systems"></a>지원되는 운영 체제

Log Analytics 에이전트에서 지 원하는 Linux 배포 목록은 [Azure Monitor 에이전트 개요](agents-overview.md#supported-operating-systems) 를 참조 하세요.

>[!NOTE]
>OpenSSL 1.1.0은 x86_x64 플랫폼(64비트)에서만 지원되고, OpenSSL 1.x 미만은 어떤 플랫폼에서도 지원되지 않습니다.
>
2018년 8월 이후에 출시된 버전부터 지원 모델이 다음과 같이 변경됩니다.  

* 서버 버전만 지원되고 클라이언트 버전은 지원되지 않습니다.  
* [Azure Linux 보증 배포](../../virtual-machines/linux/endorsed-distros.md)를 집중적으로 지원합니다. 새 배포/버전이 Azure Linux 보증 배포가 되고 Log Analytics Linux 에이전트에 대해 지원되는 데 약간의 지연이 있을 수 있습니다.
* 나열된 각 주 버전의 모든 부 버전이 지원됩니다.
* 제조업체의 지원 종료 날짜가 지난 버전은 지원되지 않습니다.  
* 새 AMI 버전은 지원되지 않습니다.  
* 기본적으로 SSL 1.x를 실행하는 버전만 지원됩니다.

>[!NOTE]
>현재 지원되지 않고 Microsoft 지원 모델에 맞지 않는 배포판 또는 버전을 사용하는 경우 이 리포지토리를 포크하고, Microsoft 기술 지원에서 포크된 에이전트 버전에 대한 지원을 제공하지 않는다는 것을 확인하는 것이 좋습니다.

### <a name="python-2-requirement"></a>Python 2 요구 사항

 Log Analytics 에이전트에는 Python 2가 필요 합니다. 가상 컴퓨터가 기본적으로 Python 2를 포함 하지 않는 배포판를 사용 하는 경우 설치 해야 합니다. 다음 샘플 명령은 다른 배포판에 Python 2를 설치 합니다.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE: `zypper install -y python2`

Python2 실행 파일은 *python*으로 별칭을 지정 해야 합니다. 다음은이 별칭을 설정 하는 데 사용할 수 있는 한 가지 방법입니다.

1. 다음 명령을 실행 하 여 기존 별칭을 제거 합니다.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 다음 명령을 실행 하 여 별칭을 만듭니다.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>지원 되는 Linux 강화
OMS 에이전트는 Linux에 대해 제한 된 사용자 지정 지원을 제공 합니다. 

현재 지원 되는 기능은 다음과 같습니다. 
- 서명에

다음은 계획 되었지만 아직 지원 되지 않습니다.
- 들
- SELINUX

기타 강화 및 사용자 지정 방법은 지원 되지 않으며 OMS 에이전트에 대해 계획 되지 않았습니다.  

## <a name="agent-prerequisites"></a>에이전트 필수 구성 요소

다음 표에서는 에이전트가 설치 될 [지원 되는 Linux](#supported-operating-systems) 배포판에 필요한 패키지를 강조 표시 합니다.

|필수 패키지 |Description |최소 버전 |
|-----------------|------------|----------------|
|Glibc |    GNU C 라이브러리 | 2.5-12 
|Openssl    | OpenSSL 라이브러리 | 1.0.x 또는 1.1.x |
|Curl | cURL 웹 클라이언트 | 7.15.5 |
|Python | | 2.6 이상 또는 3.3 이상
|Python-ctypes | | 
|PAM | 플러그형 인증 모듈 | | 

>[!NOTE]
>Syslog 메시지를 수집하려면 rsyslog 또는 syslog-ng가 필요합니다. Red Hat Enterprise Linux 버전 5, CentOS 및 Oracle Linux 버전(sysklog)에서는 syslog 이벤트 수집을 위한 기본 syslog 디먼이 지원되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

## <a name="network-requirements"></a>네트워크 요구 사항
Linux 에이전트에 대 한 네트워크 요구 사항은 [Log Analytics 에이전트 개요](log-analytics-agent.md#network-requirements) 를 참조 하세요.

## <a name="agent-install-package"></a>에이전트 설치 패키지

Linux 용 Log Analytics 에이전트는 여러 패키지로 구성 됩니다. 릴리스 파일에는 매개 변수와 함께 셸 번들을 실행 하 여 사용할 수 있는 다음과 같은 패키지가 포함 되어 있습니다 `--extract` .

**패키지** | **Version** | **설명**
----------- | ----------- | --------------
omsagent | 1.12.15 | Linux 용 Log Analytics 에이전트
omsconfig | 1.1.1 | Log Analytics 에이전트에 대 한 구성 에이전트
omi | 1.6.3 | OMI (Open Management Infrastructure) – 경량 CIM 서버입니다. *OMI에는 서비스의 작동에 필요한 cron 작업을 실행 하는 데 필요한 루트 액세스가 필요 합니다.*
scx | 1.6.3 | 운영 체제 성능 메트릭용 OMI CIM 공급자
apache-cimprov | 1.0.1 | OMI용 Apache HTTP 서버 성능 모니터링 공급자. Apache HTTP 서버가 감지되는 경우에만 설치됨.
mysql-cimprov | 1.0.1 | OMI용 MySQL 서버 성능 모니터링 공급자. MySQL/MariaDB 서버가 감지되는 경우에만 설치됨.
docker-cimprov | 1.0.0 | OMI용 Docker 공급자. Docker가 감지되는 경우에만 설치됨.

### <a name="agent-installation-details"></a>에이전트 설치 세부 정보

Linux 패키지용 Log Analytics 에이전트를 설치한 후 다음과 같은 추가 시스템 차원 구성 변경 내용이 적용 됩니다. 이러한 아티팩트는 omsagent 패키지가 제거되면 제거됩니다.

* `omsagent` 라는 이름의 권한 없는 사용자가 생성됩니다. 데몬이이 자격 증명으로 실행 됩니다. 
* Sudoers *include* 파일은에 생성 됩니다 `/etc/sudoers.d/omsagent` . 이 `omsagent` 는 syslog 및 omsagent 디먼를 다시 시작할 수 있는 권한을 부여 합니다. Sudo *include* 지시문이 설치 된 버전의 sudo에서 지원 되지 않는 경우 이러한 항목이에 기록 됩니다 `/etc/sudoers` .
* syslog 구성은 이벤트 하위 집합을 에이전트에 전달하도록 수정됩니다. 자세한 내용은 [Syslog 데이터 수집 구성](data-sources-syslog.md)을 참조 하세요.

모니터링 되는 Linux 컴퓨터에서 에이전트는로 나열 됩니다 `omsagent` . `omsconfig` 는 5 분 마다 새 포털 측 구성을 검색 하는 Linux 용 Log Analytics 에이전트 구성 에이전트입니다. 새 구성과 업데이트 된 구성은에 있는 에이전트 구성 파일에 적용 됩니다 `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>래퍼 스크립트를 사용 하 여 에이전트 설치

다음 단계는 Azure 및 Azure Government 클라우드에서 Log Analytics에 대 한 에이전트의 설치를 구성 하 고,이를 통해 직접 또는 프록시 서버를 통해 통신 하 여 GitHub에 호스트 된 에이전트를 다운로드 하 고 에이전트를 설치 합니다.  

Linux 컴퓨터가 프록시 서버를 통해 Log Analytics를 통해 통신 해야 하는 경우를 포함 하 여 명령줄에서이 구성을 지정할 수 있습니다 `-p [protocol://][user:password@]proxyhost[:port]` . *프로토콜* 속성은 `http` 또는을 허용 `https` 하 고, *proxyhost* 속성은 프록시 서버의 정규화 된 도메인 이름 또는 IP 주소를 허용 합니다. 

예: `https://proxy01.contoso.com:30443`

두 경우 모두 인증이 필요한 경우 사용자 이름 및 암호를 지정 해야 합니다. 예: `https://user01:password@proxy01.contoso.com:30443`

1. Log Analytics 작업 영역에 연결 하도록 Linux 컴퓨터를 구성 하려면 작업 영역 ID 및 기본 키를 제공 하는 다음 명령을 실행 합니다. 다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    다음 명령에는 프록시 서버에서 인증이 필요한 경우 `-p` 프록시 매개 변수와 예제 구문이 포함되어 있습니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Azure Government 클라우드에서 Linux 컴퓨터를 Log Analytics 작업 영역에 연결하도록 구성하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공하는 다음 명령을 실행합니다. 다음 명령은 에이전트를 다운로드하고, 해당 체크섬의 유효성을 검사한 다음, 설치합니다. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    다음 명령에는 프록시 서버에서 인증이 필요한 경우 `-p` 프록시 매개 변수와 예제 구문이 포함되어 있습니다.

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 다음 명령을 실행하여 에이전트를 다시 시작합니다. 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>수동으로 에이전트 설치

Linux 용 Log Analytics 에이전트는 자동 압축 풀기 및 설치 가능한 셸 스크립트 번들로 제공 됩니다. 이 번들은 각 에이전트 구성 요소에 대한 Debian 및 RPM 패키지를 포함하며, 직접 설치되거나 압축이 풀린 후에 개별 패키지를 검색하는 데 사용될 수 있습니다. 하나의 번들은 x64 및 x86 아키텍처용으로 제공 됩니다. 

> [!NOTE]
> Azure Vm의 경우 Linux 용 [azure LOG ANALYTICS VM 확장](../../virtual-machines/extensions/oms-linux.md) 을 사용 하 여 에이전트를 설치 하는 것이 좋습니다. 

1. Scp/sftp를 사용 하 여 적절 한 번들 (x64 또는 x86)을 [다운로드](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) 하 고 Linux VM 또는 물리적 컴퓨터에 전송 합니다.

2. 인수를 사용 하 여 번들을 설치 합니다 `--install` . 설치 하는 동안 Log Analytics 작업 영역에 등록 하려면 `-w <WorkspaceID>` 앞에서 `-s <workspaceKey>` 복사한 및 매개 변수를 제공 합니다.

    >[!NOTE]
    >`--upgrade`Linux 용 System Center Operations Manager 에이전트가 이미 설치 되어 있는 경우 처럼 omi, scx, omsconfig 또는 이전 버전과 같은 종속 패키지가 설치 된 경우에는 인수를 사용 해야 합니다. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Log Analytics 게이트웨이를 통해 Log Analytics 작업 영역에 설치 하 고 연결 하도록 Linux 에이전트를 구성 하려면 프록시, 작업 영역 ID 및 작업 영역 키 매개 변수를 제공 하는 다음 명령을 실행 합니다. 이 구성은를 포함 하 여 명령줄에서 지정할 수 있습니다 `-p [protocol://][user:password@]proxyhost[:port]` . *Proxyhost* 속성은 Log Analytics 게이트웨이 서버의 정규화 된 도메인 이름 또는 IP 주소를 허용 합니다.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    인증이 필요한 경우 사용자 이름 및 암호를 지정 해야 합니다. 예: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Azure Government 클라우드의 Log Analytics 작업 영역에 연결 하도록 Linux 컴퓨터를 구성 하려면 앞에서 복사한 작업 영역 ID 및 기본 키를 제공 하는 다음 명령을 실행 합니다.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

에이전트 패키지를 설치 하 고 나중에 특정 Log Analytics 작업 영역에 보고 하도록 구성 하려면 다음 명령을 실행 합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

에이전트를 설치 하지 않고 번들에서 에이전트 패키지를 추출 하려면 다음 명령을 실행 합니다.

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>이전 릴리스에서 업그레이드

버전 1.0.0-47부터 이전 버전에서 업그레이드 하는 것은 각 릴리스에서 지원 됩니다. 매개 변수를 사용 하 여 설치를 수행 `--upgrade` 하 여 에이전트의 모든 구성 요소를 최신 버전으로 업그레이드 합니다.

## <a name="cache-information"></a>캐시 정보
Linux 용 Log Analytics 에이전트의 데이터가 Azure Monitor로 전송 되기 전에 *% STATE_DIR_WS/out_oms_common*. buffer *의 로컬 컴퓨터에 캐시 됩니다. 사용자 지정 로그 데이터가 *% STATE_DIR_WS %1 out_oms_blob*에 버퍼링 됩니다. *. 경로는 일부 [솔루션 및 데이터 형식](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)에 대해 다를 수 있습니다.

에이전트는 20 초 마다 업로드 하려고 합니다. 실패 하는 경우 성공할 때까지 계속 해 서 많은 시간 동안 대기 합니다. 두 번째 시도 전에 30 초 60, 다음, 120 초 전에 30 초 동안 대기 하 고 다시 연결할 때까지 다시 시도 하는 동안 최대 9 분이 소요 됩니다. 에이전트는 데이터의 지정 된 청크를 10 번만 다시 시도 하 고 다음으로 이동 합니다. 에이전트가 다시 업로드 될 때까지 계속 됩니다. 는 데이터를 삭제 하기 전에 8.5 시간까지 버퍼링 될 수 있음을 의미 합니다.

기본 캐시 크기는 10mb 이지만 [omsagent 파일](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)에서 수정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [Windows 및 Linux용 Log Analytics 에이전트 관리 및 유지 관리](agent-manage.md)를 검토하여 가상 머신에서 에이전트를 다시 구성, 업그레이드 또는 제거하는 방법을 알아봅니다.

- 에이전트를 설치하거나 관리하는 중에 문제가 발생하면 [Troubleshooting the Linux agent](agent-linux-troubleshoot.md)(Linux 에이전트 문제 해결)를 참조하세요.
