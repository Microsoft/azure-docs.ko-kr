---
title: '자습서: 데이터 수락 및 수신 - Azure Data Share'
description: 자습서 - Azure Data Share를 사용하여 데이터 수락 및 받기
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: f2acb89597ef877543a2c4cc46f395aede41034b
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964501"
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
* Microsoft.DataShare 리소스 공급자를 등록합니다. [Azure Data Share의 문제 해결](data-share-troubleshoot.md)에 설명된 지침에 따라 데이터 공유 리소스 공급자를 등록합니다.

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
    1. **+클라이언트 IP 추가**를 클릭하고 **저장**을 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. 이 프로세스는 다음에 Azure Portal에서 SQL 데이터를 공유할 때 반복해야 할 수도 있습니다. IP 범위를 추가할 수도 있습니다. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Azure Data Explorer 클러스터로 데이터를 받습니다. 

* 데이터 공급자의 데이터 탐색기 클러스터와 동일한 Azure 데이터 센터에 있는 Azure Data Explorer 클러스터: 아직 없는 경우 [Azure Data Explorer 클러스터](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)를 만들 수 있습니다. 데이터 공급자 클러스터의 Azure 데이터 센터를 모르는 경우 나중에 프로세스에서 클러스터를 만들 수 있습니다.
* Azure Data Explorer 클러스터에 쓸 수 있는 권한으로, *Microsoft.Kusto/clusters/write*에 있습니다. 이 권한은 기여자 역할에 있습니다. 
* Azure Data Explorer 클러스터에 역할 할당을 추가할 수 있는 권한으로, *Microsoft.Authorization/role assignments/write*에 있습니다. 이 권한은 소유자 역할에 있습니다. 

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="open-invitation"></a>초대 열기

1. 받은 편지함에서 데이터 공급자의 초대를 확인합니다. 이 초대는 Microsoft Azure에서 **<yourdataprovider@domain.com>이(가) 보낸 Azure Data Share 초대**라는 제목으로 보낸 것입니다. 여러 개의 초대가 있는 경우 올바른 공유를 수락할 수 있도록 공유 이름을 적어둡니다. 

1. **초대 보기**를 선택하여 Azure에서 초대를 확인합니다. 그러면 [받은 공유] 보기로 이동합니다.

   ![초대](./media/invitations.png "초대 목록") 

1. 확인하려는 공유를 선택합니다. 

## <a name="accept-invitation"></a>초대 수락
1. **사용 약관**을 포함하여 모든 필드를 검토해야 합니다. 사용 약관에 동의하는 경우 해당 확인란을 선택하여 동의함을 표시해야 합니다. 

   ![사용 약관](./media/terms-of-use.png "사용 약관") 

1. *대상 데이터 공유 계정*에서 Data Share를 배포할 대상의 구독 및 리소스 그룹을 선택합니다. 

   기존 Data Share 계정이 없는 경우 **데이터 공유 계정** 필드에서 **새로 만들기**를 선택합니다. 그렇지 않으면 데이터 공유를 수락할 기존 Data Share 계정을 선택합니다. 

   **받은 공유 이름** 필드에 대해 데이터 공급자가 지정한 기본값을 그대로 두거나 받은 공유에 대해 새 이름을 지정할 수 있습니다. 

   ![대상 데이터 공유 계정](./media/target-data-share.png "대상 데이터 공유 계정") 

1. 사용 약관에 동의하고 공유 위치를 지정했으면 *수락 및 구성*을 선택합니다. 공유 구독이 만들어집니다.

   스냅샷 기반 공유의 경우, 다음 화면에서 데이터를 복사할 대상 스토리지 계정을 선택하라는 메시지가 표시됩니다. 

   ![수락 옵션](./media/accept-options.png "수락 옵션") 

   지금 초대를 수락하고 나중에 대상 데이터 저장소를 구성하려면 *수락 및 나중에 구성*을 선택합니다. 나중에 스토리지를 계속 구성하려면 [데이터 세트 매핑 구성](how-to-configure-mapping.md) 페이지에서 데이터 공유 구성을 다시 시작하는 방법에 대한 자세한 단계를 참조하세요. 

   현재 항목 공유는, [데이터 세트 매핑 구성](how-to-configure-mapping.md) 페이지에서 데이터 공유 구성을 다시 시작하는 방법에 대한 자세한 단계를 참조하세요. 

   초대를 수락하지 않으려면 *거부*를 선택합니다. 

## <a name="configure-storage"></a>스토리지 구성
1. *대상 스토리지 설정* 아래에서 데이터를 받도록 하려는 구독, 리소스 그룹 및 스토리지 계정을 선택합니다. 

   ![대상 스토리지 설정](./media/target-storage-settings.png "대상 스토리지") 

1. 정기적인 데이터 업데이트를 받으려면 스냅샷 설정을 사용하도록 설정해야 합니다. 데이터 공급자가 스냅샷 설정 일정을 데이터 공유에 포함시킨 경우에만 해당 일정이 표시됩니다. 

   ![스냅샷 설정](./media/snapshot-settings.png "스냅샷 설정") 

1. *저장*을 선택합니다. 

> [!IMPORTANT]
> SQL 기반 데이터를 받고 해당 데이터를 SQL 기반 소스로 받으려는 경우 [데이터 세트 매핑 구성](how-to-configure-mapping.md) 방법 가이드에서 SQL Server를 데이터 세트의 대상으로 구성하는 방법을 알아보세요. 

## <a name="trigger-a-snapshot"></a>스냅샷 트리거
이 단계는 스냅샷 기반 공유에만 적용됩니다.

1. **스냅샷 트리거**를 선택하여 받은 공유 - > 세부 정보 탭에서 스냅샷을 트리거할 수 있습니다. 여기서는 데이터의 전체 또는 증분 스냅샷을 트리거할 수 있습니다. 데이터를 데이터 공급자로부터 처음 받는 경우 전체 복사본을 선택합니다. 

   ![스냅샷 트리거](./media/trigger-snapshot.png "스냅샷 트리거") 

1. 마지막 실행 상태가 *성공*인 경우 대상 데이터 저장소로 이동하여 받은 데이터를 확인합니다. **데이터 세트**를 선택하고 대상 경로에서 링크를 클릭합니다. 

   ![소비자 데이터 세트](./media/consumer-datasets.png "소비자 데이터 세트 매핑") 

## <a name="view-history"></a>기록 보기
스냅샷 기록을 확인하려면 받은 공유 -> 기록으로 이동합니다. 여기서는 지난 60일 동안 생성된 모든 스냅샷의 기록을 확인할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Data Share를 수락하고 받는 방법을 알아보았습니다. Azure Data Share 개념에 대해 자세히 알아보려면 [개념: Azure Data Share 용어](terminology.md)로 계속 진행하세요.