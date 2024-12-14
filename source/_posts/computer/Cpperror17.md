---
title: 在VScode中改变解析代码所使用的C++标准
category: Computer
tag:
    - C++
    - vscode
---

改变编译源码所使用的编译器是很容易的，但如果标准不对，VScode的解析会报红色的error，这很烦人，例如：

```cpp
#include <algorithm>
double main(){
    double a = 2.0;
    double b = std::clamp(a, 1.0, 3.0);
    return(b);
}
```

由于std::clamp是C++17中才引入的标准，但VScode默认会按照C++14来解析，所以会报错。

那么解决方法我们可以按`Ctrl + Shift + P`，选择C/C++对应的配置文件(JSON)打开，在里面修改配置如下：

```json
{
    "configurations": [
        {
            "name": "Linux",
            "includePath": [
                "${workspaceFolder}/**",
                "/usr/include/python3.8"
            ],
            "defines": [],
            "compilerPath": "/usr/bin/gcc",
            "cStandard": "c17",
            "cppStandard": "c++17",
            "intelliSenseMode": "linux-gcc-x64"
        }
    ],
    "version": 4
}
```

把"cppStandard"对应项改为"c++17"即可，然后VScode即可正常解析。