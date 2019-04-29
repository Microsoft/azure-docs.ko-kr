---
title: 클라우드 서비스 배포 문제 해결 | Microsoft Docs
description: Azure에 클라우드 서비스를 배포할 때 발생할 수 있는 몇 가지 일반적인 문제가 있습니다. 이 문서에서는 그 중 일부에 대한 솔루션을 제공합니다.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: a18ae415-0d1c-4bc4-ab6c-c1ddea02c870
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: cc2a0177525013736445db5fd1befa478dc9b9b8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106916"
---
# <a name="troubleshoot-cloud-service-deployment-problems"></a>클라우드 서비스 배포 문제 해결
Azure에 클라우드 서비스 애플리케이션 패키지를 배포할 때 Azure portal의 **속성** 창에서 배포에 대한 정보를 얻을 수 있습니다. 클라우드 서비스에 발생한 문제를 해결하기 위해 이 창에서 세부 정보를 사용할 수 있고 새로운 지원 요청을 할 때 Azure 지원 센터에 이 정보를 제공할 수 있습니다.

다음과 같이 **속성** 창을 찾을 수 있습니다.

* Azure 포털에서 클라우드 서비스의 배포, **모든 설정**, **속성**을 차례로 클릭합니다.

> [!NOTE]
> 창의 오른쪽 위 모서리에 있는 아이콘을 클릭하여 **속성** 창의 내용을 클립보드에 복사할 수 있습니다.
>
>

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>문제: 내 웹 사이트에 액세스할 수 없습니다. 하지만 내 배포가 시작 되었고 모든 역할 인스턴스가 준비
포털에 표시된 웹 사이트 URL 링크에는 포트가 포함되지 않습니다. 웹 사이트의 기본 포트는 80입니다. 애플리케이션이 다른 포트에서 실행되도록 구성된 경우 웹 사이트에 액세스할 때 URL에 올바른 포트 번호를 추가해야 합니다.

1. Azure 포털에서 클라우드 서비스의 배포를 클릭합니다.
2. Azure 포털의 **속성** 창에서 역할 인스턴스에 대한 포트를 확인합니다(**입력 엔드포인트**에서).
3. 포트가 80인 경우 애플리케이션에 액세스할 때 URL에 올바른 포트 값을 추가합니다. 기본이 아닌 포트를 지정하려면 뒤에 콜론(:)과 공백 없이 포트 번호를 붙인 URL을 입력합니다.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>문제: 아무 작업도 하지 않았는데 내 역할 인스턴스가 재활용
Azure에서 문제가 있는 노드를 검색하고 따라서 역할 인스턴스를 새 노드로 이동하는 경우 서비스 복구가 자동으로 발생합니다. 이 경우 역할 인스턴스가 자동으로 재활용될 수 있습니다. 서비스 복구가 수행되었는지 검색하려면

1. Azure 포털에서 클라우드 서비스의 배포를 클릭합니다.
2. Azure 포털의 **속성** 창에서 정보를 검토하고 역할이 재활용된 것을 관찰하는 동안 서비스 복구가 발생했는지 여부를 확인합니다.

또한 역할은 호스트 OS와 게스트 OS를 업데이트하는 동안 대략 한 달에 한 번 정도 재활용합니다.  
자세한 내용은 [OS 업그레이드로 인해 역할 인스턴스 다시 시작](https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx)

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>문제: VIP 교환을 수행 및 오류가 나타날 수 없습니다.
배포 업데이트가 진행 중인 경우 VIP 교환이 허용되지 않습니다. 배포 업데이트가 다음과 같은 경우 자동으로 발생할 수 있습니다.

* 새 게스트 운영 체제를 사용할 수 있고 자동 업데이트를 구성합니다.
* 서비스 복구가 수행됩니다.

자동 업데이트가 VIP 교체를 수행을 방해하는지 알아보려면:

1. Azure 포털에서 클라우드 서비스의 배포를 클릭합니다.
2. Azure 포털의 **속성** 창에서 **상태** 값을 확인합니다. **준비**인 경우 **마지막 작업**을 확인하여 최근에 VIP 교체를 방해한 작업이 발생했는지 확인합니다.
3. 프로덕션 배포에 1단계와 2단계를 반복합니다.
4. 자동 업데이트가 실행 중인 경우 VIP 교체를 시도하기 전에 완료되기를 기다립니다.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>문제: 역할 인스턴스가 시작 됨, 초기화, 사용 중, 중지 됨 사이 반복 됩니다.
이 상태는 애플리케이션 코드, 패키지 또는 구성 파일에 문제가 있음을 나타낼 수 있습니다. 이 경우 몇 분마다 상태가 변경되는 것을 확인할 수 있어야 하고 Azure 포털은 **재활용 중**, **사용 중** 또는 **초기화 중**과 같이 표시됩니다. 역할 인스턴스의 실행을 방해하는 애플리케이션에 문제가 있다는 것을 의미합니다.

이 문제를 해결하는 방법에 대한 자세한 내용은 [Azure PaaS Compute 진단 데이터](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) 및 [역할을 재활용하는 일반적인 문제](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md) 블로그 게시물을 참조하세요.

## <a name="problem-my-application-stopped-working"></a>문제: 내 응용 프로그램의 작동이 중지 되었습니다
1. Azure 포털에서 역할 인스턴스를 클릭합니다.
2. Azure 포털의 **속성** 창에서 다음 조건을 고려하여 문제를 해결합니다.
   * 역할 인스턴스가 최근에 중지된 경우 ( **중단 횟수**의 값을 확인할 수 있음) 배포를 업데이트할 수 있습니다. 역할 인스턴스가 다시 스스로 작동하는지 확인하기 위해 기다립니다.
   * 역할 인스턴스가 **사용 중**인 경우 애플리케이션 코드가 [StatusCheck](/previous-versions/azure/reference/ee758135(v=azure.100)) 이벤트를 처리하는지 확인합니다. 이 이벤트를 처리하는 일부 코드를 추가하거나 수정해야 합니다.
   * [Azure PaaS Compute 진단 데이터](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) 블로그 게시물에서 진단 데이터 및 문제 해결 시나리오를 수행합니다.

> [!WARNING]
> 클라우드 서비스를 재활용하면 배포에 대한 속성을 다시 설정하며 이렇게 하면 원래 문제에 대한 정보가 효과적으로 지워집니다.
>
>

## <a name="next-steps"></a>다음 단계
클라우드 서비스에 대한 [문제해결 문서](https://docs.microsoft.com/azure/cloud-services/cloud-services-allocation-failures) 를 더 봅니다.

Azure PaaS 컴퓨터 진단 데이터를 사용하여 클라우드 서비스 역할 문제를 해결하는 방법을 알아보려면 [Kevin Williamson의 블로그 시리즈](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)를 참조하세요.
