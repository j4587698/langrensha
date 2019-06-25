<template>
	<view class="bg-white">
		<view class="cu-bar bg-white search">
			<view class="search-form round">
				<text class="cuIcon-search">选择游戏人数</text>
				<input type="text" placeholder="选择游戏人数" @input="ConfirmPlayer" ref="playerCountInput"></input>
			</view>
		</view>
		<view class="cu-list grid col-3">
			<view class="cu-item" v-for="(player, index) in players" @click="selectPlayer(index, player['name'])">
				<view class="cu-tag badge" v-if="player['dead'] || player['skill']">
					<block v-for="statue in player['status']">{{statue}}</block>
				</view>
				<text class="lg text-gray">{{player['name']}}</text>
				<text>{{index + 1}}号玩家</text>
			</view>
		</view>
		<view class="cu-bar bg-white solid-bottom">
			<view class="action">
				角色选择
			</view>
		</view>
		<view class="grid col-4 padding-sm">
			<view class="margin-tb-sm text-center" v-for="player in playerIdentity">
				<button class="cu-btn round " :class="name==player?'bg-red':'bg-white'" @click="IdentitySelect(player)">{{player}}</button>
			</view>
		</view>
		<view class="cu-bar bg-white solid-bottom">
			<view class="action">
				技能选择
			</view>
		</view>
		<view class="grid col-4 padding-sm">
			<view class="margin-tb-sm text-center" v-for="feature in features">
				<button class="cu-btn round " :class="name==feature?'bg-red':'bg-white'" @click="Features(feature)">{{feature}}</button>
			</view>
		</view>
		<view class="cu-form-group">
			<view class="title">倒计时</view>
			<input placeholder="请输入倒计时时间" name="input" v-model="downCountTime"></input>
			<button class='cu-btn bg-green shadow' @click="StartCountDown()">开始计时</button>
		</view>
		<button class="cu-btn block bg-blue margin-tb-sm lg" @click="ResetGameShow()">重置游戏</button>
		<button class="cu-btn block bg-blue margin-tb-sm lg" @click="MsgShow()">查看记录</button>

		<view class="cu-modal" :class="modalName == 'showMsg' ? 'show' : ''" @tap="HideModal">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content">游戏日志</view>
				</view>
				<view class="padding-xl">
					<view v-html="msg"></view>
				</view>
			</view>
		</view>

		<view class="cu-modal" :class="modalName=='resetGame'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content">重置游戏</view>
					<view class="action" @tap="HideModal">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view class="padding-xl">
					是否重置游戏?
				</view>
				<view class="cu-bar bg-white justify-end">
					<view class="action">
						<button class="cu-btn line-green text-green" @tap="HideModal">取消</button>
						<button class="cu-btn bg-green margin-left" @tap="ResetGame">确定</button>

					</view>
				</view>
			</view>
		</view>
		<view class="cu-modal" :class="modalName=='countDown'?'show':''">
			<view class="cu-dialog">
				<view class="cu-bar bg-white justify-end">
					<view class="content">倒计时</view>
					<view class="action" @tap="HideModal">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view class="padding-xl">
					还有{{downCountTimeNow}}秒发言结束<br>
					<view v-if="downCountTimeNow <= 0">发言结束，请下一顺位发言</view>
					<button class="cu-btn block bg-blue margin-tb-sm lg" @click="StartCountDown()">重置发言时间</button>
				</view>
			</view>
		</view>
	</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				players: [],
				msg: '',
				name: '',
				playerFlg: true,
				modalName: null,
				downCountTime: 90,
				downCountTimeNow: 0,
				eval: null,
				playerIdentity: ['平民', '狼人', '预言家', '女巫', '猎人', '白痴', '熊', '守卫'],
				features: ['刀', '毒', '救', '守护', '查验', '开枪', '投票']
			}
		},
		methods: {
			ConfirmPlayer: function() {
				let num = this.$refs.playerCountInput.inputValue;
				this.players = [];
				for (var i = 0; i < num; i++) {
					this.players.push({
						id: i,
						name: '平民',
						dead: false,
						skill: false,
						status: ''
					})
				}
			},
			IdentitySelect: function(name) {
				this.name = name;
				this.playerFlg = true;
				console.log(name);
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
								if (this.players[index]['status'] == '守') {
									this.msg += '狼人杀' + (index + 1) + '号玩家，但由于此玩家被守护，不可被刀<br>';
								} else {
									this.players[index]['dead'] = true;
									this.players[index]['status'] = '刀';
									this.msg += '狼人杀死了' + (index + 1) + '号玩家<br>';
								}

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
							} else if (this.players[index]['status'] == '守') {
								this.players[index]['dead'] = true;
								this.players[index]['status'] = '奶穿';
								this.msg += '' + (index + 1) + '号玩家被同守同救，死亡<br>';
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
						case '守护':
							if (!this.players[index]['dead']) {
								if (this.players[index]['status'] == '守') {
									this.players[index]['status'] = '';
									this.players[index]['skill'] = false;
									this.msg += '法官取消守护' + (index + 1) + '号玩家<br>';
								} else {
									this.players[index]['status'] = '守';
									this.players[index]['skill'] = true;
									this.msg += '守卫守护' + (index + 1) + '号玩家<br>';
								}
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
			},
			ResetGame: function() {
				this.ConfirmPlayer();
				this.msg = "";
				this.modalName = null;
			},
			HideModal: function() {
				if (this.modalName == 'countDown' && this.eval != null) {
					clearInterval(this.eval);
					this.eval = null;
				}
				this.modalName = null;
			},
			MsgShow: function() {
				this.modalName = 'showMsg';
			},
			ResetGameShow: function() {
				this.modalName = 'resetGame';
			},
			StartCountDown: function() {
				if (this.eval != null) {
					clearInterval(this.eval);
					this.eval = null;
				}
				this.downCountTimeNow = this.downCountTime;
				this.modalName = 'countDown';
				this.eval = setInterval(() => {
					if (this.downCountTimeNow <= 0){
						clearInterval(this.eval);
						this.eval = null;
					}else {
						this.downCountTimeNow--;
					}
				}, 1000);
			}
		}
	}
</script>

<style>
</style>
