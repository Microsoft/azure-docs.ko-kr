---
title: Azure에서 IBM zD T 개발/테스트 환경 설치 | Microsoft Docs
description: 서비스 (IaaS)로 Azure 가상 머신 (VM) 인프라에서 IBM Z 개발 및 테스트 환경 (zD & T)를 배포 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 2f5520213e7d8792c89f5445d470987323173dc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859344"
---
# <a name="install-ibm-zdt-devtest-environment-on-azure"></a>Azure에서 IBM zD T 개발/테스트 환경 설치

IBM Z 시스템에서 메인프레임 워크 로드에 대 한 개발/테스트 환경을 만들려면 서비스 (IaaS)로 IBM Z 개발 및 테스트 환경 (zD & T)에서 Azure 가상 머신 (VM) 인프라에 배포할 수 있습니다.

ZD (& T)를 사용 하 여 있습니다 활용 x86 비용을 절감 덜 중요 한 개발 및 테스트 환경 및 다음 Z 시스템이 프로덕션 환경에 업데이트를 다시 푸시에 대 한 플랫폼입니다. 자세한 내용은 참조는 [IBM ZD (& T) 설치 지침](https://www-01.ibm.com/support/docview.wss?uid=swg24044565#INSTALL)합니다.

Azure 및 Azure Stack의 다음 버전을 지원합니다.

- zD & T Personal Edition
- zD & T 병렬 Sysplex
- zD & T Enterprise Edition

ZD (& T)의 모든 버전 Linux 시스템의 Windows 서버가 아닌 경우 x86에만 실행합니다. Enterprise 버전은 Ubuntu/Debian 또는 Red Hat Enterprise Linux (RHEL)에서 지원 됩니다. RHEL 및 Debian VM 이미지를 Azure에 사용할 수 있습니다.

이 문서 작성 및 관리 환경에 대 한 zD & T Enterprise Edition 웹 서버를 사용할 수 있도록 zD 및 Azure에서 T Enterprise Edition을 설정 하는 방법을 보여 줍니다. ZD (& T)를 설치 하는 모든 환경을 설치 하지 않습니다. 이러한 설치 패키지와 별도로 만들어야 합니다. 예를 들어, 응용 프로그램 개발자가 제어 배포 (ADCD)은 테스트 환경의 볼륨 이미지입니다. IBM에서 사용 가능한 미디어 배포의 zip 이미지에 포함 된 경우. 참조 하는 방법 [Azure에서 ADCD 환경을](demo.md)합니다.

자세한 내용은 참조는 [zD & T 개요](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zdt.overview.gs.doc/topics/c_product_overview.html) IBM 지식 센터에서.

이 문서에서는 Z 개발 및 Azure에서 테스트 환경 (zD & T) Enterprise Edition을 설정 하는 방법을 보여 줍니다. 그런 다음 Azure에서 Z 기반 환경을 만들고 관리 하려면 zD & T Enterprise Edition 웹 서버를 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

> [!NOTE]
> IBM zD & T Enterprise 개발/테스트 환경 에서만 설치할 수 있습니다 —*되지* 프로덕션 환경입니다.

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- IBM 고객 및 파트너에만 사용할 수 있는 미디어에 액세스를 해야 합니다. 자세한 내용은 IBM 대리점에 문의 하거나의 연락처 정보를 참조 합니다 [zD & T](https://www.ibm.com/us-en/marketplace/z-systems-development-test-environment) 웹 사이트입니다.

- A [라이선스 서버가](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)합니다. 환경에 대 한 액세스에 대 한 필수입니다. 만든에서 IBM 소프트웨어 라이선스는 방식에 따라 다릅니다.

     - **라이선스 서버 하드웨어에 따른** 소프트웨어의 모든 부분에 액세스 하는 데 필요한 합리적인 토큰이 포함 된 USB 하드웨어 장치가 필요 합니다. IBM에서 가져와야 합니다.

     - **라이선스 서버 소프트웨어를 기반으로** 라이선스 키 관리에 대 한 중앙 집중식된 서버를 설정 해야 합니다. 이 메서드는 것이 좋습니다 하며 관리 서버에서 IBM에서 수신 하는 키를 설정할 수 있습니다.

## <a name="create-the-base-image-and-connect"></a>기본 이미지를 만들고 연결

1. Azure portal에서 [VM을 만들](/azure/virtual-machines/linux/quick-create-portal) 원하는 운영 체제 구성을 사용 하 여 합니다. 이 문서에서는 B4ms (사용 하 여 VM 4 개 Vcpu와 16GB의 메모리가) 가정 Ubuntu 16.04를 실행 합니다.

2. VM을 만든 후 SSH에 대 한 22, 21 ftp 및 웹 서버에 대 한 9443 인바운드 포트를 엽니다.

3. 에 표시 된 SSH 자격 증명을 가져올는 **개요** 를 통해 VM의 블레이드에서 합니다 **연결** 단추. 선택 된 **SSH** 탭 및 SSH 로그온 명령을 클립보드에 복사 합니다.

4. 로그온 하는 [Bash 셸을](/azure/cloud-shell/quickstart) 로컬 PC 및 붙여넣기 명령입니다. 형태로 됩니다 **ssh\<사용자 id\>\@\<IP 주소\>** 합니다. 자격 증명을 묻는 메시지가 나타나면을 입력 하 여 홈 디렉터리에 대 한 연결을 설정 합니다.

## <a name="copy-the-installation-file-to-the-server"></a>서버에 설치 파일을 복사 합니다.

웹 서버에 대 한 설치 파일이 **ZDT\_설치\_EE\_V12.0.0.1.tgz**합니다. IBM에서 미디어에 포함 됩니다. Ubuntu VM에이 파일을 업로드 해야 합니다.

1. 명령줄에서 모든 새로 만든된 이미지의 최신 상태 인지 확인 하려면 다음 명령을 입력 합니다.

    ```
    sudo apt-get update
    ```

2. 설치 하려면 디렉터리를 만듭니다.

    ```
    mkdir ZDT
    ```

3. VM에 로컬 컴퓨터에서 파일을 복사 합니다.

    ```
    scp ZDT_Install_EE_V12.0.0.1.tgz  your_userid@<IP Address /ZDT>   =>
    ```
    
> [!NOTE]
> 이 명령은 ZDT 디렉터리 클라이언트에 Windows 또는 Linux를 실행 하는 여부에 따라 달라 집니다 홈 디렉터리에 설치 파일을 복사 합니다.

## <a name="install-the-enterprise-edition"></a>Enterprise Edition을 설치

1. ZDT 디렉터리로 이동 하 고 압축 된 ZDT\_설치\_EE\_V12.0.0.1.tgz 파일에서 다음 명령을 사용 합니다.

    ```
    cd ZDT
    chmod 755 ZDT\_Install\_EE\_V12.0.0.0.tgz
    ```

2. 설치 관리자를 실행 합니다.

    ```
    ./ZDT_Install_EE_V12.0.0.0.x86_64
    ```

3. 선택 **1** 엔터프라이즈 서버를 설치 합니다.

4. 키를 눌러 **Enter** 사용권 계약을 신중 하 게 읽습니다. 입력 라이선스 끝 **예** 진행 합니다.

5. 새로 만든된 사용자에 대 한 암호를 변경 하 라는 메시지가 나타나면 **ibmsys1**을 명령을 사용 하 여 **sudo passwd ibmsys1** 하 고 새 암호를 입력 합니다.

6. 설치가 성공 했는지 여부를 확인 하려면 입력

    ```
    dpkg -l | grep zdtapp
    ```

7. 문자열을 출력에 포함 되어 있는지 확인 **zdtapp 12.0.0.0**나타내는 패키지 가스 성공적으로 설치 된

### <a name="starting-enterprise-edition"></a>Enterprise 시작

웹 서버를 시작할 때 실행 설치 프로세스 중 생성 된 zD (& T) 사용자 id 염두에 두어야 합니다.

1. 웹 서버를 시작 하려면 다음 명령을 실행 하려면 루트 사용자 ID를 사용 합니다.

    ```
    sudo /opt/ibm/zDT/bin/startServer
    ```

2. 다음과 같은 스크립트에서 URL 출력을 복사 합니다.

    ```
    https://<your IP address or domain name>:9443/ZDTMC/login.htm
    ```

3. ZD & T 설치 관리 구성 요소를 여는 웹 브라우저에 URL을 붙여 넣습니다.

## <a name="next-steps"></a>다음 단계

[설정 하는 응용 프로그램 개발자가 제어 배포 (ADCD) IBM zD & T v1](./demo.md)
