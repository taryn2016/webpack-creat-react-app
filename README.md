# creat-react-app 解读
creat-react-app 脚手架的webpack配置解读

## 目录结构
```
├─bin
│  └─react-scripts.js                # 命令入口文件
├─config
│  └─jest                            # jest配置文件夹 
│  ├─env.js                          # 环境变量配置
│  ├─getHttpsConfig.js               # 环境变量配置
│  ├─modules.js                      # 返回对应的JS或TS配置
│  ├─paths.js                        # 项目主要的入口文件配置项
│  ├─pnpTs.js                        # pnp相关配置项
│  ├─webpack.config.js               # webpack的配置
│  └─webpackDevServer.config.js      # webpack开发服务器的配置内容
├─public                             # public文件
├─scripts                            # 对应的启动脚本
│  ├─build.js                        # run build的脚本
│  ├─start.js                        # run start的脚本
│  └─test.js                         # run test工的脚本
└─src                                # 代码文件
```
## start流程

1. 设置环境变量：BABEL_ENV、NODE_ENV='development'
2. 加载自定义的环境变量配置文件(可以读取.env.development中的变量)
3. 检测文件（paths.appHtml, paths.appIndexJs），缺少文件退出执行
4. 设置HOST和DEFAULT_PORT
5. 检测是否配置browserslist。如果最终都没有browserlist则直接退出
6. 查找可用端口：先确认默认端口是否可用，不可用则确认是否自动查找可用端口，不查找则直接退出，查找则返回一个可用端口
7. 配置createCompiler的options并执行，返回一个compiler
8. 导入proxy配置，并配置代理服务prepareProxy
9. 启动本地node服务，具体的ji配置内容在webpackDevServer.config.js
10. 运行WebpackDevServer，传入compiler和proxyConfig，返回一个devServer
11. 启动devServer服务，如果在交互模式下清理控制台，再打开浏览器

## build流程

1. 设置环境变量：BABEL_ENV、NODE_ENV='procuction'
2. 加载自定义的环境变量配置文件(可以读取.env.procuction中的变量)
3. 检测文件（paths.appHtml, paths.appIndexJs），缺少文件退出执行
4. 生成webpack的 production 环境的配置
5. 检测是否配置browserslist。如果最终都没有browserlist则直接退出
6. 首先，读取构建目录中的当前文件大小。这可以让我们显示它们以后发生了多少变化。
7. 删除 build文件夹下的内容，但是保留build文件夹
8. copyPublicFolder，复制public目录到build文件夹
9. 创建生产版本并打印部署说明。
10. 完成后，在控制台输出相关创建内容，错误警告等

## JSON文件相关内容

### package.json配置
1. dependencies       -- 生产环境安装(影响用户使用，缺少app就不能使用的包，比如网络请求axios，框架react，react-native, redux等)
1. devDependencies    -- 开发环境安装(redux-logger等日志工具，ESlint，webpack，gulp等打包工具)
2. scripts            -- 配置命令,执行npm run xxx即可运行scripts文件下对应的js文件
3. eslintConfig       -- eslint规则
4. browserslist       -- 浏览器兼容范围,也可以配置在.browserslistrc文件,会被Autoprefixer Babel postcss-preset-env等使用
5. jest               -- 测试相关
6. babel              -- babel配置,也可以配置在.babelrc文件

