---
title: Azure Monitor에 Configuration Manager 연결 | Microsoft Docs
description: 이 문서에서는 Azure Monitor의 작업 영역에 Configuration Manager 연결 하 고 데이터 분석을 시작 하는 단계를 보여 줍니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 5b5af034b116ec1cdcefc811630683c9f560c840
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513666"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Azure Monitor에 Configuration Manager 연결
Microsoft 엔드포인트 Configuration Manager 환경을 Azure Monitor에 연결 하 여 장치 컬렉션 데이터를 동기화 하 고 Azure Monitor 및 Azure Automation에서 이러한 컬렉션을 참조할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

Azure Monitor Configuration Manager 현재 분기, 버전 1606 이상을 지원 합니다.

>[!NOTE]
>Log Analytics 작업 영역을 사용 하 여 Configuration Manager 연결 하는 기능은 선택 사항이 며 기본적으로 사용 하도록 설정 되어 있지 않습니다. 이 기능은 사용하기 전에 활성화해야 합니다. 자세한 내용은 [업데이트에서 선택적 기능 사용](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)을 참조하세요.

## <a name="configuration-overview"></a>구성 개요

다음 단계는 Azure Monitor와 Configuration Manager 통합을 구성 하는 단계를 요약 합니다.  

1. Azure Active Directory에서 Configuration Manager를 웹 응용 프로그램 및/또는 웹 API 앱으로 등록 하 고 Azure Active Directory에서 등록의 클라이언트 ID 및 클라이언트 암호 키가 있는지 확인 합니다. 이 단계를 수행하는 방법에 대한 자세한 내용은 [포털 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

2. Azure Active Directory에서 [Azure Monitor에 대 한 액세스 권한이 있는 Configuration Manager (등록 된 웹 앱)를 부여](#grant-configuration-manager-with-permissions-to-log-analytics)합니다.

3. Configuration Manager에서 **Azure 서비스** 마법사를 사용 하 여 연결을 추가 합니다.

4. Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행 하는 컴퓨터에 [Windows 용 Log Analytics 에이전트를 다운로드 하 여 설치](#download-and-install-the-agent) 합니다. 에이전트는 Azure Monitor의 Log Analytics 작업 영역에 Configuration Manager 데이터를 보냅니다.

5. Azure Monitor에서 컴퓨터 그룹으로 [Configuration Manager의 컬렉션을 가져옵니다](#import-collections) .

6. Azure Monitor에서 [컴퓨터 그룹](computer-groups.md)으로 Configuration Manager의 데이터를 봅니다.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager에 Log Analytics에 대한 사용 권한 제공

다음 절차에서는 Log Analytics 작업 영역의 *Contributor* 역할을 Configuration Manager에 대해 이전에 만든 AD 애플리케이션 및 서비스 주체에 부여합니다. 작업 영역이 아직 없는 경우 계속 진행 하기 전에 [Azure Monitor에서 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md) 를 참조 하세요. 이를 통해 Configuration Manager는 Log Analytics 작업 영역을 인증하고 연결할 수 있습니다.  

> [!NOTE]
> Configuration Manager에 대 한 Log Analytics 작업 영역에서 사용 권한을 지정 해야 합니다. 그렇지 않고 Configuration Manager에서 구성 마법사를 사용하면 오류 메시지가 표시됩니다.
>

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.

2. Log Analytics 작업 영역 목록에서 수정할 작업 영역을 선택합니다.

3. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.

4. [액세스 제어(IAM)] 페이지에서 **역할 할당 추가**를 클릭합니다. 그러면 **역할 할당 추가** 창이 나타납니다.

5. **역할 할당 추가** 창의 **역할** 드롭다운 목록 아래에서 **기여자** 역할을 선택합니다.  

6. **다음에 대한 액세스 할당** 드롭다운 목록 아래에서 이전에 AD에서 만든 Configuration Manager 애플리케이션을 선택한 다음, **확인**을 클릭합니다.  

## <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치

Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 호스트 하는 컴퓨터에 Windows 용 Log Analytics 에이전트를 설치 하는 데 사용할 수 있는 방법을 이해 하려면 [Azure에서 Azure Monitor에 windows 컴퓨터 연결](agent-windows.md) 문서를 검토 합니다.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Log Analytics 작업 영역에 Configuration Manager 연결

>[!NOTE]
> Log Analytics 연결을 추가 하려면 Configuration Manager 환경의 [서비스 연결 지점이](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) 온라인 모드용으로 구성 되어 있어야 합니다.

> [!NOTE]
> 계층의 최상위 계층 사이트를 Azure Monitor에 연결 해야 합니다. 독립 실행형 기본 사이트를 Azure Monitor에 연결한 다음 사용자 환경에 중앙 관리 사이트를 추가 하는 경우 새 계층 구조 내에서 연결을 삭제 하 고 다시 만들어야 합니다.

1. Configuration Manager의 **관리** 작업 영역에서 **클라우드 서비스** 를 선택한 후 **Azure 서비스**를 선택 합니다. 

2. **Azure 서비스** 를 마우스 오른쪽 단추로 클릭 하 고 **azure 서비스 구성**을 선택 합니다. **Azure 서비스 구성** 페이지가 나타납니다. 
   
3. **일반** 화면에서 아래의 작업을 완료했는지와 각 항목에 대한 상세 정보가 있는지 확인하고 **다음**을 선택합니다.

4. Azure 서비스 마법사의 Azure 서비스 페이지에서 다음을 수행 합니다.

    1. Configuration Manager의 개체 **이름**을 지정합니다.
    2. 서비스를 식별하는 데 도움이 되도록 선택적 **설명**을 지정합니다.
    3. Azure 서비스 **OMS 커넥터**를 선택 합니다.

    >[!NOTE]
    >이제 OMS를 Azure Monitor 기능인 Log Analytics 이라고 합니다.

5. **다음** 을 선택 하 여 Azure 서비스 마법사의 azure 앱 속성 페이지로 이동 합니다.

6. Azure 서비스 마법사의 **앱** 페이지에서 먼저 목록에서 azure 환경을 선택 하 고 **가져오기**를 클릭 합니다.

7. **앱 가져오기** 페이지에서 다음 정보를 지정 합니다.

    1. 앱에 대 한 **AZURE AD 테넌트 이름을** 지정 합니다.

    2. Azure ad 테 **넌 트 ID** 에 azure ad 테넌트를 지정 합니다. 이 정보는 Azure Active Directory **속성** 페이지에서 찾을 수 있습니다. 

    3. 응용 프로그램 **이름** 에 응용 프로그램 이름을 지정 합니다.

    4. 이전에 만든 Azure AD 앱의 응용 프로그램 ID 인 **클라이언트 id**를 지정 합니다.

    5. 만든 Azure AD 앱의 클라이언트 암호 키인 **비밀 키**를 지정 합니다.

    6. **비밀 키 만료**의 경우 키의 만료 날짜를 지정 합니다.

    7. **앱 ID uri**에 대해 이전에 만든 Azure AD 앱의 앱 id uri를 지정 합니다.

    8. **확인** 을 선택 하 고 오른쪽에 결과가 **확인 됨으로**표시 됩니다 .를 선택 합니다.

8. **구성** 페이지에서 정보를 검토 하 여 azure AD 응용 프로그램에 리소스 그룹에 대 한 충분 한 권한이 있음을 나타내는 azure **구독**, **Azure 리소스 그룹**및 **Operations Management Suite 작업 영역** 필드가 미리 채워져 있는지 확인 합니다. 필드가 비어 있으면 응용 프로그램에 필요한 권한이 없다는 것을 나타냅니다. 장치 컬렉션을 선택 하 여 작업 영역으로 이동 하 고 **추가**를 선택 합니다.

9. **설정 확인** 페이지의 옵션을 검토 하 고 **다음** 을 선택 하 여 연결 만들기 및 구성을 시작 합니다.

10. 구성이 완료 되 면 **완료** 페이지가 표시 됩니다. **닫기**를 선택합니다. 

Azure Monitor Configuration Manager 연결 된 후에는 컬렉션을 추가 또는 제거 하 고 연결의 속성을 볼 수 있습니다.

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics 작업 영역 연결 속성 업데이트

암호나 클라이언트 비밀 키가 만료 되거나 손실 된 경우 Log Analytics 연결 속성을 수동으로 업데이트 해야 합니다.

1. Configuration Manager의 **관리** 작업 영역에서 **Cloud Services** 를 선택 하 고 **oms 커넥터** 를 선택 하 여 **oms 연결 속성** 페이지를 엽니다.
2. 이 페이지에서 **Azure Active Directory** 탭을 클릭하여 **테넌트**, **클라이언트 ID**, **클라이언트 비밀 키 만료**를 클릭합니다. **클라이언트 비밀 키**가 만료되었는지 **확인**합니다.

## <a name="import-collections"></a>컬렉션 가져오기

Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행 하는 컴퓨터에 Log Analytics 연결을 추가 하 여 에이전트를 Configuration Manager 설치 하 고 설치한 후 다음 단계는 Azure의 Configuration Manager에서 컬렉션을 가져오는 것입니다. 컴퓨터 그룹으로 모니터링 합니다.

계층에서 장치 컬렉션을 가져오기 위한 초기 구성을 완료 한 후에는 구성원 자격을 최신 상태로 유지 하기 위해 3 시간 마다 수집 정보를 검색 합니다. 언제든지 이를 비활성화하도록 선택할 수 있습니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
2. Log Analytics 작업 영역 목록에서 Configuration Manager를 등록할 작업 영역을 선택합니다.  
3. **고급 설정**을 선택합니다.
4. **컴퓨터 그룹**을 선택한 다음, **SCCM**을 선택합니다.  
5. **구성 관리자 컬렉션 멤버 자격 가져오기**를 선택한 다음 **저장**을 클릭합니다.  
   
    ![컴퓨터 그룹 - SCCM 탭](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>구성 관리자의 데이터 보기

Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행 하는 컴퓨터에 에이전트를 Configuration Manager 및 설치 Log Analytics 연결을 추가 하면 에이전트의 데이터가 Azure Monitor의 Log Analytics 작업 영역으로 전송 됩니다. Azure Monitor에서 Configuration Manager 컬렉션은 [컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md)으로 표시 됩니다. **Settings\Computer Groups** 아래 **Configuration Manager** 페이지에서 그룹을 볼 수 있습니다.

컬렉션을 가져오면 컬렉션 멤버 자격이 있는 컴퓨터 중 삭제된 컴퓨터 수를 확인할 수 있습니다. 또한 가져온 컬렉션 수도 확인할 수 있습니다.

![컴퓨터 그룹 - SCCM 탭](./media/collect-sccm/sccm-computer-groups02.png)

하나를 클릭 하면 모든 가져온 그룹이 나 각 그룹에 속한 모든 컴퓨터를 표시 하는 로그 쿼리 편집기가 열립니다. [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용 하 여 컬렉션 멤버 자격 데이터를 자세히 분석할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용하여 구성 관리자 데이터에 대한 자세한 정보를 볼 수 있습니다.
