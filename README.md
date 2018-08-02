@ -1,271 +0,0 @@

# layaair-cmd

对原本版本的修改：

* 对原本的LayaAir-cmd导出UI代码及资源方面进行了修复，使得可以正常导出LayaAir:1.7.18版本的UI资源.
* ui导出添加了新的参数：‘customDict’-自定义UI XML文件所在路径的设置
* 添加IDE 1.7.18版本的发布功能，参照：${IDE_PATH}/resources/app/out/layarepublic/publishpro2的实现来修改的

[TOC]

**layaair-cmd**是**layaair**的命令行工具，可以使用**layaair-cmd**不打开**IDE**的情况下对**layaair**项目进行编译发布等操作。它包含以下功能，这些功能都对应一个子命令。

| 功能             | 子命令             |
| -------------- | --------------- |
| 编译             | compile         |
| 发布             | publish         |
| 导出UI           | ui              |
| 资源版本控制         | resourceVersion |
| 使用guetzli压缩jpg | guetzli         |
| 打开静态文件服务器      | open            |



##  安装

```shell
$ npm install layaair-cmd -g
```



## CLI

**layaair-cmd**的命令类似**git**命令，它的形式是：

```shell
$ layaair-cmd [command] [args]
```

比如编译项目：

```shell
$ layaair-cmd compile
```

或者查看帮助信息：

```shell
$ layaair-cmd --help
```

不仅**layaair-cmd**本身，所有的子命令都有版本信息和帮助信息，查看子命令帮助信息：

```shell
$ layaair-cmd command -h
```

**layaair-cmd**的大部分子命令都需要当前工作目录下包含**layaair**项目，少数命令可以手动指定输入目录，如`guetzl`，`atlas`命令则是即可以直接`$ layaair-cmd atlas`，也可以指定输入目录。



## 编译

```shell
$ layaair-cmd compile -h

  Usage: layaair-cmd compile [options]

  Options:

    -h, --help     output usage information
    -V, --version  output the version number
```

如果当前目录有**layaair**项目，该命令会生成编译后的**JavaScript**文件。编译只有**ActionScript**和**TypeScript**项目需要，如果是**JavaScript**，则什么也不做。

#### 使用

```shell
$ layaair-cmd compile
```



##  发布

```shell
$ layaair-cmd publish -h

  Usage: layaair-cmd publish [options]

  Options:

     -V, --version               output the version number
     --sourcePath <path>         源根目录
     --outPath <path>            发布目录
     --pngQualityLow <quality>   PNG 最小质量
     --pngQualityHigh <quality>  PNG 最大质量
     --jpgQuality <quality>      JPG 质量
     --mergeJs                   是否合并index.html内的所有js文件（小游戏必选）
     --compressPng               是否压缩 PNG (耗时)
     --compressJpg               是否压缩 JPG (非常耗时，但效果很好)
     --compressJson              是否压缩 JSON
     --jsontypes <suffix>        JSON 文件后缀, 默认:json,atlas,ls,lh,lmat,lav,prefab
     --compressJs                是否压缩混淆 JS (耗时)
     --enableVersion             是否启用版本管理，启用后，会重命名变化的文件
     --excludeFiles <files>      排除文件
     --publishType <platform>    发布平台: webnative(默认), wxnimigame, qqwanyiwan
     --projectType               项目类型: as, ts(默认), js
     --copyOutFiles <files>      复制文件列表
     --copyOutPath <path>        复制文件到
     --userCmd <cmd>             后续执行脚本
     -h, --help                  output usage information
```

如果当前目录有**layaair**项目，该命令会生成发布后的**JavaScript**文件，发布的文件夹在*release*下面。

#### 使用

```shell
$ layaair-cmd publish --sourcePath ./bin --outPath ./release/web --excludeFiles ./src --enableVersion # 开启版本管理功能
```

## 导出UI

```shell
$ layaair-cmd ui -h

  Usage: layaair-cmd ui [options]

  Options:

    -V, --version     output the version number
    -c --clear        clear will delete old ui code file.
    -a --atlas        generate atlas
    -d --code         generate ui code files
    --customDict      custom ui xml directory complete path
    -m --mode <mode>  'normal'或者'release'，指定'release'会生成除未使用资源外的UI代码文件
    -h, --help        output usage information
```

