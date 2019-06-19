<template>
	<view class="container">
		<!--内容区  30%-->
		<view class="content">

			<view class="str">
				{{str}}
			</view>
			<view class="result">
				<text>result:</text><text>{{result}}</text>
			</view>

		</view>
		<!--操作按钮去 70%-->
		<view class="box">
			<view class="row">
				<view class="one wide" @tap="clear()">Clear</view>
				<view class="one" @tap="dataClick('-')">-</view>
				<view class="one" @tap="rollback()">
					<image src="../../static/del.png"></image>
				</view>
			</view>
			<view class="row">
				<view class="one" @tap="dataClick('1')">1</view>
				<view class="one" @tap="dataClick('2')">2</view>
				<view class="one" @tap="dataClick('3')">3</view>
				<view class="one" @tap="operationClick('+')">
					<image src="../../static/add.png"></image>
				</view>
			</view>
			<view class="row">
				<view class="one" @tap="dataClick('4')">4</view>
				<view class="one" @tap="dataClick('5')">5</view>
				<view class="one" @tap="dataClick('6')">6</view>
				<view class="one" @tap="operationClick('-')">
					<image src="../../static/sub.png"></image>
				</view>
			</view>
			<view class="row">
				<view class="one" @tap="dataClick('7')">7</view>
				<view class="one" @tap="dataClick('8')">8</view>
				<view class="one" @tap="dataClick('9')">9</view>
				<view class="one" @tap="operationClick('*')">
					<image src="../../static/mult.png"></image>
				</view>
			</view>
			<view class="row">
				<view class="one wide" @tap="dataClick('0')">0</view>
				<view class="one" @tap="dataClick('.')">.</view>
				<view class="one" @tap="operationClick('/')">
					<image src="../../static/divi.png"></image>
				</view>
			</view>
			<view class="row">
				<view class="one" @tap="operationClick('(')">(</view>
				<view class="one" @tap="operationClick(')')">)</view>
				<view class="one wide" @tap="calculation()">
					<image src="../../static/res.png"></image>
				</view>
			</view>
		</view>
	</view>
