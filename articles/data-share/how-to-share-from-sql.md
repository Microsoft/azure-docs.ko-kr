---
title: Azure SQL Database 및 Azure Synapse Analytics에서 데이터 공유 및 받기
description: Azure SQL Database 및 Azure Synapse Analytics에서 데이터를 공유 하 고 수신 하는 방법을 알아봅니다.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: 2cb06b6802fdc4cebd04f687266f5ac08dde82c0
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89270131"
---
# <a name="share-and-receive-data-from-azure-sql-database-and-azure-synapse-analytics"></a>Azure SQL Database 및 Azure Synapse Analytics에서 데이터 공유 및 받기

[!INCLUDE[appliesto-sql](includes/appliesto-sql.md)]

Azure 데이터 공유는 스냅숏 기반 공유 Azure SQL Database 및 Azure Synapse Analytics (이전의 Azure SQL DW)를 지원 합니다. 이 문서에서는 이러한 원본에서 데이터를 공유 하 고 받는 방법을 설명 합니다.

Azure 데이터 공유는 Azure SQL Database 및 Azure Synapse Analytics (이전의 Azure SQL DW)에서 테이블 또는 뷰를 공유할 수 있도록 지원 합니다. 데이터 소비자는 데이터를 Azure Data Lake Storage Gen2 또는 Azure Blob Storage, csv 또는 parquet 파일, Azure SQL Database 및 Azure Synapse Analytics를 테이블로 수락 하도록 선택할 수 있습니다.

Azure Data Lake Store Gen2 또는 Azure Blob Storage에 데이터를 수락 하는 경우 전체 스냅숏은 이미 있는 경우 대상 파일의 내용을 덮어씁니다.
테이블에 데이터를 수신 하 고 대상 테이블이 아직 없는 경우 Azure 데이터 공유는 원본 스키마를 사용 하 여 SQL 테이블을 만듭니다. 동일한 이름을 가진 대상 테이블이 이미 있는 경우 삭제 되 고 최신 전체 스냅숏으로 덮어쓰여집니다. 증분 스냅숏은 현재 지원 되지 않습니다.

## <a name="share-data"></a>데이터 공유

### <a name="prerequisites-to-share-data"></a>데이터 공유를 위한 필수 구성 요소

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자 Azure 로그인 이메일 주소(이메일 별칭을 사용하면 작동하지 않음).
* 원본 Azure 데이터 저장소가 데이터 공유 리소스를 만드는 데 사용하는 것과 다른 Azure 구독에 있는 경우 Azure 데이터 저장소가 있는 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다. 

### <a name="prerequisites-for-sql-source"></a>SQL 원본에 대 한 필수 구성 요소

* 공유하려는 테이블 및 보기를 포함하는 Azure SQL Database 또는 Azure Synapse Analytics(이전의 Azure SQL Data Warehouse)
* SQL 서버에 데이터베이스를 쓸 수 있는 권한으로, *Microsoft.Sql/servers/databases/write*에 있습니다. 이 권한은 기여자 역할에 있습니다.
* 데이터 웨어하우스에 액세스할 수 있는 데이터 공유에 대한 권한. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. 자신을 SQL 서버에 대한 Azure Active Directory 관리자로 설정합니다.
    1. Azure Active Directory를 사용하여 Azure SQL Database/Data Warehouse에 연결합니다.
    1. 쿼리 편집기(미리 보기)를 사용하여 다음 스크립트를 실행하여 Data Share 리소스 관리 ID를 db_datareader로 추가합니다. SQL Server 인증이 아닌 Active Directory를 사용하여 연결해야 합니다. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       *<share_acc_name>* 은 Data Share 리소스의 이름입니다. Data Share 리소스를 아직 만들지 않은 경우 나중에 이 필수 조건으로 다시 돌아올 수 있습니다.  

* 공유하려는 테이블 및/또는 보기를 탐색하고 선택할 수 있는 ‘db_datareader’가 포함된 Azure SQL Database 사용자입니다. 

* 클라이언트 IP SQL Server 방화벽 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal의 SQL 서버에서 *방화벽 및 가상 네트워크*로 이동합니다.
    1. **켜짐** 토글을 클릭하여 Azure 서비스에 대한 액세스를 허용합니다.
    1. **+클라이언트 IP 추가**를 클릭하고 **저장**을 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다. 

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="create-a-data-share-account"></a>Data Share 계정 만들기

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. 포털의 왼쪽 위 모서리에 있는 메뉴 단추를 선택한 다음 **리소스 만들기** (+)를 선택 합니다.

1. *Data Share*를 검색합니다.

1. Data Share를 선택하고 **만들기**를 선택합니다.

