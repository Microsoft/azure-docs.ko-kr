---
title: Azure 센티널에 Syslog 데이터 연결 | Microsoft Docs
description: 어플라이언스와 센티널 사이에 Linux 컴퓨터의 에이전트를 사용 하 여 Syslog를 지 원하는 컴퓨터 또는 어플라이언스를 Azure 센티널에 연결 합니다. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7670d00a2dd25961a51d18c50c102e0f92b30975
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566151"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Syslog를 사용 하 여 Linux 기반 원본에서 데이터 수집

Linux에 대 한 Log Analytics 에이전트 (이전의 OMS 에이전트)를 사용 하 여 Linux 기반 Syslog 지원 컴퓨터 또는 어플라이언스에서 Azure 센티널로 이벤트를 스트리밍할 수 있습니다. 컴퓨터에 Log Analytics 에이전트를 직접 설치할 수 있는 모든 컴퓨터에 대해이 작업을 수행할 수 있습니다. 컴퓨터의 기본 Syslog 디먼은 지정 된 형식의 로컬 이벤트를 수집 하 고 에이전트에 로컬로 전달 하므로 Log Analytics 작업 영역으로 스트리밍합니다.

> [!NOTE]
> - 어플라이언스에서 Syslog를 **통해 CEF (일반 이벤트 형식)** 를 지 원하는 경우 더 완벽 한 데이터 집합이 수집 되 고 데이터는 컬렉션에서 구문 분석 됩니다. 이 옵션을 선택 하 고 [CEF를 사용 하 여 외부 솔루션 연결](connect-common-event-format.md)의 지침을 따르세요.
>
> - Log Analytics **는 rsyslog 또는** **syslog-기능** 디먼에서 보낸 메시지의 컬렉션을 지원 합니다. 여기서 rsyslog는 기본값입니다. Red Hat Enterprise Linux (RHEL), CentOS 및 Oracle Linux 버전 (**sy log**)의 버전 5에 있는 기본 syslog 디먼은 syslog 이벤트 수집에 대해 지원 되지 않습니다. 이 배포의 해당 버전에서 syslog 데이터를 수집하려면 rsyslog 디먼을 설치하고 sysklog를 대체하도록 구성해야 합니다.

## <a name="how-it-works"></a>작동 방식

**Syslog** 는 Linux에 공통적인 이벤트 로깅 프로토콜입니다. **Linux 용 Log Analytics 에이전트가** VM 또는 어플라이언스에 설치 된 경우 설치 루틴은 TCP 포트 25224의 에이전트로 메시지를 전달 하도록 로컬 Syslog 데몬을 구성 합니다. 그러면 에이전트는 HTTPS를 통해 Log Analytics 작업 영역으로 메시지를 보냅니다. 그러면 **Azure 센티널 > 로그**의 Syslog 테이블에 있는 이벤트 로그 항목으로 구문 분석 됩니다.

자세한 내용은 [Azure Monitor의 Syslog 데이터 원본](../azure-monitor/platform/data-sources-syslog.md)을 참조 하세요.

## <a name="configure-syslog-collection"></a>Syslog 컬렉션 구성

### <a name="configure-your-linux-machine-or-appliance"></a>Linux 컴퓨터 또는 어플라이언스 구성

1. Azure 센티널에서 **데이터 커넥터** 를 선택한 다음 **Syslog** 커넥터를 선택 합니다.

1. **Syslog** 블레이드에서 **커넥터 페이지 열기**를 선택 합니다.

1. Linux 에이전트를 설치 합니다. **에이전트를 설치할 위치 선택에서 다음을 수행 합니다.**
    
    **Azure Linux VM의 경우:**
      
    1. **Azure Linux 가상 컴퓨터에 에이전트 설치를**선택 합니다.
    
    1. **다운로드 & Azure Linux 가상 머신에 대 한 에이전트 설치 >** 링크를 클릭 합니다. 
    
    1. **Virtual machines** 블레이드에서 에이전트를 설치할 가상 컴퓨터를 클릭 한 다음 **연결**을 클릭 합니다. 연결 하려는 각 VM에 대해이 단계를 반복 합니다.
    
    **기타 Linux 컴퓨터의 경우:**

    1. **비 Azure Linux 컴퓨터에 에이전트 설치** 를 선택 합니다.

    1. & 다운로드를 클릭 하 여 **Azure Linux가 아닌 컴퓨터에 대 한 에이전트 설치 >** 링크를 클릭 합니다. 

    1. **에이전트 관리** 블레이드에서 **linux 서버** 탭을 클릭 한 다음 linux **용 에이전트 다운로드 및** 등록 명령을 복사 하 여 linux 컴퓨터에서 실행 합니다. 
    
   > [!NOTE]
   > 조직의 보안 정책에 따라 이러한 컴퓨터에 대 한 보안 설정을 구성 해야 합니다. 예를 들어 조직의 네트워크 보안 정책에 맞게 네트워크 설정을 구성 하 고, 보안 요구 사항에 맞게 디먼의 포트 및 프로토콜을 변경할 수 있습니다.

### <a name="configure-the-log-analytics-agent"></a>Log Analytics 에이전트 구성

1. Syslog 커넥터 블레이드의 아래쪽에서 **작업 영역 고급 설정 구성 >열기 ** 링크를 클릭 합니다.

