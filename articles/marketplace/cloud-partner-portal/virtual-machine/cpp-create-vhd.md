---
title: Azure Marketplace용 Azure 호환 VHD 만들기 | Microsoft Docs
description: Azure Marketplace에서 가상 머신 제안용 VHD를 만드는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2018
ms.author: pbutlerm
ms.openlocfilehash: 04a1741bbe4e60567a22445c5674ec03b232640c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744433"
---
# <a name="create-an-azure-compatible-vhd"></a>Azure 호환 VHD 만들기

이 문서에서는 Azure Marketplace에서 VM(가상 머신) 제안용 VHD(가상 하드 디스크)를 만드는 데 필요한 단계에 대해 자세히 설명합니다.  RDP(원격 데스크톱 프로토콜) 사용, VM 크기 선택, 최신 Windows 업데이트 설치 및 VHD 이미지 일반화와 같은 다양한 측면에 대한 모범 사례도 포함되어 있습니다.  다음 섹션에서는 주로 Windows 기반 VHD에 중점을 두고 있습니다. Linux 기반 VHD를 만드는 방법에 대한 자세한 내용은 [Azure 보증 배포판의 Linux](../../../virtual-machines/linux/endorsed-distros.md)를 참조하세요. 

> [!WARNING]
> Azure를 사용하여 미리 구성된 보증 운영 체제가 포함된 VM을 만들려면 이 항목의 지침을 따르는 것이 좋습니다.  솔루션을 사용 하 여 호환 되지 않으면 다음 있기을 만들고 승인 된 운영 체제를 사용 하 여 온-프레미스 VM을 구성 합니다.  그런 다음, [Azure에 업로드할 Windows VHD 또는 VHDX 준비](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)에서 설명한 대로 업로드할 수 있도록 구성하고 준비할 수 있습니다.


## <a name="select-an-approved-base"></a>승인된 기본 이미지 선택
VM 이미지용 운영 체제 VHD는 Windows Server 또는 SQL Server를 포함하는 Azure 승인 기본 이미지를 기반으로 해야 합니다.
시작하려면 Microsoft Azure Portal에 있는 다음 이미지 중 하나에서 VM을 만듭니다.

-   Windows Server([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012r2datacenter/), [2012 Datacenter](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2012datacenter/), [2008 R2 SP1](https://azure.microsoft.com/marketplace/partners/microsoft/windowsserver2008r2sp1/))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)(Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)(Enterprise, Standard, Web)

> [!TIP]
> 최신 Azure 포털 또는 PowerShell을 사용 중인 경우 2014년 9월 8일 이후에 게시된 Windows Server 이미지가 승인됩니다.

또는 Azure에서 승인된 Linux 배포판을 다양하게 제공합니다.  현재 목록은 [Azure 보증 배포판의 Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)를 참조하세요.


## <a name="create-vm-in-the-azure-portal"></a>Azure Portal에서 VM 만들기 

Microsoft [Azure Portal](https://ms.portal.azure.com/)에서 다음 단계를 사용하여 기본 이미지를 만듭니다.

1. VM 제안을 게시하려는 Azure 구독에 대한 Microsoft 계정으로 포털에 로그인합니다.
2. 새 리소스 그룹을 만들고, **리소스 그룹 이름**, **구독** 및 **리소스 그룹 위치**를 제공합니다.  자세한 지침은 [리소스 그룹 관리](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)를 참조하세요.
3. 왼쪽 도구 모음에서 **Virtual Machines**를 클릭하여 가상 머신 세부 정보 페이지를 표시합니다. 
4. 이 새 페이지에서 **+ 추가**를 클릭하여 **계산** 블레이드를 표시합니다.  초기 화면에 VM 유형이 표시되지 않으면 다음과 같이 기본 VM의 이름을 검색하면 됩니다.

    ![새 VM의 계산 블레이드](./media/publishvm_014.png)

5. 적절한 가상 이미지를 선택한 후 다음 값을 입력합니다.
   * **기본 사항** 블레이드에서 가상 머신에 대한 **이름**을 1-15자의 영숫자로 입력합니다. (이 예에서는 `DemoVm009`를 사용합니다.)
   * VM에서 로컬 계정을 만드는 데 사용되는 **사용자 이름**과 강력한 **암호**를 입력합니다.  (여기서는 `adminUser`가 사용됩니다.)  암호는 8-123자 길이여야 하며 1개의 소문자, 1개의 대문자, 1개의 숫자 및 1개의 특수 문자 등 네 가지 복잡성 요구 사항 중 적어도 세 가지를 충족해야 합니다. 자세한 내용은 [사용자 이름 및 암호 요구 사항](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm)을 참조하세요.
   * 만든 리소스 그룹을 선택합니다(여기서는 `DemoResourceGroup`).
   * Azure 데이터 센터 **위치**를 선택합니다(여기서는 `West US`).
   * **확인**을 클릭하여 이러한 값을 저장합니다. 

6. 다음 권장 사항을 사용하여 배포할 VM의 크기를 선택합니다.
   * 온-프레미스에서 VHD를 개발하려는 경우 크기는 중요하지 않습니다. 더 작은 VM 중 하나를 사용하는 것이 좋습니다.
   * Azure에서 이미지를 개발하려는 경우 선택된 이미지에 대한 권장 VM 크기 중 하나를 사용하는 것이 좋습니다.
   * 가격 책정에 대한 자세한 내용은 포털에 표시되는 **권장 가격 책정 계층** 선택기를 참조하세요. 게시자가 제공한 세 가지 권장 크기가 표시됩니다. (여기서 게시자는 Microsoft입니다.)

   ![새 VM의 크기 블레이드](./media/publishvm_015.png)

7. **설정** 블레이드에서 **관리 디스크 사용** 옵션을 **아니요**로 설정합니다.  이렇게 하면 새 VHD를 수동으로 관리할 수 있습니다. (**설정** 블레이드를 사용하면 **디스크 유형**에서 **프리미엄(SSD)** 을 선택하는 것처럼 다른 저장소 및 네트워크 옵션을 변경할 수 있습니다.)  **확인** 을 클릭하여 계속합니다.

    ![새 VM의 설정 블레이드](./media/publishvm_016.png)

8. **요약** 을 클릭하여 선택 사항을 검토합니다. **유효성 검사 통과함** 메시지가 표시되면 **확인**을 클릭합니다.

    ![새 VM의 요약 블레이드](./media/publishvm_017.png)

Azure에서 지정한 가상 머신의 프로비전을 시작합니다.  왼쪽의 **Virtual Machines** 탭을 클릭하여 진행 상황을 추적할 수 있습니다.  VM이 만들어지면 상태가 **실행 중**으로 변경됩니다.  이제 [가상 머신에 연결](./cpp-connect-vm.md)할 수 있습니다.


## <a name="next-steps"></a>다음 단계

새 Azure 기반 VHD를 만드는 데 어려움이 있는 경우 [일반적인 VHD 만들기 문제](./cpp-common-vhd-creation-issues.md)를 참조하세요.  그렇지 않으면, 다음으로 Azure에서 만든 [VM에 연결](./cpp-connect-vm.md)해야 합니다. 
