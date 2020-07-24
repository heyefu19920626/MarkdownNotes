# NodeJs

- [NodeJs](#nodejs)
  - [npm仓库设置](#npm仓库设置)
    - [单项目仓库设置](#单项目仓库设置)
    - [全局配置](#全局配置)
  - [npm全局安装](#npm全局安装)
  - [引入bootstrap](#引入bootstrap)
  - [国际化](#国际化)

## npm仓库设置

### 单项目仓库设置

1. 在项目目录下创建`.npmrc`文件
2. 在该文件中写入仓库地址
```
registry=http://npm.cloudartifact.szv.dragon.tools.huawei.com/artifactory/api/npm/sz-npm-public 
```

### 全局配置

```
//设置淘宝源
npm config set registry https://registry.npm.taobao.org
//查看源，可以看到设置过的所有的源
npm config get registry
<!-- 换成官网镜像 -->
npm config set registry https://registry.npmjs.org
```

## npm全局安装
1. npm安装模块
   1. `npm install xxx`利用npm 安装xxx模块到当前命令行所在目录
   2. `npm install -g xxx`利用npm安装全局模块xxx
2. 本地安装时将模块写入package.json中
   1. `npm install xxx`安装但不写入package.json
   2. `npm install xxx –save`安装并写入package.json的`dependencies`中
   3. `npm install xxx –save-dev`安装并写入package.json的`devDependencies`中
3. npm卸载模块
   1. `npm uninstall xxx`删除xxx模块
   2. `npm uninstall -g xxx`删除全局模块xxx

## 引入bootstrap

1. 安装bootstrap,jquery,popper.js
   1. `npm install bootstrap`
   2. `npm install --svae jquery`
   3. `npm install --save popper.js`
2. 在angular.json中引入css和对应js
```json
build{
  "styles": [
    "src/styles.css",
    "./node_modules/bootstrap/dist/css/bootstrap.css"
  ],
  "scripts": [
    "./node_modules/jquery/dist/jquery.js",
    "./node_modules/popper.js/dist/popper.js",
    "./node_modules/bootstrap/dist/js/bootstrap.js"
  ]
}
```

## 国际化

1. 安装依赖
   1. `npm install @ngx-translate/core @ngx-translate/http-loader --save`
2. 在`app.module.ts`中全局注册，并使用iot
```js
@NgModule({
  imports: [
  // 放在这以便全局注册, 翻译模块
  HttpClientModule,
  TranslateModule.forRoot({
      loader: {
          provide: TranslateLoader,
          useFactory: HttpLoaderFactory,
          deps: [HttpClient]
      }
  })]
})

export function HttpLoaderFactory(http: HttpClient) {
    // 默认i18n可以省略
    return new TranslateHttpLoader(http, '../assets/i18n/', '.json');
}
```
3. 在`app.component.ts`中使用服务
```js
export class AppComponent {
    constructor(private translate: TranslateService) {
        this.translate.setDefaultLang("zh")
    }

    changeLanguage(language: string) {
        this.translate.setDefaultLang(language); //设置默认的语言类型
        this.translateService.use(language); // 设置使用的语言类型
    }
}
```
4. 在src/assets/下建立i18n文件夹，并在其中建立`zh.json,en.json`等国际化文件
```json
{
  "i18n": {
    "top-bar": {
      "title": "我的商店",
      "title.title": "{{name}}的店"
    },
    "title": "我的应用商店",
    "title.title": "{{name}}的商店"
  }
}
```
5. 在界面增加函数调用
```html
<!-- 参数中的name可以从ts文件中传入 -->
<!-- 使用管道 -->
<h1 title="{{'i18n.top-bar.title'|translate:{name: name } }}">{{'i18n.top-bar.title' | translate}}</h1>
<span>{{ 'welcome '| translate}}</span>
<span>{{getName | translate: {name:"crk"} }}</span>
<!-- 使用指令 -->
<span [translate]="'welcome'">welcome</span>
<span [translate]="getName" [translateParams]="{'name':'ggg'}">getName</span>
```
```js
// 使用服务
this.translate.get('welcome').subscribe((res: string) => {
    console.log(res);
    this.welcomeTranslate = res;
});
this.translate.get('getName',this.param).subscribe((res: string) => {
  console.log(res);
  this.getNameTranslate = res;
});
```