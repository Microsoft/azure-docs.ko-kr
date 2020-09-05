---
title: HTTPS를 통해 Azure CDN 사용자 지정 도메인을 사용하여 스토리지 Blob에 액세스
description: Azure CDN 사용자 지정 도메인을 추가하고 사용자 지정 Blob 스토리지 엔드포인트에 대해 해당 도메인에서 HTTPS를 사용하도록 설정하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 5b6fe2b2704f101a7775b7eb700375105b0a9eca
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "81259887"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>자습서: HTTPS를 통해 Azure CDN 사용자 지정 도메인을 사용하여 스토리지 Blob에 액세스

Azure 스토리지 계정을 Azure CDN(Content Delivery Network)과 통합한 후, 사용자 지정 도메인을 추가하고 사용자 지정 Blob Storage 엔드포인트에 대한 해당 도메인에서 HTTPS를 사용하도록 설정할 수 있습니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서의 단계를 완료하려면 먼저 Azure Storage 계정을 Azure CDN과 통합해야 합니다. 자세한 내용은 [빠른 시작: Azure CDN과 Azure Storage 계정 통합](cdn-create-a-storage-account-with-cdn.md)을 참조하세요.

## <a name="add-a-custom-domain"></a>사용자 지정 도메인 추가
프로필에서 CDN 엔드포인트를 만들 때 azureedge.net의 하위 도메인인 엔드포인트 이름은 기본적으로 CDN 콘텐츠를 전송하는 URL에 포함됩니다. 사용자 지정 도메인을 CDN 엔드포인트에 연결하는 옵션도 제공됩니다. 이 옵션을 사용하면 엔드포인트 이름 대신 URL로 사용자 지정 도메인을 사용하여 콘텐츠를 전송합니다. 엔드포인트에 사용자 지정 도메인을 추가하려면 이 자습서의 지침을 따릅니다. [Azure CDN 엔드포인트에 사용자 지정 도메인을 추가합니다](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>HTTPS 구성
사용자 지정 도메인에서 HTTPS 프로토콜을 사용하면 데이터가 TLS/SSL 암호화를 통해 인터넷에서 안전하게 전송됩니다. 웹 브라우저가 HTTPS를 통해 웹 사이트에 연결되면 웹 사이트 보안 인증서의 유효성을 검사하고 합법적인 인증 기관에서 발급되었는지를 확인합니다. 사용자 지정 도메인에서 HTTPS를 구성하려면 이 자습서의 지침을 따릅니다. [Azure CDN 사용자 지정 도메인에서 HTTPS를 구성합니다](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>공유 액세스 서명
익명 읽기 액세스를 허용하지 않도록 Blob Storage 엔드포인트를 구성한 경우 [SAS(공유 액세스 서명)](cdn-sas-storage-support.md) 토큰을 사용자 지정 도메인에 사용자가 만든 각 요청에 제공해야 합니다. 기본적으로 Blob Storage 엔드포인트는 익명 읽기 액세스를 허용하지 않습니다. SAS에 대한 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../storage/blobs/storage-manage-access-to-resources.md)를 참조하세요.

Azure CDN은 SAS 토큰에 추가된 제한 사항을 무시합니다. 예를 들어 모든 SAS 토큰에 만료 시간이 있는 경우 CDN POP(point-of-presence)에서 콘텐츠가 제거되기 전에는 만료된 SAS를 사용하여 해당 콘텐츠에 계속 액세스할 수 있습니다. 캐시 응답 헤더를 설정하여 데이터를 Azure CDN에 얼마나 오래 캐시할 것인지 제어할 수 있습니다. 자세한 내용은 [Azure CDN에서 Azure Storage Blob 만료 관리](cdn-manage-expiration-of-blob-content.md)를 참조하세요.

동일한 Blob 엔드포인트에 대해 여러 SAS URL을 만드는 경우 쿼리 문자열 캐싱을 설정하는 방안을 고려해 보세요. 이렇게 하면 각 URL이 고유한 엔터티로 처리됩니다. 자세한 내용은 [쿼리 문자열을 사용하여 Azure CDN 캐싱 동작 제어](cdn-query-string.md)를 참조하세요.

## <a name="http-to-https-redirection"></a>HTTP-HTTPS 리디렉션
[표준 규칙 엔진](cdn-standard-rules-engine.md) 또는 [Verizon 프리미엄 규칙 엔진](cdn-verizon-premium-rules-engine.md)으로 URL 리디렉션 규칙을 만들어서 HTTP 트래픽을 HTTPS로 리디렉션하도록 선택할 수 있습니다. 표준 규칙 엔진은 Microsoft 프로필의 Azure CDN에만 사용할 수 있는 반면 Verizon 프리미엄 규칙 엔진은 Verizon 프로필의 Azure CDN 프리미엄에서만 사용할 수 있습니다.

![Microsoft 리디렉션 규칙](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

위의 규칙에서 호스트 이름, 경로, 쿼리 문자열 및 조각을 벗어나면 리디렉션에 들어오는 값이 사용됩니다. 

![Verizon 리디렉션 규칙](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

위의 규칙에서 *Cdn-endpoint-name*은 사용자가 CDN 엔드포인트에 대해 구성한 이름을 참조하며, 드롭다운 목록에서 이름을 선택할 수 있습니다. *Origin-path*의 값은 정적 콘텐츠가 있는 원본 스토리지 계정 내 경로를 참조합니다. 단일 컨테이너의 모든 정적 콘텐츠를 호스팅하는 경우 *origin-path*를 해당 컨테이너의 이름으로 바꿔야 합니다.

## <a name="pricing-and-billing"></a>가격 책정 및 대금 청구
Azure CDN을 통해 Blob에 액세스하는 경우 POP 서버와 원본(Blob Storage) 간 트래픽에는 [Blob Storage 가격](https://azure.microsoft.com/pricing/details/storage/blobs/), POP에서 액세스하는 데이터에는 [Azure CDN 가격](https://azure.microsoft.com/pricing/details/cdn/)으로 요금이 청구됩니다.

예를 들어 미국에 Azure CDN을 통해 액세스되는 스토리지 계정이 있는데, 유럽의 어떤 사람이 Azure CDN을 통해 해당 스토리지 계정의 Blob 중 하나에 액세스하려고 시도하면 Azure CDN은 유럽에서 가장 가까운 POP를 검사하여 해당 Blob을 찾습니다. Blob이 발견되면 Azure CDN은 해당 Blob의 복사본에 액세스하고 CDN 가격 책정을 사용합니다(Azure CDN에서 액세스되므로). Blob을 찾을 수 없는 경우 Azure CDN은 Blob을 POP 서버에 복사한 다음(이로 인해 Blob Storage 가격 책정에 지정된 대로 송신 및 트랜잭션 비용이 발생), POP 서버의 파일에 액세스합니다(이로 인해 Azure CDN 비용 발생).

## <a name="next-steps"></a>다음 단계
[자습서: Azure CDN 캐싱 규칙 설정](cdn-caching-rules-tutorial.md)




