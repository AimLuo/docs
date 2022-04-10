# 创建 Molstar 实例

有四个方法来使用 Mol*

## 使用 ``Viewer``

> molstar 提供了一个 已经集成 Molstar 的 viewer app

- 最基础的方式是使用 ``Viewer``。 这最适合于不需要太多自定义行为并且主要是显示结构的用例。
- 从 [src/apps/viewer/app.ts](https://github.com/molstar/molstar/blob/master/src/apps/viewer/app.ts) 查看`Viewer`的配置和方法

不使用WebPack的使用示例:

```HTML
<style>
    #app {
        position: absolute;
        left: 100px;
        top: 100px;
        width: 800px;
        height: 600px;
    }
</style>
<!-- 
    molstar.js 和 .css 来源于
    - 源码编译后，/build/viewer
    - NPM package 方式，位于 molstar/build/viewer 中
-->
<link rel="stylesheet" type="text/css" href="molstar.css" />
<script type="text/javascript" src="./molstar.js"></script>

<div id="app"></div>

<script type="text/javascript">
    var viewer = new molstar.Viewer('app', {
        layoutIsExpanded: false,
        layoutShowControls: false,
        layoutShowRemoteState: false,
        layoutShowSequence: true,
        layoutShowLog: false,
        layoutShowLeftPanel: true,

        viewportShowExpand: true,
        viewportShowSelectionMode: false,
        viewportShowAnimation: false,

        pdbProvider: 'rcsb',
        emdbProvider: 'rcsb',
    });
    viewer.loadPdb('7bv2');
    viewer.loadEmdb('EMD-30210', { detail: 6 });
</script>
```

拥有 `webpack` 等构建工具

```ts
import { Viewer } from 'molstar/build/viewer/molstar'

function initViewer(target: string | HTMLElement) {
    return new Viewer(target, { /* options */})
}
```

## 使用 ``PluginContext``（通过内置的 React UI ）

- 当需要更多定制选项时，可以直接使用 [``PluginContext``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/context.ts)
- 创建示例时可以定制 [``PluginSpec``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/spec.ts).
- 默认 [``PluginSpec``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/spec.ts)
- [``PluginConfig``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/config.ts) 提供额外的自定义选项
- 查看 [Viewer State Management](viewer-state.md)，了解更多自定义信息，比如背景色。
- 有关构建状态的更多信息，请参见 [Data State Management](data-state.md)。

```ts
import { DefaultPluginUISpec, PluginUISpec } from 'molstar/lib/mol-plugin-ui/spec';
import { createPluginAsync } from 'molstar/lib/mol-plugin-ui/index';
import { PluginConfig } from 'molstar/lib/mol-plugin/config';

const MySpec: PluginUISpec = {
    ...DefaultPluginUISpec(),
    config: [
        [PluginConfig.VolumeStreaming.Enabled, false]
    ]
}

async function createPlugin(parent: HTMLElement) {
    const plugin = await createPluginAsync(parent, MySpec);

    const data = await plugin.builders.data.download({ url: '...' }, { state: { isGhost: true } });
    const trajectory = await plugin.builders.structure.parseTrajectory(data, format);
    await this.plugin.builders.structure.hierarchy.applyPreset(trajectory, 'default');

    return plugin;
}

createPlugin(document.getElementById('app')!); // app is a <div> element
```

在其他 react 项目中使用内置的 React UI

```ts
function MolStarWrapper() {
    const parent = React.createRef<HTMLDivElement | null>();

    useEffect(() => {
        let plugin: PluginContext | undefined = undefined;
        async function init() {
            plugin = createPlugin(parent.current);
        }
        init();
        return () => { plugin?.dispose(); };
    }, []);

    return <div ref={parent} style={{ width: 640, height: 480 }} />;
}
```

## ``PluginContext``（不通过内置 React UI 使用）

- [``PluginContext``](https://github.com/molstar/molstar/blob/master/src/mol-plugin/context.ts) 可以不通过内置 UI 实例化

```HTML
<div id='molstar-parent' style='position: absolute; top: 0; left: 0; right: 0; bottom: 0'>
    <canvas id='molstar-canvas' style='position: absolute; top: 0; left: 0; right: 0; bottom: 0'></canvas>
</div>
```

```ts
import { DefaultPluginSpec, PluginSpec } from 'molstar/lib/mol-plugin/spec';
import { PluginContext  } from 'molstar/lib/mol-plugin/context';
import { PluginConfig } from 'molstar/lib/mol-plugin/config';

const MySpec: PluginSpec = {
    ...DefaultPluginSpec(),
    config: [
        [PluginConfig.VolumeStreaming.Enabled, false]
    ]
}

async function init() {
    const plugin = new PluginContext(MySpec);
    await plugin.init();

    const canvas = <HTMLCanvasElement> document.getElementById('molstar-canvas');
    const parent = <HTMLDivElement> document.getElementById('molstar-parent');

    if (!plugin.initViewer(canvas, parent)) {
        console.error('Failed to init Mol*');
        return;
    }

    const data = await plugin.builders.data.download({ url: '...' }, { state: { isGhost: true } });
    const trajectory = await plugin.builders.structure.parseTrajectory(data, format);
    await plugin.builders.structure.hierarchy.applyPreset(trajectory, 'default');
}

```

## 使用 ``Canvas3D``（不通过内置状态管理）

- 上面例子中的 `PluginContext` 对象可以完全省略。
- 查看 [Browser Tests](https://github.com/molstar/molstar/tree/master/src/tests/browser) 示例

```ts
const canvas = document.getElementById('canvas'); // parent <canvas> element
const canvas3d = Canvas3D.create(Canvas3DContext.fromCanvas(canvas));
canvas3d.animate();
// use the canvas3d object here
```
