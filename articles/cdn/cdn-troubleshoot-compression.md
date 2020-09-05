---
title: Azure CDN의 파일 압축 문제 해결 | Microsoft Docs
description: Azure Content Delivery Network에서 파일 압축 문제를 해결 하는 방법에 대해 알아봅니다. 이 문서에서는 몇 가지 가능한 원인을 설명 합니다.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 53db148eac0d56e53bb96e0597ad53d3183d86e9
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192519"
---
# <a name="troubleshooting-cdn-file-compression"></a>CDN 파일 압축 문제 해결
이 문서는 [CDN 파일 압축](cdn-improve-performance.md)관련 문제를 해결하는 데 도움이 됩니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 이동하여 **지원 받기**를 클릭합니다.

## <a name="symptom"></a>증상
엔드포인트에 대한 압축이 활성화되어 있지만 파일이 압축되지 않은 상태로 반환됩니다.

> [!TIP]
> 반환된 파일이 압축되었는지 확인하려면 [Fiddler](https://www.telerik.com/fiddler) 또는 브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)와 같은 도구를 사용해야 합니다.  캐시된 CDN 콘텐츠와 함께 반환된 HTTP 응답 헤더를 확인하세요.  명명된 `Content-Encoding` 헤더에 **gzip**, **bzip2** 또는 **deflate** 값이 있는 경우 콘텐츠가 압축된 것입니다.
> 
> ![Content-Encoding 헤더](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>원인
몇 가지 가능한 원인은 다음과 같습니다.

* 요청된 콘텐츠에 압축을 사용할 수 없습니다.
* 압축은 요청된 파일 형식에 대해 활성화되지 않습니다.
* HTTP 요청에 유효한 압축 형식을 요청하는 헤더가 포함되지 않았습니다.
* 원본에서 청크 분할 된 콘텐츠를 보내는 중입니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
> [!TIP]
> 새 엔드포인트 배포와 마찬가지로, CDN 구성 변경이 네트워크 전체에 전파되려면 다소 시간이 걸립니다.  일반적으로 변경 내용은 90분 이내에 적용됩니다.  CDN 엔드포인트에 압축을 처음으로 설정한 경우 압축 설정이 POP까지 전파되도록 1~2시간 기다리는 것이 좋습니다. 
> 
> 

### <a name="verify-the-request"></a>요청 확인
우선, 요청에 대해 빠른 온전성 검사를 수행합니다.  브라우저의 [개발자 도구](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) 를 사용하여 생성되는 요청을 볼 수 있습니다.

* 요청이 원본이 아닌 엔드포인트 URL, `<endpointname>.azureedge.net`에 전송되는지 확인합니다.
* 요청에 **Accept-Encoding** 헤더가 포함되는지, 헤더 값에 **gzip**, **deflate** 또는 **bzip2**가 포함되는지 확인합니다.

> [!NOTE]
> **Akamai의 Azure CDN** 프로필은 **gzip** 인코딩만 지원합니다.
> 
> 

![CDN 요청 헤더](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>압축 설정 확인(표준 CDN 프로필)
> [!NOTE]
> 이 단계는 CDN 프로필이 **Verizon의 Azure CDN 표준**, **Verizon의 Azure CDN 표준** 또는 **Akamai의 Azure CDN 표준** 프로필인 경우에만 적용됩니다. 
> 
> 

[Azure 포털](https://portal.azure.com) 에서 엔드포인트로 이동하여 **구성** 단추를 클릭합니다.

* 압축이 활성화되어 있는지 확인합니다.
* 압축될 콘텐츠에 대한 MIME 형식이 압축된 형식의 목록에 포함되어 있는지 확인합니다.

![CDN 압축 설정](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>압축 설정 확인(프리미엄 CDN 프로필)
> [!NOTE]
> 이 단계는 CDN 프로필이 **Verizon의 Azure CDN 프리미엄** 프로필인 경우에만 적용됩니다.
> 
> 

[Azure 포털](https://portal.azure.com) 에서 엔드포인트로 이동하여 **관리** 단추를 클릭합니다.  보조 포털이 열립니다.  **HTTP Large** 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다.  **압축**을 클릭합니다. 

* 압축이 활성화되어 있는지 확인합니다.
* **파일 형식** 목록에 쉼표로 구분된(공백 없음) MIME 형식 목록이 포함되는지 확인합니다.
* 압축될 콘텐츠에 대한 MIME 형식이 압축된 형식의 목록에 포함되어 있는지 확인합니다.

![CDN 프리미엄 압축 설정](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>콘텐츠 캐시 여부 확인(Verizon CDN 프로필)
> [!NOTE]
> 이 단계는 CDN 프로필이 **Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필인 경우에만 적용됩니다.
> 
> 

브라우저의 개발자 도구를 사용하여, 요청되는 지역에서 파일이 캐시되는지 응답 헤더를 확인합니다.

* **Server** 응답 헤더를 확인합니다.  헤더에는 **Platform(POP/Server ID)** 형식이 포함되어야 합니다(다음 예제 참조).
* **X-Cache** 응답 헤더를 확인합니다.  헤더는 **HIT**여야 합니다.  

![CDN 응답 헤더](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>파일의 크기 요건 충족 여부 확인(Verizon CDN 프로필)
> [!NOTE]
> 이 단계는 CDN 프로필이 **Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필인 경우에만 적용됩니다.
> 
> 

압축을 적용할 수 있으려면, 파일은 다음과 같은 크기 요건을 충족해야 합니다.

* 128바이트 초과.
* 1MB 미만.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>원본 서버의 요청에서 **Via** 헤더 확인
**Via** HTTP 헤더는 요청이 프록시 서버에 의해 전달되고 있음을 웹 서버에 알립니다.  기본적으로 Microsoft IIS 웹 서버는 요청에 **Via** 헤더가 들어 있으면 응답을 압축하지 않습니다.  이 동작을 재정의하려면 다음 단계를 수행합니다.

* **IIS 6**: [IIS 메타베이스 속성에서 HcNoCompressionForProxies="FALSE" 설정](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 이상**: [서버 구성에서 **noCompressionForHttp10** 및 **noCompressionForProxies**를 둘 다 False로 설정](https://www.iis.net/configreference/system.webserver/httpcompression)

