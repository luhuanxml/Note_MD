# Layout布局文件分类存放

### 我一般分为 activity  fragment  include  item  (view//有时候不需要)

### 在layout文件夹下新建这几个文件夹，然后在这几个文件夹下分别新建一个layout文件夹
> layout / activity / layout
> layout / fragment / layout
> layout / include / layout
> layout / item / layout
> layout / view / layout

### 然后再app moudle 中的android{} 中加入一下代码

```
 sourceSets {
        main {
            res.srcDirs = [
                    'src/main/res/layout/activity',
                    'src/main/res/layout/fragment',
                    'src/main/res/layout/include',
                    'src/main/res/layout/item',
                    'src/main/res/layout',
                    'src/main/res'
            ]
        }
    }
```

### 最后build gradle 。

