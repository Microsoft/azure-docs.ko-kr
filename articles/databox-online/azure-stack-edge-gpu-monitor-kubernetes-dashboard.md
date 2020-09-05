---
title: Kubernetes 대시보드를 통해 Azure Stack Edge 장치 모니터링 | Microsoft Docs
description: Kubernetes 대시보드를 액세스 및 사용 하 여 Azure Stack Edge 장치를 모니터링 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/29/2020
ms.author: alkohli
ms.openlocfilehash: 7274cef73bff3fb87d55ad636ff0167c8a064796
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180680"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Kubernetes 대시보드를 사용 하 여 Azure Stack Edge GPU 장치 모니터링

이 문서에서는 Kubernetes 대시보드를 액세스 및 사용 하 여 Azure Stack Edge GPU 장치를 모니터링 하는 방법을 설명 합니다. 장치를 모니터링 하려면 Azure Portal에서 차트를 사용 하거나, Kubernetes 대시보드를 보거나, `kubectl` 장치의 PowerShell 인터페이스를 통해 명령을 실행할 수 있습니다. 

이 문서에서는 Kubernetes 대시보드에서 수행할 수 있는 모니터링 작업만 중점적으로 다룹니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * 장치에서 Kubernetes 대시보드 액세스
> * `aseuser`구성 다운로드
> * 장치에 배포 된 모듈 보기
> * 장치에 배포 된 응용 프로그램의 IP 주소를 가져옵니다.
> * 장치에 배포 된 모듈의 컨테이너 로그 보기


## <a name="about-kubernetes-dashboard"></a>Kubernetes 대시보드 정보

Kubernetes 대시보드는 컨테이너 화 된 응용 프로그램 문제를 해결 하는 데 사용할 수 있는 웹 기반 사용자 인터페이스입니다. Kubernetes 대시보드는 Kubernetes 명령줄에 대 한 UI 기반 대안입니다 `kubectl` . 자세한 내용은 [Kubernetes Dashboard](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)를 참조 하세요. 

Azure Stack Edge 장치에서 *읽기 전용* 모드로 Kubernetes 대시보드를 사용 하 여 Azure Stack Edge 장치에서 실행 되는 응용 프로그램의 개요를 가져오고, Kubernetes 클러스터 리소스의 상태를 보고, 장치에서 발생 한 모든 오류를 볼 수 있습니다.

## <a name="access-dashboard"></a>액세스 대시보드

Kubernetes 대시보드는 *읽기 전용* 이며 포트 31000의 Kubernetes 마스터 노드에서 실행 됩니다. 대시보드에 액세스 하려면 다음 단계를 수행 합니다. 

1. 장치의 로컬 UI에서 **장치** 로 이동한 후 **장치 끝점**으로 이동 합니다. Kubernetes 대시보드 URL을 선택 하 여 브라우저에서 대시보드를 엽니다.

    ![로컬 UI의 장치 페이지에서 Kubernetes 대시보드 URL](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. **Kubernetes 대시보드 로그인** 페이지에서 **토큰**을 선택 합니다. 
1. 토큰을 제공 합니다. 
    1. 토큰을 가져오려면 [장치의 PowerShell 인터페이스를 통해 연결](azure-stack-edge-gpu-connect-powershell-interface.md)합니다.
    1. 다음 명령을 실행 합니다.  `Get-HcsKubernetesDashboardToken`
    
    1. 프롬프트에서 제공 된 토큰 문자열을 복사 합니다. 샘플 출력은 다음과 같습니다.
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. **로그인**을 선택합니다.

    ![Kubernetes 대시보드에 로그인](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. 이제 읽기 전용 모드에서 Azure Stack Edge 장치에 대 한 Kubernetes 대시보드를 볼 수 있습니다.

    ![Kubernetes 대시보드 기본 페이지](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>모듈 상태 보기

계산 모듈은 비즈니스 논리를 구현 하는 컨테이너입니다. 대시보드를 사용 하 여 Azure Stack Edge 장치에서 계산 모듈이 성공적으로 배포 되었는지 확인할 수 있습니다.

모듈 상태를 보려면 대시보드에서 다음 단계를 수행 합니다.

1. 대시보드의 왼쪽 창에서 **네임 스페이스**로 이동 합니다. IoT Edge 모듈이 표시 되는 네임 스페이스 (이 경우에는 **iotedge**)를 기준으로 필터링 합니다.
1. 왼쪽 창에서 **워크 로드 > 배포**로 이동 합니다.
1. 오른쪽 창에 장치에 배포 된 모든 모듈이 표시 됩니다. 이 경우 GettingStartedWithGPU 모듈은 Azure Stack Edge에 배포 되었습니다. 모듈이 배포 된 것을 볼 수 있습니다.

    ![모듈 배포 보기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>서비스 또는 모듈에 대 한 IP 주소 가져오기

대시보드를 사용 하 여 Kubernetes 클러스터 외부에 노출 하려는 서비스 또는 모듈의 IP 주소를 가져올 수 있습니다. 

**Compute 네트워크 설정** 페이지에서 장치의 로컬 웹 UI를 통해 이러한 외부 서비스에 대 한 IP 범위를 할당 합니다. IoT Edge 모듈을 배포한 후에는 특정 모듈 또는 서비스에 할당 된 IP 주소를 가져올 수 있습니다. 

IP 주소를 가져오려면 대시보드에서 다음 단계를 수행 합니다.

1. 대시보드의 왼쪽 창에서 **네임 스페이스**로 이동 합니다. 외부 서비스가 배포 되는 네임 스페이스 (이 경우에는 **iotedge**)를 기준으로 필터링 합니다.
1. 왼쪽 창에서 **검색 및 부하 분산 > 서비스로**이동 합니다.
1. 오른쪽 창에 `iotedge` Azure Stack Edge 장치의 네임 스페이스에서 실행 중인 모든 서비스가 표시 됩니다.

    ![외부 서비스에 대 한 IP 가져오기](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>컨테이너 로그 보기

컨테이너 로그를 확인 해야 하는 인스턴스가 있습니다. 대시보드를 사용 하 여 Kubernetes 클러스터에 배포한 특정 컨테이너의 로그를 가져올 수 있습니다.

컨테이너 로그를 보려면 대시보드에서 다음 단계를 수행 합니다.

1. 대시보드의 왼쪽 창에서 **네임 스페이스**로 이동 합니다. IoT Edge 모듈이 배포 되는 네임 스페이스 (이 경우에는 **iotedge**)를 기준으로 필터링 합니다.
1. 왼쪽 창에서 **워크 로드 > pod**로 이동 합니다.
1. 오른쪽 창에는 장치에서 실행 되는 모든 pod가 표시 됩니다. 로그를 보려는 모듈을 실행 하 고 있는 pod를 확인 합니다. 식별 한 pod에 대해 세로 줄임표를 선택 하 고 상황에 맞는 메뉴에서 **로그**를 선택 합니다.

    ![컨테이너 로그 보기 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. 로그는 대시보드에 기본 제공 되는 로그 뷰어에 표시 됩니다. 로그를 다운로드할 수도 있습니다.

    ![컨테이너 로그 보기 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="next-steps"></a>다음 단계

Kubernetes 문제를 해결 하는 방법 알아보기 <!--insert link-->.
