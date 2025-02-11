---
title: Azure Site Recovery の監視 | Microsoft Docs
description: ポータルを使って、Azure Site Recovery でのレプリケーションの問題と動作の監視およびトラブルシューティングを行います
author: raynew
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: d441284b265ab11dd5ece42ec3737e455d662435
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023569"
---
# <a name="monitor-site-recovery"></a>Site Recovery の監視

この記事では、Azure [Site Recovery](site-recovery-overview.md) を、Site Recovery に組み込まれている監視機能を使用して監視する方法について説明します。  以下を監視できます。

- Site Recovery によってレプリケートされたマシンの正常性と状態。
- マシンのテスト フェールオーバーの状態。
- 構成とレプリケーションに影響する問題とエラー。
- オンプレミス サーバーなどのインフラストラクチャ コンポーネント。


## <a name="before-you-start"></a>開始する前に

開始する前に、[監視に関する一般的な質問](monitoring-common-questions.md)を確認することをお勧めします。

## <a name="monitor-in-the-dashboard"></a>ダッシュボードでの監視

1. コンテナーで、 **[概要]** をクリックします。 Recovery Services ダッシュボードには、コンテナーのすべての監視情報が 1 か所にまとめられています。 Site Recoveryサービスと Azure Backup サービス には、どちらにも専用のページがあり、それらを切り替えることができます。

    ![Site Recovery のダッシュボード](./media/site-recovery-monitor-and-troubleshoot/dashboard.png)

2. ダッシュボードから、さまざまな領域にドリルダウンすることができます。 

    ![ドリルダウンできるダッシュボード上の領域を示すスクリーンショット。](./media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png).

3. **[レプリケートされたアイテム]** で **[すべて表示]** をクリックすると、コンテナー内のすべてのサーバーが表示されます。
4. ドリルダウンするには、各セクションの状態の詳細をクリックします。
5. **[インフラストラクチャ ビュー]** では、レプリケートされているマシンの種類ごとに監視情報を並べ替えます。

## <a name="monitor-replicated-items"></a>レプリケートされたアイテムの監視

**[レプリケートされたアイテム]** セクションでは、レプリケーションが有効にされているコンテナー内のすべてのマシンの正常性を監視します。

**State** | **詳細**
--- | ---
Healthy | レプリケーションは正常に進行しています。 エラーや警告の症状は検出されていません。
警告 | レプリケーションに影響を及ぼす可能性がある警告の症状が少なくとも 1 件検出されています。
Critical | 重大なレプリケーション エラーの症状が少なくとも 1 件検出されました。<br/><br/> これらのエラー症状は、通常、レプリケーションがスタックしていること、またはデータ変更速度に処理速度が追い付いていないことを示します。
適用なし | 現在、サーバーはレプリケートを行っていないものと思われます。 たとえば、マシンがフェールオーバーされた可能性があります。

## <a name="monitor-test-failovers"></a>テスト フェールオーバーの監視

**[フェールオーバー テスト成功]** では、コンテナー内のマシンのフェールオーバーの状態を監視します。

- レプリケートされたマシンについて、少なくとも半年に 1 回はテスト フェールオーバーを実行することをお勧めします。 それによって、フェールオーバーが想定どおりに機能し、運用環境の中断が発生していないことを確認できます。 
- フェールオーバーとその後のクリーンアップが正常に完了したときにはじめて、テスト フェールオーバーが成功したと見なされます。

**State** | **詳細**
--- | ---
テストをお勧めします | このマシンは、保護が有効にされてから、まだテスト フェールオーバーが実施されていません。
正常に実行されました | このマシンは、テスト フェールオーバーが少なくとも 1 回成功しています。
適用なし | このマシンは現在、テスト フェールオーバーの対象ではありません。 たとえばフェールオーバーされたマシンや、初回レプリケーション/テスト フェールオーバー/フェールオーバーが進行中のマシンが該当します。

## <a name="monitor-configuration-issues"></a>構成の問題の監視

**[構成の問題]** では、正常にフェールオーバーする機能に影響する可能性があるすべての問題を監視します。

- 構成の問題 (ソフトウェア更新プログラムの提供状況を除く) は、既定では 12 時間ごとに実行される定期的な検証コントロール操作によって検出されます。 "**構成の問題**" のセクション見出しの横にある更新アイコンをクリックすることにより、検証コントロール操作を強制的にすぐ実行できます。
- リンクをクリックすると詳細情報が表示されます。 特定のマシンに影響を及ぼす問題については、 **[ターゲット構成]** 列の **[要注意]** をクリックします。 この詳細情報には、修復に関するアドバイスが含まれます。

