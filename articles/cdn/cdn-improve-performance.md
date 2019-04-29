---
title: Azure CDN에서 파일을 압축하여 성능 향상 | Microsoft Docs
description: Azure CDN에서 파일을 압축하여 파일 전송 속도를 개선하고 페이지 로드 성능을 향상시키는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2018
ms.author: magattus
ms.openlocfilehash: afe959e80b339db5112fa97fd79d0528390e3954
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637008"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Azure CDN에서 파일을 압축하여 성능 향상
파일 압축은 파일이 서버에서 전송되기 전에 파일 크기를 줄여서 파일 전송 속도를 개선하고 페이지 로드 성능을 높이는 간단하고 효과적인 방법입니다. 파일 압축을 통해 대역폭 비용을 절감하고 사용자에게 반응이 빠른 환경을 제공할 수 있습니다.

파일 압축을 사용하도록 설정하는 두 가지 방법이 있습니다.

- 원본 서버에서 압축을 사용합니다. 이 경우 Azure CDN은 압축된 파일을 전달하고 이러한 파일을 요청하는 클라이언트에 배달합니다.
- CDN POP 서버에서 직접 압축을 사용하도록 설정합니다(*즉석에서 압축*). 이 경우 원본 서버에서 압축되지 않더라도 CDN이 파일을 압축하여 최종 사용자에게 제공합니다.

> [!IMPORTANT]
> Azure CDN 구성 변경이 네트워크 통해 전파되려면 다소 시간이 걸릴 수 있습니다. 
> - **Microsoft의 Azure CDN 표준** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
> - **Akamai의 Azure CDN Standard** 프로필의 경우, 일반적으로 1분 이내에 전파가 완료됩니다. 
> - **Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우 일반적으로 10분 이내에 전파가 완료됩니다. 
> 
> CDN 엔드포인트에 처음으로 압축을 설정하는 경우 압축 설정이 POP까지 전파되도록 1-2시간 기다렸다가 문제를 해결합니다.

## <a name="enabling-compression"></a>압축을 사용하도록 설정
표준 및 프리미엄 CDN 계층은 동일한 압축 기능을 제공하지만 사용자 인터페이스는 다릅니다. 표준과 프리미엄 CDN 계층 간의 차이점에 대한 자세한 내용은 [Azure CDN 개요](cdn-overview.md)를 참조하세요.

### <a name="standard-cdn-profiles"></a>표준 CDN 프로필 
> [!NOTE]
> 이 섹션은 **Microsoft의 Azure CDN 표준**, **Verizon의 Azure CDN 표준** 및 **Akamai의 Azure CDN 표준** 프로필에 적용됩니다.
> 
> 

1. CDN 프로필 페이지에서 관리하려는 CDN 엔드포인트를 선택합니다.

    ![CDN 프로필 엔드포인트](./media/cdn-file-compression/cdn-endpoints.png)

    CDN 엔드포인트 페이지가 열립니다.
2. **압축**을 선택합니다.

    ![CDN 압축 선택](./media/cdn-file-compression/cdn-compress-select-std.png)

    압축 페이지가 열립니다.
3. **켜기**를 선택하여 압축을 켭니다.

    ![CDN 파일 압축 옵션](./media/cdn-file-compression/cdn-compress-standard.png)
4. 기본 MIME 형식을 사용하거나, MIME 형식을 추가 또는 제거하여 목록을 수정합니다.

   > [!TIP]
   > 가능하지만 압축된 형식에 압축을 적용하는 것은 좋지 않습니다. 예를 들면 ZIP, MP3, MP4 또는 JPG 등이 있습니다.
   > 

   > [!NOTE]
   > MIME 형식의 기본 목록을 수정하는 것은 현재 Microsoft의 Azure CDN 표준에서 지원되지 않습니다.
   > 

5. 변경 후 **저장**을 선택합니다.

### <a name="premium-cdn-profiles"></a>프리미엄 CDN 프로필
> [!NOTE]
> 이 섹션은 **Verizon의 Azure CDN Premium** 프로필에만 적용됩니다.
> 

1. CDN 프로필 페이지에서 **관리**를 선택합니다.

    ![CDN 관리 선택](./media/cdn-file-compression/cdn-manage-btn.png)

    CDN 관리 포털이 열립니다.
2. **HTTP Large** 탭을 가리킨 다음 **캐시 설정** 플라이아웃을 가리킵니다. **압축**을 선택합니다.

    ![CDN 압축 선택](./media/cdn-file-compression/cdn-compress-select.png)

    압축 옵션이 표시됩니다.

    ![CDN 파일 압축 옵션](./media/cdn-file-compression/cdn-compress-files.png)
3. **압축 사용**을 선택하여 압축을 사용하도록 설정합니다. 쉼표로 구분된 목록(공백 없음)으로 압축하려는 MIME 형식을 **파일 형식** 상자에 입력합니다.

   > [!TIP]
   > 가능하지만 압축된 형식에 압축을 적용하는 것은 좋지 않습니다. 예를 들면 ZIP, MP3, MP4 또는 JPG 등이 있습니다.
   > 

