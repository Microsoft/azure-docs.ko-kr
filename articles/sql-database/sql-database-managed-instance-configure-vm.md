---
title: 클라이언트 VM 연결 - Azure SQL Database Managed Instance | Microsoft Docs
description: Azure 가상 머신에서 SQL Server Management Studio를 사용하여 Azure SQL Database Managed Instance에 연결합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, srbozovi, bonova
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 59088ad53e923f1303c0e800df9c25f70e63812f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60701884"
---
# <a name="quickstart-configure-azure-vm-to-connect-to-an-azure-sql-database-managed-instance"></a>빠른 시작: Azure SQL Database Managed Instance에 연결하도록 Azure VM 구성

이 빠른 시작에서는 SSMS(SQL Server Management Studio)를 사용하여 Azure SQL Database Managed Instance에 연결하도록 Azure 가상 머신을 구성하는 방법을 보여 줍니다. 지점 및 사이트 간 연결을 사용하여 온-프레미스 클라이언트 컴퓨터에서 연결하는 방법을 보여주는 빠른 시작은 [지점 및 사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서는 [Managed Instance 만들기](sql-database-managed-instance-get-started.md)에서 만든 리소스를 시작점으로 사용합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-new-subnet-in-the-managed-instance-vnet"></a>Managed Instance VNet에 새 서브넷 만들기

다음 단계에서는 Azure 가상 머신이 Managed Instance에 연결할 수 있도록 Managed Instance VNet에 새 서브넷을 만듭니다. Managed Instance 서브넷은 Managed Instance 전용입니다. 해당 서브넷에서 Azure 가상 머신과 같은 다른 리소스를 만들 수 없습니다.

1. [Managed Instance 만들기](sql-database-managed-instance-get-started.md) 빠른 시작에서 만든 Managed Instance의 리소스 그룹을 엽니다. Managed Instance의 가상 네트워크를 선택합니다.

   ![Managed Instance 리소스](./media/sql-database-managed-instance-configure-vm/resources.png)

2. **서브넷**을 선택한 후 **+ 서브넷**을 선택하여 새 서브넷을 만듭니다.

   ![Managed Instance 서브넷](./media/sql-database-managed-instance-configure-vm/subnets.png)

3. 이 표의 정보를 사용하여 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ---------------- | ----------------- | ----------- |
   | **Name** | 모든 유효한 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   | **주소 범위(CIDR 블록)** | 유효 범위 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **네트워크 보안 그룹** | 없음 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **경로 테이블** | 없음 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **서비스 엔드포인트** | 0개 선택됨 | 이 빠른 시작에는 기본값이 적합합니다.|
   | **서브넷 위임** | 없음 | 이 빠른 시작에는 기본값이 적합합니다.|

   ![클라이언트 VM의 새 Managed Instance 서브넷](./media/sql-database-managed-instance-configure-vm/new-subnet.png)

4. **확인**을 선택하여 Managed Instance VNet에 추가 서브넷을 만듭니다.

## <a name="create-a-virtual-machine-in-the-new-subnet-in-the-vnet"></a>Vnet의 새 서브넷에 가상 머신 만들기

다음 단계에서는 새 서브넷에서 Managed Instance에 연결하는 가상 머신을 만드는 방법을 보여줍니다.

## <a name="prepare-the-azure-virtual-machine"></a>Azure 가상 머신 준비

SQL Managed Instance가 개인 Virtual Network에 배치되므로 SQL Server Management Studio 또는 Azure Data Studio 같은 설치된 SQL 클라이언트 도구를 사용하여 Azure VM을 만들어야 합니다. 이 도구를 통해 Managed Instance에 연결하고 쿼리를 실행할 수 있습니다. 이 빠른 시작에서는 SQL Server Management Studio를 사용합니다.

필요한 도구가 모두 포함된 클라이언트 가상 머신을 만드는 가장 쉬운 방법은 Azure Resource Manager 템플릿을 사용하는 것입니다.

1. 다른 브라우저 탭에서 Azure Portal에 로그인되었는지 확인하세요. 그러고 나서 다음 단추를 선택하여 클라이언트 가상 머신을 만들고 SQL Server Management Studio를 설치합니다.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

2. 다음 표의 정보를 사용하여 양식을 작성합니다.

   | 설정| 제안 값 | 설명 |
   | ---------------- | ----------------- | ----------- |
   | **구독** | 유효한 구독 | 새 리소스를 만들 권한이 있는 구독이어야 합니다. |
   | **리소스 그룹** |[Managed Instance 만들기](sql-database-managed-instance-get-started.md) 빠른 시작에서 지정한 리소스 그룹입니다.|이 리소스 그룹은 VNet이 있는 리소스 그룹이어야 합니다.|
   | **위치**: | 리소스 그룹의 위치 | 이 값은 선택한 리소스 그룹에 따라 채워집니다. |
   | **가상 머신 이름**  | 모든 유효한 이름 | 유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요.|
   |**관리 사용자 이름**|유효한 사용자 이름|유효한 이름은 [명명 규칙 및 제한 사항](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)을 참조하세요. "serveradmin"을 예약된 서버 수준 역할로 사용하지 않습니다.<br>이 사용자 이름은 [VM에 연결](#connect-to-virtual-machine)할 때마다 사용됩니다.|
   |**암호**|유효한 암호|암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.<br>이 암호는 [VM에 연결](#connect-to-virtual-machine)할 때마다 사용됩니다.|
   | **가상 머신 크기** | 모든 유효한 크기 | 이 빠른 시작에서는 이 템플릿의 기본값인 **Standard_B2s**로 충분합니다. |
   | **위치**:|[resourceGroup().location].| 이 값은 변경하지 마세요. |
   | **가상 네트워크 이름**|관리 되는 인스턴스에서 만든 가상 네트워크.|
   | **서브넷 이름**|이전 절차에서 만든 서브넷의 이름| Managed Instance를 만든 서브넷을 선택하지 마세요.|
   | **아티팩트 위치** | [deployment().properties.templateLink.uri] | 이 값은 변경하지 마세요. |
   | **아티팩트 위치 Sas 토큰** | 비워 둡니다. | 이 값은 변경하지 마세요. |

   ![클라이언트 VM 만들기](./media/sql-database-managed-instance-configure-vm/create-client-sql-vm.png)

   [Managed Instance 만들기](sql-database-managed-instance-get-started.md)에서 추천 VNet 이름과 기본 서브넷을 사용한 경우 마지막 두 매개 변수를 변경할 필요가 없습니다. 그렇지 않은 경우 두 값을 네트워크 환경을 설정할 때 입력한 값으로는 변경해야 합니다.

3. **위에 명시된 사용 약관에 동의함** 확인란을 선택합니다.
4. **구매**를 선택하여 네트워크에서 Azure VM을 배포합니다.
5. **알림** 아이콘을 선택하여 배포 상태를 확인합니다.

> [!IMPORTANT]
> 사후 생성 스크립트가 SQL Server Management Studio를 설치할 시간을 제공하기 위해 가상 머신이 생성된 후 약 15분이 경과할 때까지 계속 진행하지 마세요.

## <a name="connect-to-virtual-machine"></a>가상 머신에 연결

다음 단계에서는 원격 데스크톱 연결을 사용하여 새로 만든 가상 머신에 연결하는 방법을 보여 줍니다.

1. 배포가 완료되면 가상 머신 리소스로 이동합니다.

    ![VM](./media/sql-database-managed-instance-configure-vm/vm.png)  

2. **연결**을 선택합니다.

   가상 머신의 공용 IP 주소 및 포트 번호를 사용하여 원격 데스크톱 프로토콜 파일(.rdp 파일) 양식이 나타납니다.

   ![RDP 양식](./media/sql-database-managed-instance-configure-vm/rdp.png)  

3. **RDP 파일 다운로드**를 선택합니다.

   > [!NOTE]
   > SSH를 사용하여 VM에 연결할 수도 있습니다.

4. **가상 머신에 연결** 양식을 닫습니다.
5. VM에 연결하려면 다운로드한 RDP 파일을 엽니다.
6. 메시지가 표시되면 **연결**을 선택합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)와 같은 RDP 클라이언트가 필요합니다.

7. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 선택합니다.

8. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 선택하여 연결을 진행합니다.

서버 관리자 대시보드에서 가상 머신에 연결되어 있습니다.

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS를 사용하여 Managed Instance에 연결

1. 가상 머신에서 SSMS(SQL Server Management Studio)를 엽니다.

   열릴 때까지 몇 분이 걸립니다. SSMS를 처음 시작하는 것이므로 구성을 완료해야 하기 때문입니다.
2. **서버에 연결** 대화 상자에서, **서버 이름** 상자에 Managed Instance의 정규화된 **호스트 이름**을 입력합니다. **SQL Server 인증**을 선택하고, 로그인 및 암호를 입력한 다음, **연결**을 선택합니다.

    ![ssms 연결](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

연결되면 데이터베이스 노드에서 시스템 및 사용자 데이터베이스를 보고, 보안, 서버 개체, 복제, 관리, SQL Server 에이전트 및 XEvent 프로파일러 노드에서 다양한 개체를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 온-프레미스 클라이언트 컴퓨터에서 지점 및 사이트 간 연결을 사용하여 연결하는 방법을 보여주는 빠른 시작은 [지점 및 사이트 간 연결 구성](sql-database-managed-instance-configure-p2s.md)을 참조하세요.
- 애플리케이션의 연결 옵션 개요는 [애플리케이션을 Managed Instance에 연결](sql-database-managed-instance-connect-app.md)을 참조하세요.
- 온-프레미스의 기존 SQL Server 데이터베이스를 Managed Instance로 복원하려면 [마이그레이션용 Azure DMS(Database Migration Service)](../dms/tutorial-sql-server-to-managed-instance.md) 또는 [T-SQL RESTORE 명령](sql-database-managed-instance-get-started-restore.md)을 사용하여 데이터베이스 백업 파일에서 복원하면 됩니다.
