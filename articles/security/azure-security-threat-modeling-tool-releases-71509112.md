---
title: Threat Modeling Tool 릴리스 - Microsoft Threat Modeling Tool - Azure | Microsoft Docs
description: Threat Modeling Tool 릴리스 정보 문서화
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2019
ms.author: jegeib
ms.openlocfilehash: bdf8b701567aaa3a0d9006333557bcec4f312723
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586465"
---
# <a name="threat-modeling-tool-ga-release-71509112---9122018"></a>Threat Modeling Tool GA 릴리스 7.1.50911.2 - 9/12/2018

이제 Microsoft Threat Modeling Tool을 지원되는 GA(일반 공급) 릴리스로 다운로드할 수 있습니다. 이 릴리스에는 버그 수정, 기능 업데이트 및 안정성 개선 사항과 함께 중요한 개인 정보 및 보안 업데이트가 포함되어 있습니다. 2017 Preview 버전의 기존 사용자에게는 클라이언트를 열 때 ClickOnce 기술을 통해 최신 릴리스로 업데이트하라는 메시지가 표시됩니다. 도구의 새 사용자는 [여기를 클릭하여 클라이언트를 다운로드](https://aka.ms/threatmodelingtool)합니다.

이 릴리스와 더불어 2017 Preview에 대한 지원은 종료되며, Preview의 모든 사용자는 GA 릴리스로 업데이트하는 것이 좋습니다. 2018년 10월 15일 이후에는 Threat Modeling Tool에 대한 최소 필수 ClickOnce 버전이 설정되며, 모든 Preview 클라이언트를 업그레이드해야 합니다.

[Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=49168)에서 사용할 수 있는 Microsoft Threat Modeling Tool 2016은 중요한 보안 수정에 대해서만 2019년 10월 1일까지 계속 지원됩니다.

## <a name="feature-changes"></a>기능 변경

### <a name="azure-stencil-updates"></a>Azure 스텐실 업데이트

이 릴리스와 함께 제공되는 스텐실 집합에는 기타 Azure 스텐실 및 관련 위협과 완화 방법이 추가되었습니다. “Azure App Services”, “Azure 데이터베이스 제품” 및 “Azure Storage”의 포커스 영역이 상당히 변경되었습니다.

![Azure 스텐실 업데이트](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencil_update-300x70.png)

### <a name="onedrive-integration-feature-removed"></a>OneDrive 통합 기능 제거됨

Preview의 “OneDrive에 저장”, “OneDrive에서 열기” 및 “링크 공유” 기능이 제거되었습니다. OneDrive 사용자는 Microsoft의 [Windows용 OneDrive](https://onedrive.live.com/about/en-us/download/) 클라이언트를 사용하여 표준 파일 저장 및 열기 대화 상자를 통해 OneDrive에 저장된 파일에 액세스하는 것이 좋습니다.

## <a name="notable-fixed-bugs-reported-by-customers"></a>고객이 보고한 버그의 주목할 만한 수정

### <a name="in-tmt-preview-the-tool-crashes-when-using-the-standard-template"></a>TMT Preview에서 표준 템플릿을 사용할 때 도구에서 크래시가 발생합니다.

- 그리기 화면에 일반 스텐실(예: “일반 데이터 흐름”)이 추가되고 위협을 생성하면 도구에서 크래시가 발생할 수 있습니다. 이 문제가 해결되었습니다.

### <a name="in-tmt-preview-when-i-save-a-report-or-copy-the-threats-the-risk-levels-are-incorrect"></a>TMT Preview에서는 보고서를 저장하거나 위협을 복사할 때 위험 수준이 잘못되었습니다.

- 사용자가 특정 위협의 위험 수준을 수정한 후 보고서를 저장하거나 위험을 복사하면 위험 수준이 “높음”으로 돌아갈 수 있습니다. 이 문제가 해결되었습니다.

## <a name="known-issues-and-faq"></a>알려진 문제 및 FAQ

### <a name="users-of-high-resolution-screens-may-experience-small-text-in-the-threat-properties"></a>고해상도 화면의 사용자에게는 위협 속성에 작은 텍스트가 표시될 수 있습니다.

#### <a name="issue"></a>문제

도구의 분석 보기에서, Windows의 가독성을 위해 기본적으로 확대되도록 설정된 고해상도 화면이 사용자에게 있는 경우 위협의 “가능한 완화 방법” 섹션에 작은 텍스트가 표시될 수 있습니다.

![고해상도 화면의 알려진 문제](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_screen_resolution-300x153.png)

#### <a name="workaround"></a>해결 방법

사용자는 완화 텍스트를 클릭하고 표준 Windows 확대/축소 컨트롤(Crtl+마우스 휠 위로)을 사용하여 해당 섹션의 확대를 늘릴 수 있습니다.

### <a name="files-in-the-recently-opened-models-section-of-the-main-window-may-fail-to-open"></a>주 창의 “최근에 열린 모델” 섹션에 있는 파일이 열리지 않을 수 있음

#### <a name="issue"></a>문제

Preview 릴리스의 “OneDrive에서 열기” 기능이 제거되었습니다. OneDrive에 저장된 “최근에 열린 모델”이 있는 사용자에게 다음 오류가 표시됩니다.

![OneDrive 기능 제거됨](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_error-300x131.png)

#### <a name="workaround"></a>해결 방법

OneDrive 사용자는 Microsoft의 [Windows용 OneDrive](https://onedrive.live.com/about/en-us/download/) 클라이언트를 사용하여 표준 및 “모델 열기” 대화 상자를 통해 OneDrive에 저장된 파일에 액세스하는 것이 좋습니다.

![OneDrive 기능 제거됨](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_save_onedrive-300x149.png)

### <a name="my-organization-uses-the-2016-version-of-the-tool-can-i-use-the-azure-stencil-set"></a>내 조직에서 도구의 2016 버전을 사용하는데, Azure 스텐실 집합을 사용할 수 있나요?

예, 사용할 수 있습니다. [Azure 스텐실 집합은 github에서 사용할 수 있으며](https://github.com/Microsoft/threat-modeling-templates/), 도구의 2016 버전에서 로드될 수 있습니다. Azure 스텐실 집합을 사용하여 새 모델을 만들려면 기본 메뉴 화면에서 “새 모델의 템플릿” 대화 상자를 사용합니다. TMT 2016에서는 Azure 스텐실 집합의 “가능한 완화 방법” 필드에 있는 링크를 렌더링할 수 없으므로 링크가 HTML 태그로 표시될 수도 있습니다.

![2016 클라이언트의 Azure 스텐실 업데이트](./media/azure-security-threat-modeling-tool-releases-71509112/tmt_azure_stencils-300x212.png)

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - Microsoft Windows 10
- 필요한 .NET 버전
  - .NET 3.5.2
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)에 있으며 [도구 사용](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
