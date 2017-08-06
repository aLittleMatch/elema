<template>
	<div class="goods">
		<div class="menu-wrapper" ref="menuWrapper">
			<ul>
				<li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex===index}" @click="selectMenu(index,$event)">
					<span class="text">
						<span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
					</span>
				</li>
			</ul>
		</div>
		<div class="foods-wrapper" ref="foodsWrapper">
			<ul>
				<li v-for="item in goods" class="food-list food-list-hook">
					<h1 class="title">{{item.name}}</h1>
					<ul>
						<li v-for="food in item.foods" class="food-item">
							<div class="icon">
								<img width="57" height="57" :src="food.icon" alt="">
							</div>
							<div class="content">
								<h2 class="name">{{food.name}}</h2>
								<p class="desc">{{food.description}}</p>
								<div class="extra">
									<span class="count">月售{{food.sellCount}}份</span>
									<span>好评率{{food.rating}}%</span>
								</div>
								<div class="price">
									<span class="now">¥{{food.price}}</span>
									<span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
								</div>
								<div class="cartcontrol-wrapper">
									<cartcontrol :food="food" :on="cart.add"></cartcontrol>
								</div>
							</div>

						</li>
					</ul>
				</li>
			</ul>
		</div>
		<shopcart :select-foods="selectFoods" :delivery-price='seller.deliveryPrice' :min-price='seller.minPrice'></shopcart>
	</div>
</template>

<script>
	import BScroll from "better-scroll";
	import shopcart from '../shopcart/shopcart.vue';
	import cartcontrol from '../cartcontrol/cartcontrol.vue';
	const ERR_OK = 0;

	export default {
		props:['seller'],
		data(){
			return {
				goods:[],
				listHeight:[],
				scrollY:0
			}
		},
		computed: {
			currentIndex() {
				for(let i=0;i<this.listHeight.length;i++){
					let height1 = this.listHeight[i];
					let height2 = this.listHeight[i+1];
					if(!height2 || (this.scrollY >= height1 && this.scrollY < height2)){
						return i;
					}
				}
				return 0;
			},
			selectFoods(){
				let foods = [];
				this.goods.forEach((good) => {
					good.foods.forEach((food) => {
						if(food.count){
							foods.push(food);
						}
					});
				});
				return foods;
			}
		},
		created(){			
  			this.classMap = ['decrease','discount','guarantee','invoice','special'],
			this.$http.get('/api/goods').then((res)=>{
				res = res.body;
				if(res.errno === ERR_OK){
					this.goods = res.data;
					this.$nextTick(() => {
						this._initScroll();
						this._calculateHeight();
					});
					
				}
			})
		},
		methods:{
			selectMenu(index, event){
				if(!event._constructed){
					return;
				}
				let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
				let el = foodList[index];
				this.foodsScroll.scrollToElement(el, 300);
			},
			_initScroll(){
				this.menuScroll = new BScroll(this.$refs.menuWrapper,{
					click: true
				});
				this.foodsScroll = new BScroll(this.$refs.foodsWrapper,{
					click: true,
					probeType: 3
				});
				this.foodsScroll.on('scroll', (pos) => {
					this.scrollY = Math.abs(Math.round(pos.y));
				})
			},
			_calculateHeight(){
				let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
				let height = 0;
				this.listHeight.push(height);
				for(let i=0;i<foodList.length;i++){
					let item = foodList[i];
					height += item.clientHeight;
					this.listHeight.push(height);
				}
			}
		},
		components:{
			shopcart,
			cartcontrol
		}
	};
</script>

