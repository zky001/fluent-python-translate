我们已经看到使用特定方法利用Python数据模型的两个优点：

1.您的类的使用者不必记住任意的标准操作的方法名称（“如何获取物件的数量?”它是.size().length()还是什么?）

2.从丰富的Python标准库获益和避免重新发明轮子这是很容易的，例如random.choice函数。

但是还有更好的。

因为我们的`__getitem__`代表着`self._cards的[]`操作符，我们的一副牌自动支持切片。
这是我们如何从全新的一副牌看顶上的三张牌，然后通过在12开始，一次跳13个卡片拿起A纸牌：
```
>>> deck[:3]
[Card(rank='2', suit='spades'), Card(rank='3', suit='spades'), Card(rank='4', suit='spades')]
>>> deck[12::13]
[Card(rank='A', suit='spades'), Card(rank='A', suit='diamonds'), Card(rank='A', suit='clubs'), Card(rank='A', suit='hearts')]
```
仅仅需要实现`__getitem__`方法，我们的一副牌也是可以进行遍历的：
```
>>> for card in deck: # doctest: +ELLIPSIS 
... print(card)
Card(rank='2', suit='spades') 
Card(rank='3', suit='spades') 
Card(rank='4', suit='spades')
...
```
也可以反向遍历这副牌：

```
>>> for card in reversed(deck): # doctest: +ELLIPSIS 
... print(card)
Card(rank='A', suit='hearts')
Card(rank='K', suit='hearts')
Card(rank='Q', suit='hearts')
...
```
###tips：doctests中的省略
只要有可能，在这本书的编写确保精度的在Python控制台列表中提取。
当输出太长，省略部分用省略号标示…像在上面的最后一行。
在这种情况下，我们使用的# doctest：+ELLIPSIS省略指令使doctest通过。
如果你在控制台交互，在这些例子中，你可以一起省略doctest指令。

迭代往往是隐式的。如果一个集合没有`__contains__`方法，那么in操作做顺序扫描。
案例：in操作于我们的frenchdeck类，因为它是可迭代的。过来验证一下:
```
>>> Card('Q', 'hearts') in deck 
True
>>> Card('7', 'beasts') in deck 
False
```
排序怎么样？
一个常见的排序卡系统是通过等级（设置A为最高），然后以花色排名：黑桃（最高），然后红心、方块和梅花（最低）。
这里是一个根据上面规则制定的牌规则函数，梅花2返回0和黑桃A返回51：
```
suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)
def spades_high(card):
rank_value = FrenchDeck.ranks.index(card.rank)
return rank_value * len(suit_values) + suit_values[card.suit]
```
有了spades_high函数，我们现在可以在我们的一副牌中增加列表排序：
```
>>> for card in sorted(deck, key=spades_high): # doctest: +ELLIPSIS 
... print(card)
Card(rank='2', suit='clubs')
Card(rank='2', suit='diamonds')
Card(rank='2', suit='hearts') 
... (46 cards ommitted) 
Card(rank='A', suit='diamonds') 
Card(rank='A', suit='hearts') 
Card(rank='A', suit='spades')
```
虽然frenchdeck隐式继承自object（在python2中你需要直接写出来FrenchDeck(object)，但在python3中就默认继承），它的功能是不是遗传的，而是来自于利用数据模型和composition。
通过实现特殊的方法`__len__`和`__getitem__`。我们frenchdeck表现得像一个标准的Python序列，允许它从核心语言功能获益，好比迭代和切片，同时从标准库获益，就像例子中所使用的dom.choice, reversed和sorted。
多亏了composition，`__len__` 和 `__getitem__` 的方法可以把所有工作交给一个列表对象，`self._cards`
###tips 如何洗牌
到现在，一个FrenchDeck卡牌对象都不能被洗牌，因为它是不可改变的，除非违反封装和直接处理_cards属性。在11章中，这些bug将被一个一行的添加下划线的`__setitem__`方法修正。
