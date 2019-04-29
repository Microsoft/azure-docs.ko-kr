---
title: SSH 터널링을 사용하여 Azure HDInsight에 액세스
description: SSH 터널을 사용하여 Linux 기반 HDInsight 노드에 호스팅되는 웹 리소스를 안전하게 검색하는 방법에 알아봅니다.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
origin.date: 04/30/2018
ms.date: 02/04/2019
ms.author: hrasheed
ms.openlocfilehash: 0361539cefbacb8fc0473a1f863cf2ae4638b444
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766751"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>SSH 터널링을 사용하여 Apache Ambari Web UI, JobHistory, NameNode, Apache Oozie 및 기타 웹 UI에 액세스

HDInsight 클러스터는 인터넷을 통해 Apache Ambari Web UI에 대한 액세스를 제공하지만, 일부 기능을 사용하려면 SSH 터널이 필요합니다. 예를 들어 Apache Oozie 서비스에 대한 웹 UI는 SSH 터널 없이 인터넷을 통해 액세스할 수 없습니다.

## <a name="why-use-an-ssh-tunnel"></a>SSH 터널을 사용하는 이유

Ambari의 일부 메뉴만 SSH 터널을 통해 작동합니다. 이러한 메뉴는 작업자 노드 등의 다른 노드 유형에서 실행되는 웹 사이트 및 서비스에 의존합니다.

다음 웹 UI에는 SSH 터널이 필요합니다.

* JobHistory
* NameNode
* Thread Stacks
* Oozie web UI
* HBase Master 및 로그 UI

클러스터를 사용자 지정하는 스크립트 작업을 사용하는 경우 웹 서비스를 노출하는 서비스 또는 유틸리티를 설치하려면 SSH 터널이 필요합니다. 예를 들어 스크립트 작업을 사용하여 Hue를 설치하는 경우 SSH 터널을 사용하여 Hue 웹 UI에 액세스해야 합니다.

> [!IMPORTANT]
> 가상 네트워크를 통해 HDInsight에 대한 직접 액세스가 있는 경우 SSH 터널을 사용할 필요가 없습니다. 가상 네트워크를 통해 HDInsight에 직접 액세스하는 예는 [온-프레미스 네트워크에 HDInsight 연결](connect-on-premises-network.md) 문서를 참조하세요.

## <a name="what-is-an-ssh-tunnel"></a>SSH 터널이란

[SSH(Secure Shell) 터널링](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling)은 로컬 컴퓨터의 포트를 HDInsight의 헤드 노드에 연결합니다. 로컬 포트로 전송된 트래픽은 SSH 연결을 통해 헤드 노드로 라우팅됩니다. 해당 요청은 마치 헤드 노드에서 시작된 것처럼 확인됩니다. 그러면 응답은 워크스테이션에 대한 터널을 통해 다시 라우팅됩니다.

## <a name="prerequisites"></a>필수 조건

