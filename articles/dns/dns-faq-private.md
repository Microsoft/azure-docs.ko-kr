---
title: Azure 프라이빗 DNS FAQ
description: 이 문서에서는 Azure 사설 DNS에 대한 질문과 대답을 알아봅니다.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: rohink
ms.openlocfilehash: 4de585a965cfefa6399b0c0929a8f732d0712617
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76939415"
---
# <a name="azure-private-dns-faq"></a>Azure 프라이빗 DNS FAQ

Azure 개인 DNS에 대한 질문과 대답은 다음과 같습니다.

## <a name="does-azure-dns-support-private-domains"></a>Azure DNS에서는 프라이빗 도메인을 지원하나요?

개인 도메인은 Azure 사설 DNS 영역 기능을 사용 하 여 지원 됩니다. 사설 DNS 영역은 지정 된 가상 네트워크 내 에서만 확인할 수 있습니다. 자세한 내용은 [개요](private-dns-overview.md)를 참조하세요.

Azure의 다른 내부 DNS 옵션에 대한 자세한 내용은 [VM 및 역할 인스턴스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)을 참조하세요.

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Azure 지역에서 Azure 사설 DNS 영역을 사용할 수 있나요?

예. 프라이빗 영역에서는 Azure 지역의 가상 네트워크 간 DNS 확인이 지원됩니다. 프라이빗 영역은 가상 네트워크를 명시적으로 피어링하지 않아도 작동합니다. 모든 가상 네트워크는 개인 DNS 영역에 연결 되어야 합니다.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>프라이빗 영역을 위해 가상 네트워크에서 인터넷에 연결되어야 하나요?

아닙니다. 프라이빗 영역은 가상 네트워크에서 작동합니다. 이를 사용 하 여 가상 네트워크 내의 가상 머신 또는 기타 리소스에 대한 도메인을 관리할 수 있습니다. 이름 확인을 위해 인터넷 연결이 필요하지는 않습니다.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>확인을 위해 여러 가상 네트워크에서 동일한 프라이빗 영역을 사용할 수 있나요?