经过 `npm run eject` 暴露过的package.json配置 该操作是单向操作不可逆
```javascript
{
  "name": "testapp", // 项目名 
  "version": "0.1.0", // 版本号
  "private": true, // 私有项目，静止意外发布私有存储库的方法
  "dependencies": { //依赖包,在开发和线上环境均需要使用
    "@babel/core": "7.9.0", //babel核心;babel是一个主要(不仅仅只是)用于将ECMAScript2015+版本的代码转换为向后兼容的JavaScript语法的工具链
    "@svgr/webpack": "4.3.3", //处理svg格式文件的webpack loader
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "@typescript-eslint/eslint-plugin": "^2.10.0", //ts语法审查
    "@typescript-eslint/parser": "^2.10.0", //ts解析器
    "babel-eslint": "10.1.0", //对所有有效的babel代码进行lint处理
    "babel-jest": "^24.9.0", //转换jest代码
    "babel-loader": "8.1.0", //对js|mjs|jsx|ts|tsx处理的webpack loader
    "babel-plugin-named-asset-import": "^0.3.6", //babel-loader中的文件导入处理plugin
    "babel-preset-react-app": "^9.1.2", //按照react-app的模式配置babel,下面的babel项中可以配置详细参数(指定typescript语法等)
    "camelcase": "^5.3.1", //驼峰命名检查
    "case-sensitive-paths-webpack-plugin": "2.3.0", //处理导入路径语法时区分大小写检查 
    "css-loader": "3.4.2", //.css文件loader
    "dotenv": "8.2.0",  //管理环境变量的模块,通过配置.env文件可以控制环境变量
    "dotenv-expand": "5.1.0", //扩展环境变量
    "eslint": "^6.6.0", //静态检查代码错误核心
    "eslint-config-react-app": "^5.2.1", //配置eslint为react-app模式
    "eslint-loader": "3.0.3", //静态检查代码错误loader
    "eslint-plugin-flowtype": "4.6.0",//eslint检查的plugin
    "eslint-plugin-import": "2.20.1",//eslint检查导入语句的plugin
    "eslint-plugin-jsx-a11y": "6.2.3",//eslint检查jsx语句的plugin
    "eslint-plugin-react": "7.19.0", // eslint检查react语句的plugin
    "eslint-plugin-react-hooks": "^1.6.1", // eslint检查react-hooks的plugin
    "file-loader": "4.3.0", // 文件处理默认loader,webpack中使用了oneOf,并且file-loader放最后,所以只有其他loader未处理情况下才会交由其处理
    "fs-extra": "^8.1.0", // 扩展自带fs的功能
    "html-webpack-plugin": "4.0.0-beta.11", // 打包html文件的webpack插件 
    "identity-obj-proxy": "3.0.0",
    // 下面几项是关于测试的依赖 
    "jest": "24.9.0",
    "jest-environment-jsdom-fourteen": "1.0.1",
    "jest-resolve": "24.9.0",
    "jest-watch-typeahead": "0.4.2",
    "mini-css-extract-plugin": "0.9.0", // 压缩css文件插件 
    "optimize-css-assets-webpack-plugin": "5.0.3", // 优化css和资源文件插件 
    "pnp-webpack-plugin": "1.6.4", // 维护依赖路径,加快node_modules的查找速度 
    "postcss-flexbugs-fixes": "4.1.0", // 预处理css,处理flex兼容问题 
    "postcss-loader": "3.0.0", // css预处理loader 
    "postcss-normalize": "8.0.1", // 引入normalize.css或者sanitize.css 
    "postcss-preset-env": "6.7.0", // css兼容处理 
    "postcss-safe-parser": "4.0.1", // 修复css语法错误 
    "react": "^16.13.1", // react核心库 
    "react-app-polyfill": "^1.0.6",  // 官方提供的兼容库,主要用于处理IE兼容问题 
    "react-dev-utils": "^10.2.1",
    "react-dom": "^16.13.1", // react核心库,处理虚拟DOM渲染等功能 
    "resolve": "1.15.0",  // 引入node中require.resolve()方法 
    "resolve-url-loader": "3.1.1",// 处理url()语句的loader 
    "sass-loader": "8.0.2",// 处理sass文件的loader 
    "semver": "6.3.0",// 格式化版本号字符串
    "style-loader": "0.23.1",// 处理style文件的loader 
    "terser-webpack-plugin": "2.3.5", // 压缩JavaScript 
    "ts-pnp": "1.1.6", // 和pnp-webpack-plugin类似 
    "url-loader": "2.3.0", // 将文件转换为base64格式(可以配置应用在小文件上) 
    "webpack": "4.42.0", // webpack核心
    "webpack-dev-server": "3.10.3",  // 开发环境服务器,具有热加载功能 
    "webpack-manifest-plugin": "2.2.0", // 帮助打包时生成资源清单文件的插件 
    "workbox-webpack-plugin": "4.3.1" // 提供完善的ServiceWorker和预缓存文件清单的插件 
  },
  "scripts": { // 配置命令,执行npm run xxx即可运行scripts文件下对应的js文件 
    "start": "node scripts/start.js",
    "build": "node scripts/build.js",
    "test": "node scripts/test.js"
  },
  "eslintConfig": {
    "extends": "react-app" // eslint规则 
  },
  "browserslist": { // 浏览器兼容范围,也可以配置在.browserslistrc文件,会被Autoprefixer Babel postcss-preset-env等使用 
    "production": [
      ">0.2%", // 兼容市场份额在0.2%以上的浏览器 
      "not dead", // 在维护中的浏览器 
      "not op_mini all" // 忽略OperaMini浏览器 
    ],
    "development": [ // 开发环境只需兼容以下三种浏览器的最新版本 
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "jest": { // 测试 
    "roots": [
      "<rootDir>/src"
    ],
    "collectCoverageFrom": [
      "src/**/*.{js,jsx,ts,tsx}",
      "!src/**/*.d.ts"
    ],
    "setupFiles": [
      "react-app-polyfill/jsdom"
    ],
    "setupFilesAfterEnv": [
      "<rootDir>/src/setupTests.js"
    ],
    "testMatch": [
      "<rootDir>/src/**/__tests__/**/*.{js,jsx,ts,tsx}",
      "<rootDir>/src/**/*.{spec,test}.{js,jsx,ts,tsx}"
    ],
    "testEnvironment": "jest-environment-jsdom-fourteen",
    "transform": {
      "^.+\\.(js|jsx|ts|tsx)$": "<rootDir>/node_modules/babel-jest",
      "^.+\\.css$": "<rootDir>/config/jest/cssTransform.js",
      "^(?!.*\\.(js|jsx|ts|tsx|css|json)$)": "<rootDir>/config/jest/fileTransform.js"
    },
    "transformIgnorePatterns": [
      "[/\\\\]node_modules[/\\\\].+\\.(js|jsx|ts|tsx)$",
      "^.+\\.module\\.(css|sass|scss)$"
    ],
    "modulePaths": [],
    "moduleNameMapper": {
      "^react-native$": "react-native-web",
      "^.+\\.module\\.(css|sass|scss)$": "identity-obj-proxy"
    },
    "moduleFileExtensions": [
      "web.js",
      "js",
      "web.ts",
      "ts",
      "web.tsx",
      "tsx",
      "json",
      "web.jsx",
      "jsx",
      "node"
    ],
    "watchPlugins": [
      "jest-watch-typeahead/filename",
      "jest-watch-typeahead/testname"
    ]
  },
  "babel": { /* babel配置,也可以配置在.babelrc文件 */
    "presets": [
      "react-app" /* 指定按照react-app的模式配置babel */
    ]
  }
}

```

