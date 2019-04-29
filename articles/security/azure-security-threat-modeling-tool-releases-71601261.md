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
ms.date: 01/25/2019
ms.author: jegeib
ms.openlocfilehash: c96b924294286be57de90dae7e6534b5ed9306ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586245"
---
# <a name="threat-modeling-tool-update-release-71601261---1292019"></a>Threat Modeling Tool 업데이트 릴리스 7.1.60126.1 - 2019/1/29

2019년 1월 29일에 릴리스된 Microsoft Threat Modeling Tool 버전 7.1.60126.1에는 다음 변경 내용이 포함되어 있습니다.

- .NET의 최소 필수 버전 늘어났습니다 [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262)합니다.
- .NET 종속성으로 인해 Windows의 최소 필수 버전이 [Windows 10 1주년 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)로 높아졌습니다.
- 도구의 옵션 메뉴에 모델 유효성 검사 토글 기능이 추가되었습니다.
- 위협 속성의 몇 가지 링크가 업데이트되었습니다.
- 도구 홈 화면의 UX가 다소 변경되었습니다.
- 이제 Threat Modeling Tool은 호스트 운영 체제의 TLS 설정을 상속하고 TLS 1.2 이상이 필요한 환경에서 지원됩니다.

## <a name="feature-changes"></a>기능 변경

### <a name="model-validation-option"></a>모델 유효성 검사 옵션

고객 피드백에 따라 모델 유효성 검사를 사용하거나 사용하지 않도록 설정하는 옵션이 도구에 추가되었습니다. 이전에 템플릿에서 두 개체 간의 단방향 데이터 흐름 하나를 사용했다면 메시지 프레임에 ObjectsName 'Any'가 하나 이상 필요하다는 오류 메시지가 표시되었을 수 있습니다. 모델 유효성 검사를 사용하지 않도록 설정하면 이러한 경고가 보기에 표시되지 않습니다.

모델 유효성 검사를 설정/해제하는 옵션은 파일->설정->옵션 메뉴에 있습니다. 이 설정의 기본값은 사용 안 함입니다.

![모델 유효성 검사 옵션](./media/azure-security-threat-modeling-tool-releases-71601261/tmt_model_validation_option.png)

## <a name="system-requirements"></a>시스템 요구 사항

- 지원되는 운영 체제
  - [Microsoft Windows 10 1주년 업데이트](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97) 이상
- 필요한 .NET 버전
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 이상
- 추가 요구 사항
  - 도구와 템플릿에 대한 업데이트를 받으려면 인터넷 연결이 필요합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="unsupported-systems"></a>지원되지 않는 시스템

#### <a name="issue"></a>문제

Windows 10 Enterprise LTSB(버전 1507)와 같이 .NET 4.7.1 이상을 설치할 수 없는 Windows 10 시스템 사용자는 업그레이드 후에 도구를 열 수 없습니다. 이러한 이전 버전 Windows는 Threat Modeling Tool용으로 더 이상 지원되지 않는 플랫폼이므로 최신 업데이트를 설치하면 안 됩니다.

#### <a name="workaround"></a>해결 방법

최신 업데이트를 설치한 Windows 10 Enterprise LTSB(버전 1507) 사용자는 앱 및 기능의 제거 대화 상자를 통해 Threat Modeling Tool의 이전 버전으로 되돌릴 수 있습니다.

## <a name="documentation-and-feedback"></a>문서 및 피드백

- Threat Modeling Tool에 대한 문서는 [docs.microsoft.com](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool)에 있으며 [도구 사용](https://docs.microsoft.com/azure/security/azure-security-threat-modeling-tool-getting-started)에 대한 정보를 포함합니다.

## <a name="next-steps"></a>다음 단계

[Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)의 최신 버전을 다운로드합니다.
