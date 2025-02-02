# nostr - リレーによる送信されるメモやその他のもの
> Notes and Other Stuff Transmitted by Relays

一度限りの検閲抵抗性のあるグローバルな「ソーシャル」ネットワークを作成できる、最もシンプルなオープンプロトコル。

信頼できる中央サーバーに依存しないため、強靱性があります。暗号鍵と署名に基づいているため、改竄防止性があります。P2P技術に依存しないため、動作します。

これは進行中の作業です。[Telegramグループに参加してください！](https://t.me/nostr_protocol)

## それがどのように機能するかの非常に簡単な要約、それ以外を読む予定がない場合：

誰もがクライアントを実行します。ネイティブクライアント、Webクライアントなどがあります。投稿するには、投稿を書き、キーで署名し、複数のリレー（他人または自分がホストするサーバー）に送信します。他の人から更新を取得するには、複数のリレーにその他の人物について何か知っているかどうか尋ねます。誰でもリレーを実行できます。リレーは非常にシンプルで愚かです。何もしないで、一部の人から投稿を受け取り、他の人に転送します。リレーは信頼される必要はありません。署名はクライアント側で検証されます。

[Nostrの使用を開始する方法](https://github.com/vishalxl/nostr_console/discussions/31)

[Nostrクライアントの機能比較](https://github.com/vishalxl/Nostr-Clients-Features-List/blob/main/Readme.md)

[Nostrで構築されたプロジェクトのリスト](https://github.com/aljazceru/awesome-nostr)

## これが必要な理由は、他の解決策が壊れているためです：

### Twitterの問題

- Twitterには広告があります。
- Twitterはあなたを中毒にさせる奇妙な技術を使っています。
- Twitterは、フォローしている人々の実際の履歴的フィードを表示しません。
- Twitterは人々を禁止します。
- Twitterは人々をシャドウバンします。
- Twitterには多くのスパムがあります。

### Mastodonや類似のプログラムの問題点

- ユーザーのアイデンティティは、第三者によって制御されるドメイン名に紐づけられている。
- サーバーのオーナーは、Twitterと同様にあなたを禁止することができる。サーバーのオーナーは他のサーバーをブロックすることもできる。
- サーバー間の移行は、サーバーが協力しない限り後回しにされ、敵対的な環境では機能しない（全てのフォロワーが失われる）。
- サーバーを運営するための明確なインセンティブが存在しないため、サーバーは愛好家やクールなドメインに名前を付けたい人たちによって運営される傾向があります。そして、ユーザーは一人の専制主義者の支配下に置かれ、しばしばTwitterのような大企業のそれよりも悪くなり、移動することができません。
- サーバーが素人的に運営される傾向があるため、しばしば放棄されることがあります。これは事実上、全員を禁止するのと同じです。
- 全てのサーバーからの更新を痛み分け（そして保存！）しなければならない場合、大量のサーバーを持つ意味がありません。この点は、サーバーが膨大な数存在する傾向があるため、より多くのデータをより頻繁により多くの場所に渡す必要があるため、悪化しています。
- 動画共有の具体例として、ActivityPubの愛好家たちは、テキストノートのようにサーバー間でビデオを完全に伝送することは完全に不可能であることに気づき、ビデオを投稿した単一のインスタンスからのみホストされるようにすることに決めました。これはNostrのアプローチに似ています。

### SSB (Secure Scuttlebutt)の問題点

- 大きな問題はないと思います。とても素晴らしいと思います。私はこれを基にしていましたが、
- このプロトコルは、オープンプロトコルになることは全く考慮されていないため、ある特定の問題を解決するために急いでJavaScriptで書かれ、それが拡大されたため、不必要で奇妙なクセがある、JSON文字列の署名がECMA-262第6版の規則に厳密に従わなければならないなどの問題があります。
- 単一ユーザーからの更新チェーンを持つようにインサイトを持っているため、私にとっては不必要であり、物事に膨張性と剛性を加えるもののように感じられます - 各サーバー/ユーザーは新しいものが有効であることを確認するために、すべての投稿のチェーンを格納する必要があります。なぜ？（良い理由があるかもしれません）
- Nostrほど単純ではありませんが、P2P同期を主な目的として作られ、 "pubs" は後付けされたものであるため、SSBをこのカスタムプロトコルの代わりに使用し、クライアントリレーサーバーモデルに適応させることを検討する価値があります。標準を再利用する方が、新しい標準に人々を移行させるよりも常に良いからです。

### 他のソリューションの問題点

- すべての人が自分自身のサーバーを実行する必要があります。
- ドメイン名が検閲されるため、これらの中でまだ検閲される可能性があります。

## Nostrはどのように機能しますか？

- 2つのコンポーネント、__クライアント__と__リレー__があります。各ユーザーはクライアントを実行します。誰でもリレーを実行できます。
- すべてのユーザーは公開鍵によって識別されます。すべての投稿に署名があります。すべてのクライアントはこれらの署名を検証します。
- クライアントは、自分が選択したリレーからデータを取得し、自分が選択した他のリレーにデータを公開します。リレーは他のリレーとは話しません。直接ユーザーと通信します。
- たとえば、誰かを「フォロー」する場合、ユーザーは自分のクライアントに対して、その公開鍵からの投稿をクエリするよう指示するだけです。
- クライアントを起動すると、ユーザーがクロノロジカルに表示されるよう、クライアントはフォローするすべてのユーザーのすべてのリレーからデータを取得します（たとえば、最後の1日間のすべての更新）。。
- 「投稿」には、任意の種類の構造化データを含めることができますが、最も使用されるものは標準に組み込まれるため、すべてのクライアントとリレーでシームレスに処理できます。

## どのようにして、Nostrは、上記のネットワークが解決できない問題を解決するのですか？

- **ユーザーの禁止およびサーバーの閉鎖**
  - リレーは、そこに何も公開しないようにユーザーをブロックすることができますが、これは彼らが他のリレーに公開することができるため、彼らに影響を与えません。ユーザーは公開鍵によって識別されるため、禁止されたときにアイデンティティとフォロワーベースを失うことはありません。
  - 新しいリレーのアドレスを手動で入力する必要はありません（ただし、これはサポートされる必要があります）。フォローしている誰かがサーバーの推奨事項を投稿した場合、クライアントは自動的にそのリストに追加する必要があります。
  - データを公開するためにリレーを使用している人が、別のリレーに移行したい場合は、以前のリレーにサーバーの推奨事項を公開して、移行先に移動することができます。
  - 多くのリレーから禁止されて、サーバーの推奨事項を放送できなくなった場合、親しい友人には、どのリレーで投稿しているかを別の手段で知らせることができます。そうすることで、これらの親しい友人は、新しいリレーにサーバーの推奨事項を公開でき、徐々に禁止されたユーザーのフォロワーが、新しいリレーから彼らの投稿を再び見つけ始めます。
  - 上記のすべては、リレーが操作を停止した場合にも有効です。

- **検閲への抵抗**
  - 各ユーザーは、任意の数のリレーに自分のアップデートを投稿することができます。
  - リレーは、そこに投稿するためにユーザーから手数料を請求できます（その手数料の交渉は、現時点ではプロトコルの外側で行われます）。これにより、検閲への抵抗性が確保されます（あなたの投稿を提供するためにお金を受け取るロシアのサーバーが常に存在するため）。

- **スパム**
  - スパムがリレーの懸念事項である場合、リレーは投稿のための支払いや、メールアドレスや電話番号などの認証形式、または内部的にパブキーに関連付けて、そのリレーに投稿することができるようにすることができます。または、ハッシュキャッシュやCAPTCHAのようなスパム対策技術を使用することもできます。リレーがスパムベクターとして使用されている場合、クライアントはそのリレーから更新を取得し続けることができず、簡単にリストから外すことができます。

- **データの保存**
  - ネットワークが健全であるためには、数百のアクティブなリレーが必要ではありません。実際、既存のリレーが誤動作を始めた場合、新しいリレーが簡単に作成されてネットワークに広がるため、わずかでも十分に機能します。したがって、一般的に必要とされるデータストレージの量は、Mastodonや類似のソフトウェアよりも相対的に少ないです。
  - また、別の結果を考慮すると、アマチュアによって運営される数百のニッチなリレーが存在する場合、各リレーが小さなユーザーグループからの更新を中継するというものがあります。このアーキテクチャも同様にスケーリングされます：データはユーザーから単一のサーバーに送信され、そのサーバーから直接それを消費するユーザーに送信されます。誰かがそれを保存する必要はありません。この状況では、他のユーザーからの更新を処理することは単一のサーバーにとって大きな負担ではなく、アマチュアサーバーを持つことも問題ありません。

- **ビデオやその他の重いコンテンツ**
  - リレーは大容量のコンテンツを拒否するか、受け入れとホスティングのために料金を請求することが簡単です。情報とインセンティブが明確であれば、市場力が問題を解決することは簡単です。

- **ユーザーをだますための技術**
  - 各クライアントは、ユーザーに投稿を最適に表示する方法を決定できるため、AIを使用して更新の順序を決定することから、時系列順に読むことまで、好きな方法で投稿を消費するオプションが常にあります。

## FAQ

- **これは非常にシンプルです。なぜ以前に誰もやっていないのですか？**

  私は知りませんが、ソーシャルネットワークを作る人々はお金を稼ぎたい企業またはサーバーなしで完全にものを作りたいP2Pアクティビストのどちらかであるため、Nostrが使用する両方の世界の特定のミックスを見落としています。

- **フォローする人をどうやって見つけますか？**

  まず、彼らを知って、どこかで公開鍵を見つける必要があります。それは、尋ねることやどこかで参照されていることがあります。Nostrソーシャルネットワークに入ると、彼らが他の人々と交流しているのを見ることができ、他の人々をフォローして、彼らと交流を始めることもできます。

- **中継を見つけるにはどうすればよいですか？他の人と同じ中継に接続されていない場合はどうなりますか？**

  その人と通信することはできません。ただし、イベントに関するヒントがあり、クライアントソフトウェア（または手動で）他の人の中継に接続して彼らとやりとりする方法を知ることができます。将来的には、これを解決するための他のアイデアがありますが、完璧な到達性を保証することはできません。どのプロトコルでもそうです。

- **私をフォローしている人数を知ることはできますか？**

  いいえ、しかし、リレーが追加のプロトコル方法で協力すれば、いくつかの推定値を得ることができます。

- **人々がリレーを実行するための動機は何ですか？**

  この質問は誤解を招くものです。それは、リレーが存在して人々がデータを移動できるようにするための自由なダムパイプであるという前提に基づいています。この場合、はい、動機は存在しません。実際、これはすべての他のP2PネットワークスタックのDHTノードにも言えることです。すなわち、人々がDHTノードを実行する動機は何ですか？

- **Nostr は、サーバーリレー間を移動したり、複数のリレーを使用したりすることができますが、これらのリレーが単に AWS や Azure 上にある場合、どのような違いがありますか？**

  今日は世界中に数千の VPS プロバイダーが点在しており、AWS や Azure だけではありません。AWS や Azure は、スケールが大量に必要な単一の中央集権的なサービスプロバイダーが使用するプロバイダーであり、それ以外にもあります。より小さいリレーサーバーには、どの VPS も非常に適しています。

## プロトコル仕様

プロトコル仕様の詳細な説明については、[NIP](https://github.com/nostr-protocol/nips)、特に[NIP-01](https://github.com/nostr-protocol/nips/blob/master/01.md)を参照してください（ヒント：非常に短くシンプルです）。

## ソフトウェア

Nostr を使用して構築されたほとんどのソフトウェアのリストは、https://github.com/aljazceru/awesome-nostr にあります。最後に確認した時は、ほぼ完全であったようです。

## ライセンス

パブリックドメイン。
