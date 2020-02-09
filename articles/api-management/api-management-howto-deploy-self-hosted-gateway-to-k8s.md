---
title: Kubernetes에 자체 호스팅 Azure API Management 게이트웨이 배포 | Microsoft Docs
description: Kubernetes에 자체 호스트 된 Azure API Management 게이트웨이를 배포 하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 109316acb73d3c5f00186298c1f8840c516e5790
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513836"
---
# <a name="deploy-a-self-hosted-azure-api-management-gateway-to-kubernetes"></a>Kubernetes에 자체 호스팅 Azure API Management 게이트웨이 배포

이 문서에서는 자체 호스팅 Azure API Management 게이트웨이를 Kubernetes 클러스터에 배포 하는 단계를 제공 합니다.

> [!NOTE]
> 자체 호스팅 게이트웨이 기능은 미리 보기 상태입니다. 미리 보기 중에 자체 호스팅 게이트웨이는 개발자 및 프리미엄 계층 에서만 추가 비용 없이 사용할 수 있습니다. 개발자 계층은 단일 자체 호스팅 게이트웨이 배포로 제한 됩니다.


## <a name="prerequisites"></a>필수 조건

- 다음 빠른 시작 [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)를 완료합니다.
- Kubernetes 클러스터를 만듭니다. [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) 는 개발 및 평가를 위해 좋은 옵션입니다. 프로덕션 워크 로드의 경우 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) 또는 외부 클라우드에서 Kubernetes 클러스터를 사용할 수 있습니다.
- [API Management 인스턴스에서 게이트웨이 리소스를 프로 비전](api-management-howto-provision-self-hosted-gateway.md)합니다.

## <a name="deploy-the-gateway-to-kubernetes"></a>Kubernetes에 게이트웨이 배포

1. **설정**아래에서 **게이트웨이** 를 선택 합니다.
2. 배포 하려는 게이트웨이 리소스를 선택 합니다.
3. **배포**를 선택 합니다.
4. **토큰** 텍스트 상자의 새 토큰은 기본 **만료** 및 **비밀 키** 값을 사용 하 여 자동으로 자동 생성 되었습니다. 원하는 경우 둘 중 하나 또는 둘 다를 조정 하 고 **생성** 을 선택 하 여 새 토큰을 만듭니다.
5. **배포 스크립트**아래에서 **Kubernetes** 이 선택 되어 있는지 확인 합니다.
6. **배포** 옆의 **게이트웨이 이름 >. yml** 파일 링크를 선택 하 여 파일을 다운로드 < 합니다.
7. 필요에 따라 iisnode.yml 파일의 포트 매핑 및 컨테이너 이름을 조정 합니다.
8. **배포** 텍스트 상자의 오른쪽 끝에 있는 **복사** 아이콘을 선택 하 여 `kubectl` 명령을 클립보드에 저장 합니다. 
9. 명령을 터미널 (또는 명령) 창에 붙여넣습니다. 이 명령은 다운로드 한 환경 파일이 현재 디렉터리에 있는 것으로 예상 합니다.
```console
    kubectl apply -f <gateway-name>.yaml
```
10. 명령을 실행합니다. 명령은 Kubernetes 클러스터에 Microsoft Container Registry에서 다운로드 한 자체 호스팅 게이트웨이의 이미지를 사용 하 여 컨테이너를 실행 하도록 지시 하 고, HTTP (8080) 및 HTTPS (443) 포트를 노출 하도록 컨테이너를 구성 합니다.
11. 아래 명령을 실행 하 여 게이트웨이 pod가 실행 중인지 확인 합니다. Pod 이름이 달라 집니다. 
```console
kubectl get pods
NAME                              READY   STATUS    RESTARTS   AGE
local-gateway-55f774f844-bv9wt    1/1     Running   0          1m
```
12. 아래 명령을 실행 하 여 게이트웨이 서비스가 실행 중인지 확인 합니다. 서비스 이름은 다를 수 있습니다. 
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
localgateway     NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
13. Azure Portal로 돌아가서 방금 배포한 게이트웨이 노드가 정상 상태를 보고 하는지 확인 합니다.

![게이트웨이 상태](media/api-management-howto-deploy-self-hosted-gateway-to-k8s/status.png)

> [!TIP]
> <code>kubectl logs <gateway-pod-name></code> 명령을 사용 하 여 자체 호스팅 게이트웨이 로그의 스냅숏을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대해 자세히 알아보려면 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md) 를 참조 하세요.
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/intro-kubernetes) 에 대한 자세한 정보


