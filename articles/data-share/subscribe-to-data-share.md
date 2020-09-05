---
title: '자습서: 데이터 수락 및 수신 - Azure Data Share'
description: 자습서 - Azure Data Share를 사용하여 데이터 수락 및 받기
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: 409f143ce67e301e3b2a973d8d2db80380fbd50e
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258629"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>자습서: Azure Data Share를 사용하여 데이터 수락 및 받기  

이 자습서에서는 Azure Data Share를 사용하여 데이터 공유 초대를 수락하는 방법에 대해 알아봅니다. 공유되는 데이터를 받는 방법과 정기적인 새로 고침 간격을 사용하도록 설정하여 공유되는 데이터의 최신 스냅샷을 항상 유지하는 방법에 대해 알아봅니다. 

> [!div class="checklist"]
> * Azure Data Share 초대를 수락하는 방법
> * Azure Data Share 계정 만들기
> * 데이터 대상 지정
> * 예약된 새로 고침을 위해 데이터 공유에 대한 구독 만들기

## <a name="prerequisites"></a>사전 요구 사항
데이터 공유 초대를 수락하려면 먼저 아래에 나열된 여러 Azure 리소스를 프로비저닝해야 합니다. 

모든 필수 조건이 충족되었는지 확인한 후에 데이터 공유 초대를 수락합니다. 

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터 공유 초대: " **<yourdataprovider@domain.com>** 이(가) 보낸 Azure Data Share 초대"라는 제목이 있는 Microsoft Azure의 초대입니다.
* 데이터 공유 리소스를 만들 Azure 구독과 대상 Azure 데이터 저장소가 있는 Azure 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다.

### <a name="receive-data-into-a-storage-account"></a>스토리지 계정으로 데이터를 받습니다. 

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)을 만들 수 있습니다. 
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write*에 있습니다. 이 권한은 기여자 역할에 있습니다. 
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write*에 있습니다. 이 권한은 소유자 역할에 있습니다.  

### <a name="receive-data-into-a-sql-based-source"></a>SQL 기반 원본으로 데이터를 받습니다.

* SQL 서버의 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write*에 있습니다. 이 권한은 기여자 역할에 있습니다. 
* 데이터 공유 리소스의 관리 ID가 Azure SQL Database 또는 Azure SQL Data Warehouse에 액세스할 수 있는 권한입니다. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. 자신을 SQL 서버에 대한 Azure Active Directory 관리자로 설정합니다.
    1. Azure Active Directory를 사용하여 Azure SQL Database/Data Warehouse에 연결합니다.
    1. 쿼리 편집기(미리 보기)를 사용하여 다음 스크립트를 실행하여 Data Share 관리 ID를 'db_datareader, db_datawriter, db_ddladmin'으로 추가합니다. SQL Server 인증이 아닌 Active Directory를 사용하여 연결해야 합니다. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.         

* 클라이언트 IP SQL Server 방화벽 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal의 SQL 서버에서 *방화벽 및 가상 네트워크*로 이동합니다.
    1. **켜짐** 토글을 클릭하여 Azure 서비스에 대한 액세스를 허용합니다.
    1. **+클라이언트 IP 추가**를 클릭하고 **저장**을 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 다음에 Azure Portal에서 SQL 대상으로 데이터를 수신할 때 이 프로세스를 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터로 데이터를 받습니다. 

* 데이터 공급자의 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에 있는 Azure Data Explorer 클러스터: 아직 없는 경우 [Azure Data Explorer 클러스터](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)를 만들 수 있습니다. 데이터 공급자 클러스터의 Azure 데이터 센터를 모르는 경우 나중에 프로세스에서 클러스터를 만들 수 있습니다.
* Azure Data Explorer 클러스터에 쓸 수 있는 권한으로, *Microsoft.Kusto/clusters/write*에 있습니다. 이 권한은 기여자 역할에 있습니다. 
* Azure Data Explorer 클러스터에 역할 할당을 추가할 수 있는 권한으로, *Microsoft.Authorization/role assignments/write*에 있습니다. 이 권한은 소유자 역할에 있습니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="open-invitation"></a>초대 열기

