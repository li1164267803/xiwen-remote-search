<template>
  <Select
    v-bind="{ ...$props, ...$attrs }"
    placeholder="请输入"
    style="width: 100%"
    :filter-option="false"
    :not-found-content="fetching ? undefined : null"
    v-on="$listeners"
    @search="fetchUser"
    @change="val => (tableSearch ? false : handleChange(val))"
    @deselect="deselect"
  >
    <spin v-if="fetching" slot="notFoundContent" size="small" />
    <!-- a-select-option不能换行，换行会使label中有空格展示 -->
    <a-select-option
      v-for="k in userOption"
      :key="k[optGroupKeyLable[0]]"
      :value="k[optGroupKeyLable[0]]"
      :disabled="k.disabled"
      >{{ k[optGroupKeyLable[1]] }}</a-select-option
    >
  </Select>
</template>

<script>
import debounce from 'lodash/debounce';
import { Select, Spin, message } from 'ant-design-vue';

export default {
  name: 'GhRemoteSearch',
  components: { Select, Spin, message, ASelectOption: Select.Option },
  model: {
    prop: 'value',
    event: 'change',
  },
  // api https://www.antdv.com/components/select-cn/#API
  props: {
    value: [Array, Object, String, Number],
    /**
     * getList 为远程请求接口的方法，
     * const getInsList = value => Interface({ insName: value });
     */
    getList: {
      type: Function,
      required: true,
    },
    optGroupKeyLable: {
      // 用于给后台提交的数据格式
      // 例：optGroupKeyLable="['userId', 'userName']"
      // userId 相当于是key， userName 相当于是lable
      type: Array,
      required: true,
    },
    labelInValue: {
      type: Boolean,
      default: true,
    },
    showSearch: {
      // 使单选模式可搜索
      type: Boolean,
      default: true,
    },
    tableSearch: {
      // 在表格中，使用单选 远程搜索，v-model时，只返回value值
      // 在新增，编辑，需要人员姓名时，该属性为false，会返回 {key,value}的形式
      // 同时组件内不能绑定 @change 事件
      type: Boolean,
      default: false,
    },
    mode: {
      // 设置 Select 的模式为多选或标签
      // 'default' | 'multiple' | 'tags' | 'combobox'
      type: String,
      default: 'multiple',
    },
    isLimit: {
      type: Boolean,
      default: false,
    },
    limitMax: {
      type: Number,
      default: 5,
    },
    limitMin: {
      type: Number,
      default: 1,
    },
    limitMinText: {
      type: String,
      default: '分配运营人数不能小于1人',
    },
    limitMaxText: {
      type: String,
      default: '运营顾问数量已达上限',
    },
    callBack: {
      // 请求数据后的回调
      type: Function,
    },
  },
  data() {
    const fetchUserFn = async value => {
      this.userOption = [];
      this.fetching = true;
      let list = await this.getList(value);
      // 请求数据后的回调——可在外部做数据的二次处理
      if(this.callBack) list =  this.callBack(list);
      this.userOption.push(...list);
      this.fetching = false;
    };
    return {
      userOption: [], // 下拉框搜索的数据
      fetching: false, // loading
      fetchUser: debounce(fetchUserFn, 800),
      deselectOpt: null, // 记录最后一个被删除的数据
    }
  },
  methods: {
      handleChange(value) {
      // 该方法只会在需要{key,value}下执行
      if (this.isLimit) {
        // 限制选择的个数
        if (value.length < this.limitMin) {
          value.push(this.deselectOpt);
          return message.warning(`${this.limitMinText}`);
        }
        if (value.length > this.limitMax) {
          value.splice(value.length - 1, 1);
          message.warning(`${this.limitMaxText}`);
        }
      }
      this.fetching = false;
      this.userOption = []; // 是否在搜索完后，清空下拉列表

      // 多个时为数组
      if (Array.isArray(value)) {
        value.forEach(v => {
          v[this.optGroupKeyLable[0]] = v.key;
          v[this.optGroupKeyLable[1]] = v.label;
        });
      } else {
        // 单个时需要返回{key,value}
        value[this.optGroupKeyLable[0]] = value.key;
        value[this.optGroupKeyLable[1]] = value.label;
      }
    },
     deselect (value) {
      // 在multiple模式下，删除调用 保留最后删除的元素
      value[this.optGroupKeyLable[0]] = value.key;
      value[this.optGroupKeyLable[1]] = value.label;
      this.deselectOpt = value;
    },
  },
};
</script>
