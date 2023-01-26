## OSI TCP/IP 计算机网络的模型设计

### OSI open system interconnection 



![1674541717073](assets/1674541717073.png)

**1 物理层**

**2 数据链路层 data link layer**

接受物理层的数据，封装成帧， 转发上层数据解析的内容发给物理层

物理层上有差错的数据 -> 无差错的数据帧

**3 网络层 network layer**

IP  ICMP

通过路由选择算法，将报文通过线路传输给指定的结点，选择最合适的路径

**4 传输层 transport layer**

TCP UDP

进行端对端的有差错控制， 流量控制，保证报文准确性

**5 会话层 session layer**

两个真是实体建立链接和使用的过程

**6 表示层 presentation layer**

承上启下 将第七层的数据解析格式，传输给会话层

**7 应用层**

![1674541811595](assets/1674541811595.png)

### TCP IP 模型

{

  "name": "react-ts-antd",

  "version": "0.1.0",

  "private": true,

  "dependencies": {

​    "@testing-library/jest-dom": "^5.16.5",

​    "@testing-library/react": "^13.4.0",

​    "@testing-library/user-event": "^13.5.0",

​    "@types/jest": "^29.2.6",

​    "@types/node": "^18.11.18",

​    "@types/react": "^18.0.27",

​    "@types/react-dom": "^18.0.10",

​    "@types/redux-persist": "^4.3.1",

​    "antd": "^5.1.6",

​    "less": "^4.1.3",

​    "less-loader": "5.0.0",

​    "react": "^18.2.0",

​    "react-dom": "^18.2.0",

​    "react-redux": "^8.0.5",

​    "react-router-dom": "^6.7.0",

​    "react-scripts": "5.0.1",

​    "redux": "^4.2.0",

​    "redux-persist": "^6.0.0",

​    "redux-thunk": "^2.4.2",

​    "typescript": "^4.9.4",

​    "web-vitals": "^2.1.4"

  },

  "scripts": {

​    "start": "set BROWSER=none&& set PORT=9000&& react-app-rewired start",

​    "build": "react-app-rewired build",

​    "test": "react-app-rewired test",

​    "eject": "react-app-rewired eject"

  },

  "eslintConfig": {

​    "extends": [

​      "react-app",

​      "react-app/jest"

​    ]

  },

  "browserslist": {

​    "production": [

​      ">0.2%",

​      "not dead",

​      "not op_mini all"

​    ],

​    "development": [

​      "last 1 chrome version",

​      "last 1 firefox version",

​      "last 1 safari version"

​    ]

  },

  "devDependencies": {

​    "@babel/plugin-proposal-decorators": "^7.20.13",

​    "babel-plugin-import": "^1.13.6",

​    "compression-webpack-plugin": "^10.0.0",

​    "customize-cra": "^1.0.0",

​    "react-app-rewired": "^2.2.1",

​    "uglifyjs-webpack-plugin": "^2.2.0"

  }

}

