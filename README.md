# super-easy-vim
vim初心者に成功体験を与える用のジョークアプリ。vimのサブセット。極端に機能を削減し、モードとキーバインドを過剰に派手に表示する

## WIP (Work In Progress)

このプロジェクトは現在開発中です。今後、tui-textareaのexampleをベースに機能追加を行う予定です。

## 概要

Windows対応のRust製TUIエディタです。以下のライブラリを使用しています：
- ratatui: ターミナルUIフレームワーク
- tui-textarea: テキストエリアウィジェット
- crossterm: クロスプラットフォームターミナル操作

## ビルド方法

```bash
cargo build
```

## 実行方法

```bash
cargo run
```

または、ファイルを指定して実行：

```bash
cargo run path/to/file.txt
```

## 基本操作

- **Normal Mode**: 
  - `q` - 終了
  - `i` - Insert Modeに移行
  - `h/j/k/l` - カーソル移動
  - `v` - Visual Modeに移行
  - その他のVimライクな操作が可能

- **Insert Mode**:
  - `Esc` - Normal Modeに戻る
  - テキスト入力

- **Visual Mode**:
  - `y` - ヤンク（コピー）
  - `d` - 削除
  - `Esc` - Normal Modeに戻る