</template>
<style>
	@import url("../../common/app.css");

	.container {
		display: flex;
		justify-content: center;
		align-items: center;
		height: 90vh;
	}

	image {
		width: 60vw;
		height: 55vw;
	}

	.content {
		position: absolute;
		width: 100vw;
		height: 30vh;
		top: 0;
		left: 0;
		box-sizing: border-box;
		font-weight: bold;
		padding: 10px;
	}

	.content .str {
		font-size: 20px;
		color: #1e75aa;
	}

	.content .result {
		font-size: 28px;
		position: absolute;
		bottom: 0;
		width: 98vw;
		right: 1vw;
		display: flex;
		justify-content: space-between;
		align-items: center;
		color: #5696BD;
		border-top: 1px dashed #5696bd;
	}

	.box {
		position: absolute;
		width: 100vw;
		height: 60vh;
		bottom: 0;
		left: 0;
		box-sizing: border-box;
	}

	.box .row {
		display: flex;
	}

	.box .row .one {
		width: 25vw;
		height: 10vh;
		box-sizing: border-box;
		box-shadow: 1px 1px 5px 5px #8fcbef;
		background: -webkit-linear-gradient(bottom, #8fcbef, #5696bd);
		color: white;
		font-size: 30px;
		font-weight: bold;
		display: flex;
		justify-content: center;
		align-items: center;
	}

	.box .row .one:active {
		background: #5696bd !important;
	}

	.box .row .one image {
		width: 8vw;
		height: 8vw;
	}

	.box .row .wide {
		width: 50vw;
	}
</style>
<script>
	export default {
		data() {
			return {
				str: "",
				result: "0.00",
				flag: true, //最后一个字符是操作数还是操作符 true 是操作符,,初始化必须为true 只能添（）数字
			}
		},
		methods: {
			dataClick: function(v) {

				if (v == '-' && !this.flag) {
					//如果这个操作数是符号需要判断前一个是操作数还是操作符
					//如果不是操作符或者括号 变成 - 好 做减法操作
					if(this.str.charAt(this.str.length - 1)==' ( '|| this.str.charAt(this.str.length - 1)==' ) '){
						//是括号
						this.str += v;
					}else{
						this.operationClick('-');
					}
					return false;
				}

				this.str += v;
				this.flag = false;
			},
			operationClick: function(v) {
				if(v=='(' || v == ')'){
					this.str += ' ' + v + ' '; //操作符前后加空格
					this.flag = false;//括号不算是操作符概念 后面可以跟 算数运算符
				}else if(!this.flag){
					this.str += ' ' + v + ' '; //操作符前后加空格
					this.flag = true;
				}
			},
			clear: function() {
				this.str = '';
				this.result = '0.00';
				this.flag=true;
			},
			rollback: function() {
				//如果是操作符回滚 3个大小字符
				//如果是操作数 回滚 1个大小字符
				if (this.flag) {
					this.str = this.str.slice(0, -3);
					//删除了一个操作符，前面必定是操作数
					this.flag = false;
				} else {
					this.str = this.str.slice(0, -1);

					//如果是一个负号，吧负号也删除了_因为能进来表示就是操作数 前面删除操作数之后，
					//若前面是操作符，绝对是一个空格  或者是负号
					//顺道删除负号——前面就绝对是操作符
					if (this.str.charAt(this.str.length - 1) == '-') {
						this.str = this.str.slice(0, -1);
					}
					//判断最后一个是不是数字
					if (!/[0-9.]/.test(this.str.charAt(this.str.length - 1))) {
						this.flag = true;
					}

				}
			},
			calculation: function() {
				//计算结果
				//可以有小数但是不能有负数出现在表达式中、、
				let regex = /\s+/; //空格分隔
				let str = this.str;
				let strarr = str.split(regex);
				console.log("中缀表达式数组（含括号）:", strarr);
				//遍历数组——转后缀表达式
				// 操作符 数组
				const opArr = ['+', '-', '*', '/', '(', ')'];
				//数据栈，储存后缀表达式结果
				const dataStack = new Array();
				//操作符栈
				const signStack = new Array();
				strarr.forEach((str, index) => {
					if (!opArr.includes(str)) {
						//直接入栈
						dataStack.push(str);
					} else {
						//操作符入栈出栈计算
						mtaCalculate(str);
					}
				})
				//最后将操作符栈 剩余元素一次出栈
				while (signStack.length > 0) {
					dataStack.push(signStack.pop());
				}
				//完成直接输出结果
				console.log("中缀（含括号）转后缀结果:", dataStack);
			
				//中转后操作符入栈出栈计算
				function mtaCalculate(str) {
			
					if (str != '(' && str != ')') { //不是括号
			
						while (mtaGetSignPrior(str) <= mtaGetSignPrior(signStack[signStack.length - 1])) {
			
							dataStack.push(signStack.pop());
			
							if (signStack.length <= 0) {
								break;
							}
						}
						signStack.push(str); //将这个符号入栈
			
					} else if (str == ')') {
						//遇到右括号，直接弹出，直到遇到左括号
						while (signStack[signStack.length - 1] != '(') {
			
							dataStack.push(signStack.pop());
						}
			
						//遇到左括号且之间的值也全部弹出，最后弹出左括号
						signStack.pop();
			
					} else {
						//左括号，入栈
						signStack.push(str);
					}
				}
				//获取操作符的优先级
				function mtaGetSignPrior(str) {
					switch (str) {
						case '+':
						case '-':
							return 1;
						case '*':
						case '/':
							return 2;
						default:
							return 0;
					}
				}
			
			
				/**
				 * 后缀表达式计算结果
				 *
				 * dataStack 数组 储存后缀表达式
				 */
			
				//中间数据栈，储存扫描后缀表达式入栈数据
				let middleDataStack = new Array();
				let result = ''; //计算结果
			
				dataStack.forEach((data, index) => {
					if (!opArr.includes(data)) {
						middleDataStack.push(data);
					} else {
						//遇到了操作符，直接出栈计算
						let r = middleDataStack.pop();
						let l = middleDataStack.pop();
			
						let res = 0;
			
						switch (data) {
							case '+':
								res = parseFloat(l) + parseFloat(r);
								break;
							case '-':
								res = parseFloat(l) - parseFloat(r);
								break;
							case '*':
								res = parseFloat(l) * parseFloat(r);
								break;
							case '/':
								res = parseFloat(l) / parseFloat(r);
								break;
			
							default:
			
								return "数据格式错误！";
						}
			
						//结果入栈
						middleDataStack.push(res);
					}
				})
			
				result = middleDataStack.pop();
				
				this.result = isNaN(result) ? '数据格式错误' : result;
			},
		}
	}
</script>

<style>

</style>
