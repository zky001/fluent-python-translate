##字符串表示形式
`__repr__`特殊方法被内置为得到对象的字符串表示形式的repr调用。如果我们没有实现`__repr__`，向量实例在控制台显示就会如 `<Vector object at 0x10e100070>`形式

表达式求值的结果,交互式控制台和调试器会调用repr，正如`% r`占位符在典型情况下的格式是%运算符，`!r `转换符在新的[格式字符串语法](https://docs.python.org/3/library/string.html#format-string-syntax)用于str.format方法
