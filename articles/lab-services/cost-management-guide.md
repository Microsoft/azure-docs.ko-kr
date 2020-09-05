---
title: Azure Lab Services에 대 한 Cost management 가이드
description: Lab Services에 대 한 비용을 볼 수 있는 다양 한 방법을 이해 합니다.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98e04ba6bb1310935c4893a3616dfd68c2e99a55
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797635"
---
# <a name="cost-management-for-azure-lab-services"></a>Azure Lab Services에 대 한 Cost management

Azure Lab Services 비용 관리는 비용 예측 및 비용 분석의 두 가지 고유한 영역으로 나눌 수 있습니다. 랩의 초기 구조가 예상 예산 내에 포함 되도록 랩을 설정 하는 경우 비용 예측을 수행 합니다. 비용 분석은 일반적으로 월의 끝에 발생 하 여 다음 달에 필요한 작업을 결정 합니다.

## <a name="estimate-the-lab-costs"></a>랩 비용 예측

각 랩 대시보드에는 해당 월에 발생 하는 랩의 비용에 대 한 대략적인 추정치를 제공 하는 **비용 & 청구** 섹션이 있습니다. 비용 예상은 시간당 예상 비용으로 최대 사용자 수를 사용 하 여 시간 사용량을 요약 합니다. 가장 정확한 추정치를 얻으려면 [일정](how-to-create-schedules.md)을 포함 하 여 랩을 설정 합니다. 대시보드는 예상 비용을 반영 합니다. 

이러한 예상치는 모든 가능한 비용을 표시 하지 않을 수 있습니다. 몇 가지 리소스는 포함 되지 않습니다.

- 템플릿 준비 비용입니다. 이는 템플릿을 만드는 데 필요한 시간에 따라 크게 다를 수 있습니다. 템플릿의 실행 비용은 시간당 전체 랩 비용과 동일 합니다. 
- 모든 [공유 이미지 갤러리](how-to-use-shared-image-gallery.md) 비용은 갤러리를 여러 실습에서 공유할 수 있기 때문입니다. 
- 랩 작성자가 VM (가상 머신)을 시작할 때 발생 하는 시간입니다.

