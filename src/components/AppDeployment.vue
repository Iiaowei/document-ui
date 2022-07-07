<template>
  <div class="tdesign-demo-block-column-large">
    <div>
      <t-loading :loading="loading" text="加载中..." fullscreen />
      <div style="display: flex;">
        <t-button style="margin-right: 10px" size="small" @click="visible = true">添加应用配置</t-button>
        <t-button style="margin-right: 10px" theme="primary" size="small" @click="start">启动</t-button>
        <t-button style="margin-right: 10px" theme="primary" size="small" @click="restart">重启</t-button>
        <t-button style="margin-right: 10px" theme="danger" size="small" @click="stop">停用</t-button>
<!--        <t-button style="margin-right: 10px" theme="danger" size="small" @click="download1">下载1</t-button>-->
<!--        <t-button style="margin-right: 10px" theme="danger" size="small" @click="download2">下载2</t-button>-->
      </div>
      <t-dialog header="应用信息" :visible="visible" :cancelBtn="null" :confirmBtn="null"
                @close-btn-click="visible = false">
        <div slot="body">
          <div>
            <t-form :data="formData" ref="form" @reset="onReset" @submit="onSubmit"
                    :rules="rules"
                    @validate="onValidate"
                    scrollToFirstError="smooth">
              <t-form-item label="应用名称" name="name">
                <t-input v-model="formData.name"></t-input>
              </t-form-item>
              <t-form-item label="部署方式" name="deploymentMode">
                <t-radio-group v-model="formData.deploymentMode">
                  <t-radio value="java">java</t-radio>
                  <t-radio value="nginx">nginx</t-radio>
                </t-radio-group>
              </t-form-item>
              <t-form-item label="机器" name="ip">
                <t-cascader class="t-demo-cascader" :options="options" v-model="formData.ip" trigger="hover"
                            :showAllLevels="false" multiple clearable/>
              </t-form-item>
              <t-form-item label="部署路径" name="path">
                <t-input v-model="formData.path"></t-input>
              </t-form-item>
              <t-form-item v-if="formData.deploymentMode === 'java'" label="启动命令" name="startCommand">
                <t-input v-model="formData.startCommand"></t-input>
              </t-form-item>
              <t-form-item v-if="formData.deploymentMode === 'java'" label="健康检查" name="health"
                           style="max-width: 500px">
                <t-input v-model="formData.health" placeholder="http://127.0.0.1:8080/actuator/health"/>
              </t-form-item>
              <t-form-item style="margin-left: 100px">
                <t-button theme="primary" type="submit" style="margin-right: 50px">保存</t-button>
                <t-button theme="primary" variant="outline" style="margin-right: 10px" @click="onCancel">取消</t-button>
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
        table-layout="fixed"
        :columnController="{
        placement,
        fields: ['ip', 'port', 'user', 'password', 'op'],
        dialogProps: { preventScrollThrough: true },
        buttonProps: customText ? { content: '显示列控制', theme: 'primary', variant: 'base' } : undefined,
      }"
        :pagination="{ defaultPageSize: 10, defaultCurrent: 1, total: 100 }"
        :bordered="bordered"
        stripe
        drag-sort="col"
        @drag-sort="onDragSortChange"
        @column-change="onColumnChange"
    >
      <template #state="{ row }">
        <t-loading v-if="row.state === 'running'" style="color: green" text="运行中" size="small"/>
        <t-loading v-if="row.state === 'startWarn'" text="正在启动" size="small"/>
        <t-loading v-if="row.state === 'stopWarn'" style="color: red" text="正在停用" size="small"/>
        <t-tag v-if="row.state === 'stop'" theme="danger">已停用</t-tag>
      </template>
      <template #op-column><p>操作</p></template>
      <template #op="slotProps">
        <div style="display: flex;">
          <t-button variant="text" theme="primary" size="small" @click="edit(slotProps)">编辑</t-button>
          <t-button variant="text" theme="danger" size="small" @click="remove(slotProps)">删除</t-button>
          <t-upload
              action=""
              v-model="file"
              @fail="handleFail"
              @success="onUploadSuccess"
              theme="custom"
              :beforeUpload="beforeUpload"
              :requestMethod="uploadMethod"
          >
            <t-button variant="text" style="color: blue" size="small">应用上传</t-button>
          </t-upload>
        </div>
      </template>
    </t-table>
  </div>
