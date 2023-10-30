参考文章：

https://juejin.cn/post/7257441221761040444

# ESLint + Prettier + husky + lint-staged

## 初始化 eslint（vscode 也需安装 eslint 插件）

```shell
npm i eslint
```

```shell
npm init @eslint/config
```

![eslint规则自定义初始化](https://cdn.jsdelivr.net/gh/HuangGuiguang/cdn@1.0.1/eslint-husky/eslint规则自定义初始化.png)

## 安装 husky

husky 是一个 Git 钩子（Git hooks）工具，它可以让你在 Git 事件发生时执行脚本，进行代码格式化、测试等操作。

### 常见的钩子

-   `pre-commit`：在执行 Git `commit` 命令之前触发，用于在提交代码前进行代码检查、格式化、测试等操作。

-   `commit-msg`：在提交消息（commit message）被创建后，但提交操作尚未完成之前触发，用于校验提交消息的格式和内容。

-   `pre-push`：在执行 Git `push` 命令之前触发，用于在推送代码前进行额外检查、测试等操作。

### 安装

> :exclamation: 注意, 要是一个 git 仓库才能安装成功

```shell
npm install husky -D

# 添加一个prepare命令执行husky install, 当然你也可以自己执行
npm pkg set scripts.prepare="husky install"

# 执行后根目录下就会多一个.husky文件夹
npm run prepare
```

## 创建 husky 钩子

### pre-commit

在 git 提交之前做 eslint 语法校验

```shell
# 在.husky/pre-commit 文件下添加一行npm test
npx husky add .husky/pre-commit "npm test"

# 添加此文本到.husky/pre-commit 文件
npx lint-staged
```

## 安装 lint-staged

-   作用：lint-staged 可以让你在 Git 暂存（staged）区域中的文件上运行脚本，通常用于在提交前对代码进行格式化、静态检查等操作。
-   使用方式：你可以在项目中使用 lint-staged 配合 husky 钩子来执行针对暂存文件的脚本。具体的使用步骤如下

```shell
npm install lint-staged -D
```

在 package.json 添加配置

-   `"src/**/*.{js,jsx,ts,vue}"` 是指定要针对的暂存文件模式，你可以根据自己的项目需求来配置。
-   `["prettier --write","eslint --fix"]`为校验命令，可执行 eslint 、prettier 等规则

```json
"devDependencies": {
},
"lint-staged": {
    "src/**/*.{js,jsx,ts,vue}": [
        "prettier --write",
        "eslint --fix"
    ]
}
```

## prettier

prettier 是一个代码格式化工具。prettier 与上述 husky 和 lint-staged 搭配使用，可以在提交代码之前自动格式化代码。

```shell
npm install prettier -D
```

添加配置文件`.prettierrc.cjs`

```javascript
// 如果package.json中"type": "module", 文件名需要以cjs来结尾
// 修改配置后需要重启vscode
module.exports = {
    semi: false, // 强制在语句末尾使用分号。
    trailingComma: 'none', // 不允许在多行结构的最后一个元素或属性后添加逗号。
    singleQuote: true, // 使用单引号而不是双引号来定义字符串。
    printWidth: 120, // 指定每行代码的最大字符宽度，超过这个宽度的代码将被换行
    tabWidth: 4 // 指定一个制表符（Tab）等于多少个空格。
}
```

## 提交信息规范化

-   `Commitizen` 是一个命令行工具，用于以一致的方式编写规范的提交消息。在使用 Commitizen 之前，你需要安装 Commitizen 及其适配器。
-   `cz-conventional-changelog` 是 Commitizen 的一个适配器，它实现了符合约定式提交（Conventional Commits）规范的提交消息。该规范定义了提交消息的格式和结构，并推荐了一些常用的提交类型和范围。

### 安装

```shell
npm install -D commitizen cz-conventional-changelog
```

### 添加配置

```json
// package.json
"lint-staged": {},
"config": {
    "commitizen": {
        "path": "cz-conventional-changelog"
    }
},
```

### 添加脚本命令

```shell
npm pkg set scripts.commit="git-cz"
```

### 提交示例

```shell
git add .

npm run commit

# 根据提示信息完成提交

```

![git-cz提交流程](https://cdn.jsdelivr.net/gh/HuangGuiguang/cdn@1.0.1/eslint-husky/git-cz提交流程.png)

## 解决 prettier 和 eslint 的冲突

本质上是用 prettier 覆盖 eslint 的规则

![解决eslint和prettierr的冲突（覆盖eslint的配置，见仁见智）](https://cdn.jsdelivr.net/gh/HuangGuiguang/cdn@1.0.1/eslint-husky/解决eslint和prettierr的冲突（覆盖eslint的配置，见仁见智）.png)
