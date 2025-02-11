---
title: Video Indexer エディターを使用してプロジェクトを作成し、ビデオ クリップを追加する
titleSuffix: Azure Media Services
description: このトピックでは、Video Indexer エディターを使用してプロジェクトを作成し、ビデオ クリップを追加する方法を示します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: 07e4b05e12a5994c707a171c5736aea04a9c9723
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632928"
---
# <a name="add-video-clips-to-your-projects"></a>ビデオ クリップをプロジェクトに追加する

[Video Indexer](https://www.videoindexer.ai/) Web サイトでは、ビデオの詳細な分析情報を使用して、適切なメディア コンテンツを検索したり、興味のある部分の位置を特定したり、結果を使用してまったく新しいプロジェクトを作成したりできます。 

作成したプロジェクトは、Video Indexer からレンダリングおよびダウンロードしたり、独自の編集アプリケーションまたは下流のワークフローで使用したりできます。

この機能が役立つかもしれないシナリオの例を次に示します。 

* 予告編のために映画のハイライトを作成する。
* ニュース放送で古いビデオ クリップを使用する。
* ソーシャル メディア用の短いコンテンツを作成する。

この記事では、プロジェクトを作成し、ビデオから選択したクリップをプロジェクトに追加する方法について説明します。 

## <a name="create-new-project-and-manage-videos"></a>新しいプロジェクトを作成してビデオを管理する

1. [Video Indexer](https://www.videoindexer.ai/) Web サイトに移動してサインインします。
1. **[プロジェクト]** タブを選択します。以前にプロジェクトを作成したことがある場合、他のすべてのプロジェクトがここに表示されます。
1. **[新しいプロジェクトの作成]** をクリックします。  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="新しいプロジェクトを作成する":::
1. 鉛筆アイコンをクリックして、プロジェクトに名前を付けます。 「Untitled project」というテキストをプロジェクト名に置き換えて、チェックマークをクリックします。

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="新しいプロジェクト":::
    
### <a name="add-videos-to-the-project"></a>ビデオをプロジェクトに追加する

> [!NOTE]
> 現時点では、プロジェクトには同じ言語でインデックス付けされたビデオしか含めることができません。 </br>ある言語のビデオを選択した後に、別の言語のビデオを自分のアカウントに追加することはできません。他の言語のビデオはグレー表示または無効になります。

1. **[ビデオを追加する]** を選択して、このプロジェクトで作業するビデオを追加します。

    アカウントのすべてのビデオと、[テキスト、キーワード、ビジュアル コンテンツの検索] という検索ボックスが表示されます。 指定された人物、ラベル、ブランド、キーワード、またはオカレンスが音声テキストおよび OCR に含まれるビデオを検索できます。
    
    たとえば、次の図では、"カスタム ビジョン" について言及しているビデオを音声テキストでのみ検索しています (検索結果をフィルター処理する場合は **[フィルター]** を使用します)。
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="カスタム ビジョンについて言及しているビデオの検索を示すスクリーンショット":::
1. **[追加]** をクリックして、ビデオをプロジェクトに追加します。
1. これで、選択したすべてのビデオが表示されます。 これらのビデオから、プロジェクトで使用するクリップを選択します。

    ドラッグ アンド ドロップによって、またはリスト メニュー ボタンを選択して **[下へ移動]** または **[上へ移動]** を選択することによって、ビデオの順序を変更することができます。 リスト メニューでは、このプロジェクトからビデオを削除することもできます。 
    
    **[ビデオを追加する]** を選択すると、いつでもこのプロジェクトにビデオを追加できます。 同じビデオの複数のオカレンスをプロジェクトに追加することもできます。 これを行うのは、あるビデオのクリップ、別のビデオのクリップ、そして最初のビデオの別のクリップという順序で表示したい場合です。 

### <a name="select-clips-to-use-in-your-project"></a>プロジェクトで使用するクリップを選択する

各ビデオの右側にある下向き矢印をクリックすると、タイムスタンプ (ビデオのクリップ) に基づいてビデオの分析情報が開きます。 

1. 特定のクリップのクエリを作成するには、[トランスクリプト、ビジュアル テキスト、人物、およびラベルを検索してください] 検索ボックスを使用します。
1. 表示する分析情報と表示しない分析情報をカスタマイズするには、 **[分析情報の表示]** を選択します。 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="&quot;Try cognitive services&quot; というビデオの検索を示すスクリーンショット":::
1. **[フィルター オプション]** を選択して、探しているシーンの詳細をさらに指定するためのフィルターを追加します。

    複数のフィルターを追加できます。 
1. 満足のいく結果が得られたら、追加するセグメントを選択して、このプロジェクトに追加するクリップを選択します。 セグメントをもう一度クリックすると、このクリップを選択解除できます。
    
    ビデオのすべてのセグメント (または、検索後に返されたすべてのもの) を追加するには、ビデオの横にあるリスト メニュー オプションをクリックして、 **[すべて選択]** を選択します。 

クリップを選択したり並べ替えたりしながら、ページの右側にあるプレーヤーでビデオをプレビューできます。 

> [!IMPORTANT]
> 変更を加えたら、ページ上部にある **[プロジェクトの保存]** を選択して、必ずプロジェクトを保存してください。 

### <a name="render-and-download-the-project"></a>プロジェクトのレンダリングとダウンロード

> [!NOTE]
> Video Indexer の有料アカウントの場合、プロジェクトのレンダリングにはエンコード コストがかかります。 Video Indexer の試用版アカウントは、レンダリングが 5 時間までに制限されています。

1. 終了したら、プロジェクトが保存されていることを確認します。 このプロジェクトをレンダリングできるようになりました。 **[レンダー]** をクリックすると、Video indexer がファイルをレンダリングした後、ダウンロード リンクが電子メールに送信されることを知らせるポップアップ ダイアログが表示されます。 [続行] を選択します。 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="プロジェクトのレンダリングとダウンロードのオプションがある Video Indexer を示すスクリーンショット":::
    
    プロジェクトのレンダリング中という通知も画面上部に表示されます。 レンダリングが完了すると、プロジェクトが正しくレンダリングされたという新しい通知が表示されます。 通知をクリックしてプロジェクトをダウンロードします。 mp4 形式でプロジェクトがダウンロードされます。
1. 保存されたプロジェクトには **[プロジェクト]** タブからアクセスできます。 

    このプロジェクトを選択すると、このプロジェクトのすべての分析情報とタイムラインが表示されます。 **[ビデオ エディター]** を選択した場合、このプロジェクトの編集を続けることができます。 編集には、ビデオやクリップの追加または削除、プロジェクトの名前変更などが含まれます。
    
## <a name="create-a-project-from-your-video"></a>ビデオからプロジェクトを作成する

アカウント内のビデオから直接、新しいプロジェクトを作成できます。 

1. Video Indexer Web サイトの **[ライブラリ]** タブに移動します。
1. プロジェクトの作成に使用するビデオを開きます。 分析情報とタイムラインのページで、 **[ビデオ エディター]** ボタンを選択します。

    新しいプロジェクトの作成に使用したものと同じページに移動します。 新しいプロジェクトとは異なり、以前に編集を開始した、ビデオのタイムスタンプ付き分析情報セグメントが表示されます。

## <a name="see-also"></a>関連項目

[Video Indexer の概要](video-indexer-overview.md)

