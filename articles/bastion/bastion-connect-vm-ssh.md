---
title: Azure 방호를 사용 하 여 Linux VM에 연결 | Microsoft Docs
description: 이 문서에서는 Azure 방호를 사용 하 여 Linux 가상 머신에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7fe1c2f74ca2a7b0fa4aefad934c45edd6f85a73
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990444"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Azure 방호를 사용 하 여 Linux 가상 머신에 SSH를 사용 하 여 연결

이 문서에서는 Azure 가상 네트워크에서 Linux Vm에 안전 하 고 원활 하 게 SSH 하는 방법을 보여 줍니다. Azure Portal에서 직접 VM에 연결할 수 있습니다. Azure Bastion을 사용하면 VM에 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. Azure 방호에 대한 자세한 내용은 [개요](bastion-overview.md)를 참조 하세요.

Azure 방호를 사용 하 여 SSH를 통해 Linux 가상 머신에 연결할 수 있습니다. 인증에 사용자 이름/암호 및 SSH 키를 모두 사용할 수 있습니다. 다음 중 하나를 사용 하 여 SSH 키를 사용 하 여 VM에 연결할 수 있습니다.

* 수동으로 입력 하는 개인 키
* 개인 키 정보를 포함 하는 파일입니다.

SSH 개인 키는 `"-----BEGIN RSA PRIVATE KEY-----"` 시작 하 고 `"-----END RSA PRIVATE KEY-----"`로 끝나는 형식 이어야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

VM이 상주 하는 가상 네트워크에 대한 Azure 방호 호스트를 설정 했는지 확인 합니다. 자세한 내용은 [Azure 방호 호스트 만들기](bastion-create-host-portal.md)를 참조 하세요. 요새 서비스가 프로 비전 되 고 가상 네트워크에 배포 되 면이를 사용 하 여이 가상 네트워크의 VM에 연결할 수 있습니다. 

방호를 사용 하 여 연결 하는 경우 RDP를 사용 하 여 Windows VM에 연결 하 고 SSH를 사용 하 여 Linux Vm에 연결 하는 것으로 가정 합니다. Windows VM에 연결 하는 방법에 대한 자세한 내용은 [vm에 연결-Windows](bastion-connect-vm-rdp.md)를 참조 하세요.

### <a name="required-roles"></a>필요한 역할

연결하려면 다음 역할이 필요합니다.

* 가상 머신에 대한 읽기 권한자 역할
* 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
* Azure Bastion 리소스에 대한 읽기 권한자 역할

### <a name="ports"></a>포트

SSH를 통해 Linux VM에 연결 하려면 VM에서 다음 포트를 열어야 합니다.

* 인바운드 포트: SSH (22)


## <a name="username"></a>연결: 사용자 이름 및 암호 사용

1.   [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결**을 클릭 합니다. SSH 연결을 사용 하는 경우 VM은 Linux 가상 머신 이어야 합니다.
1. 연결을 클릭 하면 RDP, SSH 및 방호의 3 개 탭이 있는 사이드 막대가 나타납니다. 가상 네트워크에 대해 방호를 프로 비전 한 경우에는 기본적으로 방호 탭이 활성화 됩니다. 가상 네트워크에 대한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](bastion-create-host-portal.md)을 참조 하세요.

   ![VM 연결](./media/bastion-connect-vm-ssh/bastion.png)
1. 가상 컴퓨터에 대한 SSH의 사용자 이름 및 암호를 입력 합니다.
1. 키를 입력 한 후 **연결** 단추를 클릭 합니다.

## <a name="privatekey"></a>Connect: 개인 키를 수동으로 입력 합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결**을 클릭 합니다. SSH 연결을 사용 하는 경우 VM은 Linux 가상 머신 이어야 합니다.
1. 연결을 클릭 하면 RDP, SSH 및 방호의 3 개 탭이 있는 사이드 막대가 나타납니다. 가상 네트워크에 대해 방호를 프로 비전 한 경우에는 기본적으로 방호 탭이 활성화 됩니다. 가상 네트워크에 대한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](bastion-create-host-portal.md)을 참조 하세요.

   ![VM 연결](./media/bastion-connect-vm-ssh/bastion.png)
1. 사용자 이름을 입력 하 고 **SSH 개인 키**를 선택 합니다.
1. 텍스트 영역 **SSH 개인 키** 에 개인 키를 입력 하거나 직접 붙여 넣습니다.
1. 키를 입력 한 후 **연결** 단추를 클릭 합니다.

## <a name="ssh"></a>연결: 개인 키 파일 사용

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신으로 이동한 다음 **연결**을 클릭 합니다. SSH 연결을 사용 하는 경우 VM은 Linux 가상 머신 이어야 합니다.

   ![VM 연결](./media/bastion-connect-vm-ssh/connect.png)
1. 연결을 클릭 하면 RDP, SSH 및 방호의 3 개 탭이 있는 사이드 막대가 나타납니다. 가상 네트워크에 대해 방호를 프로 비전 한 경우에는 기본적으로 방호 탭이 활성화 됩니다. 가상 네트워크에 대한 방호를 프로 비전 하지 않은 경우에는 [요새 구성](bastion-create-host-portal.md)을 참조 하세요.

   ![VM 연결](./media/bastion-connect-vm-ssh/bastion.png)
1. 사용자 이름을 입력 하 고 **로컬 파일에서 SSH 개인 키**를 선택 합니다.
1. **찾아보기** 단추 (로컬 파일의 폴더 아이콘)를 클릭 합니다.
1. 파일을 찾은 다음 **열기**를 클릭 합니다.
1. **연결** 을 클릭 하 여 VM에 연결 합니다. 연결을 클릭 하면이 가상 컴퓨터에 대한 SSH가 Azure Portal에서 바로 열립니다. 이 연결은 가상 머신의 개인 IP를 통해 요새 서비스에서 포트 443을 사용 하 여 HTML5를 통해 연결 됩니다.

## <a name="next-steps"></a>다음 단계

[요새 FAQ](bastion-faq.md) 읽기
