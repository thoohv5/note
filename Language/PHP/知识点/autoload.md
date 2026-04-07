# autoload

__autoload & spl_autoload_register 自动加载

**__autoload()函数**：尝试加载未定义的类；

函数声明是

void __autoload(string $class)

参数$class是待加载的类名，无返回值

说明：定义了这个函数之后，如果调用了某一个类，但是这个类文件没有加载进来(require,include),那么就会自动调用__autoload()函数，类名就是函数参数(有时候还会带着命名空间)

**spl_autoload_register()函数**：注册给定的函数作为 __autoload 的实现

函数声明是

bool spl_autoload_register ([ callable$autoload_function [, bool $throw = true [, bool $prepend = false ]]] )

这里只说第一个参数，这个参数是回调函数类型的，也就是会传递一个函数的名字给spl_autoload_register()函数

说明：该函数触发规则和__autoload函数一样，也是在没有找到将要实例化的类时，被触发。触发时，使用参数里的回调函数处理，回调函数参数为类名。其实和__autoload()一样，只不过，__autoload只能定义一次，而**spl_autoload_register可多次使用**。