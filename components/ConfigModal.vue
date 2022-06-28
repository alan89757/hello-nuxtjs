<template>
  <a-modal
    v-model="modal2Visible"
    title="新增配置"
    okText="提交"
    cancelText="取消"
    centered
    @ok="handleSubmit"
  >
  <a-form :form="form" :label-col="{ span: 5 }" :wrapper-col="{ span: 12 }" @submit="handleSubmit">
    <a-form-item label="Key">
      <a-input
        v-decorator="['key', { rules: [{ required: true, message: '请输入Key' }] }]"
      />
    </a-form-item>
    <a-form-item label="Value">
      <a-input
        v-decorator="['value', { rules: [{ required: true, message: '请输入Value' }] }]"
      />
    </a-form-item>
    <a-form-item label="备注">
      <a-input
        v-decorator="['remark']"
      />
    </a-form-item>
    <!-- <a-form-item :wrapper-col="{ span: 12, offset: 5 }">
      <a-button type="primary" html-type="submit">
        提交
      </a-button>
    </a-form-item> -->
  </a-form>
</a-modal>
  
</template>

<script>

import axios from 'axios';
export default {
  props: {
    modal2Visible: Boolean
  },
  data() {
    return {
      formLayout: 'horizontal',
      form: this.$form.createForm(this, { name: 'coordinated' }),
    };
  },
  methods: {
    handleSubmit(e) {
      e.preventDefault();
      this.form.validateFields((err, values) => {
        if (!err) {
          console.log('Received values of form: ', values);
          axios.post('http://localhost:3000/config/create', values)
          .then(function (response) {
            console.log(response);
          })
          .catch(function (error) {
            console.log(error);
          });
          this.$emit("onClose", true);
        }
      });
    },
    handleSelectChange(value) {
      console.log(value);
      this.form.setFieldsValue({
        note: `Hi, ${value === 'male' ? 'man' : 'lady'}!`,
      });
    },
  },
};
</script>