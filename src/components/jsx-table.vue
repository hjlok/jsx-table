<script lang="jsx">
import { Fragment } from 'fragment-for-vue'
// 自定义内容的组件
const exSlot = {
  name: 'ex-slot',
  components: {
    Fragment
  },
  props: {
    render: {
      type: Function,
      default: () => {},
    },
  },
  render() {
    return <div>{this.$props.render(this.$attrs)}</div> // 此处div使用fragment有bug
  },
}

// 递归列组件
const loopColumns = {
  name: 'loop-columns',
  components: {
    exSlot,
    Fragment
  },
  props: {
    list: {
      type: Array,
      default: []
    }
  },
  render() {
    return (
      <Fragment>
        {
          this.$props.list.map(th => {
            if (th.children) {
              return (
                <el-table-column key={th.label} props={th}>
                  <loop-columns list={th.children} />
                </el-table-column>
              )
            }
            return (
              <el-table-column
                key={th.label}
                props={th}
                scopedSlots={{
                  default: scope => {
                    return (
                      <Fragment>
                        {
                          th.render
                            ? <ex-slot render={th.render} row={scope.row} index={scope.$index} text={scope.row[th.prop]} column={th}></ex-slot>
                            : scope.row[th.prop]
                        }
                      </Fragment>
                    )
                  },
                }}
              />
            )
          })
        }
      </Fragment>
    )
  }
}
// 主组件
export default {
  name: 'jsx-table',
  components: {
    exSlot,
    loopColumns,
    Fragment
  },
  data() {
    return {
      selectIndex: -1
    }
  },
  methods: {
    radioChange(e, scope) {
      e.stopPropagation()
      this.selectIndex = scope.$index
      this.$emit('single-selection-change', scope.row)
    }
  },
  render() {
    const {
      columns,
      order,
      'select-type': selectType,
      pagination,
      pagination: {
        events: pageEvents,
        ...pageProps
      },
      ...rest
    } = this.$attrs

    const singleRadio = scope => <div class={{'radio-box': true, 'radio-box-active' : this.selectIndex === scope.$index }}  onClick={e => this.radioChange(e, scope)} />
    return (
      <Fragment>
        <el-table props={rest} on={this.$listeners}>
          { selectType === 'multiple' && <el-table-column type="selection" width="50" /> }
          { selectType === 'single' && <el-table-column width="40" scopedSlots={{ default: singleRadio }} /> }
          { order && <el-table-column type="index" label="序号" width="50" /> }
          <loop-columns list={columns} />
        </el-table>
        {pagination && <el-pagination props={pageProps} on={pageEvents} />}
      </Fragment>
    )
  },
}
</script>
<style lang="less" scoped>

.radio-box {
  border: 1px solid #dcdfe6;
  width: 14px;
  height: 14px;
  background-color: #fff;
  position: relative;
  cursor: pointer;
  display: inline-block;
  box-sizing: border-box;
  border-radius: 3px;
  transform: translateY(2px);
}
.radio-box-active {
  background: #C3002F;
  &::after {
    box-sizing: content-box;
    content: "";
    border: 1px solid #fff;
    border-left: 0;
    border-top: 0;
    height: 7px;
    left: 4px;
    position: absolute;
    top: 1px;
    width: 3px;
    transition: transform .15s ease-in .05s;
    transform-origin: center;
    transform: rotate(45deg) scaleY(1) !important;
    border-radius: 0;
    background-color: transparent;
  }
}
</style>
  