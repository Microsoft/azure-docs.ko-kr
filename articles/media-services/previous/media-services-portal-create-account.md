---
title: Azure Portal을 사용하여 Azure Media Services 계정 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Media Services 계정을 만드는 단계를 안내합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: ddc1c7f2dd207cba18a8c080c8b14cc53c149a39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61463449"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Azure Portal을 사용하여 Media Services 계정 만들기

> [!NOTE]
> Media Services v2에는 새로운 특징 또는 기능이 추가되지 않습니다. <br/>[Media Services v3](https://docs.microsoft.com/azure/media-services/latest/)의 최신 버전을 확인하세요. 참고: [v2에서 v3 마이그레이션 지침](../latest/migrate-from-v2-to-v3.md)

Azure Portal을 통해 AMS(Azure Media Services) 계정을 신속하게 만들 수 있습니다. 계정을 사용하여 Azure에서 미디어 콘텐츠를 저장, 암호화, 인코딩, 관리 및 스트리밍할 수 있는 Media Services에 액세스할 수 있습니다. Media Services 계정을 만들 때 연결된 스토리지 계정도 만듭니다(또는 기존 스토리지 계정 사용). Media Services 계정을 삭제하는 경우 관련 저장소 계정의 Blob은 삭제되지 않습니다.

범용 V1 또는 범용 V2를 기본 저장소 계정으로 사용할 수 있습니다. 현재 Azure Portal을 사용하면 v1을 선택할 수 있습니다. 하지만 API 또는 PowerShell을 사용하여 계정을 만들 때 v2를 추가할 수 있습니다. 저장소 유형에 대한 자세한 내용은 [Azure 저장소 계정 정보](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)를 참조하세요.

Media Services 계정 및 연결된 모든 스토리지 계정은 동일한 Azure 구독에 포함되어야 합니다. 추가 대기 시간 및 데이터 송신 비용이 발생하지 않도록 Media Services 계정과 동일한 위치에 스토리지 계정을 사용하는 것이 좋습니다.

이 문서에서는 Azure Portal을 사용하여 Media Services 계정을 만드는 방법을 보여줍니다.

> [!NOTE]
> 다른 지역에서 Azure Media Services 기능의 사용 가용성에 대한 정보는 [데이터 센터에서 AMS 기능의 사용 가용성](scenarios-and-availability.md#availability)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 Azure 계정이 필요합니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요. 

## <a name="create-an-ams-account"></a>AMS 계정 만들기

이 섹션의 단계에서는 AMS 계정을 만드는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **+새로 만들기** > **웹 + 모바일** > **Media Services**를 클릭합니다.
   
    ![Media Services 만들기](./media/media-services-create-account/media-services-new1.png)
3. **미디어 서비스 계정 만들기** 에 필요한 값을 입력합니다.
   
    ![Media Services 만들기](./media/media-services-create-account/media-services-new3.png)
   
   1. **계정 이름**에 새 AMS 계정의 이름을 입력합니다. Media Services 계정 이름은 공백 없이 모두 소문자로 이루어진 3-24자의 숫자 또는 문자입니다.
   2. 구독에서 액세스할 수 있는 다양한 Azure 구독 중에서 선택합니다.
   3. **리소스 그룹**에서 새 또는 기존 리소스를 선택합니다.  리소스 그룹은 수명 주기, 권한 및 정책을 공유하는 리소스의 컬렉션입니다. [여기](../../azure-resource-manager/resource-group-overview.md#resource-groups)를 참조하세요.
   4. **위치**에서 Media Services 계정에 대한 메타데이터 레코드 및 미디어를 저장하는 데 사용할 지리적 지역을 선택합니다. 이 지역은 미디어를 처리하고 스트림하는 데 사용됩니다. 사용 가능한 Media Services 지역만 드롭다운 목록 상자에 표시됩니다. 
   5. **Storage 계정**에서 Media Services 계정의 미디어 콘텐츠가 포함된 Blob 스토리지를 제공할 Storage 계정을 선택합니다. Media Services 계정과 동일한 지역의 기존 저장소 계정을 선택하거나 저장소 계정을 만들 수 있습니다. 동일한 지역에 새 저장소 계정이 생성됩니다. 저장소 계정 이름에 대한 규칙은 Media Services 계정의 경우와 같습니다.
      
       저장소에 대한 자세한 내용은 [여기](../../storage/common/storage-introduction.md)를 참조하세요.
   6. 계정 배포 진행 상태를 보려면 **대시보드에 고정** 을 선택합니다.
4. 양식 맨 아래에 있는 **만들기** 를 클릭합니다.
   
    계정이 성공적으로 만들어지면 개요 페이지가 로드됩니다. 스트리밍 엔드포인트 테이블에서 계정은 **중지됨** 상태에서 기본 스트리밍 엔드포인트를 가집니다. 

    >[!NOTE]
    >AMS 계정이 만들어질 때 **기본** 스트리밍 엔드포인트는 **중지됨** 상태에서 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 동적 패키징 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트는 **실행** 상태에 있어야 합니다. 
   
## <a name="to-manage-your-ams-account"></a>AMS 계정을 관리하려면

AMS 계정을 관리하려면(예: 프로그래밍 방식으로 AMS API에 연결, 비디오 업로드, 자산 인코딩, 콘텐츠 보호 구성, 작업 진행률 모니터링) 포털의 왼쪽에서 **설정**을 선택합니다. **설정**에서 사용할 수 있는 블레이드 중 하나로 이동합니다(예: **API 액세스**, **자산**, **작업**, **콘텐츠 보호**).


## <a name="next-steps"></a>다음 단계

이제 AMS 계정에 파일을 업로드할 수 있습니다. 자세한 내용은 [파일 업로드](media-services-portal-upload-files.md)를 참조하세요.

프로그래밍 방식으로 AMS API에 액세스하려는 경우 [Azure AD 인증을 사용하여 Azure Media Services API에 액세스](media-services-use-aad-auth-to-access-ams-api.md)를 참조하세요.

## <a name="media-services-learning-paths"></a>Media Services 학습 경로
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

