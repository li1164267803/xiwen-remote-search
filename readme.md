## 背景

**后台管理系统 crm 中会经常用到联想组件，接口返回数据普遍的值都不是一样的，
例子：返回 [{name:'小米',age:20}],但组件默认接受的字段为 key 和 label，所有这个格式不是我们直接需要的
为了避免写无用转换的代码和请求接口，封装此组件**

### 一、效果

![在这里插入图片描述](https://img-blog.csdnimg.cn/20210526135157261.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDMwOTM3NA==,size_16,color_FFFFFF,t_70)

### 使用说明

远程联想组件，只需要配置一个请求的方法 getList 和后台返回的数据格式 optGroupKeyLable=['id','name']，正常输入即可实现联想
有的后台需要{key，value}的格式提交，比如{id：1，name：'小王'}, 该组件默认 labelInValue 是 true，返回格式为{key:1,label:'小王',id:1,name:'小王'}

```html
<div>
  <span>单选联想搜索—可以传入callBack做数据的二次处理 禁用某个选项——</span>
  <span>选中了：{{singleChoice}}</span>
</div>
  <xiwen-remote-search
    v-model="singleChoice"
    mode="default"
    tableSearch
    :labelInValue="false"
    :optGroupKeyLable="['id', 'name']"
    :callBack="callBack"
    :getList="getList" />
</div>
```

```html
<!-- 单选搜索 需返回{key,value}形式< -->
<div>
  <div>
    <span>单选搜索 需返回{key,value}形式</span>
    <span>选中了：{{user}}</span>
  </div>
  <xiwen-remote-search
    v-model="user"
    mode="default"
    :optGroupKeyLable="['id', 'name']"
    :getList="getList"
  />
</div>
```

## 源码地址

**[https://github.com/li1164267803/xiwen-remote-search](https://github.com/li1164267803/xiwen-remote-search)**

## API

**下面只列出了在本插件中二次封装添加的新字段，和部分原有 antd-vue 中 select 部分默认的配置**  
**具体的 antd-vue 中 select api 配置请点击下面链接，查看** <a target="_blank" href="https://www.antdv.com/components/select-cn/#API">官方文档</a>

| 参数             | 说明                                                                                                                                                            | 类型                           | 默认值                                                                                     |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ------------------------------------------------------------------------------------------ |
| getList          | 需要远程发起的请求接口                                                                                                                                          | _function_                     | 必填项: const getList = value => Interface({ insName: value });                            |
| optGroupKeyLable | 用于展示请求回来的数据格式                                                                                                                                      | Array                          | 必填项 例:['id','name'] ,会拿数组 0 下标的 id 当做需要绑定的值，name 为展示 ，顺序不能颠倒 |
| labelInValue     | 是否返回{key,value}形式                                                                                                                                         | Boolean                        | true                                                                                       |
| showSearch       | 使单选模式可搜索                                                                                                                                                | Boolean                        | true                                                                                       |
| tableSearch      | 在表格中，使用单选 远程搜索，v-model 时，只返回 value 值 在新增，编辑，需要人员姓名时，该属性为 false，会返回 {key,value}的形式 同时组件内不能绑定 @change 事件 | Boolean                        | false                                                                                      |
| mode             | 设置 Select 的模式为多选或标签                                                                                                                                  | default/multiple/tags/combobox | multiple                                                                                   |
| isLimit          | 多选是否限制选择的数量                                                                                                                                          | Boolean                        | fasle                                                                                      |
| limitMin         | 最大 5                                                                                                                                                          | Number                         | 1                                                                                          |
| limitMax         | 最小 1                                                                                                                                                          | Number                         | 5                                                                                          |
| limitMinText     | 满足最小提示文案                                                                                                                                                | String                         | 分配运营人数不能小于 1 人                                                                  |
| limitMaxText     | 满足最大提示文案                                                                                                                                                | String                         | 运营顾问数量已达上限                                                                       |
| callBack         | 用于请求后的数据做二次处理                                                                                                                                      | _function_                     | null                                                                                       |
