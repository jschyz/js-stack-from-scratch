# 02 - Babel, ES6, ESLint, Flow, Jest, and Husky

本章代码 [here](https://github.com/verekia/js-stack-walkthrough/tree/master/02-babel-es6-eslint-flow-jest-husky).

现在开始将使用一些ES6语法，这是相对于ES5语法来说是一个很大的改进。但目前所有浏览器和JS环境都支持ES5，这时我们需要使用 Babel 来帮助我们转换成 ES5 代码！

## Babel

> 💡 **[Babel](https://babeljs.io/)** 是一个将ES6代码转换为ES5代码的编译器。它符合模块化规范并能在不同的[开发环境](https://babeljs.io/docs/setup/)使用。而且是React社区的首选ES5编译器。

- 打开 `src` 目录下的 `index.js` 文件，这是你书写ES6代码的地方。删除 `index.js` 文件中前面的 `color` 相关代码，并用一个简单的代替：

```js
const str = 'ES6'
console.log(`Hello ${str}`)
```

这里使用了ES6特性之一的*模板字符串*，可以字符串中使用插入变量 (${})，请注意，使用反引号创建模板字符串。

- 在控制台界面输入 `yarn add --dev babel-cli` 命令来安装 Babel.

Babel CLI 工具自带[两个命令](https://babeljs.io/docs/usage/cli/)：`babel` 能将ES6文件编译成ES5文件（译者注：需要指定 `--out-file` 参数才会有新文件产出)，而 `babel-node` 提供一个支持ES6的REPL环境，而且可以直接运行ES6代码。`babel-node` 不适合生产模式，仅用于开发测试。在本章中，我们将使用`babel-node`来设置开发环境，在下一章中我们将使用`babel`来生成ES5文件。

- 接下来打开 `package.json` 文件，在 `start` 脚本里，把 `node .` 替换成 `babel-node src`
- In `package.json`, in your `start` script, replace `node .` by `babel-node src` (`index.js` is the default file Node looks for, which is why we can omit `index.js`).


If you try to run `yarn start` now, it should print the correct output, but Babel is not actually doing anything. That's because we didn't give it any information about which transformations we want to apply. The only reason it prints the right output is because Node natively understands ES6 without Babel's help. Some browsers or older versions of Node would not be so successful though!

- Run `yarn add --dev babel-preset-env` to install a Babel preset package called `env`, which contains configurations for the most recent ECMAScript features supported by Babel.

- Create a `.babelrc` file at the root of your project, which is a JSON file for your Babel configuration. Write the following to it to make Babel use the `env` preset:

```json
{
  "presets": [
    "env"
  ]
}
```

🏁 `yarn start` should still work, but it's actually doing something now. We can't really tell if it is though, since we're using `babel-node` to interpret ES6 code on the fly. You'll soon have a proof that your ES6 code is actually transformed when you reach the [ES6 modules syntax](#the-es6-modules-syntax) section of this chapter.

## ES6

> 💡 **[ES6](http://es6-features.org/)**: 这是JavaScript语言最显着的改进。ES6 有很多新特性，例如：`class`，`const`，`let`，模板字符串，箭头函数(`(text) => { console.log(text) }`)。

### 创建一个类

- 创建一个新文件叫`src/dog.js`，内容如下：

```js
class Dog {
  constructor(name) {
    this.name = name
  }

  bark() {
    return `Wah wah, I am ${this.name}`
  }
}

module.exports = Dog
```

如果你有OOP (面向对象) 开发经验，那么你应该不会觉得奇怪。它是通过 `module.exports` 赋值暴露给外界。

接着我们在 `src/index.js` 里输入：

```js
const Dog = require('./dog')

const toby = new Dog('Toby')

console.log(toby.bark())
```

需要注意的是，我们引用自建模块时，需要加上相对路劲 `./`。

🏁 现在运行 `yarn start`，你将会看到 "Wah wah, I am Toby"。

### ES6模块语法

现在试试将 `const Dog = require('./dog')` 替换成 `import Dog from './dog'`，这是新的 ES6 模块语法（而不是"CommonJS"模块语法）。目前NodeJS还不支持ES6模块，Babel插件正好能帮我们进行转换。

在文件 `dog.js`，顺便也将 `module.exports = Dog` 替换成 `export default Dog`。

🏁 再次运行 `yarn start`，你看到的还是 "Wah wah, I am Toby"。

## ESLint

> 💡 **[ESLint](http://eslint.org)** 是语法检查工具。它能帮助团队统一代码风格和避免低级错误。这也是学习JavaScript过程中用于纠正错误非常好的途径。

ESLint works with *rules*, and there are [many of them](http://eslint.org/docs/rules/). Instead of configuring the rules we want for our code ourselves, we will use the config created by Airbnb. This config uses a few plugins, so we need to install those as well.

Check out Airbnb's most recent [instructions](https://www.npmjs.com/package/eslint-config-airbnb) to install the config package and all its dependencies correctly. As of 2017-02-03, they recommend using the following command in your terminal:

```sh
npm info eslint-config-airbnb@latest peerDependencies --json | command sed 's/[\{\},]//g ; s/: /@/g' | xargs yarn add --dev eslint-config-airbnb@latest
```

上面命令将安装依赖 `eslint-config-airbnb`，`eslint-plugin-import`，`eslint-plugin-jsx-a11y`，`eslint-plugin-react` 并保存至 `package.json` 里。

**Note**: `npm install` 替换成 `yarn add` 后，在 Windows 环境里，不能完整的安装依赖，这时你需要手动进行安装每个依赖 `yarn add --dev packagename@^#.#.#`。

- 创建文件 `.eslintrc.json`, 输入:

```json
{
  "extends": "airbnb"
}
```

We'll create an NPM/Yarn script to run ESLint. Let's install the `eslint` package to be able to use the `eslint` CLI:

- 运行 `yarn add --dev eslint`

在 `package.json` 里的 `scripts` 项添加新的 `test` 任务：

```json
"scripts": {
  "start": "babel-node src",
  "test": "eslint src"
},
```

这个任务将对 `src` 目录下的 JavaScript 文件进行语法检测（linter)。

标准的 `test` 任务包含链式代码验证，比如语法检测、类型检查、单元测试等。

- 运行 `yarn test` 后，你将看到一大堆错误缺少分号提示，和一些 `console.log()` 使用警告。你在使用 `console` 那行添加 `/* eslint-disable no-console */` 后，ESlint 将会忽略此警告。

**Note**: If you're on Windows, make sure you configure your editor and Git to use Unix LF line endings and not Windows CRLF. If your project is only used in Windows environments, you can add `"linebreak-style": [2, "windows"]` in ESLint's `rules` array (see the example below) to enforce CRLF instead.

### Semicolons

Alright, this is probably the most heated debate in the JavaScript community, let's talk about it for a minute. JavaScript has this thing called Automatic Semicolon Insertion, which allows you to write your code with or without semicolons. It really comes down to personal preference and there is no right and wrong on this topic. If you like the syntax of Python, Ruby, or Scala, you will probably enjoy omitting semicolons. If you prefer the syntax of Java, C#, or PHP, you will probably prefer using semicolons.

Most people write JavaScript with semicolons, out of habit. That was my case until I tried going semicolon-less after seeing code samples from the Redux documentation. At first it felt a bit weird, simply because I was not used to it. After just one day of writing code this way I could not see myself going back to using semicolons at all. They felt so cumbersome and unnecessary. A semicolon-less code is easier on the eyes in my opinion, and is faster to type.

I recommend reading the [ESLint documentation about semicolons](http://eslint.org/docs/rules/semi). As mentioned in this page, if you're going semicolon-less, there are some rather rare cases where semicolons are required. ESLint can protect you from such cases with the `no-unexpected-multiline` rule. Let's set up ESLint to safely go semicolon-less in `.eslintrc.json`:

```json
{
  "extends": "airbnb",
  "rules": {
    "semi": [2, "never"],
    "no-unexpected-multiline": 2
  }
}
```

🏁 Run `yarn test`, and it should now pass successfully. Try adding an unnecessary semicolon somewhere to make sure the rule is set up correctly.

I am aware that some of you will want to keep using semicolons, which will make the code provided in this tutorial inconvenient. If you are using this tutorial just for learning, I'm sure it will remain bearable to learn without semicolons, until going back to using them on your real projects. If you want to use the code provided in this tutorial as a boilerplate though, it will require a bit of rewriting, which should be pretty quick with ESLint set to enforce semicolons to guide you through the process. I apologize if you're in such case.

### ESLint in your editor

This chapter set you up with ESLint in the terminal, which is great for catching errors at build time / before pushing, but you also probably want it integrated to your IDE for immediate feedback. Do NOT use your IDE's native ES6 linting. Configure it so the binary it uses for linting is the one in your `node_modules` folder instead. This way it can use all of your project's config, the Airbnb preset, etc. Otherwise you will just get some generic ES6 linting.

## Flow

> 💡 **[Flow](https://flowtype.org/)**: A static type checker by Facebook. It detects inconsistent types in your code. For instance, it will give you an error if you try to use a string where should be using a number.

Right now, our JavaScript code is valid ES6 code. Flow can analyze plain JavaScript to give us some insights, but in order to use its full power, we need to add type annotations in our code, which will make it non-standard. We need to teach Babel and ESLint what those type annotations are in order for these tools to not freak out when parsing our files.

- Run `yarn add --dev flow-bin babel-preset-flow babel-eslint eslint-plugin-flowtype`

`flow-bin` is the binary to run Flow in our `scripts` tasks, `babel-preset-flow` is the preset for Babel to understand Flow annotations, `babel-eslint` is a package to enable ESLint *to rely on Babel's parser* instead of its own, and `eslint-plugin-flowtype` is an ESLint plugin to lint Flow annotations. Phew.

- Update your `.babelrc` file like so:

```json
{
  "presets": [
    "env",
    "flow"
  ]
}
```

- And update `.eslintrc.json` as well:

```json
{
  "extends": [
    "airbnb",
    "plugin:flowtype/recommended"
  ],
  "plugins": [
    "flowtype"
  ],
  "rules": {
    "semi": [2, "never"],
    "no-unexpected-multiline": 2
  }
}
```

**Note**: The `plugin:flowtype/recommended` contains the instruction for ESLint to use Babel's parser. If you want to be more explicit, feel free to add `"parser": "babel-eslint"` in `.eslintrc.json`.

I know this is a lot to take in, so take a minute to think about it. I'm still amazed that it is even possible for ESLint to use Babel's parser to understand Flow annotations. These 2 tools are really incredible for being so modular.

- Chain `flow` to your `test` task:

```json
"scripts": {
  "start": "babel-node src",
  "test": "eslint src && flow"
},
```

- Create a `.flowconfig` file at the root of your project containing:

```flowconfig
[options]
suppress_comment= \\(.\\|\n\\)*\\flow-disable-next-line
```

This is a little utility that we set up to make Flow ignore any warning detected on the next line. You would use it like this, similarly to `eslint-disable`:

```js
// flow-disable-next-line
something.flow(doesnt.like).for.instance()
```

Alright, we should be all set for the configuration part.

- Add Flow annotations to `src/dog.js` like so:

```js
// @flow

class Dog {
  name: string

  constructor(name: string) {
    this.name = name
  }

  bark() {
    return `Wah wah, I am ${this.name}`
  }
}

export default Dog
```

The `// @flow` comment tells Flow that we want this file to be type-checked. For the rest, Flow annotations are typically a colon after a function parameter or a function name. Check out the [documentation](https://flowtype.org/docs/quick-reference.html) for more details.

- Add `// @flow` at the top of `index.js` as well.

`yarn test` should now both lint and type-check your code fine.

There are 2 things that I want you to try:

- In `dog.js`, replace `constructor(name: string)` by `constructor(name: number)`, and run `yarn test`. You should get a **Flow** error telling you that those types are incompatible. That means Flow is set up correctly.

- Now replace `constructor(name: string)` by `constructor(name:string)`, and run `yarn test`. You should get an **ESLint** error telling you that Flow annotations should have a space after the colon. That means the Flow plugin for ESLint is set up correctly.

🏁 If you got the 2 different errors working, you are all set with Flow and ESLint! Remember to put the missing space back in the Flow annotation.

### Flow in your editor

Just like with ESLint, you should spend some time configuring your editor / IDE to give you immediate feedback when Flow detects issues in your code.

## Jest

> 💡 **[Jest](https://facebook.github.io/jest/)**: A JavaScript testing library by Facebook. It is very simple to set up and provides everything you would need from a testing library right out of the box. It can also test React components.

- Run `yarn add --dev jest babel-jest` to install Jest and the package to make it use Babel.

- Add the following to your `.eslintrc.json` at the root of the object to allow the use of Jest's functions without having to import them in every test file:

```json
"env": {
  "jest": true
}
```

- Create a `src/dog.test.js` file containing:

```js
import Dog from './dog'

test('Dog.bark', () => {
  const testDog = new Dog('Test')
  expect(testDog.bark()).toBe('Wah wah, I am Test')
})
```

- Add `jest` to your `test` script:

```json
"scripts": {
  "start": "babel-node src",
  "test": "eslint src && flow && jest --coverage"
},
```

The `--coverage` flag makes Jest generate coverage data for your tests automatically. This is useful to see which parts of your codebase lack testing. It writes this data into a `coverage` folder.

- Add `/coverage/` to your `.gitignore`

🏁 Run `yarn test`. After linting and type checking, it should run Jest tests and show a coverage table. Everything should be green!

## Git Hooks with Husky

> 💡 **[Git Hooks](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks)**: Scripts that are run when certain actions like a commit or a push occur.

Okay so we now have this neat `test` task that tells us if our code looks good or not. We're going to set up Git Hooks to automatically run this task before every `git commit` and `git push`, which will prevent us from pushing bad code to the repository if it doesn't pass the `test` task.

[Husky](https://github.com/typicode/husky) is a package that makes this very easy to set up Git Hooks.

- Run `yarn add --dev husky`

All we have to do is to create two new tasks in `scripts`, `precommit` and `prepush`:

```json
"scripts": {
  "start": "babel-node src",
  "test": "eslint src && flow && jest --coverage",
  "precommit": "yarn test",
  "prepush": "yarn test"
},
```

🏁 If you now try to commit or push your code, it should automatically run the `test` task.

**Note**: If you are pushing right after a commit, you can use `git push --no-verify` to avoid running all the tests again.

Next section: [03 - Express, Nodemon, PM2](03-express-nodemon-pm2.md#readme)

Back to the [previous section](01-node-yarn-package-json.md#readme) or the [table of contents](https://github.com/verekia/js-stack-from-scratch#table-of-contents).
