<template>
  <div>
    <!--页面区域-->
    <div class="page-view">
      <div class="table-operations">
        <a-space>
          <a-button type="primary" @click="handleAdd">新增</a-button>
          <a-button @click="handleBatchDelete">批量删除</a-button>
          <a-input-search addon-before="名称" enter-button @search="onSearch" @change="onSearchChange" />
        </a-space>
      </div>
      <a-table
        size="middle"
        rowKey="id"
        :loading="data.loading"
        :columns="columns"
        :data-source="data.dataList"
        :scroll="{ x: 'max-content' }"
        :row-selection="rowSelection"
        :pagination="{
          size: 'default',
          current: data.page,
          pageSize: data.pageSize,
          onChange: (current) => (data.page = current),
          showSizeChanger: false,
          showTotal: (total) => `共${total}条数据`,
        }"
      >
        <template #bodyCell="{ text, record, index, column }">
          <template v-if="column.key === 'operation'">
            <span>
              <a @click="handleEdit(record)">编辑</a>
              <a-divider type="vertical" />
              <a-popconfirm title="确定删除?" ok-text="是" cancel-text="否" @confirm="confirmDelete(record)">
                <a href="#">删除</a>
              </a-popconfirm>
            </span>
          </template>
        </template>
      </a-table>
    </div>

    <!--弹窗区域-->
    <div>
      <a-modal
        :visible="modal.visile"
        :forceRender="true"
        :title="modal.title"
        width="880px"
        ok-text="确认"
        cancel-text="取消"
        @cancel="handleCancel"
        @ok="handleOk"
      >
        <div>
          <a-form ref="myform" :label-col="{ style: { width: '80px' } }" :model="modal.form" :rules="modal.rules">
            <a-row :gutter="24">
              <a-col span="24">
                <a-form-item label="影片名称" name="title">
                  <a-input placeholder="请输入" v-model:value="modal.form.title" />
                </a-form-item>
              </a-col>
              <a-col span="24">
                <a-form-item label="影片分类" name="classificationId">
                  <a-select
                    placeholder="请选择"
                    allowClear
                    :options="modal.cData"
                    :field-names="{ label: 'title', value: 'id' }"
                    v-model:value="modal.form.classificationId"
                  />
                </a-form-item>
              </a-col>
              <a-col span="24">
                <a-form-item label="封面图">
                  <a-upload-dragger
                    name="file"
                    accept="image/*"
                    :multiple="false"
                    :before-upload="beforeUpload"
                    v-model:file-list="fileList"
                  >
                    <p class="ant-upload-drag-icon">
                      <template v-if="modal.form.coverUrl">
                        <img :src="modal.form.coverUrl" style="width: 60px; height: 60px; object-fit: cover" />
                      </template>
                      <template v-else>
                        <file-image-outlined />
                      </template>
                    </p>
                    <p class="ant-upload-text"> 请选择要上传的图片 </p>
                  </a-upload-dragger>
                </a-form-item>
              </a-col>

              <a-col span="24">
                <a-form-item label="影片简介">
                  <a-textarea placeholder="请输入" v-model:value="modal.form.description" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="价格" name="price">
                  <a-input-number placeholder="请输入" :min="0" v-model:value="modal.form.price" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="导演">
                  <a-input placeholder="请输入" v-model:value="modal.form.daoyan" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="时长">
                  <a-input placeholder="请输入" v-model:value="modal.form.shichang" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="上映时间">
                  <a-input placeholder="请输入" v-model:value="modal.form.shangying" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="地区">
                  <a-input placeholder="请输入" v-model:value="modal.form.location" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="语言">
                  <a-select placeholder="请选择" allowClear v-model:value="modal.form.yuyan">
                    <a-select-option value="国语">国语</a-select-option>
                    <a-select-option value="粤语">粤语</a-select-option>
                    <a-select-option value="英语">英语</a-select-option>
                    <a-select-option value="日语">日语</a-select-option>
                    <a-select-option value="韩语">韩语</a-select-option>
                    <a-select-option value="法语">法语</a-select-option>
                    <a-select-option value="西班牙语">西班牙语</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="年份">
                  <a-input-number placeholder="请输入" :min="1900" :max="2030" v-model:value="modal.form.nianfen" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="演职人员">
                  <a-input placeholder="请输入" v-model:value="modal.form.renyuan" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="放映时间">
                  <a-input placeholder="请输入" v-model:value="modal.form.fangying" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="放映厅">
                  <a-input placeholder="请输入" v-model:value="modal.form.fangyingting" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="余票数">
                  <a-input-number placeholder="请输入" :min="0" v-model:value="modal.form.repertory" style="width: 100%" />
                </a-form-item>
              </a-col>
              <a-col span="12">
                <a-form-item label="状态" name="status">
                  <a-select placeholder="请选择" allowClear v-model:value="modal.form.status">
                    <a-select-option key="0" value="0">上架</a-select-option>
                    <a-select-option key="1" value="1">下架</a-select-option>
                  </a-select>
                </a-form-item>
              </a-col>
            </a-row>
          </a-form>
        </div>
      </a-modal>
    </div>
  </div>
