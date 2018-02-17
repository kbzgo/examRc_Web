<template>
  <div id="app">


    <!--------------------------------------------------查询题目内容---------------------------------------------------->


    <p>
      <el-select v-model="searchType" placeholder="查询" size="mini">
        <el-option
          v-for="item in searchTypeOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value">
        </el-option>
      </el-select>
      <el-button @click="search($data,1)" type="success" size="small">搜索</el-button>
      <el-button @click="searchAllQuestion($route.params.id,$data,1);" type="success" size="small">查看题库全部</el-button>
      <el-input v-model="searchContent" placeholder="输入题干或ID"/>
    </p>


    <!--------------------------------------------------显示表格内容---------------------------------------------------->


    <el-table :data="questions" border style="width: 100%">
      <el-table-column fixed prop="qtitle" label="题干" width="150">
      </el-table-column>
      <el-table-column prop="qtype" label="题型" width="120">
      </el-table-column>
      <el-table-column prop="qdesc" label="描述" width="120">
      </el-table-column>
      <el-table-column prop="updateDate" label="上传时间" width="120">
      </el-table-column>
      <el-table-column fixed="right" label="操作" width="100">
        <template slot-scope="scope">
          <el-button @click="showDetail(scope.row,$data)" type="text" size="small">查看</el-button>
          <el-button @click="showUpdate(scope.row,$data)" type="text" size="small">编辑</el-button>
          <el-button @click="readyDelete(scope.row)" type="text" size="small">停用</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination background layout="prev, pager, next" :total=parseInt(totalpage)*10 @current-change="test1">
    </el-pagination>


    <!---------------------------------------------显示选择题Dialog框--------------------------------------------------->


    <el-dialog
      title="题目"
      :visible.sync="detailDialogShow0"
      width="50%">
      <h2>{{detail.qtitle}}</h2>
      <el-radio-group v-model="trueAnswer" >
        <div v-for="(item,index) in detail.optionList" >
          <p><el-radio :label="detail.optionList[index].odesc" disabled />
          </p>
        </div>
      </el-radio-group>
      <p>题目描述：{{detail.qdesc}}</p>
      <p>题目难度：{{detail.qcase}}</p>
    </el-dialog>


    <!---------------------------------------------显示多选题Dialog框--------------------------------------------------->


    <el-dialog
      title="题目"
      :visible.sync="detailDialogShow1"
      width="50%">
      <h2>{{detail.qtitle}}</h2>
      <el-checkbox-group v-model="trueAnswerMult" >
        <div v-for="(item,index) in detail.optionList" >
          <p><el-checkbox :label="detail.optionList[index].odesc" disabled />
          </p>
        </div>
      </el-checkbox-group>
      <p>题目描述：{{detail.qdesc}}</p>
      <p>题目难度：{{detail.qcase}}</p>
    </el-dialog>


    <!---------------------------------------------显示判断题Dialog框--------------------------------------------------->


    <el-dialog
      title="题目"
      :visible.sync="detailDialogShow2"
      width="50%">
      <h2>{{detail.qtitle}}</h2>
      <div v-if="detail.qanswer == 0">
        <h2>正确</h2>
      </div>
      <div v-if="detail.qanswer == 1">
        <h2>错误</h2>
      </div>
      <p>题目描述：{{detail.qdesc}}</p>
      <p>题目难度：{{detail.qcase}}</p>
    </el-dialog>


    <!---------------------------------------------显示简答题Dialog框--------------------------------------------------->


    <el-dialog
      title="题目"
      :visible.sync="detailDialogShow4"
      width="50%">
      <h2>{{detail.qtitle}}</h2>
      <h3>{{detail.qanswer}}</h3>
      <p>题目描述：{{detail.qdesc}}</p>
      <p>题目难度：{{detail.qcase}}</p>
    </el-dialog>


    <!---------------------------------------------显示填空题Dialog框--------------------------------------------------->


    <el-dialog
      title="题目"
      :visible.sync="detailDialogShow3"
      width="50%">
      <h2>{{detail.qtitle}}</h2>
      <h3>{{detail.qanswer}}</h3>
      <p>题目描述：{{detail.qdesc}}</p>
      <p>题目难度：{{detail.qcase}}</p>
    </el-dialog>


    <!---------------------------------------------修改判断题Dialog框--------------------------------------------------->


    <!--<el-dialog-->
      <!--title="修改"-->
      <!--:visible.sync="updateDialogShowType2"-->
      <!--:before-close="handleClose"-->
      <!--width="50%">-->
      <!--<p><el-input type="textarea" :rows="2" v-model="questionTitle" placeholder="输入题干"/></p>-->
      <!--<p><el-input type="textarea" :rows="2" v-model="questionDesc" placeholder="输入题目描述"/></p>-->
      <!--<p>-->
        <!--<el-select v-model="questionCase" placeholder="难度">-->
          <!--<el-option-->
            <!--v-for="item in qCase"-->
            <!--:key="item.value"-->
            <!--:label="item.label"-->
            <!--:value="item.value">-->
          <!--</el-option>-->
        <!--</el-select>-->
      <!--</p>-->
      <!--<p>-->
        <!--<el-select v-model="questionDid" placeholder="题库">-->
          <!--<el-option-->
            <!--v-for="item in db"-->
            <!--:key="item.dbCode"-->
            <!--:label="item.dbName"-->
            <!--:value="item.dbCode">-->
          <!--</el-option>-->
        <!--</el-select>-->
      <!--</p>-->
      <!--<el-select v-model="questionAnswer" placeholder="答案">-->
        <!--<el-option-->
          <!--v-for="item in qAnswer"-->
          <!--:key="item.value"-->
          <!--:label="item.label"-->
          <!--:value="item.value">-->
        <!--</el-option>-->
      <!--</el-select>-->
      <!--<p><el-button @click="updateDialogShowType2 = false" type="success" >放弃修改</el-button></p>-->
      <!--<p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>-->
    <!--</el-dialog>-->


    <!---------------------------------------------修改多选题Dialog框--------------------------------------------------->


    <!--<el-dialog-->
      <!--title="修改"-->
      <!--:visible.sync="updateDialogShowType1"-->
      <!--:before-close="handleClose"-->
      <!--width="50%">-->
      <!--<p><el-input type="textarea" :rows="2" v-model="questionTitle" placeholder="输入题干"/></p>-->
      <!--<p><el-input type="textarea" :rows="2" v-model="questionDesc" placeholder="输入题目描述"/></p>-->
      <!--<p>-->
        <!--<el-select v-model="questionCase" placeholder="难度">-->
          <!--<el-option-->
            <!--v-for="item in qCase"-->
            <!--:key="item.value"-->
            <!--:label="item.label"-->
            <!--:value="item.value">-->
          <!--</el-option>-->
        <!--</el-select>-->
      <!--</p>-->
      <!--<p>-->
        <!--<el-select v-model="questionDid" placeholder="题库">-->
          <!--<el-option-->
            <!--v-for="item in db"-->
            <!--:key="item.dbCode"-->
            <!--:label="item.dbName"-->
            <!--:value="item.dbCode">-->
          <!--</el-option>-->
        <!--</el-select>-->
      <!--</p>-->
      <!--<el-checkbox-group v-model="trueAnswerMult">-->
        <!--<div v-for="(item,index) in readyOptionsMult" >-->
          <!--<p>选项{{item.oName}}</p>-->
          <!--<p><el-checkbox :label="readyOptionsMult[index].oname"/></p>-->
          <!--<p><el-input v-model="readyOptionsMult[index].odesc" placeholder="输入选项内容"/></p>-->
        <!--</div>-->
      <!--</el-checkbox-group>-->
      <!--<p><el-button @click="deleteOptionMult($data.countMult,$data)" type="success" >减少选项</el-button></p>-->
      <!--<p><el-button @click="addOptionMult($data.countMult++,$data)" type="success" >添加选项</el-button></p>-->
      <!--<p><el-button @click="updateDialogShowType1 = false" type="success" >放弃修改</el-button></p>-->
      <!--<p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>-->
    <!--</el-dialog>-->


    <!---------------------------------------------修改单选题Dialog框--------------------------------------------------->


    <!--<el-dialog-->
      <!--title="修改"-->
      <!--:visible.sync="updateDialogShowType0"-->
      <!--width="50%"-->
      <!--:before-close="handleClose"-->
      <!--&gt;-->
      <!--<p><el-input type="textarea" :rows="2" v-model="questionTitle" placeholder="输入题干"/></p>-->
      <!--<p><el-input type="textarea" :rows="2" v-model="questionDesc" placeholder="输入题目描述"/></p>-->
      <!--<p>-->
        <!--<el-select v-model="questionCase" placeholder="难度">-->
          <!--<el-option-->
            <!--v-for="item in qCase"-->
            <!--:key="item.value"-->
            <!--:label="item.label"-->
            <!--:value="item.value">-->
          <!--</el-option>-->
        <!--</el-select>-->
      <!--</p>-->
      <!--<p>-->
        <!--<el-select v-model="questionDid" placeholder="题库">-->
          <!--<el-option-->
            <!--v-for="item in db"-->
            <!--:key="item.dbCode"-->
            <!--:label="item.dbName"-->
            <!--:value="item.dbCode">-->
          <!--</el-option>-->
        <!--</el-select>-->
      <!--</p>-->

      <!--<el-radio-group v-model="trueAnswer" >-->
        <!--<div v-for="(item,index) in readyOptions" >-->
          <!--<p>选项{{item.oname}}</p>-->
          <!--<p><el-radio :label="readyOptions[index].oname"/></p>-->
          <!--<p><el-input v-model="readyOptions[index].odesc" placeholder="输入选项内容"/></p>-->
        <!--</div>-->
      <!--</el-radio-group>-->
      <!--<p><el-button @click="deleteOption($data.count,$data)" type="success" >减少选项</el-button></p>-->
      <!--<p><el-button @click="addOption($data.count++,$data)" type="success" >添加选项</el-button></p>-->
      <!--<p><el-button @click="updateDialogShowType0 = false" type="success" >放弃修改</el-button></p>-->
      <!--<p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>-->
    <!--</el-dialog>-->


    <!---------------------------------------------修改通用题Dialog框--------------------------------------------------->


    <el-dialog
      title="修改"
      :visible.sync="updateDialogShowType"
      width="50%"
      :before-close="handleClose"
    >
      <p><el-input type="textarea" :rows="2" v-model="questionTitle" placeholder="输入题干"/></p>
      <p><el-input type="textarea" :rows="2" v-model="questionDesc" placeholder="输入题目描述"/></p>
      <p>
        <el-select v-model="questionCase" placeholder="难度">
          <el-option
            v-for="item in qCase"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </p>
      <p>
        <el-select v-model="questionDid" placeholder="题库">
          <el-option
            v-for="item in db"
            :key="item.dbCode"
            :label="item.dbName"
            :value="item.dbCode">
          </el-option>
        </el-select>
      </p>
      <p>
        <el-select @change="showAddMethod(questionType)" v-model="questionType" placeholder="题型">
          <el-option
            v-for="item in qType"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </p>
      <div v-show="updateType0">
        <el-radio-group v-model="trueAnswer" >
          <div v-for="(item,index) in readyOptions" >
            <p>选项{{item.oname}}</p>
            <p><el-radio :label="readyOptions[index].oname"/></p>
            <p><el-input v-model="readyOptions[index].odesc" placeholder="输入选项内容"/></p>
          </div>
        </el-radio-group>
        <p><el-button @click="deleteOption($data.count,$data)" type="success" >减少选项</el-button></p>
        <p><el-button @click="addOption($data.count++,$data)" type="success" >添加选项</el-button></p>
        <p><el-button @click="updateDialogShowType0 = false" type="success" >放弃修改</el-button></p>
        <p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>
      </div>
      <div v-show="updateType1">
        <el-checkbox-group v-model="trueAnswerMult">
          <div v-for="(item,index) in readyOptionsMult" >
            <p>选项{{item.oName}}</p>
            <p><el-checkbox :label="readyOptionsMult[index].oname"/></p>
            <p><el-input v-model="readyOptionsMult[index].odesc" placeholder="输入选项内容"/></p>
          </div>
        </el-checkbox-group>
        <p><el-button @click="deleteOptionMult($data.countMult,$data)" type="success" >减少选项</el-button></p>
        <p><el-button @click="addOptionMult($data.countMult++,$data)" type="success" >添加选项</el-button></p>
        <p><el-button @click="updateDialogShowType1 = false" type="success" >放弃修改</el-button></p>
        <p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>
      </div>
      <div v-show="updateType2">
        <el-select v-model="questionAnswer" placeholder="答案">
          <el-option
            v-for="item in qAnswer"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
        <p><el-button @click="updateDialogShowType2 = false" type="success" >放弃修改</el-button></p>
        <p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>
      </div>
      <div v-show="updateType3">
        <div v-for="(item,index) in type3answer" >
          <p>选项{{item.label}}</p>
          <p><el-input v-model="item.value" placeholder="输入选项内容"/></p>
        </div>
        <p><el-button @click="deleteOptionType3($data.countType3,$data)" type="success" >减少选项</el-button></p>
        <p><el-button @click="addOptionType3($data.countType3++,$data)" type="success" >添加选项</el-button></p>
        <p><el-button @click="updateDialogShowType1 = false" type="success" >放弃修改</el-button></p>
        <p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>
      </div>
      <div v-show="updateType4">
        <p><el-input type="textarea" :rows="2" v-model="questionAnswer" placeholder="输入题目答案"/></p>
        <p><el-button @click="updateDialogShowType4 = false" type="success" >放弃修改</el-button></p>
        <p><el-button @click="wantTo($data,businessId)" type="success" >提交修改</el-button></p>
      </div>
    </el-dialog>


    <!----------------------------------------------确认删除Dialog框--------------------------------------------------->


    <el-dialog
      title="确认删除？"
      :visible.sync="deleteDialogShow"
      width="25%">
      <h2>确认删除题目</h2>
      <h1>{{readyDeleteName}}</h1>
      <span slot="footer" class="dialog-footer">
        <el-button @click="deleteDialogShow = false">取 消</el-button>
        <el-button @click="deleteQuestion(readyDeleteId,$data)" type="warning">确 定</el-button>
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
        questionTitle : "",
        questionDesc : "",
        questionCase : "",
        questionType : "",
        questionAnswer : "",
        questionDid : "",
        count : 4,
        countMult : 4,
        readyOptions : [
          { oname : "0" , odesc: ""},
          { oname : "1" , odesc: ""},
          { oname : "2" , odesc: ""},
          { oname : "3" , odesc: ""}
        ],
        readyOptionsMult : [
          { oname : "0" , odesc: ""},
          { oname : "1" , odesc: ""},
          { oname : "2" , odesc: ""},
          { oname : "3" , odesc: ""}
        ],
        trueAnswer : "",
        trueAnswerMult : [],
        totalName : "",
        numberName : "",
        totalDesc : "",
        numberDesc : "",
        totalNameMult : "",
        numberNameMult : "",
        totalDescMult : "",
        numberDescMult : "",
        MultFinalAnswer : "",
        searchType : "",
        searchContent : "",
        searchTypeOptions: [{
          value: '0',
          label: 'ID'
        }, {
          value: '1',
          label: '题干'
        }],
        qType: [{
          value: '0',
          label: '单选题'
        }, {
          value: '1',
          label: '多选题'
        }, {
          value: '2',
          label: '判断题'
        }, {
          value: '3',
          label: '填空题'
        }, {
          value: '4',
          label: '简答题'
        }],
        qAnswer: [{
          value: '0',
          label: '正确'
        }, {
          value: '1',
          label: '错误'
        }],
        qCase: [{
          value: '0',
          label: '简单'
        }, {
          value: '1',
          label: '中等'
        }, {
          value: '2',
          label: '困难'
        }, {
          value: '3',
          label: '极度困难'
        }
        ],
        questions : [],
        radio : "",
        detailDialogShow0 : false,
        detailDialogShow1 : false,
        detailDialogShow2 : false,
        detailDialogShow3 : false,
        detailDialogShow4 : false,
        updateDialogShowType : false,
        updateDialogShowType0 : false,
        updateDialogShowType1 : false,
        updateDialogShowType2 : false,
        updateDialogShowType3 : false,
        updateDialogShowType4 : false,
        t2 : "",
        detail : "",
        StaticDid : "",
        db : [],
        businessId : "",
        pp : [],
        type3answer : [],
        totalpage : "",
        currentType : "",
        errorFlag : false,
        readyDeleteId : "",
        readyDeleteName : "",
        deleteDialogShow : false,
        updateType0 : false,
        updateType1 : false,
        updateType2 : false,
        updateType3 : false,
        updateType4 : false
      }
    },
    mounted(){
      let that = this;
      that.$options.methods.searchAllQuestion(that.$route.params.id,that.$data,1);
      that.StaticDid = that.$route.params.id;
      this.$options.methods.getDb(this.$data);
    },
    methods:{
      showAddMethod(type){
        console.log(type);
        let that = this;
        if(type == "0"){
          that.$data.updateType0 = true;
          that.$data.updateType1 = false;
          that.$data.updateType2 = false;
          that.$data.updateType3 = false;
          that.$data.updateType4 = false;
        }
        if(type == "1"){
          that.$data.updateType0 = false;
          that.$data.updateType1 = true;
          that.$data.updateType2 = false;
          that.$data.updateType3 = false;
          that.$data.updateType4 = false;
        }
        if(type == "2"){
          that.$data.updateType0 = false;
          that.$data.updateType1 = false;
          that.$data.updateType2 = true;
          that.$data.updateType3 = false;
          that.$data.updateType4 = false;
        }
        if(type == "3"){
          that.$data.updateType0 = false;
          that.$data.updateType1 = false;
          that.$data.updateType2 = false;
          that.$data.updateType3 = true;
          that.$data.updateType4 = false;
        }
        if(type == "4"){
          that.$data.updateType0 = false;
          that.$data.updateType1 = false;
          that.$data.updateType2 = false;
          that.$data.updateType3 = false;
          that.$data.updateType4 = true;
        }
      },
      //搜索功能
      search(dataplace,page){
        dataplace.currentType = 0;
        console.log(dataplace.searchType);
        if(dataplace.searchType == 0){
          axios.get("http://localhost:8082/project/tmquestion/detailQuestion/"+dataplace.searchContent, {
            headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
          }).then(function (response) {
            console.log(response);
            //转换时间戳
            let newDate = new Date();
            newDate.setTime(response.data.updateDate);
            response.data.updateDate = newDate.toLocaleString();
            //转换选项
            if(response.data.qtype == "0"){
              response.data.qtype = "单选题";
            }
            if(response.data.qtype == "1"){
              response.data.qtype = "多选题";
            }
            if(response.data.qtype == "2"){
              response.data.qtype = "判断题";
            }
            if(response.data.qtype == "3"){
              response.data.qtype = "填空题";
            }
            if(response.data.qtype == "4"){
              response.data.qtype = "简答题";
            }
            dataplace.questions = [];
            dataplace.questions.push( response.data );
          })
        }else{
          axios.get("http://localhost:8082/project/tmquestion/queryQtitleTmQuestionByPagination?page="+page+"&rows=5&qTitle="+dataplace.searchContent+"&dId="+dataplace.StaticDid, {
            headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
          }).then(function (response) {
            console.log(response);
            for(let i=0;i<response.data.rows.length;i++){
              //从时间戳转化为标准时间
              let newDate = new Date();
              newDate.setTime(response.data.rows[i].updateDate);
              response.data.rows[i].updateDate = newDate.toLocaleString();
              //从提醒编号转换为题型名
              if(response.data.rows[i].qtype == "0"){
                response.data.rows[i].qtype = "单选题";
              }
              if(response.data.rows[i].qtype == "1"){
                response.data.rows[i].qtype = "多选题";
              }
              if(response.data.rows[i].qtype == "2"){
                response.data.rows[i].qtype = "判断题";
              }
              if(response.data.rows[i].qtype == "3"){
                response.data.rows[i].qtype = "填空题";
              }
              if(response.data.rows[i].qtype == "4"){
                response.data.rows[i].qtype = "简答题";
              }
            }
            dataplace.questions = [];
            dataplace.questions =  response.data.rows ;
            dataplace.totalpage = response.data.pages;
          })
        }
      },
      //分页功能（按题目查询有BUG）
      test1(p1){
        let that = this;
        if(that.$data.currentType == 0){
          that.$data.searchType == 1;
          search(that.$data,p1);
        }
        if(that.$data.currentType == 1){
          that.$options.methods.searchAllQuestion(that.$route.params.id,that.$data,p1);
        }
        that.StaticDid = that.$route.params.id;
      },
      //增加一个选项
      addOption(p1,dataplace){
        dataplace.readyOptions.push({ oname : ""+p1+"" , odesc: ""});
        console.log(dataplace.readyOptions);
      },
      //减少一个选项
      deleteOption(p1,dataplace){
        let that = this;
        if(dataplace.readyOptions.length  == 2){
          that.$message.error("至少有两个选项");
        }else{
          dataplace.count--;
          dataplace.readyOptions.splice(p1-1);
          console.log(dataplace.readyOptions);
        }
      },
      //增加一个选项
      addOptionType3(p1,dataplace){

        dataplace.type3answer.push({ label : ""+(dataplace.type3answer.length)+"" , value: ""});
        dataplace.questionTitle = dataplace.questionTitle + "______";
      },
      //减少一个选项
      deleteOptionType3(p1,dataplace){
        let that = this;
        if(dataplace.type3answer.length == 0){
          that.$message.error("不可再减少");
        }else{
          that.$data.count--;
          dataplace.type3answer.splice(dataplace.type3answer.length-1);
        }
      },
      //增加一个选项
      addOptionMult(p1,dataplace){
        dataplace.readyOptionsMult.push({ oname : ""+p1+"" , odesc: ""});
        console.log(dataplace.readyOptionsMult);
      },
      //减少一个选项
      deleteOptionMult(p1,dataplace){
        let that = this;
        if(dataplace.readyOptionsMult.length  == 2){
          that.$message.error("至少有两个选项");
        }else{
          dataplace.countMult--;
          dataplace.readyOptionsMult.splice(p1-1);
          console.log(dataplace.readyOptionsMult);
        }
      },
      //获取题库
      getDb(dataplace){
        axios.get("http://localhost:8082/project/tmQuestionDb/findAllDbByPagination?&rows=1000&page=1", {
          headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
        }).then(function (response) {
          dataplace.db = response.data.rows;
          console.log(dataplace.db);
        })
      },
      searchAllQuestion(DbCode,dataplace,p1){
        dataplace.currentType = 1;
        if(sessionStorage.getItem("authKey") == null){
          alert("请先登录");
          this.$router.push({ path: '/login' });
        }else{
          axios.get("http://localhost:8082/project/tmquestion/queryTmQuestionByPagination?rows=5&page="+p1+"&dId="+DbCode,{
            headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
          }).then(function (response) {
            for(let i=0;i<response.data.rows.length;i++){
              //将时间戳转化为标准时间
              let newDate = new Date();
              newDate.setTime(response.data.rows[i].updateDate);
              response.data.rows[i].updateDate = newDate.toLocaleString();
              //将题型编号转化为题型名称
              if(response.data.rows[i].qtype == "0"){
                response.data.rows[i].qtype = "单选题";
              }
              if(response.data.rows[i].qtype == "1"){
                response.data.rows[i].qtype = "多选题";
              }
              if(response.data.rows[i].qtype == "2"){
                response.data.rows[i].qtype = "判断题";
              }
              if(response.data.rows[i].qtype == "3"){
                response.data.rows[i].qtype = "填空题";
              }
              if(response.data.rows[i].qtype == "4"){
                response.data.rows[i].qtype = "简答题";
              }
            }
            console.log(response);
            dataplace.questions = response.data.rows;
            dataplace.totalpage = response.data.pages;
            console.log("totalpage="+dataplace.totalpage);
          })
        }
      },
      showDetail(p1,dataplace){
        console.log(p1);
        //选择题细节显示
        if(p1.qtype == "单选题"){
          dataplace.detailDialogShow0 = true;
          dataplace.detail = p1;
          dataplace.trueAnswer = dataplace.detail.optionList[p1.qanswer].odesc;
          console.log(dataplace.trueAnswer);
        }
        //多选题细节显示
        if(p1.qtype == "多选题"){
          dataplace.detailDialogShow1 = true;
          dataplace.detail = p1;
          console.log(p1.qanswer);
          for(let i=0;i<p1.qanswer.split(",").length;i++){
            dataplace.trueAnswerMult.push(dataplace.detail.optionList[p1.qanswer.split(",")[i]].odesc);
          }
          console.log(dataplace.trueAnswerMult);
        }
        //判断题细节显示
        if(p1.qtype == "判断题"){
          dataplace.detailDialogShow2 = true;
          dataplace.detail = p1;
          console.log(p1.qanswer);
        }
        //简答题细节显示
        if(p1.qtype == "简答题"){
          dataplace.detailDialogShow4 = true;
          dataplace.detail = p1;
          console.log(p1.qanswer);
        }
        //填空题细节显示
        if(p1.qtype == "填空题"){
          dataplace.detailDialogShow3 = true;
          dataplace.detail = p1;
          console.log(p1.qanswer);
        }
      },
      showUpdate(p1,dataplace){
        console.log(p1.qtype);
        let that = this;
        if(p1.qtype == "单选题"){
          console.log(p1);
          dataplace.updateDialogShowType = true;
          dataplace.questionTitle = p1.qtitle;
          dataplace.questionDesc = p1.qdesc;
          dataplace.questionCase = p1.qcase;
          dataplace.questionDid = p1.did;
          dataplace.readyOptions = p1.optionList;
          dataplace.trueAnswer = p1.qanswer;
          dataplace.businessId = p1.businessId;
          dataplace.questionType = "0";
          console.log(dataplace.readyOptions);
          dataplace.updateType0 = true;
          dataplace.updateType1 = false;
          dataplace.updateType2 = false;
        }
        if(p1.qtype == "多选题"){
          console.log(p1);
          dataplace.updateDialogShowType = true;
          dataplace.questionTitle = p1.qtitle;
          dataplace.questionDesc = p1.qdesc;
          dataplace.questionCase = p1.qcase;
          dataplace.questionDid = p1.did;
          dataplace.readyOptionsMult = p1.optionList;
          dataplace.trueAnswerMult = p1.qanswer.split(",");
          dataplace.businessId = p1.businessId;
          dataplace.questionType = "1";
          console.log(dataplace.readyOptions);
          dataplace.updateType0 = false;
          dataplace.updateType1 = true;
          dataplace.updateType2 = false;
        }
        if(p1.qtype == "判断题"){
          console.log(p1);
          dataplace.updateDialogShowType = true;
          dataplace.questionTitle = p1.qtitle;
          dataplace.questionDesc = p1.qdesc;
          dataplace.questionCase = p1.qcase;
          dataplace.questionDid = p1.did;
          dataplace.questionAnswer = p1.qanswer;
          dataplace.businessId = p1.businessId;
          dataplace.questionType = "2";
          console.log(dataplace.readyOptions);
          dataplace.updateType0 = false;
          dataplace.updateType1 = false;
          dataplace.updateType2 = true;

        }
        if(p1.qtype == "填空题"){
          console.log(p1);
          dataplace.updateDialogShowType = true;
          dataplace.questionTitle = p1.qtitle;
          dataplace.questionDesc = p1.qdesc;
          dataplace.questionCase = p1.qcase;
          dataplace.questionDid = p1.did;
          dataplace.questionAnswer = p1.qanswer;
          dataplace.businessId = p1.businessId;
          dataplace.questionType = "3";
          dataplace.updateType0 = false;
          dataplace.updateType1 = false;
          dataplace.updateType2 = false;
          dataplace.updateType3 = true;
          dataplace.updateType4 = false;
          let i = 0;
          console.log(p1.type3AnswerList[2] == undefined);
          for(let j=0;p1.type3AnswerList[j] != undefined;j++){
            dataplace.type3answer.push({ label : ""+j+"" , value: p1.type3AnswerList[j]});
          }
          console.log(dataplace.type3answer)
        }
        if(p1.qtype == "简答题"){
          console.log(p1);
          dataplace.updateDialogShowType = true;
          dataplace.questionTitle = p1.qtitle;
          dataplace.questionDesc = p1.qdesc;
          dataplace.questionCase = p1.qcase;
          dataplace.questionDid = p1.did;
          dataplace.questionAnswer = p1.qanswer;
          dataplace.businessId = p1.businessId;
          dataplace.questionType = "4";
          console.log(dataplace.readyOptions);
          dataplace.updateType0 = false;
          dataplace.updateType1 = false;
          dataplace.updateType2 = false;
          dataplace.updateType3 = false;
          dataplace.updateType4 = true;

        }
      },
      readyDelete(p1){
        let that = this;
        that.$data.readyDeleteId = p1.businessId;
        that.$data.readyDeleteName = p1.qtitle;
        console.log(that.$data.readyDeleteId);
        that.$data.deleteDialogShow = true;
      },
      deleteQuestion(bid,dataplace){
        let that = this;
        axios.delete("http://localhost:8082/project/tmquestion/deleteQuestion/"+bid,{
          headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
        }).then(function (response) {
          console.log(response);
          that.$options.methods.searchAllQuestion(dataplace.StaticDid,dataplace,1);
          that.$message.success("删除成功");
          that.$data.deleteDialogShow = false;
        })
      },
      handleClose(done) {
        this.$confirm('确认关闭？')
          .then(_ => {
            done();
          })
          .catch(_ => {});
      },
      //分解数组并提交
      wantTo(dataplace,bid){
        let that1 = this;
        console.log(dataplace.questionType)
        dataplace.totalName = "";
        dataplace.numberName = "";
        dataplace.totalDesc ="";
        dataplace.numberDesc =""
        dataplace.totalNameMult = "";
        dataplace.numberNameMult = "";
        dataplace.totalDescMult ="";
        dataplace.numberDescMult ="";
        dataplace.MultFinalAnswer = "";
        if(sessionStorage.getItem("authKey") == null){
          alert("请先登录");
        }else{
          if(dataplace.questionTitle == "" || dataplace.questionDesc == ""){
            that1.$message.error("修改内容不能为空");
          }else{
            //如果是判断题
            if(dataplace.questionType == "2"){
              if(that1.$data.questionAnswer.length == 0){
                that1.$message.error("请选择答案");
              }else{
                console.log(sessionStorage.getItem("authKey"));
                var params = new URLSearchParams();
                params.append("qTitle",dataplace.questionTitle);
                params.append("qType",dataplace.questionType);
                params.append("qDesc",dataplace.questionDesc);
                params.append("qCase",dataplace.questionCase);
                params.append("qAnswer",dataplace.questionAnswer);
                params.append("dId",dataplace.questionDid);
                params.append("fName","null");
                params.append("fPath","null");
                axios.put("http://localhost:8082/project/tmquestion/updateQuestion/"+bid,params, {
                  headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
                }).then(function (response) {
                  console.log(response.data.data);
                  dataplace.updateDialogShowType = false;
                  that1.$options.methods.searchAllQuestion(that1.$route.params.id,that1.$data,1);
                  that1.$message.success("修改成功");
                })
              }
            }
            //如果是简答题
            if(dataplace.questionType == "4"){
              if(that1.$data.questionAnswer.length == 0){
                that1.$message.error("请完善答案");
              }else{
                console.log(sessionStorage.getItem("authKey"));
                var params = new URLSearchParams();
                params.append("qTitle",dataplace.questionTitle);
                params.append("qType",dataplace.questionType);
                params.append("qDesc",dataplace.questionDesc);
                params.append("qCase",dataplace.questionCase);
                params.append("qAnswer",dataplace.questionAnswer);
                params.append("dId",dataplace.questionDid);
                params.append("fName","null");
                params.append("fPath","null");
                axios.put("http://localhost:8082/project/tmquestion/updateQuestion/"+bid,params, {
                  headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
                }).then(function (response) {
                  console.log(response.data.data);
                  dataplace.updateDialogShowType = false;
                  that1.$options.methods.searchAllQuestion(that1.$route.params.id,that1.$data,1);
                  that1.$message.success("修改成功");
                })
              }
            }
            //如果是填空题
            if(dataplace.questionType == "3"){
              if(that1.$data.type3answer.length == 0){
                that1.$message.error("请完善答案");
              }else{
                let flag = true;
                dataplace.questionAnswer = "";
                for (let i=0;i<dataplace.type3answer.length;i++)
                {
                  if(dataplace.type3answer[i].value.indexOf(",") == -1 && dataplace.type3answer[i].value.indexOf(":") == -1){
                    dataplace.questionAnswer += dataplace.type3answer[i].label+":"+dataplace.type3answer[i].value+",";
                  }else{
                    flag = false;
                  }

                }
                dataplace.questionAnswer = dataplace.questionAnswer.substr(0,dataplace.questionAnswer.length-1);
                console.log(dataplace.totalName);
                console.log(sessionStorage.getItem("authKey"));
                if(flag){
                  var params = new URLSearchParams();
                  params.append("qTitle",dataplace.questionTitle);
                  params.append("qType",dataplace.questionType);
                  params.append("qDesc",dataplace.questionDesc);
                  params.append("qCase",dataplace.questionCase);
                  params.append("qAnswer",dataplace.questionAnswer);
                  params.append("dId",dataplace.questionDid);
                  params.append("fName","null");
                  params.append("fPath","null");
                  axios.put("http://localhost:8082/project/tmquestion/updateQuestion/"+bid,params, {
                    headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
                  }).then(function (response) {
                    console.log(response.data.data);
                    dataplace.updateDialogShowType = false;
                    that1.$options.methods.searchAllQuestion(that1.$route.params.id,that1.$data,1);
                    that1.$message.success("修改成功");
                  })
                }else{
                  that1.$message.error("选项内容不能包括逗号或者冒号");
                }
              }
            }
            //如果是选择题
            else if(dataplace.questionType == "0"){
              for (let i=0;i<dataplace.readyOptions.length;i++)
              {
                dataplace.totalName += dataplace.readyOptions[i].oname;
                dataplace.numberName += dataplace.readyOptions[i].oname.length+",";
              }
              for (let i=0;i<dataplace.readyOptions.length;i++)
              {
                if(dataplace.readyOptions[i].odesc.length == 0){
                  dataplace.errorFlag = true;
                  break;
                }else{
                dataplace.errorFlag = false;
                }
                dataplace.totalDesc += dataplace.readyOptions[i].odesc;
                dataplace.numberDesc += dataplace.readyOptions[i].odesc.length+",";
              }
              dataplace.numberName = dataplace.numberName.substr(0,dataplace.numberName.length-1);
              dataplace.numberDesc = dataplace.numberDesc.substr(0,dataplace.numberDesc.length-1);
              if(dataplace.errorFlag){
                that1.$message.error("选项内容不全");
              }else if(dataplace.trueAnswer == ""){
                that1.$message.error("没有指定正确答案");
              }else{
                console.log(dataplace.totalName);
                console.log(dataplace.numberName);
                console.log(dataplace.totalDesc);
                console.log(dataplace.numberDesc);
                console.log(dataplace.trueAnswer);
                console.log(dataplace.questionDid);
                var params = new URLSearchParams();
                params.append("qTitle",dataplace.questionTitle);
                params.append("qType",dataplace.questionType);
                params.append("qDesc",dataplace.questionDesc);
                params.append("qCase",dataplace.questionCase);
                params.append("qAnswer",dataplace.trueAnswer);
                params.append("dId",dataplace.questionDid);
                params.append("fName","null");
                params.append("fPath","null");
                params.append("options",dataplace.totalName);
                params.append("optionsLength",dataplace.numberName);
                params.append("odescs",dataplace.totalDesc);
                params.append("odescsLength",dataplace.numberDesc);
                axios.put("http://localhost:8082/project/tmquestion/updateQuestion/"+bid,params, {
                  headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
                }).then(function (response) {
                  console.log(response.data.data);
                  dataplace.updateDialogShowType = false;
                  that1.$message.success("修改成功");
                  that1.$options.methods.searchAllQuestion(that1.$route.params.id,that1.$data,1);
                })
              }
            }//如果是多选题
            else if(dataplace.questionType == "1"){
              for (let i=0;i<dataplace.readyOptionsMult.length;i++)
              {
                dataplace.totalNameMult += dataplace.readyOptionsMult[i].oname;
                dataplace.numberNameMult += dataplace.readyOptionsMult[i].oname.length+",";
              }
              for (let i=0;i<dataplace.readyOptionsMult.length;i++)
              {
                if(dataplace.readyOptionsMult[i].odesc.length == 0){
                  dataplace.errorFlag = true;
                  break;
                }else{
                  dataplace.errorFlag = false;
                }
                dataplace.totalDescMult += dataplace.readyOptionsMult[i].odesc;
                dataplace.numberDescMult += dataplace.readyOptionsMult[i].odesc.length+",";
              }
              for (let i=0;i<dataplace.trueAnswerMult.length;i++)
              {
                dataplace.MultFinalAnswer += dataplace.trueAnswerMult[i]+",";
              }

              dataplace.MultFinalAnswer = dataplace.MultFinalAnswer.substr(0,dataplace.MultFinalAnswer.length-1);
              dataplace.numberNameMult = dataplace.numberNameMult.substr(0,dataplace.numberNameMult.length-1);
              dataplace.numberDescMult = dataplace.numberDescMult.substr(0,dataplace.numberDescMult.length-1);
              if(dataplace.errorFlag){
                that1.$message.error("选项内容不全");
              }else if(dataplace.trueAnswerMult == ""){
                that1.$message.error("没有指定正确答案");
              }else{
                console.log(dataplace.totalNameMult);
                console.log(dataplace.numberNameMult);
                console.log(dataplace.totalDescMult);
                console.log(dataplace.numberDescMult);
                console.log(dataplace.trueAnswerMult);
                var params = new URLSearchParams();
                params.append("qTitle",dataplace.questionTitle);
                params.append("qType",dataplace.questionType);
                params.append("qDesc",dataplace.questionDesc);
                params.append("qCase",dataplace.questionCase);
                params.append("qAnswer",dataplace.MultFinalAnswer);
                params.append("dId",dataplace.questionDid);
                params.append("fName","null");
                params.append("fPath","null");
                params.append("options",dataplace.totalNameMult);
                params.append("optionsLength",dataplace.numberNameMult);
                params.append("odescs",dataplace.totalDescMult);
                params.append("odescsLength",dataplace.numberDescMult);
                console.log(bid);
                axios.put("http://localhost:8082/project/tmquestion/updateQuestion/"+bid,params, {
                  headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
                }).then(function (response) {
                  console.log(response.data.data);
                  dataplace.updateDialogShowType = false;
                  that1.$message.success("修改成功");
                  that1.$options.methods.searchAllQuestion(that1.$route.params.id,that1.$data,1);
                })
              }
            }
          }
        }
      }
    }
  }
  let a=(sessionStorage.getItem("authKey")!=''&&sessionStorage.getItem("authKey")!=undefined&&sessionStorage.getItem("authKey")!=null)?sessionStorage.getItem("authKey"):localStorage.getItem("authKey")
  axios.defaults.headers.common['Authorization']="Bearer "+a;
</script>

<style scoped>
  #app{
    width: 40%;
  }
  #right{
  ;
  }
</style>
