---
title: Azure Automation 변경 내용 추적 및 인벤토리 기능 제거
description: 이 문서에서는 변경 내용 추적 및 인벤토리 사용을 중지하고 Log Analytics 작업 영역에서 Automation 계정의 연결을 해제하는 방법을 설명합니다.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92210265"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Automation 계정에서 변경 내용 추적 및 인벤토리 제거

Azure Automation 변경 내용 추적 및 인벤토리를 사용하여 가상 머신의 관리를 사용하도록 설정한 후 가상 머신 관리 사용을 중지하고 계정 및 연결된 Log Analytics 작업 영역에서 구성을 제거할 수 있습니다. 이 문서에서는 관리형 VM, Automation 계정, Log Analytics 작업 영역에서 변경 내용 추적 및 인벤토리를 완전히 제거하는 방법을 설명합니다.

## <a name="sign-into-the-azure-portal"></a>Azure Portal에 로그인합니다.

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="remove-management-of-vms"></a>VM 관리 제거

변경 내용 추적 및 인벤토리를 제거하려면 먼저 VM 관리를 중지해야 합니다. 이 기능에서 등록을 취소하려면 [변경 내용 추적에서 VM 제거](remove-vms-from-change-tracking.md)를 참조하세요.

## <a name="remove-changetracking-solution"></a>ChangeTracking 솔루션 제거

작업 영역에서 Automation 계정의 연결을 해제하기 전에 다음 단계를 수행하여 변경 내용 추적 및 인벤토리를 완전히 제거해야 합니다. 작업 영역에서 **ChangeTracking** 솔루션을 제거합니다.

1. Azure Portal에서 **모든 서비스** 를 선택합니다. 리소스 목록에서 **Log Analytics** 를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록에서 제안 항목이 필터링됩니다. **Log Analytics** 를 선택합니다.

2. 변경 내용 추적 및 인벤토리를 사용하도록 설정했을 때 선택한 작업 영역을 Log Analytics 작업 영역 목록에서 선택합니다.

3. 왼쪽에서 **솔루션** 을 선택합니다.  

4. 솔루션 목록에서 **ChangeTracking(작업 영역 이름)** 을 선택합니다. 솔루션의 **개요** 페이지에서 **삭제** 를 선택합니다. 확인하라는 프롬프트가 나타나면 **예** 를 선택합니다.

## <a name="unlink-workspace-from-automation-account"></a>Automation 계정에서 작업 영역 연결 해제

1. Azure Portal에서 **Automation 계정** 을 선택합니다.

2. Automation 계정을 열고 왼쪽의 **관련 리소스** 아래에서 **연결된 작업 영역** 을 선택합니다.

3. **작업 영역 연결 해제** 페이지에서 **작업 영역 연결 해제** 를 선택하고 프롬프트에 응답합니다.

   ![작업 영역 연결 해제 페이지](media/remove-feature/automation-unlink-workspace-blade.png)

Log Analytics 작업 영역에서 연결을 해제하는 동안 메뉴의 **알림** 에서 진행 상황을 추적할 수 있습니다.

또는 작업 영역 내 Automation 계정에서 Log Analytics 작업 영역의 연결을 해제할 수 있습니다.

1. Azure Portal에서 **Log Analytics** 를 선택합니다.

2. 작업 영역의 **관련 리소스** 아래에서 **Automation 계정** 을 선택합니다.

3. Automation 계정 페이지에서 **계정 연결 해제** 를 선택합니다.

Automation 계정의 연결을 해제하는 동안 메뉴의 **알림** 에서 진행률을 추적할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 기능을 다시 사용하도록 설정하려면 [Automation 계정에서 변경 내용 추적 및 인벤토리 사용](enable-from-automation-account.md), [Azure Portal을 검색하여 변경 내용 추적 및 인벤토리를 사용하도록 설정](enable-from-portal.md), [Runbook에서 변경 내용 추적 및 인벤토리 사용](enable-from-runbook.md), [Azure VM에서 변경 내용 추적 및 인벤토리 사용](enable-from-vm.md)을 참조하세요.
