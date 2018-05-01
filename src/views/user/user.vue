<template>
  <div class="user_index">
    <Row>
      <Col span="3">
        <Input icon="ios-person-outline" placeholder="用户名" style="width: 90%"></Input>
      </Col>
      <Col span="3">
        <Input icon="ios-telephone-outline" placeholder="昵称" style="width: 90%"></Input>
      </Col>
      <Col span="3">
        <Select style="width: 90%" placeholder="状态">
          <Option value="">全部</Option>
          <Option value="0">地州1</Option>
          <Option value="1">地州2</Option>
        </Select>
      </Col>
      <Col span="5">
        <Button type="primary" icon="ios-search" @click='search(1)'>查找</Button>
        <Button type="success" icon="plus" @click="showEdit(0)">添加</Button>
      </Col>
    </Row>
    <br>

    <Table :columns="columns" :data="datas" :loading="dataLoading"></Table>
    <br>

    <Page :total="100" show-total :current='filter.pageNo' @on-change="changePage"></Page>

    <Modal v-model="modalEdit" width="500">
      <p slot="header" style="color:#f60;text-align:center">
        <Icon type="information-circled"></Icon>
        <span>{{editTitle}}</span>
      </p>
      <div style="text-align:center">
        <Form ref="editInfo" :model="editInfo" :rules="rules">
          <FormItem prop="username">
            <Input v-model="editInfo.username" placeholder="用户名">
              <span slot="prepend">
                <Icon :size="16" type="person"></Icon>
              </span>
            </Input>
          </FormItem>
          <FormItem prop="nickname">
            <Input v-model="editInfo.nickname" placeholder="昵称">
              <span slot="prepend">
                <Icon :size="16" type="ios-body"></Icon>
              </span>
            </Input>
          </FormItem>
          <FormItem prop="phone">
            <Input v-model="editInfo.phone" placeholder="手机号">
              <span slot="prepend">
                <Icon :size="16" type="ios-telephone-outline"></Icon>
              </span>
            </Input>
          </FormItem>
          <FormItem prop="email">
            <Input v-model="editInfo.email" placeholder="邮箱">
              <span slot="prepend">
                <Icon :size="16" type="ios-email-outline"></Icon>
              </span>
            </Input>
          </FormItem>
        </Form>
      </div>
      <div slot="footer">
        <Button :type="editButtonType" size="large" long :loading="modal_loading" @click="submitEdit('editInfo')">{{editType}}</Button>
      </div>
    </Modal>
  </div>
</template>

<script>
  import {ERR_OK, pageSize} from "@/config/config"
  export default {
    computed: {
      editTitle() {
        return this.ifEdit ? '编辑用户' : '新增用户'
      },
      editType() {
        return this.ifEdit ? '确认更改' : '确认添加'
      },
      editButtonType() {
        return this.ifEdit ? 'primary' : 'success'
      }
    },
    data() {
      const validatePhone = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入手机号码'));
        } else if (!/[1]\d{10}$/.test(value)) {
          callback(new Error('手机号码有误'));
        } else {
          callback();
        }
      }
      const validateEmail = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入邮箱地址'));
        } else if (!/^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/.test(value)) {
          callback(new Error('邮箱地址有误'));
        } else {
          callback();
        }
      }
      return {
        // 数据加载状态
        dataLoading: false,
        // 编辑框显示状态
        modalEdit: false,
        // 编辑/添加状态
        ifEdit: false,
        // 操作提交状态
        modal_loading: false,
        // 筛选条件
        filter: {
          pageNo: 1,
          pageSize: pageSize,
        },
        // 表头数据
        columns: [
          {
            title: 'username',
            key: 'username'
          },{
            title: '昵称',
            key: 'nickname'
          },{
            title: '手机号',
            key: 'phone'
          },{
            title: '邮箱',
            key: 'email'
          },{
            title: '状态',
            key: 'status',
            render: (h, params) => {
              return h('Tag', {
                props: {
                  color: params.row.status == 0 ? 'green' : params.row.status == 1 ? 'yellow' : 'red'
                }
              }, params.row.status == 0 ? '正常' : params.row.status == 1 ? '不正常' : '已删除')
            }
          },{
            title: '操作',
            key: 'deel',
            render: (h, parames) => {
              return h('Button', {
                props: {
                  type: 'primary',
                  icon: 'edit'
                },
                on: {
                  click: () => {
                    this.showEdit(1, parames.row);
                  }
                }
              })
            }
          }
        ],
        // 表数据
        datas: [
          {
            id: 1,
            username: 'leee',
            nickname: '小李',
            phone: '13438380098',
            email: '876733271@qq.com',
            status: 0,
          },{
            id: 2,
            username: 'zzz',
            nickname: '张大爷',
            phone: '13333333333',
            email: '1245t@qq.com',
            status: 1,
          },{
            id: 3,
            username: 'kkk',
            nickname: 'k子',
            phone: '14766666666',
            email: '77777@qq.com',
            status: 2,
          }
        ],
        // 编辑数据
        editInfo: {
          username: '',
          nickname: '',
          phone: '',
          email: ''
        },
        // 校验数据
        rules: {
          username: [
            { required: true, message: '用户名不能为空', trigger: 'blur' }
          ],
          nickname: [
            { required: true, message: '用户昵称不能为空', trigger: 'blur' }
          ],
          phone: [
            { validator: validatePhone, trigger: 'blur' }
          ],
          email: [
            {validator: validateEmail, trigger: 'blur'}
          ]
        }
      }
    },
    methods: {
      // 查询操作
      search(no) {
        this.filter.pageNo = no
        this.dataLoading = true
        setTimeout( () => this.dataLoading = false, 3000)
      },
      // 显示编辑框
      showEdit(status, item) {
        this.modalEdit = true
        status == 0 ? this.ifEdit = false : this.ifEdit = true
        this.$refs['editInfo'].resetFields()
        if(item) {
          this.editInfo = {...item}
        }else {
          this.editInfo = {}
        }
      },
      // 提交更改
      submitEdit(name) {
        this.$refs[name].validate((valid) => {
          if (valid) {
            this.modal_loading = true;
            setTimeout( () => {
              if(!this.ifEdit) {
                this.datas.push(this.editInfo);
              }else {
                let _index = this.datas.findIndex(item => {
                  return item.id === this.editInfo.id
                })
                this.datas.splice(_index, 1, this.editInfo) 
              }
              this.modal_loading = false
              this.modalEdit = false
              this.$Message.success('操作成功！')
              this.search(1)
            }, 3000)
          } else {
            this.$Message.error('信息格式错误!');
          }
        })
        
      },
      // 换页
      changePage(no) {
        this.filter.pageNo = no;
        this.search(no)
      } 
    }
  }
</script>

<style lang="less">
  
</style>