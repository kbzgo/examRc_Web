<template>
  <div id="app">
    <p><el-button @click="Update = 0" type="success" >输入题目</el-button></p>
    <p><el-button @click="Update = 1" type="success" >提交文件</el-button></p>
    <div v-show="Update == 1">
      <el-upload class="upload-demo" action="http://localhost:8082/project/tmquestion/uploadHtml"
                 name="uploadFile"
                 :headers="head"
                 multiple :limit="3">
        <el-button size="small" type="primary">点击上传</el-button>
        <div slot="tip" class="el-upload__tip">上传HTML</div>
      </el-upload>
      <el-upload class="upload-demo" action="http://localhost:8082/project/tmquestion/uploadExcel"
                 name="uploadFile"
                 :headers="head"
                 multiple :limit="3">
        <el-button size="small" type="primary">点击上传</el-button>
        <div slot="tip" class="el-upload__tip">上传Excel</div>
      </el-upload>
    </div>
    <div v-show="Update == 0">
      <p><el-input type="textarea" :rows="2" v-model="questionTitle" placeholder="输入题干"/></p>
      <p><el-input type="textarea" :rows="2" v-model="questionDesc" placeholder="输入题目描述"/></p>
      <p>
        <el-select @change="showAddMethod($data.questionType,$data)" v-model="questionType" placeholder="题型">
          <el-option
            v-for="item in qType"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      </p>
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
      <!--如果选择的是判断题-->
      <div v-show="type2show">
        <el-select v-model="questionAnswer" placeholder="答案">
          <el-option
            v-for="item in qAnswer"
            :key="item.value"
            :label="item.label"
            :value="item.value">
          </el-option>
        </el-select>
      <p><el-button @click="wantTo($data)" type="success" >提交题目</el-button></p>
      </div>

      <!--如果选择的是单选题-->
      <div v-show="type0show">
        <el-radio-group v-model="trueAnswer" >
          <div v-for="(item,index) in readyOptions" >
            <p>选项{{item.oName}}</p>
            <p><el-radio :label="readyOptions[index].oName"/></p>
            <p><el-input v-model="readyOptions[index].oDesc" placeholder="输入选项内容"/></p>
          </div>
        </el-radio-group>
          <p><el-button @click="deleteOption($data.count,$data)" type="success" >减少选项</el-button></p>
          <p><el-button @click="addOption($data.count++,$data)" type="success" >添加选项</el-button></p>
          <p><el-button @click="wantTo($data)" type="success" >提交题目</el-button></p>
      </div>

      <!--如果选择的是多选题-->
      <div v-show="type1show">
        <el-checkbox-group v-model="trueAnswerMult">
          <div v-for="(item,index) in readyOptionsMult" >
            <p>选项{{item.oName}}</p>
            <p><el-checkbox :label="readyOptionsMult[index].oName"/></p>
            <p><el-input v-model="readyOptionsMult[index].oDesc" placeholder="输入选项内容"/></p>
          </div>
        </el-checkbox-group>
        <p><el-button @click="deleteOptionMult($data.countMult,$data)" type="success" >减少选项</el-button></p>
        <p><el-button @click="addOptionMult($data.countMult++,$data)" type="success" >添加选项</el-button></p>
        <p><el-button @click="wantTo($data)" type="success" >提交题目</el-button></p>
      </div>

      <!--如果选择的是填空题-->
      <div v-show="type3show">
        <div v-for="(item,index) in readyOptionsType3" >
          <p>填空内容</p>
          <p><el-input v-model="readyOptionsType3[index].oDesc" placeholder="输入选项内容"/></p>
        </div>
        <p><el-button @click="deleteOptionType3($data.countType3,$data)" type="success" >减少选项</el-button></p>
        <p><el-button @click="addOptionType3($data.countType3++,$data)" type="success" >添加选项</el-button></p>
        <p><el-button @click="wantTo($data)" type="success" >提交题目</el-button></p>
      </div>

      <!--如果选择的是简答题-->
      <div v-show="type4show">
        <p><el-input type="textarea" :rows="2" v-model="type4Answer" placeholder="输入题目参考答案"/></p>
        <p><el-button @click="wantTo($data)" type="success" >提交题目</el-button></p>
      </div>

    </div>
  </div>
</template>

