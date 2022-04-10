# Model Server

提供对分子1D、2D和分子的3D(亚)结构模型的访问。子结构由 mol-script (MolQL)语言描述。它能够“动态”将额外数据纳入mmCIF，例如整合来自[Chemical Component Dictionary (CCD)](https://www.wwpdb.org/data/ccd),
[Protonation Variants Companion Dictionary (PVCD)](https://www.wwpdb.org/data/ccd) 和
[Biologically Interesting moleculeReference Dictionary (BIRD)](https://www.wwpdb.org/data/bird) 的主要PDB档案数据。

## 例子（Example）

```sh
node lib/commonjs/servers/model/server --sourceMap pdb-bcif '/opt/data/bcif/${id}.bcif'
```

## 用法（Usage）

| 参数 | 描述 |
| --- | --- |
| `--version`, `-v` | 显示版本号 |
| `--cfg` | JSON配置文件路径。如果未指定，则使用命令行参数/OS变量/默认值。 |
| `--printCfg` | 打印当期配置信息 |
| `--cfgTemplate` | 打印相对于默认配置的修改 |
| `--apiPrefix` | 指定 API 前置补全路径, i.e. &lt;host&gt;/&lt;apiPrefix&gt;/&lt;API queries&gt; |
| `--defaultPort` | 指定运行端口 |
| `--cacheMaxSizeInBytes` | 读取结构（Read structures）缓存：指定缓存大小，0表示off |
| `--cacheEntryTimeoutMs` | 以毫秒为单位指定在缓存中保存条目的时间 |
| `--requestTimeoutMs` | 请求超时设置 |
| `--queryTimeoutMs` | 服务器用于执行查询的最大时间(以ms为单位)，不包括读取和导出数据所花费的时间。 |
| `--shutdownTimeoutMinutes` | 服务器将在此时间后关闭，0表示关闭。 |
| `--shutdownTimeoutVarianceMinutes` | 通过+/- ' timeoutVarianceMinutes '(避免多个实例同时关闭)来修改关闭计时器 |
| `--maxQueryManyQueries` | 查询并发数 |
| `--defaultSource` | 修改默认情况下要使用的 sourceMap
| `--sourceMap` | Map `id`s for a `source` to a file path. Example: `pdb-bcif '../../data/bcif/${id}.bcif'` - JS expressions can be used inside `${}`, e.g. `${id.substr(1, 2)}/${id}.mdb` Can be specified multiple times. The `SOURCE` variable (e.g. `pdb-bcif`) is arbitrary and depends on how you plan to use the server. 支持格式: cif, bcif, cif.gz, bcif.gz |
| `--sourceMapUrl` | Same as `--sourceMap` but for URL. `--sourceMapUrl src url format` Example: `pdb-cif "https://www.ebi.ac.uk/pdbe/entry-files/download/${id}_updated.cif" cif` Supported formats: cif, bcif, cif.gz, bcif.gz |

```sh
node lib/commonjs/servers/model/server [-h] [-v]
        [--cfg CFG]
        [--printCfg]
        [--cfgTemplate]
        [--apiPrefix PREFIX]
        [--defaultPort PORT]
        [--cacheMaxSizeInBytes CACHE_SIZE]
        [--cacheEntryTimeoutMs CACHE_TIMEOUT]
        [--requestTimeoutMs REQUEST_TIMEOUT]
        [--queryTimeoutMs QUERY_TIMEOUT]
        [--shutdownTimeoutMinutes TIME]
        [--shutdownTimeoutVarianceMinutes VARIANCE]
        [--maxQueryManyQueries QUERY_MANY_LIMIT]
        [--defaultSource DEFAULT_SOURCE]
        [--sourceMap SOURCE PATH]
        [--sourceMapUrl SOURCE PATH SOURCE_MAP_FORMAT]
```

### Production Use

In production, it is required to use a service that will keep the server running, such as [forever.js](https://github.com/foreverjs/forever).

### 内存问题（Memory Issues）

nodejs 的内存问题添加 ``--max-old-space-size=8192`` 参数解决.

### 预处理（Preprocessor） 示例

The preprocessor application allows addiing custom data to CIF files and/or

预处理器应用程序允许将自定义数据添加到CIF文件，或者 [转换 CIF 为 BinaryCIF](./convert-to-bcif.md)

```sh
node lib/commonjs/servers/model/preprocess
```

### Preprocessor Usage

| Argument | Description |
| --- | --- |
| `--input`, `-i` | Input filename |
| `--outCIF`, `-oc` | Output CIF filename |
| `--outBCIF`, `-ob` | Output BinaryCIF filename |
| `--cfg`, `-c` | Config file path |
| `--folderIn`, `-fin` | Convert folder |
| `--folderOutCIF`, `-foc` | Convert folder text output |
| `--folderOutBCIF`, `-fob` | Convert folder binary output |
| `--folderNumProcesses`, `-fp` | Convert folder number processes |

Example cfg.json:

```ts
{ 
    "numProcesses": 1, 
    "customProperties": { 
        "sources": [ "wwpdb" ], 
        "params": { 
            "wwPDB": { 
                "chemCompBondTablePath": "./build/data/ccb.bcif"
            }
        }
    }
}
```

### 本地方式（Local Mode）

The server can be run in local/file based mode using

```sh
node lib/commonjs/servers/model/query
```

### 自定义属性（Custom Properties）

该功能仍在开发中。

It is possible to provide property descriptors that transform data to internal representation and define how it should
be exported into one or mode CIF categories. Examples of this are located in the  module and are
linked to the server in the config and ``servers/model/properties``.
可以提供将数据转换为内部表示形式的属性描述符，并定义它应该如何被导出到一个或多个CIF模式类别中。这方面的示例位于``mol-model-props``模块中，包括链接到配置和``servers/model/properties``中的服务器。
