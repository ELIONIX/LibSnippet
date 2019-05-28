# LibSnippet

社内ライブラリのクラス使用を補助するコードスニペットを定義するリポジトリ。

## C#のLib用のスニペット

### データクラス関連

#### propbb, propbbd

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

この雛形において、変更出来る箇所は以下の通り。

|箇所|初期文字列|説明|
|:--|:--|:--|
|summary文|Hoge's description|プロパティの説明文を記述する。|
|型|object|プロパティの型を決定する。|
|名前|Hoge|プロパティ名を決定する。|
|初期値|default|プロパティの初期値を決定する。|

#### dcattr

Lib.BindableBaseやLib.Data.CloneableDataBaseの派生クラスなど、シリアライズ対象となるクラスにはDataContract属性やSerializable属性を付ける。  
その記述を補助するスニペットとなる。  
名前は、Data Contract ATTRibuteである。

以下のコードが生成される。

```cs
[Serializable]
[DataContract(Namespace = "")]
```

また、コードの生成と同時に、"System.Runtime.Serialization"のusing宣言と、System.Runtime.Serialization.dllの参照が追加される。

### classの初期化・終了系

#### disposablebor

DisposableBaseを継承したクラスにおいてoverrideして実装しなくてはいけない、DisposeManagedResourcesとDisposeUnmanagedResources関数の雛形を生成する。  
名前は、DISPOSABLE Base OverRideである。

以下のコードが生成される。

```cs
//------------------------------------------------------------------------------------//
/// <summary>マネージドリソースを破棄する</summary>
///
/// <remarks>
/// Disposableな内部オブジェクトはこっちの関数で解放する。
/// また、この関数をoverrideする時、
/// 抽象でない基底クラスの同関数を必ず呼ぶようにすること。
/// </remarks>
//! @author ELIONIX
//------------------------------------------------------------------------------------//
protected override void DisposeManagedResources()
{
	//何も無し
}

//------------------------------------------------------------------------------------//
/// <summary>アンマネージドリソースを破棄する</summary>
///
/// <remarks>
/// 通常こちらの関数で解放するべきリソースは殆ど無い。
/// ファイルやウィンドウなどでも、Manageクラスでラップされていれば
/// ManagedResourceとして処理する（Disposeを呼ぶ）。
/// ここに書くのは、Win32 APIを直接呼んで取得したハンドル（IntPtr）等を
/// CloseHandleする必要があるような場合である。
/// また、この関数をoverrideする時、
/// 抽象でない基底クラスの同関数を必ず呼ぶようにすること。
/// </remarks>
//! @author ELIONIX
//------------------------------------------------------------------------------------//
protected override void DisposeUnmanagedResources()
{
	//何も無し
}
```

|箇所|初期文字列|説明|
|:--|:--|:--|
|author|ELIONIX|自分の名前を記述する|

### WPF関連

#### propdpe, propdpec

依存関係プロパティの定義を記述するときに使用するスニペットとなる。

- propdpe
- propdpec

の二つが存在する。  
また、標準にpropdpというスニペットが存在し、それを改良したものとなる。  
名称もpropdpから派生し、PROPDP Elionix (Callback)である。  
元のpropdpのpropはPROPerty、dpはDependency Propertyと思われる。

propdpeは以下の雛形を生成する

```cs
/// <summary>このプロパティは何か？</summary>
public static readonly DependencyProperty HogeProperty = DependencyProperty.Register(
	nameof(Hoge),
	typeof(object),
	typeof(Piyo),
	new PropertyMetadata(default));

/// <summary>Hoge依存関係プロパティのラッパー</summary>
public object Hoge
{
	get => (object)GetValue(HogeProperty);
	set => SetValue(HogeProperty, value);
}
```

また、propdpecは以下の雛形を生成する

```cs
/// <summary>このプロパティは何か？</summary>
public static readonly DependencyProperty HogeProperty = DependencyProperty.Register(
	nameof(Hoge),
	typeof(object),
	typeof(Piyo),
	new PropertyMetadata(default, new PropertyChangedCallback(OnHogePropertyChanged)));

/// <summary>Hoge依存関係プロパティのラッパー</summary>
public object Hoge
{
	get => (object)GetValue(HogeProperty);
	set => SetValue(HogeProperty, value);
}

//------------------------------------------------------------------------------------//
/// <summary>HogePropertyの値が変わった時のコールバック関数</summary>
/// 
/// <param name="d">依存関係プロパティのインスタンスがあるクラス（キャストして使用）</param>
/// <param name="e">イベントの情報</param>
//! @author ELIONIX
//------------------------------------------------------------------------------------//
protected static void OnHogePropertyChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
{
}
```

この雛形において、変更出来る箇所は以下の通り。

|箇所|初期文字列|説明|
|:--|:--|:--|
|summary文|このプロパティは何か？|プロパティの説明文を記述する。|
|型|object|プロパティの型を決定する。|
|名前|Hoge|プロパティ名を決定する。|
|初期値|default|プロパティの初期値を決定する。|
|親クラス|Piyo|依存関係プロパティを持つクラスの型。<br>実際にはPiyoでは無く雛形を生成したクラスの名前が自動的に入る。|
|author|ELIONIX|自分の名前を記述する|

また、コードの生成と同時に、"System.Windows"のusing宣言と、WindowsBase.dllの参照が追加される。
