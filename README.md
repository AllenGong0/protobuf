
# protobuf code generate
### protobuf
推荐指数：*
注：protoc 即 protobuf cli

protobuf 本身并未对 ts js 有支持，需要通过插件实现
或 通过 https://github.com/protocolbuffers/protobuf-javascript 自行编译 protoc 支持 js

```brew install protobuf```

##### TS

安装插件
``` yarn add -D protoc-gen-ts ```

代码生生成
```
protoc --plugin=./node_modules/.bin/protoc-gen-ts ./proto/example.proto --ts_proto_out=dist
```
使用
```typescript
import { MyMessage } from "./dist/proto/example";

const buffer = MyMessage.encode(
  MyMessage.create({
    id: "123",
  })
).finish();

const obj = MyMessage.decode(buffer);
console.log(obj);
```
##### JS

安装 插件
``` yarn add -D protoc-gen-js ```

代码生成
```
protoc --plugin=./node_modules/.bin/protoc-gen-js ./proto/example.proto --js_out=dist
```
使用
支持不是太好，暂未研究出使用方式

### buf
推荐指数：*****
https://buf.build/docs/

通过配置 yaml 生成不同平台文件
buf.yam 为基础配置文件
buf.gen.yaml 生成不同平台代码的配置文件

```
brew intsall buf
```
or
```
yarn add -D @bufbuild/buf
```

运行命令
```
buf generate
```
or
```
npx buf generate 
```


### js 中使用
buf 打包出来的代码并未提供序列化以及反序列化方式，所以需要额外安装 @bufbuild/protobuf 来进行序列化以及反序列化

```
 yarn add @bufbuild/protobuf
```

````js

import { MyMessageSchema } from "./gen/proto/example_pb.js";
import { create, toBinary, toJson, fromBinary } from "@bufbuild/protobuf";

let user = create(MyMessageSchema, {
  id: "12",
  age: 25,
  mes: "30",
});

const bytes = toBinary(MyMessageSchema, user);
console.log(fromBinary(MyMessageSchema, bytes, { readUnknownFields: false }));
````

优点：
1. 多语言支持友好
2. 插件可以使用 remote 版本，不需要本地安装
缺点：
1. 导出的文件没有 namespace
2. 在项目中使用时，需要 额外安装 "@bufbuild/protobuf"

## pbjs
注：高版本只能生成 js/ts 文件
推荐指数：***
protobuf version 6.11.3

安装
```
yarn add -D protobuf@6.11.3
yarn add pbjs
```

生成 js 文件

````
npx pbjs -t static-module -w commonjs -o dist/example_pb.js ./proto/example.proto
````

生成 .d.ts

````
npx pbts -o dist/example_pb.d.ts dist/example_pb.js
````

使用
```ts
import { Example } from "./dist/example_pb";
const buffer = Example.MyMessage.encode(
  Example.MyMessage.create({
    id: "123",
  })
).finish();

const obj = Example.MyMessage.decode(buffer);
console.log(obj);

```

优点：
1. 打包出的文件即可直接使用，不需要在项目中额外安装
2. ts生命文件自带命名空间支持
缺点：
1. 在ts环境下需使用额外的命令生成ts文件
2. 高版本不支持生成d.ts 可能需要配合 tsc、rollup 等工具使用

