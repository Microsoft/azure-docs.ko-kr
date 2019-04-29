---
title: Azure의 Linux VM에서 MySQL 설정 | Microsoft Docs
description: Azure Linux 가상 머신(Ubuntu 또는 RedHat 제품군 OS)에 MySQL 스택을 설치하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 153bae7c-897b-46b3-bd86-192a6efb94fa
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: f9e0582a1338bcae7b330c7ece7c3d8cc8593cfa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543942"
---
# <a name="how-to-install-mysql-on-azure"></a>Azure에 MySQL을 설치하는 방법
이 문서에서는 Linux를 실행하는 Azure 가상 머신에서 MySQL을 설치 및 구성하는 방법을 알아봅니다.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-mysql-on-your-virtual-machine"></a>가상 머신에 MySQL 설치
> [!NOTE]
> 이 자습서를 완료하려면 Linux를 실행하는 Microsoft Azure 가상 머신이 이미 있어야 합니다. 계속하기 전에 VM 이름으로 `mysqlnode`를 사용하고 사용자로 `azureuser`를 사용하여 Linux VM을 만들고 설정하려면 [Azure Linux VM 자습서](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.
> 
> 

이 경우 3306 포트를 MySQL 포트로 사용합니다.  

putty를 통해 생성한 Linux VM에 연결합니다. 처음으로 Azure Linux VM을 사용하는 경우 putty를 사용하여 Linux VM에 연결하는 방법은 [여기](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

이 문서의 예로는 MySQL5.6을 설치하는 리포지토리 패키지를 사용합니다. 실제로, MySQL5.6의 성능은 MySQL5.5보다 많이 개선되었습니다.  자세한 내용은 [여기](http://www.mysqlperformanceblog.com/2013/02/18/is-mysql-5-6-slower-than-mysql-5-5/)를 참조하세요.

### <a name="how-to-install-mysql56-on-ubuntu"></a>Ubuntu에 MySQL5.6을 설치하는 방법
여기서는 Azure에 Ubuntu가 있는 Linux VM을 사용합니다.

* 1단계: MySQL Server 5.6 설치 및 `root` 사용자로 전환:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    mysql-server 5.6 설치:
  
            #[root@mysqlnode ~]# apt-get update
            #[root@mysqlnode ~]# apt-get -y install mysql-server-5.6
  
    설치 하는 동안 아래 MySQL 루트 암호를 설정 하 라는 메시지가 표시 대화 상자 창이 표시 됩니다 하 고 여기에 암호를 설정 해야 합니다.
  
    ![Image](./media/mysql-install/virtual-machines-linux-install-mysql-p1.png)

    암호를 다시 입력하여 확인합니다.

    ![Image](./media/mysql-install/virtual-machines-linux-install-mysql-p2.png)

* 2단계: MySQL Server 로그인
  
    MySQL Server 설치가 완료되면 MySQL 서비스가 자동으로 시작됩니다. `root` 사용자로 MySQL Server에 로그인할 수 있습니다.
    아래와 같은 명령을 사용하여 로그인하고 암호를 입력합니다.
  
             #[root@mysqlnode ~]# mysql -uroot -p
* 3단계: 실행되는 MySQL 서비스 관리
  
    (a) MySQL 서비스의 상태를 가져옵니다.
  
             #[root@mysqlnode ~]# service mysql status
  
    (b) MySQL 서비스를 시작합니다.
  
             #[root@mysqlnode ~]# service mysql start
  
    (c) MySQL 서비스를 중지합니다.
  
             #[root@mysqlnode ~]# service mysql stop
  
    (d) MySQL 서비스를 다시 시작합니다.
  
             #[root@mysqlnode ~]# service mysql restart

### <a name="how-to-install-mysql-on-red-hat-os-family-like-centos-oracle-linux"></a>CentOS, Oracle Linux와 같은 Red Hat OS 제품군에서 MySQL을 설치하는 방법
여기에서는 CentOS 또는 Oracle Linux와 함께 Linux VM을 사용합니다.

* 1단계: MySQL Yum 리포지토리 추가 및 `root` 사용자로 전환:
  
            #[azureuser@mysqlnode:~]sudo su -
  
    MySQL 릴리스 패키지 다운로드 및 설치:
  
            #[root@mysqlnode ~]# wget https://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm
            #[root@mysqlnode ~]# yum localinstall -y mysql-community-release-el6-5.noarch.rpm
* 2단계: MySQL 리포지토리에서 MySQL5.6 패키지를 다운로드할 수 있도록 아래 파일 편집
  
            #[root@mysqlnode ~]# vim /etc/yum.repos.d/mysql-community.repo
  
    이 파일의 각 값을 아래와 같이 업데이트:
  
        \# *Enable to use MySQL 5.6*
  
        [mysql56-community]
        name=MySQL 5.6 Community Server
  
        baseurl=http://repo.mysql.com/yum/mysql-5.6-community/el/6/$basearch/
  
        enabled=1
  
        gpgcheck=1
  
        gpgkey=file:/etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
* 3단계: MySQL 리포지토리에서 MySQL 설치 및 MySQL 설치:
  
           #[root@mysqlnode ~]#yum install mysql-community-server
  
    MySQL RPM 패키지 및 모든 관련 패키지가 설치됩니다.
* 4단계: 실행되는 MySQL 서비스 관리
  
    (a) MySQL 서버의 서비스 상태 확인:
  
           #[root@mysqlnode ~]#service mysqld status
  
    (b) MySQL 서버의 기본 포트가 실행 중인지 확인:
  
           #[root@mysqlnode ~]#netstat  –tunlp|grep 3306

    (c) MySQL 서버 시작:

           #[root@mysqlnode ~]#service mysqld start

    (d) MySQL 서버 중지:

           #[root@mysqlnode ~]#service mysqld stop

    (e) 시스템을 부팅할 때 MySQL을 시작하도록 설정:

           #[root@mysqlnode ~]#chkconfig mysqld on


### <a name="how-to-install-mysql-on-suse-linux"></a>SUSE Linux에 MySQL을 설치하는 방법
여기에서는 OpenSUSE와 Linux VM을 사용합니다.

* 1단계: MySQL 서버 다운로드 및 설치
  
    아래 명령을 통해 `root` 사용자로 전환합니다.  
  
           #sudo su -
  
    MySQL 패키지 다운로드 및 설치:
  
           #[root@mysqlnode ~]# zypper update
  
           #[root@mysqlnode ~]# zypper install mysql-server mysql-devel mysql
* 2단계: 실행되는 MySQL 서비스 관리
  
    (a) MySQL Server 상태 확인:
  
           #[root@mysqlnode ~]# rcmysql status
  
    (b) MySQL Server의 기본 포트 상태 확인:
  
           #[root@mysqlnode ~]# netstat  –tunlp|grep 3306

    (c) MySQL 서버 시작:

           #[root@mysqlnode ~]# rcmysql start

    (d) MySQL 서버 중지:

           #[root@mysqlnode ~]# rcmysql stop

    (e) 시스템을 부팅할 때 MySQL을 시작하도록 설정:

           #[root@mysqlnode ~]# insserv mysql

### <a name="next-step"></a>다음 단계
[여기](https://www.mysql.com/)에서 MySQL에 대한 사용법 및 정보를 확인합니다.

