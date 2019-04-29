---
title: StorSimple 데이터 관리자에서 Azure Automation을 사용하여 작업 시작 | Microsoft Docs
description: Azure Automation을 사용하여 StorSimple 데이터 관리자 작업을 트리거하는 방법에 대해 알아보기
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b837aab871827c468295a365727a282f6c8a1a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634260"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Azure Automation을 사용하여 작업 트리거

이 문서에서는 StorSimple 데이터 관리자 서비스 내에서 데이터 변환 기능을 사용하여 StorSimple 디바이스 데이터를 변환하는 방법에 대해 설명합니다. 두 가지 방법으로 데이터 변환 작업을 시작할 수 있습니다. 

 - .NET SDK 사용
 - Azure Automation Runbook 사용
 
이 문서에서는 Azure Automation Runbook을 만든 후 사용하여 데이터 변환 작업을 시작하는 방법을 자세히 설명합니다. .NET SDK를 통해 데이터 변환을 시작하는 방법에 대한 자세한 내용은 [.NET SDK를 사용하여 데이터 변환 작업 트리거](storsimple-data-manager-dotnet-jobs.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 항목이 있어야 합니다.

*   클라이언트 컴퓨터에 설치된 Azure PowerShell. [Azure Powershell을 다운로드합니다](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   리소스 그룹 내의 StorSimple 데이터 관리자 서비스에서 올바르게 구성된 작업 정의.
*   Github 리포지토리에서 [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) 파일을 다운로드합니다. 
*   Github 리포지토리에서 [`Trigger-DataTransformation-Job.ps1`스크립트](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1)를 다운로드합니다.

## <a name="step-by-step-procedure"></a>단계별 절차

### <a name="set-up-the-automation-account"></a>Automation 계정 설정

1. Azure Portal에서 Azure 실행 자동화 계정을 만듭니다. 이렇게 하려면 **Azure Marketplace > 모두**로 이동한 다음 **자동화**를 검색합니다. **Automation 계정**을 선택합니다.

    ![Automation 실행 계정 만들기](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. 새 Automation 계정을 추가하려면 **+ 추가**를 클릭합니다.

    ![Automation 실행 계정 만들기](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. **Automation 추가**에서

   1. Automation 계정의 **이름**을 입력합니다.
   2. StorSimple 데이터 관리자 서비스에 연결된 **구독**을 선택합니다.
   3. 새 리소스 그룹을 만들거나 기존 리소스 그룹에서 선택합니다.
   4. **위치**를 선택합니다.
   5. 기본 **실행 계정 만들기** 옵션을 선택된 상태로 둡니다.
   6. 대시보드에 빠른 액세스에 대한 링크를 표시하려면 **대시보드에 고정**을 선택합니다. **만들기**를 클릭합니다.

      ![Automation 실행 계정 만들기](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      Automation 계정이 생성되면 알림이 표시됩니다.
    
      ![Automation 계정 배포 알림](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      자세한 내용은 [실행 계정 만들기](../automation/automation-create-runas-account.md)로 이동합니다.

3. 새로 만든 계정에서 **공유 리소스 > 모듈**로 이동한 다음 **+ 모듈 추가**를 클릭합니다.

    ![가져오기 모듈 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. 로컬 컴퓨터에서 `DataTransformationApp.zip` 파일 위치를 찾은 후 모듈을 선택하여 엽니다. **확인**을 클릭하여 모듈을 가져옵니다.

    ![가져오기 모듈 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Azure Automation이 사용자 계정에 모듈을 가져오면 모듈에 대한 메타데이터를 추출합니다. 이 작업에는 몇 분 정도 걸릴 수 있습니다.

   ![가져오기 모듈 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. 모듈이 배포되는 도중 및 완료 시점에 알림이 표시됩니다.  **모듈**의 상태가 **사용 가능**으로 변경됩니다.

    ![가져오기 모듈 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Automation Runbook 가져오기, 게시 및 실행

다음 단계에 따라 Runbook을 가져오기, 게시 및 실행하여 작업 정의를 트리거합니다.

1. Azure Portal에서 Automation 계정을 엽니다. **프로세스 자동화 > Runbook**으로 이동한 다음 **+ Runbook 추가**를 클릭합니다.

    ![Runbook 추가 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. **Runbook 추가**에서 **기존 Runbook 가져오기**를 클릭합니다.

3. **Runbook 파일**의 Azure PowerShell 스크립트 파일 `Trigger-DataTransformation-Job.ps1`을 가리킵니다. Runbook 유형이 자동으로 선택됩니다. Runbook의 이름과 선택적 설명을 입력합니다. **만들기**를 클릭합니다.

    ![Runbook 추가 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. 새 Runbook이 Automation 계정의 Runbook 목록에 표시됩니다. 이 Runbook을 선택하고 클릭합니다.

    ![Runbook 추가 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Runbook을 편집하고 **테스트** 창을 클릭합니다.

    ![Runbook 추가 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. StorSimple Data Manager 서비스의 이름, 연결된 리소스 그룹 및 작업 정의 이름 등의 매개 변수를 제공합니다. 테스트를 **시작**합니다. 실행이 완료되면 보고서가 생성됩니다. 자세한 내용은 [Runbook 테스트](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook) 방법을 참조하세요.

    ![Runbook 추가 5](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. 테스트 창에서 Runbook의 출력을 검사합니다. 만족스러우면 창을 닫습니다. **게시**를 클릭하고, 확인 메시지가 표시되면 Runbook을 확인 및 게시합니다.

    ![Runbook 추가 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. **Runbook**으로 돌아간 후 새로 만든 Runbook을 선택합니다.

    ![Runbook 추가 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. Runbook을 **시작**합니다. **Runbook 시작**에서 모든 매개 변수를 입력합니다. **확인**을 클릭하여 제출하고 데이터 변환 작업을 시작합니다.

10. Azure Portal에서 작업 진행 상황을 모니터링하려면 StorSimple 데이터 관리자 서비스의 **작업**으로 이동합니다. 작업을 선택한 후 클릭하여 작업 세부 정보를 확인합니다.

    ![Runbook 추가 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>다음 단계

[StorSimple 데이터 관리자 UI를 사용하여 데이터를 변환합니다](storsimple-data-manager-ui.md).