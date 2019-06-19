<template>
	<view>
		<view class="cu-bar bg-white search">
			<view class="search-form round">
				<text class="cuIcon-search">选择游戏人数</text>
				<input type="text" placeholder="选择游戏人数" @input="ConfirmPlayer"></input>
			</view>
		</view>
		<view class="cu-list grid col-3">
			<view class="cu-item" v-for="(player, index) in players" @click="selectPlayer(index, player['name'])">
				<view class="cu-tag badge" v-if="player['dead']">
					<block v-for="statue in player['status']">{{statue}}</block>
				</view>
				<text class="lg text-gray">{{player['name']}}</text>
				<text>{{index + 1}}号玩家</text>
			</view>
		</view>
		<view class="grid col-4 padding-sm">
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('平民')">平民</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('狼人')">狼人</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('预言家')">预言家</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('女巫')">女巫</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('猎人')">猎人</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('白吃')">白吃</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="IdentitySelect('熊')">熊</button>
			</view>
		</view>
		<view class="grid col-4 padding-sm">
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="Features('刀')">刀</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="Features('毒')">毒</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="Features('救')">救</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="Features('查验')">查验</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="Features('开枪')">开枪</button>
			</view>
			<view class="margin-tb-sm text-center">
				<button class="cu-btn round bg-white" @click="Features('投票')">投票</button>
			</view>
		</view>
		<view v-html="msg"></view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				players: [],
				msg: '',
				name: '',
				playerFlg: true
			}
		},
		methods: {
			ConfirmPlayer: function(e) {
				let num = e.detail.value;
				this.players = [];
				for (var i = 0; i < num; i++) {
					this.players.push({
						id: i,
						name: '平民',
						dead: false,
						status: ''
					})
				}
			},
			IdentitySelect: function(name) {
				this.name = name;
				this.playerFlg = true;
			},
			selectPlayer: function(index, name) {
				if (this.playerFlg) {
					this.players[index]['name'] = this.name;
					this.msg += '法官将' + (index + 1) + '号的身份从' + name + '修改为' + this.name + '<br>';
				} else {
					switch (this.name) {
						case '刀':
							if (this.players[index]['dead'] && this.players[index]['status'] == '刀') {
								this.players[index]['dead'] = false;
								this.players[index]['status'] = '';
								this.msg += '法官取消狼人杀死' + (index + 1) + '号玩家<br>';
							} else if (!this.players[index]['dead']) {
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '刀';
								this.msg += '狼人杀死了' + (index + 1) + '号玩家<br>';
							} else {
								this.msg += '法官将' + (index + 1) + '号玩家死亡状态由' + this.players[index]['status'] + '修改为刀<br>';
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '刀';
							}
							break;
						case '毒':
							if (this.players[index]['dead'] && this.players[index]['status'] == '毒') {
								this.players[index]['dead'] = false;
								this.players[index]['status'] = '';
								this.msg += '法官取消女巫毒死' + (index + 1) + '号玩家<br>';
							} else if (!this.players[index]['dead']) {
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '毒';
								this.msg += '女巫毒死了' + (index + 1) + '号玩家<br>';
							} else {
								this.msg += '法官将' + (index + 1) + '号玩家死亡状态由' + this.players[index]['status'] + '修改为毒<br>';
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '毒';
							}
							break;
						case '救':
							if (this.players[index]['dead']) {
								this.players[index]['dead'] = false;
								this.players[index]['status'] = '';
								this.msg += '女巫救了' + (index + 1) + '号玩家<br>';
							}
							break;
						case '查验':
							this.msg += '预言家查验' + (index + 1) + '号玩家，他是个' + (this.players[index]['name'] == '狼人' ? '狼人' : '好人') + '<br>';
							break;
						case '开枪':
							if (this.players[index]['dead'] && this.players[index]['status'] == '枪') {
								this.players[index]['dead'] = false;
								this.players[index]['status'] = '';
								this.msg += '法官取消猎人打死' + (index + 1) + '号玩家<br>';
							} else if (!this.players[index]['dead']) {
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '枪';
								this.msg += '猎人打死了' + (index + 1) + '号玩家<br>';
							} else {
								this.msg += '法官将' + (index + 1) + '号玩家死亡状态由' + this.players[index]['status'] + '修改为枪<br>';
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '枪';
							}
							break;
						case '投票':
							if (this.players[index]['dead'] && this.players[index]['status'] == '票') {
								this.players[index]['dead'] = false;
								this.players[index]['status'] = '';
								this.msg += '法官取消投票' + (index + 1) + '号玩家<br>';
							} else if (!this.players[index]['dead']) {
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '票';
								this.msg += '投票出局了' + (index + 1) + '号玩家<br>';
							} else {
								this.msg += '法官将' + (index + 1) + '号玩家死亡状态由' + this.players[index]['status'] + '修改为票<br>';
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '票';
							}
							break;
						default:
							break;
					}
				}

			},
			Features: function(name) {
				this.playerFlg = false;
				this.name = name;
			}
		}
	}
</script>

<style>
</style>
