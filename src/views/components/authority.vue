<template>
<div class="au-container" v-clickoutside="handleClose">
  <!-- <el-input v-model="selectedText" @focus="visible = true" auto-complete="off" readonly></el-input> -->
  <div @click="visible = true" class="select-style">{{selectedText}}</div>
  <div class="el-dropdown-menu" v-show="visible">
    <el-tabs v-model="activeName" type="card">
      <el-tab-pane label="大区" name="first">
        <el-checkbox-group v-model="selected.partitions">
          <el-checkbox-button v-for="item in partitions" :label="item.PartitionName" :value="item.Id" :key="item.Id">{{item.PartitionName}}</el-checkbox-button>
        </el-checkbox-group>
      </el-tab-pane>
      <el-tab-pane label="省份" name="second">
        <div>
          <el-input placeholder="查询省份" icon="search" v-model="pstr" @change="qProvince"></el-input>
        </div>
        <el-checkbox-group v-model="selected.provinces" class="pnlist">
          <el-checkbox-button v-show="!item.hidden" v-for="item in provinces" :label="item.Name" :value="item.Code" :key="item.Code">{{item.Name}}</el-checkbox-button>
        </el-checkbox-group>
      </el-tab-pane>
      <el-tab-pane label="城市" name="third">
        <div>
          <el-input placeholder="查询城市" icon="search" v-model="cstr" @change="qCity"></el-input>
        </div>
        <el-checkbox-group v-model="selected.cities">
          <el-checkbox-button v-show="!item.hidden" v-for="item in cities" :label="item.Name" :value="item.Code" :key="item.Code">{{item.Name}}</el-checkbox-button>
        </el-checkbox-group>
      </el-tab-pane>
      <el-tab-pane label="代理商" name="fourth">
        <div>
          <el-input placeholder="查询代理商" icon="search" v-model="astr" @change="qAgent(astr)"></el-input>
        </div>
        <el-table :data="agents" border tooltip-effect="dark" style="width: 100%" max-height="200" ref="multipleTable" @selection-change="handleSelectionChange">
          <el-table-column type="selection" width="55"></el-table-column>
          <el-table-column prop="ChannelName1" label="渠道名称(一级)"></el-table-column>
          <el-table-column prop="ChannelName2" label="渠道名称(二级)"></el-table-column>
        </el-table>
      </el-tab-pane>
    </el-tabs>
  </div>
</div>
</template>

