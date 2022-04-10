# 如何声明 global 类型

要使用如下代码：

```ts
global.jsdom.reconfigure({
  url: "https://www.baidu.com?a=1&b=2"
});
```

如果不声类型，会报：`type 'typeof globalThis' has no index signature` 的错误。经过一翻搜索后，发现如下方法可行：

```ts
// global.d.ts
declare module globalThis {
  var jsdom: any;
}
```

需要注意的是这里一定要写 `var`，而且 `module` 为 `globalThis`。
