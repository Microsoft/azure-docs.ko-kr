---
title: Azure Red Hat OpenShift 개발 환경 설정
description: Microsoft Azure Red Hat OpenShift를 사용 하기 위한 필수 구성 요소는 다음과 같습니다.
keywords: red hat openshift 설치 설정
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: 559c932c4826e82f36c09b85ee8da4186d90d34d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276081"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Azure Red Hat OpenShift 개발 환경 설정

Red Hat OpenShift 응용 프로그램 Microsoft Azure를 빌드하고 실행 하려면 다음을 수행 해야 합니다.

* Azure CLI 버전 (또는 그 이상)을 설치 하거나 Azure Cloud Shell를 사용 2.0.65.
* `AROGA` 기능 및 연결 된 리소스 공급자에 등록 합니다.
* Azure Active Directory (Azure AD) 테넌트를 만듭니다.
* Azure AD 응용 프로그램 개체를 만듭니다.
* Azure AD 사용자를 만듭니다.

다음 지침에서는 이러한 모든 필수 구성 요소를 안내 합니다.

## <a name="install-the-azure-cli"></a>Azure CLI 설치

Azure Red Hat OpenShift에는 버전 2.0.65 이상의 Azure CLI 필요 합니다. Azure CLI를 이미 설치한 경우 다음을 실행 하 여 보유 한 버전을 확인할 수 있습니다.

```bash
az --version
```

출력의 첫 번째 줄에는 `azure-cli (2.0.65)`와 같은 CLI 버전이 있습니다.

새 설치 또는 업그레이드가 필요한 경우 [Azure CLI를 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 하는 지침은 다음과 같습니다.

또는 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)를 사용할 수 있습니다. Azure Cloud Shell 사용 하는 경우 [Azure Red Hat OpenShift 클러스터 만들기 및 관리](tutorial-create-cluster.md) 자습서 시리즈를 따라 진행 하려는 경우 **Bash** 환경을 선택 해야 합니다.

## <a name="register-providers-and-features"></a>공급자 및 기능 등록

첫 번째 Azure Red Hat OpenShift 클러스터를 배포 하기 전에 `Microsoft.ContainerService AROGA` 기능, `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` 및 `Microsoft.Network` 공급자를 구독에 수동으로 등록 해야 합니다.

이러한 공급자 및 기능을 수동으로 등록 하려면 CLI를 설치한 경우 Bash 셸에서 또는 Azure Portal의 Azure Cloud Shell (Bash) 세션에서 다음 지침을 사용 합니다.

1. 여러 Azure 구독이 있는 경우 관련 구독 ID를 지정 합니다.

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. ContainerService AROGA 기능을 등록 합니다.

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Microsoft 저장소 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Microsoft. Compute 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Microsoft 솔루션 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Microsoft 네트워크 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Microsoft. KeyVault 공급자를 등록 합니다.

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. ContainerService 리소스 공급자의 등록을 새로 고칩니다.

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Azure Active Directory (Azure AD) 테넌트 만들기

Azure Red Hat OpenShift 서비스를 사용 하려면 조직 및 Microsoft와의 관계를 나타내는 연결 된 Azure Active Directory (Azure AD) 테넌트가 필요 합니다. Azure AD 테넌트를 사용 하 여 앱을 등록, 빌드 및 관리 하 고 다른 Azure 서비스를 사용할 수 있습니다.

Azure Red Hat OpenShift 클러스터에 대 한 테넌트로 사용할 Azure AD가 없거나 테스트에 대 한 테넌트를 만들려는 경우이 가이드를 계속 하기 전에 [Azure Red Hat openshift 클러스터에 대 한 AZURE ad 테넌트 만들기](howto-create-tenant.md) 의 지침을 따르세요.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Azure AD 사용자, 보안 그룹 및 응용 프로그램 개체 만들기

Azure Red Hat OpenShift를 사용 하려면 저장소 구성과 같은 클러스터에서 작업을 수행할 수 있는 권한이 있어야 합니다. 이러한 권한은 [서비스 주체](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)를 통해 표현 됩니다. 또한 Azure Red Hat OpenShift 클러스터에서 실행 중인 앱을 테스트 하기 위해 새 Active Directory 사용자를 만들려고 합니다.

[AZURE ad 앱 개체 및 사용자 만들기](howto-aad-app-configuration.md) 의 지침에 따라 서비스 주체를 만들고, 앱에 대 한 클라이언트 암호 및 인증 콜백 URL을 생성 하 고, 새 Azure AD 보안 그룹 및 사용자를 만들어 클러스터에 액세스 합니다.

## <a name="next-steps"></a>다음 단계

이제 Azure Red Hat OpenShift!를 사용할 준비가 되었습니다.

자습서를 사용해 보세요.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 만들기](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
