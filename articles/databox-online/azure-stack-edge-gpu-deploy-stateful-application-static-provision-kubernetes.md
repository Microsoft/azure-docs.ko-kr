---
title: Kubectl를 사용 하 여 Azure Stack Edge 장치에서 정적으로 프로 비전 된 공유를 통해 Kubernetes 상태 저장 앱 배포 | Microsoft Docs
description: Azure Stack Edge GPU 장치에서 kubectl를 사용 하 여 정적으로 프로 비전 된 공유를 통해 Kubernetes 상태 저장 응용 프로그램 배포를 만들고 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/18/2020
ms.author: alkohli
ms.openlocfilehash: 17be54536f785049aef6831e01f1f12219225b90
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254375"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-device"></a>Kubectl를 사용 하 여 Azure Stack Edge 장치에서 PersistentVolume로 Kubernetes 상태 저장 응용 프로그램을 실행 합니다.

이 문서에서는 PersistentVolume (PV) 및 배포를 사용 하 여 Kubernetes에서 단일 인스턴스 상태 저장 응용 프로그램을 배포 하는 방법을 보여 줍니다. 배포는 `kubectl` 기존 Kubernetes 클러스터에서 명령을 사용 하 고 MySQL 응용 프로그램을 배포 합니다. 

이 절차는 [Azure Stack Edge 장치에서 Kubernetes 저장소](azure-stack-edge-gpu-kubernetes-storage.md) 를 검토 하 고 [Kubernetes 저장소](https://kubernetes.io/docs/concepts/storage/)의 개념에 대해 잘 알고 있는 사용자를 위한 것입니다.


## <a name="prerequisites"></a>필수 구성 요소

상태 저장 응용 프로그램을 배포 하기 전에 장치에서 장치에 액세스 하는 데 사용할 클라이언트 및 장치에 대 한 다음 필수 구성 요소를 완료 했는지 확인 합니다.

### <a name="for-device"></a>디바이스의 경우

- 1 노드 Azure Stack Edge 장치에 대 한 로그인 자격 증명이 있습니다.
    - 장치가 활성화 됩니다. [장치 활성화를](azure-stack-edge-gpu-deploy-activate.md)참조 하세요.
    - 장치에 Azure Portal를 통해 구성 된 계산 역할이 있으며, Kubernetes 클러스터가 있습니다. [Compute 구성](azure-stack-edge-gpu-deploy-configure-compute.md)을 참조 하세요.

### <a name="for-client-accessing-the-device"></a>장치에 액세스 하는 클라이언트

- Azure Stack Edge 장치에 액세스 하는 데 사용 되는 Windows 클라이언트 시스템이 있습니다.
    - 클라이언트에서 Windows PowerShell 5.0 이상을 실행 하 고 있습니다. 최신 버전의 Windows PowerShell을 다운로드 하려면 [Windows Powershell 설치](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-7)로 이동 합니다.
    
    - [지원 되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 를 사용 하는 다른 클라이언트도 있을 수 있습니다. 이 문서에서는 Windows 클라이언트를 사용 하는 절차에 대해 설명 합니다. 
    
    - [Azure Stack Edge 장치에서 Kubernetes 클러스터에 액세스](azure-stack-edge-gpu-create-kubernetes-cluster.md)에 설명 된 절차를 완료 했습니다. 수행한 작업은 다음과 같습니다.
      - `userns1`명령을 통해 네임 스페이스를 만들었습니다 `New-HcsKubernetesNamespace` . 
      - `user1`명령을 통해 사용자를 만들었습니다 `New-HcsKubernetesUser` . 
      - `user1`명령을 통해에 대 한 액세스 권한이 부여 `userns1` `Grant-HcsKubernetesNamespaceAccess` 됩니다.       
      - `kubectl`클라이언트에 설치 되 고 `kubeconfig` 사용자 구성으로 파일을 C: \\ Users 사용자 \\ &lt; 이름 &gt; \\ . kube에 저장 합니다. 
    
    - `kubectl`클라이언트 버전이 Azure Stack Edge 장치에서 실행 되는 Kubernetes 마스터 버전에서 둘 이상의 버전을 사용 하지 않는지 확인 합니다. 
        - `kubectl version`클라이언트에서 실행 되는 kubectl의 버전을 확인 하는 데 사용 합니다. 전체 버전을 기록해 둡니다.
        - Azure Stack Edge 장치의 로컬 UI에서 **개요** 로 이동 하 여 Kubernetes software 번호를 확인 합니다. 
        - 지원 되는 Kubernetes 버전에서 제공 되는 매핑과의 호환성을 위해 이러한 두 버전을 확인 합니다. <!-- insert link-->. 


Azure Stack Edge 장치에 상태 저장 응용 프로그램을 배포할 준비가 되었습니다. 

## <a name="provision-a-static-pv"></a>정적 PV 프로 비전

PV를 정적으로 프로 비전 하려면 장치에 공유를 만들어야 합니다. SMB 또는 NFS 공유에 대해 PV를 프로 비전 하려면 다음 단계를 수행 합니다. 

1. 에 지 공유 또는에 지 로컬 공유를 만들지 여부를 선택 합니다. 공유 [추가](azure-stack-edge-manage-shares.md#add-a-share) 의 지침에 따라 공유를 만듭니다. **Edge 계산에 공유 사용**에 대 한 확인란을 선택 해야 합니다.

    ![PV에 대 한 Edge 로컬 공유](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. 새 공유를 만드는 대신 기존 공유를 사용 하기로 결정 한 경우 공유를 탑재 해야 합니다.
    
        Azure Stack에 지 리소스에 대 한 Azure Portal에서 **공유**로 이동 합니다. 기존 공유 목록에서 사용 하려는 공유를 선택 하 고 클릭 합니다.

        ![PV에 대 한 기존 로컬 공유 선택](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. **탑재** 를 선택 하 고 메시지가 표시 되 면 탑재를 확인 합니다.  

        ![PV에 대 한 기존 로컬 공유 탑재](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. 공유 이름을 적어 둡니다. 이 공유를 만들 때 생성 된 SMB 또는 NFS 공유에 해당 하는 Kubernetes 클러스터에 영구적 볼륨 개체가 자동으로 생성 됩니다. 

## <a name="deploy-mysql"></a>MySQL 배포

이제 Kubernetes 배포를 만들고 PersistentVolumeClaim (PVC)를 사용 하 여 이전 단계에서 만든 PV에 연결 하 여 상태 저장 응용 프로그램을 실행 합니다. 

`kubectl`상태 저장 응용 프로그램 배포를 만들고 관리 하는 데 사용 하는 모든 명령은 구성과 연결 된 네임 스페이스를 지정 해야 합니다. Kubectl 명령에서 네임 스페이스를 지정 하려면를 사용 `kubectl <command> -n <your-namespace>` 합니다.

1. 네임 스페이스의 Kubernetes 클러스터에서 실행 중인 pod의 목록을 가져옵니다. Pod는 Kubernetes 클러스터에서 실행 되는 응용 프로그램 컨테이너 또는 프로세스입니다.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   다음은 명령 사용의 예입니다.
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   출력에는 클러스터에서 실행 중인 응용 프로그램이 없기 때문에 리소스 (pod)가 없다는 것을 명시 해야 합니다.

1. 다음 YAML 파일을 사용 합니다. `mysql-deployment.yml`이 파일은 MySQL을 실행 하 고 PVC를 참조 하는 배포를 설명 합니다. 파일은에 대 한 볼륨 탑재를 정의한 `/var/lib/mysql` 후 20gb 볼륨을 검색 하는 PVC를 만듭니다. 

    이 클레임은 이전 단계에서 공유를 만들 때 정적으로 프로 비전 된 기존 PV에 의해 충족 됩니다. 장치에서 각 공유에 대해 32 TB의 긴 PV가 만들어집니다. PV는 PVC에 의해 설정 된 요구 사항을 충족 하 고 PVC는이 PV에 바인딩되어야 합니다.

    `mysql-deployment.yml`Azure Stack Edge 장치에 액세스 하는 데 사용 하는 Windows 클라이언트의 폴더에 다음 파일을 복사 하 여 저장 합니다.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. `mysql-pv.yml`을 저장 한 폴더와 동일한 폴더에 파일을 복사 하 고 저장 `mysql-deployment.yml` 합니다. 이전에에서 만든 SMB 또는 NFS 공유를 사용 하려면 `kubectl` `volumeName` PVC 개체의 필드를 공유 이름으로 설정 합니다. 

    > [!NOTE] 
    > YAML 파일의 들여쓰기가 올바른지 확인 합니다. [Yaml 보풀이](http://www.yamllint.com/) 있는지 확인 하 여 유효성을 검사 한 후 저장할 수 있습니다.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <nfs-or-smb-share-name-here>
      storageClassName: manual
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. 파일을 배포 `mysql-pv.yaml` 합니다.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    배포의 샘플 출력은 다음과 같습니다.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   만든 PVC의 이름을 적어둡니다. 이후 단계에서 사용 합니다. 

4. 파일의 콘텐츠를 배포 `mysql-deployment.yml` 합니다.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    배포의 샘플 출력은 다음과 같습니다.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. 배포에 대 한 정보를 표시 합니다.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. 배포에 의해 생성 된 pod를 나열 합니다.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    샘플 출력은 다음과 같습니다.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. PersistentVolumeClaim를 검사 합니다.

    `kubectl describe pvc <your-pvc-name>`

    샘플 출력은 다음과 같습니다.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>MySQL이 실행 중인지 확인

이전 YAML 파일은 클러스터의 Pod가 데이터베이스에 액세스할 수 있도록 하는 서비스를 만듭니다. ClusterIP: None 서비스 옵션을 사용 하면 서비스 DNS 이름을 Pod의 IP 주소로 직접 확인할 수 있습니다. 이는 서비스 뒤에 Pod가 하나 뿐 이며 Pod의 수를 늘리지 않으려는 경우에 가장 적합 합니다.

MySQL을 실행 하는 pod의 컨테이너에 대해 명령을 실행 하려면 다음을 입력 합니다.

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

샘플 출력은 다음과 같습니다.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>배포 삭제

배포를 삭제 하려면 배포 된 개체를 이름으로 삭제 합니다. 이러한 개체에는 배포, 서비스 및 PVC가 포함 됩니다.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

다음은 배포 및 서비스를 삭제할 때의 샘플 출력입니다.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
다음은 PVC를 삭제할 때의 샘플 출력입니다.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```                                                                                         

PVC가 삭제 된 후에는 더 이상 해당 PV를 PVC에 바인딩하지 않습니다. 공유가 생성 될 때 PV가 프로 비전 되 면 공유를 삭제 해야 합니다. 다음 단계를 수행합니다.

1. 공유를 탑재 해제 합니다. Azure Portal에서 **Azure Stack Edge 리소스 > 공유** 로 이동 하 여 분리 하려는 공유를 선택 하 고 클릭 합니다. **분리** 를 선택 하 고 작업을 확인 합니다. 공유가 분리 될 때까지 기다립니다. 탑재 해제는 Kubernetes 클러스터에서 공유 (따라서 연결 된 PersistentVolume)를 해제 합니다. 

    ![PV에 대 한 로컬 공유 분리](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. 이제 **삭제** 를 선택 하 고 삭제를 확인 하 여 공유를 삭제할 수 있습니다. 또한 공유 및 해당 PV를 삭제 해야 합니다.

    ![PV에 대 한 로컬 공유 삭제](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>다음 단계

저장소를 동적으로 프로 비전 하는 방법을 이해 하려면 [Azure Stack Edge 장치에서 동적 프로 비전을 통해 상태 저장 응용 프로그램 배포](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md) 를 참조 하세요.
