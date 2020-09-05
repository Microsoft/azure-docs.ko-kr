---
title: Azure Data Share 문제 해결
description: Azure 데이터 공유를 사용 하 여 데이터 공유를 만들거나 받을 때 초대 및 오류와 관련 된 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-share
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: troubleshooting
ms.date: 08/14/2020
ms.openlocfilehash: c68c9dc961475d6916b1f00e7d4f596bfd8c77dd
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257796"
---
# <a name="troubleshoot-common-issues-in-azure-data-share"></a>Azure 데이터 공유의 일반적인 문제 해결 

이 문서에서는 Azure 데이터 공유에 대 한 일반적인 문제를 해결 하는 방법을 보여 줍니다. 

## <a name="azure-data-share-invitations"></a>Azure Data Share 초대 

경우에 따라, 새로운 사용자가 전송된 이메일 초대에서 **초대 수락**을 클릭하면 빈 초대 목록이 표시될 수 있습니다. 

![초대 없음](media/no-invites.png)

이는 다음과 같은 이유로 인해 발생할 수 있습니다.

* **Azure 데이터 공유 서비스가 azure 테 넌 트의 Azure 구독에 대 한 리소스 공급자로 등록 되어 있지 않습니다.** Azure 테 넌 트에 데이터 공유 리소스가 없는 경우이 문제가 발생 합니다. Azure 데이터 공유 리소스를 만들면 Azure 구독에 리소스 공급자가 자동으로 등록 됩니다. 이러한 단계를 수행 하 여 데이터 공유 서비스를 수동으로 등록할 수도 있습니다. 이러한 단계를 완료 하려면 Azure 참가자 역할이 있어야 합니다.

    1. Azure Portal에서 **구독**으로 이동합니다.
    1. Azure 데이터 공유 리소스를 만드는 데 사용할 구독을 선택 합니다.
    1. **리소스 공급자**를 클릭합니다.
    1. DataShare를 검색 **합니다.**
    1. **등록**을 클릭합니다. 

    이러한 단계를 완료 하려면 Azure 구독에 대 한 [Azure 참가자 역할이](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) 있어야 합니다. 

* **Azure 로그인 전자 메일 대신 초대가 전자 메일 별칭으로 전송 됩니다.** Azure 데이터 공유 서비스를 등록 했거나 Azure 테 넌 트에서 데이터 공유 리소스를 이미 만들었지만 아직 초대를 볼 수 없는 경우 공급자가 Azure 로그인 메일 주소 대신 받는 사람으로 메일 별칭을 입력 했기 때문일 수 있습니다. 데이터 공급자에 게 연락 하 여 전자 메일 별칭이 아닌 Azure 로그인 전자 메일 주소로 초대를 보냈는지 확인 합니다.

* **초대가 이미 수락 되었습니다.** 전자 메일의 링크를 누르면 보류 중인 초대가 나열 된 Azure Portal의 데이터 공유 초대 페이지로 이동 합니다. 이미 초대를 수락한 경우 데이터 공유 초대 페이지에 더 이상 표시 되지 않습니다. 수신 된 공유를 확인 하 고 대상 Azure 데이터 탐색기 클러스터 설정을 구성 하기 위해 초대를 수락 하는 데 사용한 데이터 공유 리소스로 이동 합니다.

## <a name="error-when-creating-or-receiving-a-new-share"></a>새 공유를 만들거나 받을 때 발생 하는 오류

"데이터 집합을 추가 하지 못했습니다."

"데이터 집합을 매핑하지 못함"

"데이터 공유 리소스 x에서 y에 대 한 액세스를 부여할 수 없습니다."

"X에 대 한 적절 한 권한이 없습니다."

"Azure 데이터 공유 계정에 대 한 쓰기 권한을 하나 이상의 선택한 리소스에 추가할 수 없습니다."

