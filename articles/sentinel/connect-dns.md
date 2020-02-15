---
title: Azure 센티널에서 DNS 데이터 연결 | Microsoft Docs
description: Azure 센티널에서 DNS 데이터를 연결 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 96515d81668bf172325f88e3e5bac8d8cccfa999
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190853"
---
# <a name="connect-your-domain-name-server"></a>도메인 이름 서버 연결

> [!IMPORTANT]
> Azure 센티널의 DNS 데이터 커넥터는 현재 공개 미리 보기로 제공 됩니다.
> 이 기능은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows에서 실행 되는 모든 DNS (도메인 이름 서버)를 Azure 센티널에 연결할 수 있습니다. 이 작업은 DNS 컴퓨터에 에이전트를 설치 하 여 수행 합니다. DNS 로그를 사용 하 여 DNS 서버에서 분석 및 감사 로그와 기타 관련 데이터를 수집, 분석 및 상관 관계를 구성 하 여 조직의 DNS 인프라에 대 한 보안, 성능 및 작업 관련 정보를 얻을 수 있습니다.

DNS 로그 연결을 사용 하도록 설정 하면 다음을 수행할 수 있습니다.
- 악의적인 도메인 이름을 확인하려는 클라이언트 식별
- 부실 리소스 레코드 식별
- 자주 쿼리되는 도메인 이름 및 대화량이 많은 DNS 클라이언트 식별
- DNS 서버에서 요청 부하 보기
- 동적 DNS 등록 오류 보기

## <a name="connected-sources"></a>연결된 소스

다음 표는 이 솔루션이 지원하는 연결된 원본을 설명합니다.

| **연결된 원본** | **지원** | **설명** |
| --- | --- | --- |
| [Windows 에이전트](../azure-monitor/platform/agent-windows.md) | yes | 솔루션이 Windows 에이전트에서 DNS 정보를 수집합니다. |
| [Linux 에이전트](../azure-monitor/learn/quick-collect-linux-computer.md) | 예 | 솔루션이 직접 Linux 에이전트에서 DNS 정보를 수집하지 않습니다. |
| [System Center Operations Manager 관리 그룹](../azure-monitor/platform/om-agents.md) | yes | 솔루션이 연결된 Operations Manager 관리 그룹의 에이전트에서 DNS 정보를 수집합니다. Operations Manager 에이전트에서 Azure Monitor로 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |
| [Azure Storage 계정](../azure-monitor/platform/collect-azure-metrics-logs.md) | 예 | Azure Storage가 솔루션에서 사용되지 않습니다. |

### <a name="data-collection-details"></a>데이터 수집 세부 정보

솔루션이 Log Analytics가 설치된 DNS 서버에서 DNS 인벤토리 및 DNS 이벤트 관련 데이터를 수집합니다. DNS Powershell cmdlet을 실행하여 DNS 서버, 영역 및 리소스 레코드 수와 같은 인벤토리 관련 데이터가 수집됩니다. 데이터가 2일마다 한 번씩 업데이트됩니다. 이벤트 관련 데이터가 Windows Server 2012 R2의 향상된 DNS 로깅 및 진단이 제공하는 [분석 및 감사 로그](https://technet.microsoft.com/library/dn800669.aspx#enhanc)를 통해 거의 실시간으로 수집됩니다.


## <a name="connect-your-dns-appliance"></a>DNS 어플라이언스 연결

1. Azure 센티널 포털에서 **데이터 커넥터** 를 선택 하 고 **DNS (미리 보기)** 타일을 선택 합니다.
1. DNS 컴퓨터가 Azure에 있는 경우:
    1. **Azure Windows 가상 머신에서 에이전트 설치를**클릭 합니다.
    1. **Virtual machines** 목록에서 Azure 센티널로 스트리밍할 DNS 컴퓨터를 선택 합니다. Windows VM 인지 확인 합니다.
    1. 해당 VM에 대해 열리는 창에서 **연결**을 클릭 합니다.  
    1. **DNS 커넥터** 창에서 **사용** 을 클릭 합니다. 

2. DNS 컴퓨터가 Azure VM이 아닌 경우:
    1. **비 Azure 컴퓨터에서 에이전트 설치를**클릭 합니다.
    1. **직접 에이전트** 창에서 **windows 에이전트 다운로드 (64 비트)** 또는 **windows 에이전트 다운로드 (32 비트)** 를 선택 합니다.
    1. DNS 컴퓨터에 에이전트를 설치 합니다. **작업 영역 ID**, **기본 키**및 **보조 키** 를 복사 하 고 설치 중에 메시지가 표시 되 면 사용 합니다.

3. DNS 로그에 대해 Log Analytics에서 관련 스키마를 사용 하려면 **Dnsevents**를 검색 합니다.

## <a name="validate"></a>유효성 검사 

Log Analytics에서 스키마 **Dnsevents** 를 검색 하 고 이벤트가 있는지 확인 합니다.

## <a name="troubleshooting"></a>문제 해결

조회 쿼리가 Azure 센티널에 표시 되지 않는 경우 쿼리가 제대로 표시 되도록 다음 단계를 수행 합니다.
1. [서버에서 DNS 분석 로그](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11))를 켭니다.
2. Log Analytics 컬렉션 목록에 DNSEvents가 표시 되는지 확인 합니다.
3. [Azure DNS 분석](../azure-monitor/insights/dns-analytics.md)을 설정 합니다.
4. Azure DNS 분석의 **구성**에서 설정을 변경 하 고 저장 한 다음 필요한 경우 다시 변경 하 고 다시 저장 합니다.
5. Azure DNS 분석을 선택 하 여 쿼리가 현재 표시 되는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 DNS 온-프레미스 어플라이언스를 Azure 센티널에 연결 하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
