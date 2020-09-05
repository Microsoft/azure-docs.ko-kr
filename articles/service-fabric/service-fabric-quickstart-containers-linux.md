---
title: Azure의 Service Fabric에서 Linux 컨테이너 앱 만들기
description: 이 빠른 시작에서 애플리케이션을 사용하여 Docker 이미지를 빌드하고, 이미지를 컨테이너 레지스트리로 푸시하고, Service Fabric 클러스터에 컨테이너를 배포합니다.
ms.topic: quickstart
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 652c5b7c2df4e1f8bd379390451886914628505d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854975"
---
# <a name="quickstart-deploy-linux-containers-to-service-fabric"></a>빠른 시작: Linux 컨테이너를 Service Fabric에 배포

Azure Service Fabric은 확장성 있고 안정성이 뛰어난 마이크로 서비스 및 컨테이너를 배포 및 관리하기 위한 분산 시스템 플랫폼입니다.

이 빠른 시작은 Azure에서 Service Fabric 클러스터에 Linux 컨테이너를 배포하는 방법을 보여 줍니다. 작업이 완료되면 Python 웹 프런트 엔드 및 Redis 백 엔드로 구성된 Voting 애플리케이션이 Service Fabric 클러스터에서 실행됩니다. 또한 애플리케이션을 장애 조치 (failover)하는 방법과 클러스터의 애플리케이션을 확장하는 방법도 알아봅니다.

![Voting 앱 웹 페이지][quickstartpic]

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

1. 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

2. [Azure CLI](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) 설치

