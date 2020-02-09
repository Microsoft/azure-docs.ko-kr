---
title: Azure 보안 제어-로깅 및 모니터링
description: 보안 제어 로깅 및 모니터링
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545503"
---
# <a name="security-control-logging-and-monitoring"></a>보안 제어: 로깅 및 모니터링

보안 로깅 및 모니터링은 Azure 서비스에 대 한 감사 로그를 사용, 확보 및 저장 하는 작업과 관련 된 작업을 중점적으로 다룹니다.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: 승인 된 시간 동기화 원본을 사용 하십시오.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

Microsoft는 Azure 리소스에 대 한 시간 원본을 유지 관리 하지만 계산 리소스에 대 한 시간 동기화 설정을 관리할 수 있는 옵션이 있습니다.

Azure 계산 리소스에 대 한 시간 동기화를 구성 하는 방법:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2.2: 중앙 보안 로그 관리 구성

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.2 | 6.5, 6.6 | Customer |

Azure Monitor를 통해 로그를 수집 하 여 엔드포인트 장치, 네트워크 리소스 및 기타 보안 시스템에 의해 생성 된 보안 데이터를 집계 합니다. Azure Monitor 내에서 Log Analytics 작업 영역을 사용 하 여 분석을 쿼리하고 수행 하 고 장기/보관 저장소에 Azure Storage 계정을 사용할 수 있습니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용할 수 있습니다. Azure 센티널을 등록 하는 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure Monitor를 사용 하 여 Azure 가상 컴퓨터 내부 호스트 로그를 수집 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Azure Monitor 및 타사 SIEM 통합을 시작 하는 방법:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Azure 리소스에 대 한 감사 로깅 사용

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.3 | 6.2, 6.3 | Customer |

감사, 보안 및 진단 로그에 액세스 하기 위해 Azure 리소스에 대 한 진단 설정을 사용 하도록 설정 합니다. 자동으로 사용할 수 있는 활동 로그에는 이벤트 원본, 날짜, 사용자, 타임 스탬프, 원본 주소, 대상 주소 및 기타 유용한 요소가 포함 됩니다.

Azure Monitor를 사용 하 여 플랫폼 로그 및 메트릭을 수집 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Azure의 로깅 및 다른 로그 유형 이해:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: 운영 체제에서 보안 로그 수집

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.4 | 6.2, 6.3 | Customer |

Microsoft에서 계산 리소스를 소유 하는 경우 Microsoft는 모니터링을 담당 합니다. 조직에서 계산 리소스를 소유 하 고 있는 경우이를 모니터링 하는 것은 사용자의 책임입니다. Azure Security Center를 사용 하 여 OS를 모니터링할 수 있습니다. 운영 체제에서 Security Center에 의해 수집 되는 데이터에는 OS 유형 및 버전, OS 로그 (Windows 이벤트 로그), 실행 중인 프로세스, 컴퓨터 이름, IP 주소 및 로그인 한 사용자가 포함 됩니다. 또한 Log Analytics 에이전트는 크래시 덤프 파일을 수집 합니다.

Azure Monitor를 사용 하 여 Azure 가상 컴퓨터 내부 호스트 로그를 수집 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

데이터 수집 Azure Security Center 이해:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2.5: 보안 로그 저장소 보존 구성

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.5 | 6.4 | Customer |

Azure Monitor 내에서 조직의 규정 준수 규정에 따라 Log Analytics 작업 영역 보존 기간을 설정 합니다. 장기/보관 저장소에 Azure Storage 계정을 사용 합니다.

Log Analytics 작업 영역에 대 한 로그 보존 매개 변수를 설정 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2.6: 로그를 모니터링 하 고 검토 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.6 | 6.7 | Customer |

비정상적인 동작에 대 한 로그를 분석 및 모니터링 하 고 정기적으로 결과를 검토 합니다. Azure Monitor의 Log Analytics 작업 영역을 사용 하 여 로그를 검토 하 고 로그 데이터에 대 한 쿼리를 수행 합니다.

또는 Azure 센티널 또는 타사 SIEM에 대 한 온보드 데이터를 사용할 수 있습니다. 

Azure 센티널을 등록 하는 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Log Analytics 작업 영역 이해:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Azure Monitor에서 사용자 지정 쿼리를 수행 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: 비정상적인 활동에 대해 경고를 사용 하도록 설정

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.7 | 6.8 | Customer |

보안 로그 및 이벤트에 있는 비정상적인 활동을 모니터링 하 고 경고 하기 위해 Log Analytics 작업 영역에서 Azure Security Center를 사용 합니다.

또는 온보드 데이터를 Azure 센티널로 설정할 수 있습니다.

Azure 센티널을 등록 하는 방법:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Azure Security Center에서 경고를 관리 하는 방법:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Log analytics 로그 데이터를 경고 하는 방법:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2.8: 맬웨어 방지 로깅을 중앙 집중화

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.8 | 8.6 | Customer |

Azure Virtual Machines 및 Cloud Services에 대해 맬웨어 방지 이벤트 수집을 사용 하도록 설정 합니다.

Virtual Machines에 대 한 Microsoft 맬웨어 방지를 구성 하는 방법:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

Cloud Services에 대 한 Microsoft 맬웨어 방지를 구성 하는 방법:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

Microsoft 맬웨어 방지 프로그램 이해:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2.9: DNS 쿼리 로깅을 사용 하도록 설정

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.9 | 8.7 | Customer |

DNS 로깅을 위한 타사 솔루션을 구현 합니다.

## <a name="210-enable-command-line-audit-logging"></a>2.10: 명령줄 감사 로깅을 사용 합니다.

| Azure ID | CIS Id | 책임 |
|--|--|--|
| 2.1 | 8.8 | Customer |

노드 단위로 콘솔 로깅 및 PowerShell 기록을 수동으로 구성 합니다.

## <a name="next-steps"></a>다음 단계

다음 보안 제어: [id 및 Access Control](security-control-identity-access-control.md) 을 참조 하세요.
