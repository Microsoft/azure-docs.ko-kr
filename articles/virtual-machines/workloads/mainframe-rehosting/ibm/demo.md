---
title: 하는 응용 프로그램 개발자가 제어 배포 (ADCD) IBM zD & T v1 설정 | Microsoft Docs
description: IBM Z 개발 및 테스트 환경 (zD & T) 환경을 Azure Virtual Machines (Vm)에서 실행 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: f8af19056a343abdbafcd4ead8b072330cb41fd9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61438540"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>설정 하는 응용 프로그램 개발자가 제어 배포 (ADCD) IBM zD & T v1

Azure Virtual Machines (Vm)에 IBM Z 개발 및 테스트 환경 (zD & T) 환경을 실행할 수 있습니다. 이 환경 IBM Z 시리즈 아키텍처를 에뮬레이트합니다. 다양 한 Z 시리즈 운영 체제 또는 IBM 응용 프로그램 개발자가 제어 배포 (ADCDs)를 호출 하는 사용자 지정된 번들을 통해 제공 된 설치 (Z 인스턴스 또는 패키지)를 호스트할 수 있습니다.

이 문서에서는 in a zD Azure에서 T 환경을 ADCD 인스턴스를 설정 하는 방법을 보여 줍니다. ADCDs zD & T.에서 실행 되는 테스트 환경과 개발에 대 한 완전 한 Z 시리즈 운영 체제 구현 만들기

ZD (& T), 같은 ADCDs IBM 고객 및 파트너 에게만 제공 되며 개발 및 테스트 목적으로 전용입니다. 프로덕션 환경에 사용할 수는 있습니다. 다양 한 IBM 설치 패키지를 통해 다운로드할 수 있는 [Passport 장점은](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) 하거나 [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd)합니다.

## <a name="prerequisites"></a>필수 조건

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- 합니다 [zD & T 환경] [ ibm-install-z] 이전에 Azure에서 설정 합니다. 이 문서에서는 이전에 만든 동일한 Ubuntu 16.04 VM 이미지를 사용 한다고 가정 합니다.

- IBM PartnerWorld 또는 Passport Advantage를 통해 ADCD 미디어에 액세스 합니다.

- A [라이선스 서버가](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html)합니다. IBM zD T.를 실행 해야 만든에서 IBM 소프트웨어 라이선스는 방식에 따라 다릅니다.

  - **라이선스 서버 하드웨어에 따른** 소프트웨어의 모든 부분에 액세스 하는 데 필요한 합리적인 토큰이 포함 된 USB 하드웨어 장치가 필요 합니다. IBM에서 가져와야 합니다.

  - **라이선스 서버 소프트웨어를 기반으로** 라이선스 키 관리에 대 한 중앙 집중식된 서버를 설정 해야 합니다. 이 메서드는 것이 좋습니다 하며 관리 서버에서 IBM에서 수신 하는 키를 설정할 수 있습니다.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Passport 장점은에서 설치 패키지 다운로드

