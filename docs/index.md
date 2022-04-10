# 安装

## NPM Package

```shell
yarn add molstar
```

or

```shell
npm install molstar
```

通过 `import` 语法引用 ``molstar/lib/...``

```ts
import { PluginContext } from 'molstar/lib/mol-plugin/context';
```

## Clone from GitHub

> molstar 源码仓库

```shell
git clone https://github.com/molstar/molstar.git
cd molstar
npm install
npm build
```

--------------------

For a watch task to automatically rebuild the source code on changes, run

```shell
npm run watch
```

or if working just with the Viewer app for better performance

```shell
npm run watch-viewer
```
