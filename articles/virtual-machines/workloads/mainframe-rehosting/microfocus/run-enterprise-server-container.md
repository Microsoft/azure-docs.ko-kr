---
title: Micro Focus Enterprise Server 4.0을 Docker 컨테이너에서 Azure Virtual Machines에서 실행
description: Azure Virtual Machines에서 Docker 컨테이너에서 Micro Focus Enterprise Server를 실행 하 여 IBM z/OS 메인프레임 워크 로드를 다시 호스트 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61488461"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Micro Focus Enterprise Server 4.0을 Azure에서 Docker 컨테이너에서 실행

Azure에서 Docker 컨테이너에서 마이크로 포커스 Enterprise Server 4.0을 실행할 수 있습니다. 이 자습서 방법을 보여 줍니다. Enterprise Server에 대 한 Windows CICS (고객 정보 제어 시스템) acctdemo 데모를 사용합니다.

Docker 응용 프로그램에 이식성 및 격리를 추가합니다. 예를 들어, 하나의 Windows VM에서 다른 실행에서 또는 Docker 사용 하 여 Windows 서버로 리포지토리에서 Docker 이미지를 내보낼 수 있습니다. 동일한 구성 사용 하 여 새 위치에서 실행 되는 Docker 이미지-엔터프라이즈 서버를 설치 하지 않고도 합니다. 해당 이미지의 일부입니다. 라이선스 고려 사항이 계속 적용 됩니다.

이 자습서를 설치 합니다 **컨테이너를 사용 하 여 Windows 2016 Datacenter** Azure Marketplace에서 가상 머신 (VM). 이 VM이 포함 되어 있습니다 **Docker 18.09.0**합니다. 다음 단계는 컨테이너를 배포, 실행, 다음 3270 에뮬레이터를 사용 하 여 연결 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에 이러한 필수 구성이 요소 확인 하십시오.

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Micro Focus 소프트웨어 및를 유효한 라이선스 (또는 평가판 라이선스). 기존 Micro Focus 고객 인 경우 Micro Focus 담당자에 게 문의 합니다. 그렇지 않으면 [평가판 요청](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)합니다.

     > [!NOTE]
     > Docker 데모 파일 Enterprise Server 4.0을 사용 하 여 포함 됩니다. 이 자습서에서는 ent\_server\_dockerfile\_4.0\_windows.zip 합니다. Enterprise Server 설치 파일을 액세스 하는 동일한 위치에서 액세스 하거나 이동할 *Micro Focus* 를 시작 합니다.