예. 수천 개의 가상 네트워크와 개인 DNS 영역을 연결할 수 있습니다. 자세한 내용은 [Azure DNS 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 을 참조 하세요.

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>다른 구독에 속한 가상 네트워크를 개인 영역에 연결할 수 있나요?

예. 가상 네트워크와 프라이빗 DNS 영역에 대해 쓰기 작업 권한이 있어야 합니다. 쓰기 권한은 여러 RBAC 역할에 부여할 수 있습니다. 예를 들어 클래식 네트워크 참가자 RBAC 역할에는 가상 네트워크에 대한 쓰기 권한이 있고 사설 DNS 영역 참가자 역할에는 개인 DNS 영역에 대한 쓰기 권한이 있습니다. RBAC 역할에 대한 자세한 내용은 [역할 기반 액세스 제어](../role-based-access-control/overview.md)를 참조하세요.

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>가상 컴퓨터를 삭제 하면 자동으로 등록 된 가상 컴퓨터 DNS 레코드가 개인 영역에 자동으로 삭제 됩니까?

예. 이라고를 사용 하는 연결 된 가상 네트워크 내에서 가상 머신을 삭제 하면 등록 된 레코드가 자동으로 삭제 됩니다.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>연결 된 가상 네트워크에서 개인 영역에 자동으로 등록 된 가상 머신 레코드를 수동으로 삭제할 수 있나요?

예. 이러한 자동으로 등록된 DNS 레코드를 영역에서 수동으로 만든 DNS 레코드로 덮어쓸 수 있습니다. 다음 질문과 대답이 이 항목에 대한 것입니다.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>연결 된 가상 네트워크에서 자동으로 등록 된 기존 가상 머신과 동일한 호스트 이름을 가진 개인 영역에 새 DNS 레코드를 수동으로 만들려고 하면 어떻게 되나요?

연결 된 가상 네트워크에 자동으로 등록 된 기존 가상 머신과 동일한 호스트 이름을 가진 개인 영역에 새 DNS 레코드를 수동으로 만들려고 시도 합니다. 이렇게 하면 새 DNS 레코드가 자동으로 등록된 가상 머신 레코드를 덮어씁니다. 이 영역에서 다시 수동으로 만든 이 DNS 레코드를 삭제하려고 하면 삭제가 성공합니다. 가상 머신이 여전히 존재하고 프라이빗 IP가 연결되어 있기만 하면 자동 등록이 다시 수행됩니다. DNS 레코드는 영역에서 자동으로 다시 생성됩니다.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>개인 영역에서 연결 된 가상 네트워크의 연결을 해제 하면 어떻게 되나요? 가상 네트워크의 자동으로 등록된 가상 머신 레코드가 해당 영역에서도 제거되나요?

예. 개인 영역에서 연결 된 가상 네트워크의 연결을 끊으려면 DNS 영역을 업데이트 하 여 연결 된 가상 네트워크 링크를 제거 합니다. 이 프로세스에서 자동으로 등록된 가상 머신 레코드는 영역에서 제거됩니다.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>개인 영역에 연결 된 연결 된 가상 네트워크를 삭제 하면 어떻게 되나요? 영역에서 가상 네트워크를 연결 된 가상 네트워크로 연결 해제 하기 위해 개인 영역을 수동으로 업데이트 해야 하나요?

아닙니다. 먼저 개인 영역에서 연결을 해제 하지 않고 연결 된 가상 네트워크를 삭제 하면 삭제 작업이 성공 하 고 DNS 영역에 대한 링크가 자동으로 지워집니다.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>개인 영역 (예: private.contoso.com)이 가상 네트워크에 연결 된 경우에도 기본 FQDN (internal.cloudapp.net)을 사용 하 여 DNS 확인이 작동 하나요?

예. 사설 영역은 기본 Azure 제공 internal.cloudapp.net 영역을 대체 하지 않습니다. Azure에서 제공한 internal.cloudapp.net을 신뢰하는지 또는 사용자 고유의 프라이빗 영역을 신뢰하는지에 관계없이 확인하려는 영역의 FQDN을 사용하도록 합니다.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>연결된 가상 네트워크 내의 가상 머신에 사용되는 DNS 접미사가 프라이빗 영역의 DNS 접두사로 변경되나요?

아닙니다. , 연결된 가상 네트워크의 가상 머신에 사용된 DNS 접미사는 기본 Azure 제공 접미사("*. internal.cloudapp.net")로 유지됩니다. 가상 머신의 이 DNS 접미사를 프라이빗 영역의 DNS 접미사로 수동으로 변경할 수 있습니다.
이 접미사를 변경 하는 방법에 대한 지침은 [동적 dns를 사용 하 여 자체 DNS 서버에 호스트 이름 등록](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients) 을 참조 하세요.

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>Azure DNS 개인 영역에 대한 사용 제한은 무엇 인가요?

Azure DNS 전용 영역에 대한 사용 제한에 대한 자세한 내용은 [Azure DNS 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#azure-dns-limits) 을 참조 하세요.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>기존 개인 DNS 영역이 새 포털 환경에 표시 되지 않는 이유는 무엇 인가요?

미리 보기 API를 사용 하 여 기존 개인 DNS 영역을 만든 경우 이러한 영역을 새 리소스 모델로 마이그레이션해야 합니다. 미리 보기 API를 사용 하 여 만든 사설 DNS 영역은 새 포털 환경에서 표시 되지 않습니다. 새 리소스 모델로 마이그레이션하는 방법에 대한 지침은 아래를 참조 하세요.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>기존 개인 DNS 영역을 새 모델로 마이그레이션할 어떻게 할까요? 있나요?

가능한 한 빨리 새 리소스 모델로 마이그레이션하는 것이 좋습니다. 레거시 리소스 모델은 지원 되지만이 모델 위에는 추가 기능이 개발 되지 않습니다. 향후에는 새 리소스 모델을 위해 사용 중단. 기존 개인 DNS 영역을 새 리소스 모델로 마이그레이션하는 방법에 대한 지침은[Azure DNS 개인 영역에 대한 마이그레이션 가이드](private-dns-migration-guide.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

- [Azure 사설 DNS에 대한 자세한 정보](private-dns-overview.md)