* SSH 클라이언트. 대부분의 운영 체제가 `ssh` 명령을 통해 SSH 클라이언트를 제공합니다. 자세한 내용은 [HDInsight와 함께 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

* SOCKS5 프록시를 사용하도록 구성할 수 있는 웹 브라우저입니다.

    > [!WARNING]
    > Windows 인터넷 설정에 기본 제공되는 SOCKS 프록시 지원은 SOCKS5를 지원하지 않으며, 이 문서의 단계에서 작동하지 않습니다. 다음 브라우저는 Windows 프록시 설정에 의존하고 현재 이 문서의 단계에서 작동하지 않습니다.
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome도 Windows 프록시 설정에 의존합니다. 그러나 SOCKS5를 지원하는 확장을 설치할 수 있습니다. [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp)를 권장합니다.

## <a name="usessh"></a>SSH 명령을 사용하여 터널 만들기

`ssh` 명령을 사용하여 SSH 터널을 만들려면 다음 명령을 사용합니다. **sshuser**를 HDInsight 클러스터의 SSH 사용자로 바꾸고, **clustername**을 HDInsight 클러스터의 이름으로 바꿉니다.

```bash
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

이 명령은 로컬 포트 9876에서 SSH를 통해 클러스터에 트래픽을 라우팅하는 연결을 만듭니다. 옵션은 다음과 같습니다.

* **D 9876** - 터널을 통해 트래픽을 라우팅하는 로컬 포트
* **C** - 웹 트래픽은 대부분 텍스트이므로 모든 데이터 압축
* **2** - SSH가 프로토콜 버전 2만 시도하도록 강요
* **q** - 자동 모드
* **T** - 포트 전달 후 허위 tty 할당 비활성화
* **n** - 포트 전달 후 STDIN 읽지 않음
* **N** - 포트 전달 후 원격 명령 실행 안 함
* **f** - 백그라운드에서 실행

명령이 완료되면 로컬 컴퓨터에서 9876 포트로 전송되는 트래픽이 클러스터 헤드 노드로 라우팅됩니다.

## <a name="useputty"></a>PuTTY를 사용하여 터널 만들기

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty)는 Windows용 그래픽 SSH 클라이언트입니다. PuTTY에 대해 잘 모르는 경우 [PuTTY 설명서](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)를 참조하세요. PuTTY를 사용하여 SSH 터널을 만들려면 다음 단계를 사용합니다.

### <a name="create-or-load-a-session"></a>세션 만들기 또는 로드

1. PuTTY를 열고 왼쪽 메뉴에서 **세션**이 선택되어 있는지 확인합니다. 세션을 이미 저장한 경우 **저장된 세션** 목록에서 세션 이름을 선택하고 **로드**를 클릭합니다.

1. 저장된 세션이 없는 경우 연결 정보를 입력합니다.
    * **호스트 이름(또는 IP 주소)** - HDInsight 클러스터의 SSH 주소입니다. 예: **mycluster-ssh.azurehdinsight.net**
    * **포트** - 22
    * **연결 형식** - SSH
1. 페이지 맨 아래에 있는 **저장**

    ![SSH 세션 만들기](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

2. 대화 상자의 왼쪽에 있는 **Category** 섹션에서 **Connection**, **SSH**를 차례로 확장한 다음 **Tunnels**를 선택합니다.

3. **Options controlling SSH port forwarding** 양식에 다음 정보를 제공합니다.
   
   * **Source port** - 전달하려는 클라이언트의 포트입니다. 예를 들면 **9876**과 같습니다.

   * **Destination** - HDInsight 클러스터의 SSH 주소입니다. 예를 들면 **mycluster-ssh.azurehdinsight.net**과 같습니다.

   * **Dynamic** - 동적 SOCKS 프록시 라우팅을 활성화합니다.
     
     ![터널링 옵션 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. **Add**를 클릭하여 설정을 추가한 다음 **Open**을 클릭하여 SSH 연결을 엽니다.

5. 메시지가 표시되면 서버에 로그인합니다.

## <a name="use-the-tunnel-from-your-browser"></a>브라우저에서 터널 사용

> [!IMPORTANT]
> 이 섹션의 단계에서는 Mozilla FireFox 브라우저를 사용합니다. 이 브라우저는 모든 플랫폼에서 동일한 프록시 설정을 제공하기 때문입니다. Google Chrome 등의 다른 최신 브라우저에는 터널에서 작동하기 위해 FoxyProxy 등의 확장이 필요할 수 있습니다.

1. **localhost**와 **SOCKS v5** 프록시로 터널을 만들 때 사용한 포트를 사용하도록 브라우저를 구성합니다. Firefox 설정은 다음과 같습니다. 9876이 아닌 다른 포트를 사용한 경우 포트를 사용한 포트로 변경합니다.
   
    ![Firefox 설정 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > **Remote DNS**를 선택하면 HDInsight 클러스터를 통해 DNS(Domain Name System) 요청이 확인됩니다. 이 설정은 클러스터의 헤드 노드를 사용하여 DNS를 확인합니다.

2. [https://www.whatismyip.com/](https://www.whatismyip.com/)과 같은 사이트를 방문하여 터널이 작동하는지 확인합니다. 반환된 IP는 Microsoft Azure 데이터 센터에서 사용하는 하나여야 합니다.

## <a name="verify-with-ambari-web-ui"></a>Ambari 웹 UI 통해 확인

클러스터를 설정한 후에 Ambari 웹에서 서비스 웹 UI에 액세스할 수 있는지 확인하려면 다음 단계를 따릅니다.

1. 브라우저에서 http\://headnodehost:8080으로 이동합니다. `headnodehost` 주소는 터널을 통해 클러스터로 전송되며 Ambari가 실행 중인 헤드 노드를 확인합니다. 메시지가 표시되면 클러스터의 관리자 사용자 이름(관리자) 및 암호를 입력합니다. Ambari 웹 UI에서 두 번째로 메시지가 표시될 수 있습니다. 이러한 경우 정보를 다시 입력합니다.

   > [!NOTE]  
   > http\://headnodehost:8080 주소를 사용하여 클러스터에 연결할 경우 터널을 통해 연결됩니다. 통신 보안은 HTTPS가 아닌 SSH 터널을 사용하여 유지됩니다. HTTPS를 사용하여 인터넷을 통해 연결하려면 https\://clustername.azurehdinsight.net을 사용합니다. 여기서 **clustername**은 클러스터의 이름입니다.

2. Ambari 웹 UI에서 페이지의 왼쪽 목록에서 HDFS를 선택합니다.

    ![HDFS가 선택된 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. HDFS 서비스 정보가 표시되면 **빠른 링크**를 선택합니다. 클러스터 헤드 노드 목록이 표시됩니다. 헤드 노드 중 하나를 선택한 다음 **NameNode UI**를 선택합니다.

    ![확장된 빠른 링크 메뉴의 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > __빠른 링크__를 선택하면 대기 표시기가 표시될 수 있습니다. 인터넷 연결 속도가 느린 경우 이러한 상태가 발생할 수 있습니다. 데이터를 서버에서 받을 때까지 기다렸다가 목록을 다시 시도하세요.
   >
   > **빠른 링크** 메뉴의 일부 항목이 화면 오른쪽에서 잘릴 수 있습니다. 그럴 경우 마우스를 사용하여 메뉴를 확장한 다음 오른쪽 화살표 키를 사용하여 메뉴의 나머지 부분을 볼 수 있도록 오른쪽으로 화면을 스크롤합니다.

4. 다음 이미지와 유사한 페이지가 표시됩니다.

    ![NameNode UI의 이미지](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]  
   > 이 페이지의 URL에 유의하세요. **http\://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**와 유사해야 합니다. 이 URI는 노드의 내부 FQDN(정규화된 도메인 이름)을 사용하며 SSH 터널을 통해서만 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

SSH 터널을 만들고 사용하는 방법을 배웠으므로 다음 문서에서 Ambari를 사용하는 다른 방법을 참조하세요.

* [Apache Ambari를 사용하여 HDInsight 클러스터 관리](hdinsight-hadoop-manage-ambari.md)

HDInsight에서의 SSH 사용에 대한 자세한 내용은 [HDInsight에서 SSH 사용](hdinsight-hadoop-linux-use-ssh-unix.md)을 참조하세요.

