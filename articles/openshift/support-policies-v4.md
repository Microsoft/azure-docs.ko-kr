---
title: Azure Red Hat OpenShift 4 클러스터 지원 정책
description: Red Hat OpenShift 4의 지원 정책 요구 사항을 이해합니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: f496d0f38452fa7cf64bc9eef370bd0b2116cfa5
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049964"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift 지원 정책

Azure Red Hat OpenShift 4 클러스터에 대한 특정 구성은 클러스터의 지원 가능성에 영향을 줄 수 있습니다. Azure Red Hat OpenShift 4를 통해 클러스터 관리자는 내부 클러스터 구성 요소를 변경할 수 있지만 일부 변경은 지원되지 않습니다. 아래 지원 정책에서는 정책을 위반한 수정 사항과 Microsoft 및 Red Hat의 void 지원을 공유합니다.

> [!NOTE]
> OpenShift Container 플랫폼에서 기술 미리 보기로 표시된 기능은 Azure Red Hat OpenShift에서 지원되지 않습니다.

## <a name="cluster-configuration-requirements"></a>클러스터 구성 요구 사항

* 모든 OpenShift 클러스터 연산자는 관리 상태로 유지되어야 합니다. 클러스터 연산자 목록은 `oc get clusteroperators`를 실행하여 반환할 수 있습니다.
* 클러스터 Prometheus 및 Alertmanager 서비스를 제거하거나 수정하지 마세요.
* Service Alertmanager 규칙을 제거하지 마세요.
* Azure Red Hat OpenShift 서비스 로깅(mdsd pod)을 제거하거나 수정하지 마세요.
* 'arosvc.azurecr.io' 클러스터 풀 비밀을 제거하거나 수정하지 마세요.
* 모든 클러스터 가상 머신에는 최소한 ARM(Azure Resource Manager) 및 서비스 로깅(Geneva) 엔드포인트에 대한 직접적인 아웃바운드 인터넷 액세스 권한이 있어야 합니다.  HTTPS 프록시 형식은 지원되지 않습니다.
* 클러스터의 가상 네트워크에 대한 DNS 구성을 수정하지 마세요. 기본 Azure DNS 확인자를 사용해야 합니다.
* 어떤 방식으로든 클러스터의 MachineConfig 개체(예: kubelet 구성)를 재정의하지 마세요.
* Azure Red Hat OpenShift 서비스는 Private Link Service를 통해 클러스터에 액세스합니다.  서비스 액세스를 제거하거나 수정하지 마세요.
* 비 RHCOS 컴퓨팅 노드는 지원되지 않습니다. 예를 들어 RHEL 컴퓨팅 노드를 사용할 수 없습니다.

## <a name="supported-virtual-machine-sizes"></a>지원되는 가상 머신 크기

Azure Red Hat OpenShift 4는 다음 가상 머신 크기의 작업자 노드 인스턴스를 지원합니다.

### <a name="general-purpose"></a>범용 가상 컴퓨터

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>메모리에 최적화

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>컴퓨팅 최적화

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>마스터 노드

|계열|크기|vCPU|메모리: GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
