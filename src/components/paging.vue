<template>
   <div id="paging" class="paging">
     <div class="left-set-row">
       <div>
         <span>共{{pageOption.pageNum}}页</span>
         <span style="margin-left: 0.6vw">每页</span>
         <div style="" class="row-select">
           <div style="display: inline-block;border-radius: 2px;font-size: 12px;border: 1px solid #DBDBDB;margin: 0 6px">
             <span style="display: inline-block;width: 2.5vw;padding: 4px">{{pageOption.now_num}}</span>
             <span style="display: inline-block;width: 1.5vw;border-left: 1px solid #DBDBDB;padding: 4px" @click="showSelectRowBox">
               <Icon :type="icon" style="color: #BDBDBD"/>
             </span>
           </div>
           <div class="row-select-list" :hidden="rowHide">
             <li v-for="item in rowList" :key="item" @click="selectRow(item)">{{item}}</li>
           </div>
         </div>
         <span>条</span>
         <span style="margin-left: 0.6vw">共{{pageOption.totalRow}}条记录</span>
       </div>
     </div>
     <div class="right-set-page">
       <div class="select-page">
         <li @click="previousPage"><Icon type="md-arrow-dropleft" style="color: #BDBDBD"/></li>

         <li v-for="(item, index) in pageOption.page_list" :key="index" :style="{'background': item.bgc, 'color': item.color}" @click="selecPage(index)">{{item.name}}</li>
         <li @click="Nextpage"><Icon type="md-arrow-dropright" style="color: #BDBDBD"/></li>
         <input type="number" style="width: 3vw;height: 3vh;margin-left: 0.4vw;border: 1px solid #EAEAEA" v-model="jumpPage">
         <li @click="jumpTopage" style="width: 2vw" >前往</li>
       </div>
     </div>
   </div>
</template>