**State** | **詳細**
--- | ---
不足している構成 | 復旧ネットワーク、リソース グループなど、必要な設定が不足しています。
不足しているリソース | 指定されたリソースが見つからないか、サブスクリプションで利用できません。 たとえばリソースが削除されているか、移行されています。 監視対象リソースには、ターゲット リソース グループ、ターゲット VNet/サブネット、ログ/ターゲット ストレージ アカウント、ターゲットの可用性セット、ターゲット IP アドレスが含まれていました。
サブスクリプション クォータ |  使用可能なサブスクリプション リソース クォータの残量が、コンテナー内のすべてのマシンをフェールオーバーするために必要な残量と比較されます。<br/><br/> リソースが不足していると、クォータ残量不足が報告されます。<br/><br/> VM のコア数、VM ファミリのコア数、ネットワーク インターフェイス カード (NIC) の数がクォータによって監視されます。
ソフトウェア更新プログラム | 新しいソフトウェア更新プログラムの提供状況、ソフトウェア バージョンの期限切れに関する情報です。


## <a name="monitor-errors"></a>エラーを監視する

**[エラーの概要]** では、コンテナー内のサーバーのレプリケーションに影響を及ぼす可能性がある現在アクティブなエラーの症状を監視し、影響を受けているマシンの数を監視します。

- オンプレミスのインフラストラクチャ コンポーネントに影響を及ぼしているエラーが、このセクションの先頭に表示されます。 たとえば、オンプレミスの構成サーバー上の Azure Site Recovery プロバイダーまたは Hyper-V ホスト からハートビートが受信されていないことが示されます。
- 次に、レプリケート対象サーバーに影響を与えているレプリケーション エラーの症状が表示されます。
- テーブルのエントリは、最初にエラーの重大度の高い順に、次に影響を受けたマシンの数の多い順に、並べ替えられます。
- 影響を受けているサーバーの数は、基になる 1 つの問題が複数のマシンに影響している可能性があるかどうかを理解するのに役立ちます。 たとえば、ネットワークの不具合によって、Azure にレプリケートされるすべてのマシンが影響を受ける可能性があります。 
- 1 台のサーバーで複数のレプリケーション エラーが発生する場合があります。 その場合、そのサーバーは、影響を受けるサーバーの一覧でエラーの症状ごとにカウントされます。 問題が解決すると、レプリケーション パラメーターは改善し、マシンからエラーがクリアされます。

## <a name="monitor-the-infrastructure"></a>インフラストラクチャの監視

**[インフラストラクチャ ビュー]** では、レプリケーションに関係するインフラストラクチャ コンポーネントと、サーバーと Azure サービス間の接続の正常性を監視します。

