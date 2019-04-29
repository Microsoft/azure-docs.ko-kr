---
title: Azure Virtual Machines의 SQL Server에 대한 효과적인 비용 관리 | Microsoft 문서
description: 적합한 SQL Server 가상 머신 가격 책정 모델을 선택하기 위한 모범 사례를 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 19ba6896d9186cae32249fc8eb51319f9b37a6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477246"
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>SQL Server Azure VM에 대한 가격 책정 지침

이 문서에서는 Azure의 [SQL Server 가상 머신](virtual-machines-windows-sql-server-iaas-overview.md)에 대한 가격 책정 지침을 제공합니다. 비용에 영향을 미치는 다양한 옵션이 있고 비용과 비즈니스 요구 사항 간에 균형을 이루는 적합한 이미지를 선택해야 합니다.

> [!TIP]
> 특정한 SQL Server 버전과 가상 머신 크기 조합에 대한 예상 비용만 확인하려는 경우 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) 또는 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux)에 대한 가격 책정 페이지를 참조하세요. **OS/소프트웨어** 목록에서 플랫폼과 SQL Server 버전을 선택합니다.
>
> ![VM 가격 책정 페이지의 UI](./media/virtual-machines-windows-sql-server-pricing-guidance/virtual-machines-pricing-ui.png)
>
> 또는 [가격 계산기](https://azure.microsoft.com/pricing/#explore-cost)를 사용하여 가상 머신을 추가하고 구성합니다. 

## <a name="free-licensed-sql-server-editions"></a>무료 라이선스 SQL Server 버전

개념 증명을 개발, 테스트 또는 빌드하려면 무료 라이선스 **SQL Server Developer Edition**을 사용합니다. 이 버전에는 SQL Server Enterprise 버전의 모든 기능이 있어, 모든 유형의 애플리케이션을 빌드하고 테스트할 수 있습니다. 그러나 Developer 버전은 프로덕션에서는 실행할 수 없습니다. SQL Server Developer 버전 VM은 연결된 SQL Server 라이선스 비용이 없으므로 VM에 대해서만 비용이 발생합니다.

프로덕션에서 간단한 워크로드를 실행하려면(4개 코어 미만, 메모리 1GB 미만, 10GB/데이터베이스 미만) 자유롭게 사용이 허가된 **SQL Server Express Edition**을 사용하세요. SQL Server Express 버전 VM 또한 VM에 대해서만 비용 발생합니다.

이러한 개발/테스트 또는 간단한 프로덕션 작업의 경우 이러한 작업에 맞는 더 작은 VM 크기를 선택하여 비용을 절감할 수도 있습니다. 일부 시나리오에는 DS1v2를 선택하는 것이 좋습니다.

이러한 이미지 중 하나를 사용하여 SQL Server 2017 Azure VM을 만들려면 다음 링크를 참조하세요.

| 플랫폼 | 자유롭게 사용이 허가된 이미지 |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Developer Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[SQL Server 2017 Express Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>유료 SQL Server 버전

복잡한 프로덕션 작업이 있는 경우 다음 SQL Server 버전 중 하나를 사용합니다.

| SQL Server 버전 | 워크로드 |
|-----|-----|
| 웹 | 작은 웹 사이트 |
| Standard | 소규모~중간 규모 작업 |
| Enterprise | 대규모 또는 중요 업무용 작업|

이러한 버전의 SQL Server 라이선스 요금을 지급하는 두 가지 옵션은 *사용당 지급* 또는 *사용자 라이선스 필요(BYOL)* 입니다.

## <a name="pay-per-usage"></a>사용당 지급

**사용당 SQL Server 라이선스 지급**은 Azure VM의 초당 비용에 SQL Server 라이선스 비용이 포함됨을 의미합니다. [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) 또는 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux)에 대한 Azure VM 가격 페이지에서 다른 SQL Server 버전(Web, Standard, Enterprise)에 대한 가격을 확인할 수 있습니다.

SQL Server의 모든 버전(2012 SP3 ~ 2017)에 대한 비용은 동일합니다. 초당 라이선싱 비용은 VM vCPU 수에 따라 달라집니다.

사용당 SQL Server 라이선스 지급이 권장되는 경우는 다음과 같습니다.

- **임시 또는 정기적인 작업**. 예: 매주 월요일의 비즈니스 분석 또는 1년에 몇 개월 동안 이벤트를 지원해야 하는 앱.

- **수명 또는 규모를 알 수 없는 작업**. 예: 몇 개월 내에 필요하지 않을 수 있거나 수요에 따라 더 많은 또는 더 적은 계산 기능이 필요할 수 있는 앱.

이러한 사용당 지급 이미지 중 하나를 사용하여 SQL Server 2017 Azure VM을 만들려면 다음 링크를 참조하세요.

| 플랫폼 | 사용이 허가된 이미지 |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> 포털에서 SQL Server 가상 머신을 만들 때 **크기 선택** 창에 예상 비용이 표시됩니다. 이 추정치는 Windows 또는 타사 Linux 운영 체제의 모든 OS 라이선싱 비용과 함께 VM을 실행하는 계산 비용만 산출한 것입니다.
>
> ![VM 크기 선택 블레이드](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>여기에는 Web, Standard 및 Enterprise Edition에 대한 추가 SQL Server 라이선싱 비용은 포함되지 않았습니다. 가장 정확한 가격 책정 예상 값을 얻으려면 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 또는 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 대한 가격 책정 페이지에서 운영 체제와 SQL Server 버전을 선택합니다.

> [!NOTE]
> 이제 라이선스 모델을 종량제 모델과 BYOL(사용자 라이선스 필요) 간에 변경할 수 있습니다. 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. 

## <a id="byol"></a> BYOL(사용자 라이선스 필요)

**License Mobility를 통해 SQL Server 사용자 라이선스 필요**(**BYOL**이라고도 함)는 Azure VM에서 기존 SQL Server 볼륨 라이선스와 함께 Software Assurance를 사용함을 의미합니다. BYOL을 사용하는 SQL Server VM의 경우 볼륨 라이선싱 프로그램을 통해 이미 라이선스 및 Software Assurance를 구매했다면 SQL Server 라이선스가 아닌 VM 실행에 대한 비용만 청구됩니다.

> [!IMPORTANT]
> BYOL 이미지에는 Software Assurance를 통한 기업 계약이 필요합니다. 현재 BYOL 이미지는 Azure CSP(클라우드 솔루션 파트너)의 일부로 사용할 수 없습니다.

> [!NOTE]
> BYOL 이미지는 현재 Windows 가상 머신에만 사용할 수 있습니다. 그러나 Linux 전용 VM에 SQL Server를 수동으로 설치할 수 있습니다. 지침은 [Linux SQL VM FAQ](../../linux/sql/sql-server-linux-faq.md)를 참조하세요.

License Mobility를 통한 SQL 사용자 라이선스 필요가 권장되는 경우는 다음과 같습니다.

- **연속 작업**. 예: 연중무휴 비즈니스 작업을 지원해야 하는 앱.

- **수명 또는 규모가 알려진 작업**. 예: 1년 내내 필요하고 수요가 예측된 앱.

BYOL과 함께 SQL Server VM을 사용하려면 SQL Server Standard 또는 Enterprise에 대한 라이선스와 함께 일부 볼륨 라이선싱 프로그램을 통한 필수 옵션이자 다른 프로그램에 대한 선택적 구매인 [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1)가 있어야 합니다. 볼륨 라이선싱을 통해 제공된 가격 수준은 SQL Server에 대한 계약 유형과 수량 및/또는 약정에 따라 달라집니다. 하지만 일반적으로 연속 프로덕션 작업에 대한 사용자 라이선스 필요에는 다음과 같은 이점이 있습니다.

| BYOL 이점 | 설명 |
|-----|-----|
| **비용 절감** | 작업에서 SQL Server Standard 또는 Enterprise를 *10개월 넘게* 연속적으로 실행할 경우 SQL Server 사용자 라이선스 필요가 사용당 지급보다 더 비용 효과적입니다. |
| **장기적 절감** | 처음 3년에 해당하는 SQL Server 라이선스를 구매하거나 갱신하는 것이 평균적으로 *연간 30% 더 저렴*합니다. 또한 3년 후에 더 이상 라이선스를 갱신할 필요가 없고 Software Assurance 요금만 지급하면 됩니다. 이 시점에는 *200% 더 저렴*합니다. |
| **무료 수동 보조 복제본** | 사용자 라이선스 필요의 또 다른 이점은 고가용성을 위해 SQL Server당 [하나의 수동 보조 복제본에 무료 라이선스가 제공](https://azure.microsoft.com/pricing/licensing-faq/)된다는 것입니다. 이에 따라 가용성이 높은 SQL Server 배포(예: Always On 가용성 그룹 사용)의 라이선스 비용이 50% 절감됩니다. 수동 보조 복제본을 실행할 권한은 장애 조치(failover) 서버 Software Assurance 이점을 통해 제공됩니다. |

이러한 사용자 라이선스 필요 이미지 중 하나를 사용하여 SQL Server 2017 Azure VM을 만들려면 접두사 “{BYOL}”이 추가된 VM을 참조하세요.

- [SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [SQL Server 2017 Standard Azure VM](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Azure에서 사용할 SQL Server 라이선스 수를 10일 이내에 알려주세요. 이전 이미지의 링크에는 이 작업을 수행하는 방법에 대한 지침이 있습니다.

> [!NOTE]
> 이제 라이선스 모델을 종량제 모델과 BYOL(사용자 라이선스 필요) 간에 변경할 수 있습니다. 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. 



## <a name="reduce-costs"></a>비용 절감

불필요 한 비용을 방지하기 위해 최적의 가상 컴퓨터 규모를 선택하고 비연속 워크로드에 대해서는 일시적인 종료를 고려합니다.

### <a id="machinesize"></a>정확한 VM 크기 

SQL Server 라이선싱 비용은 vCPU 수와 직접적인 관련이 있습니다. 예상되는 CPU, 메모리, 저장소 및 I/O 대역폭에 부합하는 VM 크기를 선택합니다. 전체 컴퓨터 크기 옵션 목록은 [Windows VM 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) 및 [Linux VM 크기](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)를 참조하세요.

특정 유형의 SQL Server 워크로드에 잘 맞는 새로운 컴퓨터 크기가 있습니다. 이러한 컴퓨터 크기는 높은 수준의 메모리, 저장소 및 I/O 대역폭을 유지하지만 가상화된 코어 수는 더 적습니다. 예를 들어 다음 예제를 고려해 보겠습니다.

| VM 크기 | vCPU | 메모리 | 최대 디스크 수 | 최대 I/O 처리량 | SQL 라이선싱 비용 | 총 비용(계산 + 라이선싱) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112GB | 32 | 51,200 IOPS 또는 768MB/s | | |
| **Standard_DS14-4v2** | 4 | 112GB | 32 | 51,200 IOPS 또는 768MB/s | 75% 더 낮음 | 57% 더 낮음 |

> [!IMPORTANT]
> 지정 시간 예제입니다. 최신 사양은 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 및 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 대한 Azure 가격 책정 페이지와 컴퓨터 크기 문서를 참조하세요.

이전 예제에서는 **Standard_DS14v2** 및 **Standard_DS14-4v2**에 대한 사양이 vCPU를 제외하고 동일한 것을 확인할 수 있습니다. **Standard_DS14-4v2** 컴퓨터 크기 끝의 접미사 **-4v2**는 활성 vCPU 수를 표시합니다. SQL Server 라이선싱 비용은 vCPU 수와 연결되어 있으므로 추가 vCPU가 필요하지 않은 시나리오에서 VM 비용을 크게 줄일 수 있습니다. 이것이 한 예이며 이 접미사 패턴과 동일한 제약된 vCPU 수를 갖는 여러 컴퓨터 크기가 있습니다. 자세한 내용은 [더 비용 효과적인 데이터베이스 작업을 위한 새 Azure VM 크기 발표](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/) 블로그 게시물을 참조하세요.

### <a name="shut-down-your-vm-when-possible"></a>가능한 경우 VM 종료

연속적으로 실행되지 않는 작업을 사용 중이면 비활성화된 동안 가상 머신을 종료하는 것이 좋습니다. 사용한 양만큼만 요금을 지급합니다.

예를 들어 Azure VM에서 SQL Server를 사용해 보려는 경우에는 실수로 몇 주 동안 계속 실행함으로써 요금이 발생하는 것을 원하지 않을 것입니다. 한 가지 해결 방법은 [automatic shutdown feature](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/)(자동 종료 기능)를 사용하는 것입니다.

![SQL VM 자동 종료](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

자동 종료는 [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab)에서 제공되는 광범위한 유사 기능 집합에 포함됩니다.

다른 워크플로의 경우 [Azure Automation](https://azure.microsoft.com/services/automation/) 등의 스크립팅 솔루션을 사용하여 Azure VM을 자동으로 종료하고 다시 시작하는 방법을 고려하세요.

> [!IMPORTANT]
> 요금 청구를 방지하려면 VM을 종료하고 할당 해제해야 합니다. 단순히 중지하거나 전원 옵션을 사용하여 VM을 종료하면 사용당 요금이 발생합니다.

## <a name="next-steps"></a>다음 단계

Azure 가격 책정 지침에 대해서는 [Azure 청구 및 비용 관리를 사용하여 예상치 못한 비용 방지](../../../billing/billing-getting-started.md)를 참조하세요. SQL Server를 포함하여 최신 Virtual Machines 가격에 대해서는 [Windows VM](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 또는 [Linux VM](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)에 대한 Azure VM 가격 페이지를 참조하세요.

Azure Virtual Machines에서 실행되는 SQL Server에 대한 개요는 다음 문서를 참조하세요.

- [Windows VM의 SQL Server 개요](virtual-machines-windows-sql-server-iaas-overview.md)
- [Linux VM의 SQL Server 개요](../../linux/sql/sql-server-linux-virtual-machines-overview.md)