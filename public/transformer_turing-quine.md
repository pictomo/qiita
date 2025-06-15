---
title: Quineを改造してTuringMachineを作った
tags:
  - Python
  - quine
  - オートマトン
  - チューリングマシン
private: false
updated_at: '2025-06-15T15:19:12+09:00'
id: 6f7d753c9da279e60a4e
organization_url_name: null
slide: false
ignorePublish: false
---

Click [here](https://github.com/pictomo/Transformer) for an English explanation.

# 概要
[Quine](https://ja.wikipedia.org/wiki/%E3%82%AF%E3%83%AF%E3%82%A4%E3%83%B3_(%E3%83%97%E3%83%AD%E3%82%B0%E3%83%A9%E3%83%9F%E3%83%B3%E3%82%B0))とは、自分自身を出力するプログラミングコードのこと。
"Quineは自分自身を出力する、ということは、自分自身をちょっとだけ変えたものも出力できるのでは?"ということで、Quineを応用し、チューリングマシン含む色々なものを作ってみたという記録になります。
記事を読むよりも実際に実行してみた方がきっとわかりやすいです。
実際のコードは[こちらのリポジトリ](https://github.com/pictomo/Transformer)に保存してあるので、ぜひご活用ください。

# Quineとは?
百聞は一見に如かずということで、まずはこちらのpythonスクリプトを実行してみてください。
```python:python
_='_=%r;print(_%%_)';print(_%_)
```
するとシェルに以下のような文字列が出力されたと思います。
```text:shell
_='_=%r;print(_%%_)';print(_%_)
```
おや、これは実行したPythonスクリプトそれ自体と全く同じですね。
という感じで、Quineとは、**自分自身を出力するプログラムコード**のことをいいます。

[こちら](https://github.com/pictomo/quine-python/blob/main/quine.ipynb)が、私がQuineを知った時に試したリポジトリです。
また、こちらの動画([プログラマの知的遊戯！自身を出力するプログラムQuineの作り方【東工大院生の日常会話切り抜き】#3 - YouTube](https://www.youtube.com/watch?v=KHdDVSpK2sY))では、Quineが非常に興味をそそる形で紹介されています。
Quineを使って、こんな感じ([【HTML】お手軽にQuineアートを作る #JavaScript - Qiita](https://qiita.com/mogamoga1337/items/660842a1b8849a54051b))でアートを作ることもできます。

# Transformer
Quineで遊んでいるうちに、"**Quineは自分自身を出力する、ということは、当然自分自身をちょっとだけ変更したものも出力できる**のでは?"と思ったわけです。
そしてそれを**標準出力ではなく自分自身の書き換えに使えば、実行する度に自分自身が変化するスクリプトとなります。**
そこでまず私が作ったのが、Transformer(｀・ω・´)ｼｬｷｰﾝ
ちなみにTransformerという命名は、AIやNLPでいうところのTransformerとは全く関係ありません。
車がロボットに変形する方のトランスフォーマーです。

```python:transformer Robot Mode
#!/usr/bin/env python3

# Robot Mode

import sys

path = sys.argv[0]

vehicle_content = """#!/usr/bin/env python3

# Vehicle Mode

import sys

path = sys.argv[0]

vehicle_content = {0}{1}{0}
robot_content = {0}{2}{0}

triple_quotes = chr(34) + chr(34) + chr(34)
with open(path, "w") as file:
    file.write(robot_content.format(triple_quotes, vehicle_content, robot_content))
"""
robot_content = """#!/usr/bin/env python3

# Robot Mode

import sys

path = sys.argv[0]

vehicle_content = {0}{1}{0}
robot_content = {0}{2}{0}

triple_quotes = chr(34) + chr(34) + chr(34)
with open(path, "w") as file:
    file.write(vehicle_content.format(triple_quotes, vehicle_content, robot_content))
"""

triple_quotes = chr(34) + chr(34) + chr(34)
with open(path, "w") as file:
    file.write(vehicle_content.format(triple_quotes, vehicle_content, robot_content))
```

これを実行すると...

```diff_python:transformer Vehicle Mode
#!/usr/bin/env python3

- # Robot Mode
+ # Vehicle Mode

import sys

path = sys.argv[0]

vehicle_content = """#!/usr/bin/env python3

# Vehicle Mode

import sys

path = sys.argv[0]

vehicle_content = {0}{1}{0}
robot_content = {0}{2}{0}

triple_quotes = chr(34) + chr(34) + chr(34)
with open(path, "w") as file:
    file.write(robot_content.format(triple_quotes, vehicle_content, robot_content))
"""
robot_content = """#!/usr/bin/env python3

# Robot Mode

import sys

path = sys.argv[0]

vehicle_content = {0}{1}{0}
robot_content = {0}{2}{0}

triple_quotes = chr(34) + chr(34) + chr(34)
with open(path, "w") as file:
    file.write(vehicle_content.format(triple_quotes, vehicle_content, robot_content))
"""

triple_quotes = chr(34) + chr(34) + chr(34)
with open(path, "w") as file:
    file.write(vehicle_content.format(triple_quotes, vehicle_content, robot_content))
```
わかりにくいですが、コードがロボットモードから車モードに変化しています。
もう一度実行すると元に戻ります。
面白いでしょう?

他にも色々とバリエーションを作っているので、ぜひご自身で実行してみてください。
[mini-transformer](https://github.com/pictomo/Transformer/blob/main/mini-transformer/main.py)は上記のTransformerをよりコンパクトに実装してみたものです。
[self-reconstructor](https://github.com/pictomo/Transformer/blob/main/self-reconstructor/main.py)はもっとシンプルで、ただ自分自身を書き換えるだけの変化しないQuineとなります。
[ultraman-tiga](https://github.com/pictomo/Transformer/blob/main/ultraman-tiga/main.py)は3状態を行き来するものです。ウルトラマンティガみたいに( o|o)/ == ｼﾞｭﾜｯ
[counter](https://github.com/pictomo/Transformer/blob/main/counter/main.py)は、実行回数を自身に格納します。実行するたびに`execution_count`が+1されます。

# Turing Quine
さて本題です。
[counter](https://github.com/pictomo/Transformer/blob/main/counter/main.py)は実行する度に、動的に次の状態を算出し出力しています。
ということは、これを応用すれば、チューリング完全な何かになるのでは?と考えました。
こうして、試行錯誤して出来上がったのがこちら。

```python:turing-quine
#!/usr/bin/env python3

# states
execution_count = 0
state = 0
tape = ["0"]
head = 0

# machine
delta = {
	0: {' ': (1, '.', 1), '.': (0, '#', -1), '#': (0, '.', -1)},
	1: {' ': (0, '#', -1), '.': (1, '#', 1), '#': (0, ' ', 1)}
}
initial_state = 0
goal_states = []

# tape
alphabet = [' ', '.', '#']
default_symbol = "0"
initial_tape = ['0']
initial_head = 0

import sys

path = sys.argv[0]
content = """#!/usr/bin/env python3

# states
execution_count = {5}
state = {6}
tape = {7}
head = {8}

# machine
delta = {9}
initial_state = {10}
goal_states = {11}

# tape
alphabet = {12}
default_symbol = "{13}"
initial_tape = {14}
initial_head = {15}

import sys

path = sys.argv[0]
content = {1}{0}{1}

def tape_str(tape, head):
    s = "".join(tape)
    return s[:head] + "{2}033[1m" + s[head] + "{2}033[0m" + s[head+1:]

if execution_count == 0:
    print(str(execution_count).zfill(3), "Accepted" if state in goal_states else "Rejected", state, tape_str(tape, head))

next = delta.get(state, {3}{4}).get(tape[head], False)
if next:
    state = next[0]
    tape[head] = next[1]
    head += next[2]
    if head == len(tape):
        tape.append(default_symbol)
    elif head == -1:
        tape.insert(0, default_symbol)
        head = 0
    execution_count += 1
    print(str(execution_count).zfill(3), "Accepted" if state in goal_states else "Rejected", state, tape_str(tape, head))
else:
    print("No transition found.")
    sys.exit(1)

with open(path, "w") as file:
    file.write(
        content.format(content, chr(34) * 3, chr(92), chr(123), chr(125), execution_count, state, tape, head, delta, initial_state, goal_states, alphabet, default_symbol, initial_tape, initial_head)
    )
"""

def tape_str(tape, head):
    s = "".join(tape)
    return s[:head] + "\033[1m" + s[head] + "\033[0m" + s[head+1:]

if execution_count == 0:
    print(str(execution_count).zfill(3), "Accepted" if state in goal_states else "Rejected", state, tape_str(tape, head))

next = delta.get(state, {}).get(tape[head], False)
if next:
    state = next[0]
    tape[head] = next[1]
    head += next[2]
    if head == len(tape):
        tape.append(default_symbol)
    elif head == -1:
        tape.insert(0, default_symbol)
        head = 0
    execution_count += 1
    print(str(execution_count).zfill(3), "Accepted" if state in goal_states else "Rejected", state, tape_str(tape, head))
else:
    print("No transition found.")
    sys.exit(1)

with open(path, "w") as file:
    file.write(
        content.format(content, chr(34) * 3, chr(92), chr(123), chr(125), execution_count, state, tape, head, delta, initial_state, goal_states, alphabet, default_symbol, initial_tape, initial_head)
    )
```

スクリプトの上部に、チューリングマシンとその状態が定義されています。
ちなみにここで定義しているのは、[Wolframeの2状態3記号チューリングマシン](https://en.wikipedia.org/wiki/Wolfram%27s_2-state_3-symbol_Turing_machine)というやつです。
[万能チューリングマシン](https://en.wikipedia.org/wiki/Universal_Turing_machine)であることが[証明](https://www.wolframscience.com/prizes/tm23/solved.html)されています。
スクリプトを実行する度に、`# states`以下が変化していくことがわかるでしょう。

このスクリプトは、チューリングマシンとして動作していることがよりわかりやすいように、実行時に状態を標準出力するようにしてあります。
また、同じディレクトリに[リピーター](https://github.com/pictomo/Transformer/blob/main/turing-quine/repeater.py)を置いてそちらを実行すると、自動で繰り返し実行してくれます。

実際に実行してみたときの出力が以下。
[Wolframeの2状態3記号チューリングマシン](https://en.wikipedia.org/wiki/Wolfram%27s_2-state_3-symbol_Turing_machine)に特有の往復模様が確認できますね。

```text:shell
...
1101 Rejected 0 ..########........###...##.#.#.##.##.##.##.#
1102 Rejected 0 ..#######.........###...##.#.#.##.##.##.##.#
1103 Rejected 0 ..######..........###...##.#.#.##.##.##.##.#
1104 Rejected 0 ..#####...........###...##.#.#.##.##.##.##.#
1105 Rejected 0 ..####............###...##.#.#.##.##.##.##.#
1106 Rejected 0 ..###.............###...##.#.#.##.##.##.##.#
1107 Rejected 0 ..##..............###...##.#.#.##.##.##.##.#
1108 Rejected 0 ..#...............###...##.#.#.##.##.##.##.#
1109 Rejected 0 ..................###...##.#.#.##.##.##.##.#
1110 Rejected 0 .#................###...##.#.#.##.##.##.##.#
1111 Rejected 0  ##................###...##.#.#.##.##.##.##.#
1112 Rejected 1 .##................###...##.#.#.##.##.##.##.#
1113 Rejected 0 . #................###...##.#.#.##.##.##.##.#
1114 Rejected 0 . .................###...##.#.#.##.##.##.##.#
1115 Rejected 1 ...................###...##.#.#.##.##.##.##.#
1116 Rejected 1 ..#................###...##.#.#.##.##.##.##.#
1117 Rejected 1 ..##...............###...##.#.#.##.##.##.##.#
1118 Rejected 1 ..###..............###...##.#.#.##.##.##.##.#
1119 Rejected 1 ..####.............###...##.#.#.##.##.##.##.#
1120 Rejected 1 ..#####............###...##.#.#.##.##.##.##.#
1121 Rejected 1 ..######...........###...##.#.#.##.##.##.##.#
1122 Rejected 1 ..#######..........###...##.#.#.##.##.##.##.#
1123 Rejected 1 ..########.........###...##.#.#.##.##.##.##.#
1124 Rejected 1 ..#########........###...##.#.#.##.##.##.##.#
1125 Rejected 1 ..##########.......###...##.#.#.##.##.##.##.#
1126 Rejected 1 ..###########......###...##.#.#.##.##.##.##.#
1127 Rejected 1 ..############.....###...##.#.#.##.##.##.##.#
1128 Rejected 1 ..#############....###...##.#.#.##.##.##.##.#
1129 Rejected 1 ..##############...###...##.#.#.##.##.##.##.#
1130 Rejected 1 ..###############..###...##.#.#.##.##.##.##.#
1131 Rejected 1 ..################.###...##.#.#.##.##.##.##.#
1132 Rejected 1 ..####################...##.#.#.##.##.##.##.#
1133 Rejected 0 ..################# ##...##.#.#.##.##.##.##.#
1134 Rejected 0 ..################# .#...##.#.#.##.##.##.##.#
1135 Rejected 1 ..#################..#...##.#.#.##.##.##.##.#
1136 Rejected 1 ..#################.##...##.#.#.##.##.##.##.#
1137 Rejected 0 ..#################.# ...##.#.#.##.##.##.##.#
1138 Rejected 0 ..#################.# #..##.#.#.##.##.##.##.#
1139 Rejected 1 ..#################.#.#..##.#.#.##.##.##.##.#
1140 Rejected 0 ..#################.#. ..##.#.#.##.##.##.##.#
1141 Rejected 0 ..#################.#. #.##.#.#.##.##.##.##.#
1142 Rejected 1 ..#################.#..#.##.#.#.##.##.##.##.#
1143 Rejected 0 ..#################.#.. .##.#.#.##.##.##.##.#
1144 Rejected 0 ..#################.#.. ###.#.#.##.##.##.##.#
1145 Rejected 1 ..#################.#...###.#.#.##.##.##.##.#
1146 Rejected 0 ..#################.#... ##.#.#.##.##.##.##.#
1147 Rejected 0 ..#################.#... .#.#.#.##.##.##.##.#
1148 Rejected 1 ..#################.#.....#.#.#.##.##.##.##.#
1149 Rejected 1 ..#################.#....##.#.#.##.##.##.##.#
1150 Rejected 0 ..#################.#....# .#.#.##.##.##.##.#
...
```

# まとめ
というわけで、Quineがチューリング完全であることが証明できた...かはわかりませんが、Quineを応用した新しい遊びを実演してみました。
Quineにちょっとだけ手を加え、自分自身を書き換えるようにするだけで、実行する度に自分自身が変化していくおもしろスクリプトができちゃいます。
実際に動いているところを見るともっと面白いので、まだの方は[こちらのリポジトリ](https://github.com/pictomo/Transformer)をクローンしてぜひ試してみてください。
また、これを応用して出来る新しいものを思いついたら、ぜひ実装して、私に教えてください。