<script>
import {
  dataauthorityinfos
} from '../../api/api'
import Clickoutside from 'element-ui/src/utils/clickoutside'
export default {
  data() {
    return {
      visible: false,
      activeName: 'first',
      pstr: '',
      cstr: '',
      astr: '',
      partitions: [],
      provinces: [],
      cities: [],
      agents: [],
      selectedText: '',
      selected: {
        partitions: [],
        provinces: [],
        cities: [],
        agents: []
      },
      modelValue: {
        partitions: [],
        provinces: [],
        cities: [],
        agents: []
      }
    }
  },
  props: ['value'],
  directives: {
    Clickoutside
  },
  created() {
    this.getdataauthorityinfos()
  },

  methods: {
    getdataauthorityinfos() {
      dataauthorityinfos().then((res) => {
        this.partitions = res.data[0]
        this.provinces = res.data[1]
        this.cities = res.data[2]
        this.agents = res.data[3]
        setTimeout(() => {
          this.setSelected()
        }, 500)
      })
    },
    setSelected() {
      if (this.value.agents) {
        // console.log('agentIds:', this.value.agents)
        const agentIds = this.value.agents.replace(/'/g, '').split(',')
        this.agents.forEach(item => {
          // console.log('agent:', item.ChannelId, agentIds.indexOf(item.ChannelId) > -1)
          if (agentIds.indexOf(item.ChannelId) > -1) {
            this.$refs.multipleTable.toggleRowSelection(item)
          }
        })
      }
      if (this.value.partitions) {
        const partitionIds = this.value.partitions.replace(/'/g, '').split(',')
        this.selected.partitions = this.partitions.filter(item => {
          return partitionIds.indexOf(item.Id + '') > -1
        }).map(item => {
          return item.PartitionName
        })
      }
      if (this.value.provinces) {
        const provinceIds = this.value.provinces.replace(/'/g, '').split(',')
        this.selected.provinces = this.provinces.filter(item => {
          return provinceIds.indexOf(item.Code) > -1
        }).map(item => {
          return item.Name
        })
      }
      if (this.value.cities) {
        const cityIds = this.value.cities.replace(/'/g, '').split(',')
        this.selected.cities = this.cities.filter(item => {
          return cityIds.indexOf(item.Code) > -1
        }).map(item => {
          return item.Name
        })
      }
    },
    setText() {
      let selectedTextArr = this.selected.partitions.concat(this.selected.provinces).concat(this.selected.cities)
      selectedTextArr = selectedTextArr.concat(this.selected.agents.map(item => {
        return item.ChannelName2 || item.ChannelName1
      }))
      this.selectedText = selectedTextArr.join(',')
      const vmap = ['partitions', 'provinces', 'cities']
      setTimeout(() => {
        // console.log(this.$el.querySelectorAll('.el-tab-pane'))
        this.$el.querySelectorAll('.el-tab-pane').forEach((el, index) => {
          if (index > 2) return
          this.modelValue[vmap[index]] = []
          // console.log(el.querySelectorAll('input:checked'))
          el.querySelectorAll('input:checked').forEach((input) => {
            // console.log(input, 'input')
            this.modelValue[vmap[index]].push("'" + input.parentNode.__vue__.value + "'")
          })
        })
      }, 0)
      this.modelValue.agents = this.selected.agents.map(item => {
        return ("'" + item.ChannelId + "'")
      })
      // console.log(this.modelValue, 'this.modelValue')
      this.$emit('input', this.modelValue)
    },
    qProvince() {
      var str = this.pstr.replace(/\s/g, '')
      this.provinces.forEach(item => {
        item.Name = item.Name.replace(/\s/g, '')
        if (item.Name.indexOf(str) === -1) {
          item.hidden = true
        } else {
          item.hidden = false
        }
      })
    },
    qCity() {
      var str = this.cstr.replace(/\s/g, '')
      this.cities.forEach(item => {
        item.Name = item.Name.replace(/\s/g, '')
        if (item.Name.indexOf(str) === -1) {
          item.hidden = true
        } else {
          item.hidden = false
        }
      })
    },
    qAgent(str) {
      this.$el.querySelectorAll('tr').forEach(function(tr) {
        if (tr.innerText.indexOf(str) === -1) {
          tr.style.display = 'none'
        } else {
          tr.style.display = ''
        }
      })
    },
    handleSelectionChange(val) {
      this.selected.agents = val
      this.setText()
    },
    handleClose: function() {
      this.visible = false
    }
  },
  watch: {
    value(val) {
      // console.log('value', val)
    },
    'selected.partitions': function(val) {
      this.setText()
    },
    'selected.provinces': function(val) {
      this.setText()
    },
    'selected.cities': function(val) {
      this.setText()
    }
  }
}
</script>

<style>
.au-container {
  position: relative;
}

.au-container .el-input input {
  text-overflow: ellipsis;
}

.au-container .el-dropdown-menu {
  width: 500px;
  overflow: auto;
  top: auto !important;
}
.demo-ruleForm .el-form-item__content{
  height: auto !important;
}
.au-container .el-tabs {
  padding-bottom: 0;
}

.au-container .el-checkbox-button {
  margin: 6px;
  background: #bfcbd9;
}

.au-container .el-checkbox-button__inner {
  background: #bfcbd9;
  border: none;
}

.au-container .el-tab-pane {
  padding: 12px;
}

input[type="text"] {
  cursor: pointer;
}

.au-container .el-dropdown-menu .el-tabs__content {
  height: 200px;
}

.au-container .select-style {
  width: 100%;
  min-height: 36px;
  border: 1px solid #8391a5;
  border-radius: 4px;
  padding: 3px 10px;
}
</style>
