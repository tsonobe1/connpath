## Obstacle と Solution の順番について

### なやみ

新規作成後、初めて障害を登録するとき、個人的には次のようにしたい

- 思いつく限りの障害を全て入力する。
- その後、１つ１つに対して解決策を登録していく

だが、この構成にした場合、コードが複雑になってまう
用意しないといけない機能が次のとおり

- 複数 obstacle を一気に登録できる（バッチ処理）コンポーネント
- 一つの obstacle に対して複数の solution を登録できるコンポーネント
- 追加で obstacle を登録できるコンポーネント
  - この時、obstacle だけを追加するのか、それとも solution も合わせて登録できるのかでまた変わってくる。

### これでいいんじゃないか？

1. Goal 新規作成後は、[1]Obstacle と[n]Solution のセットを複数登録できる(dialog かな)
2. そこで Solution を登録してもいいし、後でしてもいいよ
3. 登録後の Obstacle に対して Solution を登録もできる
   - あとからの Solution の追加は、Obstacle の入れ子の位置に`<input>`が出てくる
4. 新しく Obstacle を追加する時は、２つの選択肢がある
   1. １つを追加するボタン +
   2. 複数追加するボタン ++
5. 削除は dialog でメッセージを出して削除
6. 編集も dialog で、変更前の文を出しながら新旧対象を比較して変更できるようにする

### Pattern 1

1. Obstacle を複数入力する
2. それぞれの Obstacle について、Solution を登録していく
3. 新しい Obstacle を追加する

## Pattern 2

1. Obstacle を 1 つ入力する
2. それについて Solution を登録する
3. Obstacle を追加する .........>>

## Pattern 3

1.
