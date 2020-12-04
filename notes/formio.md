### 安装相关依赖库

```shell
# 安装UI库

npm install vuetify --save-dev
```

```shell
# 安装Formio使用Vue简单封装的依赖库

npm install vue-formio --save-dev
```

### 引入相关样式表

```javascript
// import bootstrap
import 'bootstrap/dist/css/bootstrap.min.css'

// import formio css
import 'formiojs/dist/formio.full.min.css'
```

### API 接口及权限配置

因为 `Formio` 自带的后台使用 `JWT` 认证，所以需要在请求时带入 `x-jwt-token` 字段。

`x-jwt-token` 字段配置。因为用户体系并未使用 `Formio` 的用户体系，所以我们暂时将 `x-jwt-token` 字段配置在 `apiConfig` 文件中

```javascript
export const X_JWT_TOKEN =
  'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VyIjp7Il9pZCI6IjVjZTUyYTIyZWNlM2Q5MDA1OWEyOGRjZSJ9LCJmb3JtIjp7Il9pZCI6IjVjZTUyYTIxZWNlM2Q5MDA1OWEyOGRjMiJ9LCJpYXQiOjE1NTg2NjU3ODIsImV4cCI6MTU1ODY4MDE4Mn0.UhDqGi6XfITSptS71boDfbDirdiSO5xmsa_ezcvFLpI'
```

`axios` 请求拦截器配置

```javascript
import { X_JWT_TOKEN } from './apiConfig'

// 添加请求拦截器
request.interceptors.request.use(
  config => {
    config.headers['x-jwt-token'] = X_JWT_TOKEN
    return config
  },
  error => {
    console.log(error)
  }
)
```

`API` 配置

```javascript
import { fetch } from '@/api/fetch'

/**
 * 获取表单ID、Title、Name、Components等字段
 *
 * @export
 * @param {Object} { formId } 表单ID
 * @returns 数据请求结果
 */
export function getForm({ formId }) {
  const url = `form/${formId}`
  return fetch.get(url)
}

/**
 * 获取表单列表
 *
 * @export
 * @returns 数据请求结果
 */
export function getFormList() {
  const url = 'form?type=form'
  return fetch.get(url)
}

/**
 * 生成表单
 *
 * @export
 * @param {Object} formObj 生成表单的对象
 * @returns 数据请求结果
 */
export function generateForm(formObj) {
  const url = 'form'
  return fetch.post(url, formObj)
}

/**
 * 编辑表单
 *
 * @export
 * @param {Object} formObj 表单对象
 * @returns 数据请求结果
 */
export function editForm(formObj) {
  const url = `form/${formObj._id}`
  return fetch.put(url, formObj)
}

/**
 * 删除表单
 *
 * @export
 * @param {*} { formId } 表单ID
 * @returns 数据请求结果
 */
export function deleteForm({ formId }) {
  const url = `form/${formId}`
  return fetch.delete(url)
}

/**
 * 提交表单数据
 *
 * @export
 * @param {Object} { formId, formData } 表单ID，表单数据
 * @returns 数据请求结果
 */
export function submitForm({ formId, formData }) {
  const url = `form/${formId}/submission`
  return fetch.post(url, formData)
}

/**
 * 获取表单提交的历史数据
 *
 * @export
 * @param {Object} { formId, limit, skip, sort } 表单ID，数据条数，跳过条数，排序方式
 * @returns 数据请求结果
 */
export function getFormSubmissionArray({ formId, limit, skip, sort }) {
  const url = `form/${formId}/submission`
  const dataObj = {
    limit,
    skip,
    sort
  }
  return fetch.get(url, dataObj)
}
```

### 表单国际化

```javascript
const i18n = {
  // prettier-ignore
  zh: {
    'Basic Components': '基础组件',
    'Text Field': '文本框',
    'Text Area': '文本域',
    'Select Boxes': '复选框组',
    'Drag and Drop a form component': '拖放表单组件',
    'Number': '数字框',
    'Password': '密码框',
    'Checkbox': '复选框',
    'Time': '时间填写框',
    'Select': '下拉选择框',
    'Radio': '单选框',
    'Content': '富文本',
    'Button': '按钮',
    'Edit': '编辑',
    'Copy': '复制',
    'Remove': '删除',
    'Submit': '确认',
    'Component': '组件',
    'Display': '显示',
    'Label': '标签',
    'Preview': '预览',
    'Save': '保存',
    'Cancel': '取消',
    'Data': '数据',
    'Validation': '验证',
    'API': 'API',
    'Conditional': '条件',
    'Logic': '逻辑',
    'Hide Label': '隐藏标签',
    'Label Position': '标签位置',
    'Type to search': '输入搜索',
    'Top': '上方',
    'Left (Left-aligned)': '左边（左对齐）'
  }
}

export default i18n
```

