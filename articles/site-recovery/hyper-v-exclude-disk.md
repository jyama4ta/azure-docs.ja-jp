---
title: Azure Site Recovery を使用した Azure へのディザスター リカバリーから Hyper-V VM ディスクを除外する
description: Azure Site Recovery を使用した Azure へのレプリケーションから Hyper-V VM ディスクを除外する方法。
author: mayurigupta13
manager: rochakm
ms.topic: conceptual
ms.author: mayg
ms.date: 11/12/2019
ms.openlocfilehash: 373cffe96119af4a2fc0d74e2090e6cc24dcaf8f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86131260"
---
# <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

この記事では、Hyper-V VM を Azure にレプリケートするときにディスクを除外する方法について説明します。 レプリケーションからディスクを除外する理由はさまざまです。

- 除外されるディスク上のチャーンされた重要でないデータがレプリケートされないようにする。
- レプリケートする必要のないディスクを除外することで、消費されるレプリケーションの帯域幅またはターゲット側のリソースを最適化する。
- 必要のないデータをレプリケートしないことで、ストレージとネットワークのリソースを節約する。

レプリケーションからディスクを除外する前に:

- ディスクの除外の詳細については、[こちら](exclude-disks-replication.md)をご覧ください。
- ディスクの除外がレプリケーション、フェールオーバー、およびフェールバックにどのように影響するかを示す[一般的な除外シナリオ](exclude-disks-replication.md#typical-scenarios)と[例](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk)の記事を参照してください。

## <a name="before-you-start"></a>開始する前に

開始する前に、次の点に注意してください。

- **[Replication]\(レプリケーション\)** :既定では、マシンのすべてのディスクがレプリケートされます。
- **[ディスクの種類]** :
    - レプリケーションからベーシック ディスクを除外できます。
    - オペレーティング システム ディスクは除外できません。
    - ダイナミック ディスクは除外しないことをお勧めします。 Site Recovery では、ゲスト VM 内のどの VHD がベーシックまたはダイナミック であるかを識別できません。  依存するダイナミック ボリューム ディスクすべてが除外されていない場合、保護されたダイナミック ディスクはフェールオーバー VM 上で障害が発生したディスクになり、そのディスク上のデータにアクセスすることができなくなります。
- **ディスクの追加、削除、除外**:レプリケーションを有効にした後に、レプリケートするディスクの追加、削除、または除外を行うことはできません。 ディスクを追加/削除または除外する場合は、VM の保護を無効にし、再度有効にする必要があります。
- **フェールオーバー**:フェールオーバー後、フェールオーバーされたアプリが機能するために除外されたディスクが必要な場合は、それらのディスクを手動で作成する必要があります。 別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。
- **フェールバック**:フェールオーバー後にオンプレミス サイトにフェールバックする場合、Azure で手動で作成したディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure VM 上に 2 つのディスクを直接作成した場合、フェールオーバーされた 3 つのディスクだけがフェールバックされます。 フェールバックまたは VM のレプリケーションの反転に、手動で作成されたディスクを含めることはできません。

## <a name="exclude-disks"></a>ディスクを除外する

1. Hyper-V VM の [レプリケーションを有効にする](./hyper-v-azure-tutorial.md)ときにディスクを除外するには、レプリケートする VM を選択した後、 **[レプリケーションを有効にする]**  >  **[プロパティ]**  >  **[プロパティの構成]** ページで、 **[レプリケートするディスク]** 列を確認します。 既定では、すべてのディスクがレプリケーションの対象として選択されます。
2. 特定のディスクをレプリケートしない場合は、 **[レプリケートするディスク]** で、除外するディスクの選択を解除します。 

    ![レプリケーションからディスクを除外する](./media/hyper-v-exclude-disk/enable-replication6-with-exclude-disk.png)


## <a name="next-steps"></a>次のステップ
デプロイをセットアップし、実行状態にできたら、各種フェールオーバーの [詳細を確認](failover-failback-overview.md) します。
