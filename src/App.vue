<template>
    <!-- 跟组件 -->
    <div id="app">
        <router-view></router-view>
    </div>
</template>

<script>
export default {
    name: "App",
    components: {},
    data() {
        return {
            res: {},
            options: {
                socketPath: 'wss://scs.go-easy.net/ws',
                serverUrl: 'http://localhost:8080/',
            },
            menuList: {
                mic: true,
                sameScreen: true,
                hangUp: true,
                drawing: true,
                withdraw: false,
                clear: true,
            },
            // serverData参数说明 
            serverData: {
                activated: 0,// 激活状态 0:新建; 1:激活; 2:关闭; 
                callId: "ca25f13158ed414ebefc4e2278966b8c",// 通话记录ID
                callNo: "220505224040",// 通话编号/房间号 
                callStatus: "waiting", // 通话状态 
                callType: 0,// 通话记录来源类型, 0: 呼叫, 1: 预约
                callUserId: 101,// 客服用户id
                callUserName: "test03",// 客服用户名 
                callUserNickName: "客服小美",// 客服昵称 
                consumerId: "8512330",// 客户编号 
                consumerName: "小王",// 客户名称 
                consumerTel: "18382223266",// 客户电话 
                createTime: "2022-05-05 07:13:07",// 创建时间 
                pageName: "信息登记", // 页面名称 
                params: {},
                productId: "cd001",// 产品id 
                roomId: "220505224040", // 通话编号/房间号 s
                hareUrlPrefix: "http://localhost:9999/#/client", // 分享通话记录url前缀 
                urlSource: "http://localhost:9999/#/client", // 通话记录来源url 
                },
               callHandlerBtnStyle :{} ,
                handlerMenuStyle: {}
            }
        } ,
            mounted() {
            // 每刷新时拉取一次用户信息到Vuex，防止刷新时数据丢失
            
            //初始化 sdk和 wenrcs server 挂载到windows对象上
            this.webrcs = new window.WebrcsClient(this.options);
            console.log(this,this. webrcs);
                
           this.webrcs.showRcsHandlerMenu( this.callHandlerBtnStyle = {}, this.handlerMenuStyle = {},this.menuList ) // callHandlerBtnStyle 呼叫按钮的样式自定义对象 // handlerMenuStyle 详细操作菜单的样式自定义对象 // menuList 动态控制详细菜单的显示项目 // this.menuList = { mic: true,// 显示麦克风按钮，控制静音与否 // video: true, // 显示视频按钮，控制视频推送，在1.06版本中暂时废弃 sameScreen: true, // 同屏按钮，是否开启同屏 hangUp: true, // 挂断按钮 drawing: false, // 涂鸦按钮 Withdraw: false, // 涂鸦撤回按钮 clear: true // 清除涂鸦按钮 }



                // 点击呼叫按钮回调
                this.webrcs.clickHadler_callServer = (callback) => {
                callback({
                    callOptions: {
                    userInfo: {
                        phone: '18382223266',
                        actualName: '小王',
                        userName: "test",
                    },
                    },
                    initOptions: {
                    webRTCCOnfig: {
                        video: true,
                        audio: true,
                    },
                    },
                })
                }

                            // 初始化回调
                this.webrcs.onInit = () => {
                this.$message.info("初始化成功");
                };

                // 开始回调
                this.webrcs.onRequest_assistance_response = (serverData) => {
                console.log('开始回调', serverData);
                };
                // 挂断回调
                this.webrcs.onHangUp = (res) => {
                // this.$message.info("初始化成功");
                console.log(res);
                };

                    // 没有客服在线回调
    this.webrcs.onNot_server_online = () => {
      this.$confirm("暂时没有在线客服，可先预约后续回复", "提示", {
        showCancelButton: false,
      }).then(() => {
        // 预约方法
        // this.webrcs.reservation({
        //   // 客户的id
        //   consumerId: Math.round(Math.random() * 10000000) + '',
        //   // 客户的用户名
        //   consumerName: 'xxxx',
        //   // 客户的电话
        //   consumerTel: '1111111',
        //   // 页面名称
        //   pageName: document.title,
        //   // 产品id
        //   productId: Math.round(Math.random() * 10000000) + '',
        // })
        // 隐藏详细操作栏
        this.webrcs.hideRcsHandlerMenu();
      });
    };

    // SDK 服务端统一协助请求的回调 方便用户保存信息，然后二次进入调用
    this.webrcs.onAccept_assistance = (serverData) => {
      console.log('onAccept_assistanceonAccept_assistanceonAccept_assistance', serverData);
    };

    // 初始化失败回调
    this.webrcs.onInit_failed = (error) => {
      console.error(error);
    };
    // 呼叫超时
    this.webrcs.onCall_wait_timeout = () => { };

    // // 显示呼叫按钮
    // this.webrcs.showRcsHandlerMenu({}, {}, {
    //   drawing: true,
    //   Withdraw: true,
    // });



  
            // 在展示操作菜单之前，监听呼叫按钮点击事件，该事件参数为一个回调函数，里面传入初始化和呼叫相关 参数调用，参数信息参考api调用 
            this.webrcs.clickHadler_callServer = (callBack) => {
                callBack({
                    initOptions: {}, // 参数参考2.8.1
                    callOptions: {} // 参数参考2.8.2
                })
            }
            // //初始化完成回调
            // this.webrcs.onInit = () => { } ;

            //监听没有客服回调，方便预约
            this.webrcs.onNot_server_online = () => {
                this.$confirm('暂时没有在线客服，可先预约后续回复', '提示', { showCancelButton: false }).then(() => {
                    // 预约 // todo。。。 
                    this.webrcs.hideRcsHandlerMenu()
                })
            }

            // 点击呼叫按钮后服务器分配对应坐席的回调 
            this.webrcs.onRequest_assistance_response = (serverData) => { localStorage.setItem('serverData', JSON.stringify(serverData)) }

            // 坐席端连通后回应消息到达的回调 
            this.webrcs.onAccept_assistance = (serverData) => { localStorage.setItem('serverData', JSON.stringify(serverData)) }


            //------------------------------------------------------------------------------------------------------------
            if (this.$cookie.get("useId")) {
                this.getUser();
                this.getCartCount();
            }
        },
        methods: {
            getUser() {
                this.$store.dispatch("frashIndexUpdataUsername");
            },
            getCartCount() {
                this.$store.dispatch("frashIndexUpdataUpdata");
            },
        },
    };
</script>

<style lang='scss'>
@import "./assets/scss/reset.scss";
@import "./assets/scss/config.scss";
@import "./assets/scss/button.scss";
</style>
 
