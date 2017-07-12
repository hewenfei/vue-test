<template>
	<div class="wrap">
		<el-row>
			<el-col :spam='4' class="head">
				<div>
					<p class="title">电影列表</p>
				</div>
			</el-col>
		</el-row>
		<el-row :gutter="20" class="mid" >
			<el-breadcrumb separator="/">
				<el-col :span="5" :offset="2">
					<el-breadcrumb-item ><span style="line-height:12px">海报</span></el-breadcrumb-item>
				</el-col>
				<el-col :span="5">
					<el-breadcrumb-item><span>名称</span></el-breadcrumb-item>
				</el-col>
				<el-col :span="5">
				<el-breadcrumb-item><span>评分</span></el-breadcrumb-item>
				</el-col>
				<el-col :span="5">
					<el-breadcrumb-item><span>操作</span></el-breadcrumb-item>
				</el-col>
			</el-breadcrumb>			
			</el-row>
<!--列表-->		
		<ul>
			<li v-for="item in list" >	
				<el-row :gutter="20" v-show="item.show">
					<el-col :span="5" :offset="1" class="list">
						<img :src="item.src" :span="15" class="list">
					</el-col>
					<el-col :span="5" class="list">
						<div>
							<span>{{item.name}}</span>
						</div>
					</el-col>
					<el-col :span="5" class="list">
						<div>
							<el-rate v-model="item.introvalue" disabled></el-rate>
						</div>
					</el-col>
					<el-col :span="7"  class="list">
<!--修改删除按钮-->
						<div>
							<el-button type="primary" >详细</el-button>
							<el-button type="warning" @click="modify(item)">修改</el-button>
							<el-button type="danger" @click="deletemv(item)">删除</el-button>
						</div>
					</el-col>
				</el-row>
			</li>
		</ul>
<!--添加按钮-->
		<el-row>
			<el-col :span="1" :offset="11">
				<el-button @click="addmv=true" type="primary" class="el-icon-plus add"></el-button>
			</el-col>
		</el-row>
<!--添加窗口-->
		<el-dialog title="添加电影" :visible.sync="addmv">
			<el-form :model="form">
				<el-form-item label="电影名称" :label-width="formLabelWidth">
					<el-input v-model="form.name" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="海报地址" :label-width="formLabelWidth">
					<el-input v-model="form.src" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="电影简介" :label-width="formLabelWidth">
					<el-input type="textarea" :autosize="{minRow:1,maxRow:2}" v-model="form.introduction" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="评分" :label-width="formLabelWidth">
					<div class="block">
					  <el-rate v-model="form.introvalue"></el-rate>
					</div>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click="addmv = false">取 消</el-button>
				
				<el-button type="primary" @click="postAddmv">确 定</el-button>
			
			</div>
		</el-dialog>
<!--修改窗口-->
		<el-dialog title="修改电影" :visible.sync="modifymv">
			<el-form :model="form">
				<el-form-item label="电影名称" :label-width="formLabelWidth">
					<el-input v-model="modifyform.name" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="海报地址" :label-width="formLabelWidth">
					<el-input v-model="modifyform.src" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="电影简介" :label-width="formLabelWidth">
					<el-input type="textarea" v-model="modifyform.introduction" auto-complete="off"></el-input>
				</el-form-item>
				<el-form-item label="评分" :label-width="formLabelWidth">
					<div class="block">
					  <el-rate v-model="modifyform.introvalue"></el-rate>
					</div>
				</el-form-item>
			</el-form>
			<div slot="footer" class="dialog-footer">
				<el-button @click="modifymv = false">取 消</el-button>
				<el-button type="primary" @click="upmv(modifyform)">确 定</el-button>
			</div>
		</el-dialog>

	</div>
  </template>
  <script>
	  export default
	{
	
		data:function(){
			return {
				list:[{
				_id:'',
				name:"电影",
				src:"https://gss0.bdstatic.com/70cFfyinKgQIm2_p8IuM_a/daf/pic/item/0b7b02087bf40ad1445506445d2c11dfa8eccee0.jpg",
				introduction:"sha",
				introvalue:5,
				show:true
				}],
				addmv: false,
				form: { 
					_id:'',
					name: '', 
					src: '', 
					introduction: '',
					introvalue:5
				},
				modifymv:false,
				modifyform:{
					master:'',
					_id:'',
					name: '', 
					src: '', 
					introduction: '',
					introvalue:null
				},
				formLabelWidth: '120px',
				
				
			}
		},
		created:function(){
			this.$ajax.get('/api')
				.then(function(res){
					for(var i in res.data){
						res.data[i].show=true;
					}
					this.list=res.data;
					
				}.bind(this))
				.catch(function(err){
					alert(err);
				})
		},
		methods:{
			modify:function(item){
				this.modifymv=true;
				this.modifyform.master=item;
				this.modifyform._id=item._id;
				this.modifyform.name=item.name;
				this.modifyform.src=item.src;
				this.modifyform.introduction=item.introduction;
				this.modifyform.introvalue=item.introvalue;
			},
			
			postAddmv:function(){
				this.$ajax.post('/api',{
					name:this.form.name,
					src:this.form.src,
					introduction:this.form.introduction,
					introvalue:this.form.introvalue
				})
				.then(function(res){
					var item={
						name:this.form.name,
						src:this.form.src,
						introduction:this.form.introduction,
						introvalue:this.form.introvalue,
						show:true
					}
					this.addmv=false;
					this.list.unshift(item);
					console.log(this.list);
					
				}.bind(this))
				.catch(function(err){
					alert(err)
				})
			},
			deletemv:function(item){
				console.log(item._id)
				this.$ajax.post('/api/delete',{
					id:item._id
				})
				.then(function(res){
					item.show=false;
					console.log(res);
				}.bind(item))
				.catch(function(err){
					alert(er);
				})
			},
			upmv:function(modifyform){
				this.$ajax.post('/api/update',{
					id:modifyform._id,
					name:modifyform.name,
					src:modifyform.src,
					introduction:modifyform.introduction,
					introvalue:modifyform.introvalue
				})
				.then(function(res){
					this.modifymv=false;
					console.log(res);
					this.modifyform.master.id=modifyform._id;
					this.modifyform.master.name=modifyform.name,
					this.modifyform.master.src=modifyform.src,
					this.modifyform.master.introduction=modifyform.introduction,
					ithis.modifyform.master.introvalue=modifyform.introvalue
				}.bind(this))
				.catch(function(err){
					console.log(err);
				})
			}
		}
		
	}
  </script>
  <style>
	   ul,li{
	  list-style:none;
	  margin:0px 0px;
	  padding:0px 0px;
	  }
	  body {
	  font-family: Helvetica, sans-serif;
	}
	.wrap{
	margin-left:-10px;
	margin-top:-10px;
	}
	.head{
		background:#58B7FF;
	}
	.title{
		color:white;
		padding-left:10px;
		font-family:"Hiragino Sans GB";
		font-size:20px;
	}
	.mid{
	padding-top:12px;
	border-bottom:1px #E5E9F2 solid;
	height:40px;
	line-height:center;
	font-size:12px;
	text-align:center;
	line-height:12px;
	}
	.list{
	height:80px;
	}
	.add{
	margin:0 auto;
	border-radius:50%;
	}
  </style>