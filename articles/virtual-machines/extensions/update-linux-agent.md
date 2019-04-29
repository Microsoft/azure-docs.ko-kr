---
title: GitHub에서 Azure Linux 에이전트 업데이트 | Microsoft Docs
description: Azure Linux VM의 Azure Linux 에이전트를 업데이트하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: roiyz
ms.openlocfilehash: 5d53f34ea6b0983d0687cdaf6ec6271c703bb055
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799752"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>VM에서 Azure Linux 에이전트를 업데이트하는 방법

Azure Linux VM에서 [Azure Linux 에이전트](https://github.com/Azure/WALinuxAgent) 를 업데이트하려면 다음 항목이 이미 있어야 합니다.

- Azure에서 실행 중인 Linux VM
- SSH를 사용하여 해당 Linux VM에 연결

항상 Linux 배포판 리포지토리의 패키지에 대해 먼저 확인해야 합니다. 사용 가능한 패키지는 최신 버전이 아닐 수도 있지만 자동 업데이트를 사용하면 Linux 에이전트에서 항상 최신 업데이트를 가져올 수 있습니다. 패키지 관리자에서 설치 문제가 있는 경우 배포판 공급 업체에서 지원을 검색해야 합니다.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Azure의 최소 가상 머신 에이전트 지원
계속하기 전에 [Azure의 가상 머신 에이전트에 대한 최소 버전 지원](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)을 확인합니다.

## <a name="updating-the-azure-linux-agent"></a>Azure Linux 에이전트 업데이트

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>패키지 캐시 업데이트

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작

#### <a name="restart-agent-for-1404"></a>14.04에 대한 에이전트 다시 시작

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>16.04 / 17.04에 대한 에이전트 다시 시작

```bash
systemctl restart walinuxagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-wheezy"></a>Debian 7 “Wheezy”

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>패키지 캐시 업데이트

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>에이전트 자동 업데이트 활성화
이 버전의 Debian이 2.0.16 이상의 버전을 포함하지 않으므로 AutoUpdate를 사용할 수 없습니다. 위 명령의 출력에서 패키지가 최신 상태인지 표시합니다.

### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 “Jessie” / Debian 9 “Stretch”

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>패키지 캐시 업데이트

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo apt-get install waagent
```
#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인 

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작

```
sudo systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>사용 가능한 업데이트 확인

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인 

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>사용 가능한 업데이트 확인

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인 

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>사용 가능한 업데이트 확인

위의 출력에서 패키지가 최신 상태인지 표시합니다.

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인 

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>현재 패키지 버전 확인

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>사용 가능한 업데이트 확인

위의 출력에서는 패키지가 최신 상태인지가 표시됩니다.

#### <a name="install-the-latest-package-version"></a>최신 패키지 버전 설치

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인 

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>waagent 서비스 다시 시작

```bash
sudo systemctl restart waagent.service
```

## <a name="oracle-6-and-7"></a>Oracle 6 및 7

Oracle Linux의 경우 `Addons` 리포지토리가 사용되도록 설정되었는지 확인합니다. 파일 `/etc/yum.repos.d/public-yum-ol6.repo`(Oracle Linux 6) 또는 파일 `/etc/yum.repos.d/public-yum-ol7.repo`(Oracle Linux)를 편집하고 이 파일의 **[ol6_addons]** 또는 **[ol7_addons]** 아래에서 줄 `enabled=0`을 `enabled=1`로 변경하도록 선택합니다.

그런 다음 최신 버전의 Azure Linux 에이전트를 설치하려면 다음을 입력합니다.

```bash
sudo yum install WALinuxAgent
```

추가 기능 리포지토리를 찾을 수 없는 경우 Oracle Linux 릴리스에 따라 .repo 파일의 맨 뒤에 다음 줄을 추가하기만 하면 됩니다.

Oracle Linux 6 가상 머신의 경우:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Oracle Linux 7 가상 머신의 경우:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

그런 다음 입력합니다.

```bash
sudo yum update WALinuxAgent
```

일반적으로는 이렇게만 하면 되지만 어떤 이유로든 https://github.com에서 직접 설치해야 하는 경우 다음 단계를 사용하세요.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>배포에 대해 에이전트 패키지가 없는 경우 Linux 에이전트를 업데이트합니다.

명령줄에 `sudo yum install wget`를 입력하여 wget을 설치합니다. Red Hat, CentOS, Oracle Linux 버전 6.4 및 6.5와 같이 기본적으로 이 방법으로 설치되지 않는 몇 가지 배포판이 있습니다.

### <a name="1-download-the-latest-version"></a>1. 최신 버전 다운로드
웹 페이지에서 [GitHub의 Azure Linux 에이전트 릴리스](https://github.com/Azure/WALinuxAgent/releases) 를 열고 최신 버전 번호를 확인합니다. `waagent --version`을 입력하면 현재 버전을 찾을 수 있습니다.

#### <a name="for-version-22x-or-later-type"></a>2.2.x 이상 버전의 경우 다음을 입력합니다.
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

예를 들어 다음 줄에서는 버전 2.2.0을 사용합니다.

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Azure Linux 에이전트를 설치합니다.

#### <a name="for-version-22x-use"></a>버전 2.2.x의 경우 다음을 사용합니다.
`setuptools` 패키지를 먼저 설치해야 할 수도 있습니다. [여기](https://pypi.python.org/pypi/setuptools)를 참조하세요. 그런 후 다음을 실행합니다.

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>자동 업데이트가 활성화되었는지 확인

먼저 활성화되었는지 확인합니다.

```bash
cat /etc/waagent.conf
```

'AutoUpdate.Enabled'를 찾습니다. 이 출력이 표시되는 경우 활성화되었습니다.

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

실행을 활성화하려면:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. waagent 서비스 다시 시작
대부분의 Linux 배포판:

```bash
sudo service waagent restart
```

Ubuntu의 경우 다음을 사용합니다.

```bash
sudo service walinuxagent restart
```

CoreOS의 경우 다음을 사용합니다.

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Azure Linux 에이전트 버전 확인
    
```bash
waagent -version
```

CoreOS에서는 위의 명령이 작동하지 않을 수 있습니다.

Azure Linux 에이전트 버전이 새 버전으로 업데이트된 것을 확인할 수 있습니다.

Azure Linux 에이전트에 대한 자세한 내용은 [Azure Linux 에이전트 추가 정보](https://github.com/Azure/WALinuxAgent)를 참조하세요.
