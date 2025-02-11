---
title: Azure AD アプリケーション プロキシへのアップグレード | Microsoft Docs
description: Microsoft Forefront または Unified Access Gateway からアップグレードする場合に最適なプロキシ ソリューションを選択します。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7d089d8f57bf8ef1278d262129cc8ddfc81d581
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257745"
---
# <a name="compare-remote-access-solutions"></a>リモート アクセス ソリューションを比較します

Azure Active Directory アプリケーション プロキシは、Microsoft が提供する 2 つのリモート アクセス ソリューションの 1 つです。 もう 1 つは、オンプレミスのバージョンである Web アプリケーション プロキシです。 これら 2 つのソリューションは、Microsoft がこれまで提供していた Microsoft Forefront Threat Management Gateway (TMG) と Unified Access Gateway (UAG) の後継となるソリューションです。 この記事で、これら 4 つのソリューションを相互に比較する方法を把握できます。 非推奨の TMG または UAG ソリューションを使用しているユーザーは、この記事を活用して、どちらかのアプリケーション プロキシへの移行を計画してください。 


## <a name="feature-comparison"></a>機能の比較

この表を使用して、Threat Management Gateway (TMG)、Unified Access Gateway (UAG)、Web アプリケーション プロキシ (WAP)、および Azure AD アプリケーション プロキシ (AP) を相互に比較する方法を把握します。

| 機能 | TMG | UAG | WAP | AP |
| ------- | --- | --- | --- | --- |
| 証明書の認証 | はい | はい | - | - |
| ブラウザー アプリの選択的な発行 | はい | はい | はい | はい |
| 事前認証とシングル サインオン | はい | はい | はい | はい | 
| レイヤー 2/3 ファイアウォール | はい | はい | - | - |
| 転送プロキシ機能 | はい | - | - | - |
| VPN 機能 | はい | はい | - | - |
| プロトコルの豊富なサポート | - | はい | HTTP 経由で実行される場合はあり | HTTP 経由でまたはリモート デスクトップ ゲートウェイ経由で実行する場合はあり |
| ADFS プロキシ サーバーとしてのサーバー | - | はい | はい | - |
| アプリケーションにアクセスするための 1 つのポータル | - | はい | - | はい |
| 応答本文のリンク変換 | はい | はい | - | はい | 
| ヘッダーによる認証 | - | はい | - | あり。PingAccess を使用 | 
| クラウド規模のセキュリティ | - | - | - | はい | 
| 条件付きアクセス | - | はい | - | はい |
| 非武装地帯 (DMZ) にコンポーネントが存在しない | - | - | - | はい |
| 受信接続がない | - | - | - | はい |

ほとんどのシナリオでは、最新のソリューションとして Azure AD アプリケーション プロキシをお勧めします。 Web アプリケーション プロキシは AD FS 用のプロキシ サーバーを必要とするシナリオにのみ推奨され、Azure Active Directory のカスタム ドメインを使用することはできません。 

類似製品に比べ、Azure AD アプリケーション プロキシには、次に示す独自のメリットがあります。

- オンプレミスのリソースへの Azure AD の拡張
   - クラウド規模のセキュリティと保護
   - 簡単に有効にできる条件付きアクセスや Multi-Factor Authentication などの機能
- 非武装地帯 (DMZ) にコンポーネントが存在しない
- 受信接続は不要
- ユーザーがすべてのアプリケーション (Microsoft 365、Azure AD に統合された SaaS アプリ、オンプレミスの Web アプリなど) に移動できる 1 つの [マイ アプリ] ページ。 


## <a name="next-steps"></a>次のステップ

- [Azure AD アプリケーション プロキシを使用してセキュリティで保護されたオンプレミス アプリケーションへのリモート アクセスを提供する](application-proxy.md)
