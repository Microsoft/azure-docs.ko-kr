---
title: 문제 해결을 위한 브라우저 추적 캡처 Microsoft Docs
description: 브라우저 추적에서 네트워크 정보를 캡처하여 Azure Portal 문제를 해결 하는 데 도움을 줍니다.
services: azure-portal
keywords: ''
author: mgblythe
ms.author: mblythe
ms.date: 01/09/2020
ms.topic: troubleshooting
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 2b506c9d15dafcd23b24207fe15ed0532939209f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310699"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>문제 해결을 위한 브라우저 추적 캡처

Azure Portal 문제를 해결 하 고 Microsoft 지원에 문의 해야 하는 경우 먼저 브라우저 추적 및 몇 가지 추가 정보를 캡처하는 것이 좋습니다. 수집 하는 정보는 문제가 발생할 때 포털에 대한 중요 한 세부 정보를 제공할 수 있습니다. 사용 하는 브라우저의 개발자 도구에 대한이 문서의 단계에 따라 Google Chrome 또는 Microsoft Edge (Chromium), Microsoft Edge (EdgeHTML) 또는 Apple Safari를 사용 합니다.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 및 Microsoft Edge (Chromium)

Google Chrome 및 Microsoft Edge (Chromium)는 모두 [Chromium 오픈 소스 프로젝트](https://www.chromium.org/Home)를 기반으로 합니다. 다음 단계에서는 두 브라우저에서 매우 유사한 개발자 도구를 사용 하는 방법을 보여 줍니다. 자세한 내용은 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) 및 [Microsoft Edge (Chromium) 개발자 도구](/microsoft-edge/devtools-guide-chromium)를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적을 시작 _하기 전에_ 로그인 하 여 추적에 로그인과 관련 된 중요 한 정보가 포함 되지 않도록 하는 것이 중요 합니다. 

1. [단계 레코더](https://support.microsoft.com/help/22878/windows-10-record-steps)를 사용 하 여 포털에서 수행 하는 단계 기록을 시작 합니다.

1. 포털에서 문제가 발생 하는 위치의 바로 앞에 있는 단계로 이동 합니다.

1. F12 키를 누르거나 브라우저 설정 아이콘 ![스크린샷을 선택 하](media/capture-browser-trace/chromium-icon-settings.png)**개발자 도구** >  > **추가 도구** 를 선택 합니다.

1. 기본적으로 브라우저는 현재 로드 된 페이지에 대해서만 추적 정보를 유지 합니다. 재현에 두 개 이상의 페이지로 이동 해야 하는 경우에도 브라우저에서 모든 추적 정보를 유지 하도록 하려면 다음 옵션을 설정 합니다.

    1. **네트워크** 탭을 선택한 다음 **로그 유지**를 선택 합니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. **콘솔** 탭을 선택 하 고 **콘솔 설정**을 선택한 다음 **로그 보존**을 선택 합니다. **콘솔 설정** 을 다시 선택 하 여 설정 창을 닫습니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/chromium-console-preserve-log.png)

1. **네트워크** 탭을 선택한 후 **네트워크 로그 기록 중지** 를 선택 하 고 **선택을 취소**합니다.

    !["네트워크 로그 기록 중지" 및 "Clear"의 스크린샷](media/capture-browser-trace/chromium-stop-clear-session.png)

1. **네트워크 로그 기록**을 선택한 다음 포털에서 문제를 재현 합니다.

    !["프로 파일링 세션 시작"의 스크린샷](media/capture-browser-trace/chromium-start-session.png)

    다음 이미지와 유사한 세션 출력이 표시 됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현 한 후 **네트워크 로그 기록 중지**를 선택 하 고 **HAR 내보내기** 를 선택 하 고 파일을 저장 합니다.

    !["Export HAR"의 스크린샷](media/capture-browser-trace/chromium-network-export-har.png)

1. 단계 레코더를 중지 하 고 파일을 저장 합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 마우스 오른쪽 단추로 클릭 한 다음 다른 **이름으로 저장**...을 선택 하 고 콘솔 출력을 텍스트 파일에 저장 합니다.

    ![콘솔 출력의 스크린샷](media/capture-browser-trace/chromium-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 .zip과 같은 압축 된 형식으로 패키지 하 고 Microsoft 지원과 공유 합니다.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge (EdgeHTML)

다음 단계에서는 Microsoft Edge에서 개발자 도구 (EdgeHTML)를 사용 하는 방법을 보여 줍니다. 자세한 내용은 [Microsoft Edge (EdgeHTML) 개발자 도구](/microsoft-edge/devtools-guide)를 참조 하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적을 시작 _하기 전에_ 로그인 하 여 추적에 로그인과 관련 된 중요 한 정보가 포함 되지 않도록 하는 것이 중요 합니다. 

1. [단계 레코더](https://support.microsoft.com/help/22878/windows-10-record-steps)를 사용 하 여 포털에서 수행 하는 단계 기록을 시작 합니다.

1. 포털에서 문제가 발생 하는 위치의 바로 앞에 있는 단계로 이동 합니다.

1. F12 키를 누르거나 브라우저 설정 아이콘 ![스크린샷을 선택 하](media/capture-browser-trace/edge-icon-settings.png)**개발자 도구** >  > **추가 도구** 를 선택 합니다.

1. 기본적으로 브라우저는 현재 로드 된 페이지에 대해서만 추적 정보를 유지 합니다. 재현에 두 개 이상의 페이지로 이동 해야 하는 경우에도 브라우저에서 모든 추적 정보를 유지 하도록 하려면 다음 옵션을 설정 합니다.

    1. **네트워크** 탭을 선택 하 고 **탐색에서 항목 지우기**옵션을 선택 취소 합니다.

          !["탐색 항목 지우기"의 스크린샷](media/capture-browser-trace/edge-network-clear-entries.png)

    1. **콘솔** 탭을 선택한 다음 **로그 유지**를 선택 합니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/edge-console-preserve-log.png)

1. **네트워크** 탭을 선택한 후 **프로 파일링 세션 중지** 및 **세션 지우기**를 선택 합니다.

    !["프로 파일링 세션 중지" 및 "세션 지우기"의 스크린샷](media/capture-browser-trace/edge-stop-clear-session.png)

1. **프로 파일링 세션 시작**을 선택한 다음 포털에서 문제를 재현 합니다.

    !["프로 파일링 세션 시작"의 스크린샷](media/capture-browser-trace/edge-start-session.png)

    다음 이미지와 유사한 세션 출력이 표시 됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/edge-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현 한 후 **프로 파일링 세션 중지**를 선택 하 고 **HAR로 내보내기** 를 선택 하 고 파일을 저장 합니다.

    !["HAR로 내보내기"의 스크린샷](media/capture-browser-trace/edge-network-export-har.png)

1. 단계 레코더를 중지 하 고 파일을 저장 합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 선택 하 고 창을 확장 합니다. 콘솔 출력의 시작 부분에 커서를 놓고 출력의 전체 내용을 끌어서 선택 합니다. 마우스 오른쪽 단추로 클릭 한 다음 **복사**를 선택 하 고 콘솔 출력을 텍스트 파일에 저장 합니다.

    ![콘솔 출력의 스크린샷](media/capture-browser-trace/edge-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 .zip과 같은 압축 된 형식으로 패키지 하 고 Microsoft 지원과 공유 합니다.

## <a name="apple-safari"></a>Apple Safari

다음 단계에서는 Apple Safari에서 개발자 도구를 사용 하는 방법을 보여 줍니다. 자세한 내용은 [Safari 개발자 도구 개요](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)를 참조 하세요.

1. Apple Safari에서 개발자 도구를 사용 하도록 설정 합니다.

    1. **Safari**를 선택 하 고 **기본 설정**을 선택 합니다.

        ![Safari 기본 설정의 스크린샷](media/capture-browser-trace/safari-preferences.png)

    1. **고급** 탭을 선택 하 고 **메뉴 모음에서 개발 메뉴 표시**를 선택 합니다.

        ![Safari 고급 기본 설정의 스크린샷](media/capture-browser-trace/safari-show-develop-menu.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적을 시작 _하기 전에_ 로그인 하 여 추적에 로그인과 관련 된 중요 한 정보가 포함 되지 않도록 하는 것이 중요 합니다. 

1. 포털에서 수행 하는 단계 기록을 시작 합니다. 자세한 내용은 [Mac에서 화면을 기록 하는 방법](https://support.apple.com/HT208721)을 참조 하세요.

1. 포털에서 문제가 발생 하는 위치의 바로 앞에 있는 단계로 이동 합니다.

1. **개발**을 선택한 다음 **웹 검사기 표시**를 선택 합니다.

    !["웹 검사기 표시"의 스크린샷](media/capture-browser-trace/safari-show-web-inspector.png)

1. 기본적으로 브라우저는 현재 로드 된 페이지에 대해서만 추적 정보를 유지 합니다. 재현에 두 개 이상의 페이지로 이동 해야 하는 경우에도 브라우저에서 모든 추적 정보를 유지 하도록 하려면 다음 옵션을 설정 합니다.

    1. **네트워크** 탭을 선택한 다음 **로그 유지**를 선택 합니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/safari-network-preserve-log.png)

    1. **콘솔** 탭을 선택한 다음 **로그 유지**를 선택 합니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/safari-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 **네트워크 항목 지우기**를 선택 합니다.

    !["네트워크 항목 지우기"의 스크린샷](media/capture-browser-trace/safari-clear-session.png)

1. 포털에서 문제를 재현 합니다. 다음 이미지와 유사한 세션 출력이 표시 됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/safari-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현 한 후 **내보내기** 를 선택 하 여 파일을 저장 합니다.

    !["내보내기"의 스크린샷](media/capture-browser-trace/safari-network-export-har.png)

1. 화면 레코더를 중지 하 고 파일을 저장 합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 선택 하 고 창을 확장 합니다. 콘솔 출력의 시작 부분에 커서를 놓고 출력의 전체 내용을 끌어서 선택 합니다. 명령을 사용 하 여 출력을 복사 하 고 텍스트 파일에 저장 합니다.

    ![콘솔 출력의 스크린샷](media/capture-browser-trace/safari-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 .zip과 같은 압축 된 형식으로 패키지 하 고 Microsoft 지원과 공유 합니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal 개요](azure-portal-overview.md)