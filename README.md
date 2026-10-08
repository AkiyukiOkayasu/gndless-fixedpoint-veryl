# gndless_fixedpoint

Veryl向けのsigned固定小数点演算ライブラリです。

formatを表すproto package、任意format、名前付きQ形式preset、丸め、飽和、
resize、変換、全幅乗算を提供します。演算はすべてproject-scope functionとして
公開します。固定小数点値は合成可能な
`signed logic<WIDTH>`として扱い、format間の変換はformat-aware APIで明示します。

公開APIは`round_shift`、`saturate`、`resize`、`convert`、
`multiply`、`multiply_resize`です。利用側は`import gndless_fixedpoint::resize;`のように
project-scope functionをimportして呼び出します。parameterized moduleからはmodule parameterを
generic引数へ直接渡せます。format間の変換には`convert::<Q1_31, Q8_24, ...>`のように
format packageを指定します。

公開value境界には`FixedPointValue::<FORMAT>` interfaceを使います。ADATのQ1.23と
ASRC処理のQ2.23のようにformatが異なるvalue interfaceは直接接続せず、
`FormatConverter::<Q1_23, Q2_23>`を標準adapterとして挿入します。内部FIFOや
演算payloadは`FORMAT::Raw`を使います。

`round_shift`と`saturate`は、用途に不要なgeneric引数を指定させない
`resize`の安全な専用APIです。`multiply`はVerylの式幅に依存せず
`A_WIDTH + B_WIDTH`の全幅積を返し、`multiply_resize`は全幅積から出力formatへの
変換までを一括して行います。

## 利用例

```toml
[dependencies]
gndless_fixedpoint = {
    github = "AkiyukiOkayasu/gndless-fixedpoint-veryl",
    version = "0.2.2"
}
```

```veryl
import gndless_fixedpoint::{convert, Q1_31, Q8_24, Types};

let input : Q1_31::Raw;
let output: Q8_24::Raw;

assign output = convert::<
    Q1_31,
    Q8_24,
    Types::RoundingMode::NEAREST_TIES_TO_EVEN,
    Types::OverflowMode::SATURATE,
>(input);
```

## 開発

このpackageは`AkiyukiOkayasu/gndless-fixedpoint-veryl`で独立して開発・公開しています。

## 検証

`veryl check && veryl test && veryl build && veryl doc`。
