---
title: Azure Application Insights 내 Analytics 문제 해결 | Microsoft Docs
description: 'Application Insights Analytics에 문제가 있습니까? 여기에서 시작합니다. '
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9bbd5859-3584-4d80-9b6d-d5910fa48baa
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: mbullwin
ms.openlocfilehash: ecf0638aa999208331603ac30ccf4eb17b3c4500
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60692383"
---
# <a name="troubleshoot-analytics-in-application-insights"></a>Application Insights의 Analytics 문제 해결
[Application Insights Analytics](analytics.md)에 문제가 있습니까? 여기에서 시작합니다. Analytics는 Azure Application Insights의 강력한 검색 도구입니다.

## <a name="limits"></a>제한
* 현재, 쿼리 결과는 지난 주의 데이터로 제한됩니다.
* 테스트 브라우저: Chrome, Microsoft Edge 및 Internet Explorer 최신 버전입니다.

## <a name="known-incompatible-browser-extensions"></a>알려진 호환되지 않는 브라우저 확장
* Ghostery

확장을 사용하지 않도록 설정하거나 다른 브라우저를 사용합니다.

## <a name="e-a"></a> "예기치 않은 오류"
![예기치 않은 오류 화면](media/analytics-troubleshooting/010.png)

포털 런타임에 내부 오류 발생 – 처리되지 않은 예외.

* 브라우저의 캐시를 삭제합니다.

## <a name="e-b"></a>403 ... 다시 로드하세요.
![403 ... 다시 로드하십시오.](media/analytics-troubleshooting/020.png)

인증 관련 오류 발생(인증 또는 액세스 토크 생성 시). 브라우저 설정을 변경해야 포털 복구가 가능할 수도 있습니다.

* 브라우저에서 [타사 쿠키가 사용되도록 설정되어 있는지](#cookies) 확인합니다. 

## <a name="authentication"></a>403... 보안 영역 확인
![403... 보안 영역 확인](media/analytics-troubleshooting/030.png)

인증 관련 오류 발생(인증 또는 액세스 토크 생성 시). 브라우저 설정을 변경해야 포털 복구가 가능할 수도 있습니다.

1. 브라우저에서 [타사 쿠키가 사용되도록 설정되어 있는지](#cookies) 확인합니다. 
2. Analytics 포털을 여는 데 즐겨찾기, 책갈피, 타사 쿠키를 사용하였습니까? 링크를 저장할 때 사용한 자격 증명과 다른 것으로 로그인했습니까?
3. 비공개/시크릿 브라우저 창을 사용하세요(모든 해당 창을 닫은 후). 자격 증명을 입력해야 합니다. 
4. 다른 (일반) 브라우저 창을 열고 [Azure](https://portal.azure.com)로 이동합니다. 로그아웃합니다. 링크를 열고 올바른 자격 증명으로 로그인합니다.
5. Microsoft Edge와 Internet Explorer 사용자는 신뢰할 수 있는 영역 설정이 지원되지 않는 경우 이러한 오류를 받을 수도 있습니다.
   
    [Analytics 포털](https://portal.azure.com)과 [Azure Active Directory 포털](https://portal.azure.com)이 같은 보안 영역에 있는지 확인합니다.
   
   * Internet Explorer에서 **인터넷 옵션**, **보안**, **신뢰할 수 있는 사이트**, **사이트**를 엽니다.
     
     ![인터넷 옵션 대화 상자에서 신뢰할 수 있는 사이트에 사이트를 추가합니다.](media/analytics-troubleshooting/033.png)
     
     Websites 목록에 다음 URL이 포함되어 있다면 다른 URL도 포함하세요.
     
     https://analytics.applicationinsights.io<br/>
     https://login.microsoftonline.com<br/>
     https://login.windows.net

## <a name="e-d"></a>404 ... 리소스를 찾을 수 없음
![404 ... 리소스를 찾을 수 없음](media/analytics-troubleshooting/040.png)

애플리케이션 리소스가 Application Insights에서 삭제되어서 더는 사용할 수 없습니다. URL을 Analytics 페이지에 저장하면 이 오류가 발생합니다.

## <a name="e-e"></a>403 ... 권한 없음
![403 ... 권한 없음](media/analytics-troubleshooting/050.png)

Analytics에서 이 애플리케이션을 열 권한이 없습니다.

* 다른 사람에게 링크를 받았습니까? [이 리소스 그룹의 독자 또는 참가자](../../azure-monitor/app/resources-roles-access-control.md)에 포함되어 있는지 확인합니다.
* 다른 자격 증명으로 링크를 저장했습니까? [Azure 포털](https://portal.azure.com)을 열고 로그아웃한 다음, 다시 링크를 열고 올바른 자격 증명을 입력합니다.

## <a name="html-storage"></a>403 ... HTML5 저장소
포털에서는 HTML5 localStorage와 sessionStorage를 사용합니다.

* 크롬: 설정, 개인 정보 보호, 콘텐츠 설정
* Internet Explorer: 인터넷 옵션, 고급 탭, 보안, DOM 스토리지를 사용하도록 설정

![403 ... HTML5 저장소를 사용하려고 합니다.](media/analytics-troubleshooting/060.png)

## <a name="e-g"></a>404 ... 구독을 찾을 수 없음
![404 ... 구독을 찾을 수 없음](media/analytics-troubleshooting/070.png)

URL이 올바르지 않습니다. 

* [Application Insights 포털](https://portal.azure.com)에서 앱 리소스를 엽니다. 그런 다음 Analytics 단추를 사용합니다.

## <a name="e-h"></a>404 ... 페이지가 없습니다.
![404 ... 페이지가 존재하지 않습니다.](media/analytics-troubleshooting/080.png)

URL이 올바르지 않습니다.

* [Application Insights 포털](https://portal.azure.com)에서 앱 리소스를 엽니다. 그런 다음 Analytics 단추를 사용합니다.

## <a name="cookies"></a>타사 쿠키 사용
  [타사 쿠키를 사용하지 않도록 설정하는 방법](https://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers)을 참조하세요. 하지만 타사 쿠키를 **사용하도록 설정**해야 합니다.


[!INCLUDE [app-insights-analytics-footer](../../../includes/app-insights-analytics-footer.md)]

