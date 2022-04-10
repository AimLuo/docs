# 视图（Viewer） 状态管理

## ``Canvas3D`` Properties

[``Canvas3D``](https://github.com/molstar/molstar/blob/master/src/mol-canvas3d/canvas3d.ts) 的属性可以通过 [``PluginCommands``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/commands.ts) 修改  

### 改变背景色，高亮，选中色

```ts
import { ColorNames } from 'molstar/lib/mol-util/color/names';
import { PluginCommands } from 'molstar/lib/mol-plugin/commands';

const renderer = plugin.canvas3d!.props.renderer;
PluginCommands.Canvas3D.SetSettings(plugin, { settings: { renderer: { ...renderer, backgroundColor: ColorNames.red /* or: 0xff0000 as Color */ } } });
```

类似的, `highlightColor` 和 `selectColor` 可以被更新

## 交互（interactivity）

Mol* 的交互是基于 ``Loci``（点，坐标的集合？） 的概念. ``Loci`` 通常引用一组对象，并且可以通过 [``Selection``](selections.md) 创建。 For example, the
``Loci`` 通过蛋白质的label_asym_id B 捕获链中的所有原子:

```ts
import { Script } from 'molstar/lib/mol-script/script';
import { StructureSelection } from 'molstar/lib/mol-model/structure/query';

const data = plugin.managers.structure.hierarchy.current.structures[0]?.cell.obj?.data;
if (!data) return;

const selection = Script.getStructureSelection(Q => Q.struct.generator.atomGroups({
    'chain-test': Q.core.rel.eq(['B', Q.ammp('label_asym_id')])
}), data);
const loci = StructureSelection.toLociWithSourceUnits(selection);
```

一个 ``Loci`` 可以用于触发 [``Behaviors``](#behaviors).

### Mol* 日志

内置的日志信息位于底部中央

```ts
plugin.log.message('This message will appear in the Mol* console');
```

其他日志等级有: `info`, `warn`, and `error`.

### 显示 Toast 信息

Toast 消息将出现在插件的右下方，并在消失前停留一段有限的时间。

```ts
import { PluginCommands } from 'molstar/lib/mol-plugin/commands';

PluginCommands.Toast.Show(plugin, {
    title: 'Custom Message',
    message: 'A custom toast message that will disappear after 2 seconds.',
    key: 'toast-custom',
    timeoutMs: 2000
});
```

## 行为（Behaviors）

Mol*插件的状态通常由动态行为（behaviors）控制，动态行为（behaviors）可以在初始化时设置，也可以在运行时更新。这允许单个插件实例的高度模块化和可定制性。

### 高亮（Highlight） ``Loci``

高亮会短暂显示，直到鼠标移动到其他对象上。可以按下面方法设置 ``Loci`` 高亮:

```ts
plugin.managers.interactivity.lociHighlights.highlightOnly({ loci });
```

重置所有高亮:

```ts
plugin.managers.interactivity.clearHighlights();
```

### 选中（Select） ``Loci``

Selected elements will appear with distinct visuals and, if applicable, the corresponding sequence positions will be
shown in the Sequence Viewer panel. Selections persist until removed, for example by clicking the background. A ``Loci``
is selected by:
选定的元素将以不同的视觉效果出现，如果适用，相应的序列位置将显示在 `Sequence Viewer panel` 中。选择会一直保留，直到被移除，例如通过单击背景。`Loci` 通过以下方式选择:

```ts
plugin.managers.interactivity.lociSelects.select({ loci });
```

取消选中 ``Loci``:

```ts
plugin.managers.interactivity.lociSelects.deselect({ loci });
```

取消所有选中:

```ts
plugin.managers.interactivity.lociSelects.deselectAll();
```

### 聚焦（Focus） ``Loci``

The focus representation shows a ``Loci`` in ball-and-stick representation and, additionally, visualizes non-covalent
interactions between atoms of the ``Loci`` as well as interactions with surrounding residues (default: 5 Å).
> 显示啥残基之类的

```ts
plugin.managers.structure.focus.setFromLoci(loci);
```

添加:

```ts
plugin.managers.structure.focus.addFromLoci(loci);
```

重置:

```ts
plugin.managers.structure.focus.clear();
```

### 缩放（Zoom） ``Loci``

聚焦

```ts
plugin.managers.camera.focusLoci(loci);
```

重置

```ts
plugin.managers.camera.reset();
```
