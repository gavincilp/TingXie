<template>
   <Spin size="large" fix :show="spinShow"></Spin>
   <a href="https://github.com/gavincilp/TingXie" class="github-corner" aria-label="View source on GitHub"><svg width="100" height="100" viewBox="0 0 250 250" style="z-index:200;fill:#8bc34a; color:#fff; position: absolute; top: 0; border: 0; right: 0;" aria-hidden="true"><path d="M0,0 L115,115 L130,115 L142,142 L250,250 L250,0 Z"></path><path d="M128.3,109.0 C113.8,99.7 119.0,89.6 119.0,89.6 C122.0,82.7 120.5,78.6 120.5,78.6 C119.2,72.0 123.4,76.3 123.4,76.3 C127.3,80.9 125.5,87.3 125.5,87.3 C122.9,97.6 130.6,101.9 134.4,103.2" fill="currentColor" style="transform-origin: 130px 106px;" class="octo-arm"></path><path d="M115.0,115.0 C114.9,115.1 118.7,116.5 119.8,115.4 L133.7,101.6 C136.9,99.2 139.9,98.4 142.2,98.6 C133.8,88.0 127.5,74.4 143.8,58.0 C148.5,53.4 154.0,51.2 159.7,51.0 C160.3,49.4 163.2,43.6 171.4,40.1 C171.4,40.1 176.1,42.5 178.8,56.2 C183.1,58.6 187.2,61.8 190.9,65.4 C194.5,69.0 197.7,73.2 200.1,77.6 C213.8,80.2 216.3,84.9 216.3,84.9 C212.7,93.1 206.9,96.0 205.4,96.6 C205.1,102.4 203.0,107.8 198.3,112.5 C181.9,128.9 168.3,122.5 157.7,114.1 C157.9,116.9 156.7,120.9 152.7,124.9 L141.0,136.5 C139.8,137.7 141.6,141.9 141.8,141.8 Z" fill="currentColor" class="octo-body"></path></svg></a>
   <Layout >
        <Header class="layout-header">
            <img src="./assets/monkey.svg" class="layout-logo"/>
            <div style="display:inline-block;margin-left:5px" v-for="(word,i) in logo" :key="i">
                <div style="height:1.5em;text-align: center;">{{word.pinyin}}</div>
                <div style="font-size:20px">{{word.hanzi}}</div>
            </div> 
            <div style="float:right;margin-right:60px;font-weight: bolder;line-height:28px;text-align: center;">
                修改课本词表、词库和拼音看这里
                <br>
                 
                <Icon class="move" type="md-arrow-round-forward" size="32"/>
            </div>
        </Header>       
        <Content class="layout-content">
            <Row type="flex" :style="{minHeight:height-64+'px'}">
                <Col span="4" style=" padding:20px; background-color:lavender;">
                    <Form :model="formItem" :label-width="120" ref="form" :rules="ruleValidate" >
                        <FormItem label="标题" prop="title">
                            <Input v-model="formItem.title"></Input>                           
                        </FormItem>
                        <FormItem label="副标题" prop="subTitle">
                            <Input v-model="formItem.subTitle"></Input>                           
                        </FormItem>
                        
                        <FormItem label="出题个数" prop="count">
                            <InputNumber :min="1" :max="80" v-model="formItem.total" />
                        </FormItem>
                        <FormItem>
                            <Button type="primary" @click="generate">生成</Button>
                            <Button type="success" @click="print">打印</Button>
                        </FormItem>
                        <FormItem label="考查汉字" prop="checkWords">
                            <Input v-model="formItem.checkWords"  type="textarea"></Input>
                            {{formItem.checkWords.length}}个字
                        </FormItem>
                        <div style="margin-left:120px">
                            <Tree :data="checkCi" show-checkbox multiple @on-check-change="selectCi"></Tree>
                        </div>
                        <FormItem label="已学汉字" prop="learntWords">
                            <Input v-model="formItem.learntWords"  type="textarea" clearable></Input>
                            {{formItem.learntWords.length}}个字
                        </FormItem>
                        <div style="margin-left:120px">
                            <Tree :data="cibiao" show-checkbox multiple @on-check-change="select"></Tree>
                        </div>
                        
                        
                    </Form>
                </Col>
                <Col ref="paper" span="10">
                    <div style="text-align:center;line-height:1.5em;font-size:24px;font-weight: bolder;">{{formItem.title}}</div>
                    <div style="text-align:center;line-height:1em;font-size:16px">{{formItem.subTitle}}</div>
                    <div style="padding:0px 20px ;line-height:auto;display:flex; justify-content: space-between; flex-wrap:wrap ;align-content:flex-start;">
                        <div v-for="(w,key) in printArray" :key="key" style="padding:6px;position:relative;" @mouseenter="w.show=true;" @mouseleave="w.show=false">
                            <Icon type="md-close-circle" class="close" style="position:absolute;top:0px;right:0px;z-index: 100;" @click="remove(key)" v-show="w.show"/>
                            <div v-for="(l,i) in w.print" :key="i" style="display:inline-block;color:black;width:48px;height:78px;positon:relative;background: url('tian.jpg') no-repeat; background-size: cover;page-break-inside: avoid;">                         
                                <div style="line-height:24px;font-size:14px;text-align:center;">{{l.pinyin}}</div>
                                <div style="line-height:1.6em;font-size:32px;text-align:center" class="noprint">{{l.letter}}</div>
                            </div>
                        </div>
                    </div>
                    <div style="page-break-before: always;">
                        <div style="padding:0px 20px ;line-height:auto;display:flex; justify-content: space-between; flex-wrap:wrap ;align-content:flex-start;">
                            <div v-for="(w,key) in printArray" :key="key" style="padding:6px;position:relative;">                               
                                <div v-for="(l,i) in w.print" :key="i" style="display:inline-block;color:black;width:48px;height:48px;positon:relative; background-size: cover;page-break-inside: avoid; border-bottom: 1px solid #90b374;">                         
                                    <div style="line-height:1.6em;font-size:32px;text-align:center">{{l.letter}}</div>
                                </div>
                            </div>
                        </div>
                    </div>
                </Col>
                <Col span="10" style="background:lavender">
                    <Collapse>                            
                        <Panel  v-for="letter in currentCiku" :key="letter" :name="letter.letter">
                            {{letter.letter}} ( {{letter.arr.length}} )
                            <template #content>
                                <Button style="margin:2px;" v-for="(word,i) in letter.arr" :key="word.word" @click="add(letter,word,i)">{{word.word}} </Button>
                            </template>
                        </Panel>
                    </Collapse>
                </Col>
            </Row>
        </Content>       
    </Layout>

