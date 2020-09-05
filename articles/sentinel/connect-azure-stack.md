---
title: Azure Stack 가상 머신을 Azure 센티널에 등록 | Microsoft Docs
description: 이 문서에서는 Azure Stack 가상 머신에서 Azure Monitor, 업데이트 및 구성 관리 가상 머신 확장을 프로 비전 하 고 센티널로 모니터링을 시작 하는 방법을 보여 줍니다.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 8114a5e6db7b82b846d221471f41dbdf418ddd9d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001997"
---
# <a name="connect-azure-stack-virtual-machines-to-azure-sentinel"></a>Azure 센티널에 Azure Stack 가상 머신 연결




Azure 센티널을 사용 하면 Azure Stack Azure에서 실행 되는 Vm을 한 곳에서 모니터링할 수 있습니다. Azure 센티널에 Azure Stack 컴퓨터를 온 보 보드 하려면 먼저 기존 Azure Stack 가상 컴퓨터에 가상 컴퓨터 확장을 추가 해야 합니다. 

Azure Stack 컴퓨터를 연결한 후 데이터를 기반으로 하는 정보를 노출 하는 대시보드 갤러리에서 선택 합니다. 이러한 대시보드는 요구 사항에 맞게 쉽게 사용자 지정할 수 있습니다.



## <a name="add-the-virtual-machine-extension"></a>가상 컴퓨터 확장 추가 

Azure Stack에서 실행 되는 가상 머신에 **Azure Monitor, 업데이트 및 구성 관리** 가상 머신 확장을 추가 합니다. 

1. 새 브라우저 탭에서 [Azure Stack 포털](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal#access-the-portal)에 로그인 합니다.
2. **Virtual machines** 페이지로 이동 하 여 Azure 센티널로 보호할 가상 머신을 선택 합니다. Azure Stack에서 가상 컴퓨터를 만드는 방법에 대 한 자세한 내용은 [Azure Stack 포털을 사용 하 여 Windows SERVER Vm 만들기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-windows-portal) 또는 [Azure Stack 포털을 사용 하 여 Linux 서버 vm 만들기](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-quick-linux-portal)를 참조 하세요.
3. **확장**을 섡택합니다. 이 가상 머신에 설치된 가상 머신 확장 목록이 표시됩니다.
4. **추가** 탭을 클릭합니다. **새 리소스** 메뉴 블레이드가 열리고 사용 가능한 가상 머신 확장 목록이 표시됩니다. 
5. **Azure Monitor, 업데이트 및 구성 관리** 확장을 선택 하 고 **만들기**를 클릭 합니다. **설치 확장** 구성 창이 열립니다.

   ![Azure Monitor, 업데이트 및 구성 관리 설정](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Marketplace에 나열 된 **Azure Monitor, 업데이트 및 구성 관리** 확장이 표시 되지 않으면 Azure Stack 운영자에 게 연락 하 여 사용 가능 하도록 설정 합니다.

6. Azure 센티널 메뉴에서 **작업 영역 설정** , **고급**을 차례로 선택 하 고 **작업 영역 ID** 및 **작업 영역 키 (기본 키)** 를 복사 합니다. 
1. Azure Stack **설치 확장** 창에서 표시 된 필드에 붙여 넣고 **확인**을 클릭 합니다.
1. 확장 설치가 완료 되 면 상태가 **프로 비전 성공**으로 표시 됩니다. 가상 머신이 Azure 센티널 포털에 표시 되는 데 최대 1 시간이 걸릴 수 있습니다.

Windows 용 에이전트를 설치 하 고 구성 하는 방법에 대 한 자세한 내용은 [windows 컴퓨터 연결](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard)을 참조 하세요.

Linux 에이전트 문제 해결 방법은 [Azure Log Analytics Linux 에이전트 문제 해결](../azure-monitor/platform/agent-linux-troubleshoot.md)을 참조하세요.

Azure의 Azure 센티널 포털의 **Virtual Machines**에는 모든 vm 및 컴퓨터의 상태와 함께 개요가 표시 됩니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요 없는 경우 Azure Stack 포털을 통해 가상 머신에서 확장을 제거할 수 있습니다.

확장을 제거하려면:

1. **Azure Stack 포털**을 엽니다.
2. **가상 머신** 페이지로 이동하고 확장을 제거할 가상 머신을 선택합니다.
3. **확장**, **Microsoft.EnterpriseCloud.Monitoring** 확장을 차례로 선택합니다.
4. **제거**를 클릭 하 고 선택 내용을 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [데이터에 대한 가시성을 얻고 재적 위협을 확인](quickstart-get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](tutorial-detect-threats-built-in.md)을 시작합니다.
- [일반적인 오류 형식 어플라이언스](connect-common-event-format.md)의 데이터를 Azure Sentinel로 스트리밍합니다.
