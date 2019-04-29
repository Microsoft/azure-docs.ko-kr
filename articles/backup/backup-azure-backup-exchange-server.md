---
title: System Center 2012 R2 DPM을 사용하여 Azure Backup에 Exchange 서버 백업
description: System Center 2012 R2 DPM을 사용하여 Azure Backup에 Exchange 서버를 백업하는 방법을 알아봅니다.
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: kasinh
ms.openlocfilehash: ef976667ec580ea75dd1b8566c7bdddf35eeb0fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60647266"
---
# <a name="back-up-an-exchange-server-to-azure-backup-with-system-center-2012-r2-dpm"></a>System Center 2012 R2 DPM을 사용하여 Azure Backup에 Exchange 서버 백업
이 문서에서는 System Center 2012 R2 Data Protection Manager(DPM) 서버를 구성하여 Azure Backup에 Microsoft Exchange server를 백업하는 방법을 설명합니다.  

## <a name="updates"></a>업데이트
Azure Backup을 사용하여 DPM 서버를 성공적으로 등록하려면 System Center 2012 R2 DPM 및 Azure Backup 에이전트의 최신 버전에 대한 최신 업데이트 롤업을 설치해야 합니다. [Microsoft 카탈로그](https://catalog.update.microsoft.com/v7/site/Search.aspx?q=System%20Center%202012%20R2%20Data%20protection%20manager)에서 최신 업데이트 롤업을 가져옵니다.

> [!NOTE]
> 이 문서의 예의 경우 Azure Backup 에이전트의 2.0.8719.0 버전을 설치하고 업데이트 롤업 6을 System Center 2012 R2 DPM에 설치합니다.
>
>

## <a name="prerequisites"></a>필수 조건
계속하기 전에 워크로드를 보호하기 위하여 Microsoft Azure Backup 사용을 위한 [필수 구성 요소](backup-azure-dpm-introduction.md#prerequisites-and-limitations) 를 모두 충족하는지 확인합니다. 이러한 필수 구성 요소는 다음과 같습니다.

* Azure 사이트에서 백업 자격 증명 모음을 만들었습니다.
* DPM 서버에 에이전트 및 자격 증명 모음 자격 증명을 다운로드했습니다.
* 에이전트는 DPM 서버에 설치됩니다.
* DPM 서버를 등록하는 데 자격 증명 모음 자격 증명이 사용되었습니다.
* Exchange 2016을 보호하는 경우, DPM 2012 R2 UR9 이상으로 업그레이드하세요.

## <a name="dpm-protection-agent"></a>DPM 보호 에이전트
Exchange 서버에서 DPM 보호 에이전트를 설치하려면 다음 이 단계를 수행합니다.

1. 방화벽이 올바르게 구성되어 있는지 확인합니다. [에이전트에 대한 방화벽 예외 구성](https://technet.microsoft.com/library/Hh758204.aspx)을 참조하세요.
2. DPM 관리자 콘솔에서 **관리 > 에이전트 > 설치**를 클릭하여 Exchange 서버에 에이전트를 설치합니다. 자세한 단계는 [DPM 보호 에이전트 설치](https://technet.microsoft.com/library/hh758186.aspx?f=255&MSPPError=-2147217396) 를 참조하세요.

## <a name="create-a-protection-group-for-the-exchange-server"></a>Exchange 서버에 보호 그룹 만들기
1. DPM 관리자 콘솔에서 **보호**를 클릭한 다음 도구 리본에서 **새로 만들기**를 클릭하여 **새 보호 그룹 만들기** 마법사를 엽니다.
2. **시작** 화면에서 **다음**을 클릭합니다.
3. **보호 그룹 형식 선택** 화면에서 **서버**를 선택하고 **다음**을 클릭합니다.
4. 보호하려는 Exchange 서버 데이터베이스를 선택하고 **다음**을 클릭합니다.

   > [!NOTE]
   > Exchange 2013을 보호하는 경우 [Exchange 2013 필수 구성 요소](https://technet.microsoft.com/library/dn751029.aspx)를 확인합니다.
   >
   >

    다음 예제에서는 Exchange 2010 데이터베이스를 선택합니다.

    ![그룹 구성원 선택](./media/backup-azure-backup-exchange-server/select-group-members.png)
5. 데이터 보호 방법을 선택합니다.

    보호 그룹의 이름을 지정하고 다음 옵션 모두를 선택합니다.

   * 디스크를 사용하여 단기 보호를 하려고 합니다.
   * 온라인 보호를 사용하려고 합니다.
6. **다음**을 클릭합니다.
7. Exchange Server 데이터베이스의 무결성을 확인하려는 경우 **Eseutil 실행하여 데이터 무결성 확인** 옵션을 선택합니다.

    이 옵션을 선택한 후에 Exchange 서버에서 **eseutil** 명령을 실행하여 생성되는 I/O 트래픽을 방지하기 위해 백업 일관성 확인 작업이 DPM 서버에서 실행됩니다.

   > [!NOTE]
   > 이 옵션을 사용하려면 DPM 서버에서 C:\Program Files\Microsoft System Center 2012 R2\DPM\DPM\bin 디렉터리로 Ese.dll 및 Eseutil.exe 파일을 복사해야 합니다. 그렇지 않으면 다음 오류가 트리거됩니다.  
   > ![eseutil 오류](./media/backup-azure-backup-exchange-server/eseutil-error.png)
   >
   >
8. **다음**을 클릭합니다.
9. **복사 Backup**에 대한 데이터베이스를 선택하고 **다음**을 클릭합니다.

   > [!NOTE]
   > 데이터베이스의 DAG 복사본 하나 이상에서 "전체 백업"을 선택하지 않으면 로그는 잘리지 않습니다.
   >
   >
10. **단기 백업**에 대한 목표를 구성하고 **다음**을 클릭합니다.
11. 사용 가능한 디스크 공간을 검토하고 **다음**을 클릭합니다.
12. DPM 서버가 초기 복제 만들 시기를 선택하고 **다음**을 클릭합니다.
13. 일관성 확인 옵션을 선택하고 **다음**을 클릭합니다.
14. Azure에 백업하려는 데이터베이스를 선택하고 **다음**을 클릭합니다. 예를 들면 다음과 같습니다.

    ![온라인 보호 데이터 지정](./media/backup-azure-backup-exchange-server/specify-online-protection-data.png)
15. **Azure Backup**에 대한 일정을 정의하고 **다음**을 클릭합니다. 예를 들면 다음과 같습니다.

    ![온라인 백업 일정 지정](./media/backup-azure-backup-exchange-server/specify-online-backup-schedule.png)

    > [!NOTE]
    > 온라인 복구 지점은 Express 전체 복구 지점을 기반으로 합니다. 따라서 Express 전체 복구 지점에 지정된 시간 후에 온라인 복구 지점을 예약해야 합니다.
    >
    >
16. **Azure Backup**에 대한 보존 정책을 구성하고 **다음**을 클릭합니다.
17. 온라인 복제 옵션을 선택하고 **다음**을 클릭합니다.

    큰 데이터베이스를 가지고 있다면 네트워크를 통해 만들 초기 백업에 시간이 오래 걸릴 수 있습니다. 이 문제를 방지하려면 오프라인 백업을 만들 수 있습니다.  

    ![온라인 보존 정책 지정](./media/backup-azure-backup-exchange-server/specify-online-retention-policy.png)
18. 설정을 확인한 다음 **그룹 만들기**를 클릭합니다.
19. **닫기**를 클릭합니다.

## <a name="recover-the-exchange-database"></a>Exchange 데이터베이스 복구
1. Exchange 데이터베이스를 복구하려면 DPM 관리자 콘솔에서 **복구** 를 클릭합니다.
2. 복구하려는 Exchange 데이터베이스를 찾습니다.
3. *복구 시간* 드롭 다운 목록에서 온라인 복구 지점을 선택합니다.
4. **복구**를 클릭하여 **복구 마법사**를 시작합니다.

온라인 복구 지점의 경우 다섯 가지 형식의 복구가 있습니다.

* **원본 Exchange 서버 위치에 복구:** 데이터가 원본 Exchange 서버에 복구됩니다.
* **Exchange 서버의 다른 데이터베이스에 복구:** 데이터가 다른 Exchange 서버의 다른 데이터베이스에 복구됩니다.
* **복구 데이터베이스에 복구:** 데이터가 Exchange RDB(Recovery Database)에 복구됩니다.
* **네트워크 폴더에 복사:** 데이터가 네트워크 폴더에 복구됩니다.
* **테이프에 복사:** DPM 서버에 연결되고 구성된 테이프 라이브러리 또는 독립 실행형 테이프 드라이브가 있는 경우 복구 지점은 사용 가능한 테이프에 복사됩니다.

    ![온라인 복제 선택](./media/backup-azure-backup-exchange-server/choose-online-replication.png)

## <a name="next-steps"></a>다음 단계
* [Azure Backup - FAQ](backup-azure-backup-faq.md)