- 緑色の線は、接続が正常な状態であることを示します。
- エラー アイコンが重ねて表示されている赤い線は、接続性に影響するエラー症状が少なくとも 1 つ存在することを示します。
-  エラー アイコンをマウス ポインターでポイントすると、エラーと影響を受けるエンティティの数が表示されます。 影響を受けるエンティティのフィルター処理された一覧を表示するには、アイコンをクリックしてください。

    ![Site Recovery のインフラストラクチャ ビュー (コンテナー)](./media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

### <a name="tips-for-monitoring-the-infrastructure"></a>インフラストラクチャを監視するためのヒント

- オンプレミスのインフラストラクチャ コンポーネント (構成サーバー、プロセス サーバー、VMM サーバー、Hyper-V ホスト、VMware マシン) で、Site Recovery プロバイダーまたは Site Recovery エージェントの最新バージョンが実行されていることを確認してください。
- インフラストラクチャ ビューのすべての機能を使用するためには、これらのコンポーネントで[更新プログラムのロールアップ 22](https://support.microsoft.com/help/4072852) が実行されている必要があります。
- インフラストラクチャ ビューを使用するためには、実際の環境に合った適切なレプリケーション シナリオを選んでください。 ビューをドリルダウンすることで、より詳しい情報を把握できます。 表示されるシナリオを次の表に示します。

    **シナリオ** | **State**  | **表示可能**
    --- |--- | ---
    **オンプレミス サイト間のレプリケーション** | すべての状態 | いいえ 
    **Azure リージョン間の Azure VM レプリケーション**  | レプリケーション有効/初期レプリケーション進行中 | はい
    **Azure リージョン間の Azure VM レプリケーション** | フェールオーバー済み/フェールバック | いいえ   
    **Azure への VMware レプリケーション** | レプリケーション有効/初期レプリケーション進行中 | はい     
    **Azure への VMware レプリケーション** | フェールオーバー済み/フェールバック | いいえ      
    **Azure への Hyper-V のレプリケーション** | フェールオーバー済み/フェールバック | いいえ

- レプリケートするマシンが 1 つの場合にインフラストラクチャ ビューを表示するには、コンテナー メニューの **[レプリケートされたアイテム]** をクリックしてサーバーを選択します。  




## <a name="monitor-recovery-plans"></a>復旧計画の監視

**[復旧計画]** では、計画の数の監視、新しい計画の作成、および既存の計画の編集を行います。  

## <a name="monitor-jobs"></a>ジョブの監視

**[ジョブ]** では、Site Recovery 操作の状態を監視します。

- Azure Site Recovery のほとんどの操作は非同期的に実行され、操作の進行状況を追跡するために追跡ジョブが作成されて使用されます。 
- ジョブ オブジェクトは、操作の状態と進行状況を追跡するために必要なすべての情報を持っています。 

ジョブは次のように監視します。

1. ダッシュボードの **[ジョブ]** セクションでは、完了したジョブ、進行中のジョブ、入力待ちのジョブの概要を直近 24 時間について確認できます。 いずれかの状態をクリックすると、関連するジョブについてさらに詳しい情報を入手できます。
2. **[すべて表示]** をクリックすると、直近 24 時間のすべてのジョブが表示されます。

    > [!NOTE]
    > ジョブの情報には、コンテナー メニューの **[Site Recovery ジョブ]** からアクセスすることもできます。 

2. **[Site Recovery ジョブ]** リストに、ジョブの一覧が表示されます。 トップ メニューで、特定のジョブのエラーの詳細を把握したり、特定の条件でジョブの一覧をフィルター処理したり、選択されたジョブの詳細を Excel にエクスポートしたりすることができます。
3. ジョブをクリックすると、さらに詳しい情報を表示できます。 

## <a name="monitor-virtual-machines"></a>仮想マシンの監視

**[レプリケートされたアイテム]** には、レプリケートされたマシンの一覧が表示されます。 
    ![Site Recovery のレプリケートされたアイテムの一覧表示](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

2. 情報の表示とフィルター処理を行うことができます。 上部のアクション メニューでは、特定のマシンを対象に操作を実行することができます (テスト フェールオーバーの実行、特定のエラーの表示など)。
3. RPO、ターゲット構成の問題、レプリケーション エラーなど、他の列を表示するには、 **[列]** をクリックします。
4. レプリケーションの正常性、特定のレプリケーション ポリシーなど、特定のパラメーターに基づいて情報を表示するには、 **[フィルター]** をクリックします。
5. テスト フェールオーバーなどの操作を開始したり、関連付けられている特定のエラーの詳細を確認したりするには、その対象となるマシンを右クリックします。
6. いずれかのマシンをクリックすると、さらに詳しい情報が表示されます。 詳細には次のものが含まれます。
   - **レプリケーション情報**:マシンの現在の状態と正常性。
   - **RPO** (復旧ポイントの目標):仮想マシンの現在の RPO と、RPO が最後に計算された日時。
   - **復旧ポイント**:マシンの利用可能な最新の回復ポイント。
   - **フェールオーバーの準備**:対象のマシンに関してテスト フェールオーバーが実行されたかどうかや、マシン上で実行されているエージェントのバージョン (モビリティ サービスを実行しているマシンの場合)、構成上の問題を示します。
   - **エラー**:現在マシンで観察されているレプリケーション エラーの症状と考えられる原因/対策の一覧。
   - **イベント**:マシンに影響を与える最近のイベントの時系列での一覧。 エラーの詳細が、現在観察できるエラーの症状を示しているのに対し、イベントは、マシンに影響を与えた問題の履歴レコードになります。
   - **インフラストラクチャ ビュー**:Azure にマシンをレプリケートするシナリオに関して、インフラストラクチャの状態を表示します。

     ![Site Recovery のレプリケートされたアイテムの詳細/概要](./media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

## <a name="subscribe-to-email-notifications"></a>電子メール通知に登録する

次に示したような重大なイベントに関して、メール通知の受け取りを登録することができます。
 
- レプリケートされたマシンが重大な状態に陥った。
- オンプレミスのインフラストラクチャ コンポーネントと Site Recovery サービスとの間の接続が失われた。 コンテナーに登録されているオンプレミス サーバーと Site Recovery との間の接続は、ハートビート機構を使って検出されます。
- フェールオーバーに失敗した。

登録の手順は次のとおりです。

[コンテナー] の **[監視]** セクションで、 **[Azure Site Recovery イベント]** をクリックします。
1. **[メール通知]** をクリックします。
1. **[電子メールの通知]** で通知をオンにし、送信先を指定します。 すべてのサブスクリプション管理者に通知を送信できるほか、必要に応じて特定のメール アドレスを対象にすることもできます。

    ![メール通知](./media/site-recovery-monitor-and-troubleshoot/email.png)

## <a name="next-steps"></a>次のステップ

[Azure Monitor を使用した Site Recovery の監視](monitor-log-analytics.md)について確認します。
