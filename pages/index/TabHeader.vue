<template>
    <tab-bar class="bar">
        <tab-bar-item path="/pages/mes/pro/index" activeColor="mediumturquoise">
            <template v-slot:item-text><div class="title-style">生产</div></template>
            <template v-slot:item-icon><img class="hengxian" :src="require('@/static/icons/png/pro.png')" alt=""></template>
            <template v-slot:item-icon-active><img class="hengxian" :src="require('@/static/icons/png/pro.png')" alt=""></template>
        </tab-bar-item>
        <tab-bar-item path="/pages/mes/gx/index" activeColor="mediumturquoise">
            <template v-slot:item-text><div class="title-style">工序</div></template>
            <template v-slot:item-icon><img class="hengxian" :src="require('@/static/icons/png/gx.png')" alt=""></template>
            <template v-slot:item-icon-active><img class="hengxian" :src="require('@/static/icons/png/gx.png')" alt=""></template>
        </tab-bar-item>
        <tab-bar-item path="/pages/mes/qc/index" activeColor="mediumturquoise">
            <template v-slot:item-text><div class="title-style">质量</div></template>
            <template v-slot:item-icon><img class="hengxian" :src="require('@/static/icons/png/qc.png')" alt=""></template>
            <template v-slot:item-icon-active><img class="hengxian" :src="require('@/static/icons/png/qc.png')" alt=""></template>
        </tab-bar-item>
        <tab-bar-item path="/pages/mes/dev/index" activeColor="mediumturquoise">
            <template v-slot:item-text><div class="title-style">设备</div></template>
            <template v-slot:item-icon><img class="hengxian" :src="require('@/static/icons/png/dev.png')" alt=""></template>
            <template v-slot:item-icon-active><img class="hengxian" :src="require('@/static/icons/png/dev.png')" alt=""></template>
        </tab-bar-item>
    </tab-bar>
</template>
 
<script>
    import TabBar from "@/components/TabBar/TabBar.vue";
    import TabBarItem from "@/components/TabBar/TabBarItem.vue";
 
    export default {
        name: "TabHeader",
        components: {
            TabBar,
            TabBarItem
        },
		data(){
			return {
				proImg: require('@/static/icons/png/pro.png')
			}
		}
    }
</script>
 
<style scoped>
    .title-style {
        font-weight: bold;
		margin: auto;
		text-align: center;
        cursor: pointer;
    }
    .title-style:hover {
        color: #48decc;
    }
 
    .hengxian {
		margin: auto;
		text-align: center;
        width: 30px;
        height: 30px;
    }
	
	.Hline{
	 	background-image: linear-gradient(to right, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.75), rgba(0, 0, 0, 0));
		width: 1px;/*设置宽高*/
		vertical-align: middle;
		height:80%;
		position:relative;/*调整位置*/
		left:150px;
		float:left;/*让此div与前面的并排显示*/
	}
	.style-two {	
		align: center;
		width: 80%;
		margin: 0 auto;
		border: 0;
		height: 1px;
		background-image: linear-gradient(to right, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.75), rgba(0, 0, 0, 0));
	}
</style>