<script>
  import axios from "axios/lib/axios"
  import ElRadioGroup from "element-ui/packages/radio/src/radio-group";

  export default {
    components: {ElRadioGroup},
    data() {
      return {
        head : {Authorization: "Bearer "+sessionStorage.getItem("authKey")},
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
        },{
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
        questionTitle : "",
        questionDesc : "",
        showAdd : false,
        questionCase : "",
        questionType : "",
        questionAnswer : "",
        questionDid : "",
        count : 4,
        countMult : 4,
        countType3 : 0,
        type2show : false,
        type0show : false,
        type3show : false,
        type1show : false,
        type4show : false,
        readyOptions : [
          { oName : "0" , oDesc: ""},
          { oName : "1" , oDesc: ""},
          { oName : "2" , oDesc: ""},
          { oName : "3" , oDesc: ""}
        ],
        readyOptionsType3 : [

        ],
        readyOptionsMult : [
          { oName : "0" , oDesc: ""},
          { oName : "1" , oDesc: ""},
          { oName : "2" , oDesc: ""},
          { oName : "3" , oDesc: ""}
        ],
        trueAnswer : "",
        trueAnswerMult : [],
        type4Answer : "",
        totalName : "",
        numberName : "",
        totalDesc : "",
        numberDesc : "",
        totalNameMult : "",
        numberNameMult : "",
        totalDescMult : "",
        numberDescMult : "",
        MultFinalAnswer : "",
        db : [],
        errorFlag : false,
        normalUpdate : false,
        Update : ""
      }
    },
    mounted(){
      this.$options.methods.getDb(this.$data);
    },
    methods:{
      getDb(dataplace){
        axios.get("http://localhost:8082/project/tmQuestionDb/findAllDbByPagination?&rows=1000&page=1", {
          headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
        }).then(function (response) {
          dataplace.db = response.data.rows;
          console.log(dataplace.db);
        })
      },
      showAddMethod(p1,dataplace){
        console.log(p1);
        if(p1 == "0"){
          dataplace.type0show = true;
          dataplace.type1show = false;
          dataplace.type2show = false;
          dataplace.type3show = false;
          dataplace.type4show = false;
        }else if(p1 == "1"){
          dataplace.type0show = false;
          dataplace.type1show = true;
          dataplace.type2show = false;
          dataplace.type3show = false;
          dataplace.type4show = false;
        }else if(p1 == "2") {
          dataplace.type0show = false;
          dataplace.type1show = false;
          dataplace.type2show = true;
          dataplace.type3show = false;
          dataplace.type4show = false;
        }else if(p1 == "3"){
          dataplace.type0show = false;
          dataplace.type1show = false;
          dataplace.type2show = false;
          dataplace.type3show = true;
          dataplace.type4show = false;
        }else if(p1 == "4"){
          dataplace.type0show = false;
          dataplace.type1show = false;
          dataplace.type2show = false;
          dataplace.type3show = false;
          dataplace.type4show = true;
        }
      },
      //增加一个选项
      addOption(p1,dataplace){
        dataplace.readyOptions.push({ oName : ""+p1+"" , oDesc: ""});
        console.log(dataplace.readyOptions);
      },
      //减少一个选项
      deleteOption(p1,dataplace){
        let that = this;
        if(dataplace.readyOptions.length == 2){
          that.$message.error("至少有两个选项");
        }else{
          that.$data.count--;
          dataplace.readyOptions.splice(p1-1);
          console.log(dataplace.readyOptions);
        }
      },
      //增加一个选项
      addOptionType3(p1,dataplace){
        dataplace.readyOptionsType3.push({ oName : ""+p1+"" , oDesc: ""});
        dataplace.questionTitle = dataplace.questionTitle + "______";
      },
      //减少一个选项
      deleteOptionType3(p1,dataplace){
        let that = this;
        if(dataplace.readyOptionsType3.length == 0){
          that.$message.error("不可再减少");
        }else{
          that.$data.count--;
          dataplace.readyOptionsType3.splice(p1-1);
        }
      },
      //增加一个选项
      addOptionMult(p1,dataplace){
        dataplace.readyOptionsMult.push({ oName : ""+p1+"" , oDesc: ""});
        console.log(dataplace.readyOptionsMult);
      },
      //减少一个选项
      deleteOptionMult(p1,dataplace){
        let that = this;
        if(dataplace.readyOptionsMult.length == 2){
          that.$message.error("至少有两个选项");
        }else{
          that.$data.countMult--;
          dataplace.readyOptionsMult.splice(p1-1);
          console.log(dataplace.readyOptionsMult);
        }
      },

      //分解数组并提交
      wantTo(dataplace){
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
        }if(dataplace.questionTitle == "" || dataplace.questionType == "" || dataplace.questionDesc == "" || dataplace.questionCase == "" ){
          alert("请填写完整题目信息")
        }else{
          //如果是简答题
          if(dataplace.questionType == "4"){
            if(dataplace.type4Answer == ""){
              alert("请确定答案");
            }else{
              console.log(sessionStorage.getItem("authKey"));
              var params = new URLSearchParams();
              params.append("qTitle",dataplace.questionTitle);
              params.append("qType",dataplace.questionType);
              params.append("qDesc",dataplace.questionDesc);
              params.append("qCase",dataplace.questionCase);
              params.append("qAnswer",dataplace.type4Answer);
              params.append("dId",dataplace.questionDid);
              params.append("fName","null");
              params.append("fPath","null");
              axios.post("http://localhost:8082/project/tmquestion/add",params, {
                headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
              }).then(function (response) {
                console.log(response.data.data);
                dataplace.question = response.data.data;
                alert("添加成功");
              })
            }
          }
          //如果是判断题
          else if(dataplace.questionType == "2"){
            if(dataplace.questionAnswer == ""){
              alert("请确定答案");
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
              axios.post("http://localhost:8082/project/tmquestion/addQuestionAndOptions",params, {
                headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
              }).then(function (response) {
                console.log(response.data.data);
                dataplace.question = response.data.data;
                alert("添加成功");
              })
            }
          }//如果是选择题
          else if(dataplace.questionType == "0"){
            for (let i=0;i<dataplace.readyOptions.length;i++)
            {
              dataplace.totalName += dataplace.readyOptions[i].oName;
              dataplace.numberName += dataplace.readyOptions[i].oName.length+",";
            }
            for (let i=0;i<dataplace.readyOptions.length;i++)
            {
              if(dataplace.readyOptions[i].oDesc == ""){
                dataplace.errorFlag = true;
              }
              dataplace.totalDesc += dataplace.readyOptions[i].oDesc;
              dataplace.numberDesc += dataplace.readyOptions[i].oDesc.length+",";
            }
            dataplace.numberName = dataplace.numberName.substr(0,dataplace.numberName.length-1);
            dataplace.numberDesc = dataplace.numberDesc.substr(0,dataplace.numberDesc.length-1);
            if(dataplace.errorFlag){
              alert("选项内容不全");
            }else if(dataplace.trueAnswer == ""){
              alert("没有指定正确答案");
            }
            else {
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
              axios.post("http://localhost:8082/project/tmquestion/addQuestionAndOptions",params, {
                headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
              }).then(function (response) {
                console.log(response.data.data);
                dataplace.question = response.data.data;
                alert("添加成功");
              })
            }
          }
          //如果是填空题
          else if(dataplace.questionType == "3"){
            let flag = true;
            for (let i=0;i<dataplace.readyOptionsType3.length;i++)
            {
              console.log(dataplace.readyOptionsType3[i].oDesc.indexOf(","));
              console.log(dataplace.readyOptionsType3[i].oDesc.indexOf(":"));
              if(dataplace.readyOptionsType3[i].oDesc.indexOf(",") == -1 && dataplace.readyOptionsType3[i].oDesc.indexOf(":") == -1){
                dataplace.totalName += dataplace.readyOptionsType3[i].oName+":"+dataplace.readyOptionsType3[i].oDesc+",";
              }else{
                flag = false;
              }
            }
            dataplace.totalName = dataplace.totalName.substr(0,dataplace.totalName.length-1);
            console.log(dataplace.totalName);
            console.log(flag);
            if(flag) {
              var params = new URLSearchParams();
              params.append("qTitle",dataplace.questionTitle);
              params.append("qType",dataplace.questionType);
              params.append("qDesc",dataplace.questionDesc);
              params.append("qCase",dataplace.questionCase);
              params.append("qAnswer",dataplace.totalName);
              params.append("dId",dataplace.questionDid);
              params.append("fName","null");
              params.append("fPath","null");
              axios.post("http://localhost:8082/project/tmquestion/add",params, {
                headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
              }).then(function (response) {
                console.log(response.data.data);
                dataplace.question = response.data.data;
                alert("添加成功");
              })
            }else{
              alert("选项内容不能包括逗号或者冒号");
            }
          }
          //如果是多选题
          else if(dataplace.questionType == "1"){
            for (let i=0;i<dataplace.readyOptionsMult.length;i++)
            {
              dataplace.totalNameMult += dataplace.readyOptionsMult[i].oName;
              dataplace.numberNameMult += dataplace.readyOptionsMult[i].oName.length+",";
            }
            for (let i=0;i<dataplace.readyOptionsMult.length;i++)
            {
              if(dataplace.readyOptionsMult[i].oDesc == ""){
                dataplace.errorFlag = true;
              }
              dataplace.totalDescMult += dataplace.readyOptionsMult[i].oDesc;
              dataplace.numberDescMult += dataplace.readyOptionsMult[i].oDesc.length+",";
            }
            for (let i=0;i<dataplace.trueAnswerMult.length;i++)
            {
              dataplace.MultFinalAnswer += dataplace.trueAnswerMult[i]+",";
            }

            dataplace.MultFinalAnswer = dataplace.MultFinalAnswer.substr(0,dataplace.MultFinalAnswer.length-1);
            dataplace.numberNameMult = dataplace.numberNameMult.substr(0,dataplace.numberNameMult.length-1);
            dataplace.numberDescMult = dataplace.numberDescMult.substr(0,dataplace.numberDescMult.length-1);
            console.log(dataplace.totalNameMult);
            console.log(dataplace.numberNameMult);
            console.log(dataplace.totalDescMult);
            console.log(dataplace.numberDescMult);
            console.log(dataplace.trueAnswerMult);
            if(dataplace.errorFlag){
              alert("选项内容不全");
            }else if(dataplace.trueAnswerMult == ""){
              alert("没有指定正确答案");
            }
            else {
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
              axios.post("http://localhost:8082/project/tmquestion/addQuestionAndOptions",params, {
                headers: {"Authorization": "Bearer "+sessionStorage.getItem("authKey")}
              }).then(function (response) {
                console.log(response.data.data);
                dataplace.question = response.data.data;
                alert("添加成功");
              })
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
    width: 20%;
  }
  #right{
  ;
  }
</style>
