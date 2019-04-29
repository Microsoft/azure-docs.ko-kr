---
title: 온-프레미스 Always On 가용성 그룹을 Azure로 확장 | Microsoft Docs
description: 이 자습서에서는 클래식 배포 모델을 사용하여 만든 리소스를 사용하며, SSMS(SQL Server Management Studio)에서 복제본 추가 마법사를 사용하여 Azure에 Always On 가용성 그룹 복제본을 추가하는 방법을 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: d3e56f1741a9cfd3f2d9f786c2ce22eb6a946ef2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481620"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>온-프레미스 Always On 가용성 그룹을 Azure로 확장
Always On 가용성 그룹은 보조 복제본 추가를 통해 데이터베이스 그룹에 고가용성을 제공합니다. 이러한 복제본은 오류 발생 시의 데이터베이스 장애 조치를 허용합니다. 또한 읽기 워크로드 또는 백업 작업의 부하를 줄이는 데 사용할 수 있습니다.

SQL Server에 하나 이상의 Azure VM을 프로비전한 다음 온-프레미스 가용성 그룹에 복제본 형태로 추가하면 온-프레미스 가용성 그룹을 Microsoft Azure로 확장할 수 있습니다.

이 자습서에서는 사용자에게 다음 항목이 있다고 가정합니다.

