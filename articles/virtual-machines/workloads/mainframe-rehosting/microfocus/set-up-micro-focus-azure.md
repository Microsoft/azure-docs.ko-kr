---
title: Azure에서 Micro Focus Enterprise Server 4.0 및 엔터프라이즈 개발자 4.0 설치 | Microsoft Docs
description: Micro Focus 개발을 사용 하 여 IBM z/OS 메인프레임 워크 로드를 다시 호스트 하 고 Azure virtual machines (Vm)에서 환경을 테스트 합니다.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 33d0baf10df1882baf212c3e2c2683c8ca072fcc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487725"
---
# <a name="install-micro-focus-enterprise-server-40-and-enterprise-developer-40-on-azure"></a>Azure에서 Micro Focus Enterprise Server 4.0 및 엔터프라이즈 개발자 4.0 설치

이 문서에서는 설정 하는 방법을 보여 줍니다 [마이크로 포커스 Enterprise Server 4.0](https://www.microfocus.com/documentation/enterprise-developer/es30/) 하 고 [마이크로 포커스 엔터프라이즈 개발자 4.0](https://www.microfocus.com/documentation/enterprise-developer/ed_30/) Azure에서.

Azure에서 일반적인 작업을 개발 및 테스트 환경 이므로 따라서 쉽고 비용 효율적으로 배포 하 고 종료 하는 것입니다. Enterprise Server를 사용 하 여 플랫폼 중 하나는 가장 큰 메인프레임 재호스팅 사용할 Micro Focus 만들었습니다. 저렴 x86 z/OS 워크 로드를 실행할 수 있습니다 Windows 또는 Linux virtual machines (Vm)를 사용 하 여 Azure에서 플랫폼입니다.

이 설치 프로그램은 이미 설치 된 Microsoft SQL Server 2017을 사용 하 여 Azure Marketplace에서 Windows Server 2016 이미지를 실행 하는 Azure Vm을 사용 합니다. 이 설정은 Azure Stack에도 적용 됩니다.

Enterprise Server에 대 한 해당 개발 환경의 하거나 Microsoft Visual Studio 2017, Visual Studio Community (무료 다운로드)를 실행 하는 엔터프라이즈 개발자는 Eclipse 또는 합니다. 이 문서에서는 Visual Studio 2017이 설치를 사용 하 여 제공 되는 Windows Server 2016 가상 컴퓨터를 사용 하 여 배포 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에 이러한 필수 구성이 요소 확인 하십시오.

- Azure 구독. 구독이 없으면 시작하기 전에 [계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만드세요.

- Micro Focus 소프트웨어 및를 유효한 라이선스 (또는 평가판 라이선스). 기존 Micro Focus 고객 인 경우 Micro Focus 담당자에 게 문의 합니다. 그렇지 않으면 [평가판 요청](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/)합니다.

- 에 대 한 문서를 받아보세요 [엔터프라이즈 서버 및 엔터프라이즈 개발자](https://www.microfocus.com/documentation/enterprise-developer/#")합니다.

> [!NOTE]
> Azure Vm에 대 한 액세스를 제어할 수 있도록 사이트 간 가상 사설망 (VPN) 터널 또는 jumpbox를 설정 하는 것이 좋습니다.

## <a name="install-enterprise-server"></a>Enterprise Server 설치

1. 보안 및 관리 효율성 향상을 방금이 프로젝트에 대 한 새 리소스 그룹을 만드는 것이 좋습니다.-예를 들어 **RGMicroFocusEntServer**합니다. Azure에서 이름의 첫 번째 부분을 사용 하 여 목록에서 찾을 수 있도록 리소스의 유형을 지정 합니다.

2. 가상 머신을 만듭니다. Azure Marketplace에서 가상 컴퓨터 및 운영 체제를 선택 합니다. 권장 되는 설치는 다음과 같습니다.

    - **Enterprise Server**: Windows Server 2016 및 SQL Server 2017 설치를 사용 하 여 ES2 v3 VM (및 사용 하 여 2 개의 Vcpu 16GB 메모리)를 선택 합니다. 이 이미지는 Azure Marketplace에서 사용할 수 있습니다. Enterprise Server도 Azure SQL Database를 사용할 수 있습니다.

    - **엔터프라이즈 개발자**: Windows 10 및 Visual Studio가 설치 (사용 하 여 2 개의 Vcpu 및 8GB 메모리) B2ms VM을 선택 합니다. 이 이미지는 Azure Marketplace에서 사용할 수 있습니다.

3. 에 **기본 사항** 블레이드에서 사용자 이름과 암호를 입력 합니다. 선택 된 **구독** 하 고 **위치/지역을** Vm에 대해 사용 하려는. 선택 **RGMicroFocusEntServer** 리소스 그룹에 대 한 합니다.

4. 서로 통신할 수 있도록 동일한 가상 네트워크에 두 Vm을 배치 합니다.

5. 설정의 나머지 부분에 대 한 기본값을 적용 합니다. 사용자 이름 및 이러한 Vm의 관리자에 대해 만든 암호를 기억 합니다.

6. 가상 컴퓨터를 만든 경우 9003, 인바운드 포트를 열어야 86 및 HTTP 80 및 3389 Enterprise Server 컴퓨터에서 RDP에 대 한 개발자 컴퓨터에서 3389 합니다.

7. Azure portal에서 엔터프라이즈 서버 가상 머신에 로그온 ES2 v3 VM을 선택 합니다. 로 이동 합니다 **개요** 블레이드 **Connect** RDP 세션을 시작 하려면. VM에 대해 생성 된 자격 증명을 사용 하 여 로그온 합니다.

8. RDP 세션에서 다음 두 파일을 로드 합니다. Windows 이므로 이므로 끌어서 RDP 세션에 파일을 삭제할 수 있습니다.

    - **es\_40. exe**, 엔터프라이즈 서버 설치 합니다.

    - **mflic**, 해당 라이선스 파일-엔터프라이즈 서버 없이 로드 되지 것입니다.

9. 설치를 시작 하려면 파일을 두 번 클릭 합니다. 첫 번째 창에서 설치 위치를 선택 하 고 최종 사용자 사용권 계약에 동의 합니다.

     ![마이크로 포커스 엔터프라이즈 서버 설정 화면](media/01-enterprise-server.png)

     설치가 완료 되 면 다음 메시지가 표시 됩니다.

     ![마이크로 포커스 엔터프라이즈 서버 설정 화면](media/02-enterprise-server.png)

### <a name="check-for-updates"></a>업데이트 확인

설치 후 해야 하므로 Microsoft와 같은 필수 구성 요소 추가 업데이트가 있는지 확인 하려면 C++ 재배포 가능 패키지 및.NET Framework는 엔터프라이즈 서버와 함께 설치 됩니다.

### <a name="upload-the-license"></a>라이선스를 업로드 합니다.

1. Micro Focus 라이선스 관리를 시작 합니다.

2. 클릭 **시작** \> **마이크로 포커스 라이선스 관리자** \> **라이선스 관리**를 클릭 하 고는 **설치** 탭 합니다. 업로드할 라이선스 형식 선택: 라이선스 파일 또는 16 자 라이선스 코드입니다. 파일에 대 한 예를 들어,에 **라이선스 파일**로 이동 합니다 **mflic** 파일 이전에 업로드 된 VM을 선택 **라이선스 설치**합니다.

     ![마이크로 포커스 라이선스 관리 대화 상자](media/03-enterprise-server.png)

3. 엔터프라이즈 서버 로드 되는지 확인 합니다. 이 URL을 사용 하 여 브라우저에서 엔터프라이즈 서버 관리 사이트를 시작 해 봅니다 <http://localhost:86/> 합니다. 엔터프라이즈 서버 관리 페이지는 표시 된 것 처럼 표시 됩니다.

     ![엔터프라이즈 서버 관리 페이지](media/04-enterprise-admin.png)

## <a name="install-enterprise-developer-on-the-developer-machine"></a>엔터프라이즈 개발자 개발자 컴퓨터에 설치

1. 앞에서 만든 리소스 그룹을 선택 합니다 (예를 들어 **RGMicroFocusEntServer**), 개발자 이미지를 선택 합니다.

2. 가상 컴퓨터에 로그온 하려면로 이동 합니다 **개요** 블레이드에서 선택한 **연결**합니다. 이 RDP 세션을 시작합니다. VM에 대해 생성 된 자격 증명을 사용 하 여 로그온 합니다.

3. RDP 세션에서 다음 두 파일 (끌어서 놓기 사용 하려는 경우)를 로드 합니다.

    - **edvs2017.exe**, 엔터프라이즈 서버 설치 합니다.

    - **mflic**, 해당 라이선스 파일 (엔터프라이즈 개발자 없이 로드 되지 것입니다).

4. 두 번 클릭 합니다 **edvs2017.exe** 파일 설치를 시작 합니다. 첫 번째 창에서 설치 위치를 선택 하 고 최종 사용자 사용권 계약에 동의 합니다. 원한다 면 선택 **Rumba 9.5 설치** 할 수 있습니다이 터미널 에뮬레이터를 설치 합니다.

     ![Visual Studio 2017 설치 대화 상자에 대 한 마이크로 포커스 엔터프라이즈 개발자](media/04-enterprise-server.png)

5. 설치가 완료 되 면 다음 메시지가 표시 됩니다.

     ![설치 성공 메시지](media/05-enterprise-server.png)

6. Enterprise server와 마찬가지로 마이크로 포커스 라이선스 관리자를 시작 합니다. 선택 **시작** \> **마이크로 포커스 라이선스 관리자** \> **라이선스 관리**를 클릭 하 고는 **설치**탭 합니다.

7. 업로드할 라이선스 형식 선택: 라이선스 파일 또는 16 자 라이선스 코드입니다. 파일에 대 한 예를 들어,에 **라이선스 파일**로 이동 합니다 **mflic** 파일 이전에 업로드 된 VM을 선택 **라이선스 설치**합니다.

     ![마이크로 포커스 라이선스 관리 대화 상자](/edia/07-enterprise-server.png)

엔터프라이즈 개발자가 로드 되 면 Azure에서 Micro Focus 개발 및 테스트 환경 배포 완료 되었습니다!

## <a name="next-steps"></a>다음 단계

- [은행 데모 응용 프로그램 설정](./demo.md)
- [Docker 컨테이너에서 Enterprise Server를 실행 합니다.](./run-enterprise-server-container.md)
- [메인프레임 애플리케이션 마이그레이션](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
