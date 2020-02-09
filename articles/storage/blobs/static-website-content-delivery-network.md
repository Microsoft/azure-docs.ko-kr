---
title: 정적 웹 사이트를 Azure CDN와 통합-Azure Storage
description: Azure Content Delivery Network (CDN)를 사용 하 여 Azure Storage 계정에서 정적 웹 사이트 콘텐츠를 캐시 하는 방법을 알아봅니다.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 01/22/2020
ms.openlocfilehash: aaf61ccbb3577036c614aa6196d2af57124550fa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908554"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Azure CDN와 정적 웹 사이트 통합

Azure 저장소 계정에 호스팅된 [정적 웹 사이트](storage-blob-static-website.md) 에서 [AZURE Content Delivery Network (CDN)](../../cdn/cdn-overview.md) 를 사용 하도록 설정 하 여 콘텐츠를 캐시할 수 있습니다. Azure CDN를 사용 하 여 정적 웹 사이트의 사용자 지정 도메인 엔드포인트을 구성 하 고, 사용자 지정 SSL 인증서를 프로 비전 하 고, 사용자 지정 재작성 규칙을 구성할 수 있습니다. Azure CDN을 구성하면 추가 비용이 발생하지만 전세계 어디에서나 웹 사이트에 대해 일관되게 낮은 대기 시간을 제공합니다. 또한 Azure CDN은 고유한 인증서를 사용하여 SSL 암호화를 제공합니다. 

Azure CDN 가격 책정에 대한 정보는 [Azure CDN 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조하세요.

## <a name="enable-azure-cdn-for-your-static-website"></a>정적 웹 사이트에 Azure CDN 사용

저장소 계정에서 직접 정적 웹 사이트에 대 한 Azure CDN를 사용 하도록 설정할 수 있습니다. [대형 파일 다운로드 최적화](../../cdn/cdn-optimization-overview.md#large-file-download)처럼 CDN 엔드포인트에 대한 고급 구성 설정을 지정하려면 [Azure CDN 확장](../../cdn/cdn-create-new-endpoint.md)을 대신 사용하여 CDN 프로필 및 엔드포인트를 만들면 됩니다.

1. Azure Portal에서 스토리지 계정을 찾아 계정 개요를 표시합니다.

2. **Blob 서비스** 메뉴에서 **Azure CDN**을 선택하여 Azure CDN을 구성합니다.

    **Azure CDN** 페이지가 나타납니다.

    ![CDN 엔드포인트 만들기](../../cdn/media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png)

3. **CDN 프로필** 섹션에서 새 CDN 프로필 또는 기존 CDN 프로필을 지정합니다. 

4. CDN 엔드포인트에 대한 가격 책정 계층을 지정합니다. 가격 책정에 대해 자세히 알아보려면 [Content Delivery Network 가격 책정](https://azure.microsoft.com/pricing/details/cdn/)을 참조 하세요. 각 계층에서 사용 가능한 기능에 대 한 자세한 내용은 [Azure CDN 제품 기능 비교](../../cdn/cdn-features.md)를 참조 하세요.

5. **CDN 엔드포인트 이름** 필드에서 CDN 엔드포인트에 대한 이름을 지정합니다. CDN 엔드포인트는 Azure에서 고유해야 합니다.

6. **원본 호스트 이름** 필드에서 정적 웹 사이트 엔드포인트를 지정합니다. 

   정적 웹 사이트 엔드포인트를 찾으려면 스토리지 계정에 대한 **정적 웹 사이트** 설정으로 이동합니다.  기본 엔드포인트을 복사 하 여 CDN 구성에 붙여넣습니다.

   > [!IMPORTANT]
   > URL에서 프로토콜 식별자 (*예:* HTTPS)와 후행 슬래시를 제거 해야 합니다. 예를 들어 정적 웹 사이트 엔드포인트이 `https://mystorageaccount.z5.web.core.windows.net/`경우 **원본 호스트 이름** 필드에 `mystorageaccount.z5.web.core.windows.net`를 지정 합니다.

   다음 이미지에서는 엔드포인트 구성의 예를 보여 줍니다.

   ![CDN 엔드포인트 구성 샘플을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

7. **만들기**를 선택 하 고 전파 될 때까지 기다립니다. 만든 엔드포인트는 엔드포인트 목록에 나타납니다.

8. CDN 엔드포인트가 올바르게 구성되었는지 확인하려면 엔드포인트를 클릭하여 해당 설정으로 이동합니다. 스토리지 계정에 대한 CDN 개요에서 다음 이미지와 같이 엔드포인트 호스트 이름을 찾아 해당 엔드포인트로 이동합니다. CDN 엔드포인트의 형식은 `https://staticwebsitesamples.azureedge.net`과 비슷합니다.

    ![CDN 엔드포인트의 개요를 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

9. CDN 엔드포인트 전파가 완료되어 CDN 엔드포인트로 이동하면 이전에 정적 웹 사이트에 업로드한 index.html 파일의 내용이 표시됩니다.

10. CDN 엔드포인트에 대한 원본 설정을 검토하려면 CDN 엔드포인트에 대한 **설정** 섹션 아래에서 **원본**으로 이동합니다. **원본 형식** 필드가 *사용자 지정 원본*으로 설정되어 있고 **원본 호스트 이름** 필드에 정적 웹 사이트 엔드포인트가 표시됩니다.

    ![CDN 엔드포인트에 대한 원본 설정을 보여 주는 스크린샷](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Azure CDN에서 콘텐츠 제거

더 이상 Azure CDN에 개체를 캐시하지 않으려면 다음 단계 중 하나를 수행할 수 있습니다.

* 컨테이너를 공용 대신 프라이빗으로 설정합니다. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](storage-manage-access-to-resources.md)를 참조하세요.
* Azure Portal을 사용하여 CDN 엔드포인트를 사용하지 않도록 설정하거나 삭제합니다.
* 더 이상 개체 요청에 응답하지 않도록 호스티드 서비스를 수정합니다.

Azure CDN에 이미 캐시된 개체는 개체의 TTL(Time-to-Live) 기간이 만료되거나 엔드포인트가 [삭제](../../cdn/cdn-purge-endpoint.md)될 때까지 캐시된 상태로 유지됩니다. TTL(Time-to-Live)이 만료되면 Azure CDN에서 CDN 엔드포인트가 여전히 유효하며 개체에 익명으로 액세스할 수 있는지 확인합니다. 그렇지 않으면 개체가 더 이상 캐시되지 않습니다.

## <a name="next-steps"></a>다음 단계

필드 Azure CDN 엔드포인트에 사용자 지정 도메인을 추가 합니다. [자습서: 사용자 지정 도메인을 Azure CDN 엔드포인트에 추가](../../cdn/cdn-map-content-to-custom-domain.md)를 참조 하세요.
