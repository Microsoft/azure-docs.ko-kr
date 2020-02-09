---
title: 컨테이너 그룹에 대한 YAML 참조
description: 컨테이너 그룹을 구성 하는 Azure Container Instances에서 지원 되는 YAML 파일에 대한 참조
ms.topic: article
ms.date: 08/12/2019
ms.openlocfilehash: 8497330a327201c4c64e9f7ae57e6fc4225b52de
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896565"
---
# <a name="yaml-reference-azure-container-instances"></a>YAML 참조: Azure Container Instances

이 문서에서는 [컨테이너 그룹](container-instances-container-groups.md)을 구성 하는 Azure Container Instances에서 지원 되는 yaml 파일에 대한 구문 및 속성을 설명 합니다. YAML 파일을 사용 하 여 그룹 구성을 Azure CLI의 [az container create][az-container-create] 명령에 입력 합니다. 

YAML 파일은 재현 가능한 배포를 위해 컨테이너 그룹을 구성 하는 편리한 방법입니다. [리소스 관리자 템플릿](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups) 또는 Azure Container Instances sdk를 사용 하 여 컨테이너 그룹을 만들거나 업데이트 하는 방법에 대한 간단한 대안입니다.

> [!NOTE]
> 이 참조는 Azure Container Instances REST API 버전 `2018-10-01`에 대한 YAML 파일에 적용 됩니다.

## <a name="schema"></a>스키마 

