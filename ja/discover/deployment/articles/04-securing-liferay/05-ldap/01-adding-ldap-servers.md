# LDAPサーバーの追加[](id=adding-ldap-servers)

LDAPサーバーの見出しの下にある*[追加]*ボタンをクリックして、LDAPサーバー接続を追加します。複数のLDAPサーバーがある場合は、上/下矢印を使用して優先順位の高い順にサーバーを並べ替えることができます。LDAPサーバーを追加するときは、@product@がLDAPサーバーにバインドしてユーザーレコードを検索できるように、いくつかのデータを指定する必要があります。LDAPサーバーをいくつ追加しても、各サーバーには同じ設定オプションがあります。

**サーバー名：**LDAPサーバーの名前を入力します。

**デフォルト値：**いくつかの主要なディレクトリサーバがここにリストされています。これらのいずれかを使用している場合は、それを選択してください。フォームの残りの部分には、そのディレクトリの適切なデフォルト値が入力されています。

**接続：**これらの設定はLDAPへの基本的な接続をカバーします。

- *ベースプロバイダーURL：*LDAPサーバーへのリンク。@product@サーバーがLDAPサーバーと通信できることを確認してください。2つのシステム間にファイアウォールがある場合は、適切なポートが開かれていることを確認してください。

- *ベースDN：*LDAPディレクトリのベース識別名。通常、お客様の組織をモデルにしています。商業組織の場合、次のようになります。`dc=companynamehere,dc=com`

- *プリンシパル：*デフォルトでは、LDAP管理者のユーザーIDがここに取り込まれています。デフォルトのLDAP管理者を削除した場合は、代わりに使用する管理資格情報の完全修飾名を使用する必要があります。@product@はこのIDを使用してLDAPとの間でユーザーアカウントを同期するため、管理者の資格情報が必要です。

- *認証情報：*これはLDAP管理ユーザーのパスワードです。

LDAPディレクトリに定期的に接続するために必要なものは以上です。ただし、残りの設定は適切なデフォルトの最善の推測にすぎないので、カスタマイズが必要な場合があります。通常、デフォルトの属性マッピングはユーザーがログインを試みるときに@product@データベースに同期するのに十分なデータを提供します。LDAPサーバーへの接続をテストするには、*[LDAP接続テスト]*ボタンをクリックします。

## 確認事項[](id=checkpoint)

@product@のLDAP接続を微調整する前に、以下の確認をしてください。

1. _[コントロールパネル]_で、LDAP接続は有効になっていますか。ニーズに応じて、バインドされたユーザーだけがログインできるように、LDAP認証が必要になる場合があります。

2. *エクスポート/インポート：*クラスター環境のユーザーの場合、起動時に大量のインポートが行われないように、[起動時にインポート/エクスポートを有効にする]を無効にする必要があります。

3. LDAPサーバーを追加するとき、*サーバー名*、*デフォルト値*、 *接続の値*は正しいですか。保存する前に、*[LDAP接続のテスト]*をクリックすることをお勧めします。

## セキュリティ[](id=security)

資格情報が暗号化されない状態でネットワークを通過しないよう、SSLモードでLDAPディレクトリを実行する場合は、2つのシステム間で暗号化キーと証明書を共有するための追加の手順を実行する必要があります。

たとえば、LDAPディレクトリがWindows Server 2003上のMicrosoft Active Directoryの場合は、次のように証明書を共有します。

*[スタート]* → *[管理ツール]* → *[認証局]*の順にクリックします。
認証局であるマシンをハイライトして右クリックし、*[プロパティ]*をクリックします。[一般メニュー]から、*[証明書の表示]*をクリックします。詳細ビューを選択して、*[ファイルへコピーする]*をクリックします。作成されたウィザードを使用して、証明書をファイルとして保存します。証明書を*[cacertsキーストア]*に以下のようにインポートする必要もあります。

    keytool -import -trustcacerts -keystore /some/path/java-8-jdk/jre/lib/security/cacerts -storepass changeit -noprompt -alias MyRootCA -file /some/path/MyRootCA.cer

 *keytool* ユーティリティは、Java SDKの一部として出荷されています。

これが完了したら、コントロールパネルのLDAPページに戻ります。以下のようにプロトコルを`ldaps`に、ポートを`636`に変更して、ベースDNフィールドのLDAP URLを安全なバージョンに変更します。

    ldaps://myLdapServerHostname:636

変更を保存してください。LDAPへの通信は暗号化されました。

## LDAPインポート/エクスポートの設定[](id=configuring-ldap-import-export)

他の設定では、LDAPと@product@間のマッピングを設定して、ユーザーとグループをインポートできるようにします。

### ユーザー[](id=users)

このセクションには、LDAPディレクトリでユーザーを検索するための設定が含まれています。

**認証検索フィルタ：**検索フィルタボックスを使用して、ユーザーログインの検索基準を決定できます。デフォルトでは、@product@はログイン名にユーザーのEmailメールアドレスを使用します。この設定を変更した場合は、ここで検索フィルタを変更する必要があります。デフォルトでは、検索基準としてLDAPのEmailメールアドレス属性が使用されます。たとえば、@product@の認証方法をEmailメールアドレスの代わりにスクリーン名を使用するように変更した場合は、入力されたログイン名と一致するように検索フィルタを変更します。

    (cn=@screen_name@)

