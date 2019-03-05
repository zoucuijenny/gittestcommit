<template>
  <div class="seeSystemApplication">
    <div class="popupRightHeader">
      <span>{{popupRightTitle}}</span>
      <el-button type="text" icon="el-icon-close" class="popupRightTimes" @click="closePopupWindow"></el-button>
    </div>
    <div class="popupContentBox">
      <div class="popupContent  seeApplicationPop">
        <div class="row clear">
          <div class="title fl"> 应用logo</div>
          <div class="rightContent fl">
            <img :src="afterContactData.logoSrc" class="seeLogoImg" alt="logo图标">
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">应用名称</div>
          <div class="rightContent fl">
            {{afterContactData.title }}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">创建人</div>
          <div class="rightContent fl">
            {{afterContactData.founder }}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">应用来源</div>
          <div class="rightContent fl">
            {{afterContactData.origin}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">应用类别</div>
          <div class="rightContent fl">
            {{afterContactData.typeName}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">应用链接</div>
          <div class="rightContent fl">
            {{afterContactData.path}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">设为导航</div>
          <div class="rightContent fl">
            {{afterContactData.isShow}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">发布状态</div>
          <div class="rightContent fl">
            {{afterContactData.isPublic}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl ">使用状态</div>
          <div class="rightContent fl">
            <span :class="afterContactData.status === '使用中'? 'inUse': 'deactivated'">{{afterContactData.status }}</span>
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">开发日期</div>
          <div class="rightContent fl">
            {{afterContactData.createDate}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">更新日期</div>
          <div class="rightContent fl">
            {{afterContactData.updataData}}
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">应用描述</div>
          <div class="rightContent fl">
            <div class="applicationDescribe">
              {{afterContactData.describe}}
            </div>
          </div>
        </div>
        <div class="row clear">
          <div class="title fl">可见范围</div>
          <div class="bottomContent">
            <div class="visibleList">
              <el-card class="box-card" shadow="hover">
                <div slot="header" class="clearfix cardTitle">
                  <span></span>
                  <el-button style="float: right; padding: 3px 0" type="text" @click="selectVisibleList">编辑可见范围
                  </el-button>
                </div>
                <div v-for="item in visibleCheckedGroup" :key="item.id" class="text item">
                  <span class="itemKey">{{item.name}}：</span>
                  <span class="itemVal">
                    <span v-for="(itemB, index) in item.checked" :key="'check'+index">{{itemB}}</span>
                  </span>
                </div>
              </el-card>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="popupRightBottom">
      <el-row type="flex" justify="end">
        <el-button class="el-button el-button--primary el-button--small" @click="closePopupWindow">确定</el-button>
        <el-button class="el-button el-button--small" @click="closePopupWindow">取消</el-button>
      </el-row>
    </div>
  </div>
</template>

<script>
  import bus from '@/bus'
  import {createNamespacedHelpers} from 'vuex'

  const {mapState, mapMutations, mapActions} = createNamespacedHelpers('cloudService/')

  export default {
    data() {
      return {
        popupRightTitle: '查看应用',
        showPopupWindow: false,
        beforeContactData: {},
        visibleGroup: [
          {
            name: "所有员工",
            id: 1,
            checked: []
          }, {
            name: "员工",
            id: 2,
            checked: []
          }, {
            name: "部门",
            id: 3,
            checked: []
          }, {
            name: "所有关注用户",
            id: 4,
            checked: []
          }, {
            name: "关注用户",
            id: 5,
            checked: []
          }, {
            name: "关注用户分类",
            id: 6,
            checked: []
          }, {
            name: "所有外部联系人",
            id: 7,
            checked: []
          }, {
            name: "外部联系人",
            id: 8,
            checked: []
          }, {
            name: "外部联系人分类",
            id: 9,
            checked: []
          }, {
            name: "角色",
            id: 10,
            checked: []
          }
        ]
      }
    },
    computed: {
      ...mapState([
        'isFrom',
        'myAppVisibleCheckedList'
      ]),
      afterContactData() {
        let temp = {}
        temp.logoSrc = this.beforeContactData.logo || ''
        temp.title = this.beforeContactData.name || '未知'
        temp.path = this.beforeContactData.path || '无'
        temp.origin = this.beforeContactData.origin
        temp.founder = this.beforeContactData.creator || '未知'
        temp.typeName = this.beforeContactData.typeName || '未知'
        temp.describe = this.beforeContactData.describe || '未知'
        temp.isPublic = this.beforeContactData.publish === 1 ? '已发布' : (this.beforeContactData.publish === 0 ? '未发布' : '未知')
        temp.status = this.beforeContactData.enable === 1 ? '使用中' : (this.beforeContactData.enable === 0 ? '已停用' : '未知')
        temp.isShow = this.beforeContactData.is_show === 1 ? '是':'否'
        if (this.beforeContactData.createDate) {
          temp.createDate = this.beforeContactData.createDate
        } else {
          temp.createDate = '暂无'
        }
        if (this.beforeContactData.updataData) {
          temp.updataData = this.beforeContactData.updataData
        } else {
          temp.updataData = '暂无'
        }
        return temp
      },
      visibleCheckedGroup() {
        let temp = [...this.visibleGroup]
        for (let itemC of temp) {
          itemC.checked = []
        }
        for (let item of this.myAppVisibleCheckedList) {
          if (item['all_pub'] !== null) {
            temp[0].checked.push('是')
          }
          if (item['pub_id'] !== null) {
            temp[1].checked.push(item['pubName'])
          }
          if (item['dept_id'] !== null) {
            temp[2].checked.push(item['deptMame'])
          }
          if (item['all_concern'] !== null) {
            temp[3].checked.push('是')
          }
          if (item['concern_id'] !== null) {
            temp[4].checked.push(item['concernName'])
          }
          if (item['type_id'] !== null) {
            temp[5].checked.push(item['typeName'])
          }
          if (item['all_outer'] !== null) {
            temp[6].checked.push('是')
          }
          if (item['outer_id'] !== null) {
            temp[7].checked.push(item['outerName'])
          }
          if (item['outer_type_id'] !== null) {
            temp[8].checked.push(item['outerTypeName'])
          }
          if (item['role_id'] !== null) {
            temp[9].checked.push(item['roleName'])
          }
        }
        return temp
      }
    },
    watch: {
      beforeContactData() {
        this.getVisibleList()
      }
    },
    methods: {
      ...mapMutations([
        'hidePop',
        'showPop'
      ]),
      ...mapActions([
        'getVisibleCheckedList'
      ]),
      closePopupWindow: function () {
        bus.$emit('closePopupWindowByBus')
      },
      selectVisibleList() {
        let me = this
        // let temp
        // if(this.formData.managerId != null){
        //   temp = {name: this.formData.managerName, id: this.formData.managerId}
        // }else {
        //   temp = {}
        // }
        // sessionStorage.setItem("changeDepPageData",this.formData&&JSON.stringify(this.formData)||'false')
        bus.$emit('showPopupWindowByBus', {isFrom: 'selectVisibleList'})
        this.showPop({isFrom: 'selectVisibleList', data: {sourceId: me.beforeContactData && me.beforeContactData.id}})
      },
      getVisibleList() {
        let me = this
        me.getVisibleCheckedList({
          data: {
            sourceId: me.beforeContactData && me.beforeContactData.id,
            sourceType: 7
          },
          error(error) {
            console.error('获取可见范围信息失败-->', error)
          }
        })
      }
    },
    created: function () {
      let me = this
      bus.$on('selfGetApplicationId', function (params) {
        me.beforeContactData = params.rowData
      })
    }
  }
</script>
<style lang="scss">
  @import "../../variables";
  @import "../../popSeeApplication";
</style>
