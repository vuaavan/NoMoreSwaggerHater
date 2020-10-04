<template>
  <div id="app">
    <div class="container">
      <!-- Portfolio Section Heading-->
      <h2 class="page-section-heading text-center text-uppercase text-secondary mb-0">Portfolio</h2>
      <!-- Icon Divider-->
      <div class="divider-custom">
        <div class="divider-custom-line"></div>
        <div class="divider-custom-icon"><i class="fas fa-star"></i></div>
        <div class="divider-custom-line"></div>
      </div>
      <!-- Portfolio Grid Items-->
      <method-block :method="method" />
      <div class="row mb-5  section-block">
         <div class="col-md-12">
            <h3 class="title-block">{{eleText}}</h3>
        </div>
        <div class="col-md-12">
          <ParamBlock v-for="(param, index) in params" :param="param" :key="index" v-show="useParam"  />
          <RequestBody v-for="(req, index) in requests" :request="req" :key="index" v-show="useRequest"  />
        </div>
        <div class="col-md-12 text-right">
            <span class="btn btn-outline-danger btn-sm" @click="addEle">{{addElementText}}</span>
        </div>
      </div>
      <div class="row">
        <div class="col-md-12 mb-5">
          <span class="btn btn-primary btn-lg btn-block" @click="gene">Generate</span>
        </div>
      </div>
      <div class="row" v-show="showResult">
        <div class="col-md-6 mb-5">
          <div class="result" style="padding : 20px; background : #444; color : #DA3C78;white-space: break-spaces; text-align:left  ">
              {{ result}}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ParamBlock from './components/ParamBlock.vue'
import MethodBlock from './components/MethodBlock.vue'
import RequestBody from './components/RequestBody.vue'

export default {
  name: 'App',
  components: {
    ParamBlock, 
    RequestBody,
    MethodBlock
  },
  data(){
    return {
      showResult : false,
      str: '',
      result : null,
      params : [],
      requests : [],
      method:{
        path : '',
        type : 'Get',
        tags : '',
        description : '',
        operationId : ''
      },
      paramElement : {
        name : '',
        description : '',
        in : 'path',
        required : false,
        type : 'string'
      },
      requestElement : {
        property : '',
        type : 'string'
      }
    }
  },
  computed:{
    useParam(){
      if(this.method.type == 'Get')
        return true;
      return false;
    },
    useRequest(){
      if(this.method.type == 'Post')
        return true;
      return false;
    },
    eleText(){
      if(this.useParam)
        return 'Parameters'
      return 'RequestBody'
    },
    addElementText(){
      if(this.useParam)
        return 'Add Parameter'
      return 'Add RequestBody'
    }
  },
  methods : {
    addEle(){
      if(this.useParam)
        this.params.push({... this.paramElement})
      else
        this.requests.push({... this.requestElement})
    },
    loopChild(childs, level = 0){
      childs.forEach(key =>{
        let lv = level + 1;
        this.str += `* ` + '     '.repeat(level) + key.value + `\n `
        if(key.childs)
          this.loopChild(key.childs, lv)
      })
    },
    buildString(result){
      this.loopChild(result, 0);
      return this.str
      
    },
    getChildParams(method){
      this.params.forEach(param =>{
        let childParams = [];
        if(param.name)
          childParams.push({ value : `name="${param.name}",`});
        if(param.description)
          childParams.push({ value : `description="${param.description}",`});
        if(param.in)
          childParams.push({ value : `in="${param.in}",`});
        if(param.required)
          childParams.push({ value : `required=${param.required},`});        
        if(param.type){
          let childType = [];
          childType.push({ value : `type="${param.type}",`});
          childParams.push({ value : `@OA\\Schema(`, childs : childType}); 
          childParams.push( { value : `)`} );
        }

        method.childs.push( { value : `@OA\\Parameter(`, childs : childParams} );

        method.childs.push( { value : `),`} );
      })
    },
    getChildRequests(method){
      let requestBody = { value : `@OA\\RequestBody(`, childs : [
        { value : `@OA\\MediaType(`, childs : [
            { value : `mediaType="multipart/form-data",`},
            { value : `@OA\\Schema(` , childs : [

            ]},
            { value : `)`},
        ]},
        { value : `)`},
      ]};
      this.requests.forEach(request =>{
        let childRequests = [];
        if(request.property)
          childRequests.push({ value : `property="${request.property}",`});
        if(request.type)
          childRequests.push({ value : `type="${request.type}",`});
        /*  
        if(param.in)
          childParams.push({ value : `in="${param.in}",`});
        if(param.required)
          childParams.push({ value : `required=${param.required},`});        
        if(param.type){
          let childType = [];
          childType.push({ value : `type="${param.type}",`});
          childParams.push({ value : `@OA\\Schema(`, childs : childType}); 
          childParams.push( { value : `)`} );
        }
*/

        requestBody.childs[0].childs[1].childs.push( { value : `@OA\\Property(`, childs : childRequests} );

        requestBody.childs[0].childs[1].childs.push( { value : `),`} );
      })
      
      method.childs.push(requestBody)
      method.childs.push( { value : `),`} );
    },
    gene(){
      this.showResult= true;
       this.str = '';
      let result = [];
      // method
      let method = { value : `@OA\\${this.method.type}(`}
      method.childs = [];
      if(this.method.path)
        method.childs.push({ value : `path="/${this.method.path}",`});
      if(this.method.tags)
        method.childs.push({ value : `tags={${this.method.tags.split(',').map(d => `"${d}"`).join(",")}},`});
      if(this.method.operationId)
        method.childs.push({ value : `operationId="${this.method.operationId}",`});

      if(this.useParam)
        this.getChildParams(method);
      else
        this.getChildRequests(method)
      //parameters
      /*
      this.params.forEach(param =>{
        let childParams = [];
        if(param.name)
          childParams.push({ value : `name="${param.name}",`});
        if(param.description)
          childParams.push({ value : `description="${param.description}",`});
        if(param.in)
          childParams.push({ value : `in="${param.in}",`});
        if(param.required)
          childParams.push({ value : `required=${param.required},`});        
        if(param.type){
          let childType = [];
          childType.push({ value : `type="${param.type}",`});
          childParams.push({ value : `@OA\\Schema(`, childs : childType}); 
          childParams.push( { value : `)`} );
        }

        method.childs.push( { value : `@OA\\Parameter(`, childs : childParams} );

        method.childs.push( { value : `)`} );
      })
      */
      method.childs.push({ value : `@OA\\Response(`, childs : [
        { value : `response=200,`},
        { value : `description="success", `},
      ] });
      method.childs.push({ value : `)`} )

  console.log('method',method)
      if(this.method.description)
        result.push({ value : this.method.description})
      // result = result.concat(method);
      result.push(method)
      result.push({ value : `)`} )
/*
      let result = [
        { value : 'pika'},
        { value : 'oa/post',
          childs : [
            { value : 'path="/pet"'},
            { value : 'path="/pet"'},
            { value : 'path="/pet"'},
            { value : '@SWG\Parameter(' , childs : [
              { value : 'name="ComponentName"'}
            ]}
          ]
        }
      ];
      */
     console.log('result ', result)
      let str = this.buildString(result);
      this.result = `
/**
 ${str}*/      
`
    },

  },
  mounted(){
  //  this.addParam();
  }
}
</script>

<style>
</style>
