## Dva

### Dva 配置 TypeScript

1，全局安装 typeScript：

```js
npm i typescript -g
```

2，安装所需依赖：

```js
npm i link typescript
npm i --save-dev ts-loader source-map-loader
npm i --save-dev @types/react @types/react-dom
```

3，配置 tsconfig.json 文件：

```json
{
  "compilerOptions": {
    "target": "es5",                         
    "module": "commonjs",                     
    "lib": ["es6", "es2016", "es2017", "es2018", "esnext", "dom"],                            
    "allowJs": true,                      
    "skipLibCheck": true,
    "jsx": "react",                     
    "sourceMap": true,                     
    "noEmit": true,                        
    "importHelpers": true,                 
    "strict": true,                        
    "strictNullChecks": true,              
    "noUnusedLocals": true,                
    "moduleResolution": "node",             
    "baseUrl": "./",                       
    "paths": {
      "@/*": ["src/*"],
      "$/*": ["typings/*"]
    },
    "allowSyntheticDefaultImports": true,  
    "esModuleInterop": true,             
    "experimentalDecorators": true      
  },
  "include": [
    "src",
    "typings"
  ],
  "exclude": [
    "build",
    "vendor",
    "**/*/_*",
  ]
}
```

4，声明全局变量，以便于解决在 ts 文件模块中使用 `import styles from '.index.less'` 的方式引入样式资源报找不到模块的错误。

<1>，首先在与 `src` 文件同级目录下创建一个 `typings` 文件夹。

<2>，在 typings 文件夹中创建一个 `index.d.ts` 文件，该文件具体配置如下：

```js

declare module '*.svg' {
  const url: string;
  export default url;
}

declare module '*.png' {
  const url: string;
  export default url;
}

declare module '*.webp' {
  const url: string;
  export default url;
}

declare module '*.webm' {
  const url: string;
  export default url;
}

declare module '*.mp4' {
  const url: string;
  export default url;
}

declare module '*.jpg' {
  const url: string;
  export default url;
}

/**
 * 考虑使用 typed-css-modules 对 less 自动生成 d.ts （主要是需要开进程 watch 编译，比较麻烦）
 * 或者调研 TypeScript 扩展
 */
declare module '*.less' {
  const styles: Record<string, string>;
  export default styles;
}

declare module '*.css' {
  const styles: Record<string, string>;
  export default styles;
}

declare module 'dva-loading' {
  export default function createLoading(options: any): any;
}

declare module '*.woff';
declare module '*.woff2';
declare module '*.otf';


declare module 'echarts/map/json/*.json' {
  const json: any;
  export default json;
}
declare module 'react-lines-ellipsis' {
  // type definitions goes here
  const LinesEllipsis: any;
  export default LinesEllipsis;
}

declare module 'react-lines-ellipsis/lib/responsiveHOC' {
  const responsiveHOC: any;
  export default responsiveHOC;
}

declare module 'react-useanimations' {
  const UseAnimations: any;
  export default UseAnimations;
}

interface HTMLVideoElement {
  __hls__?: Hls;
}

interface Window {
  HLS_JS_DEBUG?: boolean;
}

declare module 'china-division/dist/*.json' {
  const json: any;
  export default json;
}

declare module 'braft-utils' {
  const ContentUtils: any;
  export { ContentUtils };
}
```

**说明**：`index.d.ts` 不需要在具体的文件模块中引用，只需要在 `tsconfig` 中按照上述 tsconfig.json 中配置即可。

### 配置 antd 按需加载

1，安装 antd：

```js
npm i antd -S
```

2，安装所需依赖 `babel-plugin-import`：

```js
npm i babel-plugin-import -S
```

3，在 `.webpackrc.js` 中配置如下代码：

```js
extraBabelPlugins: [
  ['import', { libraryName: 'antd', libraryDirectory: 'es', style: true }]
]
```

### webpack 基本配置

1，在项目根目录下的 `.webpackrc.js` 文件中配置如下代码：

```js
const path = require('path');
const { name, version } = require('./package.json');

export default {
  entry: 'src/index.js',
  outputPath: 'build',
  urlLoaderExcludes: [/assets\/icons\/|magicUI\/Base\/Icon\/icons\//],
  es5ImcompatibleVersions: true,
  extraBabelPlugins: [
    ['import', { libraryName: 'antd', libraryDirectory: 'es', style: true }]
  ],
  env: {
    development: {
      extraBabelPlugins: ['dva-hmr']
    }
  },
  alias: {
      components: path.resolve(__dirname, 'src/components/')
  },
  alias: {
    '@': path.resolve(__dirname, './src'),
    '$': path.resolve(__dirname, './typings'),
  },
  html: {
    template: './src/index.ejs'
  },
  publicPath: '/',
  proxy: {

  }
};
```

### Dva Router

1，解决 dva 中使用 `createBrowserHistory` 的路由方式创建二级路由时报 `＂Uncaught SyntaxError: Unexpected token <＂` 的错误。

<1>，只需要在项目的 `index.html` 中引入项目 `index.js` 文件时使用绝对路径即可解决该问题，如下：

```js
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Dva Demo</title>
  <link rel="stylesheet" href="index.css" />
</head>

<body>
  <div id="root"></div>
  <script src="/index.js"></script>
</body>

</html>
```


