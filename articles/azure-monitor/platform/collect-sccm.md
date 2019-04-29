---
title: Azure Monitor에 구성 관리자 연결 | Microsoft Docs
description: 이 문서에서는 Azure Monitor에서 작업 영역에 Configuration Manager를 연결 하 고 데이터 분석을 시작 하는 단계를 보여 줍니다.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741665"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Azure Monitor에 Configuration Manager 연결
System Center Configuration Manager 환경의 장치 컬렉션 데이터를 동기화 하려면 Azure Monitor에 연결할 수 있으며 Azure Monitor 및 Azure Automation에서 이러한 컬렉션을 참조할 수 있습니다.  

## <a name="prerequisites"></a>필수 조건

Azure Monitor는 System Center Configuration Manager 현재 분기, 1606 이상 버전을 지원합니다.  

## <a name="configuration-overview"></a>구성 개요
다음 단계는 Azure Monitor를 사용 하 여 Configuration Manager 통합을 구성 하는 단계를 요약 합니다.  

1. Azure Portal에서 Configuration Manager를 웹 애플리케이션 및/또는 Web API 앱으로 등록하고 Azure Active Directory에서 등록할 때 사용한 클라이언트 ID와 클라이언트 암호 키가 있는지 확인합니다. 이 단계를 수행하는 방법에 대한 자세한 내용은 [포털 사용하여 리소스에 액세스할 수 있는 Active Directory 애플리케이션 및 서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.
2. Azure portal의 [Azure Monitor에 액세스할 수 있는 권한이 있는 Configuration Manager (등록 된 웹 앱)을 부여](#grant-configuration-manager-with-permissions-to-log-analytics)합니다.
3. Configuration Manager에서 OMS 연결 추가 마법사를 사용하여 연결을 추가합니다.
4. Configuration Manager에서 암호 또는 클라이언트 비밀 키가 만료되거나 분실된 경우 연결 속성을 업데이트합니다.
5. Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행하는 컴퓨터에서 [Microsoft Monitoring Agent를 다운로드 및 설치](#download-and-install-the-agent)합니다. 에이전트는 Azure Monitor에서 Log Analytics 작업 영역에 Configuration Manager 데이터를 보냅니다.
6. Azure Monitor에서 [Configuration Manager에서 컬렉션을 가져옵니다](#import-collections) 컴퓨터 그룹으로 합니다.
7. Azure Monitor에서으로 Configuration Manager에서 데이터를 볼 [컴퓨터 그룹](computer-groups.md)합니다.

자세한 내용은 Configuration Manager에서 Azure Monitor에 연결 하는 방법에 대 한 [Microsoft Log Analytics 작업 영역에 Configuration Manager에서 데이터 동기화](https://technet.microsoft.com/library/mt757374.aspx)합니다.

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager에 Log Analytics에 대한 사용 권한 제공
다음 절차에서는 Log Analytics 작업 영역의 *Contributor* 역할을 Configuration Manager에 대해 이전에 만든 AD 애플리케이션 및 서비스 주체에 부여합니다.  작업 영역이 아직 없는 경우 [Azure Monitor에서 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md) 계속 하기 전에 합니다.  이를 통해 Configuration Manager는 Log Analytics 작업 영역을 인증하고 연결할 수 있습니다.  

> [!NOTE]
> 지정 해야 사용 권한을 Log Analytics 작업 영역에서 Configuration Manager에 대 한 합니다. 그렇지 않고 Configuration Manager에서 구성 마법사를 사용하면 오류 메시지가 표시됩니다.
>

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
2. Log Analytics 작업 영역 목록에서 수정할 작업 영역을 선택합니다.
3. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
4. [액세스 제어(IAM)] 페이지에서 **역할 할당 추가**를 클릭합니다. 그러면 **역할 할당 추가** 창이 나타납니다.
5. **역할 할당 추가** 창의 **역할** 드롭다운 목록 아래에서 **기여자** 역할을 선택합니다.  
6. **다음에 대한 액세스 할당** 드롭다운 목록 아래에서 이전에 AD에서 만든 Configuration Manager 애플리케이션을 선택한 다음, **확인**을 클릭합니다.  

## <a name="download-and-install-the-agent"></a>에이전트 다운로드 및 설치
문서를 검토 [Azure Monitor에서 Azure에 연결 하는 Windows 컴퓨터](agent-windows.md) Configuration Manager 서비스 연결 지점을 호스트 하는 컴퓨터에서 Microsoft Monitoring Agent를 설치 하는 데 사용할 수 있는 방법을 이해 하려면 사이트 시스템 역할입니다.  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>구성 관리자에 Log Analytics 연결 추가
Log Analytics 연결을 추가하려면 구성 관리자 환경에 온라인 모드를 위해 구성된 [서비스 연결점](https://technet.microsoft.com/library/mt627781.aspx)이 있어야 합니다.

1. 구성 관리자의 **관리** 작업 영역에서 **OMS 커넥터**를 선택합니다. 그러면 **Log Analytics 연결 추가 마법사**가 열립니다. **다음**을 선택합니다.

   >[!NOTE]
   >OMS는 이제 Azure Monitor의 기능인 Log Analytics 이라고 합니다.
   
2. **일반** 화면에서 아래의 작업을 완료했는지와 각 항목에 대한 상세 정보가 있는지 확인하고 **다음**을 선택합니다.

   1. Azure Portal에서 구성 관리자를 웹 애플리케이션 및/또는 웹 API 앱으로 등록했는지 여부 및 [등록 시 지정한 클라이언트 ID](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md)가 있는지 여부
   2. Azure Portal에서 Azure Active Directory에 등록된 앱의 앱 비밀 키를 만들었는지 여부  
   3. Azure portal에서 Azure Monitor에서 Log Analytics 작업 영역에 대 한 액세스 권한이 있는 등록된 된 웹 앱을 제공 했습니다.  
      ![Log Analytics 마법사 일반 페이지에 연결](./media/collect-sccm/sccm-console-general01.png)
3. 에 **Azure Active Directory** 화면에서 제공 하 여 Log Analytics 작업 영역 연결 설정을 구성 하 **테 넌 트**를 **클라이언트 ID**, 및 **클라이언트 암호 키**을 선택한 후 **다음**합니다.  
   ![Log Analytics 마법사 Azure Active Directory 페이지에 연결](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. 나머지 절차를 성공적으로 완료한 경우 이 페이지에 **OMS 연결 구성** 화면이 자동으로 나타납니다. **Azure 구독**, **Azure 리소스 그룹**, **Operations Management Suite 작업 영역**에 대한 연결 설정 정보가 나타납니다.  
   ![Log Analytics 마법사 Log Analytics 페이지에 연결](./media/collect-sccm/sccm-wizard-configure04.png)
5. 마법사는 사용자가 입력 한 정보를 사용 하 여 Log Analytics 작업 영역에 연결 합니다. 서비스와 동기화하려는 디바이스 컬렉션을 선택한 다음, **추가**를 클릭합니다.  
   ![컬렉션 선택](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. **요약** 화면에서 연결 설정을 확인하고 **다음**을 선택합니다. **진행률** 화면에 연결 상태와 **완료**가 차례로 나타납니다.

> [!NOTE]
> Azure Monitor을 계층의 최상위 계층 사이트에 연결 해야 합니다. Azure Monitor에 독립 실행형 기본 사이트를 연결 하 고 다음 환경에 중앙 관리 사이트를 추가 하는 경우 삭제 하 고 다시 새 계층 안에서 연결 해야 합니다.
>
>

Azure Monitor에 Configuration Manager를 연결 하 게 추가 하 고 또는 컬렉션을 제거 하 고, 연결의 속성을 볼 수 있습니다.

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics 작업 영역 연결 속성 업데이트
암호 또는 클라이언트 암호 키가 만료되거나 분실된 경우 Log Analytics 연결 속성을 수동으로 업데이트해야 합니다.

1. Configuration Manager에서 **Cloud Services**로 이동한 다음 **OMS 커넥터**를 선택하여 **OMS 연결 속성** 페이지를 엽니다.
2. 이 페이지에서 **Azure Active Directory** 탭을 클릭하여 **테넌트**, **클라이언트 ID**, **클라이언트 비밀 키 만료**를 클릭합니다. **클라이언트 비밀 키**가 만료되었는지 **확인**합니다.

## <a name="import-collections"></a>컬렉션 가져오기
Configuration Manager 서비스 연결을 실행 하는 컴퓨터에서 지점 사이트 시스템 역할, Azure에서 Configuration Manager에서 컬렉션을 가져오려면 다음 단계는 Configuration Manager에 대 한 Log Analytics 연결 추가 하 고 에이전트를 설치 후 컴퓨터 그룹으로 모니터링 합니다.

계층 구조에서 디바이스 컬렉션을 가져오기 위한 초기 구성을 완료한 후에는 멤버 자격을 최신으로 유지하기 위해 3시간마다 컬렉션 멤버 정보가 검색됩니다. 언제든지 이를 비활성화하도록 선택할 수 있습니다.

1. Azure Portal의 왼쪽 위 모서리에 있는 **모든 서비스**를 클릭합니다. 리소스 목록에서 **Log Analytics**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics 작업 영역**을 선택합니다.
2. Log Analytics 작업 영역 목록에서 Configuration Manager를 등록할 작업 영역을 선택합니다.  
3. **고급 설정**을 선택합니다.
4. **컴퓨터 그룹**을 선택한 다음, **SCCM**을 선택합니다.  
5. **구성 관리자 컬렉션 멤버 자격 가져오기**를 선택한 다음 **저장**을 클릭합니다.  
   ![컴퓨터 그룹 - SCCM 탭](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>구성 관리자의 데이터 보기
Configuration Manager에 대 한 Log Analytics 연결 추가 Configuration Manager 서비스 연결 지점 사이트 시스템 역할을 실행 하는 컴퓨터에 에이전트를 설치할 한 후 에이전트에서 데이터는 Azure Monitor에서 Log Analytics 작업 영역에 전송 됩니다. Azure Monitor에서 Configuration Manager 컬렉션으로 나타납니다 [컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md)합니다. **Settings\Computer Groups** 아래 **Configuration Manager** 페이지에서 그룹을 볼 수 있습니다.

컬렉션을 가져오면 컬렉션 멤버 자격이 있는 컴퓨터 중 삭제된 컴퓨터 수를 확인할 수 있습니다. 또한 가져온 컬렉션 수도 확인할 수 있습니다.

![컴퓨터 그룹 - SCCM 탭](./media/collect-sccm/sccm-computer-groups02.png)

각 항목을 클릭하면 가져온 그룹 전체 또는 각 그룹에 속하는 전체 컴퓨터가 표시된 검색이 열립니다. [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용하여 구성 관리자 데이터의 자세한 분석을 시작할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [로그 검색](../../azure-monitor/log-query/log-query-overview.md)을 사용하여 구성 관리자 데이터에 대한 자세한 정보를 볼 수 있습니다.
