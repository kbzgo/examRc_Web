<template>
  <div id="app">
    <p><el-button @click="addDialogShow = true" type="success">添加新题库</el-button></p>
    <el-table :data="questionDbs" border style="width: 100%">
      <el-table-column fixed prop="dbCode" label="题库编号" width="150">
      </el-table-column>
      <el-table-column prop="dbName" label="题库名" width="120">
      </el-table-column>
      <el-table-column fixed="right" label="操作" width="100">
        <template slot-scope="scope">
          <el-button @click="findByQuestionDb(scope.row)" type="text" size="small">查看</el-button>
          <el-button @click="changeShow(scope.row)" type="text" size="small">修改</el-button>
          <el-button @click="download(scope.row)" type="text" size="small">导出</el-button>
          <!--<a href="http://localhost:8082/project/tmQuestionDb/downloadDb?businessId=159f1f90e42e426ca043c5e10ab575c6">-->
            <!--导出-->
          <!--</a>-->
          <el-button @click="readyDelete(scope.row)" type="text" size="small">停用</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination background layout="prev, pager, next" :total=parseInt(totalpage)*10 @current-change="test1">
    </el-pagination>


    <!----------------------------------------------修改题库Dialog框--------------------------------------------------->


    <el-dialog
      title="修改题库"
      :visible.sync="detailDialogShow"
      width="25%">
      <el-input v-model="updateQuestionDbname" placeholder="请输入新的题库名"/>
      <span slot="footer" class="dialog-footer">
        <el-button @click="detailDialogShow = false">取 消</el-button>
        <el-button @click="updateQuestionDb()" type="primary">确 定</el-button>
      </span>
    </el-dialog>


    <!----------------------------------------------添加题库Dialog框--------------------------------------------------->


    <el-dialog
      title="添加题库"
      :visible.sync="addDialogShow"
      width="25%">
      <p><el-input v-model="newQuestionDbCode" placeholder="请输入题库编号"/></p>
      <el-input v-model="newQuestionDbname" placeholder="请输入题库名"/>
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogShow = false">取 消</el-button>
        <el-button @click="addNewQuestionDb()" type="primary">确 定</el-button>
      </span>
    </el-dialog>


    <!----------------------------------------------确认删除Dialog框--------------------------------------------------->


    <el-dialog
      title="确认删除？"
      :visible.sync="deleteDialogShow"
      width="25%">
      <h2>确认删除题库</h2>
      <h1>{{readyDeleteName}}</h1>
      <span slot="footer" class="dialog-footer">
        <el-button @click="deleteDialogShow = false">取 消</el-button>
        <el-button @click="deleteQuestionDb(readyDeleteId)" type="warning">确 定</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
  import axios from "axios/lib/axios"
  import ElRadioGroup from "element-ui/packages/radio/src/radio-group";

  export default {
    components: {ElRadioGroup},
    data() {
      return {
        questionDbs : [],
        QuestionDb : "",
        newQuestionDbCode : "",
        newQuestionDbname : "",
        detailDialogShow : false,
        addDialogShow : false,
        totalpage : "",
        updateQuestionDbname : "",
        readyDeleteId : "",
        readyDeleteName : "",
        deleteDialogShow : false
      }
    },
    mounted(){
      let that = this;
      that.$options.methods.searchAllQuestionDb(that.$data,1);
    },
    methods:{
      test1(p1){
        let that = this;
        that.$options.methods.searchAllQuestionDb(that.$data,p1);
      },
      //查询题库
      searchAllQuestionDb(dataplace,page){
        let that = this;
        if(sessionStorage.getItem("authKey") == null){
          alert("请先登录");
          that.$router.push({ path: '/login' });
        }else{
          axios.get("http://localhost:8082/project/tmQuestionDb/findAllDbByPagination?rows=5&page="+page,{
            headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
          }).then(function (response) {
            console.log(response);
            dataplace.questionDbs = response.data.rows;
            dataplace.totalpage = response.data.pages;
          })
        }
      },
      readyDelete(p1){
        let that = this;
        that.$data.readyDeleteId = p1.businessId;
        that.$data.readyDeleteName = p1.dbName;
        console.log(that.$data.readyDeleteId);
        that.$data.deleteDialogShow = true;
      },
      //删除题库
      deleteQuestionDb(bid){
        let that = this;
        axios.delete("http://localhost:8082/project/tmQuestionDb/deleteDb/"+bid,{
          headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
        }).then(function (response) {
          console.log(response);
          that.$options.methods.searchAllQuestionDb(that.$data,1);
        })
      },
      //显示修改Dialog
      changeShow(p1){
        let that = this;
        that.$data.QuestionDb = p1;
        that.$data.updateQuestionDbname = that.$data.QuestionDb.dbName;
        that.$data.detailDialogShow = true;
      },
      //修改题库
      updateQuestionDb(){
        let that = this;
        if(that.$data.updateQuestionDbname == ""){
          that.$message.error("不能修改为空名称");

        }else{
          let params = new URLSearchParams();
          params.append("dbName",that.$data.updateQuestionDbname);
          axios.put("http://localhost:8082/project/tmQuestionDb/updateDb/"+that.$data.QuestionDb.businessId,params,{
            headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
          }).then(function (response) {
            console.log(response);
            that.$data.detailDialogShow = false;
            that.$options.methods.searchAllQuestionDb(that.$data,1);
            that.$message.success("修改完成");
          })
        }
      },
      download(row){
        window.location.href="http://localhost:8082/project/tmQuestionDb/downloadDb?businessId="+row.businessId;
      },
      //添加题库
      addNewQuestionDb() {
        let that = this;
        if( that.$data.newQuestionDbCode == "" || that.$data.newQuestionDbname == ""){
          that.$message.error("题库信息不能为空");
        }else {
          let params = new URLSearchParams();
          params.append("dbCode", that.$data.newQuestionDbCode);
          params.append("dbName", that.$data.newQuestionDbname);
          axios.post("http://localhost:8082/project/tmQuestionDb/addDb", params, {
            headers: {"Authorization": "Bearer " + sessionStorage.getItem("authKey")}
          }).then(function (response) {
            console.log(response);
            that.$data.addDialogShow = false;
            that.$message.success("添加成功");
            that.$options.methods.searchAllQuestionDb(that.$data,1);
          })
        }
      },
      findByQuestionDb(p1){
        this.$router.push({ path: '/searchAllQuestion/'+p1.dbCode });
      }
    }
  }
  let a=(sessionStorage.getItem("authKey")!=''&&sessionStorage.getItem("authKey")!=undefined&&sessionStorage.getItem("authKey")!=null)?sessionStorage.getItem("authKey"):localStorage.getItem("authKey")
  axios.defaults.headers.common['Authorization']="Bearer "+a;
</script>

<style scoped>
  #app  {
    width: 25%
  }
</style>