</template>

<style scoped>
.close:hover{
    color:#ed4014;
    cursor:pointer;
}
.layout-base{
    border: 1px solid #d7dde4;
    background: #f5f7f9;
    position: relative;
    border-radius: 4px;
    color: white;
    text-align: center;
    overflow: hidden;
}
.layout-base > .ivu-layout {
    margin-bottom: 20px;
}
.layout-con{
    position: relative;
    overflow: hidden;
    text-align: center;
}
.layout-single{
    height: 253px;
    margin-bottom: 48px;
    font-size: 14px;
    color: white;
}
.layout-con:last-child{
    margin: 0;
}
.layout-logo{
     
    height:60px;
    
    border-radius: 3px;
    float: left;
    position: relative;
    top: 2px;
    left: 0px;
}
.layout-nav{
    width: 420px;
    margin: 0 auto;
        margin-right: 20px;
}
.layout-breadcrumb{
    padding: 10px 15px 0;
}
.layout-logo-left{
    width: 90%;
    height: 30px;
    background: #5b6270;
    border-radius: 3px;
    margin: 15px auto;
}
.layout-hide-text .layout-text{
    display: none;
}
.layout-header{
    color:white;
    line-height: 2.5em !important;
    background-color:#0F8DE9
}

.layout-footer{
    background: #7CBCE9;
    color: white;
}

 
.layout-footer-center{
    text-align: center;
}
.layout iframe{
    width: 100%;
    border: 0;
}
.layout-header-bar{
    background: #fff;
    box-shadow: 0 1px 1px rgba(0,0,0,.1);
}
.menu-icon{
    transition: all .3s;
}
.rotate-icon{
    transform: rotate(-90deg);
}
.menu-item span{
    display: inline-block;
    overflow: hidden;
    width: 69px;
    text-overflow: ellipsis;
    white-space: nowrap;
    vertical-align: bottom;
    transition: width .2s ease .2s;
}
.menu-item i{
    transform: translateX(0px);
    transition: font-size .2s ease, transform .2s ease;
    vertical-align: middle;
    font-size: 16px;
}
.collapsed-menu span{
    width: 0px;
    transition: width .2s ease;
}
.collapsed-menu i{
    transform: translateX(5px);
    transition: font-size .2s ease .2s, transform .2s ease .2s;
    vertical-align: middle;
    font-size: 22px;
}
</style>
<style>.github-corner:hover .octo-arm{animation:octocat-wave 560ms ease-in-out}@keyframes octocat-wave{0%,100%{transform:rotate(0)}20%,60%{transform:rotate(-25deg)}40%,80%{transform:rotate(10deg)}}@media (max-width:500px){.github-corner:hover .octo-arm{animation:none}.github-corner .octo-arm{animation:octocat-wave 560ms ease-in-out}}

.move {
  animation:bounce-in 3s linear 2s infinite;
}

@keyframes bounce-in {
  0% {
    transform: 0px;
    opacity: 0;
    color:#fff;
  }
  100% {
    transform: translateX(120px);
    opacity: 1;
    color: #b5c34a;
  }
 
}

</style>



