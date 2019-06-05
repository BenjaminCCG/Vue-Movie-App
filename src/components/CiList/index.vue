<template>
    <div class="cinema_body">
        <Loading v-if="isLoading" />
        <Scroller v-else>
        <ul>
            <li v-for='(item,index) in cinemas' :key='item.id'>
                <div>
                    <div class="head">
                        <h2>{{item.nm}}</h2>
                        <p><span class="price">{{item.sellPrice}}</span>元起</p>                       
                    </div>
                    <p>{{item.addr}}</p>
                    <p class="sign">                        
                        <span :class="isColor(obj[current])" v-for='(current,key) in signList[index]' :key='key'>{{obj[current]}}</span>
                        <!-- <span class="green">退</span>
                        <span class="yellow">折扣卡</span>
                        <span class="yellow">小吃</span> -->
                    </p>
                </div>
                <div>
                    {{item.distance}}
                </div>
            </li>
            
        </ul>
        </Scroller>
    </div>
</template>

<script>
export default {
    name : 'CiList',
    data(){
        return {
            cinemaList : [],
            isLoading : true,
            prevCityId : -1,
            cinemas:[],
            signList:[],
            obj:{
                allowRefund:'改签',
                endorse:'退',
                sell:'折扣卡',
                snack:'小吃'
            },
        };
    },
    activated(){
        this.axios.get('/api/cinemaList?cityId=10').then(res=>{
          let msg=res.data.msg;
          let cinemas=res.data.data.cinemas   
          cinemas.forEach(item=>{
              let obj=[]
              for(let i in item.tag){
                  if(item.tag[i]=='1'){
                      obj.push(i)
                  }
              }
              this.signList.push(obj)
          })     
          if(msg==='ok'){
              this.cinemas=cinemas ,
              this.isLoading=false             
          }
      })
    },
    methods: {
        isColor(msg){
            if(msg==='改签'||msg==='退'){
                return 'green'
            }else{
                return 'yellow'
            }
        }
    },
    filters : {
        formatCard(key){
            var card = [
                { key : 'allowRefund' , value : '改签' },
                { key : 'endorse' , value : '退' },
                { key : 'sell' , value : '折扣卡' },
                { key : 'snack' , value : '小吃'}
            ];
            for(var i=0;i<card.length;i++){
                if(card[i].key === key){
                    return card[i].value;
                }
            }
            return '';
        },
        classCard(key){
            var card = [
                { key : 'allowRefund' , value : 'bl' },
                { key : 'endorse' , value : 'bl' },
                { key : 'sell' , value : 'or' },
                { key : 'snack' , value : 'or'}
            ];
            for(var i=0;i<card.length;i++){
                if(card[i].key === key){
                    return card[i].value;
                }
            }
            return '';
        }
    }
}
</script>

<style lang='less' scoped>
    ul{
        padding: 0 1.2rem;
        li{
            padding: .8rem 0 .4rem;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px solid #e6e6e6; 
            font-size: .8rem;
            .head{
                display: flex;
                h2{
                    font-size: .8rem;
                }
                .price{
                    font-size: .8rem;
                    font-weight: 700;
                    margin: 0 .2rem;
                }
                p{
                    color: #f03d37
                    
                }
            }   
            &>div:first-child{
                line-height: 2rem;
                flex:1;
                .sign>span{
                    display: inline-block;
                    line-height: 1rem;
                    padding: .2rem;
                    vertical-align: middle;
                    margin-right: .5rem;
                }
                .green{                    
                    color: #589daf;
                    border: 1px solid #589daf;
                }
                .yellow{                    
                    color: #f90;
                    border: 1px solid #f90;
                }
            }      
            &>div:last-child{
                align-self: center;
            }
        }
    }
</style>