### 表单构建器

```html
<template>
  <div class="form-builder">
    <page-title-bar></page-title-bar>

    <v-container fluid grid-list-xl pt-0>
      <v-layout row wrap>
        <v-flex xs12>
          <v-card style="padding: 40px;">
            <v-layout row wrap>
              <v-flex xs3 offset-xs2>
                <v-text-field v-model="builder.title" :counter="10" label="标题（中文或英文）" required></v-text-field>
              </v-flex>
              <v-flex xs3 offset-xs2>
                <v-text-field v-model="builder.name" :counter="10" label="名称（包含字母，数字，连字符）" required></v-text-field>
              </v-flex>
            </v-layout>

            <v-layout row wrap>
              <v-flex xs3 offset-xs2>
                <v-text-field v-model="builder.path" :counter="10" label="路径（包含字母，数字，连字符）" required></v-text-field>
              </v-flex>
              <v-flex xs3 offset-xs2>
                <v-select :items="builder.displaySelect" :value="builder.display" label="展示为" @change="onChangeDisplay"></v-select>
              </v-flex>
            </v-layout>
            <v-layout row>
              <v-flex xs12>
                <FormBuilder :form="builder" :options="builder.options" @change="onChangeSchema" ref="formBuilder"></FormBuilder>
              </v-flex>
            </v-layout>
            <v-layout row style="margin-top:40px;">
              <v-flex xs1 offset-xs10>
                <v-btn color="error" large @click="onBuilderSubmit">重置</v-btn>
              </v-flex>
              <v-flex xs1>
                <v-btn color="success" large @click="onBuilderSubmit">确认</v-btn>
              </v-flex>
            </v-layout>
          </v-card>
        </v-flex>

        <v-dialog v-model="warningDialog" width="500">
          <v-card>
            <v-card-title class="headline warning lighten-2" primary-title>警告</v-card-title>
            <v-card-text>请确认表单标题、名称、路径等字段填写完整。</v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue-grey lighten-1" flat @click="warningDialog = false">确认</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
  // 从vue-formio库中引入FormBuilder组件
  import { FormBuilder } from 'vue-formio'
  // 引入i18n配置文件
  import i18n from './formio-i18n'
  import { generateForm, editForm, getForm } from '@/api/formio'

  export default {
    name: 'formBuilder',
    data() {
      return {
        // 表单ID
        checkedFormId: '',
        // 构建器
        builder: {
          title: '',
          name: '',
          path: '',
          display: 'form',
          displaySelect: [{ text: '单一式表单', value: 'form' }, { text: '向导式表单', value: 'wizard' }],
          components: [],
          options: {
            language: 'zh',
            i18n,
            builder: {
              advanced: false, //进阶组件不显示
              layout: false, //布局组件不显示
              data: false //数据组件不显示
            }
          }
        },
        warningDialog: false
      }
    },
    created() {
      this._initFormBuilder()
    },
    methods: {
      /* 改变表单的展现形式，单一式表单或者向导式表单 */
      onChangeDisplay(display) {
        this.builder.display = display
        this.builder.components = []

        this.$refs.formBuilder.initializeBuilder()
      },
      /* 表单对象的字段改变时触发事件 */
      onChangeSchema(schema) {
        if (schema.components && schema.components.length) {
          console.log(schema)
        }
      },
      /* 提交构建器构建的表单渲染对象 */
      onBuilderSubmit() {
        const keys = ['displaySelect', 'options']
        // 最终提交的Form对象。注意：this.formByFetch与this.builder的顺序不可颠倒
        const form = {
          ...this.formByFetch,
          ...this.builder
        }
        keys.forEach(item => {
          delete form[item]
        })

        if (!form.title || !form.name || !form.path || !form.components || !form.components.length) {
          this.warningDialog = true
          return false
        }

        if (this.checkedFormId) {
          /* 编辑表单 */
          editForm(form).then(result => {
            if (result._id) {
              this.$router.push({
                path: '/default/formio/list'
              })
            }
          })
        } else {
          /* 生成表单 */
          generateForm(form).then(result => {
            if (result._id) {
              this.$router.push({
                path: '/default/formio/list'
              })
            }
          })
        }
      },
      /* 初始化 Formio 构建器 */
      _initFormBuilder() {
        this.checkedFormId = this.$route.query.id

        if (!this.checkedFormId) {
          this.formByFetch = {}
          return false
        }

        /* 存在 form id 便请求数据  */
        getForm({ formId: this.checkedFormId }).then(result => {
          this.builder = {
            ...this.builder,
            title: result.title,
            name: result.name,
            path: result.path,
            display: result.display,
            components: result.components
          }

          this.formByFetch = result
        })
      }
    },
    components: {
      FormBuilder
    }
  }
</script>

<style lang="scss">
  .formio-dialog {
    .tab-content {
      margin-top: 10px;
    }
  }
</style>
```

