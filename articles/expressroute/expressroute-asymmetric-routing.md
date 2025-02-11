---
title: 'Azure ExpressRoute: 非対称ルーティング'
description: この記事では、送信先へのリンクが複数あるネットワーク内の非対称ルーティングに関して発生する可能性がある問題について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 0713c52c7f07db93d9ae9084062ef2c3b25a9074
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97560510"
---
# <a name="asymmetric-routing-with-multiple-network-paths"></a>複数のネットワーク パスを使用した非対称ルーティング
この記事では、ネットワークの送信元と送信先の間で利用できるルートが複数存在する場合に、ネットワーク トラフィックが異なるパスを取る場合がある理由について説明します。

非対称ルーティングを理解するために知っておく必要がある概念が 2 つあります。 1 つ目は、複数のネットワーク パスの影響です。 もう 1 つは、ファイアウォールなどのデバイスが状態を維持する方法です。 これらの種類のデバイスはステートフル デバイスと呼ばれます。 これら 2 つの要素を組み合わせると、ステートフル デバイスによってネットワーク トラフィックがドロップされるシナリオを作成できます。  トラフィックがドロップされるのは、そのトラフィックがデバイス自体から発生したことを検出しなかったためです。

## <a name="multiple-network-paths"></a>複数のネットワーク パス
インターネット サービス プロバイダーを介したインターネットへのリンクがエンタープライズ ネットワークに 1 つしかない場合、インターネットとの間のトラフィックの往来はすべて同じパスを経由して行われます。 企業で、複数の回線を調達し冗長パスを構成して、ネットワークのアップタイム向上を図ることはよくあることです。 この種類の構成の場合、トラフィックがインターネットに送信されるときのリンクと、返されるときのリンクが異なることがあります。 このシナリオが一般に非対称ルーティングと呼ばれます。 非対称ルーティングでは、戻りのネットワーク トラフィックは元の送信フローとは異なるパスを経由します。

![Network with multiple paths](./media/expressroute-asymmetric-routing/AsymmetricRouting3.png)

ただし、非対称ルーティングは通常、インターネットにアクセスするときに発生します。 また、複数のパスを組み合わせて使用する場合にも発生します。 最初の例は、インターネット パスがあり、同じ宛先に向かうプライベート パスがある場合です。 2 番目の例は、やはり同じ宛先に向かう複数のプライベート パスがある場合です。

送信元と送信先の間のパスにある各ルーターは、送信先に至る最適なパスを計算します。 ルーターによる最適と思われるパスの決定は、主に次の 2 つの要因に基づきます。

* 外部ネットワーク間のルーティングは、ボーダー ゲートウェイ プロトコル (BGP) というルーティング プロトコルに基づきます。 BGP では、アドバタイズメントが近隣ノードから取得され、一連の段階を経てそれらが実行されることで、目的の送信先までの最適なパスが決定されます。 その後、最適なパスはルーティング テーブルに格納されます。
* ルートに関連付けられたサブネット マスクの長さは、ルーティング パスに影響します。 同じ IP アドレスの複数のアドバタイズメントを受信した場合、ルーターはサブネット マスクが長い方のパスを選択します。これは、こちらの方がより具体的なルートであると判断されるためです。

## <a name="stateful-devices"></a>ステートフル デバイス
ルーターは、ルーティングを行うためにパケットの IP ヘッダーを確認します。 いくつかのデバイスは、パケットをより詳細に確認します。 これらのデバイスは通常、レイヤー 4 - 伝送制御プロトコル (TCP) またはユーザー データグラム プロトコル (UDP) のヘッダー、またはレイヤー 7 (アプリケーション レイヤー) のヘッダーまで確認します。 この種のデバイスは、セキュリティ デバイスと帯域幅最適化デバイスのいずれかです。 

たとえば、一般的なステートフル デバイスとしてファイアウォールが挙げられます。 ファイアウォールは、さまざまな条件に基づいて、インターフェイスを通過するパケットを許可または拒否します。 これらの条件は、プロトコル、TCP/UDP ポート、URL ヘッダーなどが含まれますが、これらに限定されるわけではありません。 このレベルのパケット インスペクションでは、デバイスに大きな処理負荷がかかる場合があります。 

パフォーマンスを向上させるために、ファイアウォールではフローの最初のパケットが検査されます。 ファイアウォールでパケットがインターフェイスの通過を許可されると、その状態テーブルにフロー情報が保持されます。 このフローに関連するその後のパケットはすべて、最初の決定に基づいて許可されます。 既存のフローの一部であるパケットは、そのパケットの送信元ではないファイアウォールに到達することがあります。 最初のフローに関する事前の状態情報がないので、このパケットはファイアウォールによってドロップされます。

## <a name="asymmetric-routing-with-expressroute"></a>ExpressRoute を使用した非対称ルーティング
Azure ExpressRoute 経由で Microsoft に接続すると、ネットワークは次のように変更されます。

* Microsoft へのリンクが複数になります。 1 つは既存のインターネット接続で、もう 1 つは ExpressRoute 接続を介したものです。 Microsoft 宛ての特定のトラフィックは、インターネット接続を通過しますが、ExpressRoute 接続で返されることがあります。 これは、トラフィックが ExpressRoute を経由するが、インターネット パスで返されるときにも発生する可能性があります。
* ExpressRoute 回線からより具体的な IP アドレスを受信しました。 そのため、ExpressRoute 経由で提供されるサービスに対してネットワークからのトラフィックが Microsoft に送信される場合、ルーターは常に ExpressRoute 接続を優先します。

