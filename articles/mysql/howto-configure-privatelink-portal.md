---
title: Azure Database for MySQL (미리 보기) 포털 설치 방법에 대 한 개인 링크
description: Azure Portal에서 Azure Database for MySQL에 대 한 개인 링크를 구성 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: fa8bc56376704b96f5ddee09db7b09e28f10a936
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281243"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-portal"></a>포털을 사용 하 여 Azure Database for MySQL (미리 보기)에 대 한 개인 링크 만들기 및 관리

프라이빗 엔드포인트는 Azure에서 프라이빗 링크를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트는 VM(Virtual Machines) 같은 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있게 해줍니다. 이 문서에서는 azure 개인 엔드포인트을 사용 하 여 Azure Virtual Network 및 Azure Database for MySQL 서버에서 VM을 만드는 Azure Portal를 사용 하는 방법을 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

> [!NOTE]
> 이 기능은 Azure Database for MySQL 범용 및 메모리 액세스에 최적화 된 가격 책정 계층을 지 원하는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인
[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-vm"></a>Azure VM 만들기

이 섹션에서는 가상 네트워크와 개인 링크 리소스 (Azure의 MySQL 서버)에 액세스 하는 데 사용 되는 VM을 호스트 하는 서브넷을 만듭니다.

### <a name="create-the-virtual-network"></a>가상 네트워크 만들기
이 섹션에서는 Private Link 리소스에 액세스하는 데 사용되는 VM을 호스트하는 Virtual Network와 서브넷을 만듭니다.

1. 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 차례로 선택합니다.
2. **가상 네트워크 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 이름 | *MyVirtualNetwork*를 입력합니다. |
    | 주소 공간 | *10.1.0.0/16*을 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | 리소스 그룹 | **새로 만들기**를 선택하고 *myResourceGroup*을 입력한 다음, **확인**을 선택합니다. |
    | 위치 | **유럽 서부**를 선택합니다.|
    | 서브넷 - 이름 | *mySubnet*을 입력합니다. |
    | 서브넷 - 주소 범위 | *10.1.0.0/24*를 입력합니다. |
    |||
3. 나머지 항목은 기본값으로 유지하고 **만들기**를 선택합니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **가상 머신**을 선택합니다.

2. **가상 머신 만들기 - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 정보** | |
    | Subscription | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.  |
    | **인스턴스 정보** |  |
    | 가상 머신 이름 | *myVm*을 입력합니다. |
    | 지역 | **유럽 서부**를 선택합니다. |
    | 가용성 옵션 | 기본값인 **인프라 중복이 필요하지 않습니다**를 그대로 둡니다. |
    | 이미지 | **Windows Server 2019 Datacenter**를 선택합니다. |
    | 크기 | 기본값인 **표준 DS1 v2**를 그대로 둡니다. |
    | **관리자 계정** |  |
    | 사용자 이름 | 선택한 사용자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    | 암호 확인 | 암호를 다시 입력합니다. |
    | **인바운드 포트 규칙** |  |
    | 공용 인바운드 포트 | 기본값인 **없음**을 그대로 둡니다. |
    | **비용 절감** |  |
    | 이미 Windows 라이선스가 있나요? | 기본값인 **아니요**를 그대로 둡니다. |
    |||

1. **다음: 디스크**를 선택 합니다.

1. **가상 머신 만들기-디스크**에서 기본값을 그대로 두고 **다음: 네트워킹**을 선택 합니다.

1. **가상 머신 만들기 - 네트워킹**에서 다음 정보를 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 가상 네트워크 | 기본값인 **MyVirtualNetwork**를 그대로 둡니다.  |
    | 주소 공간 | 기본값인 **10.1.0.0/24**를 그대로 둡니다.|
    | 서브넷 | 기본값인 **mySubnet(10.1.0.0/24)** 을 그대로 둡니다.|
    | 공용 IP | 기본값 **(신규) myVm-ip**를 그대로 둡니다. |
    | 공용 인바운드 포트 | **선택한 포트 허용**을 선택합니다. |
    | 인바운드 포트 선택 | **HTTP** 및 **RDP**를 선택합니다.|
    |||


1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다.

1. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다.

## <a name="create-an-azure-database-for-mysql"></a>Azure Database for MySQL 만들기

이 섹션에서는 Azure에서 Azure Database for MySQL 서버를 만듭니다. 

1. Azure Portal 화면 왼쪽 상단에서 **리소스 만들기** > **데이터베이스** > **Azure Database for MySQL**를 선택 합니다.

1. **Azure Database for MySQL** 에서 다음 정보를 제공 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **서버 정보** |  |
    |서버 이름  | *MyServer*를 입력 합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다.|
    | 관리자 사용자 이름| 원하는 관리자 이름을 입력합니다. |
    | 암호 | 선택한 암호를 입력합니다. 암호는 8자 이상이어야 하며 정의된 요구 사항을 충족해야 합니다. |
    | 위치 | MySQL Server를 배치할 Azure 지역을 선택 합니다. |
    |버전  | 필요한 MySQL 서버의 데이터베이스 버전을 선택 합니다.|
    | Compute + 저장소| 워크 로드에 따라 서버에 필요한 가격 책정 계층을 선택 합니다. |
    |||
 
7. **확인**을 선택합니다. 
8. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
9. 유효성 검사 통과 메시지가 표시되면 **만들기**를 선택합니다. 
10. 유효성 검사 통과 메시지가 표시되면 [만들기]를 선택합니다. 

## <a name="create-a-private-endpoint"></a>프라이빗 엔드포인트 만들기

이 섹션에서는 MySQL 서버를 만들고 여기에 개인 엔드포인트을 추가 합니다. 

1. Azure Portal 화면의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **Private Link 센터(미리 보기)** 를 선택합니다.

2. **Private Link 센터 - 개요**의 **서비스에 대한 프라이빗 연결 설정** 옵션에서 **시작**을 선택합니다.

    ![개인 링크 개요](media/concepts-data-access-and-security-private-link/privatelink-overview.png)

1. **프라이빗 엔드포인트 만들기(미리 보기) - 기본 사항**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | **프로젝트 세부 정보** | |
    | Subscription | 구독을 선택합니다. |
    | 리소스 그룹 | **myResourceGroup**을 선택합니다. 이전 섹션에서 만든 것입니다.|
    | **인스턴스 세부 정보** |  |
    | 이름 | *myPrivateEndpoint*를 입력합니다. 이 이름을 사용하는 경우 고유한 이름을 만듭니다. |
    |지역|**유럽 서부**를 선택합니다.|
    |||

5. **다음: 리소스**를 선택 합니다.
6. **프라이빗 엔드포인트 만들기 - 리소스**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |연결 방법  | 내 디렉터리의 Azure 리소스에 연결하도록 선택합니다.|
    | Subscription| 구독을 선택합니다. |
    | 리소스 유형 | **Microsoft DBforMySQL/servers**를 선택 합니다. |
    | 리소스 |*myServer*를 선택합니다.|
    |대상 하위 리소스 |*Mysqlserver* 를 선택 합니다.|
    |||
7. **다음: 구성**을 선택 합니다.
8. **프라이빗 엔드포인트 만들기(미리 보기) - 구성**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    |**네트워킹**| |
    | 가상 네트워크| *MyVirtualNetwork*를 선택합니다. |
    | 서브넷 |  *mySubnet*을 선택합니다. |
    |**프라이빗 DNS 통합**||
    |프라이빗 DNS 영역과 통합 |**예**를 선택합니다. |
    |프라이빗 DNS 영역 |*(New) privatelink* 를 선택 합니다. |
    |||

1. **검토 + 만들기**를 선택합니다. **검토 + 만들기** 페이지로 이동됩니다. 여기서 구성이 유효한지 검사됩니다. 
2. **유효성 검사 통과** 메시지가 표시되면 **만들기**를 선택합니다. 

    ![개인 링크를 만듦](media/concepts-data-access-and-security-private-link/show-mysql-private-link.png)

## <a name="connect-to-a-vm-using-remote-desktop-rdp"></a>RDP(원격 데스크톱)를 사용하여 VM에 연결


**myVm**을 만든 후 다음과 같이 인터넷을 통해 연결합니다. 

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. *downloaded.rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.

## <a name="access-the-mysql-server-privately-from-the-vm"></a>VM에서 전용 MySQL 서버 액세스

1.  *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.

2.  `nslookup  myServer.privatelink.mysql.database.azure.com`을 입력합니다. 

    다음과 유사한 메시지가 표시됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myServer.privatelink.mysql.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **MySQL**.|
    | Server name| Select *myServer.privatelink.mysql.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the MySQL server creation. |
    |Password |Enter a password provided during the MySQL server creation. |
    |SSL|Select **Required**.|
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL server.

8. Close the remote desktop connection to myVm.

## Clean up resources
When you're done using the private endpoint, MySQL server, and the VM, delete the resource group and all of the resources it contains:

1. Enter *myResourceGroup* in the **Search** box at the top of the portal and select *myResourceGroup* from the search results.
2. Select **Delete resource group**.
3. Enter myResourceGroup for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this how-to, you created a VM on a virtual network, an Azure Database for MySQL, and a private endpoint for private access. You connected to one VM from the internet and securely communicated to the MySQL server using Private Link. To learn more about private endpoints, see [What is Azure private endpoint](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).
