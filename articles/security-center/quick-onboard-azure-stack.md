---
title: Azure Stack 가상 머신을 Azure Security Center에 온보딩
description: 이 빠른 시작에서는 Azure Stack 가상 머신에서 Azure Monitor, Update 및 Configuration Management 가상 머신 확장을 프로비저닝하는 방법을 보여줍니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8982348a-0624-40c7-8a1e-642a523c7f6b
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: memildin
ms.openlocfilehash: 7469d60095026c8a016fc1ecaa1a4f92361800f3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019382"
---
# <a name="quickstart-onboard-your-azure-stack-virtual-machines-to-security-center"></a>빠른 시작: Azure Stack 가상 머신을 Security Center에 온보딩
Azure 구독을 온보딩한 후 Azure Stack 마켓플레이스에서 **Azure Monitor, Update 및 Configuration Management** 가상 머신 확장을 추가하여 Azure Stack에서 실행 중인 가상 머신을 보호하도록 Security Center를 설정할 수 있습니다.

이 빠른 시작에서는 Azure Stack에서 실행 중인 가상 머신(Linux 및 Windows 모두 지원)에서 **Azure Monitor, Update 및 Configuration Management** 가상 머신 확장을 추가하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

이 빠른 시작을 수행하려면 Azure 구독이 Security Center의 표준(Standard) 계층에 있어야 합니다. 업그레이드 지침은 [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md)을 참조하세요. 30일 동안 추가 비용 없이 Security Center 표준 계층을 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="select-your-workspace-in-azure-security-center"></a>Azure Security Center에서 작업 영역 선택

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.
2. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다. 

   ![Security Center 개요][2]

3. Security Center 주 메뉴에서 **시작하기**를 선택합니다.
4. **시작하기** 탭을 선택합니다.

   ![시작하기][3]

5. **새 비 Azure 컴퓨터 추가**에서 **구성**을 클릭합니다. Log Analytics 작업 영역 목록이 표시됩니다. 이 목록에는 자동 프로비저닝을 사용하는 경우 Security Center에서 자동으로 생성되는 기본 작업 영역이 포함됩니다(해당하는 경우). 이 작업 영역 또는 Azure Stack VM이 보안 데이터를 보고하도록 할 다른 작업 영역을 선택합니다.

    ![비 Azure 컴퓨터 추가](./media/quick-onboard-windows-computer/non-azure.png)

   에이전트 및 이러한 에이전트를 구성할 때 사용할 작업 영역 ID의 키를 다운로드할 수 있는 링크를 포함하는 **직접 에이전트** 블레이드가 열립니다.

   >[!NOTE]
   > 에이전트를 수동으로 다운로드할 필요가 없습니다. 에이전트는 아래 단계에서 VM 확장으로 설치됩니다.

6. **작업 영역 ID** 오른쪽에서 복사 아이콘을 선택하고 ID를 메모장에 붙여 넣습니다.

7. **기본 키** 오른쪽에서 복사 아이콘을 선택하고 키를 메모장에 붙여 넣습니다.

## <a name="add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines"></a>기존 Azure Stack 가상 머신에 가상 머신 확장 추가
이제 Azure Stack에서 실행 중인 가상 머신에 **Azure Monitor, Update 및 Configuration Management** 가상 머신 확장을 추가해야 합니다.

1. 새 브라우저 탭에서 **Azure Stack** 포털에 로그인합니다.
2. **가상 머신** 페이지에서 Security Center로 보호하려는 가상 머신을 선택합니다. Azure Stack에서 가상 머신을 만드는 방법에 대한 자세한 내용은 [Windows 가상 머신용 빠른 시작](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) 또는 [Linux 가상 머신용 빠른 시작](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)을 참조하세요.
3. **확장**을 섡택합니다. 이 가상 머신에 설치된 가상 머신 확장 목록이 표시됩니다.
4. **추가** 탭을 클릭합니다. **새 리소스** 메뉴 블레이드가 열리고 사용 가능한 가상 머신 확장 목록이 표시됩니다. 
5. **Azure Monitor, Update 및 Configuration Management** 확장을 선택하고 **만들기**를 클릭합니다. **설치 확장** 구성 블레이드가 열립니다.

>[!NOTE]
> **Azure Monitor, Update 및 Configuration Management** 확장이 Marketplace에 나열되지 않으면 사용할 수 있도록 Azure Stack 운영자에게 연락하세요.

6. **설치 확장** 구성 블레이드에서 이전 절차에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키**(기본 키)를 붙여넣습니다.
7. 필요한 구성 설정을 제공했으면 **확인**을 클릭합니다.
8. 확장 설치가 완료되면 상태가 **프로비전 성공**으로 표시됩니다. Security Center 포털에 가상 머신이 나타나기까지 최대 1시간이 걸릴 수 있습니다.

Windows용 에이전트 설치 및 구성에 대한 자세한 내용은 [Windows 컴퓨터 연결](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)을 참조하세요.

Linux 에이전트 문제 해결 방법은 [Azure Log Analytics Linux 에이전트 문제 해결](../azure-monitor/platform/agent-linux-troubleshoot.md)을 참조하세요.

이제 Azure VM과 비 Azure 컴퓨터를 한곳에서 모니터링할 수 있습니다. Azure의 Security Center 포털에서 **컴퓨팅** 아래에 모든 VM 및 컴퓨터의 개요와 권장 사항이 나와 있습니다. Security Center는 보안 경고를 통해서도 이러한 컴퓨터에서 검색된 내용을 표시합니다.

  ![컴퓨팅 블레이드][6]

**컴퓨팅** 블레이드에는 두 가지 유형의 아이콘이 표시됩니다.

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) 비 Azure 컴퓨터 

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM(이 그룹에 Azure Stack VM이 표시됨)

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요 없는 경우 Azure Stack 포털을 통해 가상 머신에서 확장을 제거할 수 있습니다.

확장을 제거하려면:

1. **Azure Stack 포털**을 엽니다.
2. **가상 머신** 페이지로 이동하고 확장을 제거할 가상 머신을 선택합니다.
3. **확장**, **Microsoft.EnterpriseCloud.Monitoring** 확장을 차례로 선택합니다.
4. **제거**를 클릭하고 **예**를 클릭하여 선택을 확인합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Azure Stack에서 실행 중인 가상 머신에서 Azure Monitor, Update 및 Configuration Management 확장을 프로비저닝했습니다. Security Center를 사용하는 방법에 대한 자세한 내용은 보안 정책 구성 및 리소스 보안 평가를 위한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: 보안 정책 정의 및 평가](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
