---
title: Azure Sdk를 사용 하 여 azure VM에서 관리 되는 id 사용-Azure AD
description: Azure 리소스에 대한 관리 ID가 있는 Azure VM으로 Azure SDK를 사용하기 위한 코드 샘플입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecfb2fa5f45a23d387741d4865aa9707df960e86
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018419"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-with-azure-sdks"></a>Azure SDK를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하는 방법 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  
이 문서에서는 Azure 리소스에 대한 관리 ID에 해당하는 Azure SDK 지원을 사용하는 방법을 보여주는 SDK 샘플 목록을 제공합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

> [!IMPORTANT]
> - 이 문서의 모든 샘플 코드/스크립트는 Azure 리소스에 대한 관리 ID를 사용하는 VM에서 클라이언트가 실행되고 있다고 가정합니다. Azure Portal에서 VM "연결" 기능을 사용하여 VM에 원격으로 연결합니다. VM에서 Azure 리소스에 대한 관리 ID 사용에 대한 자세한 내용은 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md) 또는 (PowerShell, CLI, 템플릿 또는 Azure SDK를 사용하는) 변형 문서 중 하나를 참조하세요. 

## <a name="sdk-code-samples"></a>SDK 코드 샘플

| SDK             | 코드 샘플 |
| --------------- | ----------- |
| .NET            | [Azure 리소스에 대한 관리 ID를 사용하여 Windows VM에서 Azure Resource Manager 템플릿 배포](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core       | [Azure 리소스에 대한 관리 ID를 사용하여 Linux VM에서 Azure 서비스 호출](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js         | [Azure 리소스에 대한 관리 ID를 사용하는 리소스 관리](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python          | [Azure 리소스에 대한 관리 ID를 사용하여 VM 내부에서 간단하게 인증](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby            | [Azure 리소스에 대한 관리 ID를 사용하는 VM에서 리소스 관리](https://github.com/Azure-Samples/resources-ruby-manage-resources-with-msi/) |

## <a name="next-steps"></a>다음 단계

- 라이브러리 다운로드, 문서 등을 비롯한 Azure SDK 리소스의 전체 목록은 [Azure SDK](https://azure.microsoft.com/downloads/)를 참조하세요.
- Azure VM에서 Azure 리소스에 대한 관리 ID를 사용하려면 [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](qs-configure-portal-windows-vm.md)을 참조하세요.