YAML 파일에 대한 스키마는 키 속성을 강조 표시 하는 주석을 포함 하 여 다음을 수행 합니다. 이 스키마의 속성에 대한 설명은 [속성 값](#property-values) 섹션을 참조 하십시오.

```yml
name: string  # Name of the container group
apiVersion: '2018-10-01'
location: string
tags: {}
identity: 
  type: string
  userAssignedIdentities: {}
properties: # Properties of container group
  containers: # Array of container instances in the group
  - name: string # Name of an instance
    properties: # Properties of an instance
      image: string # Container image used to create the instance
      command:
      - string
      ports: # External-facing ports exposed on the instance, must also be set in group ipAddress property 
      - protocol: string
        port: integer
      environmentVariables:
      - name: string
        value: string
        secureValue: string
      resources: # Resource requirements of the instance
        requests:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
        limits:
          memoryInGB: number
          cpu: number
          gpu:
            count: integer
            sku: string
      volumeMounts: # Array of volume mounts for the instance
      - name: string
        mountPath: string
        readOnly: boolean
      livenessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
      readinessProbe:
        exec:
          command:
          - string
        httpGet:
          path: string
          port: integer
          scheme: string
        initialDelaySeconds: integer
        periodSeconds: integer
        failureThreshold: integer
        successThreshold: integer
        timeoutSeconds: integer
  imageRegistryCredentials: # Credentials to pull a private image
  - server: string
    username: string
    password: string
  restartPolicy: string
  ipAddress: # IP address configuration of container group
    ports:
    - protocol: string
      port: integer
    type: string
    ip: string
    dnsNameLabel: string
  osType: string
  volumes: # Array of volumes available to the instances
  - name: string
    azureFile:
      shareName: string
      readOnly: boolean
      storageAccountName: string
      storageAccountKey: string
    emptyDir: {}
    secret: {}
    gitRepo:
      directory: string
      repository: string
      revision: string
  diagnostics:
    logAnalytics:
      workspaceId: string
      workspaceKey: string
      logType: string
      metadata: {}
  networkProfile: # Virtual network profile for container group
    id: string
  dnsConfig: # DNS configuration for container group
    nameServers:
    - string
    searchDomains: string
    options: string
```

## <a name="property-values"></a>속성 값

다음 표에서는 스키마에 설정해야 하는 값에 대해 설명합니다.

<a id="Microsoft.ContainerInstance/containerGroups" />

### <a name="microsoftcontainerinstancecontainergroups-object"></a>ContainerInstance/containerGroups 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이름 | 문자열 | yes | 컨테이너 그룹의 이름입니다. |
|  apiVersion | enum | yes | 2018-10-01 |
|  location | 문자열 | 아닙니다. | 리소스 위치입니다. |
|  tags | object | 아닙니다. | 리소스 태그입니다. |
|  ID | object | 아닙니다. | 구성 된 경우 컨테이너 그룹의 id입니다. - [ContainerGroupIdentity 개체](#ContainerGroupIdentity) |
|  properties | object | yes | [ContainerGroupProperties 개체](#ContainerGroupProperties) |


<a id="ContainerGroupIdentity" />

### <a name="containergroupidentity-object"></a>ContainerGroupIdentity 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  type | enum | 아닙니다. | 컨테이너 그룹에 사용 되는 id 유형입니다. ' SystemAssigned 됨, UserAssigned 됨 ' 형식에는 암시적으로 생성 된 id와 사용자 할당 id 집합이 모두 포함 됩니다. ' 없음 ' 유형은 컨테이너 그룹에서 모든 id를 제거 합니다. -SystemAssigned 됨, UserAssigned 됨, SystemAssigned 됨, UserAssigned 안 함 |
|  userAssignedIdentities | object | 아닙니다. | 컨테이너 그룹과 연결 된 사용자 id 목록입니다. 사용자 id 사전 키 참조는 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName} ' 형식으로 리소스 Id를 Azure Resource Manager 됩니다. |


<a id="ContainerGroupProperties" />

### <a name="containergroupproperties-object"></a>ContainerGroupProperties 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  containers | array | yes | 컨테이너 그룹 내의 컨테이너입니다.[컨테이너 개체](#Container)  -  |
|  imageRegistryCredentials | array | 아닙니다. | 컨테이너 그룹을 만들 때 기준이 되는 이미지 레지스트리 자격 증명입니다. - [ImageRegistryCredential 개체](#ImageRegistryCredential) |
|  restartPolicy | enum | 아닙니다. | 컨테이너 그룹 내의 모든 컨테이너에 대한 다시 시작 정책입니다. - `Always` 항상 다시 시작-오류 발생 시 다시 시작 `OnFailure` `Never` 다시 시작 안 함 -Always, OnFailure, Never |
|  \\ | object | 아닙니다. | 컨테이너 그룹의 IP 주소 유형입니다. - [IpAddress 개체](#IpAddress) |
|  osType | enum | yes | 컨테이너 그룹의 컨테이너에 필요한 운영 체제 유형입니다. -Windows 또는 Linux |
|  volumes | array | 아닙니다. | 이 컨테이너 그룹의 컨테이너로 탑재할 수 있는 볼륨의 목록입니다.[볼륨 개체](#Volume)  -  |
|  진단 | object | 아닙니다. | 컨테이너 그룹에 대한 진단 정보입니다. - [ContainerGroupDiagnostics 개체](#ContainerGroupDiagnostics) |
|  NetworkProfile | object | 아닙니다. | 컨테이너 그룹에 대한 네트워크 프로필 정보입니다. - [ContainerGroupNetworkProfile 개체](#ContainerGroupNetworkProfile) |
|  dnsConfig | object | 아닙니다. | 컨테이너 그룹에 대한 DNS 구성 정보입니다. - [Dnsconfiguration 개체](#DnsConfiguration) |


<a id="Container" />

### <a name="container-object"></a>컨테이너 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이름 | 문자열 | yes | 컨테이너 인스턴스의 사용자 제공 이름입니다. |
|  properties | object | yes | 컨테이너 인스턴스의 속성입니다. - [ContainerProperties 개체](#ContainerProperties) |


<a id="ImageRegistryCredential" />

### <a name="imageregistrycredential-object"></a>ImageRegistryCredential 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  서버 | 문자열 | yes | 프로토콜이 없는 Docker 이미지 레지스트리 서버 (예: "http" 및 "https") |
|  username | 문자열 | yes | 개인 레지스트리의 사용자 이름입니다. |
|  암호 | 문자열 | 아닙니다. | 개인 레지스트리에 대한 암호입니다. |


<a id="IpAddress" />

### <a name="ipaddress-object"></a>IpAddress 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  ports | array | yes | 컨테이너 그룹에 노출 된 포트 목록입니다. - [Port 개체](#Port) |
|  type | enum | yes | IP가 공용 인터넷 또는 개인 VNET에 노출 되는지 여부를 지정 합니다. -공용 또는 개인 |
|  ip | 문자열 | 아닙니다. | 공용 인터넷에 노출 되는 IP입니다. |
|  dnsNameLabel | 문자열 | 아닙니다. | IP의 Dns 이름 레이블입니다. |


<a id="Volume" />

### <a name="volume-object"></a>볼륨 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이름 | 문자열 | yes | 볼륨의 이름입니다. |
|  azureFile | object | 아닙니다. | Azure 파일 볼륨입니다. - [AzureFileVolume 개체](#AzureFileVolume) |
|  emptyDir | object | 아닙니다. | 빈 디렉터리 볼륨입니다. |
|  secret | object | 아닙니다. | 비밀 볼륨입니다. |
|  gitRepo | object | 아닙니다. | Git 리포지토리 볼륨입니다. - [Gitrepovolume 개체](#GitRepoVolume) |


<a id="ContainerGroupDiagnostics" />

### <a name="containergroupdiagnostics-object"></a>ContainerGroupDiagnostics 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  logAnalytics | object | 아닙니다. | 컨테이너 그룹 로그 분석 정보입니다.[Loganalytics 개체](#LogAnalytics)  -  |


<a id="ContainerGroupNetworkProfile" />

### <a name="containergroupnetworkprofile-object"></a>ContainerGroupNetworkProfile 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  id | 문자열 | yes | 네트워크 프로필의 식별자입니다. |


<a id="DnsConfiguration" />

### <a name="dnsconfiguration-object"></a>DnsConfiguration 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이름 서버 | array | yes | 컨테이너 그룹에 대한 DNS 서버입니다. -문자열 |
|  searchDomains | 문자열 | 아닙니다. | 컨테이너 그룹에서 호스트 이름 조회에 대한 DNS 검색 도메인입니다. |
|  options | 문자열 | 아닙니다. | 컨테이너 그룹에 대한 DNS 옵션입니다. |


<a id="ContainerProperties" />

### <a name="containerproperties-object"></a>ContainerProperties 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이미지 | 문자열 | yes | 컨테이너 인스턴스를 만드는 데 사용 되는 이미지의 이름입니다. |
|  command | array | 아닙니다. | Exec 형식의 컨테이너 인스턴스 내에서 실행할 명령입니다. -문자열 |
|  ports | array | 아닙니다. | 컨테이너 인스턴스의 노출 된 포트입니다. - [ContainerPort 개체](#ContainerPort) |
|  environmentVariables | array | 아닙니다. | 컨테이너 인스턴스에서 설정할 환경 변수입니다. - [고 environmentvariable 개체](#EnvironmentVariable) |
|  리소스 | object | yes | 컨테이너 인스턴스의 리소스 요구 사항입니다. - [ResourceRequirements 개체](#ResourceRequirements) |
|  volumeMounts | array | 아닙니다. | 컨테이너 인스턴스에 사용할 수 있는 볼륨 탑재입니다. - [VolumeMount 개체](#VolumeMount) |
|  livenessProbe | object | 아닙니다. | 선거의 프로브입니다. - [ContainerProbe 개체](#ContainerProbe) |
|  readinessProbe | object | 아닙니다. | 준비 프로브입니다. - [ContainerProbe 개체](#ContainerProbe) |


<a id="Port" />

### <a name="port-object"></a>포트 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 아닙니다. | 포트와 연결 된 프로토콜입니다. -TCP 또는 UDP |
|  포트 | 정수 | yes | 포트 번호 |


<a id="AzureFileVolume" />

### <a name="azurefilevolume-object"></a>AzureFileVolume 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  공유 | 문자열 | yes | 볼륨으로 탑재할 Azure 파일 공유의 이름입니다. |
|  readOnly | 부울 | 아닙니다. | 볼륨으로 탑재 된 Azure 파일 공유를 읽기 전용인 지 여부를 나타내는 플래그입니다. |
|  storageAccountName | 문자열 | yes | Azure 파일 공유를 포함 하는 저장소 계정의 이름입니다. |
|  storageAccountKey | 문자열 | 아닙니다. | Azure 파일 공유에 액세스 하는 데 사용 되는 저장소 계정 액세스 키입니다. |


<a id="GitRepoVolume" />

### <a name="gitrepovolume-object"></a>GitRepoVolume 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  디렉터리 | 문자열 | 아닙니다. | 대상 디렉터리 이름입니다. '.. '를 포함 하거나 시작 해서는 안 됩니다.  '. '가 제공 되 면 볼륨 디렉터리는 git 리포지토리가 됩니다.  그렇지 않으면 지정 된 경우 볼륨은 지정 된 이름의 하위 디렉터리에 git 리포지토리를 포함 합니다. |
|  리포지토리 | 문자열 | yes | 리포지토리 URL |
|  revision | 문자열 | 아닙니다. | 지정 된 수정 버전에 대한 해시를 커밋합니다. |


<a id="LogAnalytics" />

### <a name="loganalytics-object"></a>LogAnalytics 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  workspaceId | 문자열 | yes | Log analytics에 대한 작업 영역 id |
|  workspaceKey | 문자열 | yes | Log analytics에 대한 작업 영역 키 |
|  logType | enum | 아닙니다. | 사용할 로그 유형입니다. -ContainerInsights 또는 ContainerInstanceLogs |
|  metadata | object | 아닙니다. | Log analytics에 대한 메타 데이터입니다. |


<a id="ContainerPort" />

### <a name="containerport-object"></a>ContainerPort 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  protocol | enum | 아닙니다. | 포트와 연결 된 프로토콜입니다. -TCP 또는 UDP |
|  포트 | 정수 | yes | 컨테이너 그룹 내에서 노출 되는 포트 번호입니다. |


<a id="EnvironmentVariable" />

### <a name="environmentvariable-object"></a>고 environmentvariable 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이름 | 문자열 | yes | 환경 변수의 이름입니다. |
|  값 | 문자열 | 아닙니다. | 환경 변수의 값입니다. |
|  secureValue | 문자열 | 아닙니다. | 보안 환경 변수의 값입니다. |


<a id="ResourceRequirements" />

### <a name="resourcerequirements-object"></a>ResourceRequirements 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  requests | object | yes | 이 컨테이너 인스턴스의 리소스 요청입니다. - [ResourceRequests 개체](#ResourceRequests) |
|  제한 | object | 아닙니다. | 이 컨테이너 인스턴스의 리소스 제한입니다. - [Resourcelimits 개체](#ResourceLimits) |


<a id="VolumeMount" />

### <a name="volumemount-object"></a>VolumeMount 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  이름 | 문자열 | yes | 볼륨 탑재의 이름입니다. |
|  mountPath | 문자열 | yes | 볼륨이 탑재 되어야 하는 컨테이너 내의 경로입니다. 콜론 (:)을 포함 하지 않아야 합니다. |
|  readOnly | 부울 | 아닙니다. | 볼륨 탑재를 읽기 전용인 지 여부를 나타내는 플래그입니다. |


<a id="ContainerProbe" />

### <a name="containerprobe-object"></a>ContainerProbe 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  exec | object | 아닙니다. | [ContainerExec 개체](#ContainerExec) 에 대한 실행 명령 |
|  httpGet | object | 아닙니다. | [ContainerHttpGet 개체](#ContainerHttpGet) 에 대한 Http 설정 가져오기 |
|  initialDelaySeconds | 정수 | 아닙니다. | 초기 지연 시간 (초)입니다. |
|  periodSeconds | 정수 | 아닙니다. | 기간 (초)입니다. |
|  카운터가 failurethreshold | 정수 | 아닙니다. | 오류 임계값입니다. |
|  successThreshold | 정수 | 아닙니다. | 성공 임계값입니다. |
|  timeoutSeconds | 정수 | 아닙니다. | 시간 제한 (초)입니다. |


<a id="ResourceRequests" />

### <a name="resourcerequests-object"></a>ResourceRequests 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | yes | 이 컨테이너 인스턴스의 메모리 요청 (GB)입니다. |
|  CPU | number | yes | 이 컨테이너 인스턴스의 CPU 요청입니다. |
|  GPU | object | 아닙니다. | 이 컨테이너 인스턴스의 GPU 요청입니다. - [GpuResource 개체](#GpuResource) |


<a id="ResourceLimits" />

### <a name="resourcelimits-object"></a>ResourceLimits 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  memoryInGB | number | 아닙니다. | 이 컨테이너 인스턴스의 메모리 한도 (GB)입니다. |
|  CPU | number | 아닙니다. | 이 컨테이너 인스턴스의 CPU 한도입니다. |
|  GPU | object | 아닙니다. | 이 컨테이너 인스턴스의 GPU 제한입니다. - [GpuResource 개체](#GpuResource) |


<a id="ContainerExec" />

### <a name="containerexec-object"></a>ContainerExec 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  command | array | 아닙니다. | 컨테이너 내에서 실행할 명령입니다. -문자열 |


<a id="ContainerHttpGet" />

### <a name="containerhttpget-object"></a>ContainerHttpGet 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  경로 | 문자열 | 아닙니다. | 프로브 경로입니다. |
|  포트 | 정수 | yes | 프로브 할 포트 번호입니다. |
|  scheme | enum | 아닙니다. | 체계입니다. -http 또는 https |


<a id="GpuResource" />

### <a name="gpuresource-object"></a>GpuResource 개체

|  name | Type | 필수 | Value |
|  ---- | ---- | ---- | ---- |
|  count | 정수 | yes | GPU 리소스의 수입니다. |
|  sku | enum | yes | GPU 리소스의 SKU입니다. -K80, P100, V100 |


## <a name="next-steps"></a>다음 단계

[YAML 파일을 사용 하 여 다중 컨테이너 그룹 배포](container-instances-multi-container-yaml.md)자습서를 참조 하세요.

YAML 파일을 사용 하 여 [가상 네트워크](container-instances-vnet.md) 에 컨테이너 그룹을 배포 하거나 [외부 볼륨을 탑재](container-instances-volume-azure-files.md)하는 예제를 참조 하세요.

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create

