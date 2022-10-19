<template>
  <div class="about">
    <el-button @click="submit">提交</el-button>
    <el-form :model="database" ref="formRef">
      <jsx-table
        :columns="columns"
        :order="true"
        select-type="single"
        :data="database.list"
        :pagination="{
          total: 100,
          pageSize: 10,
          currentPage: 1,
          events: {
            'size-change': sizeChange,
            'current-change': currentChange
          }
        }"
        @row-click="rowClick"
        @single-selection-change="singleChange"
      />
    </el-form>
  </div>
</template>
<script lang="jsx">
import jsxTable from '@/components/jsx-table.vue';
export default {
  components: {
    jsxTable
  },
  data() {
    return {
      columns: [
        {
          label: '姓名',
          prop: 'name',
          align: 'center'
        },
        {
          label: '年龄',
          prop: 'age',
          align: 'center'
        },
        {
          label: '爱好',
          align: 'center',
          // prop: 'hobby',
          children: [
            {
              label: '球类',
              align: 'center',
              children: [
                {
                  label: '足球',
                  // prop: 'football',
                  align: 'center',
                  render: ({row}) => {
                    return <div>{['否', '是'][row.football]}</div>
                  }
                },
                {
                  label: '篮球',
                  prop: 'basketball',
                  align: 'center',
                }
              ]
            },
            {
              label: '唱歌',
              prop: 'sing',
              align: 'center'
            }
          ]
        },
        {
          label: '性别',
          prop: 'sex',
          align: 'center',
          render: ({ text }) => {
            return <div>{ text }hhh</div>
          }
        },
        {
          label: '操作',
          align: 'center',
          render: () => {
            return (
              <div style="color: #03a9f4">
                <span><div style="color: #03a9f4">删除</div></span>  
              </div>
            )
          }
        }
      ],
      database: {
        list: [
          {
            name: '123',
            age: 16,
            sex: '男',
            football: 1,
            basketball: '是',
            sing: '否',
            company: [
              {
                companyId: '1',
                companyName: '百度',
                companyScore: 99,
                companyPass: 1
              },
              {
                companyId: '2',
                companyName: '腾讯',
                companyScore: 98,
                companyPass: 0
              }
            ]
          },
          {
            // name: '2323',
            age: 18,
            sex: '女',
            football: 0,
            basketball: '是',
            sing: '是',
            company: [
              {
                companyId: '1',
                companyName: '百度',
                companyScore: 97,
                companyPass: 0
              },
              {
                companyId: '2',
                companyName: '腾讯',
                companyScore: 99,
                companyPass: 1
              }
            ]
          }
        ] 
      }
    }
  },
  created() {
    this.getColumns()
  },
  methods: {
    getColumns() {
      const company = this.database.list[0].company
      company.forEach((i, idx) => {
        const { companyName } = i
        this.columns.push({
          label: companyName,
          align: 'center',
          children: [
            {
              label: '分数',
              align: 'center',
              render: ({ row, index }) => {
                const rules = [
                  { 
                    validator: (rule, value, cb) => {
                      console.log(value, '校验？？？', rule)
                      if (!value) {
                        return cb(new Error('分数不能为空'));
                      }
                      cb()
                    },
                    trigger: 'change'
                  },
                ]
                const str = `list[${index}].company[${idx}].companyScore`
                return (
                  <el-form-item rules={rules} prop={str}>
                    <el-input v-model={row.company[idx].companyScore} />
                  </el-form-item>
                )
              }
            },
            {
              label: '是否合格',
              align: 'center',
              render: ({ row }) => {
                return ['不合格', '合格'][row.company[idx].companyPass]
              }
            }
          ]
        })
      })
    },
    rowClick(row, column, event) {
      console.log('点击---', row, column, event)
    },
    singleChange(row) {
      console.log(row, '单选选中')
    },
    sizeChange(size) {
      console.log(size, 'size改变')
    },
    currentChange(currrent) {
      console.log(currrent, 'current改变')
    },
    submit() {
      console.log(this.database.list, '保存数据')
      this.$refs.formRef.validate((valid) => {
        if (valid) {
          alert('submit!');
        } else {
          console.log('error submit!!');
          return false;
        }
      })
    }
  }
}
</script>
<style lang="less" scoped>
.about {
  height: 300px;
}
</style>