- 에 대 한 설명서 [엔터프라이즈 서버 및 엔터프라이즈 개발자](https://www.microfocus.com/documentation/enterprise-developer/#")합니다.

## <a name="create-a-vm"></a>VM 만들기

1. 미디어를 ent에서 secure\_server\_dockerfile\_4.0\_windows.zip 파일입니다. (Docker 이미지를 빌드하는 데 필요한) ES-Docker-Prod-XXXXXXXX.mflic 라이선스 파일을 보호 합니다.

2. VM을 만듭니다. 이렇게 하려면 Azure portal 선택 엽니다 **리소스 만들기** 왼쪽, 위를 기준으로 필터링 *windows server*합니다. 결과에서 선택 **컨테이너를 사용 하 여 Windows Server 2016 Datacenter –** 합니다.

     ![Azure portal 검색 결과](media/01-portal.png)

3. VM에 대 한 속성을 구성 하려면 인스턴스 세부 정보를 선택 합니다.

    1. VM 크기를 선택합니다. 이 자습서를 사용 하는 것이 좋습니다는 **표준 DS2\_v2** 2 개 Vcpu와 7GB 메모리를 사용 하 여 VM.

    2. 선택 합니다 **지역** 하 고 **리소스 그룹** 에 배포 하려는 합니다.

    3. 에 대 한 **가용성 옵션**, 기본 설정을 사용 합니다.

    4. 에 대 한 **Username**를 사용 하려면 관리자 계정 및 암호를 입력 합니다.

    5. 했는지 **3389 RDP 포트** 열려 있습니다. 이 포트에만 VM에 로그인 할 수 있도록 공개적으로 노출 해야 합니다. 그런 다음 모든 기본값을 적용 하 고 클릭 **검토 + 만들기**합니다.

     ![가상 머신 창 만들기](media/container-02.png)

4. 배포가 완료 (몇 분) 될 때까지 기다립니다. 메시지 VM 생성 되어 있는지를 알려 줍니다.

5. 클릭 **리소스로 이동** 로 이동 하는 **개요** 블레이드에서 VM에 대 한 합니다.

6. 오른쪽을 클릭 합니다 **Connect** 단추입니다. 합니다 **가상 머신에 연결** 옵션 오른쪽에 나타납니다.

7. 클릭 합니다 **RDP 파일 다운로드** 단추 VM에 연결할 수 있도록 RDP 파일을 다운로드 합니다.

8. 파일 다운로드 완료 되 면 사용자 이름 및 암호를 VM에 대해 생성 하 고 형식을 엽니다.

     > [!NOTE]
     > 로그인 하려면 회사 자격 증명을 사용 하지 마세요. (RDP 클라이언트가 사용 하려는 경우를 가정 합니다. 그렇지 않으면입니다.)

9.  선택 **보다 선택 항목**, 그런 다음 VM 자격 증명을 선택 합니다.

이 시점에서 VM이 실행 되 고 RDP를 통해 연결 합니다. 및 다음 단계에 대 한 준비를 로그인 합니다.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>샌드박스 디렉터리를 만들고 zip 파일 업로드

1.  데모 및 라이선스 파일을 업로드할 수 있는 VM에서 디렉터리를 만듭니다. 예를 들어 **c:\\샌드박스**합니다.

2.  업로드 **ent\_서버\_dockerfile\_4.0\_windows.zip** 하며 **ES-Docker-Prod-XXXXXXXX.mflic** 파일을 만들 디렉터리입니다.

3.  Zip 파일의 압축을 풉니다 합니다 **ent\_server\_dockerfile\_4.0\_windows** 추출 프로세스에서 만든 디렉터리입니다. 이 디렉터리는 추가 정보 파일 (.html 및.txt 파일) 및 하위 디렉터리 두 개를 포함 **EnterpriseServer** 하 고 **예제**합니다.

4.  복사본 **ES-Docker-Prod-XXXXXXXX.mflic** c:\\샌드박스\\ent\_server\_dockerfile\_4.0\_windows\\ C: 및 EnterpriseServer\\샌드박스\\ent\_server\_dockerfile\_4.0\_windows\\예제\\CICS 디렉터리.

> [!NOTE]
> 두 디렉터리를 라이선스 파일을 복사 해야 합니다. 이러한 이미지에 제대로 라이선스가 부여 되었는지 확인 하는 Docker 빌드 단계에 대 한 필수 이며

## <a name="check-docker-version-and-create-base-image"></a>기본 이미지를 만들고 Docker 버전 확인

> [!IMPORTANT]
> 적절 한 Docker 이미지를 만드는 2 단계 프로세스입니다. 먼저 Enterprise Server 4.0 기본 이미지를 만듭니다. X64에 대 한 다른 이미지를 만든 플랫폼입니다. (32 비트)는 x86을 만들 수 있지만 이미지, 64 비트 이미지를 사용 합니다.

1. 명령 프롬프트를 엽니다.

2. Docker가 설치 되어 있는지 확인 합니다. 명령 프롬프트에 다음을 입력합니다.

    ```
        docker version
    ```

     예를 들어 버전 때 18.09.0이 작성 되었습니다.

3. 디렉터리를 변경 하려면 입력 **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer**합니다.

4. 형식 **bld.bat IacceptEULA** 초기 기본 이미지 빌드 프로세스를 시작 합니다. 이 프로세스를 실행할 몇 분 동안 기다립니다. 결과에서 표시 하는 두 개의 이미지 만든-x64 및 x86에 대 한 하나:

     ![이미지를 표시 하는 명령 창](media/container-04.png)

5. CICS 데모를 위한 최종 이미지를 만들려면 디렉터리로 전환 CICS 입력 하 여 **cd\\샌드박스\\ent\_server\_dockerfile\_4.0\_windows\\ 예제\\CICS**합니다.

6. 입력 이미지를 만들려면 **bld.bat x64**합니다. 실행 하는 프로세스 및 이미지를 작성 하는 메시지가 몇 분 동안 기다립니다.

7. 형식 **docker 이미지** 모든 VM에 설치 된 Docker 이미지의 목록을 표시 합니다. 했는지 **microfocus/es-acctdemo** 그 중 하나입니다.

     ![Es acctdemo 이미지를 표시 하는 명령 창](media/container-05.png)

## <a name="run-the-image"></a>이미지 실행 

1.  Enterprise Server 4.0 및 명령 프롬프트에서 입력 acctdemo 응용 프로그램을 시작 합니다.

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  3270 터미널 에뮬레이터와 같은 설치 [x3270](http://x3270.bgp.nu/) 포트 9040, 실행 되는 이미지를 통해 연결 하는 데 사용 합니다.

3.  Docker 컨테이너 관리에 대 한 DHCP 서버로 작동할 수 있도록 acctdemo 컨테이너의 IP 주소를 가져옵니다.

    1.  실행 중인 컨테이너의 ID를 가져옵니다. 형식 **Docker ps** 참고 ID 확인 하 고 명령 프롬프트에서 (**22a0fe3159d0** 이 예제의). 다음 단계를 위해 저장 합니다.

    2.  Acctdemo 컨테이너에 대 한 IP 주소를 확인 하려면 이전 단계에서 컨테이너 ID를 다음과 같이 사용 합니다.

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       예를 들면 다음과 같습니다.

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Acctdemo 이미지에 대 한 IP 주소를 note 합니다. 예를 들어, 다음과 같이 출력 주소가 172.19.202.52 없습니다.

     ![IP 주소를 표시 하는 명령 창](media/container-06.png)

5. 에뮬레이터를 사용 하 여 이미지를 탑재 합니다. Acctdemo 이미지 및 9040 포트의 주소를 사용 하도록 에뮬레이터를 구성 합니다. 여기서 있기 **172.19.202.52:9040**합니다. 원하는 대로 비슷할 것입니다. 합니다 **CICS Signon** 화면이 열립니다.

    ![Signon CICS 화면](media/container-07.png)

6. 입력 하 여 CICS 영역에 로그인 **SYSAD** 에 대 한 합니다 **USERID** 및 **SYSAD** 에 대 한 합니다 **암호**합니다.

7. 에뮬레이터의 키맵을 사용 하 여 화면을 지웁니다. X3270를 선택 합니다 **키맵** 메뉴 옵션입니다.

8. Acctdemo 응용 프로그램을 시작 하려면 입력 **ACCT**합니다. 응용 프로그램에 대 한 초기 화면이 표시 됩니다.

     ![데모 계정 화면](media/container-08.png)

9. 표시 계정 형식으로 실험해 보세요. 예를 들어 입력 **D** 요청에 대 한 및 **11111** 에 대 한 합니다 **계정**합니다. 다른 계정 번호를은 22222, 33333, 및 등입니다.

     ![데모 계정 화면](media/container-09.png)

10. Enterprise Server 관리 콘솔을 표시 하려면 명령 프롬프트 및 형식에 이동 **http:172.19.202.52:86 시작**

     ![Enterprise Server 관리 콘솔](media/container-010.png)

이것으로 끝입니다. 이제 실행 하 고 Docker 컨테이너에서 CICS 응용 프로그램을 관리 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure에서 Micro Focus Enterprise Server 4.0 및 엔터프라이즈 개발자 4.0 설치](./set-up-micro-focus-azure.md)
- [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
