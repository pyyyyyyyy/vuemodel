
<template>
  <el-container>
    <el-header style="width: 100%;font-size: 25px">
      个人任务管理
    </el-header>

    <el-container>
      <el-aside width="15%">
        <el-col>
          <el-button type="primary" round @click="dialogFormVisible = true">添加任务</el-button>
          <el-dialog title="添加任务" :visible.sync="dialogFormVisible">

            <el-form ref="form" :model="form" label-width="80px">
              <el-form-item label="任务名称">
                <el-input v-model="form.name"></el-input>
              </el-form-item>

              <el-form-item label="任务类型" align="left">
                <el-select v-model="form.type" placeholder="请选择任务类型">
                  <el-option
                    v-for="item in options"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                  </el-option>
                </el-select>
              </el-form-item>

              <el-form-item label="任务时间">
                <el-col :span="11">
                  <el-date-picker type="date" placeholder="选择开始日期"  v-model="form.begindate" value-format="yyyy-MM-dd" style="width: 100%;">
                  </el-date-picker>
                  <el-date-picker type="date" placeholder="选择截至日期"  v-model="form.enddate" value-format="yyyy-MM-dd" style="width: 100%;">
                  </el-date-picker>
                </el-col>
              </el-form-item>



              <el-form-item>
                <el-button type="primary" @click="create">立即创建</el-button>
                <el-button @click="dialogFormVisible = false">取 消</el-button>
              </el-form-item>
            </el-form>
          </el-dialog>
        </el-col>
      </el-aside>

      <el-container>
        <el-main style="width: 100%">
          <template>
          <el-table
            :data="tabledata"
            ref="filterTable"
            align="left"
            border="true"
            :row-class-name="tableRowClassName"
            :cell-class-name="delLine"
            style="width: 100%;table-layout:fixed;font-size: 20px"
            :default-sort = "{order: 'descending'}"
            v-show="isShow">

            <el-table-column
              label="任务"
              min-width="20%"
              header-align="center">
              <el-table-column
                prop="name"
                label="任务名称"
                min-width="12%"
                align="left"
                header-align="center">
              </el-table-column>
              <el-table-column
                prop="type"
                label="任务类型"
                min-width="8%"
                align="center"
                header-align="center"
                :filters="[{ text:'重要任务',value:'重要任务'},{text: '紧急任务',value: '紧急任务'},{text: '重复任务',value: '重复任务'},{text: '普通任务',value: '普通任务'}]"
                :filter-method="filterType"
                filter-placement="bottom-end">
              </el-table-column>
            </el-table-column>

            <el-table-column
              label="日期"
              min-width="25%"
              align="left"
              header-align="center"
              >

              <el-table-column
              prop="begindate"
              label="开始日期"
              min-width="15%"
              align="left"
              header-align="center"
              sortable
              >
              </el-table-column>
              <el-table-column
                prop="enddate"
                label="截至日期"
                min-width="10%"
                align="left"
                header-align="center"
                sortable>
              </el-table-column>
            </el-table-column>

            <el-table-column
              prop="desc"
              label="当前任务状态"
              min-width="25%"
              align="center"
              header-align="center"
              :filters="[{text:'待完成',value:'待完成'},{text:'已完成',value: '已完成'}]"
              :filter-method="filterDesc"
              filter-placement="bottom-end">
            </el-table-column>

            <el-table-column
              label="操作"
              min-width="25%"
              header-align="center"
              align="left">
              <template slot-scope="a">
                <el-button size="medium" type="success" @click="finish(a.row)">完成</el-button>
                <el-button size="medium" type="danger" @click="del(a.$index,a.row)">删除</el-button>
                <el-button size="medium" type="primary" @click="dialogEditVisible = true,EditClick(a.$index, a.row)">编辑</el-button>

                <el-dialog title="编辑任务" :visible.sync="dialogEditVisible">
                  <el-form ref="Editform" :model="Editform" label-width="80px">
                    <el-form-item label="任务名称">
                      <el-input v-model="Editform.name"></el-input>
                    </el-form-item>

                    <el-form-item label="任务类型" align="left">
                      <el-select v-model="Editform.type" placeholder="请选择任务类型">
                        <el-option
                          v-for="item in options"
                          :key="item.value"
                          :label="item.label"
                          :value="item.value">
                        </el-option>
                      </el-select>
                    </el-form-item>

                    <el-form-item label="任务时间">
                      <el-col :span="11">
                        <el-date-picker type="date" placeholder="选择开始日期"  v-model="Editform.begindate" value-format="yyyy-MM-dd" style="width: 100%;">
                        </el-date-picker>
                        <el-date-picker type="date" placeholder="选择截至日期"  v-model="Editform.enddate" value-format="yyyy-MM-dd" style="width: 100%;">
                        </el-date-picker>
                      </el-col>
                      <el-col class="line" :span="2">-</el-col>
                    </el-form-item>

                    <el-form-item label="任务状态">
                      <el-radio v-model="Editform.desc" label="待完成">待完成</el-radio>
                      <el-radio v-model="Editform.desc" label="已完成">已完成</el-radio>
                    </el-form-item>

                    <el-form-item>
                      <el-button type="primary" @click="update(currentIndex)">确认编辑</el-button>
                      <el-button @click="dialogEditVisible = false">取 消</el-button>
                    </el-form-item>
                  </el-form>
                </el-dialog>
              </template>
            </el-table-column>

          </el-table>
          </template>
        </el-main>
        <el-footer>这是底部!</el-footer>
      </el-container>
    </el-container>
  </el-container>