### npm install命令相关
1. npm install
会下载dependencies和devDependencies中的模块，当使用npm install --production或者注明NODE_ENV变量值为production时，只会下载dependencies中的模块。

2. npm install 单个模块 (npm i 单个模块)
安装到node_modules目录中，默认情况下，npm install会将任何指定的包保存到dependencies项中（npm 5.x.x后）。此外，您还可以使用一些附加标志控制如何保存它们。

3. npm install 单个模块 --save  (npm i 单个模块 -S)
安装到node_modules目录中，保存在package.json中dependencies字段下，安装生产环境依赖的模块，即项目运行时的模块，例如react，react-dom,jQuery等类库或者框架。运行npm install，或者npm install --production或者注明NODE_ENV变量值为production时，会将这些模块自动安装到node_modules中。

4. npm install 单个模块 --save-dev (npm i 单个模块 -D)
安装到node_modules目录中，保存在package.json中devDependencies字段下，安装开发环境依赖的模块，即项目开发时的模块，例如babel（转码器，可以将ES6代码转为ES5代码）等一些工具，只需在开发环境时用到。运行npm install，会将这些模块自动安装到node_modules中，但运行npm install --production或者注明NODE_ENV变量值为production时，不会将这些模块自动安装到node_modules中。


## react-scripts 和 react-app-rewired
### react-scripts
react-scripts是create-react-app生成项目所有的依赖。
通常情况下，我们创建spa应用时是使用npm安装项目依赖，在通过配置webpack.config.js进行配置，搭建好环境后在src编写源代码。而create-react-app是自动构建，在package.json中只有react-scripts作为依赖，而在reacr-scripts中已经配置好了项目所有需要的。 从react，es6，babel,webpack编辑到打包，react-scripts都做了。
create-react-app默认的 package.json配置
```json
  {
  "name": "demo",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "react": "^16.13.1",
    "react-dom": "^16.13.1",
    "react-scripts": "3.4.3"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}
```

