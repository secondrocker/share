### 字符串
- #ljust  向右补齐到指定位数 "s".ljust(3,'0') == 's00'
- #rjust 向左补齐到指定位数 "s".rjust(3,'0') == '00s'

### File
- ::basename 获取文件名（不含路径）
- ::dirname 获取路径（不含文件名）
- ::extname 获取文件后缀
- ::expand_path 获取绝对路径，第二个参数为给定路径，无第二参数以当前工作路径起
- ::join 格式化路径  File.join("s/","/b") == "s/b"

### Enumerable
- #exclude? 不包含指定与include相反(rails方法)
- #any? 有任意一个符合
- #all? 全部符合，注意这个判断逻辑是没有不符合的
- #none? 没有一个符合的
- #sort_by 排序
```ruby
[{age:1,gender:0},{age:1,gender:-1}].sort_by{|x| [x[:age],x[:gender]]}
```
- reverse 反转数组
### 数字
- #floor、#ceil
