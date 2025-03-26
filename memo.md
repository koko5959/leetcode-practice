
# Step1

## I/O分析
### input
- l1
	- Type: Linked List(No case for none)
	- Value range: 0~9
	- Length: 1~100
	- Not represents 0
- l2: Linked List
	- The same as l1
### output
- Linked list
	- Value range: 0 ~ 9
	- Length: 1 ~ 101
## 発想
- 出力のために
  - 新たなList Nodeを作成したほうがよい
- 処理
	- 類似の処理
		- ひっ算
			- 2つの数の和をひっ算するとき、その数の桁数は気にしないので、この問題と全く同じ処理で私は和のひっ算を行っている→真似すればよい
	- 処理のざっとした流れ
		- 変数を置く
			- l1を順にみていくポインタ
			- l2を順にみていくポインタ
			- 各処理の合計値
			- 繰り上がり
			- 出力用のポインタ
				- 1桁目から最後まで同じ計算がしたいので、番兵を設定する
		- 繰り返し処理
			- while loop
			- 終了条件
				- 以下いずれかを満たす
					- l1の終端まで見た
					- l2の終端まで見た
					- 繰り上がりが0
		- 出力
			- 番兵の次のノード

## Pythonの言語仕様について
Q.関数の引数でオブジェクト(ミュータブルな値)への参照を与えられたとき、その参照を別の参照にすると実引数側に影響はあるか？  
A.別に何も起きない。[仮引数には実引数が参照しているオブジェクトへの参照が渡される](https://docs.python.org/3/tutorial/controlflow.html#defining-functions:~:text=The%20execution%20of,for%20that%20call.)のであって、実引数側には影響がない。
ただし、参照が渡される\*ので、実引数の参照しているオブジェクトを仮引数経由で変更を行った場合、実引数の参照先のオブジェクトも当然変更される。
\*正確には値が渡されるが、Pythonの場合すべての変数はオブジェクトへの参照なので、参照が結果的に渡される。

Q. None比較のベストプラクティスは？(※Step2実行時にStep1のコードが反していることが判明したので、ここにメモする)    
A. x is /is not Noneとすべき。  
[pep8](https://pep8-ja.readthedocs.io/ja/latest/#:~:text=None%20%E3%81%AE%E3%82%88%E3%81%86%E3%81%AA%E3%82%B7%E3%83%B3%E3%82%B0%E3%83%AB%E3%83%88%E3%83%B3%E3%81%A8%E6%AF%94%E8%BC%83%E3%82%92%E3%81%99%E3%82%8B%E5%A0%B4%E5%90%88%E3%81%AF%E3%80%81%E5%B8%B8%E3%81%AB%20is%20%E3%81%8B%20is%20not%20%E3%82%92%E4%BD%BF%E3%81%86%E3%81%B9%E3%81%8D%E3%81%A7%E3%81%99%E3%80%82%E7%B5%B6%E5%AF%BE%E3%81%AB%E7%AD%89%E5%80%A4%E6%BC%94%E7%AE%97%E5%AD%90%E3%82%92%E4%BD%BF%E3%82%8F%E3%81%AA%E3%81%84%E3%81%A7%E3%81%8F%E3%81%A0%E3%81%95%E3%81%84%E3%80%82)
\*この規約の背景として演算子オーバーロードがあるっぽいが、文献がwikibooksしか見つからず。いったん断念。

```python
class Solution:

    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        #出力用リストの始点の前
        fake = ListNode(-1, None)
        #ひっ算を想像しつつ、変数を置く
        pointer1 = l1 #l1, l2そのまま使ってもいい？
        pointer2 = l2
        pointer3 = fake #和を計算している桁の直前の桁
        sum_digit = 0
        carry_up = 0
        
        #ひっ算する
        while pointer1 != None or pointer2 != None or carry_up != 0:
            node_of_sum = ListNode(0, None) #計算する桁のノードを生成
            #桁の和の計算
            if pointer1 != None:
                sum_digit += pointer1.val
            if pointer2 != None:
                sum_digit += pointer2.val
            sum_digit += carry_up
            
            carry_up = 0 #繰り上がり桁のリセット
            node_of_sum.val = sum_digit % 10 #計算する桁の数字
            pointer3.next = node_of_sum
            carry_up = sum_digit//10　#繰り上がり桁
            sum_digit = 0 #和のリセット
            #次の桁へ
            if pointer1 != None:
                pointer1 = pointer1.next
            if pointer2 != None:
                pointer2 = pointer2.next
            pointer3 = pointer3.next
        #終了状態: l1 == None and l2 == None and carry_up == 0 and pointer3.next = None
        return fake.next
```

# Step2

## 方針
- l1とl2に対して関数内で変数制限する必要はない
- 番兵の変数名はfakeではなくsentにする

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode], carry: int = 0) -> Optional[ListNode]:
        #番兵
        sent = ListNode(-1, None)
        #ひっ算を想像して変数を置く
        sum_of_the_digit = 0 
        carry = 0 
        l3 = sent #計算する桁のポインタの直前の桁
        while l1 != None or l2 != None or carry != 0:
            #計算
            digit_node = ListNode(0, None) #計算する桁の計算結果を保存するためのポインタ
            if l1 != None:
                sum_of_the_digit += l1.val
            if l2 != None:
                sum_of_the_digit += l2.val
            sum_of_the_digit += carry
            digit_node.val = sum_of_the_digit%10
            carry = sum_of_the_digit//10
            #次の桁の計算への準備
            l3.next = digit_node
            l3 = l3.next
            sum_of_the_digit = 0
            if l1 != None:
                l1 = l1.next
            if l2 != None:
                l2 = l2.next
        return sent.next
```


# Step2(re)
## 方針
- 変数名の変更
- ポインタの繋ぎ変えの処理をループの最初に持ってくる
- None比較をis/is notに

```python
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        #番兵
        sent = ListNode(-1, None)
        #ひっ算に必要なメモリ
        sum_of_the_digit = 0
        carry = 0
        prev_node = sent
        while l1 is not None or l2 is not None or carry != 0:
            node_for_the_digit = ListNode(0, None)
            prev_node.next = node_for_the_digit
            prev_node = prev_node.next
            if l1 is not None:
                sum_of_the_digit += l1.val
            if l2 is not None:
                sum_of_the_digit += l2.val
            sum_of_the_digit += carry
            node_for_the_digit.val = sum_of_the_digit%10
            carry = sum_of_the_digit//10
            #メモリのリセット&次の桁へ進む
            sum_of_the_digit = 0
            if l1 is not None:
                l1 = l1.next
            if l2 is not None:
                l2 = l2.next
        return sent.next
```

# Step3
```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        #番兵
        sent = ListNode(-1, None)
        #ひっ算に必要なメモリ
        sum_of_the_digit = 0
        carry = 0
        prev_digit = sent
        while l1 is not None or l2 is not None or carry != 0:
            node_for_the_digit = ListNode(0, None)
            prev_digit.next = node_for_the_digit
            prev_digit = prev_digit.next
            if l1 is not None:
                sum_of_the_digit += l1.val
            if l2 is not None:
                sum_of_the_digit += l2.val
            #見ている桁の計算
            sum_of_the_digit += carry
            node_for_the_digit.val = sum_of_the_digit%10
            carry = sum_of_the_digit//10
            #次の桁の計算のための準備
            sum_of_the_digit = 0
            if l1 is not None:
                l1 = l1.next
            if l2 is not None:
                l2 = l2.next
        return sent.next
```
