---
title: 포털을 사용 하 여 역할에 대한 원격 데스크톱을 사용 하도록 설정
titleSuffix: Azure Cloud Services
description: 원격 데스크톱 연결을 허용하기 위해 Azure 클라우드 서비스 애플리케이션을 구성하는 방법
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75360602"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Azure Cloud Services의 역할에 대해 원격 데스크톱 연결 사용

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

원격 데스크톱을 사용하면 Azure에서 실행 중인 역할의 데스크톱에 액세스할 수 있습니다. 원격 데스크톱 연결을 사용하여 애플리케이션 실행 중에 애플리케이션 문제를 진단하고 해결할 수 있습니다.

서비스 정의에 원격 데스크톱 모듈을 포함하여 개발 중에 원격 데스크톱 연결을 사용하도록 설정하거나 원격 데스크톱 확장을 통해 원격 데스크톱을 사용 하도록 선택할 수 있습니다. 권장되는 방법은 원격 데스크톱 확장을 사용하는 것입니다. 이 방법을 사용하면 애플리케이션이 배포된 후에도 애플리케이션을 다시 배포할 필요 없이 원격 데스크톱을 사용하도록 설정할 수 있습니다.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Azure Portal에서 원격 데스크톱 구성

Azure Portal에서는 애플리케이션이 배포된 후에도 원격 데스크톱을 사용 가능하게 설정할 수 있도록 원격 데스크톱 확장 접근 방법을 사용합니다. 클라우드 서비스의 **원격 데스크톱** 설정에서 원격 데스크톱을 사용하도록 설정하거나 가상 머신에 연결하는 데 사용되는 로컬 관리자 계정, 인증에 사용되는 인증서를 변경하고 만료 날짜를 설정할 수 있습니다.

1. **Cloud Services**, 클라우드 서비스의 이름, **원격 데스크톱**을 차례로 선택합니다.

    ![클라우드 서비스 원격 데스크톱](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. 개별 역할 또는 모든 역할 중 어떤 범주에 대해 원격 데스크톱을 사용하도록 설정할지 선택한 후 전환기의 값을 **Enabled**로 변경합니다.

3. 사용자 이름, 암호, 만료 및 인증서에 대한 필수 필드를 입력합니다.

    ![클라우드 서비스 원격 데스크톱](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > 처음으로 원격 데스크톱을 사용하도록 설정한 후 **확인**(확인 표시)을 선택하면 모든 역할 인스턴스가 다시 시작됩니다. 다시 부팅되지 않도록 하려면 암호를 암호화하는 데 사용되는 인증서가 역할에 설치되어 있어야 합니다. 다시 시작되지 않도록 하려면 [클라우드 서비스에 대한 인증서를 업로드](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) 하고 이 대화 상자로 돌아옵니다.

4. **역할**에서 업데이트할 역할을 선택하거나 모든 역할을 선택하려면 **모두**를 선택합니다.

5. 구성 업데이트를 마치면 **저장**을 선택합니다. 역할 인스턴스가 연결을 수신할 준비가 되기까지 몇 분 정도 걸립니다.

## <a name="remote-into-role-instances"></a>원격으로 역할 인스턴스 액세스

역할에 대해 원격 데스크톱이 사용 가능하게 설정되면 Azure Portal에서 직접 연결을 시작할 수 있습니다.

1. **인스턴스**를 클릭하여 **인스턴스** 설정을 엽니다.
2. 원격 데스크톱이 구성된 역할 인스턴스를 선택합니다.
3. **연결**을 클릭하여 역할 인스턴스에 대한 RDP 파일을 다운로드합니다.

    ![클라우드 서비스 원격 데스크톱](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. **열기**를 클릭한 후 **연결**을 클릭하여 원격 데스크톱 연결을 시작합니다.

>[!NOTE]
> 클라우드 서비스가 NSG 뒤에 있는 경우 포트 **3389** 및 **20000**의 트래픽을 허용하는 규칙을 만들어야 할 수 있습니다.  원격 데스크톱은 포트 **3389**를 사용합니다.  클라우드 서비스 인스턴스에서 부하가 분산되므로 연결할 인스턴스를 직접 제어할 수 없습니다.  *RemoteForwarder* 및 *RemoteAccess* 에이전트가 RDP 트래픽을 관리하고 클라이언트에서 RDP 쿠키를 전송하고 연결할 개별 인스턴스를 지정할 수 있도록 합니다.  *RemoteForwarder* 및 *RemoteAccess* 에이전트를 사용하려면 포트 **20000***이 열려 있어야 합니다. 이 포트는 NSG가 있으면 차단될 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

[Cloud Services를 구성하는 방법](cloud-services-how-to-configure-portal.md)
