<template>
  <div class="tdesign-demo-block-column-large">
    <div>
      <t-button @click="visible = true">添加机器</t-button>
      <t-dialog header="机器信息" :visible="visible" :cancelBtn="null" :confirmBtn="null"
                @close-btn-click="visible = false">
        <div slot="body">
          <div>
            <t-form :data="formData" :rules="rules" ref="form" @reset="onReset" @submit="onSubmit"
                    @validate="onValidate"
                    scrollToFirstError="smooth">
              <t-form-item label="ip" help="机器地址" name="ip">
                <t-input v-model="formData.ip"></t-input>
              </t-form-item>
              <t-form-item label="端口" name="port">
                <t-input v-model="formData.port"></t-input>
              </t-form-item>
              <t-form-item label="登录用户" name="user">
                <t-input v-model="formData.user"></t-input>
              </t-form-item>
              <t-form-item label="密码" name="password">
                <t-input type="password" v-model="formData.password"></t-input>
              </t-form-item>
              <t-form-item style="margin-left: 100px">
                <t-button theme="primary" variant="outline" style="margin-right: 10px">测试</t-button>
                <t-button theme="primary" type="submit" style="margin-right: 10px">保存</t-button>
                <t-button theme="default" variant="base" type="reset" style="margin-right: 10px">重置</t-button>
              </t-form-item>
            </t-form>
          </div>
        </div>
      </t-dialog>
    </div>
    <t-table
        rowKey="index"
        :data="data"
        :columns="columns"
        :displayColumns.sync="displayColumns"
        :hover="true"
        size="small"
        :columnController="{
        placement,
        fields: ['platform', 'type', 'default', 'op'],
        dialogProps: { preventScrollThrough: true },
        buttonProps: customText ? { content: '显示列控制', theme: 'primary', variant: 'base' } : undefined,
      }"
        :pagination="{ defaultPageSize: 5, defaultCurrent: 1, total: 100 }"
        :bordered="bordered"
        tableLayout="auto"
        stripe
        drag-sort="col"
        @drag-sort="onDragSortChange"
        @column-change="onColumnChange"
    >
      <template #op-column><p>操作</p></template>
      <template #op="slotProps">
        <t-button variant="text" theme="primary" @click="rehandleClickOp(slotProps)">编辑</t-button>
        <t-button variant="text" theme="danger" @click="rehandleClickOp(slotProps)">删除</t-button>
<!--        <t-button variant="text" theme="danger" @click="rehandleClickOp(slotProps)">重启</t-button>-->
      </template>
    </t-table>
  </div>
</template>
<script lang="jsx">
const data = [];
for (let i = 1; i < 100; i++) {
  data.push({
    index: i,
    ip: '10.10.90.125',
    port: 22,
    user: 'cpicapp'
  });
}

const staticColumn = ['user', 'password'];
export default {
  name: 'AppDeployment',
  data() {
    return {
      visible: false,
      data,
      placement: 'top-left',
      customText: true,
      bordered: true,
      displayColumns: staticColumn.concat(['index', 'ip', 'port', 'user', 'password', 'op']),
      columns: [
        {
          align: 'center',
          className: 'row',
          colKey: 'index',
          title: '序号',
        },
        {
          colKey: 'ip',
          title: 'ip',
        },
        {
          colKey: 'port',
          title: '端口',
        },
        {
          colKey: 'user',
          title: '用户名',
        },
        {
          colKey: 'password',
          title: '密码',
        },
        {
          colKey: 'op',
          title: 'op-column',
          cell: 'op',
          fixed: 'right'
        }
      ],
      formData: {}
    };
  },

  methods: {
    onColumnChange(params) {
      console.log(params);
    },
    onDragSortChange({newData}) {
      this.columns = newData;
    },
    onReset() {

    },
    onSubmit() {

    },
    onValidate() {

    }
  },
};
</script>

<style lang="scss" scoped>
.link {
  cursor: pointer;
  margin-right: 15px;
}
</style>