![Formio-Builder](./imgs/formio-builder.png)

### 表单列表

```html
<template>
  <div class="form-list">
    <page-title-bar></page-title-bar>

    <v-container fluid grid-list-xl pt-0>
      <v-layout row wrap>
        <v-flex xs4 offset-xs4>
          <v-card>
            <v-toolbar color="light-blue" dark>
              <v-toolbar-title>表单列表</v-toolbar-title>
              <v-spacer></v-spacer>
              <v-btn icon>
                <v-icon>search</v-icon>
              </v-btn>
            </v-toolbar>

            <v-list>
              <v-list-tile v-for="(item, index) in formList" :key="item.name" @click="onNavigation(item._id)">
                <v-list-tile-content>
                  <v-list-tile-title>{{ item.title }}</v-list-tile-title>
                </v-list-tile-content>
                <v-list-tile-action class="form-item-action-wrapper">
                  <v-btn icon ripple title="编辑" @click.stop.prevent="onEditForm(item)">
                    <v-icon color="grey lighten-1">edit</v-icon>
                  </v-btn>
                  <v-btn icon ripple title="删除" @click.stop.prevent="onDelete(item, index)">
                    <v-icon color="grey lighten-1">delete</v-icon>
                  </v-btn>
                </v-list-tile-action>
              </v-list-tile>
            </v-list>
          </v-card>
        </v-flex>

        <!-- 是否删除表单确认弹窗 -->
        <v-dialog v-model="deleteFormDialog" max-width="400" transition>
          <v-card>
            <v-card-title class="headline">删除表单？</v-card-title>

            <v-card-text>此项操作不可逆，请谨慎操作</v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="darken-1" flat @click="deleteFormDialog = false">取消</v-btn>
              <v-btn color="warning darken-1" @click="onConfirm">确认</v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
  import { getFormList, deleteForm } from '@/api/formio'
  export default {
    data() {
      return {
        formList: [],
        deleteFormDialog: false
      }
    },
    created() {
      this._initFormList()
    },
    methods: {
      /* 跳转至表单数据提交页面 */
      onNavigation(formId) {
        this.$router.push({
          path: '/default/formio/create',
          query: {
            id: formId
          }
        })
      },
      /* 跳转至表单构建起页面进行表单字段等的编辑 */
      onEditForm(form) {
        this.$router.push({
          path: '/default/formio/builder',
          query: {
            id: form._id
          }
        })
      },
      /* 删除表单，调起确认对话框 */
      onDelete(form, index) {
        // 存储被选中的表单数据
        this.selectedForm = {
          ...form,
          selectedFormIndex: index
        }

        this.deleteFormDialog = true
      },
      /* 确认删除表单 */
      onConfirm() {
        deleteForm({ formId: this.selectedForm._id }).then(result => {
          if (result === 'OK') {
            this.formList.splice(this.selectedForm.selectedFormIndex, 1)
            this.selectedForm = {}
          } else {
            this.selectedForm = {}
          }

          this.deleteFormDialog = false
        })
      },
      /* 表单列表初始化 */
      _initFormList() {
        getFormList().then(result => {
          this.formList = result
        })
      }
    }
  }
</script>

<style lang="scss" scoped>
  .form-list {
    /deep/ .form-item-action-wrapper {
      flex-direction: row;
      align-items: center;
    }
  }
</style>
```

![Formio-List](./imgs/formio-list.png)

### 表单数据提交或者数据渲染的核心组件

