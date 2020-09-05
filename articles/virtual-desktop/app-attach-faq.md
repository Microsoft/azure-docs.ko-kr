---
title: Windows 가상 데스크톱 MSIX 앱 연결 FAQ-Azure
description: Windows 가상 데스크톱에 대 한 MSIX 앱 연결에 대 한 질문과 대답입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a63d7f067665836910b91b2911db522f0a92bbb1
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556273"
---
# <a name="msix-app-attach-faq"></a>MSIX 앱 연결 FAQ

이 문서에서는 Windows 가상 데스크톱에 대 한 MSIX 앱 연결에 대해 자주 묻는 질문과 대답을 설명 합니다.

## <a name="does-msix-app-attach-use-fslogix"></a>MSIX 앱 연결에서 FSLogix를 사용 하나요?

MSIX 앱 연결에서는 FSLogix를 사용 하지 않습니다. 그러나 앱 연결 및 FSLogix는 원활한 사용자 환경을 제공 하기 위해 함께 작동 하도록 설계 되었습니다.

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>Windows 가상 데스크톱 외부에서 MSIX 앱 연결을 사용할 수 있나요?

예, MSIX 앱 연결은 Windows 10 Enterprise에 포함 된 기능이 며 Windows 가상 데스크톱 외부에서 사용할 수 있습니다. 그러나 Windows 가상 데스크톱 외부에서 MSIX 앱 연결에 대 한 관리 평면이 없습니다.

## <a name="how-do-i-get-an-msix-package"></a>MSIX 패키지를 가져올 어떻게 할까요? 있나요?

소프트웨어 공급 업체는 MSIX 패키지를 제공 합니다. MSIX이 아닌 패키지를 MSIX으로 변환할 수도 있습니다. [기존 설치 관리자를 MSIX으로 이동 하는 방법](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)에 대해 자세히 알아보세요.

## <a name="which-operating-systems-support-msix-app-attach"></a>MSIX 앱 연결을 지 원하는 운영 체제는 무엇 인가요?

Windows 10 Enterprise 및 Windows 10 Enterprise 다중 세션.

## <a name="next-steps"></a>다음 단계

MSIX 앱 연결에 대해 자세히 알아보려면 [개요](what-is-app-attach.md) [용어집](app-attach-glossary.md)을 확인 하세요. 그렇지 않으면 [앱 연결 설정](app-attach.md)을 시작 합니다.