<script>
    import ToolUtil from "../utils/tool";
    export default {
        name: 'paging',
        data(){
           return {
               rowHide: true,
               icon: 'md-arrow-dropdown',
               rowList: [1,2,3,4,5,6,7,8,9,10],
               pageIndex: 0,
               now_page: 1,
               jumpPage: '',
               toolUtil: new ToolUtil()
           }
        },
        props: {
            pageOption: {
                now_num: {
                    type: Number,
                    default: 0
                },
                now_page: {
                    type: Number,
                    default: 1
                },
                pageNum: {
                    type: Number,
                    default: 0
                },
                totalRow: {
                    type: Number,
                    default: 0
                },
                page_list: {
                    type: Array,
                    default: []
                }
            }
        },
        updated() {
            // console.log(this.pageOption);
            // this.pageOption.now_page = this.pageOption.now_page;
            // this.pageOption.now_num = this.pageOption.now_num;
            // this.pageOption.pageNum = this.pageOption.pageNum;
            // this.totalRow = this.pageOption.totalRow;

            // for (let i = 1; i<= this.pageOption.pageNum; i++) {
            //     this.pageOption.page_list.push({name: i, bgc: '#FFFFFF', color: '#6D6F71'})
            //     console.log(123);
            // }
            // if (this.pageOption.pageNum < this.pageOption.page_list.length){
            //     this.pageOption.page_list =  this.pageOption.page_list.slice(0 , this.pageOption.pageNum);
            // }
        },
        methods: {
            // 显示选择条数的弹窗
            showSelectRowBox(){
                if (  this.rowHide )  {
                    this.rowHide = false;
                    this.icon = 'md-arrow-dropup';
                }else {
                    this.rowHide = true;
                    this.icon = 'md-arrow-dropdown';
                }

            },
            selectRow(data) {
                this.rowHide = true;
                this.icon = 'md-arrow-dropdown';
                this.pageOption.now_num = data;
                this.pageOption.now_num = data;
                this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page, label: 'row'});
            },
            // 选择某一页
            selecPage(index){
                this.pageIndex = index;
                this.pageOption.page_list.forEach(val => {
                    val.bgc= '#FFFFFF';
                    val.color= '#6D6F71';
                });
                this.pageOption.page_list[index].bgc = '#A9B0B6';
                this.pageOption.page_list[index].color = '#EDEEEF';
                this.pageOption.now_page = this.pageOption.page_list[index].name;
                this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page,label: 'page'});
            },
            // 上一页
            previousPage(){
                // if ( this.pageOption.page_list[0].name === 1)
                if(this.pageIndex === 0) {
                    if( this.pageOption.page_list[0].name > 1){
                        this.pageOption.page_list.forEach(v => {
                            v.name -= 1;
                        });
                        this.pageOption.now_page =  this.pageOption.page_list[0].name;
                        this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page, label: 'page'});
                    }else {
                        this.toolUtil.toast('info', '当前已经是第一页')
                    }

                }else {
                    this.pageIndex -= 1;
                    this.pageOption.page_list.forEach(val => {
                        val.bgc= '#FFFFFF';
                        val.color= '#6D6F71';
                    });
                    this.pageOption.page_list[this.pageIndex].bgc = '#A9B0B6';
                    this.pageOption.page_list[this.pageIndex].color = '#EDEEEF';
                    this.pageOption.now_page = this.pageOption.page_list[this.pageIndex].name;
                    this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page, label: 'page'});
                }


            },
            // 下一页
            Nextpage(){
                if (this.pageIndex === this.pageOption.page_list.length -1) {

                    if(this.pageOption.pageNum > this.pageOption.page_list[this.pageOption.page_list.length -1].name){
                        this.pageOption.page_list.forEach(v => {
                            v.name += 1;
                        });
                        this.pageOption.now_page = this.pageOption.page_list[this.pageOption.page_list.length -1].name;
                        this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page, label: 'page'});
                    }else {
                        this.toolUtil.toast('info', '当前已经是最后一页')
                    }
                }else {
                    this.pageIndex += 1;
                    this.pageOption.page_list.forEach(val => {
                        val.bgc= '#FFFFFF';
                        val.color= '#6D6F71';
                    });
                    this.pageOption.page_list[this.pageIndex].bgc = '#A9B0B6';
                    this.pageOption.page_list[this.pageIndex].color = '#EDEEEF';
                    this.pageOption.now_page = this.pageOption.page_list[this.pageIndex].name;
                    this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page, label: 'page'});
                }


            },
            // 跳转到某一页
            jumpTopage(){
                if (this.jumpPage !== ''){
                    if (this.jumpPage <= this.pageOption.pageNum){
                        this.pageOption.now_page = Number(this.jumpPage);
                        if (this.pageOption.pageNum < 5 ){
                            this.pageOption.page_list.forEach( (v, index) => {
                                if (v.name === Number(this.jumpPage)){
                                    this.pageIndex = index;
                                    v.color = '#EDEEEF';
                                    v.bgc = '#A9B0B6';
                                }else {
                                    v.bgc= '#FFFFFF';
                                    v.color= '#6D6F71';
                                }
                            })
                        }else {
                            if (Number(this.jumpPage) > this.pageOption.page_list[4].name) {

                                this.pageOption.page_list.forEach(val => {
                                    val.bgc= '#FFFFFF';
                                    val.color= '#6D6F71';
                                });
                                this.pageIndex = 4;
                                // eslint-disable-next-line for-direction
                                for (let i = 4; i >= 0; i--){
                                    if (i === 4) {
                                        this.pageOption.page_list[i].color = '#EDEEEF';
                                        this.pageOption.page_list[i].bgc = '#A9B0B6';
                                        this.pageOption.page_list[i].name = Number(this.jumpPage);
                                    }else {
                                        this.pageOption.page_list[i].name = this.pageOption.page_list[i + 1].name - 1;
                                    }

                                }
                            }else if (Number(this.jumpPage) < this.pageOption.page_list[0].name) {

                                if (Number(this.jumpPage) > 5){
                                    for (let i = 4; i >= 0; i--){
                                        if (i === 4) {
                                            this.pageOption.page_list[i].color = '#EDEEEF';
                                            this.pageOption.page_list[i].bgc = '#A9B0B6';
                                            this.pageOption.page_list[i].name = Number(this.jumpPage);
                                        }else {
                                            this.pageOption.page_list[i].name = this.pageOption.page_list[i + 1].name - 1;
                                        }
                                    }
                                }else {
                                    for (let i = 4; i >= 0; i--){
                                        if (i === 4) {
                                            this.pageOption.page_list[i].name = 5;
                                        }else {
                                            this.pageOption.page_list[i].name = this.pageOption.page_list[i + 1].name - 1;
                                        }
                                    }
                                    this.pageOption.page_list.forEach( v => {
                                        if (v.name === Number(this.jumpPage)){
                                            v.color = '#EDEEEF';
                                            v.bgc = '#A9B0B6';
                                        }else {
                                            v.bgc= '#FFFFFF';
                                            v.color= '#6D6F71';
                                        }
                                    })
                                }
                            }else {
                                this.pageOption.page_list.forEach( v => {
                                    if (v.name === Number(this.jumpPage)){
                                        v.color = '#EDEEEF';
                                        v.bgc = '#A9B0B6';
                                    }else {
                                        v.bgc= '#FFFFFF';
                                        v.color= '#6D6F71';
                                    }
                                })
                            }
                        }
                        this.$emit('getPageDate', {num_Size: this.pageOption.now_num, nowPage: this.pageOption.now_page, label: 'page'});
                    }else {
                        this.toolUtil.toast('info', '您输入的页数不符合规则，超出当前数据最长页数')
                    }

                }else {
                    this.toolUtil.toast('info', '请输入需要跳转的页数')
                }

            }

        }
    }
</script>

<style scoped lang="scss">
  .paging {
    display: flex;
    justify-content: space-between;
    font-size: 12px;
    .left-set-row {
      //position: relative;
      padding: 2vh 1vw;

      .row-select{
        position: relative;
        display: inline-block;
        .row-select-list {
          position: absolute;
          list-style: none;
          top: -28vh;
          background: #fff;
          margin-left: 6px;
          width: 4.5vw;
          border-radius: 4px;
          //border: 1px solid #EAEAEA;
          box-shadow: 0 0 5px #2E3235;
          li {
            border-radius: 4px;
            padding: 4px 0;
          }
          li:first-child {
            padding-top: 4px;
          }
          li:hover {
            background: #CDE5FE;

          }
        }
      }


    }
    .right-set-page {
      padding: 1.8vh 1vw;
      .select-page {
        li {
          list-style: none;
          display: inline-block;
          width: 1.6vw;
          height: 3vh;
          line-height: 3vh;
          border: 1px solid #EAEAEA;
          margin-left: 4px;
        }
      }

    }
  }

</style>