<style>
	.goods{
		display: flex;
		position: absolute;
		width: 100%;
		top: 174px;
		bottom: 46px;
		overflow: hidden;
	}
	.goods .menu-wrapper{
		flex: 0 0 80px;
		width: 80px;
		background: #f3f5f7;
	}
	.goods .menu-wrapper .menu-item{
		display: table;
		height: 54px;
		width: 56px;
		line-height: 14px;
		padding:0 12px;
	}
	.goods .menu-wrapper .menu-item.current{
		position: relative;
		z-index: 10;
		margin-top: -1px;
		background: #fff;
		font-weight: 700;
	}
	.goods .menu-wrapper .menu-item.current .text{
		border-bottom: none;
	}
	.goods .menu-wrapper .menu-item .icon{
		display: inline-block;
		vertical-align: top;
		width: 12px;
		height: 12px;
		margin-right:2px;
		background-size: 12px 12px;
		background-repeat: no-repeat;
	}
	@media (-webkit-min-device-pixel-ratio:3),(min-device-pixel-ratio:3){
		.goods .menu-wrapper .menu-item .icon.decrease{
			background-image: url(decrease_3@3x.png);			
		}
	}
	@media (-webkit-min-device-pixel-ratio:2),(min-device-pixel-ratio:2){
		.goods .menu-wrapper .menu-item .icon.decrease{
			background-image: url(decrease_3@2x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:3),(min-device-pixel-ratio:3){
		.goods .menu-wrapper .menu-item .icon.discount{
			background-image: url(discount_3@3x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:2),(min-device-pixel-ratio:2){
		.goods .menu-wrapper .menu-item .icon.discount{
			background-image: url(discount_3@2x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:3),(min-device-pixel-ratio:3){
		.goods .menu-wrapper .menu-item .icon.guarantee{
			background-image: url(guarantee_3@3x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:2),(min-device-pixel-ratio:2){
		.goods .menu-wrapper .menu-item .icon.guarantee{
			background-image: url(guarantee_3@2x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:3),(min-device-pixel-ratio:3){
		.goods .menu-wrapper .menu-item .icon.invoice{
			background-image: url(invoice_3@3x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:2),(min-device-pixel-ratio:2){
		.goods .menu-wrapper .menu-item .icon.invoice{
			background-image: url(invoice_3@2x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:3),(min-device-pixel-ratio:3){
		.goods .menu-wrapper .menu-item .icon.special{
			background-image: url(special_3@3x.png);
		}
	}
	@media (-webkit-min-device-pixel-ratio:2),(min-device-pixel-ratio:2){
		.goods .menu-wrapper .menu-item .icon.special{
			background-image: url(special_3@2x.png);
		}
	}
	.goods .menu-wrapper .menu-item .text{
		display: table-cell;
		width: 56px;
		vertical-align: middle;
		font-size: 12px;
		border-bottom: 1px solid rgba(7,17,27,0.1);
	}
	.goods .foods-wrapper{
		flex: 1;
	}
	.goods .foods-wrapper .title{
		padding-left: 14px;
		height: 26px;
		line-height: 26px;
		border-left: 2px solid #d9dde;
		font-size: 12px;
		color: rgb(147,153,159);
		background-color: #f3f5f7;
	}
	.goods .foods-wrapper .food-item{
		display: flex;
		padding: 18px;
		border-bottom: 1px solid rgba(7,17,27,0.1);
		position: relative;
	}
	.goods .foods-wrapper .food-item:last-child{
		border-bottom: none;
		margin-bottom: 0;
	}
	.goods .foods-wrapper .food-item .icon{
		flex: 0 0 57px;
		margin-right: 10px;
	}
	.goods .foods-wrapper .food-item .content{
		flex: 1;
	}
	.goods .foods-wrapper .food-item .content .name{
		margin: 2px 0 8px 0;
		height: 14px;
		line-height: 14px;
		font-size: 14px;
		color: rgb(7,17,27);
	}
	.goods .foods-wrapper .food-item .content .desc{
		margin-bottom: 8px;
		line-height: 12px;
		font-size: 10px;
		color: rgb(147,153,159);
	}
	.goods .foods-wrapper .food-item .content .extra{
		line-height: 10px;
		font-size: 10px;
		color: rgb(147,153,159);
	}
	.goods .foods-wrapper .food-item .content .extra .count{
		margin-right: 12px;
	}
	.goods .foods-wrapper .food-item .content .price{
		font-weight: 700;
		line-height: 24px;
	}
	.goods .foods-wrapper .food-item .content .cartcontrol-wrapper{
		position: absolute;
		right: 0;
		bottom: 12px;
	}
	.goods .foods-wrapper .food-item .content .price .now{
		margin-right: 8px;
		font-size: 14px;
		color: rgb(240,20,20);
	}
	.goods .foods-wrapper .food-item .content .price .old{
		text-decoration: line-through;
		font-size: 10px;
		color: rgb(147,153,159);
	}
</style>