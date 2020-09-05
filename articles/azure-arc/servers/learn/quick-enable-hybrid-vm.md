---
title: Azure Arc 사용 서버(미리 보기)를 사용하여 하이브리드 머신 연결
description: Azure Arc 사용 서버(미리 보기)를 사용하여 하이브리드 머신을 연결하고 등록하는 방법을 알아봅니다.
ms.topic: quickstart
ms.date: 08/12/2020
ms.openlocfilehash: eacf75871b1f7cc7fc3b703d8859338578e43456
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213617"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers-preview"></a>빠른 시작: Azure Arc 사용 서버(미리 보기)를 사용하여 하이브리드 머신 연결

[Azure Arc 사용 서버](../overview.md)(미리 보기)를 사용하면 온-프레미스, 에지 및 다중 클라우드 환경에서 호스트되는 Windows 및 Linux 머신을 관리하고 제어할 수 있습니다. 이 빠른 시작에서는 Arc 사용 서버(미리 보기)를 사용하여 관리하기 위해 Azure 외부에 호스트된 Windows 또는 Linux 머신에서 Connected Machine 에이전트를 배포하고 구성합니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* Arc 사용 서버(미리 보기) Hybrid Connected Machine 에이전트를 배포하려면 머신에 에이전트를 설치하고 구성하기 위한 관리자 권한이 있어야 합니다. Linux에서는 루트 계정을 사용하고 Windows에서는 로컬 관리자 그룹의 멤버인 계정을 사용합니다.

* 시작하기 전에 에이전트 [필수 구성 요소](../agent-overview.md#prerequisites)를 검토하고 다음을 확인해야 합니다.

    * 대상 머신에서 지원되는 [운영 체제](../agent-overview.md#supported-operating-systems)가 실행되고 있어야 합니다.

    * 계정에는 [필요한 Azure 역할](../agent-overview.md#required-permissions)에 대한 할당이 부여됩니다.

    * 머신이 인터넷을 통해 통신하기 위해 방화벽 또는 프록시 서버를 통해 연결되는 경우 [나열된](../agent-overview.md#networking-configuration) URL이 차단되지 않는지 확인합니다.

    * Azure Arc 사용 서버(미리 보기)는 [여기](../overview.md#supported-regions)에서 지정된 영역만 지원합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Azure 리소스 공급자 등록

Azure Arc 사용 서버(미리 보기)는 이 서비스를 사용하기 위해 구독의 다음 Azure 리소스 공급자를 사용합니다.

* Microsoft.HybridCompute
* Microsoft.GuestConfiguration

다음 명령을 사용하여 등록합니다.

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>설치 스크립트 생성

다운로드, 설치를 자동화하고 Azure Arc와의 연결을 설정하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료하려면 다음을 수행합니다.

1. **모든 서비스**를 클릭한 다음, **머신 - Azure Arc**를 검색하고 선택하여 Azure Portal에서 Azure Arc 서비스를 시작합니다.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="모든 서비스에서 Arc 사용 서버 검색" border="false":::

1. **머신 - Azure Arc** 페이지의 왼쪽 위에서 **추가**를 선택하거나 가운데 창의 아래쪽에서 **머신 만들기 - Azure Arc** 옵션을 선택합니다.

1. **메서드 선택** 페이지에서 **대화형 스크립트를 사용하여 머신 추가** 타일, **스크립트 생성**을 차례로 선택합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다.

1. **스크립트 생성** 페이지의 **운영 체제** 드롭다운 목록에서 스크립트를 실행할 운영 체제를 선택합니다.

1. 머신에서 프록시 서버를 통해 통신하여 인터넷에 연결하는 경우 **다음: 프록시 서버**를 선택합니다.

1. **프록시 서버** 탭에서 머신에서 프록시 서버와 통신하는 데 사용할 프록시 서버 IP 주소 또는 이름 및 포트 번호를 지정합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다.

1. **검토 + 생성**을 선택합니다.

1. **검토 + 생성** 탭에서 요약 정보를 검토한 다음, **다운로드**을 선택합니다. 그래도 변경해야 하는 경우 **이전**을 선택합니다.

## <a name="install-the-agent-using-the-script"></a>스크립트를 사용하여 에이전트 설치

### <a name="windows-agent"></a>Windows 에이전트

1. 서버에 로그인합니다.

1. 관리자 권한 64비트 PowerShell 명령 프롬프트를 엽니다.

1. 스크립트를 복사한 폴더 또는 공유로 변경하고, `./OnboardingScript.ps1` 스크립트를 실행하여 서버에서 실행합니다.

### <a name="linux-agent"></a>Linux 에이전트

1. Azure와 직접 통신할 수 있는 대상 머신에 Linux 에이전트를 설치하려면 다음 명령을 실행합니다.

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * 대상 머신이 프록시 서버를 통해 통신하는 경우 다음 명령을 실행합니다.

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 사용 서버(미리 보기)에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="머신 연결 성공" border="false":::

## <a name="next-steps"></a>다음 단계

Linux 또는 Windows 하이브리드 머신을 사용하도록 설정하고 서비스에 성공적으로 연결되었으므로 Azure의 규정 준수를 이해하도록 Azure Policy를 사용할 수 있습니다.

Log Analytics 에이전트가 설치되어 있지 않은 Azure Arc 사용 서버(미리 보기) 사용 머신을 확인하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [비준수 리소스를 식별하는 정책 할당 만들기](tutorial-assign-policy-portal.md)
