---
title: Configuration Manager 클라이언트와 함께 Azure 업데이트 관리 사용
description: 이 문서는이 솔루션을 사용 하 여 Microsoft 엔드포인트 Configuration Manager 구성 하 여 소프트웨어 업데이트를 ConfigMgr 클라이언트에 배포 하는 데 도움을 주기 위한 것입니다.
services: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: f0ca836e3b53c3cce755d45b50fe168073f0bbaa
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513136"
---
# <a name="deploy-updates-to-microsoft-endpoint-configuration-manager-clients-with-update-management"></a>업데이트 관리를 사용 하 여 클라이언트 Configuration Manager Microsoft 엔드포인트에 업데이트 배포

Microsoft 엔드포인트에 투자 하 여 Pc, 서버 및 모바일 장치를 관리 하는 고객은 소프트웨어 업데이트 관리 (SUM) 주기의 일부로 소프트웨어 업데이트를 관리 하는 데에도 강도 및 완성도를 Configuration Manager 합니다.

Configuration Manager에서 소프트웨어 업데이트 배포를 만들고 미리 준비하여 관리되는 Windows 서버를 보고 및 업데이트하고, [업데이트 관리 솔루션](automation-update-management.md)을 사용하여 완료된 업데이트 배포에 대한 자세한 상태를 확인할 수 있습니다. 업데이트 준수 보고를 위해 Configuration Manager를 사용 하지만 Windows 서버를 사용 하 여 업데이트 배포를 관리 하지 않는 경우 보안 업데이트가 업데이트 관리 솔루션으로 관리 되는 동안 Configuration Manager에 대한 보고를 계속할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

* Automation 계정에 추가된 [업데이트 관리 솔루션](automation-update-management.md)이 있어야 합니다.
* 현재 Configuration Manager 환경에서 관리 하는 Windows server도 업데이트 관리 솔루션이 사용 하도록 설정 된 Log Analytics 작업 영역에 보고 해야 합니다.
* 이 기능은 Configuration Manager 현재 분기 버전 1606 이상에서 사용할 수 있습니다. Configuration Manager 중앙 관리 사이트나 독립 실행형 기본 사이트를 Azure Monitor 로그 및 가져오기 컬렉션과 통합 하려면 [Azure Monitor 로그에 Configuration Manager 연결](../azure-monitor/platform/collect-sccm.md)을 검토 합니다.  
* Windows 에이전트는 WSUS(Windows Server Update Services) 서버와 통신하도록 구성되거나 Configuration Manager에서 보안 업데이트를 받지 않는 경우 Microsoft Update에 대한 액세스 권한이 있어야 합니다.   

주로 기존 Configuration Manager 환경으로 Azure IaaS에 호스트되는 클라이언트를 관리하는 방법은 Azure 데이터 센터와 사용자 인프라 사이의 연결에 따라 달라집니다. 이 연결은 디자인 변경에 영향을 줍니다. 사용자는 Configuration Manager 인프라 및 그러한 필수 변경 사항을 지원하기 위해 관련된 비용을 마련해야 할 수도 있습니다. 계속하기 전에 평가해야 하는 계획 고려 사항에 대해 알아보려면 [Azure에서 Configuration Manager - 질문과 대답](https://docs.microsoft.com/configmgr/core/understand/configuration-manager-on-azure#networking)을 검토하세요.

## <a name="configuration"></a>구성

### <a name="manage-software-updates-from-configuration-manager"></a>Configuration Manager에서 소프트웨어 업데이트 관리 

Configuration Manager에서 업데이트 배포 관리를 계속하려는 경우 다음 단계를 수행합니다. Azure Automation에서 Configuration Manager에 연결하여 Log Analytics 작업 영역에 연결된 클라이언트 컴퓨터에 업데이트를 적용합니다. 업데이트 콘텐츠는 배포를 Configuration Manager에서 관리하는 경우 클라이언트 컴퓨터 캐시에서 사용할 수 있습니다.

1. [소프트웨어 업데이트 배포](https://docs.microsoft.com/configmgr/sum/deploy-use/deploy-software-updates)에 설명 된 프로세스를 사용 하 여 Configuration Manager 계층 구조의 최상위 사이트에서 소프트웨어 업데이트 배포를 만듭니다. 표준 배포와 다르게 구성해야 하는 유일한 설정은 배포 패키지의 다운로드 동작을 제어하는 **소프트웨어 업데이트를 설치하지 않음** 옵션입니다. 이 동작은 다음 단계에서 예약된 업데이트 배포를 만들어 업데이트 관리 솔루션에서 관리됩니다.

1. Azure Automation에서 **업데이트 관리**를 선택합니다. [업데이트 배포 만들기](automation-tutorial-update-management.md#schedule-an-update-deployment)에서 설명한 단계에 따라 새 배포를 만들고, **유형** 드롭다운에서 **가져온 그룹**을 선택하여 적절한 Configuration Manager 컬렉션을 선택합니다. 다음 중요 사항에 주의하세요. a. 선택한 Configuration Manager 디바이스 컬렉션에 유지 관리 기간이 정의되어 있는 경우 컬렉션의 멤버는 예약된 배포에 정의된 **기간** 설정 대신 이 기간을 사용합니다.
    b. 대상 컬렉션의 구성원은 인터넷에 연결되어 있어야 합니다(직접, 프록시 서버 또는 Log Analytics 게이트웨이를 통해).

Azure Automation을 통해 업데이트 배포가 완료되면, 선택한 컴퓨터 그룹의 멤버인 대상 컴퓨터에서 로컬 클라이언트 캐시의 예약된 시간에 업데이트를 설치합니다. [업데이트 배포 상태를 확인하여](automation-tutorial-update-management.md#view-results-of-an-update-deployment) 배포 결과를 모니터링할 수 있습니다.

### <a name="manage-software-updates-from-azure-automation"></a>Azure Automation에서 소프트웨어 업데이트 관리

Configuration Manager 클라이언트인 Windows Server VM에 대한 업데이트를 관리하려면 이 솔루션에서 관리하는 모든 클라이언트에 대해 소프트웨어 업데이트 관리 기능을 사용하지 않도록 클라이언트 정책을 구성해야 합니다. 기본적으로 클라이언트 설정은 계층의 모든 디바이스를 대상으로 합니다. 이 정책 설정 및 구성 방법에 대한 자세한 내용은 [Configuration Manager에서 클라이언트 설정을 구성 하는 방법](https://docs.microsoft.com/configmgr/core/clients/deploy/configure-client-settings)을 참조 하세요.

이 구성 변경을 수행한 후에 [업데이트 배포 만들기](automation-tutorial-update-management.md#schedule-an-update-deployment)에서 설명한 단계에 따라 새 배포를 만들고, **유형** 드롭다운에서 **가져온 그룹**을 선택하여 적절한 Configuration Manager 컬렉션을 선택합니다.

## <a name="next-steps"></a>다음 단계

