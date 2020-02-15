---
title: '자습서: 조직 외부에서 공유 - Azure Data Share'
description: 자습서 - Azure Data Share를 사용하여 고객 및 파트너와 데이터 공유
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: tutorial
ms.date: 07/10/2019
ms.openlocfilehash: 64c5d80b5a2660164b21e71f06e847d5b11e40da
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964427"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>자습서: Azure Data Share를 사용하여 데이터 공유  

이 자습서에서는 새로운 Azure Data Share를 설정하여 Azure 조직 외부의 고객 및 파트너와 데이터를 공유하는 방법을 알아봅니다. 

이 자습서에서 학습할 방법은 다음과 같습니다.

> [!div class="checklist"]
> * Data Share를 만듭니다.
> * Data Share에 데이터 세트를 추가합니다.
> * Data Share에 대해 스냅샷 일정을 사용하도록 설정합니다. 
> * Data Share에 받는 사람을 추가합니다. 

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 수신자 Azure 로그인 이메일 주소(이메일 별칭을 사용하면 작동하지 않음).

### <a name="share-from-a-storage-account"></a>스토리지 계정에서 공유:

* Azure Storage 계정: 아직 없는 경우 [Azure Storage 계정](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)을 만들 수 있습니다.
* 스토리지 계정에 쓸 수 있는 권한으로, *Microsoft.Storage/storageAccounts/write*에 있습니다. 이 권한은 기여자 역할에 있습니다.
* 스토리지 계정에 역할 할당을 추가할 수 있는 권한입니다. 이 권한은 *Microsoft.Authorization/role assignments/write*에 있습니다. 이 권한은 소유자 역할에 있습니다. 


### <a name="share-from-a-sql-based-source"></a>SQL 기반 소스에서 공유:

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
       *<share_acc_name>* 은 Data Share 계정의 이름입니다. 아직 Data Share 리소스를 만들지 않은 경우 나중에 이 필수 조건으로 돌아갈 수 있습니다.  

* 공유하려는 테이블 및/또는 보기를 탐색하고 선택할 수 있는 ‘db_datareader’가 포함된 Azure SQL Database 사용자입니다. 

* 클라이언트 IP SQL Server 방화벽 액세스. 이 작업은 다음 단계를 통해 수행할 수 있습니다. 
    1. Azure Portal의 SQL 서버에서 *방화벽 및 가상 네트워크*로 이동합니다.
    1. **켜짐** 토글을 클릭하여 Azure 서비스에 대한 액세스를 허용합니다.
    1. **+ 클라이언트 IP 추가**를 클릭하고 **저장**을 클릭합니다. 클라이언트 IP 주소는 변경될 수 있습니다. IP 범위를 추가할 수도 있습니다. 

### <a name="share-from-azure-data-explorer"></a>Azure Data Explorer에서 공유
* 공유하려는 데이터베이스가 있는 Azure Data Explorer 클러스터.
* Azure Data Explorer 클러스터에 쓸 수 있는 권한으로, *Microsoft.Kusto/clusters/write*에 있습니다. 이 권한은 기여자 역할에 있습니다.
* Azure Data Explorer 클러스터에 역할 할당을 추가할 수 있는 권한으로, *Microsoft.Authorization/role assignments/write*에 있습니다. 이 권한은 소유자 역할에 있습니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-data-share-account"></a>Data Share 계정 만들기

Azure 리소스 그룹에서 Azure Data Share 리소스를 만듭니다.

1. 포털의 왼쪽 상단 모서리에서 **리소스 만들기** 단추(+)를 선택합니다.

1. *Data Share*를 검색합니다.

1. Data Share를 선택하고 **만들기**를 선택합니다.

1. 다음 정보를 사용하여 Azure Data Share 리소스의 기본 세부 정보를 채웁니다. 

     **설정** | **제안 값** | **필드 설명**
    |---|---|---|
    | 속성 | *datashareacount* | 데이터 공유 계정의 이름을 지정합니다. |
    | Subscription | 사용자의 구독 | 데이터 공유 계정에 사용할 Azure 구독을 선택합니다.|
    | Resource group | *test-resource-group* | 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다. |
    | 위치 | *미국 동부 2* | 데이터 공유 계정에 대한 지역을 선택합니다.
    | | |

1. **만들기**를 선택하여 데이터 공유 계정을 프로비저닝합니다. 새 데이터 공유 계정을 프로비저닝하는 데 일반적으로 2분 정도 걸립니다. 

1. 배포가 완료되면 **리소스로 이동**을 선택합니다.

## <a name="create-a-data-share"></a>Data Share 만들기

1. Data Share 개요 페이지로 이동합니다.

    ![데이터 공유](./media/share-receive-data.png "데이터 공유") 

1. **Start sharing your data**(데이터 공유 시작)를 선택합니다.

1. **만들기**를 선택합니다.   

1. Data Share에 대한 세부 정보를 채웁니다. 이름, 공유 유형, 공유 콘텐츠의 설명 및 사용 약관(선택 사항)을 지정합니다. 

    ![EnterShareDetails](./media/enter-share-details.png "공유 세부 정보 입력") 

1. **계속**을 선택합니다.

1. Data Share에 데이터 세트를 추가하려면 **데이터 세트 추가**를 선택합니다. 

    ![데이터 세트](./media/datasets.png "데이터 세트")

1. 추가하려는 데이터 세트 형식을 선택합니다. 이전 단계에서 선택한 공유 유형(스냅샷 또는 내부)에 따라 다른 데이터 세트 유형 목록이 표시됩니다. Azure SQL Database 또는 Azure SQL Data Warehouse에서 공유하는 경우 일부 SQL 자격 증명을 입력하라는 메시지가 표시됩니다. 필수 구성 요소의 일부로 만든 사용자를 사용하여 인증합니다.

    ![AddDatasets](./media/add-datasets.png "데이터 세트 추가")    

1. 공유하려는 개체로 이동하여 '데이터 세트 추가'를 선택합니다. 

    ![SelectDatasets](./media/select-datasets.png "데이터 세트 선택")    

1. 받는 사람 탭에서 '+ 받는 사람 추가'를 선택하여 데이터 소비자의 이메일 주소를 입력합니다. 

    ![AddRecipients](./media/add-recipient.png "수신자 추가") 

1. **계속**을 선택합니다.

1. 스냅샷 공유 유형을 선택한 경우 데이터 소비자에게 데이터 업데이트를 제공하도록 스냅샷 일정을 구성할 수 있습니다. 

    ![EnableSnapshots](./media/enable-snapshots.png "스냅샷 사용") 

1. 시작 시간과 되풀이 간격을 선택합니다. 

1. **계속**을 선택합니다.

1. 검토 + 만들기 탭에서 패키지 콘텐츠, 설정, 받는 사람 및 동기화 설정을 검토합니다. **만들기**

이제 Azure Data Share가 생성되었고 Data Share의 받는 사람이 초대를 수락할 준비가 되었습니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Share를 만들고 받는 사람을 초대하는 방법을 알아보았습니다. 데이터 소비자가 데이터 공유를 수락하고 받는 방법을 알아보려면, [데이터 수락 및 받기](subscribe-to-data-share.md) 자습서를 계속 진행하세요. 
