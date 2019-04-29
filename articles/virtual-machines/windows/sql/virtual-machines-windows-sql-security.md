---
title: Azure의 SQL Server에 대한 보안 고려 사항 | Microsoft Docs
description: 이 항목에서는 Azure Virtual Machine에서 실행되는 SQL Server 보안에 대한 일반적인 지침을 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 69b6bd07699d179fc87ac6c5364a7a34b23d14eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477552"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항

이 항목에는 Azure VM(Virtual Machine)에서 SQL Server 인스턴스로의 보안 액세스를 설정하는 데 도움이 되는 전반적인 보안 지침이 포함되어 있습니다.

Azure는 가상 머신에서 실행되는 SQL Server로 호환되는 솔루션을 제작할 수 있도록 하는 몇 가지 산업 규정 및 표준을 준수합니다. Azure 규정 준수에 대한 자세한 내용은 [Azure 보안 센터](https://azure.microsoft.com/support/trust-center/)를 참조하세요.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>SQL VM에 대한 액세스 제어

SQL Server 가상 컴퓨터를 만들 때는 컴퓨터 및 SQL Server에 대한 액세스 권한을 갖는 사용자를 신중하게 제어하는 방법을 고려합니다. 일반적으로 다음과 같이 해야 합니다.

- SQL Server에 대한 액세스 권한을 이러한 권한이 필요한 애플리케이션 및 클라이언트로만 제한합니다.
- 사용자 계정 및 암호를 관리하는 모범 사례를 따릅니다.

다음 섹션에서는 이러한 내용을 충분히 생각해 보도록 제안 사항을 제공합니다.

## <a name="secure-connections"></a>보안 연결

갤러리 이미지를 사용하여 SQL Server 가상 머신을 만들 때 **SQL Server 연결** 옵션은 **로컬(VM 내부)**, **개인(Virtual Network 내)** 또는 **공용(인터넷)** 중에서 선택할 수 있게 해줍니다.

![SQL Server 연결](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

최상의 보안을 위해 해당 시나리오에 대해 가장 제한적인 옵션을 선택합니다. 예를 들어 같은 VM에 있는 SQL Server에 액세스하는 애플리케이션을 실행 중인 경우 **로컬**이 가장 안전한 선택 사항입니다. SQL Server에 액세스해야 하는 Azure 애플리케이션을 실행 중인 경우 **개인**은 지정된 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md) 내에 있는 SQL Server로의 통신만 보호합니다. SQL Server VM에 대한 **공용**(인터넷) 액세스가 필요한 경우 이 항목의 모범 사례를 따라 공격 노출 영역을 줄이도록 합니다.

포털에서 선택한 옵션은 VM NSG([네트워크 보안 그룹 ](../../../virtual-network/security-overview.md))에 대해 인바운드 보안 규칙을 사용하여 가상 머신에 대한 네트워크 트래픽을 허용하거나 거부합니다. SQL Server 포트(기본값 1433)에 대한 트래픽을 허용하도록 인바운드 NSG 규칙을 수정하거나 새 인바운드 NSG 규칙을 만들 수 있습니다. 이 포트를 통해 통신할 수 있는 특정 IP 주소를 지정할 수도 있습니다.

![네트워크 보안 그룹 규칙](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

네트워크 트래픽을 제한하기 위한 NSG 규칙 외에 가상 컴퓨터에서 Windows 방화벽을 사용할 수도 있습니다.

클래식 배포 모델이 적용된 엔드포인트를 사용하는 경우 사용하지 않는 모든 엔드포인트를 가상 머신에서 제거합니다. 엔드포인트에서 ACL을 사용하는 방법에 대한 지침은 [엔드포인트에 대한 ACL 관리](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)를 참조하세요. Resource Manager를 사용하는 VM에는 이렇게 할 필요가 없습니다.

마지막으로, Azure Virtual Machine에서 SQL Server 데이터베이스 엔진의 인스턴스에 대해 암호화된 연결 사용을 고려합니다. 서명된 인증서로 SQL server 인스턴스를 구성합니다. 자세한 내용은 [데이터베이스 엔진에 암호화된 연결 사용](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) 및 [연결 문자열 구문](https://msdn.microsoft.com/library/ms254500.aspx)을 참조하세요.

## <a name="use-a-non-default-port"></a>기본 포트가 아닌 포트 사용

기본적으로 SQL Server는 잘 알려진 포트 1433에서 수신 대기합니다. 보안 강화를 위해 기본 포트가 아닌 포트(예: 1401)에서 수신 대기하도록 SQL Server를 구성합니다. Azure Portal에서 SQL Server 갤러리 이미지를 프로비전하는 경우 **SQL Server 설정** 블레이드에서 이 포트를 지정할 수 있습니다.

프로비전한 후 이를 구성할 때 다음과 같은 두 가지 옵션이 있습니다.

- Resource Manager VM의 경우 VM 개요 블레이드에서 **SQL Server 구성**을 선택할 수 있습니다. 그러면 포트를 변경하는 옵션이 제공됩니다.

  ![포털에서 TCP 포트 변경](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- 포털에서 프로비전되지 않은 클래식 VM 또는 SQL Server VM의 경우 원격으로 VM에 연결하여 포트를 수동으로 구성할 수 있습니다. 구성 단계는 [Configure a Server to Listen on a Specific TCP Port](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port)(특정 TCP 포트에서 수신 대기하도록 서버 구성)를 참조하세요. 이 수동 기법을 사용하는 경우 해당 TCP 포트에서 들어오는 트래픽을 허용하도록 Windows 방화벽 규칙도 추가해야 합니다.

> [!IMPORTANT]
> SQL Server 포트가 공용 인터넷 연결에 대해 열려 있는 경우 기본 포트가 아닌 포트를 지정하는 것은 좋은 생각입니다.

SQL Server가 기본 포트가 아닌 포트에서 수신 대기하는 경우 연결할 때 포트를 지정해야 합니다. 예를 들어 서버 IP 주소가 13.55.255.255이고 SQL Server가 포트 1401에서 수신 대기하고 있는 시나리오를 고려합니다. SQL Server에 연결하려면 연결 문자열에 `13.55.255.255,1401`을 지정합니다.

## <a name="manage-accounts"></a>계정 관리

공격자가 쉽게 계정 이름이나 암호를 추측하기를 원하지는 않을 것입니다. 도움이 되도록 다음 팁을 사용하세요.

- 로컬 관리자 계정의 이름을 **Administrator**가 아닌 다른 이름으로 만듭니다.

- 모든 계정에 복잡하고 강력한 암호를 사용합니다. 강력한 암호를 만드는 방법에 대한 자세한 내용은 [강력한 암호 만들기](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) 문서를 참조하세요.

- 기본적으로 Azure는 SQL Server Virtual Machine을 설치하는 동안 Windows 인증을 선택합니다. 따라서 **SA** 로그인이 사용하지 않도록 설정되고 설치 프로그램에서 암호를 할당합니다. **SA** 로그인을 사용하거나 사용하도록 설정하지 않는 것이 좋습니다. SQL 로그인이 있어야 하는 경우 다음 전략 중 하나를 사용합니다.

  - **sysadmin** 멤버 자격이 있는 고유한 이름을 가진 SQL 계정을 만듭니다. 포털에서 프로비전하는 동안 **SQL 인증**을 사용하도록 설정하여 이렇게 할 수 있습니다.

    > [!TIP] 
    > 프로비전하는 동안 SQL 인증을 사용하도록 설정하지 않으면 인증 모드를 수동으로 **SQL Server 및 Windows 인증 모드**로 변경해야 합니다. 자세한 내용은 [서버 인증 모드 변경](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode)을 참조하세요.

  - **SA** 로그인을 사용해야 하는 경우 프로비전한 후 로그인을 사용하도록 설정하고 새로운 강력한 암호를 할당합니다.

## <a name="follow-on-premises-best-practices"></a>온-프레미스 모범 사례 따르기

이 항목에서 설명하는 모범 사례 외에 해당하는 경우 기존의 온-프레미스 보안 방법을 검토하고 구현하는 것이 좋습니다. 자세한 내용은 [SQL Server 설치에 대한 보안 고려 사항](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)을 참조하세요.

## <a name="next-steps"></a>다음 단계

성능에 대한 모범 사례에도 관심이 있으면 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 참조하세요.

Azure VM에서 SQL Server 실행과 관련된 다른 항목은 [Azure Virtual Machines의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](virtual-machines-windows-sql-server-iaas-faq.md)을 참조하세요.

