---
title: Microsoft 끝점과 Azure Automation 업데이트 관리 통합 Configuration Manager
description: 이 문서에서는 업데이트 관리를 사용하여 Microsoft Endpoint Configuration Manager를 구성하여 소프트웨어 업데이트를 관리자 클라이언트에 배포하는 방법을 설명합니다.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 09c8ef818428157c7de8c3a87bcce8a7b80e62ea
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245913"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Microsoft 끝점과 업데이트 관리 통합 Configuration Manager

PC, 서버 및 모바일 디바이스를 관리하기 위해 Microsoft Endpoint Configuration Manager에 투자하는 고객들은 SUM(소프트웨어 업데이트 관리) 주기의 일환으로 이 강력하고 완성도 있는 소프트웨어 업데이트 관리를 신뢰합니다.

Microsoft Endpoint Configuration Manager에서 소프트웨어 업데이트 배포를 만들고 미리 준비 하 여 관리 되는 Windows 서버를 보고 하 고 업데이트할 수 있으며 [업데이트 관리](update-mgmt-overview.md)를 사용 하 여 완료 된 업데이트 배포의 자세한 상태를 가져올 수 있습니다. Microsoft Endpoint Configuration Manager를 사용 하 여 업데이트 준수 보고를 사용 하지만 Windows 서버를 사용 하 여 업데이트 배포를 관리 하지 않는 경우에는 보안 업데이트가 Azure Automation 업데이트 관리로 관리 되는 동안 Microsoft Endpoint Configuration Manager에 계속 보고할 수 있습니다.

>[!NOTE]
>업데이트 관리는 Windows Server 2008 r 2의 업데이트 평가 및 패치를 지원 하지만이 운영 체제를 실행 하는 Configuration Manager Microsoft 끝점에서 관리 하는 클라이언트는 지원 하지 않습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Automation 계정에 추가된 [Azure Automation 업데이트 관리](update-mgmt-overview.md)가 있어야 합니다.
* 현재 Microsoft 끝점 Configuration Manager 환경에서 관리 하는 Windows server도 업데이트 관리 사용 하도록 설정 된 Log Analytics 작업 영역에 보고 해야 합니다.
* 이 기능은 Microsoft 끝점 Configuration Manager 현재 분기 버전 1606 이상에서 사용할 수 있습니다. Microsoft 끝점 Configuration Manager 중앙 관리 사이트 또는 독립 실행형 기본 사이트를 Azure Monitor 로그 및 가져오기와 통합 하려면 [Azure Monitor 로그에 Configuration Manager 연결](../../azure-monitor/platform/collect-sccm.md)을 검토 합니다.  
* Windows 에이전트는 WSUS (Windows Server Update Services) 서버와 통신 하도록 구성 되거나 Microsoft 끝점 Configuration Manager에서 보안 업데이트를 수신 하지 않는 경우 Microsoft 업데이트에 액세스할 수 있어야 합니다.

기존 Microsoft 끝점 Configuration Manager 환경을 사용 하 여 Azure IaaS에서 호스트 되는 클라이언트를 관리 하는 방법은 주로 Azure 데이터 센터와 인프라 간의 연결에 따라 달라 집니다. 이 연결은 Microsoft 끝점 Configuration Manager 인프라에 적용 해야 하는 모든 디자인 변경 내용과 필요한 변경 내용을 지원 하기 위한 관련 비용에 영향을 줍니다. 계속하기 전에 평가해야 하는 계획 고려 사항에 대해 알아보려면 [Azure에서 Configuration Manager - 질문과 대답](/configmgr/core/understand/configuration-manager-on-azure#networking)을 검토하세요.

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Microsoft 끝점에서 소프트웨어 업데이트 관리 Configuration Manager

Microsoft 끝점 Configuration Manager에서 업데이트 배포를 계속 관리 하려는 경우 다음 단계를 수행 합니다. Azure Automation Microsoft 끝점 Configuration Manager에 연결 하 여 Log Analytics 작업 영역에 연결 된 클라이언트 컴퓨터에 업데이트를 적용 합니다. 업데이트 콘텐츠는 Microsoft 끝점 Configuration Manager에서 배포를 관리 하는 것 처럼 클라이언트 컴퓨터 캐시에서 사용할 수 있습니다.

1. [소프트웨어 업데이트 배포](/configmgr/sum/deploy-use/deploy-software-updates)에 설명 된 프로세스를 사용 하 여 Microsoft Endpoint Configuration Manager 계층 구조의 최상위 사이트에서 소프트웨어 업데이트 배포를 만듭니다. 표준 배포와 다르게 구성해야 하는 유일한 설정은 배포 패키지의 다운로드 동작을 제어하는 **소프트웨어 업데이트를 설치하지 않음** 옵션입니다. 이 동작은 다음 단계에서 예약된 업데이트 배포를 만들어 업데이트 관리에서 관리됩니다.

2. Azure Automation에서 **업데이트 관리**를 선택합니다. [업데이트 배포 만들기](update-mgmt-deploy-updates.md#schedule-an-update-deployment) 에 설명 된 단계에 따라 새 배포를 만들고, **유형** 드롭다운에서 **가져온 그룹** 을 선택 하 여 적절 한 Microsoft 엔드포인트 Configuration Manager 컬렉션을 선택 합니다. 다음 중요 사항을 주의하세요.

    a. 선택 된 Microsoft 끝점 Configuration Manager 장치 컬렉션에 유지 관리 기간이 정의 된 경우 컬렉션의 멤버는 예약 된 배포에 정의 된 **기간** 설정 대신이를 인식 합니다.

    b. 대상 컬렉션의 구성원은 인터넷에 연결되어 있어야 합니다(직접, 프록시 서버 또는 Log Analytics 게이트웨이를 통해).

Azure Automation을 통해 업데이트 배포가 완료되면, 선택한 컴퓨터 그룹의 멤버인 대상 컴퓨터에서 로컬 클라이언트 캐시의 예약된 시간에 업데이트를 설치합니다. [업데이트 배포 상태를 확인하여](update-mgmt-deploy-updates.md#check-deployment-status) 배포 결과를 모니터링할 수 있습니다.

## <a name="manage-software-updates-from-azure-automation"></a>Azure Automation에서 소프트웨어 업데이트 관리

Microsoft Endpoint Configuration Manager 클라이언트인 Windows Server Vm에 대 한 업데이트를 관리 하려면 업데이트 관리에서 관리 하는 모든 클라이언트에 대해 소프트웨어 업데이트 관리 기능을 사용 하지 않도록 클라이언트 정책을 구성 해야 합니다. 기본적으로 클라이언트 설정은 계층의 모든 디바이스를 대상으로 합니다. 이 정책 설정 및 구성하는 방법에 대한 자세한 내용은 [Configuration Manager에서 클라이언트 설정을 구성하는 방법](/configmgr/core/clients/deploy/configure-client-settings)을 검토합니다.

이 구성 변경을 수행한 후에는 [업데이트 배포 만들기](update-mgmt-deploy-updates.md#schedule-an-update-deployment) 에 설명 된 단계에 따라 새 배포를 만들고, **유형** 드롭다운에서 **가져온 그룹** 을 선택 하 여 적절 한 Microsoft 끝점 Configuration Manager 컬렉션을 선택 합니다.

## <a name="next-steps"></a>다음 단계

통합 일정을 설정하려면 [업데이트 배포 예약](update-mgmt-deploy-updates.md#schedule-an-update-deployment)을 참조하세요.
