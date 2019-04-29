---
title: Aspera를 사용하여 Azure Media Services 계정에 파일 업로드 | Microsoft Docs
description: 이 자습서에서는 Azure에서 **Aspera Server On Demand** 서비스를 사용하여 Media Services 계정과 연결된 저장소 계정에 파일을 업로드하는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: e522883da7fddad44741599107f2dbc4c99aace6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60827021"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Azure에서 Aspera Server On Demand 서비스를 사용하여 Media Services 계정에 파일 업로드 

## <a name="overview"></a>개요

**Aspera** 는 고속 파일 전송 소프트웨어입니다. Azure용 **Aspera Server On Demand**는 Azure BLOB 개체 저장소에 직접 대용량 파일을 고속으로 업로드 및 다운로드할 수 있게 해줍니다. **Aspera On Demand**에 대한 자세한 내용은 [Aspera Cloud](http://cloud.asperasoft.com/) 사이트를 참조하세요. 
  
Azure용 **Aspera Server On Demand**는 [Azure Marketplace](https://azure.microsoft.com/marketplace/)에서 구매할 수 있습니다. Azure용 **Aspera Server On Demand**의 구매를 완료하려면 Windows Live ID를 사용하여 Azure Marketplace에 로그인하세요.

이 자습서에서는 Azure에서 **Aspera Server On-Demand** 서비스를 사용하여 Media Services 계정과 연결된 저장소 계정에 파일을 업로드하는 단계를 안내합니다. 

[여기](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest)에서는 Aspera 및 Media Services와 함께 Azure Functions를 사용하는 방법을 보여 주는 예제를 찾을 수 있습니다.

>[!NOTE]
>Azure Media Services MP(미디어 프로세서)에서 처리할 수 있는 최대 파일 크기에 제한이 있습니다. 파일 크기 제한에 대한 세부 정보는 [이](media-services-quotas-and-limitations.md) 문서를 참조하세요.
>

## <a name="prerequisites"></a>필수 조건 

이 자습서를 완료하려면 다음이 필요합니다.

* Windows Live ID
* [Azure 계정](https://azure.microsoft.com) 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 
* [Azure Media Services 계정](media-services-portal-create-account.md)

## <a name="purchase-aspera-on-demand-for-azure"></a>Azure용 Aspera On Demand 구매

Azure Marketplace에 로그인하고 나서 다음 기본 단계를 따라 Azure용 Aspera On Demand의 구매를 완료합니다.

1. Aspera를 검색하고 'Server On Demand'를 선택합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. 구독 계획을 검토하고 '등록'을 클릭합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Server on Demand 구독에 대한 세부 정보를 입력합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. **가격 책정 계층**을 클릭하고 하위 패널에서 원하는 월별 볼륨을 선택합니다. **계획 세부 정보** 패널에서 **확인**을 선택합니다. 그런 다음 **가격 책정 계층 선택** 패널에서 **선택**을 클릭합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. **약관**을 클릭하여 하위 패널에서 약관을 보고 동의합니다. 약관을 검토했으면 **구매**를 클릭합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. **만들기**를 클릭하여 구매를 완료합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. Azure 대시보드에서 서비스를 프로비전하고 있다는 알림을 표시합니다.  프로비전이 완료되면 리소스에서 서비스 이름을 검색하여 새 구독을 찾을 수 있습니다. 서비스를 찾았으면 서비스를 두 번 클릭하여 서비스 관리 포털을 시작합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Azure 관리 포털을 시작합니다. 새 Aspera 서비스를 찾았으면 서비스를 클릭하여 관리 포털에 액세스할 수 있습니다.  새 패널이 시작됩니다. 새 패널 내에서 새 서비스의 **리소스 이름**을 클릭해야 합니다.  다음 스크린샷에서 리소스 이름은 'AsperaTransferDemo'입니다. 리소스 이름을 클릭하면 다른 패널이 시작됩니다. 새로 시작된 패널에 '관리' 링크가 표시됩니다. '관리' 링크를 클릭하여 Aspera 관리 포털을 시작합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. 관리 링크를 클릭하면 서비스에 액세스하는 데 필요한 등록 페이지로 이동합니다.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. 이때 Aspera 서비스 관리 포털에 액세스할 수 있어야 선택키를 만들고, Aspera 클라이언트 및 라이선스를 다운로드하고, 사용 현황을 보고, API에 대해 알아볼 수 있습니다.

    다음 스크린샷은 액세스 권한 생성 방법을 보여 줍니다. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    다음 스크린샷은 포털의 사용 현황 보고 인터페이스를 보여 줍니다. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Aspera를 사용하여 파일 업로드

1. Aspera 클라이언트 소프트웨어 다운로드 및 설치:
    
    * [브라우저 플러그 인](https://downloads.asperasoft.com/connect2/)
    * [리치 클라이언트](https://downloads.asperasoft.com/en/downloads/2)

2. 첫 번째 전송을 수행하세요. Aspera 전송 서비스로 전송할 때 Aspera 클라이언트를 사용하려면 다음을 완료해야 합니다. 

   1. Aspera 포털을 사용하여 선택키를 만듭니다.  
   2. Aspera 클라이언트(Aspera 포털에 있는 소프트웨어)를 다운로드하고, 설치하고, 라이선스를 부여합니다.  

      >[!NOTE]
      >Aspera 클라이언트 가이드에서 구성 정보를 읽습니다.
    
   3. [Azure Portal](https://portal.azure.com/)을 사용하여 Azure 미디어 계정과 연결된 저장소 계정의 일부 정보를 검색합니다. 구체적으로, 이름과 키, 그리고 콘텐츠를 배치할 저장소 BLOB 컨테이너 이름 등이 있습니다. 

       * 포털에서 저장소 정보를 가져오려면 저장소 계정을 찾고, 선택키를 클릭하고, 계정의 이름 및 키를 복사합니다.
       * 컨테이너 이름을 가져오려면 저장소 계정을 찾고 **BLOB**을 선택하고 콘텐츠를 업로드하려는 컨테이너의 이름을 선택합니다. 

      다음은 Aspera 클라이언트 **연결 관리자**의 스크린샷입니다. 여기서 'Azure' 저장소 유형 및 자격 증명과 BLOB 컨테이너를 지정해야 합니다.

      ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>리소스

다음 리소스는 이 문서에서 설명했습니다. 

* [브라우저 플러그 인 연결](https://downloads.asperasoft.com/connect2/)
* [가이드 연결](https://downloads.asperasoft.com/en/documentation/8)
* [Aspera 클라이언트](https://downloads.asperasoft.com/en/downloads/2)
* [클라이언트 가이드](https://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>다음 단계

이제 [Blob을 스토리지 계정에서 AMS 계정으로 복사](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account)할 수 있습니다.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