これらの 2 つの変更がネットワークに与える影響を把握するために、いくつかのシナリオについて考えてみましょう。 たとえば、インターネットへの回線が 1 つあり、Microsoft のサービスをすべてインターネット経由で利用しているとします。 ユーザーのネットワークから Microsoft へと送信され戻ってくるトラフィックは、同じインターネット リンクを経由し、ファイアウォールを通過します。 ファイアウォールは、最初のパケットを確認したときにフローを記録します。 そのメッセージ交換の後続のパケットはすべて、状態テーブルにフローが存在するので許可されます。

![ExpressRoute を使用した非対称ルーティング](./media/expressroute-asymmetric-routing/AsymmetricRouting1.png)

次に ExpressRoute 回路を有効にし、Microsoft から提供されるサービスを ExpressRoute を介して利用します。 Microsoft の他のサービスはすべてインターネットを介して利用します。 ExpressRoute 接続に接続されている境界に別のファイアウォールをデプロイします。 特定のサービスについて、Microsoft によってより具体的なプレフィックスが ExpressRoute 経由でネットワークにアドバタイズされます。 ルーティング インフラストラクチャは、これらのプレフィックスの優先パスとして ExpressRoute を選択します。 

パブリック IP アドレスを ExpressRoute 経由で Microsoft にアドバタイズしない場合。 Microsoft は、インターネット経由でパブリック IP アドレスと通信します。 ネットワークから Microsoft に送信されるトラフィックでは ExpressRoute 接続を使用しますが、Microsoft から返されるトラフィックではインターネット パスを使用します。 境界のファイアウォールが認識していないフローの応答パケットを検出すると、それらのパケットがドロップされます。

ExpressRoute とインターネットに同じネットワーク アドレス変換 (NAT) プールをアドバタイズすることにした場合。 ネットワーク内のクライアントでプライベート IP アドレスに関する同様の問題が発生します。 Windows Update などのサービスに対する要求は、これらのサービスの IP アドレスが ExpressRoute 経由でアドバタイズされていないため、インターネット経由で送信されます。 ただし、戻りトラフィックは、ExpressRoute 経由で返されます。 Microsoft はインターネットと ExpressRoute から同じサブネット マスクの IP アドレスを受信したので、優先されるパスは常に ExpressRoute になります。 ExpressRoute 接続に接しているネットワーク境界にあるファイアウォールや別のステートフル デバイスに、フローに関する情報がない場合、これらのパケットはドロップされます。

## <a name="asymmetric-routing-solutions"></a>非対称ルーティングの解決策
非対称ルーティングの問題を解決するには、2 つの方法を利用できます。 1 つ目はルーティングによるもので、2 つ目は送信元ベースの NAT (SNAT) を使用するものです。

### <a name="routing"></a>ルーティング
必ず、適切なワイド エリア ネットワーク (WAN) リンクに、パブリック IP アドレスをアドバタイズするようにしてください。 たとえば、認証トラフィックにはインターネットを使用し、電子メール トラフィックには ExpressRoute を使用する場合です。 Active Directory フェデレーション サービス (AD FS) パブリック IP アドレスを ExpressRoute 経由でアドバタイズしないでください。 同様に、ルーターが ExpressRoute 経由で受信する IP アドレスに、オンプレミスの AD FS サーバーを公開しないようにしてください。 ExpressRoute 経由で受け取ったルートはより具体的であるため、Microsoft への認証トラフィックのパスとして ExpressRoute が優先されます。 ネットワークでルーティングがどのように行われるかに注意を払っていない場合は、非対称ルーティングの問題が発生する可能性があります。

ExpressRoute を認証に使用する場合は、NAT を使用せずに ExpressRoute 経由で AD FS パブリック IP アドレスをアドバタイズしていることを確認してください。 このように設定すると、Microsoft から送信されオンプレミスの AD FS サーバーに向かうトラフィックは、ExpressRoute を経由します。 ネットワークから Microsoft に戻るトラフィックには ExpressRoute が使用されます。これは、ExpressRoute がインターネットより優先されるルートであるためです。

### <a name="source-based-nat"></a>送信元ベースの NAT
非対称ルーティングの問題を解決するもう 1 つの方法は、SNAT を使用するものです。 たとえば、オンプレミスの簡易メール転送プロトコル (SMTP) サーバーのパブリック IP アドレスを ExpressRoute 経由でアドバタイズしないように選択したとします。 代わりに、この種の通信にインターネットを使用するつもりです。 Microsoft からオンプレミスの SMTP サーバーに送信される要求はインターネットを経由します。 SNAT を使用して受信要求を内部 IP アドレスに変換します。 SMTP サーバーからの戻りトラフィックは、ExpressRoute を経由せず、(NAT に使用する) 境界ファイアウォールに届きます。 その結果、戻りトラフィックはインターネット パスを使用します。

![Source-based NAT network configuration](./media/expressroute-asymmetric-routing/AsymmetricRouting2.png)

## <a name="asymmetric-routing-detection"></a>非対称ルーティングの検出
traceroute は、ネットワーク トラフィックが予想されるパスを経由していることを確認するうえで、最善の手段です。 オンプレミスの SMTP サーバーから Microsoft に送信されるトラフィックがインターネット パスを経由することが予想される場合、予想される traceroute は SMTP サーバーから Microsoft 365 までです。 その結果から、トラフィックが実際にユーザーのネットワークから (ExpressRoute ではなく) インターネットに向かって送信されていることが確認できます。

