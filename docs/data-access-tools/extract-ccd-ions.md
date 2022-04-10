# Extract Ions from CCD

[Chemical Component Dictionary (CCD)](https://www.wwpdb.org/data/ccd) 是一份外部参考文件，描述了在PDB条目中发现的所有残留物和小分子成分。

此脚本从 CCD 中提取所有离子，并提供它们的名称作为 TypeScript set。

## Example

```sh
node --max-old-space-size=4096 lib/commonjs/cli/chem-comp-dict/create-ions.js src/mol-model/structure/model/types/ions.ts
```

## Usage

| Argument | Description |
| --- | --- |
| `out` | Generated file output path |
| `--forceDownload`, `-f` | Force download of CCD |

```sh
create-ions.js [-h] [--forceDownload] out
```