1. 다음 정보를 사용하여 Azure Data Share 리소스의 기본 세부 정보를 채웁니다. 

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | Subscription | 사용자의 구독 | 데이터 공유 계정에 사용할 Azure 구독을 선택합니다.|
    | 리소스 그룹 | *test-resource-group* | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
    | 이름 | *datashareaccount* | 데이터 공유 계정의 이름을 지정합니다. |
    | | |

1. **검토 + 만들기**를 선택한 다음, **만들기**를 선택하여 데이터 공유 계정을 프로비저닝합니다. 새 데이터 공유 계정을 프로비저닝하는 데 일반적으로 2분 정도 걸립니다. 

1. 배포가 완료되면 **리소스로 이동**을 선택합니다.

### <a name="create-a-share"></a>공유 만들기

1. Data Share 개요 페이지로 이동합니다.

    ![데이터 공유](./media/share-receive-data.png "데이터 공유") 

1. **Start sharing your data**(데이터 공유 시작)를 선택합니다.

1. **만들기**를 선택합니다.   

1. 공유에 대 한 세부 정보를 입력 합니다. 이름, 공유 유형, 공유 콘텐츠의 설명 및 사용 약관(선택 사항)을 지정합니다. 

    ![EnterShareDetails](./media/enter-share-details.png "공유 세부 정보 입력") 

1. **계속**을 선택합니다.

1. 공유에 데이터 집합을 추가 하려면 **데이터 집합 추가**를 선택 합니다. 

    ![공유에 데이터 집합 추가](./media/datasets.png "데이터 세트")

1. 추가하려는 데이터 세트 형식을 선택합니다. 이전 단계에서 선택한 공유 유형(스냅샷 또는 내부)에 따라 다른 데이터 세트 유형 목록이 표시됩니다. 

    ![AddDatasets](./media/add-datasets.png "데이터 세트 추가")    

1. SQL server를 선택 하 고 자격 증명을 제공 하 고 **다음** 을 선택 하 여 공유할 개체로 이동 하 고 ' 데이터 집합 추가 '를 선택 합니다. 

    ![SelectDatasets](./media/select-datasets-sql.png "데이터 세트 선택")    

1. 받는 사람 탭에서 '+ 받는 사람 추가'를 선택하여 데이터 소비자의 이메일 주소를 입력합니다. 

    ![AddRecipients](./media/add-recipient.png "수신자 추가") 

1. **계속**을 선택합니다.

1. 스냅샷 공유 유형을 선택한 경우 데이터 소비자에게 데이터 업데이트를 제공하도록 스냅샷 일정을 구성할 수 있습니다. 

    ![EnableSnapshots](./media/enable-snapshots.png "스냅샷 사용") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속**을 선택합니다.

1. 검토 + 만들기 탭에서 패키지 콘텐츠, 설정, 받는 사람 및 동기화 설정을 검토합니다. **만들기**를 선택합니다.

이제 Azure Data Share가 생성되었고 Data Share의 받는 사람이 초대를 수락할 준비가 되었습니다. 

## <a name="receive-data"></a>데이터 수신

### <a name="prerequisites-to-receive-data"></a>데이터를 받기 위한 필수 구성 요소
데이터 공유 초대를 수락하려면 먼저 아래에 나열된 여러 Azure 리소스를 프로비저닝해야 합니다. 

모든 필수 조건이 충족되었는지 확인한 후에 데이터 공유 초대를 수락합니다. 

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터 공유 초대: " **<yourdataprovider@domain.com>** 이(가) 보낸 Azure Data Share 초대"라는 제목이 있는 Microsoft Azure의 초대입니다.
* 데이터 공유 리소스를 만들 Azure 구독과 대상 Azure 데이터 저장소가 있는 Azure 구독에 [Microsoft.DataShare 리소스 공급자](concepts-roles-permissions.md#resource-provider-registration)를 등록합니다.

### <a name="prerequisites-for-target-storage-account"></a>대상 저장소 계정에 대 한 필수 구성 요소
Azure Storage 데이터를 수신 하도록 선택 하는 경우 다음은 필수 구성 요소 목록입니다.

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)을 만들 수 있습니다. 
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write*에 있습니다. 이 권한은 기여자 역할에 있습니다. 
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write*에 있습니다. 이 권한은 소유자 역할에 있습니다.  

### <a name="prerequisites-for-sql-target"></a>SQL 대상에 대 한 필수 구성 요소
Azure SQL Database으로 데이터를 받도록 선택 하는 경우 Azure Synapse Analytics는 필수 구성 요소 목록입니다.

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

### <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

