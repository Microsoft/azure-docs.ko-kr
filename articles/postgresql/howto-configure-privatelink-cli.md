---
title: Azure Database for PostgreSQL 단일 서버 (미리 보기) CLI 설치 방법에 대 한 개인 링크
description: Azure CLI에서 Azure Database for PostgreSQL-단일 서버에 대 한 개인 링크를 구성 하는 방법에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 19dd0051985231a0274baf550755cc61782ce740
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281311"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-preview-using-cli"></a>CLI를 사용 하 여 Azure Database for PostgreSQL 단일 서버 (미리 보기)에 대 한 개인 링크 만들기 및 관리

프라이빗 엔드포인트는 Azure에서 프라이빗 링크를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트는 VM(Virtual Machines) 같은 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있게 해줍니다. 이 문서에서는 azure 개인 엔드포인트을 사용 하 여 Azure Virtual Network 및 Azure Database for PostgreSQL 단일 서버에서 VM을 만드는 Azure CLI를 사용 하는 방법을 알아봅니다.

> [!NOTE]
> 이 기능은 Azure Database for PostgreSQL 단일 서버가 범용 및 메모리 액세스에 최적화 된 가격 책정 계층을 지 원하는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.

- [PostgreSQL용 Azure Database 서버 및 데이터베이스](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

대신 Azure CLI를 로컬에 설치하고 사용하려는 경우 이 빠른 시작을 진행하려면 Azure CLI 버전 2.0.28 이상을 사용해야 합니다. 설치된 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드 정보는 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스를 만들려면 먼저 Virtual Network를 호스팅할 리소스 그룹을 만들어야 합니다. [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *westeurope* 위치에 *myresourcegroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Virtual Network 만들기
[az network vnet create](/cli/azure/network/vnet)를 사용하여 Virtual Network를 만듭니다. 다음 예제에서는 *mySubnet*이라는 하나의 서브넷이 있는 *myVirtualNetwork*라는 기본 Virtual Network를 만듭니다.

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>서브넷 프라이빗 엔드포인트 정책 사용 안 함 
Azure는 리소스를 가상 네트워크 내의 서브넷에 배포하므로 프라이빗 엔드포인트 네트워크 정책을 사용하지 않도록 서브넷을 만들거나 업데이트해야 합니다. [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update)를 사용하여 *mySubnet*이라는 서브넷 구성을 업데이트합니다.

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>VM 만들기 
az vm create를 사용하여 VM을 만듭니다. 메시지가 표시되면 VM에 대한 로그인 자격 증명으로 사용할 암호를 제공합니다. 다음 예제에서는 *myVm*이라는 VM을 만듭니다. 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 VM의 공용 IP 주소를 적어둡니다. 이 주소를 사용하여 다음 단계에서 인터넷을 통해 VM에 연결합니다.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL 단일 서버 만들기 
Az postgres server create 명령을 사용 하 여 Azure Database for PostgreSQL를 만듭니다. PostgreSQL 서버의 이름은 Azure에서 고유 해야 하므로 괄호 안의 자리 표시자 값을 고유한 값으로 바꿉니다. 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

참고 PostgreSQL 서버 ID는 다음 단계에서 PostgreSQL 서버 ID를 사용 하 ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.```와 유사 합니다. 

## <a name="create-the-private-endpoint"></a>프라이빗 엔드포인트 만들기 
Virtual Network에서 PostgreSQL 서버에 대 한 개인 엔드포인트을 만듭니다. 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>프라이빗 DNS 영역 구성 
PostgreSQL 서버 도메인에 대 한 사설 DNS 영역을 만들고 Virtual Network 연결 링크를 만듭니다. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>인터넷에서 VM에 연결

다음과 같이 인터넷에서 *myVm* VM에 연결합니다.

1. 포털의 검색 창에 *myVm*을 입력합니다.

1. **연결** 단추를 선택합니다. **연결** 단추를 선택하면 **가상 머신에 연결**이 열립니다.

1. **RDP 파일 다운로드**를 선택합니다. Azure에서 원격 데스크톱 프로토콜( *.rdp*) 파일을 만들고, 컴퓨터에 다운로드합니다.

1. 다운로드 한 .rdp* 파일을 엽니다.

    1. 메시지가 표시되면 **연결**을 선택합니다.

    1. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다.

        > [!NOTE]
        > **추가 선택 사항** > **다른 계정 사용**을 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.

1. **확인**을 선택합니다.

1. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 인증서 경고가 표시되면 **예** 또는 **계속**을 선택합니다.

1. VM 데스크톱이 나타나면 최소화하여 로컬 데스크톱으로 돌아갑니다.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>VM에서 개인적으로 PostgreSQL 서버에 액세스

1.  *myVM*의 원격 데스크톱에서 PowerShell을 엽니다.

2.  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`를 입력합니다. 

    다음과 유사한 메시지가 표시됩니다.
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the PostgreSQL server using any available client. In the example below I have used [Azure Data studio](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) to do the operation.

4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Server type| Select **PostgreSQL**.|
    | Server name| Select *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | User name | Enter username as username@servername which is provided during the PostgreSQL server creation. |
    |Password |Enter a password provided during the PostgreSQL server creation. |
    |SSL|Select **Required**.|
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the postgreSQL server.

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>다음 단계
- [Azure 개인 엔드포인트 이란?](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 에 대해 자세히 알아보세요.
