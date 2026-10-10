# 字符串与常用 STL

## 字符串操作

需要 `<string>`；下标从 0 开始。

| 操作 | 含义 | 注意 |
| --- | --- | --- |
| `s.size()` | 长度 | 返回无符号长度类型 |
| `s += t` / `s.append(t)` | 末尾追加 | 会增加长度 |
| `s.push_back(c)` | 末尾追加一个字符 | `c` 是字符 |
| `s.substr(pos, len)` | 取子串 | 第二个参数是长度，长度过大取到末尾；`pos > size()` 会抛异常 |
| `s.insert(pos, t)` | 在 `pos` 前插入 | `pos == size()` 表示末尾插入 |
| `s.find(t)` | 第一次匹配位置 | 找不到返回 `string::npos` |
| `s1 < s2` | 比较字典序 | 逐个字符比较；公共前缀相同时短串更小 |

```cpp
// 需要 <iostream>、<string>；放在 main 内。
string s = "abc";
s += "de";             // abcde
s = s.substr(1, 3);     // bcd
s.insert(1, "XY");    // bXYcd
auto pos = s.find("XY");
if (pos == string::npos) cout << -1 << '\n';
else cout << pos << '\n';  // 1
```

原笔记中的“追加、截取、插入、查找”四种操作都可以用以上接口直接完成。

**空字符串不能靠下标赋值来追加内容。** 下标用于读写已有字符，正常可修改的字符位置满足 `i < s.size()`。

```cpp
// 需要 <string>；放在 main 内。
string s;
s.push_back('a');  // 正确：扩展长度
s[0] = 'b';        // 正确：修改已有字符
s.resize(5, '0');  // b0000，可继续修改下标 0..4
```

## `vector` 与范围遍历

```cpp
// 需要 <vector>；放在 main 内。
vector<int> v = {1, 2, 3};
v.assign(5, 0);              // 替换全部内容，变成 5 个 0
v.assign({4, 5, 6});         // 替换为给定序列
for (int x : v) { (void)x; } // 按值遍历，不改变 v
for (int& x : v) x += 1;     // 按引用遍历，可以修改 v
```

`assign` 会覆盖原内容；`resize` 改变大小时会保留仍在范围内的已有元素。

## 排列枚举

需要 `<algorithm>`。`next_permutation` 把序列改为下一种字典序排列，存在下一种时返回 `true`；已经是最后一种时恢复到最小排列并返回 `false`。完整枚举应先排序。

```cpp
// 需要 <algorithm>、<iostream>、<vector>；放在 main 内。
vector<int> a = {1, 2, 3};
sort(a.begin(), a.end());
do {
    for (int x : a) cout << x << ' ';
    cout << '\n';
} while (next_permutation(a.begin(), a.end()));
```

有重复值时枚举不同的数值排列。不同元素的排列数为 `n!`，只适合较小规模。

标准库接口核对：[Microsoft `<algorithm>` 文档](https://learn.microsoft.com/en-us/cpp/standard-library/algorithm?view=msvc-170)。
