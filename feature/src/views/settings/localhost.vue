<template>
  <a-alert
    message="把插件发布到公网 npm 如果不符合您的公司安全要求，rubick 支持内网私有源和私有插件库，如果您需要内网部署使用，可以自行配置以下规则。"
    type="warning"
    style="margin-bottom: 20px"
  />
  <a-form
    name="custom-validation"
    ref="formRef"
    :model="formState"
    :rules="rules"
    v-bind="layout"
  >
    <a-form-item has-feedback label="npm 源" name="register">
      <a-input
        placeholder="https://registry.npm.taobao.org"
        v-model:value="formState.register"
      />
    </a-form-item>
    <a-form-item has-feedback label="database url" name="database">
      <a-input
        placeholder="https://gitcode.net/rubickcenter/rubick-database/-/raw/master"
        v-model:value="formState.database"
      />
    </a-form-item>
    <a-form-item has-feedback label="access_token" name="access_token">
      <a-input
        placeholder="内网gitlab仓库必填"
        v-model:value="formState.access_token"
      />
    </a-form-item>
    <a-form-item :wrapper-col="{ span: 18, offset: 6 }">
      <a-button @click="submit" type="primary">确定</a-button>
      <a-button style="margin-left: 10px" @click="resetForm">恢复默认</a-button>
    </a-form-item>
  </a-form>
</template>
<script lang="ts" setup>
import { ref, toRaw } from 'vue';
import { message } from 'ant-design-vue';

let _rev: any;

let defaultConfig = {
  register: 'https://registry.npm.taobao.org',
  database: 'https://gitcode.net/rubickcenter/rubick-database/-/raw/master',
  access_token: '',
};

try {
  const dbdata = window.rubick.db.get('rubick-localhost-config');
  defaultConfig = dbdata.data;
  _rev = dbdata._rev;
} catch (e) {
  // ignore
}

const formState = ref(JSON.parse(JSON.stringify(defaultConfig)));

const rules = {
  register: [{ required: true, trigger: 'change' }],
  database: [{ required: true, trigger: 'change' }],
};
const layout = {
  labelCol: { span: 6 },
  wrapperCol: { span: 18 },
};

const resetForm = () => {
  formState.value = {
    register: 'https://registry.npm.taobao.org',
    database: 'https://gitcode.net/rubickcenter/rubick-database/-/raw/master',
    access_token: '',
  };
};

const submit = () => {
  const changeData: any = {
    _id: 'rubick-localhost-config',
    data: toRaw(formState.value),
  };

  if (_rev) {
    changeData._rev = _rev;
  }

  window.rubick.db.put(changeData);
  message.success('设置成功！重启插件市场后生效！');
};
</script>

<style lang="less" scoped>
.ant-form {
  :deep(.ant-form-item) {
    label {
      color: var(--color-text-content);
    }
  }
}
:deep(.ant-input) {
  background: var(--color-input-hover);
  color: var(--color-text-content);
}
</style>