### react-app-rewired
react-app-rewired 是 react-scripts的扩展。
react-app-rewired在可以不需要 `npm run eject` 暴露配置文件的情况下进行扩展。
react-app-rewired 是对create-react-app 进行自定义配置的社区解决方案。它不会直接去修改create-react-app的默认配置，而是在create-react-app配置的基础上进行扩展。
#### 使用步骤
  1. 安装react-app-rewired
  ```
    yarn add react-app-rewired customize-cra
  ```

  2. 在项目根目录中创建config-overrides.js文件用于修改默认配置

  ```javascript
    /* config-overrides.js */

    module.exports = function override(config, env) {
      //do stuff with the webpack config...
      return config;
    }
  ```

  3. 修改 package.json 里的启动配置

  ```json
    "scripts": {
      "start": "react-app-rewired start",
      "build": "react-app-rewired build",
      "test": "react-app-rewired test",
    }
  ```
  4. 配置文件config-overrides.js
  通过上面3步已经可以扩展webpack配置了，接下来就根据需求去配置即可。下面介绍我使用过的一些常见配置
  - 启用ES7的修改器语法（babel 7）
  - css模块化（安装和使用见参考链接）
  - 配置别名
  - antd按需加载
  - less模块化和覆盖变量
  ```
    npm install customize-cra
  ```

  ``` javascript
    const { override, fixBabelImports, addDecoratorsLegacy, addLessLoader } = require('customize-cra');
    const { getThemeVariables } = require('antd/dist/theme');

    module.exports = override(
      fixBabelImports('import', { // antd 按需加载
        libraryName: 'antd',
        libraryDirectory: 'es',
        style: true
      }),
      addLessLoader({ // 修改主题
        javascriptEnabled: true,
        modifyVars: {
          '@primary-color': '#5987FF',
          '@menu-item-active-bg': '#EDEEF0',
          '@text-color': '#222222',
          '@btn-default-color': '#999999'
        },
      }),
      // 设置根目录
      addWebpackAlias({
        'UTILS': path.resolve(__dirname, './src/utils'),
        'STORE': path.resolve(__dirname, './src/store'),
        'SERVICES': path.resolve(__dirname, './src/services'),
        'COMPONTENTS': path.resolve(__dirname, './src/compontents')
      }),
      addDecoratorsLegacy() // 启用旧版装饰器Babel插件
    );
  ```

  ## 多页面修改

  ### eject后的修改
  
  1. path.js 的修改
  ```javascript
    const glob = require('glob');
    // 获取指定路径下的入口文件
    function getEntries(globPath) {
      const files = glob.sync(globPath),
        entries = {};
      files.forEach(function(filepath) {
          const split = filepath.split('/');
          const name = split[split.length - 2];
          entries[name] = './' + filepath;
      });
      return entries;
    }

    const entries = getEntries('src/**/index.js');

    function getIndexJs() {
      const indexJsList = [];
      Object.keys(entries).forEach((name) => {
        const indexjs = resolveModule(resolveApp, `src/${name}/index`)
        indexJsList.push({
          name,
          path: indexjs
        });
      })
      return indexJsList;
    }
    const indexJsList = getIndexJs()

    // module.exports修改appIndexJs并添加entries
    module.exports = {
      appIndexJs: indexJsList, // +++++++++++++
      entries // +++++++++++++
    };
  ```
  2. 配置webpack
  ```JavaScript
    // 配置webpack入口entry
    const entry = {}
    paths.appIndexJs.forEach(e => {
      entry[e.name] = [
        isEnvDevelopment &&
          require.resolve('react-dev-utils/webpackHotDevClient'),
        e.path
      ].filter(Boolean)
    });
    // 修改entry为 entry
    return {
      entry: entry,
    }
    // 更改出口文件的配置ouput

    // 没更改之前的
    // filename: isEnvProduction
    // ? 'static/js/[name].[contenthash:8].js'
    // : isEnvDevelopment && 'static/js/bundle.js',
    ...
    // chunkFilename: isEnvProduction
    // ? 'static/js/[name].[contenthash:8].chunk.js'
    // : isEnvDevelopment && 'static/js/[name].chunk.js',

    // 更改后的
    filename: isEnvProduction
    ? 'static/js/[name]/[name].[contenthash:8].js'
    : isEnvDevelopment && 'static/js/[name]/[name].bundle.js',
    ...
    chunkFilename: isEnvProduction
    ? 'static/js/[name]/[name].[contenthash:8].chunk.js'
    : isEnvDevelopment && 'static/js/[name]/[name].chunk.js',

    // 更改HtmlWebpackPlugin配置
    // HtmlWebpackPlugin
    // 这个plugin曝光率很高，他主要有两个作用
    // 为html文件中引入的外部资源如script、link动态添加每次compile后的hash，防止引用缓存的外部文件问题
    // 可以生成创建html入口文件，比如单页面可以生成一个html文件入口，配置N个html-webpack-plugin可以生成N个页面入口
    ...Object.keys(paths.entries).map((name) => {
      return new HtmlWebpackPlugin(
        Object.assign(
          {},
          {
            inject: true,
            chunks: [name],
            template: paths.appHtml,
            filename: name + '.html',
          },
          isEnvProduction
            ? {
              minify: {
                removeComments: true,
                collapseWhitespace: true,
                removeRedundantAttributes: true,
                useShortDoctype: true,
                removeEmptyAttributes: true,
                removeStyleLinkTypeAttributes: true,
                keepClosingSlash: true,
                minifyJS: true,
                minifyCSS: true,
                minifyURLs: true,
              },
            }
          : undefined
        )
      )
    }),
    // 注释ManifestPlugin部分代码
    // 这是为了生成manifest.json文件的配置，这里不需要。
  ```
  3. 修改scripts/build.js和scripts/start.js文件的校验代码
  ```javascript
  // 原来的代码
  // if (!checkRequiredFiles([paths.appHtml, paths.appIndexJs])) {
  //   process.exit(1);
  // }

  // 修改后的代码
  if (!checkRequiredFiles([paths.appHtml, ...paths.appIndexJs.map(e => e.path)])) {
    process.exit(1);
  }
  ```
  4. 删除public下多余文件
    现在项目已经能够正常运行，但是public下还有多余的代码可以删除。
    其中只需要留下index.html作为模板文件，并进行修改。

  5. 总结
    添加页面的方法
    如果需要添加页面，只需要复制page1和page2的目录结构，放在src目录下，注意不能同名。
    访问路径
    http://localhost:3000/page1.html
    http://localhost:3000/page2.html
  
  参考： https://blog.csdn.net/iwowen/article/details/103538942
  

  ### 直接自己完成webpack配置
  ```javascript
    const webpack = require('webpack');
    const glob = require('glob');
    const path = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const ExtractTextPlugin = require('extract-text-webpack-plugin');
    const CleanWebpackPlugin = require('clean-webpack-plugin');
    const webpackConfig = {
      entry: {},
      output:{
        path:path.resolve(__dirname, './dist/'),
        filename:'[name]/[name].[chunkhash:6].js'
      },
      //设置开发者工具的端口号,不设置则默认为8080端口
      devServer: {
        inline: true,
        port: 8082
      },
      module:{
        rules:[
          {
            test:/\.js?$/,
            exclude:/node_modules/,
            loader:'babel-loader',
            query:{
                presets:['es2015','react']
            }
          },
          {
            test: /\.(scss|sass|css)$/, 
            loader: ExtractTextPlugin.extract({fallback: "style-loader", use: "css-loader"})
          },
        ]
      },
      plugins: [
        new ExtractTextPlugin("[name]/[name].[chunkhash:6].css"),
        new CleanWebpackPlugin(
          ['dist'],　 
          {
            root: __dirname,　　　　　　　　　
            verbose:  true,        　　　　　　　　　　
            dry:      false        　　　　　　　　　　
          }
        )
      ],
    };
    // 获取指定路径下的入口文件
    function getEntries(globPath) {
      const files = glob.sync(globPath),
        entries = {};
      files.forEach(function(filepath) {
        const split = filepath.split('/');
        const name = split[split.length - 2];
        entries[name] = './' + filepath;
      });
      return entries;
    }
    const entries = getEntries('src/**/index.js');
    Object.keys(entries).forEach(function(name) {
      webpackConfig.entry[name] = entries[name];
      const plugin = new HtmlWebpackPlugin({
        filename: name + '/' + name + '.html',
        template: './public/index.html',
        inject: true,
        chunks: [name]
      });
      webpackConfig.plugins.push(plugin);
    })
    module.exports = webpackConfig;
  ```
  package.json
  ```json
    {
      "name": "recmultify",
      "version": "0.1.0",
      "private": true,
      "dependencies": {
        "react": "^16.8.6",
        "react-dom": "^16.8.6",
        "react-scripts": "2.1.8"
      },
      "devDependencies": {
        "babel-core": "^6.26.0",
        "babel-loader": "^7.1.2",
        "babel-preset-es2015": "^6.24.1",
        "babel-preset-react": "^6.24.1",
        "clean-webpack-plugin": "^0.1.16",
        "css-loader": "^0.28.7",
        "extract-text-webpack-plugin": "^3.0.0",
        "file-loader": "^1.0.0",
        "glob": "^7.1.2",
        "html-webpack-plugin": "^2.30.1",
        "postcss-loader": "^2.0.6",
        "style-loader": "^0.18.2",
        "url-loader": "^0.5.9",
        "webpack": "^3.5.6",
        "webpack-dev-server": "^2.8.1"
      },
      "scripts": {
        "start": "webpack-dev-server --open",
        "build": "webpack"
      },
      "eslintConfig": {
        "extends": "react-app"
      },
      "browserslist": [
        ">0.2%",
        "not dead",
        "not ie <= 11",
        "not op_mini all"
      ]
    }
  ```

  ### 利用 react-app-rewired 扩展来完成多页面配置

  最终 config-overrides.js

  ```JavaScript
    const rewireLess = require('react-app-rewire-less');
    const { injectBabelPlugin, paths } = require('react-app-rewired');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    const path = require('path');
    const fs = require('fs');
    const globby = require('globby');
    const appDirectory = fs.realpathSync(process.cwd());
    const resolveApp = relativePath => path.resolve(appDirectory, relativePath);
    // const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
    module.exports = function override(config, env) {
      // 使用 babel-plugin-import 按需加载组件
      config = injectBabelPlugin(
        ['import', { libraryName: 'antd', libraryDirectory: 'es', style: true }],
        config,
      );

      // 增加 less 支持
      config = rewireLess.withLoaderOptions({
        // 解决报错: Inline JavaScript is not enabled. Is it set in your options?
        javascriptEnabled: true,
      })(config, env);

      // 入口文件路径
      // const entriesPath = globby.sync([resolveApp('src') + '/*/index.js']);
      const entriesPath = globby.sync([resolveApp('src') + '/*/index.js'], {cwd: process.cwd()});
      paths.entriesPath = entriesPath;

      // 获取指定路径下的入口文件
      function getEntries(){
        const entries = {};
        const files = paths.entriesPath;
        files.forEach(filePath => {
          let tmp = filePath.split('/');
          let name = tmp[tmp.length - 2];
          if(env === 'production'){
            entries[name] = [
              require.resolve('./node_modules/react-scripts/config/polyfills.js'),
              filePath,
            ];
          } else {
            entries[name] = [
              require.resolve('./node_modules/react-scripts/config/polyfills.js'),
              require.resolve('react-dev-utils/webpackHotDevClient'),
              filePath,
            ];
          }
        });
        return entries;
      }

      // 入口文件对象
      const entries = getEntries();

      // 配置 HtmlWebpackPlugin 插件, 指定入口文件生成对应的 html 文件
      let htmlPlugin;
      if(env === 'production'){
        htmlPlugin = Object.keys(entries).map(item => {
          return new HtmlWebpackPlugin({
            inject: true,
            template: paths.appHtml,
            filename: item + '.html',
            chunks: [item],
            minify: {
              removeComments: true,
              collapseWhitespace: true,
              removeRedundantAttributes: true,
              useShortDoctype: true,
              removeEmptyAttributes: true,
              removeStyleLinkTypeAttributes: true,
              keepClosingSlash: true,
              minifyJS: true,
              minifyCSS: true,
              minifyURLs: true,
            },
          });
        });
      } else {
        htmlPlugin = Object.keys(entries).map(item => {
          return new HtmlWebpackPlugin({
            inject: true,
            template: paths.appHtml,
            filename: item + '.html',
            chunks: [item],
          });
        });
      }

      if (env === 'production') {
        for (let i = 0; i < config.plugins.length; i++) {
          let item = config.plugins[i];

          // 更改输出的样式文件名
          if (item.constructor.toString().indexOf('class MiniCssExtractPlugin') > -1) {
            item.options.filename = 'static/css/[name].css?_v=[contenthash:8]';
            item.options.chunkFilename = 'static/css/[name].chunk.css?_v=[contenthash:8]';
          }

          // SWPrecacheWebpackPlugin: 使用 service workers 缓存项目依赖
          if(item.constructor.toString().indexOf('function GenerateSW') > -1){
            // 更改输出的文件名
            item.config.precacheManifestFilename = 'precache-manifest.js?_v=[manifestHash]';
          }
        }

        // 更改生产模式输出的文件名
        config.output.filename = 'static/js/[name].js?_v=[chunkhash:8]';
        config.output.chunkFilename = 'static/js/[name].chunk.js?_v=[chunkhash:8]';

      } else {
        // 更改开发模式输出的文件名
        config.output.filename = 'static/js/[name].js';
        config.output.chunkFilename = 'static/js/[name].chunk.js';
      }

      // 修改入口
      config.entry = entries;

      // 修改 HtmlWebpackPlugin 插件
      for (let i = 0; i < config.plugins.length; i++) {
        let item = config.plugins[i];
        if (item.constructor.toString().indexOf('class HtmlWebpackPlugin') > -1) {
          config.plugins.splice(i, 1);
        }
      }

      config.plugins.push(...htmlPlugin);

      // 分析打包内容
      // config.plugins.push(new BundleAnalyzerPlugin());

      // 设置别名路径
      config.resolve.alias = {
        ...config.resolve.alias,
        '@src': paths.appSrc, // 在使用中有些 Eslint 规则会报错, 禁用这部分代码的 Eslint 检测即可
      };

      // 处理 html 文档中图片路径问题
      config.module.rules[2].oneOf.push({
        test: /\.html$/,
        loader: 'html-withimg-loader'
      });

      // 修改 build/static/media/ 路径下的文件名
      for (let i = 0; i < config.module.rules[2].oneOf.length; i++) {
        const item = config.module.rules[2].oneOf[i];
        if(!item.options || !item.options.name){ 
          continue;
        }
        let str = item.options.name.toString();
        if(str.indexOf('static/media/[name].[hash:8].[ext]') > -1){
          item.options.name = 'static/media/[name].[ext]?_v=[hash:8]';
        }
      }

      // 修改代码拆分规则，详见 webpack 文档：https://webpack.js.org/plugins/split-chunks-plugin/#optimization-splitchunks
      config.optimization = {
        splitChunks: {
          // 将所有入口点共同使用到的、次数超过 2 次的模块，创建为一个名为 commons 的代码块
          // 这种配置方式可能会增大初始的捆绑包，比如有些公共模块在首页其实并未用到，但也会打包进来，会降低首页的加载性能
          // 建议将非必需模块使用 import() 的方式动态加载，提升页面的加载速度
          // cacheGroups: {
          //   commons: {
          //     name: 'commons',
          //     chunks: 'initial',
          //     minChunks: 2
          //   }
          // }

          // 将所有使用到的 node_modules 中的模块包打包为 vendors 代码块。（不推荐）
          // 这种方式可能会产生一个包含所有外部依赖包的较大代码块，建议只包含核心框架和工具函数代码，其他依赖项动态加载
          // cacheGroups: {
          //   commons: {
          //     test: /[\\/]node_modules[\\/]/,
          //     name: 'vendors',
          //     chunks: 'all'
          //   }
          // }

          cacheGroups: {
            // 通过正则匹配，将 react react-dom echarts-for-react 等公共模块拆分为 vendor
            // 这里仅作为示例，具体需要拆分哪些模块需要根据项目需要进行配置
            // 可以通过 BundleAnalyzerPlugin 帮助确定拆分哪些模块包
            vendor: {
              test: /[\\/]node_modules[\\/](react|react-dom|echarts-for-react)[\\/]/,
              name: 'vendor',
              chunks: 'all', // all, async, and initial
            },

            // 将 css|less 文件合并成一个文件, mini-css-extract-plugin 的用法请参见文档：https://www.npmjs.com/package/mini-css-extract-plugin
            // MiniCssExtractPlugin 会将动态 import 引入的模块的样式文件也分离出去，将这些样式文件合并成一个文件可以提高渲染速度
            // 其实如果可以不使用 mini-css-extract-plugin 这个插件，即不分离样式文件，可能更适合本方案，但是我没有找到方法去除这个插件
            styles: {            
              name: 'styles',
              test: /\.css|less$/,
              chunks: 'all',    // merge all the css chunk to one file
              enforce: true
            }
          },
        },
      };

      return config;
    };
  ```