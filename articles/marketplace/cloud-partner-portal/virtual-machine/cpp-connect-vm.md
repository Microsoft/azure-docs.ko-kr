---
title: Microsoft Azure 기반 가상 머신에 연결 | Microsoft Docs
description: Azure에서 만든 새 가상 머신에 연결하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744563"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Azure 기반 가상 머신에 연결

이 문서에서는 Azure에서 만든 VM(가상 머신)에 연결하고 로그인하는 방법에 대해 설명합니다.  성공적으로 연결되면 호스트 서버에 로컬로 로그온한 것처럼 VM을 사용하여 작업할 수 있습니다. 

## <a name="connect-to-a-windows-based-vm"></a>Windows 기반 VM에 연결

원격 데스크톱 클라이언트를 사용하여 Azure에서 호스팅되는 Windows 기반 VM에 연결합니다.  대부분의 Windows 버전에는 기본적으로 RDP(원격 데스크톱 프로토콜)에 대한 지원이 포함되어 있습니다.  다른 머신의 경우 [원격 데스크톱 클라이언트](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)에서 클라이언트에 대한 자세한 정보를 찾을 수 있습니다.  

다음 문서에는 기본 제공 Windows RDP 지원을 사용 하 여 VM에 연결 하는 방법을 자세히 설명: [연결에 Windows를 실행 하는 Azure 가상 머신에 로그온 하는 방법을](../../../virtual-machines/windows/connect-logon.md)합니다.  

>[!TIP]
> 프로세스 중에 알 수 없는 게시자의 .rdp 파일이거나 사용자 자격 증명을 확인할 수 없다는 것과 같은 보안 경고가 표시될 수 있습니다.  이러한 경고는 무시해도 됩니다.


## <a name="connect-to-a-linux-based-vm"></a>Linux 기반 VM에 연결

Linux 기반 VM을 연결하려면 SSH(Secure Shell 프로토콜) 클라이언트가 필요합니다.  이 토론에서는 추가 비용이 들지 않는 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 터미널을 사용합니다.

1. [Azure Portal](https://ms.portal.azure.com)의 **Virtual Machines** 블레이드에서 연결하려는 VM을 선택합니다.  
2. VM이 아직 실행되지 않은 경우 VM을 **시작**합니다.
3. VM의 이름을 클릭하여 **개요** 페이지를 엽니다.
4. VM의 공용 IP 주소와 DNS 이름을 적어 둡니다.  이러한 값을 설정하지 않으면 [네트워크 인터페이스를 만들어야 합니다](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).

   ![VM 개요 설정](./media/publishvm_019.png)
 
5. PuTTY 애플리케이션을 엽니다.  
6. [PuTTY 구성] 대화 상자에서 VM의 IP 주소 또는 DNS 이름을 입력합니다. 

   ![PuTTY 터미널 설정](./media/publishvm_020.png)
 
7. **열기**를 클릭하여 PuTTY 터미널을 엽니다.  
8. 메시지가 표시되면 Linux VM 계정의 계정 이름과 암호를 입력합니다. 

   연결 문제가 발생 하는 경우 설명서를 참조 하면 SSH 클라이언트에 대 한 예를 들어 [10 장: 일반적인 오류 메시지](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors)합니다.

프로비전된 Linux VM에 데스크톱을 추가하는 방법을 포함한 자세한 내용은 [Azure에서 원격 데스크톱을 설치 및 구성하여 Linux VM에 연결](../../../virtual-machines/linux/use-remote-desktop.md)을 참조하세요.


## <a name="stop-unused-vms"></a>사용되지 않는 VM 중지
Azure는 실행 중이거나 *유휴 상태*인 VM의 호스팅에 대한 요금을 청구합니다.  따라서 현재 사용되지 않는 VM을 중지하는 것이 가장 좋습니다.  예를 들어 테스트, 백업 또는 사용 중지 VM은 종료 대상입니다. VM을 종료하려면 다음 단계를 수행합니다.

1. **Virtual Machines** 블레이드에서 중지하려는 VM을 선택합니다. 
2. 페이지 위쪽의 도구 모음에서 **중지** 단추를 클릭합니다.

   ![VM 중지](./media/publishvm_018.png)

Azure는 VM에서 운영 체제를 종료할 뿐만 아니라 이전에 프로비전된 하드웨어 및 네트워크 리소스를 해제하는 *할당 취소라는*  프로세스에서 VM을 빠르게 중지합니다.

중지된 VM을 나중에 다시 활성화하려면 해당 VM을 선택하고 **시작** 단추를 클릭합니다.


## <a name="next-steps"></a>다음 단계

원격으로 연결되면 [VM을 구성할](./cpp-configure-vm.md) 준비가 됩니다.
