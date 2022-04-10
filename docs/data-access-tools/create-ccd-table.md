# Create Table from CCD

[Chemical Component Dictionary (CCD)](https://www.wwpdb.org/data/ccd) 作为外部参考文件，描述了在PDB条目中发现的所有残留物和小分子成分。[Protonation Variants Companion Dictionary (PVCD)](https://www.wwpdb.org/data/ccd) 列举了典型氨基酸的质子化变体。

This script bundles all `chem_comp_bond` information from the CCD and the PVCD into a single file for later use.
Optionally, it can also generate a second output file that contains all `chem_comp_atom` information.

该脚本将来自 CCD 和 PVCD 的所有chem_comp_bond信息打包成一个文件，以备后用。或者，它也可以生成包含所有chem_comp_atom信息的第二个输出文件（it can also generate a second output file that contains all chem_comp_atom information）。

## Example

```sh
node --max-old-space-size=4096 lib/commonjs/cli/chem-comp-dict/create-table.js build/data/ccb.bcif -b
```

## Usage

| Argument | Description |
| --- | --- |
| `out` | Generated file output path |
| `--forceDownload`, `-f` | Force download of CCD and PVCD |
| `--binary`, `-b` | Output as BinaryCIF |
| `--ccaOut`, `-a` | File output path of optionally generated chem_comp_atom |

```sh
create-table.js [-h] [--forceDownload] [--binary] [--ccaOut CCAOUT] out
```