</template>

<script>
  export default {
    data() {
      return {
        options: [{
          value: '普通任务',
          label: '普通任务'
        }, {
          value: '重要任务',
          label: '重要任务'
        }, {
          value: '重复任务',
          label: '重复任务'
        }, {
          value: '紧急任务',
          label: '紧急任务'
        }, ],

        //添加对话框
        dialogFormVisible: false,
        form: {
          name: '',
          type: '',
          begindate: '',
          enddate:'',
          //创建任务的时候默认任务状态为待完成
          desc: '待完成'
        },
        //编辑对话框
        dialogEditVisible: false,
        Editform: {
          name: '',
          type: '',
          begindate: '',
          enddate:'',
          desc: ''
        },


        formLabelWidth: '120px',
        tabledata:[{
          name:'查Vue实验',
          type:'重要任务',
          begindate:'2019-12-03',
          enddate:'2019-12-04',
          desc:'待完成',
        },
          {
            name:'完成操作系统作业',
            type:'普通任务',
            begindate:'2019-12-22',
            enddate:'2019-12-31',
            desc:'待完成',
          },
          {
            name:'还花呗',
            type:'紧急任务',
            begindate:'2019-12-11',
            enddate:'2019-12-14',
            desc:'待完成',
          },
          {
            name:'背单词',
            type:'重复任务',
            begindate:'2019-12-02',
            enddate:'2019-12-08',
            desc:'待完成',
          },
        ]

      }
    },

    methods: {
      isShow(){
        if(row.desc === '已完成')
          this.show = true;
      },
      //添加任务方法
      create(){
        this.tabledata.push(this.form)//给tabledata添加一个对象
        this.form =  {name: '',begindate:'',enddate:'',type:'',desc:'待完成'}//点击创建后，让option还原，而不是停留在所选的项
        //创建后消失对话框
        //this.dialogFormVisible = false
      },
      del(index){
        this.tabledata.splice(index,1)//删除点击的对象，index是lot-scope="a" a.$index传过来的
      },
      //编辑按钮事件
      EditClick(index,row){
        this.Editform=this.tabledata[index];
        this.currentIndex=index;
      },
      update(currentIndex){
        this.tabledata.splice(currentIndex,1)
        this.tabledata.push(this.Editform)
        this.dialogEditVisible=false
      },
      //完成按钮事件
      finish(row){
        row.desc='已完成'
      },
      //Table带状态表格
      tableRowClassName({row, rowIndex}) {
        if(row.desc === '已完成'){
          return 'finishi-row'
        }
        return '';
      },

      //筛选方法
      filterType(value, row) {
        return row.type === value;
      },
      filterDesc(value,row){
        return row.desc === value;
      },


    }
  }
</script>

<style>

  .el-table .finishi-row {
    background: oldlace;
  }


</style>