<script>
import { pinyin } from 'pinyin-pro';
import ciku from './ciku';
import _ from 'underscore';
import printJS from 'print-js';
import cibiao from './cibiao';
export default {
    computed:{
        height(){
            return window.innerHeight;
        }
    },
    data(){
        const logo = '看拼音写词语';
        return {
            spinShow:false,
            currentCiku:{},
            logo:pinyin(logo,{type:'array'}).map((e,i)=>{
                return {
                    pinyin:e,
                    hanzi:logo[i]
                };
            }),
            formItem:{
                checkWords:'',
                learntWords:'',
                total:48,
                title:'看拼音写词语',
                subTitle:'☆☆☆'
            },
            ruleValidate: {
                title:{ required: true, message: '标题不能为空', trigger: 'blur' },
                checkWords: [
                    { required: true, message: '请添加考察汉字', trigger: 'blur' }
                ],
                learntWords: [
                    { required: true, message: '已学汉字不能为空', trigger: 'blur' },
                    
                ],
            },
            printArray:[],
            cibiao:JSON.parse(JSON.stringify(cibiao)),
            checkCi:JSON.parse(JSON.stringify(cibiao)),
        };
    },
    methods:{
        remove(i){
            const word = this.printArray.splice(i,1)[0];
            word.show=false;
            for(let i = 0 ; i < word.word.length;i++){
                for(let j = 0; j < this.currentCiku.length; j++){
                    if(this.currentCiku[j].letter==word.word[i]){
                        this.currentCiku[j].arr.push(word);
                        return;
                    }
                }
            }
        },
        select(nodes){
            let letter='';
            nodes.forEach(e=>{
                letter=letter+(e.data?e.data:'');
            });
            this.formItem.learntWords=letter;
        },
        selectCi(nodes){
            let letter='';
            nodes.forEach(e=>{
                letter=letter+(e.data?e.data:'');
            });
            this.formItem.checkWords=letter;
        },
        add(letter,word,i){
            const o = [];
            for(let i = 0 ; i < word.word.length; i ++){
                const pinyinArr=pinyin(word.word[i],{ toneType: 'num', type: 'array',multiple:true });
                if(pinyinArr.length==1){
                    o.push({letter:word.word[i],pinyin:pinyin(word.word[i])});
                }else{
                    o.push({letter:word.word[i],pinyin:pinyin(word.word[i],{type:'array',multiple:true})[pinyinArr.findIndex(
                        e=>e.replace('0','').replace('ü','v')==word.pinyin[i]
                    )]});    
                }
                               
            }
            word.print=o;
            this.printArray.push(word);
            letter.arr.splice(i,1);
            this.formItem.total++;
        },
        generate(){
            this.$refs['form'].validate((valid) => {
                this.spinShow=true;
                if (valid) {
                    const tempObject={};
                    ciku.filter(e=>{
                        const all = this.formItem.checkWords+this.formItem.learntWords;
                        for(let i = 0 ; i < e.word.length; i ++){
                            if(all.search(e.word[i])==-1){
                                return false;
                            }
                        }
                        for(let i =0 ; i < this.formItem.checkWords.length; i++){
                            if(e.word.search(this.formItem.checkWords[i])!=-1){
                                return true;
                            }
                        }
                        return false;
                    }).forEach(e=>{
                        for(let i = 0 ; i < this.formItem.checkWords.length;i++){
                            if(e.word.search(this.formItem.checkWords[i]) >=0){
                                if(!tempObject[this.formItem.checkWords[i]]){
                                    tempObject[this.formItem.checkWords[i]]=[];
                                }
                                tempObject[this.formItem.checkWords[i]].push(e);
                                return;
                            }
                        }
                    });
                    let tempArray=[];
                    for(let a in tempObject){
                        tempArray.push({letter:a,arr:_.shuffle(tempObject[a])});
                    }
                    
                    const printed = [];
                    this.currentCiku=[];
                    while(printed.length < this.formItem.total && tempArray.length > 0){
                        const copy = _.shuffle(tempArray);
                        tempArray = copy;
                        while(copy.length > 0 && printed.length < this.formItem.total){
                            const currentLetter = copy.shift();
                            const currentWord = currentLetter.arr.shift();
                            const o = [];
                            for(let i = 0 ; i < currentWord.word.length; i ++){
                                const pinyinArr=pinyin(currentWord.word[i],{ toneType: 'num', type: 'array',multiple:true });
                                if(pinyinArr.length==1){
                                    o.push({letter:currentWord.word[i],pinyin:pinyin(currentWord.word[i])});
                                }else{
                                    o.push({letter:currentWord.word[i],pinyin:pinyin(currentWord.word[i],{type:'array',multiple:true})[pinyinArr.findIndex(e=>e.replace('0','').replace('ü','v')==currentWord.pinyin[i])]});    
                                }
                               
                            }
                            currentWord.print=o;
                            printed.push(currentWord);
                            if(currentLetter.arr.length>0){
                                tempArray.push(currentLetter);
                            }else{
                                this.currentCiku.push(currentLetter);
                            }
                        } 
                    }
                    this.printArray=printed;
                    this.currentCiku.push(...tempArray);
                    this.spinShow=false;
                }
            });
        },
        print(){      
            printJS({ printable: this.$refs['paper'].$el, type: 'html', scanStyles: false,style:'.noprint{display:none}'});
            
        }
    }
};
</script>
