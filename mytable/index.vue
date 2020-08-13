<template>
  <div class="mytableClass">
    <div v-if="formList.length" class="tableForm">
      <el-form
        style="position: relative;"
        :inline="true"
        :model="formData"
        class="demo-form-inline"
      >
        <el-form-item :key="i" v-for="(item,i) in formList">
          <el-input
            v-if="item.type == 'input'"
            :placeholder="item.placeholder"
            v-model="item.value"
          ></el-input>
          <!-- <el-cascader
          v-if="item.type == 'regional'"
          :placeholder="item.placeholder"
          clearable
          v-model="item.value"
          :options="placeOptions"
          >
          </el-cascader>-->

          <el-select
            v-else-if="item.type == 'select'"
            v-model="item.value"
            clearable
            :placeholder="item.placeholder"
          >
            <el-option
              v-for="(item2,i2) in item.options"
              :key="i2"
              :label="item2.label"
              :value="item2.value"
            ></el-option>
          </el-select>
          <el-date-picker
            v-else-if="['year','month','date','dates', 'week','datetime','datetimerange', 'daterange','monthrange'].includes(item.type)  "
            clearable
            range-separator="-"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            v-model="item.value"
            value-format="yyyy-MM-dd"
            :type="item.type"
            :placeholder="item.placeholder"
          ></el-date-picker>
        </el-form-item>

        <el-form-item>
          <el-button
            type="primary"
            icon="el-icon-search"
            @keyup.enter="onSubmit"
            @click="onSubmit"
          >查询</el-button>
        </el-form-item>
      </el-form>
    </div>
    <el-table :data="tableData" stripe>
      <el-table-column
        v-for="(item,i) in columns"
        :key="i"
        :label="item.label"
        :width="item.width || ''"
      >
        <template slot-scope="scope">
          <!-- tool用于一行无法展示的情况，可设置最大长度 -->
          <el-tooltip
            v-if="item.tooltip && scope.row[item.key].length > item.tooltip"
            class="box"
            effect="dark"
            :content="scope.row[item.key] || '-'"
            placement="top"
          >
            <span>{{scope.row[item.key] ? scope.row[item.key].substr(0,item.tooltip) + '...': '-'}}</span>
          </el-tooltip>
          <span v-else>{{scope.row[item.key] || '-'}}</span>
        </template>
      </el-table-column>
      <slot></slot>
    </el-table>
    <div v-if="page" class="page_compent">
      <el-pagination
        background
        :layout="layout"
        @current-change="currentChange"
        @size-change="handleSizeChange"
        :page-size="pageSize"
        :page-sizes="pageSizes"
        :current-page.sync="page.currentPage"
        :total="page.total"
      ></el-pagination>
    </div>
  </div>
</template>
<script>

export default {
  name: 'myTable',
  data () {
    return {
      // pageSize: 10,
      // pageSizes: [10, 20, 50],
      formData: {},
    }
  },
  computed: {
    // placeOptions() {
    //   let placeOptions
    //   if (this.formList.some(el => el.type == 'regional')) {
    //     placeOptions = require('@/place.json')
    //   } else {
    //     placeOptions = {}
    //   }
    //   return placeOptions.data
    // }
  },
  props: {
    pageSize: {
      type: Number,
      defualt: 10
    },
    pageSizes: {
      type: Array,
      defualt () {
        return [10, 20, 50]
      }
    },
    formList: {
      type: Array,
      default () {
        return []
      }
    },

    showpage: {
      type: Boolean,
      default: false
    },
    columns: {
      default () {
        return []
      },
      type: Array,
      require: true
    },
    tableData: {
      default () {
        return []
      },
      type: Array,
      require: true
    },

    page: {
      type: Object,
    },
    layout: {
      type: String,
      default: 'total, sizes, prev, pager, next, jumper'
    }
  },
  methods: {
    onSubmit () {
      console.log(this.formList)
      this.formList.forEach(el => {
        this.formData[el.key] = el.value
      })
      this.$emit('onSubmit', this.formData)
    },
    handleSizeChange (val) {
      this.page.pageSize = val
      this.currentChange(this.page)
    },
    currentChange () {
      this.$emit('changePage', this.page)
    },
  },
  mounted () {
  }

}
</script>
<style scoped lang="scss">
.block {
  margin: 20px 0;
  text-align: right;
}
.el-pager li.active {
  font-size: 16px !important;
}
.tableForm {
  .el-input__inner {
    width: 180px !important;
  }
  .el-date-editor.el-input,
  .el-date-editor.el-input__inner {
    width: 180px;
  }
}
</style>
<style>
.mytableClass .el-table .cell {
  padding-right: 0 !important;
}
</style>
