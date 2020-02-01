# 复现`umi`问题的一个仓库

**问题复现步骤：**

+ umi通过设置`cssLoaderOptions.modules`为true来开启css modules
+ 项目中引入第三方库中的css文件，如 `src/pages/index.tsx`中引入：
  ```js
    import 'kindeditor/themes/default/default.css'
  ```
+ 第三方库中的css样式中背景引入图片，该图片地址形式为：
  ```css
  .xxx {
    background: url(background.png);
    ...
  }
  ```


然后执行`npm start`编译报错：

```js

 ERROR  Failed to compile with 1 errors                       9:56:41 AM

 error  in ./node_modules/kindeditor/themes/default/default.css

Module build failed (from ./node_modules/mini-css-extract-plugin/dist/loader.js):
ModuleNotFoundError: Module not found: Error: Can't resolve 'background.png' in '/Users/didi/Desktop/didi/study/umi-question-report/node_modules/kindeditor/themes/default'
    at factory.create (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/webpack/lib/Compilation.js:925:10)
    at factory (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/webpack/lib/NormalModuleFactory.js:401:22)
    at resolver (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/webpack/lib/NormalModuleFactory.js:130:21)
    at asyncLib.parallel (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/webpack/lib/NormalModuleFactory.js:224:22)
    at /Users/didi/Desktop/didi/study/umi-question-report/node_modules/neo-async/async.js:2830:7
    at /Users/didi/Desktop/didi/study/umi-question-report/node_modules/neo-async/async.js:6877:13
    at normalResolver.resolve (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/webpack/lib/NormalModuleFactory.js:214:25)
    at doResolve (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/enhanced-resolve/lib/Resolver.js:213:14)
    at hook.callAsync (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/enhanced-resolve/lib/Resolver.js:285:5)
    at _fn0 (eval at create (/Users/didi/Desktop/didi/study/umi-question-report/node_modules/tapable/lib/HookCodeFactory.js:33:10), <anonymous>:15:1)

 @ ./src/pages/index.tsx 3:0-47
 @ ./src/pages/.umi/router.js
 @ ./src/pages/.umi/umi.js
 @ multi ./node_modules/af-webpack/lib/webpackHotDevClient.js ./src/pages/.umi/umi.js
```


**原因：**

umi的`css modules`的配置导致`css-loader`在处理node_modules中库的css文件时，不会对其`url`进行处理；例如上面的background的图片path没有转化为`./background.png`。

这是因为`css modules`的配置影响到node_modules第三方库中的css modules，导致postcss未对css中的`url`进行处理。

