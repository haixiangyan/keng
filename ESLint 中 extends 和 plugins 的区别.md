# ESLint 中 `extends` 和 `plugins` 的区别

在配置 eslint 的时候，有两个配置项一直很让人迷惑：`extends` 以及 `plugins`。

举个例子，我们要配置 eslint x typescript，可以看到官网有这样的配置：

```js
module.exports = {
  root: true,
  parser: '@typescript-eslint/parser',
  plugins: [
    '@typescript-eslint',
  ],
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
  ],
};
```

神奇的是，当你去掉 `plugins` 之后发现 `eslint` 依然可以正常工作。更神奇的是，只要你写了 `extends`，那么连 `parser` 也可以不用加，要知道没有指定 `parser` 选项，eslint 可看不懂你的 TypeScript 文件。

所以说，到底是 `plugins` 加上了 TypeScript 的能力还是 `extends` 加上了 TypeScript 的规则呢？真让人头大，直到终于有一天受不了了，翻找了一下网上的资料发现了[这个帖子](https://stackoverflow.com/questions/61528185/eslint-extends-vs-plugins-v2020)。

先来说结论吧：**每个 `plugins` 只是开启了这个插件，而 `extends` 则会继承别人写好的一份 `.eslintrc` 的配置，这份配置不仅仅包括了 `rules` 还有 `parser`，`plugins` 之类的东西。**

所以回到问题，为什么在继承了 `plugin:@typescript-eslint/recommended` 之后就可以不写 `plugins` 和 `parser` 呢？因为别人已经把配置都放在 `recommended` 这份配置表里了，这样对使用的人来说，就可以少写很多配置项了。

也就是说，下面两份配置是等价的：

```js
module.exports = {
  parser: "@typescript-eslint/parser",
  parserOptions: { sourceType: "module" },
  plugins: ["@typescript-eslint"],
  extends: [],
  rules: {
    "@typescript-eslint/explicit-function-return-type": [
      "error",
      {
        allowExpressions: true
      }
    ]
  }
}
```

以及 

```js
module.exports = {
  plugins: [],
  extends: ["plugin:@typescript-eslint/recommended"],
  rules: {
    "@typescript-eslint/explicit-function-return-type": [
      "error",
      {
        allowExpressions: true
      }
    ]
  }
}
```

对于第一份配置：
* 需要手动添加 `parser`, `parserOptions`, `plugins`
* 只开启了 `@typescript-eslint/explicit-function-return-type` 一个规则

对于第二份配置：
* `plugin:@typescript-eslint/recommended` 自动添加了 `parser`, `parserOptions`, `plugins`
* 一些推荐的 TypeScript ESLint 规则也自动加上了
* 只对 `@typescript-eslint/explicit-function-return-type` 这个规则进行自定义配置
