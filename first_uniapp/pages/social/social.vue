
<template>
	<view class="container">
		<view class="one">
			<view class="l">所在城市</view>
			<view class="r">
				 <view class="uni-input">{{citys[citysIndex].name}}</view>
			</view>
		</view>
		<view class="one">
			<view class="l">税前月收入</view>
			<view class="r">
			   <input  type="digit"  :value="salary.preTax" data-col="preTax" @input="salaryModify"/>
			</view>
		</view>
		<view class="one">
			<view class="l">专项扣除</view>
			<view class="r">
			   <input  type="digit" :value="salary.specialReduce"  data-col="specialReduce" @input="salaryModify"/>
			</view>
		</view>
	
		<view class="row bold">
			缴费比例
		</view>
		<view class="row">
			<text>养老:{{citys[citysIndex].insurance_base_age.b}}%</text>
			<text>医疗:{{citys[citysIndex].insurance_base_medical.b}}%</text>
			<text>失业:{{citys[citysIndex].insurance_base_work.b}}%</text>
			<view>公积金:<input :value="citys[citysIndex].insurance_base_fund.b" @input="insuranceBaseFundModify" maxlength="2"/>%</view>
		</view>
		<view class="row bold">
			缴费金额
		</view>
		<view class="one">
			<view class="l">养老</view>
			<view class="r">
			   ￥{{salary.insurance_age}}
			</view>
		</view>
		<view class="one">
			<view class="l">医疗</view>
			<view class="r">
			   ￥{{salary.insurance_medical}}
			</view>
		</view>
		<view class="one">
			<view class="l">失业</view>
			<view class="r">
			   ￥{{salary.insurance_work}}
			</view>
		</view>
		<view class="one">
			<view class="l">公积金</view>
			<view class="r">
			   ￥{{salary.insurance_fund}}
			</view>
		</view>
		<view class="one">
			<view class="l">个人所得税</view>
			<view class="r">
			   ￥{{salary.tax}}
			</view>
		</view>
		<view class="one">
			<view class="l">税后收入</view>
			<view class="r">
			   ￥{{salary.afterTax}}
			</view>
		</view>
	</view>
</template>

<style>
	page{
		background-color: #8fcbef;
	}
	.container{
		box-sizing: border-box;
		padding: 10px 5px;
		font-size: 16px;
		display: flex;
		justify-content: center;
		width: 100%;
		flex-direction: column;
	}
	.title{
		text-align: center;
	}
	.one{
		display: flex;
		justify-content: space-between;
		align-items: center;
		padding: 10px;
	}
	.bold{
		font-weight: bold;
	}
	input{
		text-align: center;
		background-color: white;
		width: 100px;
	}
	.row{
		padding: 10px 10px;
		display: flex;
		justify-content:flex-start;
		align-items: center;
	}
	.row text{
		margin-right: 10px;
	}
	.row  input{
		width:40px;
		margin: 0 5px;
	}
	.row *{
		display: inline-block;
		font-size: 16px;
		color: gray;
	}
	.row view{
		display: flex;
		align-items: center;
	}
    
</style>
<script>
	export default {
		data() {
			return {
				citysIndex:0,
				citys:[
					{
						name:"上海",
						insurance_base_age:{
							a:0.08,
							b:8
						},
						insurance_base_medical:{
							a:0.05,
							b:5
						},
						insurance_base_work:{
							a:0.002,
							b:0.2
						},
						insurance_base_fund:{
							a:0.12,
							b:12
						},
					},
					{
						name:"上海",
						insurance_base_age:8,
						insurance_base_medical:5,
						insurance_base_work:0.2,
						insurance_base_fund:12,
					}
				],
				tax:[5000,8000,17000,30000,40000,60000,85000],
				taxBase:[0,0.03,0.1,0.2,0.25,0.3,0.35,0.45],
				salary:{
					specialReduce:0,
					tax:0,
					preTax:0,
					afterTax:0,
					insurance_age:0,
					insurance_work:0,
					insurance_medical:0,
					insurance_fund:0,
				}
			}
		},
		methods: {
			cityPickerChange:function(e){
				 console.log('picker发送选择改变，携带值为', e.target.value);
				 
			},
			salaryModify:function(e){
				
				let col = e.currentTarget.dataset.col;
				let val = e.detail.value;
				this.$set(this.salary,col,val);
				// console.log(col,val,this.salary.preTax);
				
				this.calculate();
			},
			calculate:function(){
				
				if(this.salary.preTax == 0){
					return 0;
				}
				  //0.减去专项扣除
				  let s1 = this.salary.preTax - this.salary.specialReduce;
				  console.log(s1);
		
				 //1.计算社保
				 this.salary.insurance_age =s1*this.citys[this.citysIndex].insurance_base_age.a;
				 this.salary.insurance_work=s1*this.citys[this.citysIndex].insurance_base_work.a;
				 this.salary.insurance_medical=s1*this.citys[this.citysIndex].insurance_base_medical.a;
				 this.salary.insurance_fund=s1*this.citys[this.citysIndex].insurance_base_fund.a;
				 
				 //2.个人所得税
				 let s2 = s1 -  this.salary.insurance_age - this.salary.insurance_work -this.salary.insurance_medical-this.salary.insurance_fund;
			
				 let index = 0;
				 let temps = this.tax;
				 for(let i = 0;i<temps.length;i++){
					 console.log(s2,i,temps[i]);
					 if(s2 <= temps[i]){
					 		index = i;
					 		
							break;
					 }
				 }
				 let realtax = this.taxBase[index];
				 console.log(s2," 税率:",realtax);
				  this.salary.tax = s2*realtax;
				 //3。税后工资
				 this.salary.afterTax = s2 -  this.salary.tax;
			},
			insuranceBaseFundModify:function(e){
				this.citys[this.citysIndex].insurance_base_fund.b = e.detail.value;
				let temp = e.detail.value;
				if(temp.length == 1){
					this.citys[this.citysIndex].insurance_base_fund.a = "0.0"+temp;
				}else{
					this.citys[this.citysIndex].insurance_base_fund.a = "0."+temp;
				}
				this.calculate();
			},
		}
	}
</script>

<style>

</style>