1. **고급 설정** 블레이드에서 **데이터**  >  **Syslog**를 선택 합니다. 그런 다음 수집할 커넥터에 대 한 기능을 추가 합니다.
    
    - Syslog 어플라이언스에 포함 된 기능을 로그 헤더에 추가 합니다. 
    
    - 수집한 데이터를 사용 하 여 비정상적인 SSH 로그인 검색을 사용 하려는 경우 **auth** 및 **authpriv**를 추가 합니다. 자세한 내용은 [다음 섹션](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) 을 참조 하세요.

1. 모니터링 하려는 모든 기능을 추가 하 고 각 기능에 대 한 심각도 옵션을 조정한 경우 **내 컴퓨터에 아래 구성 적용**확인란을 선택 합니다.

1. **저장**을 선택합니다. 

1. VM 또는 어플라이언스에서 지정한 시설을 전송 하 고 있는지 확인 합니다.

1. **로그**에서 syslog 로그 데이터를 쿼리하려면 `Syslog` 쿼리 창에를 입력 합니다.

1. [Azure Monitor 로그 쿼리의 함수를 사용 하 여](../azure-monitor/log-query/functions.md) Syslog 메시지를 구문 분석 하는 데 설명 된 쿼리 매개 변수를 사용할 수 있습니다. 그런 다음 쿼리를 새 Log Analytics 함수로 저장 하 고 새 데이터 형식으로 사용할 수 있습니다.

> [!NOTE]
> **동일한 컴퓨터를 사용 하 여 일반 Syslog *및* cef 메시지 모두 전달**
>
>
> 기존 [Cef 로그 전달자 컴퓨터](connect-cef-agent.md) 를 사용 하 여 일반 Syslog 원본 에서도 로그를 수집 하 고 전달할 수 있습니다. 그러나 다음 단계를 수행 하 여 두 형식의 이벤트를 Azure 센티널로 보내지 않도록 하 여 이벤트를 중복 하는 것을 방지 해야 합니다.
>
>    이미 [CEF 원본에서 데이터 수집](connect-common-event-format.md)을 설정 하 고 위와 같이 Log Analytics 에이전트를 구성 했습니다.
>
> 1. CEF 형식으로 로그를 전송 하는 각 컴퓨터에서 Syslog 구성 파일을 편집 하 여 CEF 메시지를 보내는 데 사용 되는 기능을 제거 해야 합니다. 이러한 방식으로 CEF에서 전송 되는 기능은 Syslog로 보내지지 않습니다. 이 작업을 수행 하는 방법에 대 한 자세한 지침은 [Linux 에이전트에서 Syslog 구성](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) 을 참조 하세요.
>
> 1. Azure 센티널의 Syslog 구성과 에이전트의 동기화를 사용 하지 않도록 설정 하려면 해당 컴퓨터에서 다음 명령을 실행 해야 합니다. 이렇게 하면 이전 단계에서 변경한 구성 변경을 덮어쓰지 않습니다.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>비정상적인 SSH 로그인 검색을 위한 Syslog 커넥터 구성

> [!IMPORTANT]
> 비정상적인 SSH 로그인 검색은 현재 공개 미리 보기 상태입니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 센티널은 syslog 데이터에 기계 학습 (ML)을 적용 하 여 비정상적인 Secure Shell (SSH) 로그인 작업을 식별할 수 있습니다. 시나리오는 다음과 같습니다.

- 불가능 한 이동-두 로그인 이벤트의 기간 내에 도달할 수 없는 두 위치에서 두 개의 성공한 로그인 이벤트가 발생 하는 경우
- 예기치 않은 위치 – 성공적인 로그인 이벤트가 발생 한 위치를 의심 합니다. 예를 들어 위치는 최근에 표시 되지 않았습니다.
 
이 검색에는 Syslog 데이터 커넥터의 특정 구성이 필요 합니다. 

1. 이전 절차의 5 단계에는 **auth** 및 **authpriv** 가 모두 모니터링할 기능으로 선택 되어 있는지 확인 합니다. 심각도 옵션의 기본 설정은 모두 선택 된 상태로 유지 합니다. 예를 들면 다음과 같습니다.
    
    > [!div class="mx-imgBorder"]
    > ![비정상적인 SSH 로그인 검색에 필요한 기능](./media/connect-syslog/facilities-ssh-detection.png)

2. Syslog 정보를 수집할 수 있는 충분 한 시간을 허용 합니다. 그런 다음 **Azure 센티널-Logs**로 이동 하 여 다음 쿼리를 복사 하 여 붙여넣습니다.
    
    ```console
    Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    ```
    
    필요한 경우 **시간 범위** 를 변경 하 고 **실행**을 선택 합니다.
    
    결과 수가 0 이면 커넥터의 구성을 확인 하 고 모니터링 되는 컴퓨터에 쿼리를 위해 지정한 기간 동안 성공한 로그인 작업이 있는지 확인 합니다.
    
    결과 개수가 0 보다 크면 syslog 데이터가 비정상적인 SSH 로그인 검색에 적합 합니다. **분석**  >   **규칙 템플릿**  >  **(미리 보기) 비정상적인 SSH 로그인 검색**에서이 검색을 사용 하도록 설정 합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Syslog 온-프레미스 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](tutorial-monitor-your-data.md)하여 데이터를 모니터링합니다.