如果当前目录有**layaair**项目，该命令为UI页面导出与UI相关的文件。

#### 使用

```shell
$ layaair-cmd ui -c -m release # 导出前清理，并且把mode设置为release
$ layaair-cmd ui -d # 导出UI代码文件
$ layaair-cmd ui -a # 导出图集文件
```



## 资源版本控制

**资源版本控制**用于为资源生成版本。版本号默认从数字1000开始递增，如果传入`--versionName`参数，则使用用户指定的版本名称。下次建立建立版本时如果没有再次指定`--name`，版本号为1002，因为每次生成版本，**资源版本控制**内部版本计数器都会递增。

在建立版本时，相对于上次版本建立，修改了的文件或者新增的文件会被记录在新版本中。如果没有新增文件或者没有修改文件，不会有新版本生成。

> 在最终使用资源时，不允许使用上层相对路径，即路径中包含“..”。

```shell
$ layaair-cmd resourceVersion -h

  Usage: layaair-cmd resourceVersion [options]

  Options:

    -h, --help                       output usage information
    -V, --version                    output the version number
    -i --input <input>               资源目录
    -o --output <output>             导出目录
    -n --versionName <version name>  版本名称，默认是从1000开始递增的数字
```

该命令不需要当前目录包含**layaair**项目，取而代之的是，你需要指定输入目录。



#### 使用

```shell
$ layaair-cmd resourceVersion -i input_dir -o output_dir -n 1.1.0
# 指定了输入目录、输出目录和版本名称
```



#### 生成的文件

> 1000
>
> > resources...
>
> 1001
>
> > resources...
>
> 1002
>
> > resources...
>
> .record
>
> manifest.json



#### 资源版本

名为*1000*、*1001*、*1002*的文件夹是默认资源版本名称，里面保存的是对应版本被修改了的资源。根据从*manifest.json*中得到的每个资源的最新版本号，从这些文件夹读取*manifest.json*对应的版本资源。



#### .record记录文件

*.record*在**Unix-like**系统中是隐藏文件。这个文件保存最近的版本建立信息，**资源版本控制**由此来判定建立新版本时哪些文件被修改。这个文件不能被删除，如果这个文件丢失，之前建立的版本就会丢失，相当于重新开始建立版本。



#### manifest.json资源清单

用户根据*manifest.json*获取最新的资源。该文件包含资源键值对：

```json
{
    "res1": "1000",
    "res2": "1000",
    "res3": "1002",
    "sub\\res3": "1000",
    "sub\\res4": "1000"
}
```

用户从该映射中获取资源对应的版本号后，使用`资源根目录/版本号/相对文件路径`得到资源的url，加载并使用。



#### 资源版本切换

由于*manifest.json*保存各版本的文件版本号。所以只需要保留历史*manifest.json*即可使用对应版本的资源。


## guetzli

**guetzli**是**google**的开源**jpeg**编码器。关于它的介绍、注意事项等参见官方https://github.com/google/guetzli。

**guetzli**的压缩过程很慢，而且占用资源大，所以可能要等待些时间。

最好在**资源版本控制**生成的文件夹中使用**guetzli**压缩，这可以保证不会重复压缩一张图。

```shell
$ layaair-cmd guetzli -h

  Usage: layaair-cmd guetzli [options]

  Options:

    -h, --help              output usage information
    -V, --version           output the version number
    -i --input <input>      resource directory.
    -q --quality <quality>  quality, more than 84.
```

该命令不需要当前目录包含**layaair**项目，取而代之的是，你需要指定输入目录。压缩成功后，源文件会被修改。压缩失败则源文件保持不变。

#### 使用

```shell
$ layaair-cmd guetzli -i input_dir -q 95
# 指定了压缩率95
```



## 打开静态文件服务器

```shell
$ layaair-cmd open -h

  Usage: layaair-cmd open [port] [args]

  Options:

    -h, --help     output usage information
    -V, --version  output the version number
    -p <port>      resource directory.
    -s             don't open browser
```

该命令需要在与**layaair**项目拥有同样结构的目录中使用。对于**ActionScript**项目，它会在*./bin/h5*开启静态文件服务器；对于**JavaScript**和**TypeScript**项目，它会在**./bin**开启静态文件服务器；
