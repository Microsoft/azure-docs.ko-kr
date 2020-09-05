---
title: SQL Server 버전의 현재 위치 변경
description: Azure에서 SQL Server 가상 머신의 버전을 다운 그레이드로 변경 하 여 비용을 줄이거나 업그레이드 하 여 더 많은 기능을 사용할 수 있도록 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 60020667126fe76d33264795ddb2e17e1241a7b2
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236287"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM에서 SQL Server 버전의 현재 위치 변경
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure의 Windows 가상 머신에서 SQL Server의 버전을 변경하는 방법을 설명합니다. 

SQL Server 버전은 제품 키로 결정되며 설치 과정에서 설치 미디어를 사용하여 지정됩니다. 버전은 SQL Server 제품에서 사용할 수 있는 [기능](/sql/sql-server/editions-and-components-of-sql-server-2017)을 결정합니다. 설치 미디어를 사용하여 SQL Server 버전을 변경하면 다운그레이드하여 비용을 줄이거나 업그레이드하여 더 많은 기능을 활성화할 수 있습니다.

SQL Server 버전이 SQL Server VM 내부적으로 변경된 후에는 청구를 위해 Azure Portal에서 SQL Server의 버전 속성을 업데이트해야 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

SQL Server 버전의 현재 위치 변경을 수행하려면 다음이 필요합니다. 

- [Azure 구독](https://azure.microsoft.com/free/).
- [SQL VM 리소스 공급자](sql-vm-resource-provider-register.md)에 등록된 [Windows의 SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision).
- **원하는 버전**의 SQL Server를 포함하는 설치 미디어. [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)가 있는 고객은 [볼륨 라이선싱 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 구할 수 있습니다. Software Assurance가 없는 고객은 Azure Marketplace SQL Server VM 이미지에서 원하는 버전을 포함하는 설치 미디어를 사용할 수 있습니다(일반적으로 `C:\SQLServerFull`에 있음). 


## <a name="upgrade-an-edition"></a>버전 업그레이드

> [!WARNING]
> SQL Server 버전을 업그레이드하면 Analysis Services 및 R Services와 같은 관련 서비스와 함께 SQL Server용 서비스가 다시 시작합니다. 

SQL Server 버전을 업그레이드하려면 원하는 SQL Server 버전의 SQL Server 설치 미디어를 구한 후 다음을 수행합니다.

1. SQL Server 설치 미디어에서 Setup.exe를 엽니다. 
1. **유지 관리**로 이동하여 **버전 업그레이드** 옵션을 선택합니다. 

   ![SQL Server 버전을 업그레이드하기 위한 선택 항목](./media/change-sql-server-edition/edition-upgrade.png)

1. **다음**을 계속 선택하여 **버전 업그레이드 준비 완료** 페이지가 나오면 **업그레이드**를 선택합니다. 변경 내용이 적용되는 동안 설치 창이 몇 분 동안 응답하지 않을 수 있습니다. **완료** 페이지에서 버전 업그레이드가 완료되었음을 확인할 수 있습니다. 

SQL Server 버전을 업그레이드한 후 Azure Portal에서 SQL Server 가상 머신의 버전 속성을 수정합니다. 그러면 이 VM과 연결된 메타데이터 및 청구가 업데이트됩니다.

## <a name="downgrade-an-edition"></a>버전 다운그레이드

SQL Server 버전을 다운그레이드하려면 SQL Server를 완전히 제거하고 원하는 버전의 설치 미디어를 사용하여 다시 설치해야 합니다. 

> [!WARNING]
> SQL Server를 제거하면 추가 가동 중지 시간이 발생할 수 있습니다. 

다음 단계를 수행하여 SQL Server 버전을 다운그레이드할 수 있습니다.

1. 시스템 데이터베이스를 포함하여 모든 데이터베이스를 백업합니다. 
1. 시스템 데이터베이스(마스터, 모델 및 msdb)를 새 위치로 이동합니다. 
1. SQL Server와 모든 연결된 서비스를 완전히 제거합니다. 
1. 가상 머신을 다시 시작합니다. 
1. 원하는 SQL Server 버전의 미디어를 사용하여 SQL Server를 설치합니다.
1. 최신 서비스 팩과 누적 업데이트를 설치합니다.  
1. 설치하는 동안 생성된 새 시스템 데이터베이스를 앞서 다른 위치로 이동한 시스템 데이터베이스로 바꿉니다. 

SQL Server 버전을 다운그레이드한 후 Azure Portal에서 SQL Server 가상 머신의 버전 속성을 수정합니다. 그러면 이 VM과 연결된 메타데이터 및 청구가 업데이트됩니다.

## <a name="change-edition-in-portal"></a>포털에서 버전 변경 

설치 미디어를 사용하여 SQL Server 버전을 변경하고 SQL Server VM을 [SQL VM 리소스 공급자](sql-vm-resource-provider-register.md)에 등록한 후에는 Azure Portal을 사용하여 청구를 위해 SQL Server VM의 버전 속성을 수정할 수 있습니다. 이렇게 하려면 다음 단계를 따르십시오. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 
1. SQL Server 가상 머신 리소스로 이동합니다. 
1. **설정**에서 **구성**을 선택합니다. 그런 다음 **버전** 아래의 드롭다운 목록에서 원하는 SQL Server 버전을 선택합니다. 

   ![버전 메타데이터 변경](./media/change-sql-server-edition/edition-change-in-portal.png)

1. 먼저 SQL Server 버전을 변경해야 한다는 경고와 버전 속성이 SQL Server 버전과 일치해야 한다는 버전을 검토합니다. 
1. **적용**을 선택하여 버전 메타데이터 변경 내용을 적용합니다. 


## <a name="remarks"></a>설명

- SQL Server VM에 대한 버전 속성은 종량제 및 사용자 라이선스 필요 라이선스 유형을 포함하여 모든 SQL Server 가상 머신에 설치된 SQL Server 인스턴스의 버전과 일치해야 합니다.
- SQL Server VM 리소스를 삭제하면 이미지의 하드 코드된 버전 설정으로 돌아갑니다.
- 버전을 변경하는 기능은 SQL VM 리소스 공급자의 기능입니다. Azure Portal을 통해 Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 리소스 공급자에 등록됩니다. 그러나 SQL Server를 자체 설치하는 고객은 수동으로 [SQL Server VM을 등록](sql-vm-resource-provider-register.md)해야 합니다.
- 가용성 집합에 SQL Server VM을 추가하려면 VM을 다시 만들어야 합니다. 가용성 집합에 추가된 VM은 모두 기본 버전으로 돌아가며 버전을 다시 수정해야 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](doc-changes-updates-release-notes.md)


