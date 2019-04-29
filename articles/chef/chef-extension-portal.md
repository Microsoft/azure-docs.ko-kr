---
title: Azure Portal에서 Chef 클라이언트 설치
description: Azure Portal에서 Chef 클라이언트를 배포 및 구성하는 방법 알아보기
keywords: azure, chef, devops, client, install, portal
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: cf7afb50006fb273b4d685f9e4259be1cb60fe4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563838"
---
# <a name="install-the-chef-client-from-the-azure-portal"></a>Azure Portal에서 Chef 클라이언트 설치
Azure Portal에서 Linux 또는 Windows 머신에 직접 Chef 클라이언트 확장을 추가할 수 있습니다. 이 문서에서는 새 Linux 가상 머신을 사용하여 해당 프로세스를 안내합니다.

## <a name="prerequisites"></a>필수 조건

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

- **Chef**: 활성 Chef 계정이 없는 경우 [호스트된 Chef의 평가판](https://manage.chef.io/signup)에 등록합니다. 이 문서의 지침을 따르려면 Chef 계정에서 다음 값이 필요합니다.
  - organization_validation 키
  - rb
  - run_list

## <a name="install-the-chef-extension-on-a-new-linux-virtual-machine"></a>새 Linux 가상 머신에 Chef 확장 설치
이 섹션에서는 먼저 Azure Portal을 사용하여 Linux 컴퓨터를 만듭니다. 프로세스 중 새 가상 머신에 Chef 확장을 설치하는 방법도 알게 됩니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.

1. 왼쪽 메뉴에서 **가상 머신** 옵션을 선택합니다. **가상 머신** 옵션이 없는 경우 **모든 서비스**를 선택한 다음, **가상 머신**을 선택합니다.

1. **가상 머신** 탭에서 **추가**를 선택합니다.

    ![Azure Portal에 새 가상 머신을 추가합니다.](./media/chef-extension-portal/add-vm.png)

1. **계산** 탭에서 원하는 운영 체제를 선택합니다. 이 데모에서는 **Ubuntu Server**가 선택됩니다.

1. **Ubuntu Server** 탭에서 **Ubuntu Server 16.04 LTS**를 선택합니다.

    ![Ubuntu 가상 머신을 만들 때 필요한 버전을 지정합니다.](./media/chef-extension-portal/ubuntu-server-version.png)

1. **Ubuntu Server 16.04 LTS** 탭에서 **만들기**를 선택합니다.

    ![Ubuntu는 해당 제품에 대한 추가 정보를 제공합니다.](./media/chef-extension-portal/create-vm.png)

1. **가상 머신 만들기** 탭에서 **기본**을 선택합니다.

1. **기본** 탭에서 다음 값을 지정한 다음, **확인**을 선택합니다.

   - **이름** - 새 가상 머신의 이름을 입력합니다.
   - **VM 디스크 유형** - 저장소 디스크 유형에 대해 **SSD** 또는 **HDD**를 지정합니다. Azure의 가상 머신 디스크 유형에 대한 자세한 내용은 [디스크 유형 선택](../virtual-machines/windows/disks-types.md) 문서를 참조하세요.
   - **사용자 이름** - 가상 머신에서 관리자 권한이 부여된 사용자 이름을 입력합니다.
   - **인증 형식** - **암호**를 선택합니다. **SSH 공개 키**를 선택하고, SSH 공개 키 값을 제공할 수도 있습니다. 이 데모의 목적을 위해(및 스크린샷에서) **암호**가 선택됩니다.
   - **암호** 및 **암호 확인** - 사용자에 대한 암호를 입력합니다.
   - **Azure Active Directory로 로그인** - **사용 안 함**을 선택합니다.
   - **구독** - 둘 이상의 구독이 있는 경우 원하는 Azure 구독을 선택합니다.
   - **리소스 그룹** - 리소스 그룹의 이름을 입력합니다.
   - **위치** - **미국 동부**를 선택합니다.

     ![가상 머신 생성용 기본 탭](./media/chef-extension-portal/add-vm-basics.png)

1. **크기 선택** 탭에서 가상 머신의 크기를 선택한 다음, **선택**을 선택합니다.

1. **설정** 탭에서 대부분의 값은 이전 탭에서 선택한 값을 기반으로 채워집니다. **확장**을 섡택합니다.

     ![설정 탭을 통해 확장이 가상 머신에 추가됩니다.](./media/chef-extension-portal/add-vm-select-extensions.png)

1. **확장** 탭에서 **확장 추가**를 선택합니다.

     ![확장 추가를 선택하여 가상 머신에 확장을 추가합니다.](./media/chef-extension-portal/add-vm-add-extension.png)

1. **새 리소스** 탭에서 **Linux Chef 확장(1.2.3)** 을 선택합니다.

     ![Chef에는 Linux 및 Windows 가상 머신에 대한 확장이 있습니다.](./media/chef-extension-portal/select-linux-chef-extension.png)

1. **Linux Chef 확장** 탭에서 **만들기**를 선택합니다.

1. **확장 설치** 탭에서 다음 값을 지정한 다음, **확인**을 선택합니다.

    - **Chef 서버 URL** - 조직 이름을 포함하는 Chef 서버 URL을 입력합니다(예: *https://api.chef.io/organization/mycompany*).
    - **Chef 노드 이름** - Chef 노드 이름을 입력합니다. 모든 값이 될 수 있습니다.
    - **실행 목록** - 컴퓨터에 추가되는 Chef 실행 목록을 입력합니다. 비워 둘 수 있습니다.
    - **유효성 검사 클라이언트 이름** - Chef 유효성 검사 클라이언트 이름을 입력합니다. 예: *tarcher-validator*.
    - **유효성 검사 키** - 컴퓨터를 부트스트래핑할 때 사용되는 유효성 검사 키를 포함하는 파일을 선택합니다.
    - **클라이언트 구성 파일** - chef 클라이언트에 대한 구성 파일을 선택합니다. 비워 둘 수 있습니다.
    - **Chef 클라이언트 버전** - 설치할 chef 클라이언트의 버전을 입력합니다. 비워 둘 수 있습니다. 값을 비워 두면 최신 버전이 설치됩니다.
    - **SSL 확인 모드** - **없음** 또는 **피어**를 선택합니다. 데모에 ‘없음’이 선택되었습니다.
    - **Chef 환경** - 이 노드가 구성원이어야 하는 Chef 환경을 입력합니다. 비워 둘 수 있습니다.
    - **암호화된 Databag 비밀** - 이 컴퓨터에 액세스 권한이 있어야 하는 암호화된 Databag에 대한 비밀을 포함하는 파일을 선택합니다. 비워 둘 수 있습니다.
    - **Chef 서버 SSL 인증서** - Chef 서버에 할당된 SSL 인증서를 선택합니다. 비워 둘 수 있습니다.

      ![Linux 가상 머신에 Chef 서버 설치](./media/chef-extension-portal/install-extension.png)

1. **확장** 탭으로 돌아오면 **확인**을 선택합니다.

1. **설정** 탭으로 돌아오면 **확인**을 선택합니다.

1. **만들기** 탭으로 돌아오면(선택 및 입력한 옵션의 요약을 나타냄) 정보 및 **사용 약관**을 확인하고 **만들기**를 선택합니다.

Chef 확장을 사용하여 가상 머신을 만들고 배포하는 프로세스가 완료되면 알림은 작업의 성공 또는 실패 여부를 나타냅니다. 또한 가상 머신이 만들어지면 새 가상 머신에 대한 리소스 페이지가 Azure Portal에서 자동으로 열립니다.

![Linux 가상 머신에 Chef 서버 설치](./media/chef-extension-portal/resource-created.png)

## <a name="next-steps"></a>다음 단계

- [Azure에서 Chef를 사용하여 Windows 가상 머신 만들기](/azure/virtual-machines/windows/chef-automation)