1. 이메일 또는 Azure Portal에서 직접 초대를 열 수 있습니다. 

   이메일에서 초대를 열려면 받은 편지함에서 데이터 공급자의 초대를 확인합니다. 이 초대는 Microsoft Azure에서 **<yourdataprovider@domain.com>이(가) 보낸 Azure Data Share 초대**라는 제목으로 보낸 것입니다. **초대 보기**를 클릭하여 Azure에서 초대를 확인합니다. 

   Azure Portal에서 직접 초대를 열려면 Azure Portal에서 **데이터 공유 초대**를 검색합니다. 그러면 데이터 공유 초대 목록으로 이동됩니다.

   ![초대](./media/invitations.png "초대 목록") 

1. 확인하려는 공유를 선택합니다. 

## <a name="accept-invitation"></a>초대 수락
1. **사용 약관**을 포함하여 모든 필드를 검토해야 합니다. 사용 약관에 동의하는 경우 해당 확인란을 선택하여 동의함을 표시해야 합니다. 

   ![사용 약관](./media/terms-of-use.png "사용 약관") 

1. *대상 데이터 공유 계정*에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 

   기존 Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기**를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

   **받은 공유 이름** 필드에 대해 데이터 공급자가 지정한 기본값을 그대로 두거나 받은 공유에 대해 새 이름을 지정할 수 있습니다. 

   사용 약관에 동의하고 받은 공유를 관리할 데이터 공유 계정을 지정했으면 **수락 및 구성**을 선택합니다. 공유 구독이 만들어집니다. 

   ![수락 옵션](./media/accept-options.png "수락 옵션") 

   그러면 데이터 공유 계정에서 받은 공유로 이동합니다. 

   초대를 수락하지 않으려면 *거부*를 선택합니다. 

## <a name="configure-received-share"></a>수신된 공유 구성
아래 단계에 따라 데이터를 받을 위치를 구성합니다.

1. **데이터 세트** 탭을 선택합니다. 대상을 할당하려는 데이터 세트 옆에 있는 상자를 선택합니다. **+ 대상에 매핑**을 선택하여 대상 데이터 저장소를 선택합니다. 

   ![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

1. 데이터를 가져올 대상 데이터 저장소 유형을 선택합니다. 경로와 이름이 동일한 대상 데이터 저장소에 있는 모든 데이터 파일 또는 테이블을 덮어씁니다. 

   내부 공유의 경우 지정된 위치에서 데이터 저장소를 선택합니다. 위치는 데이터 공급자의 원본 데이터 저장소가 있는 Azure 데이터 센터입니다. 데이터 세트가 매핑되면 대상 경로의 링크를 따라 데이터에 액세스할 수 있습니다.

   ![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 스토리지") 

1. 스냅샷 기반 공유의 경우 데이터 공급자가 데이터에 정기적인 업데이트를 제공하기 위해 스냅샷 일정을 만든 경우 **스냅샷 일정** 탭을 선택하여 스냅샷 일정을 사용하도록 설정할 수도 있습니다. 스냅샷 일정 옆의 확인란을 선택하고 **+ 사용**을 선택합니다.

   ![스냅샷 일정 사용](./media/enable-snapshot-schedule.png "스냅샷 일정 사용")

## <a name="trigger-a-snapshot"></a>스냅샷 트리거
이 단계는 스냅샷 기반 공유에만 적용됩니다.

1. **세부 정보** 탭 다음에 **스냅샷 트리거**를 선택하여 스냅샷을 트리거할 수 있습니다. 여기서는 데이터의 전체 또는 증분 스냅샷을 트리거할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 전체 복사본을 선택합니다. 

   ![스냅샷 트리거](./media/trigger-snapshot.png "스냅샷 트리거") 

1. 마지막 실행 상태가 *성공*인 경우 대상 데이터 저장소로 이동하여 받은 데이터를 확인합니다. **데이터 세트**를 선택하고 대상 경로에서 링크를 클릭합니다. 

   ![소비자 데이터 세트](./media/consumer-datasets.png "소비자 데이터 세트 매핑") 

## <a name="view-history"></a>기록 보기
이 단계는 스냅샷 기반 공유에만 적용됩니다. 스냅샷의 기록을 보려면 **기록** 탭을 선택합니다. 여기서는 지난 30일 동안 생성된 모든 스냅샷의 기록을 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Data Share를 수락하고 받는 방법을 알아보았습니다. Azure Data Share 개념에 대해 자세히 알아보려면 [개념: Azure Data Share 용어](terminology.md)로 계속 진행하세요.