<template>
	<div class="cartcontrol">
		<transition name="move">
			<div class="cart-decrease" v-show="food.count>0" @click="decreaseCart">
				<span class="inner icon-remove_circle_outline"></span>
			</div>
		</transition>
		<div class="cart-count" v-show="food.count>0">{{food.count}}</div><div class="cart-add icon-add_circle" @click="addCart"></div>
	</div>
</template>
<script>
	import Vue from 'vue';
	export default{
		props:{
			food:{
				type: Object
			}
		},
		created(){

		},
		methods:{
			addCart(event){
				if(!event._constructed){
					return;
				}
				if(!this.food.count){
					Vue.set(this.food,'count',1);
				}else{
					this.food.count++;
				}
				this.$emit('cart.add', event.target);
			},
			decreaseCart(event){
				if(!event._constructed){
					return;
				}
				if(this.food.count){
					this.food.count--;
				}
			}
		}
	};
</script>
<style>
	.cartcontrol{
		font-size: 0;
	}
	.cartcontrol .cart-decrease{
		display: inline-block;		
		padding: 6px;		
	}
	.cartcontrol .cart-decrease .inner{
		display: inline-block;
		font-size: 24px;
		line-height: 24px;
		color: rgb(0,160,220);
	}
	.cartcontrol .cart-count{
		display: inline-block;
		vertical-align: top;
		width: 12px;
		padding-top: 6px;
		line-height: 24px;
		text-align: center;
		font-size: 10px;
		color: rgb(147,153,159);
	}
	.cartcontrol .cart-add{
		display: inline-block;
		font-size: 24px;
		line-height: 24px;
		padding: 6px;
		color: rgb(0,160,220);
	}
	.move-enter-active {
	  transition: all .4s linear;
	}
	.move-leave-active {
	  transition: all .4s linear;
	}
	.move-enter-active,.move-leave-active .inner{
		transition: all .4s linear;
		transform: rotate(0);
	}	
	.move-enter, .move-leave-to
	/* .slide-fade-leave-active for below version 2.1.8 */ {
	  transform: translate3d(24px,0,0);
	  opacity: 0;
	}
	.move-enter,.move-leave-to .inner{
		transform: rotate(180deg);
	}
</style>
