---
title: Azure 가상 머신을 만든 후 WinRM 구성 | Microsoft Docs
description: Azure에 호스트된 가상 머신을 만든 후 WinRM(Windows 원격 관리)을 구성하는 방법을 설명합니다.
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
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 6350d5bde737c46cf14a9aef75a7ec57260a6afa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744603"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>가상 머신을 만든 후 WinRM 구성

이 문서에서는 HTTPS를 통한 WinRM을 사용하도록 Azure에 호스트된 기존 VM(가상 머신)을 구성하는 방법을 설명합니다.  이 구성은 Windows 기반 VM에만 적용되며 다음 2단계 프로세스가 필요합니다.

1. HTTPS 프로토콜을 통한 WinRM에 대해 포트 트래픽을 사용하도록 설정합니다.  Azure Portal에서 VM에 대해 이 설정을 구성합니다.
2. 제공된 PowerShell 스크립트를 실행하여 WinRM을 사용하도록 VM을 구성합니다.


## <a name="enabling-port-traffic"></a>포트 트래픽 사용

HTTPS 프로토콜을 통한 WinRM은 Azure Marketplace에서 제공되는 사전 구성된 Windows VM에서 기본적으로 사용할 수 없는 포트 5896을 사용합니다. 이 프로토콜을 사용하려면 [Azure Portal](https://portal.azure.com)에서 다음 단계를 수행하여 NSG(네트워크 보안 그룹)에 새 규칙을 추가합니다.  NSG에 대한 자세한 내용은 [보안 그룹](https://docs.microsoft.com/azure/virtual-network/security-overview)을 참조하세요.

1.  **가상 머신 >**  <*vm-name*>  **> 설정/네트워킹** 블레이드로 이동합니다.
2.  NSG 이름(이 예제에서는 **testvm11002**)을 클릭하여 해당 속성을 표시합니다.

    ![네트워크 보안 그룹 속성](./media/nsg-properties.png)
 
3. **설정**에서 **인바운드 보안 규칙**을 선택하여 이 블레이드를 표시합니다.
4. **+추가**를 클릭하여 TCP 포트 5986에 대해 `WinRM_HTTPS`라는 새 규칙을 만듭니다.

    ![인바운드 네트워크 보안 규칙 추가](./media/nsg-new-rule.png)

5. 값을 모두 제공했으면 **확인**을 클릭합니다.  인바운드 보안 규칙 목록에 다음과 같은 새 항목이 포함됩니다.

    ![인바운드 네트워크 보안 규칙 목록](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>WinRM을 사용하도록 VM 구성 

Windows VM에서 Windows 원격 관리 기능을 사용하도록 설정하고 구성하려면 다음 단계를 사용합니다.   

1. Azure에 호스트된 VM에 대한 원격 데스크톱 연결을 설정합니다.  자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그인하는 방법](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)을 참조하세요.  나머지 단계는 VM에서 실행됩니다.
2. 다음 파일을 다운로드하여 VM의 폴더에 저장합니다.
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. 상승된 권한(**관리자 권한으로 실행**)을 사용하여 **PowerShell 콘솔**을 엽니다. 
4. 필수 매개 변수인 VM의 FQDN(정규화된 도메인 이름)을 제공하여 다음 명령을 실행합니다. <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Powershell 구성 스크립트 1](./media/powershell-file1.png)

    이 스크립트는 동일한 폴더에 있는 다른 두 파일에 종속됩니다.


## <a name="next-steps"></a>다음 단계

WinRM을 구성했으므로 이제 [구성된 VHD를 통해 VM을 배포](./cpp-deploy-vm-vhd.md)할 준비가 되었습니다.