새 공유 또는 매핑 데이터 집합을 만들 때 위의 오류가 발생 하면 Azure 데이터 저장소에 대 한 권한이 부족 하기 때문일 수 있습니다. 필요한 권한에 대 한 [역할 및 요구 사항](concepts-roles-permissions.md) 을 참조 하세요. 

일반적으로 참가자 역할에 존재 하는 Azure 데이터 저장소에서 데이터를 공유 하거나 수신 하려면 쓰기 권한이 있어야 합니다. 

Azure 데이터 저장소에서 데이터를 처음 공유 하거나 수신 하는 경우에는 일반적으로 소유자 역할에 존재 하는 *Microsoft 권한 부여/역할 할당/쓰기* 권한이 필요 합니다. Azure 데이터 저장소 리소스를 만든 경우에도 리소스 소유자를 자동으로 만들지 않습니다. 적절 한 권한이 있는 Azure 데이터 공유 서비스는 데이터 공유 리소스의 관리 id에 데이터 저장소에 대 한 액세스 권한을 자동으로 부여 합니다. 이 프로세스를 적용 하는 데 몇 분 정도 걸릴 수 있습니다. 이러한 지연으로 인해 오류가 발생 하는 경우 몇 분 후에 다시 시도 하세요.

SQL 기반 공유에는 추가 권한이 필요 합니다. 자세한 내용은 SQL 기반 공유 문제 해결을 참조 하세요.

## <a name="troubleshooting-sql-based-sharing"></a>SQL 기반 공유 문제 해결

"사용자 x가 SQL Database"에 없습니다.

SQL 기반 원본에서 데이터 집합을 추가할 때이 오류가 발생 하는 경우 SQL Database에서 Azure 데이터 공유 관리 되는 id에 대 한 사용자를 만들지 않았기 때문일 수 있습니다.  이 문제를 해결 하려면 다음 스크립트를 실행 합니다.

```sql
    create user "<share_acct_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acct_name>";
```      
데이터 집합을 SQL 기반 대상에 매핑할 때이 오류가 발생 하는 경우 SQL Server에서 Azure 데이터 공유 관리 되는 id에 대 한 사용자를 만들지 않았기 때문일 수 있습니다.  이 문제를 해결 하려면 다음 스크립트를 실행 합니다.

```sql
    create user "<share_acc_name>" from external provider; 
    exec sp_addrolemember db_datareader, "<share_acc_name>"; 
    exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
    exec sp_addrolemember db_ddladmin, "<share_acc_name>";
```
*<share_acc_name>* 은 Data Share 리소스의 이름입니다.      

[데이터 공유](share-your-data.md) 및 [데이터 수락 및 받기](subscribe-to-data-share.md) 자습서에 나열 된 모든 필수 구성 요소를 준수 하는지 확인 합니다.

## <a name="snapshot-failed"></a>스냅숏 실패
스냅숏은 다양 한 이유로 인해 실패할 수 있습니다. 스냅숏의 시작 시간을 클릭 하 고 각 데이터 집합의 상태를 클릭 하 여 자세한 오류 메시지를 찾을 수 있습니다. 

오류 메시지가 사용 권한과 관련 된 경우 데이터 공유 서비스에 필요한 권한이 있는지 확인 합니다. 자세한 내용은 [역할 및 요구 사항](concepts-roles-permissions.md) 을 참조 하세요. 스냅숏을 처음 사용 하는 경우 데이터 공유 리소스에 Azure 데이터 저장소에 대 한 액세스 권한을 부여 하는 데 몇 분 정도 걸릴 수 있습니다. 몇 분 정도 기다린 후 다시 시도 하세요.

## <a name="next-steps"></a>다음 단계

데이터 공유를 시작하는 방법을 알아보려면 [데이터 공유](share-your-data.md) 자습서로 계속 진행하세요. 

데이터를 수신 하는 방법을 알아보려면 [데이터 수락 및 받기](subscribe-to-data-share.md) 자습서를 계속 진행 합니다.