ADCD 미디어에 대 한 액세스가 필요 합니다. 다음 단계는 IBM 고객 및 Passport 활용을 사용할 수 있습니다를 가정 합니다. IBM 파트너가 사용할 수 있습니다 [IBM PartnerWorld](https://www-356.ibm.com/partnerworld/wps/servlet/ContentHandler/isv_com_sys_zos_adcd)합니다.

> [!NOTE]
> 이 문서에서는 Azure portal에 액세스 하 고 IBM 미디어를 다운로드 하려면 Windows PC에 사용 되는 것을 가정 합니다. Mac 또는 Ubuntu desktop을 사용 하는 경우 명령 및 IBM 미디어를 얻는 프로세스는 약간 달라질 수 있습니다.

1. 에 로그온 [Passport 장점은](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer)합니다.

2. 선택 **소프트웨어 다운로드** 하 고 **미디어 액세스**합니다.

3. 선택 **프로그래밍 기능 및 계약 번호**, 클릭 **계속**합니다.

4. 일부 설명 또는 부품 번호를 입력 하 고 클릭 **Finder**합니다.

5. 필요에 따라 사전순 목록 표시 하 고 제품 이름으로 보기를 클릭 합니다.

6. 선택 **모든 운영 체제** 에 **운영 체제 필드**, 및 **모든 언어** 에 **언어 필드**합니다. 그런 다음 클릭 **이동**합니다.

7. 클릭 **개별 파일을 선택** 에 목록을 확장 하 고 다운로드에 개별 미디어를 표시 합니다.

8. 다운로드를 선택 하려는 패키지를 확인 **다운로드**, 고 디렉터리에 파일을 다운로드 합니다.

## <a name="upload-the-adcd-packages"></a>ADCD 패키지 업로드

패키지 했으므로 Azure에서 VM에 업로드 해야 합니다.

1. Azure portal에서 시작 된 **ssh** 만든 Ubuntu VM을 사용 하 여 세션. VM 선택으로 이동 합니다 **개요** 블레이드에서 선택한 후 **Connect**합니다.

2. 선택 된 **SSH** 탭을 선택한 다음 복사는 ssh 명령을 클립보드에 합니다.

3. 자격 증명을 사용 하 여 VM에 로그온 및 [SSH 클라이언트](/azure/virtual-machines/linux/use-remote-desktop) 선택 합니다. 이 데모에서는 bash 셸 Windows 명령 프롬프트에 추가 하는 Windows 10 용 Linux 확장을 사용 합니다. PuTTY와 마찬가지로 작동합니다.

4. 에 로그온 하는 경우 IBM 패키지를 업로드 하려면 디렉터리를 만듭니다. Linux에서 대/소문자 구분입니다. 예를 들어이 데모에 업로드 된 패키지를 가정 합니다.

        /home/MyUserID/ZDT/adcd/nov2017/volumes

5. 와 같은 SSH 클라이언트를 사용 하 여 파일 업로드[WinSCP](https://winscp.net/eng/index.php)합니다. SCP SSH의 일부 이므로 사용 포트 22에서 SSH 사용 하는 합니다. 입력할 수 있습니다 Windows 로컬 컴퓨터에 없는 경우는 [scp 명령이](http://man7.org/linux/man-pages/man1/scp.1.html) SSH 세션에서.

6. 사용자가 만든 Azure VM 디렉터리로 업로드를 시작, zD & T. 이미지 저장소 됩니다.

    > [!NOTE]
    > 했는지 **ADCDTOOLS 합니다. XML** 업로드가에 포함 되는 **홈/MyUserID/ZDT/adcd/nov2017** 디렉터리입니다. 이 시간은 나중에 필요합니다.

7. Azure에 대 한 연결에 따라 다소 시간이 소요 될 수 있는 파일을 업로드 하려면 될 때까지 기다립니다.

8. 업로드가 완료 되 면 볼륨 디렉터리로 이동 하 고 모든 압축을 해제 합니다 **gz** 볼륨:

    ```
        gunzip \*.gz
    ```
    
![파일 탐색기를 보여 주는 압축 gz 볼륨](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>이미지 저장소를 구성 합니다.

다음 단계 zD & 업로드 된 패키지를 사용 하는 T를 구성 하는 것입니다. ZD (& T) 내에서 이미지 저장소 프로세스 탑재 하 고 이미지를 사용할 수 있습니다. SSH 또는 FTP를 사용할 수 있습니다.

1. 시작 합니다 **zDTServer**합니다. 이렇게 하려면 루트 수준에서 여야 합니다. 순서 대로 다음 두 명령을 입력 합니다.
    ```
        sudo su -
        /opt/ibm/zDT/bin/startServer
    ```
2. 명령에 의해 URL 출력을 기록한이 URL을 사용 하 여 웹 서버에 액세스 합니다. 비슷합니다.
     > https://(your VM name or IP address):9443/ZDTMC/index.html
     >
     > 9443 포트를 사용 하는 웹 액세스를 해야 합니다. 이 사용 하 여 웹 서버에 로그온 합니다. ZD (& T)에 대 한 사용자 ID가 **zdtadmin** 암호 이며 **암호**합니다.

    ![IBM zD & T Enterprise Edition 시작 화면](media/02-welcome.png)

3. 에 **빠른 시작** 페이지의 **구성**를 선택 **이미지 저장소**합니다.

     ![IBM zD & T Enterprise Edition 빠른 시작 화면](media/03-quickstart.png)

4. 에 **이미지 저장소 구성** 페이지에서 **SSH 파일 전송 프로토콜**합니다.

5. 에 대 한 **호스트 이름**, 형식 **Localhost** 이미지를 업로드 한에 대 한 디렉터리 경로 입력 합니다. 예를 들어 /home/MyUserID/ZDT/adcd/nov2017/volumes 합니다.

6. 입력 된 **사용자 ID** 하 고 **암호** VM에 대 한 합니다. ZD, T 사용자 ID 및 암호를 사용 하지 마세요.

7. 액세스 하 고 선택에 대 한 연결 테스트 **저장할** 구성을 저장 합니다.

## <a name="configure-the-target-environments"></a>대상 환경 구성

다음 단계 zD T 대상 환경을 구성 하는 것입니다. 에뮬레이트된이 호스 티 드 환경 이미지 실행 하는 위치입니다.

1. 에 **빠른 시작** 페이지의 **구성**를 선택 **대상 환경**합니다.

2. 에 **대상 환경 구성** 페이지에서 **Add Target**합니다.

3. 선택 **Linux**합니다. IBM에서는 두 가지 환경, Linux 및 Cloud(OpenStack), 하지만이 데모는 Linux에서 실행 합니다.

4. 에 **추가 대상 환경** 페이지에 대 한 **호스트 이름**를 입력 **localhost**합니다. 유지할 **SSH 포트가** 로 설정 **22**합니다.

5. 에 **대상 환경 레이블을** 상자와 같은 레이블을 입력 합니다 **MyCICS 합니다.**

     ![대상 환경 화면 추가](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>ADCD 구성 및 배포

이전 구성 단계를 완료 한 후 zD & T 패키지 및 대상 환경을 사용 하도록 구성 해야 합니다. 마찬가지로 zD & T, 탑재 하 고 이미지를 사용할 수 있는 이미지 저장소 프로세스를 사용 하면 됩니다. SSH 또는 FTP를 사용할 수 있습니다.

1. 에 **빠른 시작** 페이지의 **구성**를 선택 **ADCD**합니다. ADCD 패키지를 탑재할 수 전에 완료 해야 하는 단계를 알려주는 지침의 집합을 표시 합니다. 왜 이름을 대상 디렉터리 앞에서 수행한 방식으로 설명 합니다.

2. 클릭을 올바른 디렉터리에는 모든 이미지를 업로드 하는 것으로 가정 합니다 **ADCD에서 이미지** (단계 7 다음 스크린샷에 표시) 오른쪽 아래에 표시 되는 링크가 있습니다.

     ![IBM zD & T Enterprise Edition-ADCD 구성 화면](media/05-adcd.png)

## <a name="create-the-image"></a>이미지 만들기

이전 구성 단계 완료 되 면 합니다 **ADCD 구성 요소를 사용 하 여 이미지를 만들려면** 페이지가 나타납니다.

1. 해당 볼륨에 있는 여러 패키지를 표시 하려면 (이 경우 2017 년 11 월) 볼륨을 선택 합니다.

2. 이 데모에서는 선택 **고객 정보 제어 시스템 (CICS)-5.3**합니다.

3. 에 **이미지 이름** 상자와 같은 이미지의 이름을 입력 합니다 **MyCICS 이미지**합니다.

4. 선택 된 **이미지 만들기** 오른쪽 아래에 있는 단추입니다.

     ![IBM zD & T Enterprise Edition-ADCD 구성 요소 화면을 사용 하 여 이미지 만들기](media/06-adcd.png)

5. 표시 되는 창에서 이미지를 알리는 성공적으로 배포를 선택 **이미지를 배포할**합니다.

6. 에 **이미지를 대상 환경에 배포** 페이지에서 이전 페이지에서 만든 이미지를 선택 (**MyCICS 이미지**) 및 앞에서 만든 대상 환경 (**MyCICS**).

7. 다음 화면에서 VM (즉, 없습니다 ztadmin 자격 증명)에 대 한 자격 증명을 제공 합니다.

8. 속성 창에서 입력 수가 **중앙 프로세서 (CPs)**, 양을 **시스템 메모리 (GB)**, 및 **배포 디렉터리** 실행 중인 이미지에 대 한 합니다. 데모 이므로 작게 유지 합니다.

9. 에 대 한 확인란을 선택 했는지 **z/OS 후 문제 IPL 명령을 자동으로 배포**합니다.

     ![속성 화면](media/07-properties.png)

10. 선택 **전체**합니다.

11. 선택 **이미지 배포** 에서 합니다 **대상 환경에 이미지 배포** 페이지입니다.

이미지는 이제 배포할 수 있습니다 및 3270 터미널 에뮬레이터에서 탑재할 준비가 되었습니다.

> [!NOTE]
> 오류 라는 수신 되 면 필요가 없습니다 지역 필요 151 Gb 디스크 공간이 충분 합니다.

축하합니다! 이제 Azure에서 IBM 메인프레임 환경을 실행 하는 있습니다.

## <a name="learn-more"></a>자세한 정보

- [메인프레임 마이그레이션: 허구와 팩트](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [Azure에서 IBM DB2 pureScale](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure)
- [문제 해결](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
- [Azure로 마이그레이션 메인프레임 익히기](https://azure.microsoft.com/en-us/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
