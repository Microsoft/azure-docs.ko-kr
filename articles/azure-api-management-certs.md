---
title: Azure Service Management 인증서 업로드 | Microsoft Docs
description: Azure Portal에 대한 Service Management 인증서를 업로드하는 방법을 알아봅니다.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: jeconnoc
ms.openlocfilehash: 014a26c2500959502eeb1c50d3f311584c1ad84e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742971"
---
# <a name="upload-an-azure-service-management-certificate"></a>Azure Service Management 인증서 업로드
관리 인증서를 사용하면 Azure에서 제공하는 클래식 배포 모델로 인증할 수 있습니다. Visual Studio 또는 Azure SDK와 같은 많은 프로그램 및 도구에서 이러한 인증서를 사용하여 다양한 Azure 서비스의 구성 및 배포를 자동화합니다. 

> [!WARNING]
> 주의가 필요합니다! 이러한 형식의 인증서를 사용하면 해당 인증서로 인증된 사람은 누구나 연결된 구독을 관리할 수 있습니다.
>
>

Azure 인증서(자체 서명 인증서 포함)에 대한 자세한 내용은 [Azure Cloud Services에 대한 인증서 개요](cloud-services/cloud-services-certs-create.md#what-are-management-certificates)를 참조하세요.

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/)를 사용하여 자동화를 위한 클라이언트 코드를 인증할 수도 있습니다.

**참고:** 관리 인증서에서 모든 작업을 수행 하는 구독에서 공동 관리자 여야 합니다. 새 Azure Portal에서 공동 관리자를 추가하거나 제거하는 방법에 대해 [자세히 알아보세요](https://go.microsoft.com/fwlink/?linkid=849300). 

## <a name="upload-a-management-certificate"></a>관리 인증서 업로드
관리 인증서(공개 키만 포함하는 .cer 파일)를 만든 후에는 포털에 업로드할 수 있습니다. 포털에서 인증서를 사용할 수 있는 경우 일치하는 인증서(개인 키)가 있는 모든 사람이 관리 API를 통해 연결할 수 있고 연결된 구독에 대한 리소스에 액세스할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure 서비스 목록 하단에서 **모든 서비스**를 클릭하고 **일반** 서비스 그룹에서 _구독_을 선택합니다.

    ![구독 메뉴](./media/azure-api-management-certs/subscriptions_menu.png)

3. 인증서와 연결하려는 정확한 구독을 선택해야 합니다.     
4. 정확한 구독을 선택한 뒤 **설정** 그룹에서 _관리 인증서_를 누릅니다.

    ![설정](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. **업로드** 단추를 누릅니다.

    ![인증서 페이지에 업로드](./media/azure-api-management-certs/certificates_page.png)
6. 대화 상자 정보를 입력하고 **업로드**를 누릅니다.

    ![설정](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>다음 단계
이제 구독과 연관된 관리 인증서가 있으므로(로컬에서 일치하는 인증서를 설치한 후에) [클래식 배포 모델 REST API](/azure/#pivot=sdkstools)에 프로그래밍 방식으로 연결할 수 있고 구독과 연관된 다양한 Azure 리소스를 자동화할 수 있습니다.
