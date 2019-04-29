---
title: Linux Azure Virtual Machines의 SQL Server FAQ | Microsoft Docs
description: 이 문서는 Linux Azure VM에서 SQL Server를 실행하는 방법에 대한 질문과 대답을 제공합니다.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: fb1cafcf9405576749ea91aeea033c6ee783a026
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739270"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-azure-virtual-machines"></a>Linux Azure Virtual Machines의 SQL Server에 대한 FAQ(질문과 대답)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](sql-server-linux-faq.md)

이 문서에서는 [Linux Azure Virtual Machines에서 SQL Server 실행](sql-server-linux-virtual-machines-overview.md)에 대해 가장 일반적인 질문 중 몇 가지에 대한 답변을 제공합니다.

> [!NOTE]
> 이 문서에서는 Linux VM의 SQL Server에 관련된 문제에 초점을 맞춥니다. Windows VM에서 SQL Server를 실행 하는 경우 [Windows FAQ](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)를 참조하세요.

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a id="images"></a>이미지

1. **사용 가능한 SQL Server 가상 머신 갤러리 이미지는 무엇인가요?**

   Azure는 모든 Linux 및 Windows용 버전의 SQL Server의 지원되는 모든 주요 릴리스에서 가상 머신 이미지를 유지 관리합니다. 자세한 내용은 [Linux VM 이미지](sql-server-linux-virtual-machines-overview.md#create) 및 [Windows VM 이미지](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)의 전체 목록은 참조하세요.

1. **기존 SQL Server 가상 머신 갤러리 이미지가 업데이트되나요?**

   2개월마다 가상 머신 갤러리의 SQL Server 이미지는 최신 Linux 및 Windows 업데이트로 업데이트됩니다. Linux 이미지의 경우 최신 시스템 업데이트가 포함됩니다. Windows 이미지의 경우 중요한 SQL Server 보안 업데이트 및 서비스 팩을 비롯한 Windows 업데이트에서 중요하다고 표시된 모든 업데이트가 포함됩니다. SQL Server 누적 업데이트는 Linux 및 Windows에서 다르게 처리됩니다. Linux의 경우 SQL Server 누적 업데이트는 새로 고침에도 포함됩니다. 하지만 이번에 Windows VM은 SQL Server 또는 Windows Server 누적 업데이트로 업데이트되지 않습니다.

1. **함께 설치되는 관련 SQL Server 패키지는 무엇인가요?**

   SQL Server Linux VM에 기본적으로 설치되는 SQL Server 패키지를 보려면 [설치된 패키지](sql-server-linux-virtual-machines-overview.md#packages)를 참조하세요.

1. **SQL Server 가상 머신 이미지가 갤러리에서 제거될 수 있나요?**

   예. Azure에서는 주 버전당 하나의 이미지를 유지 관리합니다. 예를 들어 새 SQL Server 서비스 팩이 출시되면 Azure에서는 해당 서비스 팩의 갤러리에 새 이미지를 추가합니다. 이전 서비스 팩의 SQL Server 이미지는 Azure Portal에서 즉시 제거됩니다. 그러나 다음 3개월 동안은 PowerShell에서 프로비전할 수 있습니다. 3개월 후에는 이전 서비스 팩 이미지를 사용할 수 없습니다. 해당 수명 끝에 도달하면 이 제거 정책이 SQL Server 버전이 지원되지 않는 경우에 적용됩니다.

## <a name="creation"></a>만들기

1. **SQL Server를 사용하여 Linux Azure 가상 머신을 만들려면 어떻게 해야 합니까?**

   가장 쉬운 해결 방법은 SQL Server가 포함된 Linux Virtual Machine을 만드는 것입니다. Azure에 등록하고 포털에서 SQL VM을 만드는 방법에 대한 자습서는 [Azure Portal에 Linux SQL Server 가상 머신 프로비전](provision-sql-server-linux-virtual-machine.md)을 참조하세요. 무료 라이선스 버전(Developer 또는 Express)을 사용하거나 온-프레미스 라이선스를 재사용하여 VM에 SQL Server를 수동으로 설치할 수도 있습니다. 사용자 라이선스가 필요하면 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility)이 가능해야 합니다.

1. **지출 한도가 있는 Azure 구독에서 RHEL 또는 SLES SQL Server VM을 프로비전하지 못하는 이유는 무엇인가요?**

   RHEL 및 SLES 가상 머신에는 지출 한도가 없는 구독과, 구독에 연결된 검증된 결제 수단(보통은 신용카드)가 필요합니다. 지출 한도를 제거하지 않고 RHEL 또는 SLES VM을 프로비전하면 구독이 비활성화되고 모든 VM/서비스가 중지됩니다. 이 상태가 되어 구독을 다시 활성화하려면 [지출 한도를 제거](https://account.windowsazure.com/subscriptions)합니다. 현재 청구 주기에 대해 남은 크레딧이 복원되지만 다시 시작하여 실행을 계속하도록 선택하면 RHEL 또는 SLES VM 이미지 추가 요금이 해당 신용카드로 청구됩니다.

## <a name="licensing"></a>라이선스

1. **Azure VM에 라이선스가 있는 내 SQL Server 사본을 설치하려면 어떻게 해야 합니까?**

   먼저 Linux OS 전용 가상 컴퓨터를 만듭니다. 그런 다음 Linux 배포에 대한 [SQL Server 설치 단계](https://docs.microsoft.com/sql/linux/sql-server-linux-setup#platforms)를 실행합니다. SQL Server의 무료 라이선스 버전 중 하나를 설치하지 않은 한 SQL Server 라이선스와 [Azure에서 Software Assurance를 통한 라이선스 이동](https://azure.microsoft.com/pricing/license-mobility/)이 필요합니다.

1. **SQL Server에 대한 BYOL(사용자 라이선스 필요) Linux 가상 컴퓨터 이미지가 있나요?**

   현재 SQL Server에 대한 BYOL(사용자 라이선스 필요) Linux 가상 머신 이미지는 없습니다. 그러나 앞의 질문에서 설명한 것처럼 Linux 전용 VM에 SQL Server를 수동으로 설치할 수 있습니다.

1. **종량제 갤러리 이미지 중 하나에서 만들어진 경우 사용자 고유의 SQL Server 라이선스를 사용하도록 VM을 변경할 수 있나요?**

   아니요. 초 단위로 요금이 부과되는 라이선스에서 사용자 라이선스 사용으로 전환할 수 없습니다. 새로운 Linux VM을 만들고, SQL Server를 설치하고, 데이터를 마이그레이션해야 합니다. 사용자 고유 라이선스를 가져오기에 대한 자세한 내용은 이전 질문을 참조하세요.

## <a name="administration"></a>관리

1. **SSMS(SQL Server Management Studio)를 사용하여 Linux SQL Server 가상 머신을 관리할 수 있나요?**

   예, 하지만 SSMS는 현재 Windows 전용 도구입니다. Linux SQL Server VM에서 SSMS를 사용하도록 Windows 컴퓨터에서 원격으로 연결해야 합니다. 새 [mssql-conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf) 도구는 Linux에서 로컬로 다양한 관리 작업을 수행할 수 있습니다. 플랫폼 간 데이터베이스 관리 도구는 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is)를 참조하세요.

1. **SQL VM에서 SQL Server를 완전히 제거할 수 있나요?**

   예, 그러나 [SQL Server Azure VM에 대한 가격 책정 지침](../../windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json)에 설명된 대로 SQL VM에 대한 비용 청구가 계속됩니다. SQL Server가 더 이상 필요하지 않는 경우 새 가상 컴퓨터를 배포하고 데이터와 애플리케이션을 새 가상 컴퓨터에 마이그레이션할 수 있습니다. 그런 다음 SQL Server 가상 머신을 제거할 수 있습니다.

## <a name="updating-and-patching"></a>업데이트 및 패치

1. **Azure VM에서 SQL Server의 새 버전을 업그레이드하려면 어떻게 합니까?**

   현재는 Azure VM에서 실행 중인 SQL Server를 전체 업그레이드할 수 없습니다. 원하는 SQL Server 버전으로 새 Azure 가상 머신을 만든 다음 표준 [데이터 마이그레이션 기법](https://docs.microsoft.com/sql/linux/sql-server-linux-migrate-overview)을 사용하여 데이터베이스를 새 서버로 마이그레이션합니다.

## <a name="general"></a>일반

1. **Azure VM에서 SQL Server 고가용성 솔루션이 지원되나요?**

   지금은 없습니다. Always On 가용성 그룹과 장애 조치(failover) 클러스터링에는 모두 Pacemaker 같은 Linux의 클러스터링 솔루션이 필요합니다. SQL Server에 대해 지원되는 Linux 배포는 클라우드에서 고가용성 추가 기능을 지원하지 않습니다.

## <a name="resources"></a>리소스

**Linux VM**:

* [Linux VM의 SQL Server 개요](sql-server-linux-virtual-machines-overview.md)
* [SQL Server Linux VM 프로비전](provision-sql-server-linux-virtual-machine.md)
* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)

**Windows VM**:

* [Windows VM에서 SQL Server 개요](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server Windows VM 프로비전](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md)
* [FAQ(Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-faq.md)