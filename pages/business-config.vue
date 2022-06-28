<template>
  <div>
    <!-- <div class="header">
      <a-button class="editable-add-btn" @click="handleAdd">
        新增
      </a-button>
    </div> -->
    <a-table
      :columns="columns"
      :row-key="record => record.row_num"
      :data-source="dataSource"
      :pagination="false"
      :loading="loading"
    > 
      <template slot="key" slot-scope="key"> {{ key }}</template>
      <template slot="value" slot-scope="value, record">
        <editable-cell :text="value" :editable="true" @change="onCellChange(record.business_config_id, 'value', $event)" />
      </template>
      <template slot="remark" slot-scope="remark, record">
        <editable-cell :text="remark" :editable="true" @change="onCellChange(record.business_config_id, 'remark', $event)" />
      </template>
    </a-table>
  </div>
  
</template>
<script>

  import Axios from 'axios';
  import { message } from 'ant-design-vue';
  import EditableCell from '~/components/EditableCell';
  // import ConfigModal from '~/components/ConfigModal';

  const getEnv = function(){
    // return 'http://localhost:3000'
    const href = window.location.href;
    if(href.indexOf('-dev') > 0) {
      return 'https://api-dev.shinwell.cn';
    } else if(href.indexOf('-qa') > 0) {
      return 'https://api-qa.shinwell.cn';
    } else if(href.indexOf('-stg') > 0) {
      return 'https://api-stg.shinwell.cn';
    } else {
      return 'https://api.shinwell.cn';
    }
  }

  const queryData = params => {
    return Axios.get(`${getEnv()}/api/business-config/list`, { params: params });
  };
  const columns = [
    // {
    //   title: 'business_config_id',
    //   dataIndex: 'business_config_id',
    //   className:'tableHiddle',
    // },
    {
      title: 'Key',
      dataIndex: 'key',
      width: '30%',
      scopedSlots: { customRender: 'key' },
    },
    {
      title: 'Value',
      dataIndex: 'value',
      scopedSlots: { customRender: 'value' },
      width: '30%',
    },
    {
      title: '备注',
      dataIndex: 'remark',
      scopedSlots: { customRender: 'remark' },
    },
    // {
    //   title: '操作',
    //   key: 'action',
    //   scopedSlots: { customRender: 'action' },
    // },
  ];

  export default {
    components: {
      EditableCell
    },
    data() {
      return {
        dataSource: [],
        // pagination: {},
        loading: false,
        columns,
        addShow: false,
      };
    },
    mounted() {
      this.fetch();
    },
    methods: {
      // 关闭弹窗
      closeModal() {
        this.addShow = false;
      },
      // 编辑单元格
      onCellChange(key, dataIndex, value) {
        // console.log(key, dataIndex, value);
        const dataSource = [...this.dataSource];
        const target = dataSource.find(item => item.business_config_id === key);
        if (target) {
          const params = this.getParamsObj();
          target[dataIndex] = value;
          target['uid'] = params['uid'];
          this.dataSource = dataSource;
          // 更新
          Axios.post(`${getEnv()}/api/business-config/update`, target).then(({ data })=> {
            if(data.code === 0) {
              // this.dataSource = data?.data || [];
              message && message.success('操作成功');
            } else {
              message && message.error(data.message || '操作失败');
            }
          })
        }
      },
      // 参数对象
      getParamsObj() {
        const paramsString = location.search.substring(1);
        const searchParams = new URLSearchParams(paramsString);
        const params = {};
        for (let p of searchParams) {
          params[p[0]] = p[1];
        }
        return params;
      },
      fetch() {
        this.loading = true;
        const params = this.getParamsObj();
        queryData(params).then(({ data }) => {
          // console.log(data);
          // const { data } = reponse || {};
          // const { data, code } = data;
          if(data.code === 0) {
            this.dataSource = data?.data || [];
          } else {
            message && message.error(data.message);
          }
          this.loading = false;
        }).catch(err=> {
          console.log(err)
        })
      },
    },
  };
</script>

<style scoped>
  .header {
    margin: 20px
  }
  .tableHiddle {
    display: none;
  }
</style>
