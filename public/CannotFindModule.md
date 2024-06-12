---
title: tscでHoge.tsとhoge.jsが混在する
tags:
  - TypeScript
private: false
updated_at: '2024-06-12T11:02:57+09:00'
id: 69f54172bf6489d80d25
organization_url_name: null
slide: false
ignorePublish: false
---

# 環境

| name             | version |
| ---------------- | ------- |
| Windows11        | 23H2    |
| tsc              | 5.4.5   |
| node             | 20.9.0  |
| npm/npx          | 10.5.1  |
| electron         | 27.0.3  |
| electron-builder | 24.13.3 |

# 問題

electron コマンドで実行した際には正常に動くのに、electron-builder でビルドして実行すると cannot find module となり困っていた。
tsc 並びに electron コマンドが通るので、もちろんファイル名は正しい（つもりだった）。

![こんなエラーが出る](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/3797993/df558be8-64df-e795-d9d4-803da3283a11.png)

::: note warn
Error:Cannot find module
require 文で Cpu.class.js モジュールが見つからない。
:::

# 原因と解決

以下、コンパイルは tsc によるもの。
hoge.ts をコンパイル後、Hoge.ts （先頭大文字）にリネームしたことが原因だった。
リネーム後再度コンパイルしても、hoge.js が更新されるだけで Hoge.js は生成されない。
一度 hoge.js を削除してからコンパイルすれば Hoge.js が生成される。

> root/
> ├ Hoge.ts
> └ hoge.js
> こういう構成のとき困るという話

また、tsc は.ts ファイルを見てモジュールが存在するかどうか判断する。
そのため Hoge.ts と hoge.js がある状態だと、"Hoge"を import しないと警告が出る。（tsconfig の [forceConsistentCasingInFileNames](https://zenn.dev/chida/articles/bdbcd59c90e2e1#forceconsistentcasinginfilenames) が true の場合）
しかし、コンパイル後には"hoge"モジュールである。ややこしや～
