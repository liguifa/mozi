<template>
	<div class="onshare-folder" @click="open">
		<img src="../assets/folder.png" />
		<span>{{title}}</span>
		<Contextmenu>
			<!-- <MenuItem title="下载" @menu="download" /> -->
			<MenuItem title="重命名" @menu="rename" />
		　　<MenuItem title="删除" @menu="removeDoc" />
		</Contextmenu>
	</div>
</template>

<script>
import Contextmenu from "../components/Contenxtmenu";
import MenuItem from "../components/MenuItem";

export default {
	props:{
		id:{
			type:Number,
			require:true
		},
		title:{
			type:String,
			require:true
		}
	},
	data(){
		return {
			newname:""
		}
	},
	methods:{
		open(){
			this.$router.push(`/editer/${this.id}`)
		},
		download(){
			location.href=`http://127.0.0.1:3001/api/export?id=${this.id}`;
		},
		removeDoc(){
			this.$Modal.confirm({
				title:"警告",
				content:"删除之后不可恢复，你确认要删除吗？",
				onOk:async ()=>{
					let result = await this.http.post(`/document/delete`,{id:this.id});
					if(result.isSuccess){
						this.$store.dispatch("getDocuments");
					}
				}
			});
		},
		rename(){
			let self = this;
			this.$Modal.info({
				title:"重命名",
				render(h){
					return	h('Input', {
                                props: {
									placeholder: '请输入新名称',
								},
								on:{
									"on-change":(event)=>{
										self.newname = event.target.value;
									}
								}
                            });
				},
				async onOk(){
					let result = await this.http.post("/document/rename",{id:self.id,name:self.newname});
					if(result.isSuccess){
						self.$store.dispatch("getDocuments");
					}
				}
			})
		}
	},
	components:{
		Contextmenu,
		MenuItem
	}
}
</script>

<style>
	.onshare-folder{
		width: 100px;
		height:120px;
		border-radius: 5px;
		/* background: url('http://memonotepad.com/img/paperfibers.png'); */
		/* background: #ffffff; */
    	background-size: auto;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
		overflow: hidden;
		text-overflow: ellipsis;
		cursor: pointer;
		user-select: none;
		position: relative;
	}

	.onshare-folder:hover{
		background-color: #bbbbbb;
	}

	.onshare-folder:hover > span{
		color: #ffffff;
	}

	.onshare-folder > span{
		white-space: nowrap;
		text-overflow: ellipsis;
		overflow: hidden;
		margin-top: 10px;
		width: 90%;
		text-align: center;
	}

	@media screen and (max-width:992px) {
		.onshare-folder{
			width: calc(100% - 15px);
			background: #fff;
    		box-shadow: 0 2px 8px 0 rgba(0,0,0,0.1);
    		border-radius: 2px;
		}
	}
</style>
