---
title: Windows PowerShell 인터페이스를 통해 Microsoft Azure Stack Edge 장치에 연결 및 관리 | Microsoft Docs
description: Windows PowerShell 인터페이스를 통해에 연결 하 고 Azure Stack에 지를 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 85e95dc4138fd638c8db9f5c98a7064153c7ef17
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181649"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Windows PowerShell을 통해 Azure Stack Edge GPU 장치 관리

Azure Stack Edge 솔루션을 사용 하면 데이터를 처리 하 고 네트워크를 통해 Azure에 보낼 수 있습니다. 이 문서에서는 Azure Stack Edge 장치에 대 한 몇 가지 구성 및 관리 작업을 설명 합니다. Azure Portal, 로컬 웹 UI 또는 Windows PowerShell 인터페이스를 사용 하 여 장치를 관리할 수 있습니다.

이 문서에서는 장치의 PowerShell 인터페이스와이 인터페이스를 사용 하 여 수행할 수 있는 작업에 연결 하는 방법을 중점적으로 설명 합니다. 


## <a name="connect-to-the-powershell-interface"></a>PowerShell 인터페이스에 연결

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>지원 패키지 만들기

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>인증서 업로드

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

IoT Edge 인증서를 업로드 하 여 IoT Edge 장치와이 장치에 연결할 수 있는 다운스트림 장치 간에 보안 연결을 설정할 수도 있습니다. 설치 해야 하는 다음과 같은 세 가지 IoT Edge 인증서 (*pem* 형식)가 있습니다.

- 루트 CA 인증서 또는 소유자 CA
- 디바이스 CA 인증서
- 장치 키 인증서

다음 예에서는이 cmdlet을 사용 하 여 IoT Edge 인증서를 설치 하는 방법을 보여 줍니다.

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
이 cmdlet을 실행 하면 네트워크 공유에 대 한 암호를 제공 하 라는 메시지가 표시 됩니다.

인증서에 대 한 자세한 내용은 인증서 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) 또는 [게이트웨이에서 인증서 설치](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)를 참조 하세요.

## <a name="view-device-information"></a>장치 정보 보기
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>GPU 드라이버 정보 보기

장치에서 계산 역할이 구성 된 경우 PowerShell 인터페이스를 통해 GPU 드라이버 정보를 가져올 수도 있습니다. 

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 를 사용 `Get-HcsGpuNvidiaSmi` 하 여 장치에 대 한 GPU 드라이버 정보를 가져옵니다.

    다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    이 cmdlet의 샘플 출력에서 드라이버 정보를 적어 둡니다.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>MP (다중 프로세스 서비스) 사용

