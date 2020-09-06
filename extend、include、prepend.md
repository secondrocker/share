### module
module定义了一个命名空间，可把方法、类、常亮组合在一起；ruby没有多继承，使用module的混入可以实现多继承的效果。
;module类似于类(确切地说是特殊的class)，但不能：
- 不能实例化
- 没有子类（不能被继承）
```ruby
module Motion
  def move_left
    @position ||= {x: 1, y: 1}
    @position.merge! x: @position[:x] - 1
    puts "move to x:#{@position[:x]},y:#{@position[:y]}"
  end
end
```
### include、included
在类内include混入module的方法为类的实例方法
```ruby
class A
  include Motion
end
A.new.move_left #move to x:0,y:1
```
included钩子在module被include时触发，可以在被混入时执行一些操作,如下面代码通过base.extend实现“类扩展混入”
```ruby
module Motion
  def move
    puts "#{self.class}" #这里self是 混入module的类的实例(调用这个方法的实例)
    puts "move"
  end

  def self.included(base)#base就是要混入module的类
    base.extend ClassMethods
  end

  module ClassMethods
    def class_move
      puts "class_move"
    end
  end
end

class A
  include Motion
end

A.new.move #A,move
A.class_move #class_move
```

### extend、extended
extend在类内混入module的方法为类的类方法
```ruby
module Motion
  def move
    puts "move"  
  end
end

class A
  extend Motion
end
A.move #move
```
extended是在module被extend触发的钩子
```ruby
module Motion
  def move
    puts "move"  
  end
  def self.extended(base)
    puts "#{base} extend Motion"
  end
end
class A
  extend Motion # A extend Motion
end
A.move #move
```
### prepend、prepended(ruby2.0引入)
include时如果要扩展的类内有和module同名的实例方法，调用时会调用类内定义的实例方法的（方法查找顺序，下面讲到）,如果要覆盖类内定义的实例方法，可使用prepend混入(prepended与included类似,在混入后触发)
```ruby
module Motion
  def move
    puts "module method move"
  end
end
class A
  include Motion
  def move
    puts "instance method move"
  end
end
A.new.move #instance method move

class B
  prepend Motion
  def move
    puts "instance method move"
  end
end
B.new.move #module method move
```
### 继承顺序，Method lookup 顺序
继承或混入时会改变类的祖先链（重复混入不再起作用），方法查找是按照祖先链顺序查找
```ruby
module Motion
  def move
    puts "module move"
  end
end

module Appearance
  def face
    puts "face"
  end
end

class A
  def move
    puts "A move"
  end
end

class B < A
  include Motion
  prepend Appearance
end
B.ancestors #[Appearance, B, Motion, A, Object, Kernel, BasicObject] 
```

### rails对扩展的封装
rails在[ActiveSupport::Concern](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/concern.rb)对ruby的扩展进行了一些扩展，主要解决依赖问题
```ruby
require 'active_support/concern'
module A
  extend ActiveSupport::Concern
  included do# self是include A的类/module
    def instance_method1

    end

    def instance_method2

    end
    def self.class_method3

    end
  end
  class_methods do
    def class_method1

    end

    def class_method2

    end
  end
end

module B
  extend ActiveSupport::Concern
  include A
  class_methods do
    def class_method1
      puts 'B class method1'
    end
  end
end

class X
  include B

  def self.class_method1
    puts "X class method1"
  end
end
X.class_method1 # 'X class method1'
X.class_method2
X.class_method3
X.new.instance_method1
X.new.instance_method2
X.ancestors # [X, B, A, Object, Kernel, BasicObject]



```