</template>

<script setup>
  import { message } from 'ant-design-vue';
  import { createApi, listApi, updateApi, deleteApi } from '/@/api/thing';
  import { listApi as listClassificationApi } from '/@/api/classification';
  import { BASE_URL } from '/@/store/constants';
  import { FileImageOutlined } from '@ant-design/icons-vue';




  const columns = reactive([
    {
      title: '序号',
      dataIndex: 'index',
      key: 'index',
      width: 60,
    },
    {
      title: '名称',
      dataIndex: 'title',
      key: 'title',
    },
    {
      title: '价格',
      dataIndex: 'price',
      key: 'price',
    },
    {
      title: '语言',
      dataIndex: 'yuyan',
      key: 'yuyan',
    },
    {
      title: '地区',
      dataIndex: 'location',
      key: 'location',
    },
    {
      title: '年份',
      dataIndex: 'nianfen',
      key: 'nianfen',
    },
    {
      title: '余票数',
      dataIndex: 'repertory',
      key: 'repertory',
    },
    {
      title: '简介',
      dataIndex: 'description',
      key: 'description',
      customRender: ({ text, record, index, column }) => (text ? text.substring(0, 10) + '...' : '--'),
    },
    {
      title: '状态',
      dataIndex: 'status',
      key: 'status',
      customRender: ({ text, record, index, column }) => (text === '0' ? '上架' : '下架'),
    },
    {
      title: '操作',
      dataIndex: 'action',
      key: 'operation',
      align: 'center',
      fixed: 'right',
      width: 140,
    },
  ]);

  const beforeUpload = (file) => {
    // 改文件名
    const fileName = new Date().getTime().toString() + '.' + file.type.substring(6);
    const copyFile = new File([file], fileName);
    console.log(copyFile);
    modal.form.imageFile = copyFile;
    return false;
  };

  // 文件列表
  const fileList = ref([]);

  // 页面数据
  const data = reactive({
    dataList: [],
    loading: false,
    keyword: '',
    selectedRowKeys: [],
    pageSize: 10,
    page: 1,
  });

  // 弹窗数据源
  const modal = reactive({
    visile: false,
    editFlag: false,
    title: '',
    cData: [],
    locationData: ['北京', '上海', '深圳', '济南', '广州', '武汉'],
    form: {
      id: undefined,
      title: undefined,
      classificationId: undefined,
      price: undefined,
      daoyan: undefined,
      shichang: undefined,
      shangying: undefined,
      yuyan: undefined,
      nianfen: undefined,
      fangying: undefined,
      fangyingting: undefined,
      renyuan: undefined,
      repertory: undefined,
      location: undefined,
      status: undefined,
      cover: undefined,
      coverUrl: undefined,
      imageFile: undefined,
    },
    rules: {
      title: [{ required: true, message: '请输入名称', trigger: 'change' }],
      classificationId: [{ required: true, message: '请选择分类', trigger: 'change' }],
      price: [{ required: true, message: '请输入定价', trigger: 'change' }],
      status: [{ required: true, message: '请选择状态', trigger: 'change' }],
    },
  });

  const myform = ref();

  onMounted(() => {
    getDataList();
    getCDataList();
  });

  const getDataList = () => {
    data.loading = true;
    listApi({
      keyword: data.keyword,
    })
      .then((res) => {
        data.loading = false;
        console.log(res);
        res.data.forEach((item, index) => {
          item.index = index + 1;
        });
        data.dataList = res.data;
      })
      .catch((err) => {
        data.loading = false;
        console.log(err);
      });
  };

  const getCDataList = () => {
    listClassificationApi({}).then((res) => {
      modal.cData = res.data;
    });
  };


  const onSearchChange = (e) => {
    data.keyword = e.target.value;
    console.log(data.keyword);
  };

  const onSearch = () => {
    getDataList();
  };

  const rowSelection = ref({
    onChange: (selectedRowKeys, selectedRows) => {
      console.log(`selectedRowKeys: ${selectedRowKeys}`, 'selectedRows: ', selectedRows);
      data.selectedRowKeys = selectedRowKeys;
    },
  });

  const handleAdd = () => {
    resetModal();
    modal.visile = true;
    modal.editFlag = false;
    modal.title = '新增';
    // 重置
    for (const key in modal.form) {
      modal.form[key] = undefined;
    }
    modal.form.cover = undefined;
  };
  const handleEdit = (record) => {
    resetModal();
    modal.visile = true;
    modal.editFlag = true;
    modal.title = '编辑';
    // 重置
    for (const key in modal.form) {
      modal.form[key] = undefined;
    }
    for (const key in record) {
      if (record[key]) {
        modal.form[key] = record[key];
      }
    }
    if (modal.form.cover) {
      modal.form.coverUrl = BASE_URL + '/api/staticfiles/image/' + modal.form.cover;
      modal.form.cover = undefined;
    }
  };

  const confirmDelete = (record) => {
    console.log('delete', record);
    deleteApi({ ids: record.id })
      .then((res) => {
        getDataList();
      })
      .catch((err) => {
        message.error(err.msg || '操作失败');
      });
  };

  const handleBatchDelete = () => {
    console.log(data.selectedRowKeys);
    if (data.selectedRowKeys.length <= 0) {
      console.log('hello');
      message.warn('请勾选删除项');
      return;
    }
    deleteApi({ ids: data.selectedRowKeys.join(',') })
      .then((res) => {
        message.success('删除成功');
        data.selectedRowKeys = [];
        getDataList();
      })
      .catch((err) => {
        message.error(err.msg || '操作失败');
      });
  };

  const handleOk = () => {
    myform.value
      ?.validate()
      .then(() => {
        const formData = new FormData();
        if (modal.editFlag) {
          formData.append('id', modal.form.id);
        }
        formData.append('title', modal.form.title);
        if (modal.form.classificationId) {
          formData.append('classificationId', modal.form.classificationId);
        }
        if (modal.form.imageFile) {
          formData.append('imageFile', modal.form.imageFile);
        }
        formData.append('price', modal.form.price || '');
        formData.append('description', modal.form.description || '');
        formData.append('daoyan', modal.form.daoyan || '');
        formData.append('shichang', modal.form.shichang || '');
        formData.append('shangying', modal.form.shangying || '');
        formData.append('yuyan', modal.form.yuyan || '');
        formData.append('nianfen', modal.form.nianfen || '');
        formData.append('fangying', modal.form.fangying || '');
        formData.append('fangyingting', modal.form.fangyingting || '');
        formData.append('renyuan', modal.form.renyuan || '');
        formData.append('repertory', modal.form.repertory || '');
        if (modal.form.location) {
          formData.append('location', modal.form.location);
        }
        if (modal.form.status) {
          formData.append('status', modal.form.status);
        }
        if (modal.editFlag) {
          updateApi(
            formData,
          )
            .then((res) => {
              hideModal();
              getDataList();
            })
            .catch((err) => {
              console.log(err);
              message.error(err.msg || '操作失败');
            });
        } else {
          createApi(formData)
            .then((res) => {
              hideModal();
              getDataList();
            })
            .catch((err) => {
              console.log(err);
              message.error(err.msg || '操作失败');
            });
        }
      })
      .catch((err) => {
        console.log('不能为空');
      });
  };

  const handleCancel = () => {
    hideModal();
  };

  // 恢复表单初始状态
  const resetModal = () => {
    myform.value?.resetFields();
    fileList.value = [];
  };

  // 关闭弹窗
  const hideModal = () => {
    modal.visile = false;
  };
</script>

<style scoped lang="less">
  .page-view {
    min-height: 100%;
    background: #fff;
    padding: 24px;
    display: flex;
    flex-direction: column;
  }

  .table-operations {
    margin-bottom: 16px;
    text-align: right;
  }

  .table-operations > button {
    margin-right: 8px;
  }
</style>