4. 변경 후 **업데이트**를 선택합니다.

## <a name="compression-rules"></a>압축 규칙

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Microsoft의 Azure CDN 표준 프로필

**Microsoft의 Azure CDN 표준** 프로필의 경우에는 적합한 파일만 압축됩니다. 압축이 가능하려면 파일이 다음 조건을 충족해야 합니다.
- 된 MIME 형식 이어야 [압축 용으로 구성](#enabling-compression)합니다.
- 1KB 보다 클 수
- 8MB 보다 작을 수

이러한 프로필은 다음과 같은 압축 인코딩을 지원합니다.
- gzip(GNU zip)
- brotli 

요청에서 두 가지 이상의 압축 형식을 지원하는 경우 brotli 압축이 우선적으로 사용됩니다.

자산에 대한 요청이 gzip 압축을 지정하고 캐시의 요청 결과가 누락된 경우 Azure CDN은 POP 서버에서 직접 자산의 gzip 압축을 수행합니다. 이후 압축된 파일은 캐시에서 제공됩니다.

### <a name="azure-cdn-from-verizon-profiles"></a>Verizon의 Azure CDN 프로필

**Verizon의 Azure CDN 표준** 및 **Verizon의 Azure CDN 프리미엄** 프로필의 경우, 적합한 파일만 압축될 수 있습니다. 압축이 가능하려면 파일이 다음 조건을 충족해야 합니다.
- 128바이트 초과.
- 3MB 보다 작을 수

이러한 프로필은 다음과 같은 압축 인코딩을 지원합니다.
- gzip(GNU zip)
- DEFLATE
- bzip2
- brotli 

요청에서 두 가지 이상의 압축 형식을 지원하는 경우 해당 압축 형식은 brotli 압축보다 우선합니다.

자산에 대한 요청이 brotli 압축(HTTP 헤더는 `Accept-Encoding: br`임)을 지정하고 캐시의 요청 결과가 누락된 경우 Azure CDN은 POP 서버에서 직접 자산의 brotli 압축을 수행합니다. 이후 압축된 파일은 캐시에서 제공됩니다.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Akamai의 Azure CDN 표준 프로필

**Akamai의 Azure CDN 표준** 프로필의 경우, 모든 파일이 압축에 적합합니다. 그러나 파일은 [압축용으로 구성된](#enabling-compression) MIME 형식이어야 합니다.

이러한 프로필은 gzip 압축 인코딩만 지원합니다. 프로필 엔드포인트가 gzip 인코딩 파일을 요청하는 경우 클라이언트 요청에 관계없이 항상 원본으로부터 요청됩니다. 

## <a name="compression-behavior-tables"></a>압축 동작 표
다음 표는 모든 시나리오에 적용되는 Azure CDN 압축 동작을 설명합니다.

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>압축이 비활성화되었거나 파일이 압축에 부적합
| 클라이언트 요청 형식(Accept-Encoding 헤더를 통한) | 캐시된 파일 형식 | 클라이언트에 대한 CDN 응답 | 참고&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| 압축 |압축 |압축 | |
| 압축 |미압축 |미압축 | |
| 압축 |캐시되지 않음 |압축 또는 미압축 |원래 응답은 CDN의 압축 수행 여부를 결정합니다. |
| 미압축 |압축 |미압축 | |
| 미압축 |미압축 |미압축 | |
| 미압축 |캐시되지 않음 |미압축 | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>압축이 활성화되고 파일이 압축에 적합
| 클라이언트 요청 형식(Accept-Encoding 헤더를 통한) | 캐시된 파일 형식 | 클라이언트에 대한 CDN 응답 | 메모 |
| --- | --- | --- | --- |
| 압축 |압축 |압축 |지원되는 형식 간 CDN 코드 변환. |
| 압축 |미압축 |압축 |CDN이 압축 수행. |
| 압축 |캐시되지 않음 |압축 |원본에서 미압축 파일을 반환하면 CDN이 압축을 수행합니다. <br/>**Verizon에서 Azure CDN** 은 첫 번째 요청에 압축되지 않은 파일을 전달한 다음 후속 요청에 대한 파일을 압축하고 캐시합니다. <br/>`Cache-Control: no-cache` 헤더가 있는 파일은 압축되지 않습니다. |
| 미압축 |압축 |미압축 |CDN이 압축을 풉니다. |
| 미압축 |미압축 |미압축 | |
| 미압축 |캐시되지 않음 |미압축 | |

## <a name="media-services-cdn-compression"></a>Media Services CDN 압축
Media Services CDN 스트리밍을 사용하도록 설정된 엔드포인트의 경우 다음 MIME 형식에 대해 기본적으로 압축이 사용됩니다. 
- application/vnd.ms-sstr+xml 
- application/dash+xml
- application/vnd.apple.mpegurl
- application/f4m+xml 

## <a name="see-also"></a>참고 항목
* [CDN 파일 압축 문제 해결](cdn-troubleshoot-compression.md)    

