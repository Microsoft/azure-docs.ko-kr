---
title: Azure DevOps 및 Azure Application Insights를 사용한 DevOps 릴리스 파이프라인의 연속 모니터링 | Microsoft Docs
description: Application Insights를 사용하여 연속 모니터링을 빠르게 설정하기 위한 지침을 제공합니다.
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 41999defb01e024773b6364f169a1ce3b1377237
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902385"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>릴리스 파이프라인에 연속 모니터링 추가

Azure DevOps Services는 Azure Application Insights와 통합되어 소프트웨어 개발 수명 주기 내내 DevOps 릴리스 파이프라인을 지속적으로 모니터링할 수 있습니다. 

현재 Azure DevOps Services는 릴리스 파이프라인이 Application Insights 및 기타 Azure 리소스의 모니터링 데이터를 통합할 수 있는 연속 모니터링을 지원합니다. Application Insights 경고가 검색되면 경고가 해결될 때까지 배포가 제어된 상태로 유지되거나 롤백될 수 있습니다. 모든 검사에 통과하면 수동 개입 없이도 테스트에서 프로덕션까지 자동으로 진행할 수 있습니다. 

## <a name="configure-continuous-monitoring"></a>연속 모니터링 구성

1. 기존 Azure DevOps Services 프로젝트를 선택합니다.

2. 마우스로 **빌드 및 릴리스**를 가리키고, **릴리스**를 선택하고, **더하기 기호** > **릴리스 정의 만들기**를 클릭하고, **모니터링** > **Azure App Service 배포 및 연속 모니터링**을 검색합니다.

   ![새 Azure DevOps Services 릴리스 파이프라인](media/continuous-monitoring/001.png)

3. **적용**을 클릭합니다.

4. 빨간색 느낌표 옆에서 파란색 텍스트를 선택하여 **환경 작업을 봅니다**.

   ![환경 작업 보기](media/continuous-monitoring/002.png)

   구성 상자가 표시되면 다음 표를 사용하여 입력 필드를 채웁니다.

    | 매개 변수        | 값 |
   | ------------- |:-----|
   | **환경 이름**      | 릴리스 파이프라인 환경을 설명하는 이름입니다. |
   | **Azure 구독** | 드롭다운이 Azure DevOps Services 조직에 연결된 모든 Azure 구독으로 채워집니다.|
   | **App Service 이름** | 다른 항목을 선택할 경우 이 필드에 새 값을 수동으로 입력해야 할 수 있습니다. |
   | **리소스 그룹**    | 드롭다운이 사용 가능한 리소스 그룹으로 채워집니다. |
   | **Application Insights 리소스 이름** | 드롭다운이 이전에 선택된 리소스 그룹에 해당하는 모든 Application Insights 리소스로 채워집니다.

5. **Application Insights 경고 구성**을 선택합니다.

6. 기본 경고 규칙의 경우 **저장**을 선택하고, 설명을 입력하고, **확인**을 클릭합니다.

## <a name="modify-alert-rules"></a>경고 규칙 수정

1. 미리 정의된 경고 설정을 수정하려면 **경고 규칙**의 오른쪽에 **줄임표...** 가 있는 상자를 클릭합니다.

   기본 제공되는 네 가지 경고 규칙은 가용성, 실패한 요청, 서버 응답 시간 및 서버 예외입니다.

2. **가용성** 옆에 있는 드롭다운 기호를 클릭합니다.

3. 서비스 수준 요구 사항을 충족하도록 가용성 **임계값**을 수정합니다.

   ![경고 수정](media/continuous-monitoring/003.png)

4. **확인** > **저장**을 선택하고, 설명을 입력하고, **확인**을 클릭합니다.

## <a name="add-deployment-conditions"></a>배포 조건 추가

1. **파이프라인**을 클릭하고 연속 모니터링 게이트가 필요한 단계에 따라 **배포 전 조건** 또는 **배포 후 조건**을 선택합니다.

   ![배포 전 조건](media/continuous-monitoring/004.png)

2. **게이트**를 **사용** > **승인 게이트**로 설정하고 **추가**를 클릭합니다.

3. **Azure Monitor**를 선택합니다. 이 옵션은 Azure Monitor 및 Application Insights에서 모두 경고에 액세스하는 기능을 제공합니다.

    ![Azure Monitor](media/continuous-monitoring/005.png)

4. **게이트 시간 제한** 값을 입력합니다.

5. **샘플링 간격**을 입력합니다.

## <a name="deployment-gate-status-logs"></a>배포 게이트 상태 로그

배포 게이트를 추가하면 이전에 정의된 임계값을 초과하는 Application Insights의 경고를 통해 배포의 불필요한 릴리스 승격을 방지할 수 있습니다. 경고가 해결되면 배포가 자동으로 진행될 수 있습니다.

이 동작을 관찰하려면 **릴리스**를 선택하고, 릴리스 이름을 마우스 오른쪽 단추로 클릭하고, **열기** > **로그**를 선택합니다.

![로그](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>다음 단계

Azure Pipelines에 대한 자세한 내용은 이 [빠른 시작](https://docs.microsoft.com/azure/devops/pipelines)을 참조하세요.