Nvidia Gpu의 MP (다중 프로세스 서비스)는 여러 작업에서 Gpu를 공유할 수 있는 메커니즘을 제공 합니다. 여기에서 각 작업에는 GPU 리소스의 일부 백분율이 할당 됩니다. Azure Stack Edge 장치에서 MP를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. 시작 하기 전에 다음을 확인 합니다. 

    1. Azure에서 Azure Stack Edge/Data Box Gateway 리소스를 사용 하 여 [Azure Stack edge 장치](azure-stack-edge-gpu-deploy-activate.md) 를 구성 하 고 활성화 했습니다.
    1. [Azure Portal에서이 장치에 대 한 계산을 구성](azure-stack-edge-deploy-configure-compute.md#configure-compute)했습니다.
    
1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
1. 다음 명령을 사용 하 여 장치에서 MP를 사용 하도록 설정 합니다.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>디바이스 재설정

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>계산 로그 가져오기

장치에서 계산 역할이 구성 된 경우 PowerShell 인터페이스를 통해 계산 로그를 가져올 수도 있습니다.

1. [PowerShell 인터페이스에 연결](#connect-to-the-powershell-interface)합니다.
2. 를 사용 `Get-AzureDataBoxEdgeComputeRoleLogs` 하 여 장치에 대 한 계산 로그를 가져옵니다.

    다음 예에서는이 cmdlet을 사용 하는 방법을 보여 줍니다.

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    다음은 cmdlet에 사용 되는 매개 변수에 대 한 설명입니다.
    - `Path`: 계산 로그 패키지를 만들려는 공유의 네트워크 경로를 제공 합니다.
    - `Credential`: 네트워크 공유에 대 한 사용자 이름을 제공 합니다. 이 cmdlet을 실행 하는 경우 공유 암호를 제공 해야 합니다.
    - `FullLogCollection`:이 매개 변수를 사용 하면 로그 패키지에 모든 계산 로그가 포함 됩니다. 기본적으로 로그 패키지에는 로그 하위 집합만 포함 되어 있습니다.



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>IoT Edge와 관련 된 Kubernetes 문제를 디버그 합니다.

Kubernetes 클러스터가 만들어지면 `aseuser` 시스템 네임 스페이스와 연결 된 기본 사용자 `iotedge` 도 만들어집니다. IoT Edge와 관련 된 문제를 디버깅 하기 위해이 사용자 및 시스템 네임 스페이스를 사용할 수 있습니다.  

### <a name="create-config-file-for-system-namespace"></a>System 네임 스페이스에 대 한 구성 파일 만들기

문제를 해결 하려면 먼저를 `config` 사용 하 여 네임 스페이스에 해당 하는 파일을 만듭니다 `iotedge` `aseuser` .

명령을 실행 `Get-HcsKubernetesUserConfig -AseUser` 하 고 출력을 파일로 저장 `config` 합니다 (파일 확장명 없음). `.kube`로컬 컴퓨터에 있는 사용자 프로필의 폴더에 파일을 저장 합니다.

다음은 명령의 샘플 출력입니다 `Get-HcsKubernetesUserConfig` .

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```

계산 역할이 구성 된 Azure Stack Edge 장치에서 모든 `kubectl` 명령을 사용 하 여 모듈을 모니터링 하거나 문제를 해결할 수 있습니다. 사용 가능한 명령 목록을 보려면 `kubectl --help` 명령 창에서를 실행 합니다.

```PowerShell
C:\Users\myuser>kubectl --help
kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

전체 `kubectl` 명령 목록은 [ `kubectl` 참고 자료](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)로 이동 합니다.


### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Kubernetes 클러스터 외부에 노출 되는 서비스 또는 모듈의 IP를 가져오려면

Kubernetes 외부에 노출 된 부하 분산 서비스 또는 모듈의 IP를 가져오려면 다음 명령을 실행 합니다.

`kubectl get svc -n iotedge`

다음은 Kubernetes 클러스터 외부에 노출 되는 모든 서비스 또는 모듈의 샘플 출력입니다. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
외부 IP 열의 IP 주소는 서비스 또는 모듈의 외부 끝점에 해당 합니다. [Kubernetes 대시보드에서 외부 IP를 가져올](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)수도 있습니다.


### <a name="to-check-if-module-deployed-successfully"></a>모듈이 성공적으로 배포 되었는지 확인 하려면

계산 모듈은 비즈니스 논리를 구현 하는 컨테이너입니다. Kubernetes pod는 여러 컨테이너를 실행할 수 있습니다. 계산 모듈이 성공적으로 배포 되었는지 확인 하려면 명령을 실행 하 `get pods` 고 컨테이너 (계산 모듈에 해당)가 실행 중인지 확인 합니다.

특정 네임 스페이스에서 실행 중인 모든 pod 목록을 가져오려면 다음 명령을 실행 합니다.

`get pods -n <namespace>`

다음은 네임 스페이스에서 실행 중인 모든 pod의 샘플 출력입니다 `iotedge` .

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

상태 **상태** 는 네임 스페이스의 모든 pod이 실행 중이 고 **준비** 된는 pod에 배포 된 컨테이너 수를 나타냅니다. 위의 샘플에서는 모든 pod가 실행 되 고 있으며 각 pod에 배포 된 모든 모듈이 실행 중입니다. 

지정 된 네임 스페이스에 대 한 특정 pod의 자세한 출력을 보려면 다음 명령을 실행 하면 됩니다.

`kubectl describe pod <pod name> -n <namespace>` 

샘플 출력은 다음과 같습니다.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>컨테이너 로그를 가져오려면

모듈에 대 한 로그를 가져오려면 다음 명령을 실행 합니다.

`kubectl logs <pod_name> -n <namespace> --all-containers` 

`all-containers`플래그는 모든 컨테이너에 대 한 모든 로그를 덤프 하므로 최근 오류를 확인 하는 좋은 방법은 옵션을 사용 하는 것입니다 `--tail 10` .

다음은 샘플 출력입니다. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>원격 세션 종료

원격 PowerShell 세션을 종료 하려면 PowerShell 창을 닫습니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal에 [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) 를 배포 합니다.
