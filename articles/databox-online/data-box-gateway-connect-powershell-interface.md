---
title: Windows PowerShell 인터페이스를 통해 Microsoft Azure Data Box Gateway 장치에 연결 및 관리 | Microsoft Docs
description: Windows PowerShell 인터페이스를 통해 Data Box Gateway에 연결 하 고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 650777d849e172686e8b46502a84db8c519174e7
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775192"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure Data Box Gateway 장치 관리

Azure Data Box Gateway 솔루션을 사용 하면 네트워크를 통해 Azure에 데이터를 보낼 수 있습니다. 이 문서에서는 Data Box Gateway 장치에 대한 구성 및 관리 작업을 설명 합니다. Azure Portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용 하 여 장치를 관리할 수 있습니다.

이 문서에서는 PowerShell 인터페이스를 사용 하 여 수행 하는 작업을 중점적으로 설명 합니다.

이 문서에는 다음과 같은 절차가 포함 되어 있습니다.

- PowerShell 인터페이스에 연결
- 지원 패키지 만들기
- 인증서 업로드
- DHCP가 아닌 환경에서 부팅
- 장치 정보 보기

## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>DHCP가 아닌 환경에서 부팅

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>장치 정보 보기

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)를 배포합니다.
