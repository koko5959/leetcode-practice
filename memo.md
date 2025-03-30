# はじめに
- Step1
    - 初見で解く
    - 知らなければできないことは調べる
- Step2
    - なんでも使ってコードを洗練させる
- Step3
    - 何も見ずにStep1の思考でStep2と同等のコードを3回繰り返してすらすら書く

# Step1
## I/O Analysis
- Input
    - s
        - type
            - string
        - length
            - 1 ~ 10000
        - Restriction
            - consists of parentheses only '()[]{}'.
- Output
    - boolean

## 方針
### 発想
スタックを使うと簡単に解けそう。
### 具体的な処理
(, [, {が来たらスタックにプッシュする  
逆のかっこが来たらポップする  
popできなかったらFalse
 - かっこが合わない
 - Stackが空(考慮漏れ)
最後までスタックにアイテムが残ってたらFalse

### Pythonでスタックを使いたいときはどうすればいいんだろう？
https://qiita.com/saba/items/107c4237206e31acdbef    
> Deques are a generalization of stacks and queues (the name is pronounced “deck” and is short for “double-ended queue”). Deques support thread-safe, memory efficient appends and pops from either side of the deque with approximately the same O(1) performance in either direction.
[Official document](https://docs.python.org/3/library/collections.html#collections.deque)
listの実装だと、pop(0) と insert(0,v)でO(n)だよ、とも書かれている。listは固定長の時に利用すべき。

```python
import collections


class Solution:
    def isValid(self, s: str) -> bool:
        stack = collections.deque()
        left_parentheses = ["(", "{", "["]
        for char in s:
            if char in left_parentheses:
                stack.append(char)
            else:
                if len(stack) == 0:
                    return False
                popped_char = stack.pop()
                if (
                    (popped_char == "(" and char == ")")
                    or (popped_char == "{" and char == "}")
                    or (popped_char == "[" and char == "]")
                ):
                    continue
                else:
                    return False
        if len(stack) != 0:
            return False
        return True
```

# Step2
## Step1の反省
- テストケースを見なかった
    - 入力が途中で空になるケースを入れておくべきだった
- かっこは組にできるので、辞書を使う
- 空リスト判定は、not list_1を使う
    - [pep8](https://pep8-ja.readthedocs.io/ja/latest/#:~:text=%E3%82%B7%E3%83%BC%E3%82%B1%E3%83%B3%E3%82%B9%20(%E6%96%87%E5%AD%97%E5%88%97%2C%20%E3%83%AA%E3%82%B9%E3%83%88%2C%20%E3%82%BF%E3%83%97%E3%83%AB)%20%E3%81%AB%E3%81%A4%E3%81%84%E3%81%A6%E3%81%AF%E3%80%81%20%E7%A9%BA%E3%81%AE%E3%82%B7%E3%83%BC%E3%82%B1%E3%83%B3%E3%82%B9%E3%81%8C%20False%20%E3%81%A7%E3%81%82%E3%82%8B%E3%81%93%E3%81%A8%E3%82%92%E5%88%A9%E7%94%A8%E3%81%97%E3%81%BE%E3%81%97%E3%82%87%E3%81%86%E3%80%82%3A)

```python
import collections


class Solution:
    def isValid(self, s: str) -> bool:
        stack = collections.deque()
        mapping_doc = {")": "(", "]": "[", "}": "{"}
        for char in s:
            if char in mapping_doc:
                if not stack or stack.pop() != mapping_doc[char]:
                    return False
            else:
                stack.append(char)
        return not stack
```

# Step3
特筆すべきことがなかったため、略
