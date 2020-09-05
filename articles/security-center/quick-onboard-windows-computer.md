---
title: Azure Security Center에 Windows 컴퓨터 온보딩
description: 이 빠른 시작에서는 Windows 컴퓨터에서 Log Analytics 에이전트를 프로비저닝하는 방법을 보여줍니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: memildin
ms.openlocfilehash: 2cec55b7c9450070d2e692fae50eff9ea880c54e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013744"
---
# <a name="quickstart-onboard-windows-computers-to-azure-security-center"></a>빠른 시작: Azure Security Center에 Windows 컴퓨터 온보딩
Azure 구독을 온보딩한 후 Log Analytics 에이전트를 프로비저닝하여 Azure 외부(예: 온-프레미스 또는 다른 클라우드)에서 실행되는 리소스에 Security Center를 사용하도록 설정할 수 있습니다.

이 빠른 시작에서는 Windows 컴퓨터에서 Log Analytics 에이전트를 설치하는 방법을 보여줍니다.

## <a name="prerequisites"></a>사전 요구 사항
Security Center를 시작하려면 Microsoft Azure에 대한 구독이 있어야 합니다. 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/pricing/free-trial/)으로 등록할 수 있습니다.

이 빠른 시작을 수행하기 위해서는 Security Center의 가격 계층이 표준이어야 합니다. 업그레이드 지침은 [Security Center 표준에 Azure 구독 온보딩](security-center-get-started.md)을 참조하세요. 비용 없이 Security Center의 표준을 사용해 볼 수 있습니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)를 참조하세요.

## <a name="add-new-windows-computer"></a>새 Windows 컴퓨터 추가

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에 로그인합니다.
2. **Microsoft Azure** 메뉴에서 **Security Center**를 선택합니다. **Security Center - 개요**가 열립니다.

   ![Security Center 개요][2]

3. Security Center 주 메뉴에서 **시작하기**를 선택합니다.
4. **시작하기** 탭을 선택합니다.

   ![시작하기][3]

5. **새 비 Azure 컴퓨터 추가**에서 **구성**을 클릭합니다. Log Analytics 작업 영역 목록이 표시됩니다. 이 목록에는 자동 프로비저닝을 사용하는 경우 Security Center에서 자동으로 생성되는 기본 작업 영역이 포함됩니다(해당하는 경우). 이 작업 영역이나 사용할 다른 작업 영역을 선택합니다.

    ![비 Azure 컴퓨터 추가](./media/quick-onboard-windows-computer/non-azure.png)

   Windows 에이전트 및 이러한 에이전트를 구성할 때 사용할 작업 영역 ID용 키를 다운로드할 수 있는 링크를 포함하는 **직접 에이전트** 블레이드가 열립니다.

6. 사용하는 컴퓨터 프로세서 유형에 해당하는 **Windows 에이전트 다운로드** 링크를 선택하여 설치 파일을 다운로드합니다.

7. **작업 영역 ID** 오른쪽에서 복사 아이콘을 선택하고 ID를 메모장에 붙여 넣습니다.

8. **기본 키** 오른쪽에서 복사 아이콘을 선택하고 키를 메모장에 붙여 넣습니다.

## <a name="install-the-agent"></a>에이전트 설치
이제 다운로드한 파일을 대상 컴퓨터에 설치해야 합니다.

1. 대상 컴퓨터에 파일을 복사하고 설치 프로그램을 실행합니다.
2. **Welcome** 페이지에서 **다음**을 선택합니다.
3. **사용 조건** 페이지에서 라이선스를 읽고 **동의함**을 선택합니다.
4. **대상 폴더** 페이지에서 기본 설치 폴더를 변경 또는 유지하고 **다음**을 선택합니다.
5. **에이전트 설치 옵션** 페이지에서 Azure Log Analytics에 에이전트를 연결하도록 선택한 후 **다음**을 선택합니다.
6. **Azure Log Analytics** 페이지에서 이전 절차에서 메모장에 복사해 둔 **작업 영역 ID**와 **작업 영역 키**(기본 키)를 붙여 넣습니다.
7. 컴퓨터가 Azure Government 클라우드에서 Log Analytics 작업 영역에 보고해야 하는 경우 **Azure Cloud** 드롭다운 목록에서 **Azure 미국 정부**를 선택합니다. 컴퓨터가 프록시 서버를 통해 Log Analytics 서비스와 통신해야 하는 경우 **고급**을 선택하고 프록시 서버의 URL 및 포트 번호를 제공합니다.
8. 필요한 구성 설정 제공을 완료한 후 **다음**을 선택합니다.

   ![에이전트 설치][5]

9. **설치 준비** 페이지에서 선택 항목을 검토한 다음 **설치**를 선택합니다.
10. **구성 완료** 페이지에서 **마침**을 선택합니다.

완료되면 **제어판**에 **Log Analytics 에이전트**가 나타납니다. 구성을 검토하고 에이전트에 연결되었는지 확인할 수 있습니다.

에이전트 설치 및 구성에 대한 자세한 내용은 [Windows 컴퓨터 연결](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)을 참조하세요.

이제 Azure VM과 비 Azure 컴퓨터를 한곳에서 모니터링할 수 있습니다. **컴퓨팅** 아래에는 모든 VM 및 컴퓨터의 개요가 권장 사항과 함께 나와 있습니다. 각 열에 하나의 권장 사항 집합이 표시됩니다. 색은 해당 권장 사항에 대한 VM 또는 컴퓨터의 현재 보안 상태를 나타냅니다. Security Center는 보안 경고를 통해서도 이러한 컴퓨터에서 검색된 내용을 표시합니다.

  ![컴퓨팅 블레이드][6]

**컴퓨팅** 블레이드에는 두 가지 유형의 아이콘이 표시됩니다.

![icon1](./media/quick-onboard-windows-computer/security-center-monitoring-icon1.png) 비 Azure 컴퓨터

![icon2](./media/quick-onboard-windows-computer/security-center-monitoring-icon2.png) Azure VM

## <a name="clean-up-resources"></a>리소스 정리
에이전트가 더 이상 필요하지 않은 경우 Windows 컴퓨터에서 에이전트를 제거할 수 있습니다.

에이전트를 제거하려면

1. **제어판**을 엽니다.
2. **프로그램 및 기능**을 엽니다.
3. **프로그램 및 기능**에서 **Log Analytics 에이전트**를 선택하고 **제거**를 클릭합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 Windows 컴퓨터에서 Log Analytics 에이전트를 프로비저닝했습니다. Security Center를 사용하는 방법에 대한 자세한 내용은 보안 정책 구성 및 리소스 보안 평가를 위한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [자습서: 보안 정책 정의 및 평가](tutorial-security-policy.md)

<!--Image references-->
[2]: ./media/quick-onboard-windows-computer/overview.png
[3]: ./media/quick-onboard-windows-computer/get-started.png
[4]: ./media/quick-onboard-windows-computer/add-computer.png
[5]: ./media/quick-onboard-windows-computer/log-analytics-mma-setup-laworkspace.png
[6]: ./media/quick-onboard-windows-computer/compute.png