3. [Service Fabric SDK 및 CLI](service-fabric-get-started-linux.md#installation-methods) 설치

4. [Git 설치](https://git-scm.com/)


## <a name="get-the-application-package"></a>애플리케이션 패키지 가져오기

Service Fabric에 컨테이너를 배포하려면 개별 컨테이너 및 애플리케이션에 대해 설명하는 매니페스트 파일(애플리케이션 정의)의 세트이 필요합니다.

콘솔에서 git을 사용하여 애플리케이션 정의를 복사한 다음, 디렉터리를 복제본의 `Voting` 디렉터리로 변경합니다.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/Voting
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric 클러스터 만들기

애플리케이션을 Azure에 배포하려면 애플리케이션을 실행하는 Service Fabric 클러스터가 필요합니다. 다음 명령은 Azure에서 5노드 클러스터를 만듭니다.  이 명령은 자체 서명된 인증서도 만들어, 키 자격 증명 모음에 추가하고, 해당 인증서를 로컬로 다운로드합니다. 새 인증서는 배포 시 클러스터를 보호하는 데 사용되며 클라이언트를 인증하는 데도 사용됩니다.

```azurecli
#!/bin/bash

# Variables
ResourceGroupName='containertestcluster' 
ClusterName='containertestcluster' 
Location='eastus' 
Password='q6D7nN%6ck@6' 
Subject='containertestcluster.eastus.cloudapp.azure.com' 
VaultName='containertestvault' 
VmPassword='Mypa$$word!321'
VmUserName='sfadminuser'

# Login to Azure and set the subscription
az login

az account set --subscription <mySubscriptionID>

# Create resource group
az group create --name $ResourceGroupName --location $Location 

# Create secure five node Linux cluster. Creates a key vault in a resource group
# and creates a certficate in the key vault. The certificate's subject name must match 
# the domain that you use to access the Service Fabric cluster.  The certificate is downloaded locally.
az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $ResourceGroupName --vm-password $VmPassword --vm-user-name $VmUserName
```

> [!Note]
> 웹 프런트 엔드 서비스는 들어오는 트래픽에 대해 80 포트에서 수신 대기하도록 구성됩니다. 기본적으로 포트 80이 클러스터 VM 및 Azure Load Balancer에서 열려 있습니다.
>

## <a name="configure-your-environment"></a>환경 구성

Service Fabric은 클러스터 및 해당 애플리케이션을 관리하는 데 사용할 수 있는 몇 가지 도구를 제공합니다.

- Service Fabric Explorer, 브라우저 기반 도구입니다.
- Service Fabric CLI(명령줄 인터페이스), Azure CLI를 기반으로 실행됩니다. 
- PowerShell 명령

이 빠른 시작에서는 Service Fabric CLI 및 Service Fabric Explorer(웹 기반 도구)를 사용합니다. Service Fabric Explorer를 사용하려면 인증서 PFX 파일을 브라우저로 가져와야 합니다. 기본적으로 PFX 파일에는 암호가 없습니다.

Mozilla Firefox가 Ubuntu 16.04의 기본 브라우저입니다. 인증서를 Firefox로 가져 오려면 브라우저의 오른쪽 위 모서리에 있는 메뉴 단추를 클릭한 다음 **옵션**을 클릭합니다. **기본 설정** 페이지에서 검색 상자를 사용하여 "인증서"를 검색합니다. **인증서 보기**를 클릭하고 **Your Certificates**(인증서) 탭을 선택하고 **가져오기**를 클릭하여 프롬프트에 따라 인증서를 가져옵니다.

   ![Firefox에서 인증서 설치](./media/service-fabric-quickstart-containers-linux/install-cert-firefox.png)

## <a name="deploy-the-service-fabric-application"></a>Service Fabric 애플리케이션 배포

1. CLI를 사용하여 Azure에서 Service Fabric 클러스터에 연결합니다. 엔드포인트는 클러스터의 관리 엔드포인트입니다. 이전 섹션에서 PEM 파일을 만들었습니다. 

    ```bash
    sfctl cluster select --endpoint https://containertestcluster.eastus.cloudapp.azure.com:19080 --pem containertestcluster22019013100.pem --no-verify
    ```

2. 설치 스크립트를 사용하여 클러스터에 Voting 애플리케이션 정의를 복사하고, 애플리케이션 유형을 등록하며, 애플리케이션의 인스턴스를 만듭니다.  PEM 인증서 파일은 *install.sh* 파일과 동일한 디렉터리에 있어야 합니다.

    ```bash
    ./install.sh
    ```

3. 웹 브라우저를 열고 클러스터의 Service Fabric Explorer 엔드포인트로 이동합니다. 엔드포인트의 형식은 **https://\<my-azure-service-fabric-cluster-url>:19080/Explorer**입니다(예: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`). </br>

4. **애플리케이션** 노드를 확장하여 만든 Voting 애플리케이션 유형 및 생성한 인스턴스에 대한 항목이 표시되는지 확인합니다.

    ![Service Fabric Explorer][sfx]

5. 실행 중인 컨테이너에 연결하려면 웹 브라우저를 열고 클러스터의 URL로 이동합니다. 예: `http://containertestcluster.eastus.cloudapp.azure.com:80`. 브라우저에서 선택 애플리케이션이 표시됩니다.

    ![Voting 앱 웹 페이지][quickstartpic]

> [!NOTE]
> Docker Compose를 사용하여 Service Fabric 애플리케이션을 배포할 수도 있습니다. 예를 들면, 다음 명령으로 Docker Compose를 사용하여 클러스터에 애플리케이션을 배포하고 설치할 수 있습니다.
>  ```bash
> sfctl compose create --deployment-name TestApp --file-path ../docker-compose.yml
> ```

## <a name="fail-over-a-container-in-a-cluster"></a>클러스터에서 컨테이너 장애 조치(failover)

Service Fabric은 장애가 발생할 경우 컨테이너 인스턴스가 클러스터의 다른 노드로 자동 이동하도록 합니다. 또한 수동으로 컨테이너의 노드를 드레이닝하고 클러스터의 다른 노드로 정상적으로 옮길 수 있습니다. Service Fabric은 서비스를 확장하는 여러 가지 방법을 제공합니다. 다음 단계에서는 Service Fabric Explorer를 사용합니다.

프런트 엔드 컨테이너를 장애 조치(failover)하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `https://containertestcluster.eastus.cloudapp.azure.com:19080/Explorer`
2. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다. 트리 뷰에서 노드 이름을 확인합니다. 컨테이너에서 현재 실행 중인 노드를 보여 줍니다. 예: `_nodetype_1`
3. 트리 뷰에서 **노드** 노드를 확장합니다. 컨테이너를 실행 중인 노드 옆에 있는 줄임표(...)를 클릭합니다.
4. **다시 시작**을 선택하여 해당 노드를 다시 시작하고 다시 시작 작업을 확인합니다. 다시 시작하면 컨테이너를 클러스터의 다른 노드로 장애 조치(failover)합니다.

    ![Service Fabric Explorer의 노드 보기][sfxquickstartshownodetype]

## <a name="scale-applications-and-services-in-a-cluster"></a>클러스터에서 애플리케이션 및 서비스 크기 조정

Service Fabric 서비스는 해당 서비스에 대한 부하를 수용하도록 클러스터 간에 쉽게 크기를 조정할 수 있습니다. 클러스터에서 실행되는 인스턴스 수를 변경하여 서비스 크기를 조정합니다.

웹 프런트 엔드 서비스의 크기를 조정하려면 다음 단계를 수행합니다.

1. 클러스터에서 Service Fabric Explorer를 엽니다. 예: `https://containertestcluster.eastus.cloudapp.azure.com:19080`
2. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드 옆에 있는 줄임표(...)를 클릭하고 **Scale Service**(서비스 크기 조정)를 선택합니다.

    ![Service Fabric Explorer 크기 조정 서비스 시작][containersquickstartscale]

    이제 웹 프런트 엔드 서비스의 인스턴스 수를 조정하도록 선택할 수 있습니다.

3. 이 수를 **2**로 변경하고 **Scale Service**를 클릭합니다.
4. 트리 뷰에서 **fabric:/Voting/azurevotefront** 노드를 클릭하고 파티션 노드(GUID로 표현됨)를 확장합니다.

    ![Service Fabric Explorer 크기 조정 서비스 완료][containersquickstartscaledone]

    이제 서비스에 두 인스턴스가 있는 것을 볼 수 있습니다. 트리 뷰에서 인스턴스가 실행하는 노드를 볼 수 있습니다.

이 간단한 관리 작업을 통해 프런트 엔드 서비스에서 사용 가능한 리소스를 두 배로 늘려서 사용자 부하를 처리했습니다. 서비스를 안정적으로 실행하기 위해 여러 인스턴스가 필요하지 않다는 것을 이해하는 것이 중요합니다. 서비스가 실패하면 Service Fabric은 클러스터에서 새 서비스 인스턴스가 실행되는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

템플릿에 제공된 제거 스크립트(uninstall.sh)를 사용하여 클러스터에서 애플리케이션 인스턴스를 삭제하고 애플리케이션 유형을 등록 해제합니다. 이 스크립트는 인스턴스를 정리하는 데 약간의 시간이 걸리므로 이 스크립트 직후에 설치 스크립트를 실행하면 안됩니다. Service Fabric Explorer를 사용하여 인스턴스가 제거되고 애플리케이션 유형이 등록 취소된 시기를 확인할 수 있습니다.

```bash
./uninstall.sh
```

클러스터 및 클러스터에서 사용하는 모든 리소스를 삭제하는 가장 간단한 방법은 리소스 그룹을 삭제하는 것입니다.

Azure에 로그인하고, 클러스터를 제거하려는 구독 ID를 선택합니다. Azure Portal에 로그인하여 구독 ID를 찾을 수 있습니다. [az group delete 명령](/cli/azure/group?view=azure-cli-latest)을 사용하여 리소스 그룹 및 모든 클러스터 리소스를 삭제합니다.

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="containertestcluster"
az group delete --name $ResourceGroupName
```

클러스터 작업이 완료되면 인증서를 인증서 저장소에서 제거할 수 있습니다. 예를 들면 다음과 같습니다.
- Windows에서: [인증서 MMC 스냅인](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)을 사용합니다. 스냅인을 추가할 때 반드시 **내 사용자 계정**을 선택합니다. `Certificates - Current User\Personal\Certificates`로 이동하여 인증서를 제거합니다.
- Mac에서: 키 집합 앱을 사용합니다.
- Ubuntu에서: 인증서를 보는 데 사용한 단계를 수행하여 인증서를 제거합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure에서 Service Fabric 클러스터에 Linux 컨테이너 애플리케이션을 배포하고, 애플리케이션에서 장애 조치(failover)를 수행하고, 클러스터에서 애플리케이션을 확장했습니다. Service Fabric에서 Linux 컨테이너 작업에 대해 자세히 알아보려면 Linux 컨테이너 앱에 대한 자습서를 계속 진행하십시오.

> [!div class="nextstepaction"]
> [Linux 컨테이너 앱 만들기](./service-fabric-tutorial-create-container-images.md)

[sfx]: ./media/service-fabric-quickstart-containers-linux/containersquickstartappinstance.png
[quickstartpic]: ./media/service-fabric-quickstart-containers-linux/votingapp.png
[sfxquickstartshownodetype]:  ./media/service-fabric-quickstart-containers-linux/containersquickstartrestart.png
[containersquickstartscale]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscale.png
[containersquickstartscaledone]: ./media/service-fabric-quickstart-containers-linux/containersquickstartscaledone.png
