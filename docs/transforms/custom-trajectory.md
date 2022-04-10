# 通过自定义格式加载 Trajectory

本节展示了一个从专用 Mol* 实例中的自定义数据加载 trajectory 的高级示例。 更完整示例查看 [G3D format extension](https://github.com/molstar/molstar/tree/master/src/extensions/g3d).

## 定义和使用自定义转换器

```ts
import { StateTransformer } from 'molstar/lib/mol-state';

const CreateTransformer = StateTransformer.builderFactory('custom-namespace');

export interface CustomTrajectoryData {
    // ...
}

export const TrajectoryFromCustomData = CreateTransformer({
    name: 'trajectory-from-custom-data',
    display: 'Trajectory',
    from: PluginStateObject.Root,
    to: PluginStateObject.Molecule.Trajectory,
    params: {
        data: PD.Value<CustomTrajectoryData>(void 0 as any, { isHidden: true }),
    },
})({
    apply({ params }) {
        return Task.create('Trajectory', async (ctx) => {
            const models = await customParse(params.data, ctx);
            return new PluginStateObject.Molecule.Trajectory(models, {
                label: 'Trajectory',
            });
        });
    },
});
```

自定义解析器（``customParse``）可以通过修改和扩展 [existing parser already available in Mol*](https://github.com/molstar/molstar/tree/master/src/mol-model-formats/structure) 来实现

用转换器

```ts
const data: CustomTrajectoryData = await (await fetch(url)).json();
const trajectory = await plugin.build().toRoot().apply(TrajectoryFromCustomData, { data }).commit();
// Create the representation
await plugin.builders.structure.hierarchy.applyPreset(trajectory, 'default');
```

## 通过 Mol* 加载（download）数据

```ts
export const TrajectoryFromCustomData = CreateTransformer({
    name: 'trajectory-from-custom-data',
    display: 'Trajectory',
    from: PluginStateObject.Data.String, // or PluginStateObject.Data.Binary
    to: PluginStateObject.Molecule.Trajectory,
})({
    apply({ a }) {
        return Task.create('Trajectory', async (ctx) => {
            const models = await customParse(a.data, ctx);
            return new PluginStateObject.Molecule.Trajectory(models, {
                label: 'Trajectory',
            });
        });
    },
});

//////////////

const data = await plugin.builders.data.download({ url, isBinary });
const trajectory = await plugin.build().to(data).apply(TrajectoryFromCustomData, { data }).commit();
await plugin.builders.structure.hierarchy.applyPreset(trajectory, 'default');
```