</template>
<script lang="jsx">
const data = [];
for (let i = 1; i < 100; i++) {
  data.push({
    index: i,
    name: '账户管理',
    state: ['running', 'startWarn', 'stopWarn', 'stop'][i % 4],
    deploymentMode: 'java',
    ip: '10.10.90.125',
    path: '/cpic/cpicapp/jars',
    startCommand: 'java -jar account-server.jar'
  });
}

const staticColumn = ['name', 'state', 'deploymentMode', 'ip', 'path', 'startCommand'];
export default {
  name: 'AppDeployment',
  data() {
    return {
      loading: false,
      file: [],
      visible: false,
      data,
      placement: 'top-left',
      customText: true,
      bordered: true,
      displayColumns: staticColumn.concat(['index', 'name', 'state', 'deploymentMode', 'ip', 'path', 'startCommand', 'op']),
      columns: [
        {
          align: 'center',
          className: 'row',
          colKey: 'index',
          title: '序号',
        },
        {
          colKey: 'name',
          title: '应用名称',
        },
        {
          colKey: 'state',
          title: '状态',
        },
        {
          colKey: 'deploymentMode',
          title: '部署方式',
        },
        {
          colKey: 'ip',
          title: '机器',
        },
        {
          colKey: 'path',
          title: '部署路径',
        },
        {
          colKey: 'startCommand',
          title: '启动命令',
        },
        {
          colKey: 'op',
          title: 'op-column',
          cell: 'op',
          fixed: 'right'
        }
      ],
      formData: {
        name: '',
        deploymentMode: 'java',
        ip: '',
        path: '',
        startCommand: ''
      },
      rules: {
        name: [{required: true, message: '必填', type: 'error'}],
        deploymentMode: [{required: true, message: '必填', type: 'error'}],
        ip: [{validator: () => true}],
        path: [{required: true, message: '必填', type: 'error'}]
      },
      options: [
        {
          label: '选项一',
          value: '1',
          children: [
            {
              label: '子选项一',
              value: '1.1',
            },
            {
              label: '子选项二',
              value: '1.2',
            },
            {
              label: '子选项三',
              value: '1.3',
            },
          ],
        },
        {
          label: '选项二',
          value: '2',
          children: [
            {
              label: '子选项一',
              value: '2.1',
            },
            {
              label: '子选项二',
              value: '2.2',
            },
          ],
        },
      ],
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

    },
    onCancel() {
      this.visible = false
    },
    handleFail({file}) {
      this.$message.error(`文件 ${file.name} 上传失败`);
    },

    beforeUpload(file) {
      if (file.size > 5 * 1024 * 1024) {
        // this.$message.warning('上传的图片不能大于5M');
        return true;
      }
      return true;
    },
    onUploadSuccess() {
      this.$message.success('上传成功');
    },
    uploadMethod(file) {
      console.log(file)
      const formData = new FormData()
      formData.append('file', file.raw)

      return new Promise((resolve, reject) => {
        this.request.post('/api/deployment/upload', formData)
            .then((res) => {
              return resolve({status: 'success', response: res})
            })
            .catch((res) => {
              return reject({status: 'fail', response: res, error: '上传失败'})
            })
      })
    },
    start() {

    },
    restart() {

    },
    stop() {

    },
    edit(slotProps) {
      console.log(slotProps)
    },
    remove(slotProps) {
      console.log(slotProps)
    },
    download1() {
      this.loading = true
      const config = {
        method: 'get',
        url: '/api/deployment/download',
        responseType: 'blob'
      };
      this.request.get(config).then(response => {
        const url = window.URL.createObjectURL(new Blob([response.data]));
        const link = document.createElement('a');
        link.href = url;
        link.setAttribute('download', 'xxxx.srt');
        document.body.appendChild(link);
        link.click();
        this.loading = false
      })
    },
    download2() {
      window.open("http://localhost:9931/api/deployment/download")
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