* 활성 Azure 구독. [무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.
* 기존 Always On 가용성 그룹 온-프레미스. 가용성 그룹에 대한 자세한 내용은 [Always On 가용성 그룹](https://msdn.microsoft.com/library/hh510230.aspx)을 참조하세요.
* Azure 가상 네트워크와 온-프레미스 네트워크 간의 연결. 이 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal(클래식)을 사용하여 사이트 간 연결 만들기](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)를 참조하세요.

> [!IMPORTANT] 
> Azure에는 리소스를 만들고 사용하기 위한 [Resource Manager 및 클래식](../../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다.

## <a name="add-azure-replica-wizard"></a>Azure Replica Wizard 추가
이 섹션에서는 **Azure 복제본 추가 마법사** 를 사용하여 Azure 복제본을 포함하도록 Always On 가용성 그룹 솔루션을 확장하는 방법을 보여줍니다.

> [!IMPORTANT]
> **Azure 복제본 추가 마법사**는 클래식 배포 모델을 사용하여 만든 가상 머신만 지원합니다. 새 VM 배포는 최신 Resource Manager 모델을 사용해야 합니다. Resource Manager와 함께 VM을 사용 중인 경우 Transact-SQL 명령(여기에 표시되지 않음)을 사용하여 보조 Azure 복제본을 수동으로 추가해야 합니다. 이 마법사는 Resource Manager 시나리오에서는 작동하지 않습니다.

1. SQL Server Management Studio에서 **Always On 고가용성** > **가용성 그룹** > **[가용성 그룹 이름]** 을 확장합니다.
2. 마우스 오른쪽 단추로 **가용성 복제본**을 클릭한 다음 **복제본 추가**를 클릭합니다.
3. 기본적으로는 **가용성 그룹에 복제본 추가 마법사** 가 표시됩니다. **다음**을 클릭합니다.  그 전에 이 마법사를 실행하는 중에 페이지 하단에 표시되는 **이 페이지를 다시 표시 안 함** 옵션을 선택한 경우 이 화면이 표시되지 않습니다.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. 모든 기존 보조 복제본에 연결해야 합니다. **연결…** 을 클릭합니다. 화면 하단의 **모두 연결…** 을 을 클릭할 수 있습니다. 인증 후 **다음** 을 클릭하여 다음 화면으로 이동합니다.
5. 에 **복제본 지정** 페이지 맨 위에 있는 여러 탭이 나열 됩니다. **복제본**, **끝점**합니다 **백업 기본 설정**, 및 **수신기**합니다. **복제본** 탭을 클릭하여 **Azure 복제본 추가...** 를 클릭하여 Azure 복제본 추가 마법사를 실행합니다.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. 이전에 Azure 관리 인증서를 설치한 경우 로컬 Windows 인증서 저장소에서 해당 기존 인증서를 선택합니다. 이전에 Azure 구독을 사용한 경우 해당 구독의 ID를 선택하거나 입력합니다. 다운로드를 클릭하여 Azure 관리 인증서를 다운로드하여 설치하고, Azure 계정을 사용하여 구독 목록을 다운로드할 수 있습니다.
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. 복제본을 호스팅하는 Azure Virtual Machine(VM)을 만드는 데 사용할 값으로 페이지의 각 필드를 입력하게 됩니다.
   
   | 설정 | 설명 |
   | --- | --- |
   | **이미지** |원하는 OS와 SQL Server 조합 선택 |
   | **VM 크기** |비즈니스 요구에 가장 적합한 VM 크기를 선택합니다. |
   | **VM 이름** |새 VM에 대해 고유한 이름을 지정합니다. 이름은 3~15자를 포함해야 하며, 문자/숫자/하이픈만 포함할 수 있습니다. 또한 문자로 시작해서 문자나 숫자로 끝나야 합니다. |
   | **VM 사용자 이름** |VM에서 관리자 계정이 될 사용자 이름을 지정합니다. |
   | **VM 관리자 암호** |새 계정의 암호 지정 |
   | **암호 확인** |새 계정의 암호 확인 |
   | **Virtual Network** |새 VM이 사용해야 할 Azure 가상 네트워크를 지정합니다. 가상 네트워크에 대한 자세한 내용은 [Virtual Network 개요](../../../virtual-network/virtual-networks-overview.md)를 참조하세요. |
   | **Virtual Network 서브넷** |새 VM이 사용해야 할 가상 네트워크서브넷을 지정합니다. |
   | **도메인** |도메인에 대해 사전 입력된 값이 정확한지 확인합니다. |
   | **도메인 사용자 이름** |로컬 클러스터 노드에서 로컬 관리자 그룹에 해당하는 계정을 지정합니다. |
   | **암호** |도메인 사용자 이름의 암호를 지정합니다. |
8. **확인** 을 클릭하여 배포 설정의 유효성을 검사합니다.
9. 약관이 다음에 표시됩니다. 약관을 읽고 동의하면 **확인** 을 클릭합니다.
10. **복제본 지정** 페이지가 다시 표시됩니다. **복제본**, **엔드포인트** 및 **Backup 기본 설정** 탭에서 새 Azure 복제본에 대한 설정을 확인합니다. 비즈니스 요구 사항에 맞게 설정을 수정합니다.  이 탭에 포함된 매개 변수에 대한 자세한 내용은 [복제본 지정 페이지(새 가용성 그룹 마법사/복제본 추가 마법사)](https://msdn.microsoft.com/library/hh213088.aspx)를 참조하세요. Azure 복제본이 포함된 가용성 그룹에 대해서는 수신기 탭을 사용하여 수신기를 만들 수 없습니다. 또한 마법사를 실행하기 전에 이미 수신기가 만들어진 경우 Azure에서 해당 사항이 지원되지 않는다는 메시지가 표시됩니다. 수신기를 만드는 방법은 **가용성 그룹 수신기 만들기** 섹션에서 살펴볼 것입니다.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. **다음**을 클릭합니다.
12. **초기 데이터 동기화 선택** 페이지에서 사용하려는 데이터 동기화 방법을 선택하고 **다음**을 클릭합니다. 대부분의 시나리오에서 **전체 데이터 동기화**를 선택합니다. 데이터 동기화 방법에 대한 자세한 내용은 [최초 데이터 동기화 선택 페이지(Always On 가용성 그룹 마법사)](https://msdn.microsoft.com/library/hh231021.aspx)를 참조하세요.
13. **유효성 검사** 페이지에서 결과를 검토합니다. 해결되지 않은 문제를 수정하고 필요에 따라 유효성 검사를 다시 실행합니다. **다음**을 클릭합니다.
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. **요약** 페이지에서 설정을 검토한 다음 **마침**을 클릭합니다.
15. 프로비전 프로세스가 시작됩니다. 마법사가 성공적으로 완료되면 **닫기** 를 클릭하여 마법사를 종료합니다.

> [!NOTE]
> Azure 복제본 추가 마법사는 Users\User Name\AppData\Local\SQL Server\AddReplicaWizard에 로그 파일을 만듭니다. 이 로그 파일을 사용하여 실패한 Azure 복제본 배포의 문제를 해결할 수 있습니다. 마법사가 어떤 작업을 실행하는 데 실패할 경우 프로비전된 VM 삭제를 포함하여 이전의 모든 작업이 롤백됩니다.
> 
> 

## <a name="create-an-availability-group-listener"></a>가용성 그룹 수신기 만들기
가용성 그룹을 만든 후에는 클라이언트가 복제본에 연결할 수 있게 수신기를 만들어야 합니다. 수신기는 들어오는 연결을 주 또는 읽기 전용 보조 복제본으로 전달합니다. 수신기에 대한 자세한 내용은 [Azure에서 Always On 가용성 그룹에 대한 ILB 수신기 구성](../classic/ps-sql-int-listener.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
**Azure 복제본 추가 마법사** 를 사용하여 Always On 가용성 그룹을 Azure로 확장하는 것 외에도 일부 SQL Server 워크로드를 완전히 Azure로 이동할 수 있습니다. 시작하려면 [Azure에서 SQL Server Virtual Machine 프로비전](../sql/virtual-machines-windows-portal-sql-server-provision.md)을 참조하세요.

Azure VM에서의 SQL Server 실행에 관한 다른 항목은 [Azure Virtual Machines의 SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

