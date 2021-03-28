## 面向对象的标准库
IO 类型在三个独立的头文件中定义：
- `iostream` 定义读写控制窗口类型
- `fstream` 定义读写已命名文件的类型
- `sstream` 定义的类型则用于读写存储在内存中的 `string` 对象

在 `fstream` 和 `sstream`里定义的每种类型都是从 `iostream` 头文件中定义的相关类型派生而来

| 头文件 | 类型 |
| :---- | :---- | 
| `iostream` | `istream` 从流中读取</br>`ostream` 写到流中去</br>`iostream`|
| `fstream` | `ifstream` 从文件中读取</br>`ofstream` 写到文件中去 |
| `sstream` | `istringstream` 从 `string` 对象中读取</br>`ostringstream` 写到 `string` 对象中去 |

### 1. 国际字符的支持
标准库定义了一组相关的类型，支持 `wchar_t` 类型。每个类都加上 `w` 前缀，于 `char` 类型分开来

### 2. IO 对象不可复制或赋值
一般情况下，如果要传递 IO 对象以便对它进行读写，可用非 `const` 引用的方式传递这个流对象，对 IO 对象的读写会改变它的状态，因此引用必须是非 `const` 的 

## 条件状态
| 条件状态 | 解释 |
| :---- | :---- | 
| `strm::iostate` | `iostate` 是一种机器相关的类型，提供了表达条件状态的完整功能 |
| `strm::badbit` | `strm::ioslate` 类型的值，用于指出被破坏的流 |
| `strm::` | `strm::ioslate` 类型的值，用于指出失败的 IO 操作 |
| `strm::eofbit` | `strm::ioslate` 类型的值，用于指出流已经到达文件结束符 |
| `s.eof()` | 如果设置了流 *s* 的 `eofbit` 值，则该函数返回 `true` |
| `s.fail()` | 如果设置了流 *s* 的 `eofbit` 值，则该函数返回 `true`|
| `s.bad()` | 如果设置了流 *s* 的 `eofbit` 值，则该函数返回 `true` |
| `s.good()` | 如果流 *s* 处于有效的状态，则该函数返回 `true` |
| `s.clear()` | 将流 *s* 中的所有状态值都重设为有效状态 |
| `s.clear(flag)` | 将流 *s* 中的某个指定条件状态设置为有效（*flag* 类型是 `strm::ioslate`）|
| `s.setstate(flag)` | 给流 *s* 添加指定条件（*flag* 类型是 `strm::ioslate`）|
| `s.rdstate()` | 返回流 *s* 的当前条件，返回值类型为 `strm::ioslate`|

