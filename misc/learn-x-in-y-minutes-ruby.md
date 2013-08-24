# [译] 分分钟学会一门语言之 Ruby 篇

> * Original: [Learn X in Y Minutes: Where X = Ruby](http://learnxinyminutes.com/docs/ruby/)
> * Source File: [adambard/learnxinyminutes-docs - GitHub](https://github.com/adambard/learnxinyminutes-docs/blob/master/ruby.html.markdown)
> * Translated by: [cssmagic](https://github.com/cssmagic)

> *声明*：原文版权属于 [David Underwood](http://theflyingdeveloper.com/)、[Joel Walden](http://joelwalden.net/) 和 [Luke Holder](http://twitter.com/lukeholder)，原文以 CC BY-SA 3.0 协议发布。

```ruby
# This is a comment
# 这是一行注释

=begin
This is a multiline comment
No-one uses them
You shouldn't either
多行注释是这样写的，
没人用它，你也不要用它。
=end

# First and foremost: Everything is an object.
# 第一条也是最重要的一条：每样东西都是对象。

# Numbers are objects
# 数字是对象

3.class #=> Fixnum
        # （译注：`class` 属性指向对象所属的类。这里的 Fixnum 即整数类。）

3.to_s #=> "3"
       # （译注：`to_s` 是整数对象的一个方法，其作用是转换为字符串。）


# Some basic arithmetic
# 一些基本运算
1 + 1 #=> 2
8 - 1 #=> 7
10 * 2 #=> 20
35 / 5 #=> 7

# Arithmetic is just syntactic sugar
# for calling a method on an object
# 这些运算符实际上都是语法糖，
# 相当于在对象上调用方法
1.+(3) #=> 4
10.* 5 #=> 50

# Special values are objects
# 特殊值也是对象
nil # Nothing to see here
    # 空值
true # truth
     # 真值
false # falsehood
      # 假值

nil.class #=> NilClass
true.class #=> TrueClass
false.class #=> FalseClass

# Equality
# 等式判断
1 == 1 #=> true
2 == 1 #=> false

# Inequality
# 不等式判断
1 != 1 #=> false
2 != 1 #=> true
!true  #=> false
!false #=> true

# apart from false itself, nil is the only other 'falsey' value
# 除了 false 本身之外，nil 是剩下的唯一假值

!nil   #=> true
!false #=> true
!0     #=> false
       # （译注：这个毁三观啊！）

# More comparisons
# 更多比较操作
1 < 10 #=> true
1 > 10 #=> false
2 <= 2 #=> true
2 >= 2 #=> true

# Strings are objects
# 字符串当然还是对象

'I am a string'.class #=> String
"I am a string too".class #=> String
# （译注：用单引号或双引号来标记字符串。）

placeholder = "use string interpolation"
"I can #{placeholder} when using double quoted strings"
#=> "I can use string interpolation when using double quoted strings"
# （译注：这里展现了字符串插入方法。）


# print to the output
# 打印输出
puts "I'm printing!"

# Variables
# 变量
x = 25 #=> 25
x #=> 25

# Note that assignment returns the value assigned
# This means you can do multiple assignment:
# 请注意，赋值语句会返回被赋进变量的那个值，
# 这意味着你可以进行多重赋值：

x = y = 10 #=> 10
x #=> 10
y #=> 10

# By convention, use snake_case for variable names
# 按照惯例，变更名使用下划线串连的小写字母
# （译注：因为看起来像一条蛇，这种拼写称作“snake case”）
snake_case = true

# Use descriptive variable names
# 建议使用描述性的变量名
path_to_project_root = '/good/name/'
path = '/bad/name/'

# Symbols (are objects)
# Symbols are immutable, reusable constants represented internally by an
# integer value. They're often used instead of strings to efficiently convey
# specific, meaningful values
# 符号（也是对象）
# 符号是不可修改的、可重用的常量，在内部表示为一个整数值。
# 它们通常被用来代替字符串，来有效地传递一些特定的、有意义的值。

:pending.class #=> Symbol

status = :pending

status == :pending #=> true

status == 'pending' #=> false

status == :approved #=> false

# Arrays
# 数组

# This is an array
# 这是一个数组
[1, 2, 3, 4, 5] #=> [1, 2, 3, 4, 5]

# Arrays can contain different types of items
# 数组可以包含不同类型的元素

array = [1, "hello", false] #=> => [1, "hello", false]

# Arrays can be indexed
# From the front
# 数组可以用索引号来查询，下面是顺序索引查询
array[0] #=> 1
array[12] #=> nil

# Like arithmetic, [var] access
# is just syntactic sugar
# for calling a method [] on an object
# 类似于运算符，[var] 这种查询语法也是语法糖，
# 相当于在对象上调用 [] 方法
array.[] 0 #=> 1
array.[] 12 #=> nil

# From the end
# 下面是逆向索引查询
array[-1] #=> 5

# With a start and end index
# 使用开始和结束索引来查询
array[2, 4] #=> [3, 4, 5]

# Or with a range
# 或者使用范围来查询
array[1..3] #=> [2, 3, 4]

# Add to an array like this
# 用这种方式来向数组追加元素
array << 6 #=> [1, 2, 3, 4, 5, 6]

# Hashes are Ruby's primary dictionary with keys/value pairs.
# Hashes are denoted with curly braces:
# 哈希表是 Ruby 最主要的字典型名值对数据。
# 哈希表用花括号来表示：
hash = {'color' => 'green', 'number' => 5}

hash.keys #=> ['color', 'number']

# Hashes can be quickly looked up by key:
# 哈希表可以通过键名来快速查询：
hash['color'] #=> 'green'
hash['number'] #=> 5

# Asking a hash for a key that doesn't exist returns nil:
# 向哈希表查询一个不存在的键名会返回 nil：
hash['nothing here'] #=> nil

# Iterate over hashes with the #each method:
# 使用 #each 方法来迭代哈希表：
hash.each do |k, v|
  puts "#{k} is #{v}"
end

# Since Ruby 1.9, there's a special syntax when using symbols as keys:
# 从 Ruby 1.9 开始，当使用符号作为键名时，有其特定语法：

new_hash = { defcon: 3, action: true}

new_hash.keys #=> [:defcon, :action]

# Tip: Both Arrays and Hashes are Enumerable
# They share a lot of useful methods such as each, map, count, and more
# 提示：数组和哈希表都是可枚举的。
# 它们拥有很多相似的方法，比如 each、map、count 等等。

# Control structures
# 控制结构

if true
  "if statement" # （译注：条件语句）
elsif false
 "else if, optional" # （译注：可选的 else if 语句）
else
 "else, also optional" # （译注：同样也是可选的 else 语句）
end

for counter in 1..5
  puts "iteration #{counter}"
end
#=> iteration 1
#=> iteration 2
#=> iteration 3
#=> iteration 4
#=> iteration 5

# HOWEVER
# No-one uses for loops
# Use `each` instead, like this:
# 不过，
# 没人喜欢用 for 循环，
# 大家都用 `each` 来代替了，像这样：

(1..5).each do |counter|
  puts "iteration #{counter}"
end
#=> iteration 1
#=> iteration 2
#=> iteration 3
#=> iteration 4
#=> iteration 5

counter = 1
while counter <= 5 do
  puts "iteration #{counter}"
  counter += 1
end
#=> iteration 1
#=> iteration 2
#=> iteration 3
#=> iteration 4
#=> iteration 5

grade = 'B'

case grade
when 'A'
  puts "Way to go kiddo"
when 'B'
  puts "Better luck next time"
when 'C'
  puts "You can do better"
when 'D'
  puts "Scraping through"
when 'F'
  puts "You failed!"
else
  puts "Alternative grading system, eh?"
end

# Functions
# 函数

def double(x)
  x * 2
end

# Functions (and all blocks) implcitly return the value of the last statement
# 函数（包括所有的代码块）隐式地返回最后一行语句的值
double(2) #=> 4

# Parentheses are optional where the result is unambiguous
# 当不会产生歧义时，小括号居然也是可写可不写的。
double 3 #=> 6

double double 3 #=> 12
# （译注：连续省略小括号居然也可以！）

def sum(x,y)
  x + y
end

# Method arguments are separated by a comma
# 方法的参数使用逗号来分隔
sum 3, 4 #=> 7

sum sum(3,4), 5 #=> 12

# yield
# All methods have an implicit, optional block parameter
# it can be called with the 'yield' keyword
# 所有的方法都有一个隐式的、可选的块级参数，
# 它可以通过 `yield` 关键字来调用。

def surround
  puts "{"
  yield
  puts "}"
end

surround { puts 'hello world' }

# {
# hello world
# }


# Define a class with the class keyword
# 使用 class 关键字来定义类
class Human

    # A class variable. It is shared by all instances of this class.
    # 一个类变量。它将被这个类的所有实例所共享。
    @@species = "H. sapiens"

    # Basic initializer
    # 基本的初始化函数（构造函数）
    def initialize(name, age=0)
        # Assign the argument to the "name" instance variable for the instance
        # 把参数 `name` 赋值给实例变量 `@name`
        @name = name
        # If no age given, we will fall back to the default in the arguments list.
        # 如果没有指定 age，我们会从参数列表中获取后备的默认值。
        @age = age
    end

    # Basic setter method
    # 基本的 setter 方法
    def name=(name)
        @name = name
    end

    # Basic getter method
    # 基本的 getter 方法
    def name
        @name
    end

    # A class method uses self to distinguish from instance methods.
    # It can only be called on the class, not an instance.
    # 一个类方法使用开头的 `self` 来与实例方法区分开来。
    # 它只能在类上调用，而无法在实例上调用。
    def self.say(msg)
       puts "#{msg}"
    end

    def species
        @@species
    end

end


# Instantiate a class
# 实例化一个类
jim = Human.new("Jim Halpert")

dwight = Human.new("Dwight K. Schrute")

# Let's call a couple of methods
# 我们试着调用一些方法
jim.species #=> "H. sapiens"
jim.name #=> "Jim Halpert"
jim.name = "Jim Halpert II" #=> "Jim Halpert II"
jim.name #=> "Jim Halpert II"
dwight.species #=> "H. sapiens"
dwight.name #=> "Dwight K. Schrute"

# Call the class method
# 调用类方法
Human.say("Hi") #=> "Hi"

```

***

> 原文版本：2013-07-30 （如果原文已更新，请提醒我。）

***

&copy; Creative Commons BY-NC-ND 3.0 &nbsp; | &nbsp; [我要订阅](http://www.cssmagic.net/blog/subscribe) &nbsp; | &nbsp; [我要捐助](http://www.cssmagic.net/blog/donate)

&nbsp;
> * [参与评论](https://github.com/cssmagic/blog/issues/26)
> * [查看更多文章](https://github.com/cssmagic/blog/issues?state=open)
