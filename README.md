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

#### cloneabledb, fileabledb

```CloneableDataBase<T>```、または、```FileableDataBase<T>```を継承するクラスの定義において、親クラスを指定する部分のコードを生成する。  
名前は、CLONEABLEDataBase、及び、FILEABLEDataBaseである。  

例えばcloneabledbの場合、以下のコードが生成される。

```cs
CloneableDataBase<Hoge>
```

Hogeの部分には、自分のクラス名が自動的に挿入される。  
また、コードの生成と同時に、"ELIONIX.Lib.Data"のusing宣言と、ELIONIX.Lib.dllの参照が追加される。

### classの初期化・終了系

#### vmctorwpf

WPFのプロジェクトにおける、標準的なコンストラクタの記述の雛形を生成する。  

以下の雛形が生成される。

```cs
//------------------------------------------------------------------------------------//
/// <summary>xamlデザイナ用のインスタンスを生成</summary>
///
//! @author ELIONIX
//------------------------------------------------------------------------------------//
public HogeViewModel()
	: this(null, null)
{
	ViewModelUtility.InitializeForXamlDesigner(() => { });
}

//------------------------------------------------------------------------------------//
/// <summary>Modelを注入してインスタンスを生成</summary>
///
/// <param name="container">モデル格納オブジェクト</param>
/// <param name="uiActions">UIに依存する処理を中継するオブジェクト</param>
//! @author ELIONIX
//------------------------------------------------------------------------------------//
public HogeViewModel(
	Models.IModelContainer container,
	IWpfUiActions uiActions)
	: base(container, uiActions)
{
}
```

この雛形において、変更出来る箇所は以下の通り。

|箇所|初期文字列|説明|
|:--|:--|:--|
|author|ELIONIX|自分の名前を記述する|
|model container|Models.IModelContainer|modelを格納するオブジェクトの型を決定する。|
|関数名|HogeVilewModel|コンストラクタ。<br>実際にはHogeViewModelでは無く雛形を生成したクラスの名前が自動的に入る。|

また、コードの生成と同時に、"ELIONIX.Lib.Wpf"のusing宣言と、ELIONIX.Lib.Wpf.dllの参照が追加される。

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

#### propae, propaeb

添付プロパティと添付ビヘイビアの定義を記述するときに使用するスニペットとなる。

- proppe
- proppeb

の二つが存在する。  
また、標準にpropaというスニペットが存在し、それを改良したものとなる。  
名称もpropaから派生し、PROPA Elionix (Behavior)である。  
元のpropdpのpropはPROPerty、aはAttachedと思われる。

propaeは以下の雛形を生成する

```cs
/// <summary>このプロパティは何か？</summary>
public static readonly DependencyProperty HogeProperty = DependencyProperty.RegisterAttached(
	"Hoge",
	typeof(object),
	typeof(Piyo),
	new PropertyMetadata(default));

/// <summary>Hoge添付プロパティのgetter</summary>
public static object GetHoge(DependencyObject obj) => (object)obj.GetValue(HogeProperty);
/// <summary>Hoge添付プロパティのsetter</summary>
public static void SetHoge(DependencyObject obj, object value) => obj.SetValue(HogeProperty, value);
```

また、propaebは以下の雛形を生成する

```cs
/// <summary>このプロパティは何か？</summary>
public static readonly DependencyProperty HogeProperty = DependencyProperty.RegisterAttached(
	"Hoge",
	typeof(object),
	typeof(Piyo),
	new PropertyMetadata(default, new PropertyChangedCallback(OnHogePropertyChanged)));

/// <summary>Hoge添付プロパティのgetter</summary>
public static object GetHoge(DependencyObject obj) => (object)obj.GetValue(HogeProperty);
/// <summary>Hoge添付プロパティのsetter</summary>
public static void SetHoge(DependencyObject obj, object value) => obj.SetValue(HogeProperty, value);

//------------------------------------------------------------------------------------//
/// <summary>HogePropertyの値が変わった時のコールバック関数（添付ビヘイビア動作）</summary>
///
/// <param name="d">添付プロパティを添付されたオブジェクト（キャストして使用）</param>
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
|対象クラス|Piyo|添付プロパティを添付するクラスの型。|
|author|ELIONIX|自分の名前を記述する|

また、コードの生成と同時に、"System.Windows"のusing宣言と、WindowsBase.dllの参照が追加される。

### 単体テスト関連

#### mstestclass

MSTestでのUTクラスを作成する時に使用するスニペット。  
以下の雛形を生成する。

```cs
[TestClass()]
public class HogeTests
{
}
```

この雛形において、変更出来る箇所は以下の通り。

|箇所|初期文字列|説明|
|:--|:--|:--|
|対象クラス|Hoge|UT対象のクラス名を入力し、UT用のクラスの名前を完成させる。|

また、コードの生成と同時に、"Microsoft.VisualStudio.TestTools.UnitTesting"のusing宣言が追加される。  
dll参照はnuget packageのため追加しない。

#### mstestfunction, mstestfunctionasync

MSTestでのUT関数を作成する時に使用するスニペット。

mstestfunctionとmstestfunctionasyncはそれぞれ以下の雛形を生成する。

```cs
[TestMethod()]
public void TestHoge()
{

}

[TestMethod()]
public async Task TestHogeAsync()
{

}
```

この雛形において、変更出来る箇所は以下の通り。

|箇所|初期文字列|説明|
|:--|:--|:--|
|対象関数|Hoge|UT対象の関数名を入力し、UT用の関数名を完成させる。|

また、コードの生成と同時に、"Microsoft.VisualStudio.TestTools.UnitTesting"のusing宣言が追加される。  
dll参照はnuget packageのため追加しない。

#### utut

UTUtilityという文字だけのコードを生成する。  
また、コードの生成と同時に、"ELIONIX.Lib.Tests.Core"のusing宣言と、ELIONIX.Lib.Tests.dll及びELIONIX.Lib.Tests.Core.dllの参照が追加される。

usingと参照の追加動作の方がメインのスニペット。

#### ututiodirectory, ututiodirectoryasync

ファイルの入出力が関わる単体テストを書く場面において、UTUtility.RunIOTestWithAppropriateDirectoryやUTUtility.RunIOTestWithAppropriateDirectoryAsyncを使いたいときに使用するスニペット。  

ututiodirectoryは以下の雛形を生成する。

```cs
UTUtility.RunIOTestWithAppropriateDirectory(
	GetType(),
	directory => {
		
	});
```

また、コードの生成と同時に、"ELIONIX.Lib.Tests.Core"のusing宣言と、ELIONIX.Lib.Tests.dll及びELIONIX.Lib.Tests.Core.dllの参照が追加される。
