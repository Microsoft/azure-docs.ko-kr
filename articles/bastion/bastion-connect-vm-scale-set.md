---
title: Azure 방호를 사용 하 여 Windows 가상 머신 확장 집합에 연결 | Microsoft Docs
description: 이 문서에서는 Azure 방호를 사용 하 여 Azure 가상 머신 확장 집합에 연결 하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4f513aaf113ef4bd6e75e5c4b31e0f0252d45f10
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988093"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Azure 방호를 사용 하 여 가상 머신 확장 집합에 연결

이 문서에서는 Azure 방호를 사용 하 여 Azure 가상 네트워크에서 Windows 가상 머신 확장 집합 인스턴스에 안전 하 고 원활 하 게 RDP 하는 방법을 보여 줍니다. Azure Portal에서 직접 가상 머신 확장 집합 인스턴스에 연결할 수 있습니다. Azure Bastion을 사용하면 VM에 클라이언트, 에이전트 또는 추가 소프트웨어가 필요하지 않습니다. Azure 방호에 대한 자세한 내용은 [개요](bastion-overview.md)를 참조 하세요.

## <a name="before-you-begin"></a>시작하기 전에

가상 머신 확장 집합이 있는 가상 네트워크에 대해 Azure 방호 호스트를 설정 했는지 확인 합니다. 자세한 내용은 [Azure 방호 호스트 만들기](bastion-create-host-portal.md)를 참조 하세요. 가상 네트워크에 방호 서비스가 프로 비전 되 고 배포 되 면이를 사용 하 여이 가상 네트워크의 가상 머신 확장 집합 인스턴스에 연결할 수 있습니다. 요새는 RDP를 사용 하 여 Windows 가상 머신 확장 집합에 연결 하 고 SSH를 사용 하 여 Linux 가상 머신 확장 집합에 연결 하는 것으로 가정 합니다. Linux VM에 연결 하는 방법에 대한 자세한 내용은 [VM에 연결-Linux](bastion-connect-vm-ssh.md)를 참조 하세요.

## <a name="rdp"></a>RDP를 사용 하 여 연결

1. [Azure Portal](https://portal.azure.com)을 엽니다. 연결 하려는 가상 머신 확장 집합으로 이동 합니다.

   ![이동해](./media/bastion-connect-vm-scale-set/1.png)
2. 연결 하려는 가상 머신 확장 집합 인스턴스로 이동한 다음 **연결**을 선택 합니다. RDP 연결을 사용 하는 경우 가상 머신 확장 집합은 Windows 가상 머신 확장 집합 이어야 합니다.

   ![가상 머신 확장 집합](./media/bastion-connect-vm-scale-set/2.png)
3. **연결**을 선택 하면 RDP, SSH 및 방호의 3 개 탭이 있는 사이드 막대가 나타납니다. 세로 막대에서 **요새** 탭을 선택 합니다. 가상 네트워크에 대한 방호를 프로 비전 하지 않은 경우에는 링크를 선택 하 여 방호를 구성할 수 있습니다. 구성 지침은 [요새 구성](bastion-create-host-portal.md)을 참조 하세요.

   ![요새 탭](./media/bastion-connect-vm-scale-set/3.png)
4. 요새 탭에서 가상 머신 확장 집합에 대한 사용자 이름 및 암호를 입력 하 고 **연결**을 선택 합니다.

   ![연결](./media/bastion-connect-vm-scale-set/4.png)
5. 이 가상 머신에 대한 RDP 연결은 포트 443 및 요새 서비스를 사용 하 여 Azure Portal (HTML5 이상)에서 직접 열립니다.

## <a name="next-steps"></a>다음 단계

[요새 FAQ](bastion-faq.md)를 읽어 보세요.