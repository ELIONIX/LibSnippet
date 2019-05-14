# LibSnippet

社内ライブラリのクラス使用を補助するコードスニペットを定義するリポジトリ。

## C#のLib用のスニペット

### データクラス関連

#### propbb

Lib.BindableBaseやその派生クラスを継承して作成するクラスにおいて、変更通知プロパティの定義を記述するときに使用するスニペットとなる。  
以下の２つが存在する。

- propbb
- propbbd

プロパティ用のコードスニペットとしてよく使われるpropにBindableBaseのbbを加えた名前となっている。  
propbbdの最後のdはDataMemberのdである。  
つまり、propbbとpropbbdの違いは、DataMemober属性が付くか付かないかとなる。

以下の雛形が生成される。

```cs
/// <summary>Hoge's description(backing field)</summary>
private object _Hoge = default;
/// <summary>Hoge's description</summary>
[DataMember]
public object Hoge
{
	get { return _Hoge; }
	set { SetProperty(ref _Hoge, value); }
}
```

この雛形において、以下の４つが変更出来る部分である。

- summary文（ただし(backing field)の部分は固定）
- クラス型（デフォルトだとobjectになっている部分）
- プロパティ名（Hogeの部分。backing field側のアンダースコアは固定）
- プロパティの初期値（defaultの部分）

#### dcattr

Lib.BindableBaseやLib.Data.CloneableDataBaseの派生クラスなど、シリアライズ対象となるクラスにはDataContract属性やSerializable属性を付ける。  
その記述を補助するスニペットとなる。  
名前は、Data Contract ATTRibuteである。

以下のコードが生成される。

```cs
[Serializable]
[DataContract(Namespace = "")]
```

同時に、"System.Runtime.Serialization"のusing宣言と、System.Runtime.Serialization.dllの参照が追加される。