### <a name="open-invitation"></a>초대 열기

1. 이메일 또는 Azure Portal에서 직접 초대를 열 수 있습니다. 

   이메일에서 초대를 열려면 받은 편지함에서 데이터 공급자의 초대를 확인합니다. 이 초대는 Microsoft Azure에서 **<yourdataprovider@domain.com>이(가) 보낸 Azure Data Share 초대**라는 제목으로 보낸 것입니다. **초대 보기**를 클릭하여 Azure에서 초대를 확인합니다. 

   Azure Portal에서 직접 초대를 열려면 Azure Portal에서 **데이터 공유 초대**를 검색합니다. 그러면 데이터 공유 초대 목록으로 이동됩니다.

   ![초대 목록](./media/invitations.png "초대 목록") 

1. 확인하려는 공유를 선택합니다. 

### <a name="accept-invitation"></a>초대 수락
1. **사용 약관**을 포함하여 모든 필드를 검토해야 합니다. 사용 약관에 동의하는 경우 해당 확인란을 선택하여 동의함을 표시해야 합니다. 

   ![사용 약관](./media/terms-of-use.png "사용 약관") 

1. *대상 데이터 공유 계정*에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 

   기존 Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기**를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

   **받은 공유 이름** 필드에 대해 데이터 공급자가 지정한 기본값을 그대로 두거나 받은 공유에 대해 새 이름을 지정할 수 있습니다. 

   사용 약관에 동의하고 받은 공유를 관리할 데이터 공유 계정을 지정했으면 **수락 및 구성**을 선택합니다. 공유 구독이 만들어집니다. 

   ![수락 옵션](./media/accept-options.png "수락 옵션") 

   그러면 데이터 공유 계정에서 받은 공유로 이동합니다. 

   초대를 수락하지 않으려면 *거부*를 선택합니다. 

### <a name="configure-received-share"></a>수신된 공유 구성
아래 단계에 따라 데이터를 받을 위치를 구성합니다.

1. **데이터 세트** 탭을 선택합니다. 대상을 할당하려는 데이터 세트 옆에 있는 상자를 선택합니다. **+ 대상에 매핑**을 선택하여 대상 데이터 저장소를 선택합니다. 

   ![대상에 매핑](./media/dataset-map-target.png "대상에 매핑") 

1. 데이터를 넣을 대상 데이터 저장소를 선택 합니다. 경로와 이름이 동일한 대상 데이터 저장소에 있는 모든 데이터 파일 또는 테이블을 덮어씁니다. 

   ![대상 스토리지 계정](./media/dataset-map-target-sql.png "대상 데이터 저장소") 

1. 스냅샷 기반 공유의 경우 데이터 공급자가 데이터에 정기적인 업데이트를 제공하기 위해 스냅샷 일정을 만든 경우 **스냅샷 일정** 탭을 선택하여 스냅샷 일정을 사용하도록 설정할 수도 있습니다. 스냅샷 일정 옆의 확인란을 선택하고 **+ 사용**을 선택합니다.

   ![스냅샷 일정 사용](./media/enable-snapshot-schedule.png "스냅샷 일정 사용")

### <a name="trigger-a-snapshot"></a>스냅샷 트리거
이 단계는 스냅샷 기반 공유에만 적용됩니다.

1. **세부 정보** 탭 다음에 **스냅샷 트리거**를 선택하여 스냅샷을 트리거할 수 있습니다. 여기서는 데이터의 전체 또는 증분 스냅샷을 트리거할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 전체 복사본을 선택합니다. SQL 원본의 경우 전체 스냅숏으로 지원 됩니다.

   ![스냅샷 트리거](./media/trigger-snapshot.png "스냅샷 트리거") 

1. 마지막 실행 상태가 *성공*인 경우 대상 데이터 저장소로 이동하여 받은 데이터를 확인합니다. **데이터 세트**를 선택하고 대상 경로에서 링크를 클릭합니다. 

   ![소비자 데이터 세트](./media/consumer-datasets.png "소비자 데이터 세트 매핑") 

### <a name="view-history"></a>기록 보기
이 단계는 스냅샷 기반 공유에만 적용됩니다. 스냅샷의 기록을 보려면 **기록** 탭을 선택합니다. 여기서는 지난 30일 동안 생성된 모든 스냅샷의 기록을 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
Azure 데이터 공유 서비스를 사용 하 여 저장소 계정에서 데이터를 공유 하 고 수신 하는 방법을 배웠습니다. 다른 데이터 원본에서 공유 하는 방법에 대 한 자세한 내용은 [지원 되는 데이터 저장소](supported-data-stores.md)를 계속 확인 하세요.