```html
<template>
  <div class="formio-comp">
    <formio
      v-if="data.form.display"
      :src="data.src"
      :url="data.url"
      :form="data.form"
      :submission="data.submission"
      :options="data.options"
      :language="data.options.language"
      @submit="onFormSubmit"
    ></formio>
  </div>
</template>

<script>
  // 从vue-formio库中引入Form组件
  import { Form } from 'vue-formio'

  export default {
    name: 'formioComp',
    props: {
      data: {
        type: Object,
        required: true,
        default: function() {
          return {
            src: '',
            url: '',
            form: {
              // 展示方式，默认一定要置为空值
              display: '',
              // 表单字段
              components: []
            },
            submission: {
              // 表单携带的数据
              data: {}
            },
            options: {
              language: 'en',
              i18n: false,
              readOnly: false
            }
          }
        }
      }
    },
    methods: {
      // 提交表单数据后的回调函数
      onFormSubmit(submissionData) {
        this.$emit('submit', submissionData)
      }
    },
    components: {
      Formio: Form
    }
  }
</script>
```

### 表单 ID 缺失，提升用户体验

```html
<template>
  <div class="form-missing">
    <v-alert :value="true" type="error">暂时没有获取到表单ID，请从其他页面跳转</v-alert>
  </div>
</template>
```

![Formio-Missing](./imgs/formio-missing.png)

### 表单数据提交

```html
<template>
  <div class="formio-create">
    <page-title-bar></page-title-bar>
    <v-container fluid grid-list-xl pt-0>
      <formio-comp v-if="checkedFormId" :data="formCreate" @submit="onSubmitForm"></formio-comp>
      <form-missing v-else></form-missing>
    </v-container>
  </div>
</template>

<script>
  import FormioComp from './FormioComp'
  import FormMissing from './FormMissing'
  import i18n from './formio-i18n'

  import { getForm, submitForm } from '@/api/formio'
  export default {
    name: 'formCreate',
    data() {
      return {
        checkedFormId: '',
        formCreate: {
          src: '',
          url: '',
          form: {
            // 展示方式，默认一定要置为空值
            display: '',
            // 表单字段
            components: []
          },
          submission: {
            // 表单携带的数据
            data: {}
          },
          options: {
            language: 'zh',
            i18n: i18n,
            readOnly: false
          }
        }
      }
    },
    created() {
      this._initForm()
    },
    methods: {
      /* 提交表单数据及跳转 */
      onSubmitForm(data) {
        submitForm({ formId: this.checkedFormId, formData: data }).then(result => {
          if (result) {
            this.$router.push({
              path: '/default/formio/render',
              query: {
                id: this.checkedFormId
              }
            })
          }
        })
      },
      /* 初始化表单 */
      _initForm() {
        this.checkedFormId = this.$route.query.id

        if (!this.checkedFormId) {
          return false
        }

        getForm({ formId: this.checkedFormId }).then(result => {
          this.formCreate.form = {
            display: result.display,
            components: result.components
          }
        })
      }
    },
    components: {
      FormioComp,
      FormMissing
    }
  }
</script>
```

![Formio-Submit](./imgs/formio-submit.png)

### 表单数据渲染

```html
<template>
  <div class="formio-render">
    <page-title-bar></page-title-bar>
    <v-container fluid grid-list-xl pt-0>
      <formio-comp v-if="checkedFormId" :data="formRender"></formio-comp>
      <form-missing v-else></form-missing>
    </v-container>
  </div>
</template>

<script>
  import FormioComp from './FormioComp'
  import FormMissing from './FormMissing'
  import i18n from './formio-i18n'

  import { getForm, getFormSubmissionArray } from '@/api/formio'

  export default {
    name: 'formRender',
    data() {
      return {
        checkedFormId: '',
        formRender: {
          src: '',
          url: '',
          form: {
            // 展示方式，默认一定要置为空值
            display: '',
            // 表单字段
            components: []
          },
          submission: {
            // 表单携带的数据
            data: {}
          },
          options: {
            language: 'zh',
            i18n: i18n,
            readOnly: true
          }
        }
      }
    },
    created() {
      this._initForm()
    },
    methods: {
      /* 初始化表单 */
      async _initForm() {
        this.checkedFormId = this.$route.query.id

        if (!this.checkedFormId) {
          return false
        }
        /* 获取表单字段结构 */
        await getForm({ formId: this.checkedFormId }).then(result => {
          this.formRender.form = {
            display: result.display,
            components: result.components
          }
        })
        /* 获取表单提交的历史数据 */
        await getFormSubmissionArray({ formId: this.checkedFormId, limit: 25, skip: 0, sort: null }).then(result => {
          this.formRender.submission = {
            data: result[0].data
          }
        })
      }
    },
    components: {
      FormioComp,
      FormMissing
    }
  }
</script>

<style lang="scss" scoped>
  .formio-render {
    /deep/ button#submit {
      display: none;
    }
  }
</style>
```

![Formio-Data](./imgs/formio-data.png)
