---
title: 사용자 지정 CA 인증서 추가 - Azure API Management | Microsoft Docs
description: Azure API Management에서 사용자 지정 CA 인증서를 추가하는 방법을 알아봅니다. 지침을 참조하여 인증서를 삭제할 수도 있습니다.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102040"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Azure API Management에서 사용자 지정 CA 인증서를 추가하는 방법

Azure API Management를 통해 신뢰할 수 있는 루트 및 중간 인증서 저장소 내의 머신에 CA 인증서를 설치할 수 있습니다. 서비스에 사용자 지정 CA 인증서가 필요한 경우 이 기능을 사용해야 합니다.

이 문서에서는 Azure Portal에서 Azure API Management 서비스 인스턴스의 CA 인증서를 관리하는 방법을 보여줍니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>CA 인증서 업로드

![CA 인증서 추가](media/api-management-howto-ca-certificates/00.png)

새 CA 인증서를 업로드하려면 다음 단계를 수행합니다. 아직 API Management 서비스 인스턴스를 만들지 않은 경우 자습서 [API Management 서비스 인스턴스 만들기](get-started-create-service-instance.md)를 참조하세요.

1. Azure Portal에서 Azure API Management 서비스 인스턴스로 이동합니다.

2. 메뉴에서 **CA 인증서** 를 선택합니다.

3. **+추가** 단추를 클릭합니다.  

    ![CA 인증서를 추가하기 위한 + Add 단추가 표시되는 스크린샷입니다.](media/api-management-howto-ca-certificates/01.png)  

4. 인증서를 찾고 인증서 저장소를 결정합니다. 공개 키만 필요하므로 암호는 필요하지 않습니다.

    ![인증서를 찾아보는 방법을 보여 주는 스크린샷입니다.](media/api-management-howto-ca-certificates/02.png)  

5. **저장** 을 클릭합니다. 이 작업은 몇 분 정도 걸릴 수 있습니다.

    ![인증서를 저장하는 방법을 보여 주는 스크린샷입니다.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> `New-AzApiManagementSystemCertificate` Powershell 명령을 사용하여 CA 인증서를 업로드할 수 있습니다.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>클라이언트 인증서 삭제

인증서를 삭제하려면 바로 가기 메뉴 **...** 를 클릭하고 인증서 옆에 있는 **삭제** 를 선택합니다.

![CA 인증서 삭제](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
