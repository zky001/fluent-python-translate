#1.          python数据模型对象 
Guido的语言设计的审美是惊人的。我见过很多好的语言设计者可以建立理论上美丽的语言，但是没有人会使用，但Guido是一个罕见的可以建立一个，只是在理论上略低于完美但可在写程序感到快乐的编程语言的人。
<p align="right">— Jim Hugunin Jython创造者, AspectJ合伙创建人, .Net DLR架构师</p>
Python最好的一个品质是它的一致性。使用Python工作一阵子后，您可以开始对新的特性做出正确的猜测。

然而，如果您学Python之前学了另一种面向对象的语言，您会发现它使用len(collection)而不是collection.len()可能会感到奇怪。这种奇特的现象是冰山的一角，当正确的理解，是一切我们称之为Pythonic的关键。冰山就相当于Python的数据模型，您可以使用它描述的API来拿最符合习惯的语言特点来构建您自己的对象。

您可以认为数据模型是作为一个框架来描述Python。它形式化语言本身的基本构件的接口，如序列，迭代器，函数，类，上下文管理器等。

在使用任何框架进行编码时，您花了很多时间来实现由框架调用的方法。当您利用Python数据模型时同样如此。Python解释器调用特殊的方法进行基本的对象操作‐情况，往往通过特殊的语法触发。特殊方法的名字总是拼双下划线作为开头和结尾，例如`__getitem__`。好比说，`obj[key]`是由`__getitem__`方法来支持的。要使用`my_collection[key]`，解释器会调用`my_collection.__getitem__(key)`。

特殊的方法名称允许您的对象实现，支持和与基本的语言结构交互作用，如：

* 迭代器; 
* 集合; 
* 属性访问; 
* 操作符重载; 
* 函数和方法调用; 
* 对象的创建和销毁; 
* 字符串表示和格式; 
* 管理上下文等 (i.e. with 块); 

###魔术和dunder 
魔术方法的术语是特殊用法的俚语，但当谈论关于具体方法如`__getitem__`，一些Python开发人员‐走捷径说“under-under-getitem”这是模棱两可，由于语法`__x`还有另一个特殊的意义。但准确发音“under-under-getitem-under-under”是无聊的，所以我效仿作者，Steve Holden老师说“dunder-getitem”。因此，特殊的方法也被称为dunder方法。

##一个python风格的纸牌游戏
下面是一个非常简单的例子，但它只是说明使用`__getitem__`和`__len__`两种方法的威力。
例1-1 使用一个类来表示一副扑克牌：
```
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])

class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]
```
首先要注意的是使用`collections.namedtuple`构建一个简单的类来表示个人卡。因为Python 2.6中namedtuple可以用来构建类，这些类是只是捆绑了属性而没有自定义方法的对象类，如数据库的记录。在这个例子中，我们使用它提供了具有一个很好的代表性的卡，如显示在控制台会话那样：
```
>>> beer_card = Card('7', 'diamonds') 
>>> beer_card
Card(rank='7', suit='diamonds')
```
但这个例子的关键点是frenchdeck类。它很短，但它包括了一个重点。首先，像任何标准的Python集合，一副牌对len()函数返回卡的数量。
```
>>> deck = FrenchDeck() 
>>> len(deck)
52
```
从一副牌中读取特定的卡片，好比第一个或最后一个，应该是和deck[0]或deck[-1]一样容易，这就是`__getitem__`方法提供的访问。
```
>>> deck[0]
Card(rank='2', suit='spades') 
>>> deck[-1]
Card(rank='A', suit='hearts')
```
我们应该创建一个方法来选择一个随机卡吗？不需要！Python已经有一个函数可以从一个序列得到一个随机的元素：random.choice。我们可以使用它在一副牌中获得一张卡：
```
>>> from random import choice 
>>> choice(deck)
Card(rank='3', suit='hearts') 
>>> choice(deck) 
Card(rank='K', suit='spades') 
>>> choice(deck) 
Card(rank='2', suit='clubs')
```
