<template>
	<div ref='page'  class="lt-full zmiti-index-main-ui "   :class="{'show':show}" >
		


		<div class="zmiti-title" v-if='false' :class="{'hide':!showOthers}">
			<img :src="imgs.title" alt="">

			<div class="zmiti-logos">
				<div>
					<img :src="imgs.logo" alt="" class="zmiti-logo">
					<span>新华社新媒体中心出品</span>
				</div>
				<div>
					<span>合作单位:</span>
					<img :src="imgs.logo1" alt=""  class="zmiti-logo1">
					<img :src="imgs.logo2" alt="" class="zmiti-logo2">
				</div>
			</div>
		</div>

		<canvas  ref='canvas'></canvas>
		
		
	</div>
</template>

<script>
	import './index.css';
	import {imgs} from '../lib/assets.js';
	import {zmitiUtil} from '../lib/util';
	import Point from './point';
	export default {
		props:['obserable','nickname','pv'],
		name:'zmitiindex',
		data(){
			return{
				imgs,
				pointW:0,
				showSubmit:true,
				transY:0,
				pointH:0,
				points:[],
				stars:[],
				showStartBtn:false,
				index:-1,
				showOthers:true,
				showRemark:false,
				organizationArr:window.organizationArr,
				showLight:false,
				starting:false,
				planeClass:'',
				show:true,
				maxHeight:80,
				showjiasu:false,
				showIndexMask:false,
				viewW:Math.min(window.innerWidth,750),
				viewH:window.innerHeight
			}
		},
		components:{
		},
		
		methods:{

			imgStart(e){
				e.preventDefault(); 
			},
			setSize(){
				var canvas = this.$refs['canvas'];
				canvas.width = this.viewW;
				canvas.height = this.viewH;
			
				return canvas;
			},

			addStar(){
				for(var i = 0; i<100 ; i++){
					var aStar = {
						x:Math.round(Math.random()*this.viewW),
						y:Math.round(Math.random()*this.viewH),
						r:Math.random()*5,
						ra:Math.random()*0.05,
						alpha:Math.random(),
						vx:Math.random()*0.2-0.1,
						vy:Math.random()*0.2-0.1
					}
					this.stars.push(aStar);
				}
			},
			
			initCanvas(){//
				var canvas = this.setSize();
				var context = canvas.getContext('2d');
				this.addStar();
				var rnd = 0;
				
				var zmitiAnimationFrame = window.requestAnimationFrame ||  window.webkitRequestAnimationFrame;
				var width = canvas.width,
					height=  canvas.height;

				var WINDOW_WIDTH = this.viewH,
					WINDOW_HEIGHT = this.viewH;
				var render = ()=>{
					context.fillStyle = 'rgba(14,12,62,.5)';
					context.fillRect(0,0,WINDOW_WIDTH,WINDOW_HEIGHT);
					//context.clearRect(0,0,WINDOW_WIDTH,WINDOW_HEIGHT)
					for(var i =0; i<this.stars.length ; i++){
						var star = this.stars[i];
						if(i == rnd){
							star.vx = -5;
							star.vy = 4;
							context.beginPath();
							context.strokeStyle = 'rgba(255,255,255,'+star.alpha+')';
							context.lineWidth = star.r;
							context.moveTo(star.x,star.y);
							context.lineTo(star.x+star.vx,star.y+star.vy);
							//context.drawImage(img,star.x+star.vx,star.y+star.vy);
							context.stroke();
							context.closePath();
						}
						star.alpha += star.ra;
						if(star.alpha<=0){
							star.alpha = 0;
							star.ra = -star.ra;
							star.vx = Math.random()*0.2-0.1;
							star.vy = Math.random()*0.2-0.1;
						}else if(star.alpha>1){
							star.alpha = 1;
							star.ra = -star.ra
						}
						star.x += star.vx;
						if(star.x>=WINDOW_WIDTH){
							star.x = 0;
						}else if(star.x<0){
							star.x = WINDOW_WIDTH;
							star.vx = Math.random()*0.2-0.1;
							star.vy = Math.random()*0.2-0.1;
						}
						star.y += star.vy;
						if(star.y>=WINDOW_HEIGHT){
							star.y = 0;
							star.vy = Math.random()*0.2-0.1;
							star.vx = Math.random()*0.2-0.1;
						}else if(star.y<0){
							star.y = WINDOW_HEIGHT;
						}
						context.beginPath();
						var bg = context.createRadialGradient(star.x, star.y, 0, star.x, star.y, star.r);
						bg.addColorStop(0,'rgba(255,255,255,'+.5+')')
						bg.addColorStop(1,'rgba(255,255,255,.1)')
						context.fillStyle  = bg;
						context.arc(star.x,star.y, star.r, 0, Math.PI*2, true);
						context.fill();
						context.closePath();

					}
					zmitiAnimationFrame(render);
				}
				render();
			},
			
		},
		mounted(){

			var {obserable} = this;

			obserable.on('toggleIndex',(data)=>{
				this.show = data.show;
			})
			this.initCanvas();

		}
	}
</script>