**検索フィルタのインポート：****LDAP**サーバーによっては、ユーザーを識別するためのさまざまな方法があります。通常は、デフォルト設定で問題ありません。

    (objectClass=inetOrgPerson)

ユーザーのサブセットまたは異なるLDAPオブジェクトクラスを持つユーザーのみを検索したい場合は、これで変更できます。

**ユーザーマッピング：**次に、LDAP属性からLiferayフィールドへのマッピングが定義できます。LDAPユーザー属性はLDAPサーバーごとに異なる可能性がありますが、ユーザーが認識されるためには@product@のマッピングに必要な5つのフィールドがあります。

+ *スクリーンネーム*（例：*uid*）

+ *パスワード*（例：*userPassword*）

+ *Emailアドレス*（例：*mail*または*email*）

+ *名*（例：*name*または*givenName*）

+ *姓*（例：*sn*）

**注：**Emailアドレスを持たないユーザーを作成、またはインポートする場合は、`portal-ext.properties`に`users.email.address.required=false`を設定する必要があります。このセットで、LiferayはユーザーIDと`users.email.address.auto.suffix=`プロパティで定義されたサフィックスを組み合わせたEmailアドレスを自動生成します users.email.address.auto.suffix=。最後に、LiferayおよびLDAP認証をEmailアドレス以外のものに設定してください。

LDAPグループを@product@ ユーザーグループとしてインポートする場合は、メンバーシップ情報が保持されるように、@product@のグループフィールドのマッピングを必ず定義してください。

+ *グループ*（例： *member*）

他のLDAPユーザーのマッピングフィールドはオプションです。

コントロールパネルは、一般的に使用されるLDAP属性のデフォルトマッピングを提供します。
独自のマッピングを追加することもできます。

**LDAPユーザーテスト：**属性マッピングを設定したら（上記を参照）、*[LDAPユーザーをテストする]*ボタンをクリックします。すると、@product@はLDAPユーザーをプルして、プレビューとしてマッピングと照合しようとします。

![図1：LDAPユーザーのテスト](../../../images/server-configuration-testing-ldap-users.png)

### [グループ](id=groups)

このセクションには、LDAPグループを@product@のユーザーグループにマッピングするための設定が含まれています。

**検索フィルタのインポート：**これは、@product@のユーザーグループにマッピングするLDAPグループを見つけるためのフィルタです。例えば、

    (objectClass=groupOfNames)

このマッピング用に取得したいLDAPグループ属性を入力します。以下の属性をマッピングすることができます。*[グループ名]*および*[ユーザー]*フィールドは必須です。*[説明]*はオプションです。

+ *グループ名*（例：*cn*または*o*）

+ *説明*（例：*description*）

+ *ユーザー*（例：*member*）

**LDAPグループテスト：***[LDAPグループをテストする]*ボタンをクリックして、検索フィルタによって返されたグループのリストを表示します。

### エクスポート[](id=export)

このセクションでは、LDAPからユーザーデータをエクスポートするための設定について説明します。

**ユーザーDN：**ユーザーが保存されているLDAPツリー内の場所を入力します。
@product@がエクスポートを実行すると、ユーザーはこの場所にエクスポートされます。

**ユーザデフォルトオブジェクトクラス：**ユーザがエクスポートされると、リストされたデフォルトのオブジェクトクラスを使用して、ユーザが作成されます。デフォルトのオブジェクトクラスが何かを調べるには、Apache Directory StudioなどのLDAPブラウザツールを使用してユーザーを見つけ、そのユーザーのLDAPに格納されているオブジェクトクラス属性を確認します。

**グループDN：**グループが保存されているLDAPツリー内の場所を入力します。
@product@がエクスポートを実行すると、グループはこの場所にエクスポートされます。

**グループデフォルトオブジェクトクラス：**グループがエクスポートされると、リストされたデフォルトのオブジェクトクラスを使用して、グループが作成されます。デフォルトのオブジェクトクラスが何かを調べるには、Apache Directory StudioなどのLDAPブラウザツールを使用してグループを見つけ、そのグループのLDAPに格納されているオブジェクトクラス属性を確認します。

すべてのオプションを設定して接続をテストしたら、*[保存]*をクリックします。

+$$$

**注：**@product@内のパスワードなどの値をユーザーが変更すると、@product@に変更を加えるための十分なスキーマアクセス権がある限り、その変更はLDAPサーバーに渡されます。

$$$

LDAPサーバーを@product@に接続する方法、およびユーザーのインポート動作、エクスポート動作、およびその他のLDAP設定を設定する方法は以上です。

## 関連トピック[](id=related-topics)

[@product@セキュリティの概要](/discover/deployment/-/knowledge_base/7-0/liferay-portal-security-overview)[@product@へのログイン](/discover/deployment/-/knowledge_base/7-0/logging-in-to-liferay)
