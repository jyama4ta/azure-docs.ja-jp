---
title: StorSimple Virtual Array のバックアップを複製する | Microsoft Docs
description: バックアップから複製し、StorSimple Virtual Array からファイルを回復する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: af6e979c-55e3-477c-b53e-a76a697f80c9
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2016
ms.author: alkohli
ms.openlocfilehash: b6cc89cb082cd0ed32abd88e3a6683c60a27ba90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023042"
---
# <a name="clone-from-a-backup-of-your-storsimple-virtual-array"></a>StorSimple Virtual Array のバックアップから複製する

## <a name="overview"></a>概要

この記事では、Microsoft Azure StorSimple Virtual Array の共有またはボリュームのバックアップ セットから複製する方法について手順を追って説明します。 複製されたバックアップを使用して、削除されたファイルや消失したファイルを回復します。 また、ファイル サーバーとして構成された StorSimple Virtual Array で、項目レベルの回復を実行する手順についても詳しく説明します。

## <a name="clone-shares-from-a-backup-set"></a>バックアップ セットから共有を複製する

**共有を複製する前に、デバイスにこの操作を実行できるだけの十分な領域があることを確認してください。** バックアップから複製するには、[Azure Portal](https://portal.azure.com/) で次の手順を実行します。

#### <a name="to-clone-a-share"></a>共有を複製するには

1. **[デバイス]** ブレードに移動します。 デバイスを選択してクリックし、 **[共有]** をクリックします。 複製する共有を選択し、共有を右クリックして、コンテキスト メニューを呼び出します。 **[複製]** を選択します。
   
   ![バックアップの作成](./media/storsimple-virtual-array-clone/cloneshare1.png)
2. **[複製]** ブレードで **[バックアップ] > [選択]** の順にクリックし、次の操作を行います。 
   
   a.    時間範囲に基づいて、このデバイスのバックアップをフィルター処理します。 **[過去 7 日間]** 、 **[過去 30 日間]** 、および **[過去 1 年]** から選択します。
   
   b.    フィルター処理されたバックアップの一覧から、複製元のバックアップを選択します。
   
   c.    **[OK]** をクリックします。
   
   ![バックアップの作成 2](./media/storsimple-virtual-array-clone/cloneshare3.png)
3. **[複製]** ブレードで **[ターゲット設定]** をクリックし、次の操作を行います。
   
   a.    共有名を指定します。 共有名は、3 ～ 127 文字で指定する必要があります。
   
   b.    必要に応じて、複製された共有の説明を入力します。
   
   c.    復元先の共有の種類は変更できません。 階層化共有は階層化共有として、ローカル固定共有はローカル固定共有として複製されます。
   
   d.    容量のサイズは、複製元の共有と同じに設定されます。
   
   e.    この共有の管理者を割り当てます。 複製が完了したら、ファイル エクスプローラーを使用して共有のプロパティを変更できます。
   
   f.    **[OK]** をクリックします。
   
   ![バックアップの作成 3](./media/storsimple-virtual-array-clone/cloneshare6.png)

4. **[複製]** をクリックして、複製ジョブを開始します。 ジョブが完了したら、複製操作が開始され、その旨が通知されます。 複製の進行状況を監視するには、 **[ジョブ]** ブレードに移動し、詳細を表示するジョブをクリックします。
5. 複製が正常に作成されたら、デバイスで **[共有]** ブレードに戻ります。
6. デバイスの共有の一覧に、複製された新しい共有が表示されます。 階層化共有は階層化共有として、ローカル固定共有はローカル固定共有として複製されます。
   
   ![バックアップの作成 4](./media/storsimple-virtual-array-clone/cloneshare10.png)

## <a name="clone-volumes-from-a-backup-set"></a>バックアップ セットからボリュームを複製する

バックアップから複製するには、Azure Portal で、共有を複製するときと同様の手順を実行する必要があります。 複製操作では、同じ仮想デバイス上の新しいボリュームにバックアップを複製します。別のデバイスに複製することはできません。

#### <a name="to-clone-a-volume"></a>ボリュームを複製するには

1. **[デバイス]** ブレードに移動します。 デバイスを選択してクリックし、 **[ボリューム]** をクリックします。 複製するボリュームを選択し、ボリュームを右クリックしてコンテキスト メニューを呼び出します。 **[複製]** を選択します。
   
   ![ボリュームを複製する](./media/storsimple-virtual-array-clone/clonevolume1.png)
2. **[複製]** ブレードで **[バックアップ]** をクリックし、次の操作を行います。 
   
   a.    時間範囲に基づいて、このデバイスのバックアップをフィルター処理します。 **[過去 7 日間]** 、 **[過去 30 日間]** 、および **[過去 1 年]** から選択します。 
   
   b.    フィルター処理されたバックアップの一覧から、複製元のバックアップを選択します。
   
   c.    **[OK]** をクリックします。
   
   ![ボリュームを複製する 2](./media/storsimple-virtual-array-clone/clonevolume3.png)
3. **[複製]** ブレードで **[ターゲット ボリューム設定]** をクリックし、次の操作を行います。
   
   a. デバイス名は自動的に設定されます。
   
   b. **複製されたボリューム** のボリューム名を指定します。 ボリューム名は、3 ～ 127 文字で指定する必要があります。
   
   c. ボリュームの種類は、元のボリュームに自動的に設定されます。 階層化ボリュームは階層化ボリュームとして、ローカル固定ボリュームはローカル固定ボリュームとして複製されます。
   
   d. **[接続済みのホスト]** で **[選択]** をクリックします。
   
   ![ボリュームを複製する 3](./media/storsimple-virtual-array-clone/clonevolume4.png)
4. **[接続済みホスト]** ブレードで、既存の ACR から選択するか、新しい ACR を追加します。 新しい ACR を追加するには、ACR 名とホスト IQN を指定する必要があります。 **[選択]** をクリックします。
   
   ![ボリュームを複製する 4](./media/storsimple-virtual-array-clone/clonevolume5.png)
5. **[複製]** をクリックして、複製ジョブを開始します。
   
   ![ボリュームを複製する 5](./media/storsimple-virtual-array-clone/clonevolume6.png)  
6. 複製ジョブを作成すると、複製が開始されます。 作成された複製は、デバイス上の [ボリューム] ブレードに表示されます。 階層化ボリュームは階層化ボリュームとして、ローカル固定ボリュームはローカル固定ボリュームとして複製されることに注意してください。
   
   ![ボリュームを複製する 6](./media/storsimple-virtual-array-clone/clonevolume8.png)
7. ボリュームの一覧にボリュームが [オンライン] と表示されたら、ボリュームは使用可能です。 iSCSI イニシエーターのホストで、iSCSI イニシエーターのプロパティ ウィンドウにあるターゲットの一覧を更新します。 複製されたボリューム名を含む新しいターゲットが、[状態] 列に "非アクテイブ" として表示されます。
8. ターゲットを選択し、 **[接続]** をクリック します。 イニシエーターがターゲットに接続されると、状態が **[接続]** に変わります。
9. 次の図に示すように、マウントされているボリュームが **[ディスクの管理]** ウィンドウに表示されます。 検出されたボリュームを右クリックし (ディスク名をクリック)、 **[オンライン]** をクリックします。

> [!IMPORTANT]
> バックアップ セットからボリュームまたは共有を複製するとき、複製ジョブが失敗したのに、ポータルでターゲットのボリュームまたは共有が作成されることがあります。 将来、これに起因して発生する問題を最小限に抑えるために、このターゲット ボリュームまたは共有を削除することが重要です。
> 
> 

## <a name="item-level-recovery-ilr"></a>項目レベルの回復 (ILR)

このリリースでは、ファイル サーバーとして構成されている StorSimple Virtual Array での項目レベルの回復 (ILR) が導入されています。 この機能を使用すると、StorSimple デバイス上のすべての共有のクラウド バックアップから、ファイルとフォルダーの細かいレベルでの回復を実行できます。 セルフサービス モデルを使用して、削除されたファイルを最近のバックアップから取得できます。

各共有には、最新のバックアップが格納された *.backups* フォルダーがあります。 目的のバックアップに移動し、該当するファイルとフォルダーをバックアップからコピーして復元できます。 この機能により、バックアップからファイルを復元するときに管理者に依頼する必要はなくなります。

1. ILR を実行するときは、ファイル エクスプローラーを使用してバックアップを表示できます。 バックアップを調べる共有をクリックします。 共有の下で *.backups* フォルダーが作成されていることを確認できます。このフォルダーにすべてのバックアップが保存されています。 *.backups* フォルダーを展開してバックアップを表示します。 このフォルダーには、バックアップ階層全体の展開ビューが表示されます。 このビューはオンデマンドで作成されます。通常、作成には数秒しかかかりません。
   
   最後の 5 つのバックアップは、この方法で表示され、項目レベルの回復を実行するために使用できます。 これには、既定のスケジュールされたバックアップと手動バックアップの両方が含まれます。
   
   * **スケジュールされたバックアップ** には、&lt;デバイス名&gt;DailySchedule-YYYYMMDD-HHMMSS-UTC の形式で名前が付けられています。
   * **手動バックアップ** には、Ad-hoc-YYYYMMDD-HHMMSS-UTC の形式で名前が付けられています。
     
     ![.backups フォルダーが表示されているファイル エクスプローラーのスクリーンショット。 そのフォルダー内で、手動バックアップ フォルダーが選択されています。](./media/storsimple-virtual-array-clone/image14.png)

2. 削除されたファイルの最新バージョンが含まれたバックアップを特定します。 前の例ではフォルダー名にそれぞれの UTC タイムスタンプが含まれていますが、フォルダーの作成時刻は、バックアップが開始されたときの実際のデバイスの時刻です。 フォルダーのタイムスタンプを使用してバックアップを検索し、特定します。

3. 前の手順で特定したバックアップで、復元するフォルダーまたはファイルを見つけます。 表示できるのは、アクセス許可が付与されているファイルまたはフォルダーだけです。 特定のファイルまたはフォルダーにアクセスできない場合は、共有管理者にお問い合わせください。 管理者はファイル エクスプローラーを使用して共有アクセス許可を編集し、ユーザーが特定のファイルまたはフォルダーにアクセスできるようにします。 ベスト プラクティスとして、1 人のユーザーではなく、ユーザー グループが共有管理者になることをお勧めします。

4. ファイルまたはフォルダーを、StorSimple ファイル サーバー上の適切な共有にコピーします。

## <a name="next-steps"></a>次のステップ

[ローカル Web UI を使用して StorSimple Virtual Array を管理する](storsimple-ova-web-ui-admin.md)方法の詳細を確認します。