> [!div class="mx-imgBorder"]
> ![대시보드 예상 비용을 보여 주는 스크린샷](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-the-previous-months-usage"></a>이전 월의 사용 현황 분석

비용 분석은 이전 월의 사용량을 검토 하 여 랩에 대 한 조정을 결정 하는 데 사용 됩니다. [구독 비용 분석](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)에서 과거 비용의 분석을 찾을 수 있습니다. Azure Portal에서 검색 상자에 **구독** 을 입력 하 고 **구독** 옵션을 선택할 수 있습니다. 

> [!div class="mx-imgBorder"]
> ![검색 상자와 구독 옵션을 보여 주는 스크린샷](./media/cost-management-guide/subscription-search.png)

검토할 특정 구독을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![구독 선택 항목을 보여 주는 스크린샷](./media/cost-management-guide/subscription-select.png)

왼쪽 창에서 **Cost Management**의 **비용 분석** 을 선택 합니다.

> [!div class="mx-imgBorder"]
> ![그래프에 대 한 구독 비용 분석을 보여 주는 스크린샷](./media/cost-management-guide/subscription-cost-analysis.png)

이 대시보드에서는 일정에 따라 다양 한 파일 형식으로 내보내는 기능을 포함 하 여 심층 비용 분석을 수행할 수 있습니다. 자세한 내용은 [Cost Management + 청구 개요](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)를 참조 하세요.

리소스 종류를 기준으로 필터링 할 수 있습니다. `microsoft.labservices/labaccounts`를 사용 하면 Lab Services와 관련 된 비용도 표시 됩니다.

## <a name="understand-the-usage"></a>사용법 이해

다음 스크린샷은 비용 분석의 예입니다.

> [!div class="mx-imgBorder"]
> ![구독에 대 한 예제 비용 분석을 보여 주는 스크린샷](./media/cost-management-guide/cost-analysis.png)

기본적으로 **리소스**, **리소스 종류**, **위치**, **리소스 그룹 이름**, **태그**및 **비용**의 6 개 열이 있습니다. **리소스** 열에는 랩 계정, 랩 이름 및 VM에 대 한 정보가 포함 됩니다. 랩 계정, 랩 이름 및 기본값 (두 번째와 세 번째 행)을 표시 하는 행은 랩에 대 한 비용입니다. 사용 된 Vm에는 랩 계정, 랩 이름, 기본 및 VM 이름을 표시 하는 행에 대해 볼 수 있는 비용이 있습니다. 

이 예제에서는 첫 번째 및 두 번째 행 (둘 다 **aaalab/dockerlab**로 시작)을 추가 하 여 "aaalab" 랩 계정의 랩 "dockerlab"에 대 한 총 비용을 제공 합니다.

이미지 갤러리의 전체 비용을 얻으려면 리소스 종류를로 변경 합니다 `Microsoft.Compute/Galleries` . 갤러리의 저장 위치에 따라 공유 이미지 갤러리가 비용에 표시 되지 않을 수 있습니다.

> [!NOTE]
> 공유 이미지 갤러리는 랩 계정에 연결 됩니다. 즉, 여러 랩에서 동일한 이미지를 사용할 수 있습니다.

## <a name="separate-the-costs"></a>비용 구분

일부 대학에서는 클래스를 구분 하는 방법으로 랩 계정 및 리소스 그룹을 사용 했습니다. 각 클래스에는 고유한 랩 계정 및 리소스 그룹이 있습니다. 

비용 분석 창에서 리소스 그룹 이름에 기반 하는 필터를 클래스에 대 한 적절 한 리소스 그룹 이름으로 추가 합니다. 그런 다음 해당 클래스에 대 한 비용도 표시 됩니다. 이렇게 하면 비용을 볼 때 클래스 사이에 더 명확 하 게 경계 수 있습니다. 비용 분석의 [예약 된 내보내기](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) 기능을 사용 하 여 각 클래스의 비용을 별도의 파일로 다운로드할 수 있습니다.

## <a name="manage-costs"></a>비용 관리

클래스 유형에 따라 학생 없이 실행 되는 Vm의 인스턴스를 줄이기 위한 비용을 관리 하는 방법이 있습니다.

### <a name="automatic-shutdown-settings-for-cost-control"></a>비용 제어를 위한 자동 종료 설정

자동 종료 기능을 사용 하면 랩에서 낭비 된 VM 사용 시간을 방지할 수 있습니다. 다음 설정은 사용자가 실수로 가상 컴퓨터를 실행 하는 대부분의 경우를 파악 합니다.

> [!div class="mx-imgBorder"]
> ![3 가지 자동 종료 설정을 보여 주는 스크린샷](./media/cost-management-guide/auto-shutdown-disconnect.png)

랩 계정 수준과 랩 수준 모두에서 이러한 설정을 구성할 수 있습니다. 랩 계정 수준에서 사용 하도록 설정 하면 랩 계정 내의 모든 랩에 적용 됩니다. 이러한 설정은 모든 새 랩 계정에 대해 기본적으로 설정 되어 있습니다. 

#### <a name="automatically-disconnect-users-from-virtual-machines-that-the-os-deems-idle"></a>OS가 유휴 상태로 하다 고 판단 가상 머신에서 사용자를 자동으로 연결 끊기

> [!NOTE]
> 이 설정은 Windows 가상 컴퓨터에만 사용할 수 있습니다.

**가상 컴퓨터가 유휴 상태일 때 사용자 연결 끊기** 설정이 켜져 있으면 Windows OS에서 세션을 유휴 상태로 하다 고 판단 때 (템플릿 가상 컴퓨터 포함) 사용자가 랩의 모든 컴퓨터에서 연결이 끊어집니다. [유휴의 WINDOWS OS 정의](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) 에는 두 가지 기준이 사용 됩니다. 

* 사용자 부재: 키보드 또는 마우스 입력이 없습니다.
* 리소스 사용 부족: 모든 프로세서 및 모든 디스크가 특정 시간 (%) 동안 유휴 상태 였습니다.

사용자는 연결을 끊기 전에 VM에 다음과 같은 메시지를 표시 합니다. 

> [!div class="mx-imgBorder"]
> ![세션이 시간 제한을 초과 하 여 유휴 상태이 고 연결을 끊을 경고 메시지를 보여 주는 스크린샷](./media/cost-management-guide/idle-timer-expired.png)
 
사용자의 연결이 끊어질 때 가상 컴퓨터가 계속 실행 되 고 있습니다. 사용자가 로그인 하 여 가상 컴퓨터에 다시 연결 하는 경우에는 열려 있는 windows 또는 파일이 나 연결을 끊기 전에 저장 되지 않은 작업은 계속 됩니다. 이 상태에서 가상 머신이 실행 되 고 있기 때문에 여전히 활성 및 발생 비용으로 계산 됩니다. 
 
연결이 끊긴 Windows 가상 컴퓨터를 자동으로 종료 하려면 **가상 컴퓨터가 유휴 상태일 때 사용자 연결 끊기** 를 사용 하 고 **사용자가 설정의 연결을 끊을 때 가상 컴퓨터를 종료** 합니다.

예를 들어 다음과 같이 설정을 구성 합니다.
 
* **가상 컴퓨터가 유휴 상태일 때 사용자 연결 끊기**: 유휴 상태가 검색 된 후 15 분
* 사용자가 연결을 끊으면 5 분 후에 **가상 컴퓨터를 종료**합니다.
 
Windows 가상 머신은 사용자가 사용을 중지 한 후 자동으로 20 분 후에 종료 됩니다. 
 
> [!div class="mx-imgBorder"]
> ![자동 VM 종료가 발생 하는 설정의 조합을 보여 주는 다이어그램입니다.](./media/cost-management-guide/vm-idle-diagram.png)

#### <a name="automatically-shut-down-virtual-machines-when-users-disconnect"></a>사용자가 연결을 끊을 때 자동으로 가상 컴퓨터 종료
 
**사용자가 연결을 끊을 때 가상 컴퓨터 종료** 설정은 Windows 및 Linux 가상 컴퓨터를 모두 지원 합니다. 이 설정이 켜져 있으면 다음과 같은 경우 자동 종료가 수행 됩니다.
 
* Windows의 경우 RDP (원격 데스크톱) 연결의 연결이 끊깁니다.
* Linux의 경우 SSH 연결의 연결이 끊깁니다.
 
> [!NOTE]
> [특정 배포 및 Linux 버전만](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) 지원 됩니다.
 
가상 컴퓨터가 자동으로 종료 되기 전에 사용자가 다시 연결 될 때까지 대기 해야 하는 기간을 지정할 수 있습니다. 

#### <a name="automatically-shut-down-virtual-machines-that-are-started-but-users-dont-connect"></a>시작 되었지만 사용자가 연결 하지 않는 가상 컴퓨터를 자동으로 종료
 
랩에서 사용자는 가상 컴퓨터를 시작할 수 있지만 연결 하지는 않습니다. 예를 들면 다음과 같습니다.
 
* 랩의 일정은 클래스 세션의 모든 가상 컴퓨터를 시작 하지만 일부 학생은 표시 되지 않고 컴퓨터에 연결 되지 않습니다. 
* 사용자가 가상 컴퓨터를 시작 하지만 연결을 잊어버린 경우 
 
**사용자가 연결 하지 않을 때 가상 컴퓨터 종료** 설정은 이러한 사례를 파악 하 여 자동으로 가상 컴퓨터를 종료 합니다. 
 
연결을 끊을 때 Vm의 자동 종료를 구성 하 고 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [랩 계정에 대 한 Vm의 자동 종료 구성](how-to-configure-lab-accounts.md)
* [랩에 대 한 Vm의 자동 종료 구성](how-to-enable-shutdown-disconnect.md)

### <a name="scheduled-time-vs-quota-time"></a>예약 된 시간 및 할당량 시간

[예약 된 시간](classroom-labs-concepts.md#schedules) 및 [할당량 시간](classroom-labs-concepts.md#quota) 을 이해 하면 교수 및 학생의 요구에 더 잘 맞도록 랩을 구성 하는 데 도움이 됩니다. 

예약 된 시간은 모든 학생 Vm이 시작 되 고 연결에 사용할 수 있는 설정 된 시간입니다. 예약 된 시간은 모든 학생에 게 고유한 Vm이 있고 하루 중에 설정 된 시간 (예: 클래스 시간)에서 교수의 지시를 따라 수행 되는 경우에 일반적으로 사용 됩니다. 단점은 학생이 VM에 로그인 하지 않더라도 모든 학생 Vm이 시작 되 고 비용이 발생는 것입니다. 

할당량 시간은 각 학생에 게 신중 하 게 할당 되 고 독립적인 연구에 사용 되는 경우가 많습니다. 학생이 VM을 시작할 때까지 Vm이 시작 되지 않습니다. 

랩에서는 할당량 시간 또는 예약 된 시간 또는 둘의 조합을 사용할 수 있습니다. 클래스에 예약 된 시간이 필요 하지 않은 경우 Vm을 가장 효과적으로 사용 하는 데 할당량 시간만 사용 합니다.

### <a name="scheduled-event-stop-only"></a>예약 된 이벤트: 중지만

일정에서 특정 시간에 모든 컴퓨터를 중지 하는 중지 전용 이벤트 유형을 추가할 수 있습니다. 일부 랩 소유자는 학생이 사용 중인 VM을 종료 하는 것을 잊은 경우 비용 및 할당량 사용량을 줄이기 위해 자정에 매일 중지 전용 이벤트를 설정 했습니다. 이 유형의 이벤트에 대 한 단점은 학생이 VM을 사용 하는 경우에도 모든 Vm이 종료 된다는 것입니다.

### <a name="other-costs-related-to-labs"></a>랩 관련 기타 비용 

일부 비용은 Lab Services로 롤백되지 않지만 랩 서비스에 연결 될 수 있습니다. 공유 이미지 갤러리를 랩에 연결할 수 있지만 랩 서비스 비용은 표시 되지 않으며 비용은 있습니다. 전반적인 비용을 줄일 수 있도록 이미지에 고유한 저장소 비용이 있으므로 갤러리에서 사용 하지 않는 이미지를 제거 해야 합니다. 

Labs는 가상 네트워크를 통해 다른 Azure 리소스에 대 한 연결을 가질 수 있습니다. 랩을 제거 하면 가상 네트워크 및 기타 리소스를 제거 해야 합니다.

## <a name="conclusion"></a>결론

이 문서의 정보를 통해 사용 비용을 줄이는 데 도움이 되는 도구를 보다 잘 이해할 수 있습니다.
