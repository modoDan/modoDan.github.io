<template>
	<div class="date-picker__time-header" ref="datePicker">
		<div class="date-event active">
			<input type="text" v-show="options.text" class="calender-input" v-model="dateText" @click="openDate($event)">
		</div>
		<div class="date-pos" ref="datePos" v-clickoutside="() => showMenu = false">
			<template v-if="showMenu">
				<!-- <full-date 
				:dateType="options.dateType" 
				:events="options.events" 
				:compareTime="options.compareTime" 
				:styles="options.styles"
				:initOptions="initOptions"
				@new-initOptions="newinitOptions"
				></full-date> -->
				<full-date 
				v-bind="$attrs"
				v-model="initOptions"
				></full-date>
			</template>
		</div>
	</div>
</template>

<script>
	// import clickoutside from 'element-ui/src/utils/clickoutside';
	import clickoutside from '../utils/clickoutside';
	import fullDate from './fullDate'
	import moment from 'moment'
	export default {
		name: 'my-calender',
		data: function() {
			return {
				showMenu: false,
				initOptions:{
					curnow: new Date(),
					initnow: new Date(),
					inittype: false
				}
			}
		},
		directives: {
			clickoutside
		},
		watch:{
			curNow(val,oldVal){
				console.log(val)
				if(this.options.events) {//自定义事件
					this.options.events(this.dateText)//传递点击的日历，以供外层需要传值
				}
			}
		},
		computed: {
			options(){
				return this.$attrs.options
			},
			curNow() {
				return this.initOptions.curnow
			},
			dateText() {
				let valueMoment = this.curNow
				if(this.options.dateType == 'year') { //年
					return moment(valueMoment).format('YYYY年')
				} else if(this.options.dateType == 'month') { //月份
					return moment(valueMoment).format('YYYY年M月')
				} else if(this.options.dateType == 'day') { //天
					return moment(valueMoment).format('YYYY年M月D日')
				} else if(this.options.dateType == 'quarter') { //季度
					let newM = moment(valueMoment).format('Q') * 3 - 3
					return moment(valueMoment).format('YYYY年第Q季度')
				} else if(this.options.dateType == 'week') { //周
					var s_week = moment(this.getRangeWeek[0]).format('M月D日')
					var e_week = moment(this.getRangeWeek[1]).format('M月D日')
					return s_week + '-' + e_week;
				}
			},
			getRangeWeek() {
				var startStop = []
				var currentDate = this.curNow;
				var week = moment(currentDate).format('E');
				var minusDay = week != 0 ? week - 1 : 6;
				var monday = new Date(currentDate.getFullYear(), currentDate.getMonth(), currentDate.getDate() - minusDay);
				var sunday = new Date(currentDate.getFullYear(), currentDate.getMonth(), currentDate.getDate() + (6 - minusDay));
				startStop.push(monday); //本周起始时间
				startStop.push(sunday); //本周终止时间
				return startStop;
			},
		},
		components: {
			fullDate
		},
		mounted() {
		},
		methods: {
			newinitOptions(msg){
				this.initOptions.curnow = msg.curnow
				this.initOptions.initnow = msg.initnow
				this.initOptions.inittype = msg.inittype
            },
			openDate: function(e) {
				this.showMenu = true
				var _this = this
				window.setTimeout(function() {
					var datePos = _this.$refs.datePos;
					var left = e.target.offsetLeft;
					var top = e.clientY;
					var maxHeight = document.getElementsByTagName("body")[0].offsetHeight + document.documentElement.scrollTop;
					var bodywidth = document.getElementsByTagName("body")[0].offsetWidth
					var targetHeight = datePos.offsetHeight;
					top = top + targetHeight > maxHeight ? maxHeight - targetHeight - 10 : top;
					datePos.style.left = (e.clientX - bodywidth/1920 * 150) + 'px';
					datePos.style.top = top + 'px'; // 指定创建的DIV在文档中距离顶部的位置
					datePos.style.position = 'absolute'; // 为新创建的DIV指定绝对定位
				},10)
				//重置日历组件数据
				if(!this.options.text) {
					this.initOptions.curnow = new Date()
					this.initOptions.initnow = new Date()
					this.initOptions.inittype = false
				}
			}
		}
	}
</script>
<style>
	.calender-input {
		background: #ececec;
		text-indent: 15px;
		height: 30px;
		max-width: 220px;
		border-radius: 15px;
		outline: none;
		border: none;
	}
	
	.calender-see {
		display: inline-block;
		width: 30px;
		height: 28px;
		margin: 0 7px;
		cursor: pointer;
		border-radius: 2px;
		background: #ececec;
		outline: none;
		border: none;
	}
	.date-picker__time-header{
		display:inline-block
	}
</style>