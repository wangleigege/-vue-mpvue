<template>
    <div>
        <button class="Query" v-if="buttonVisible" open-type="getPhoneNumber" @getphonenumber="bindGetUserInfo">获取权限</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            buttonVisible: '',
            loaclRegistered: ''
        }
    },
    created() {
        //  检查本地是否存在token
        this.loaclRegistered = wx.getStorageSync('sessionlocal')
        console.log(this.loaclRegistered, 'bendiren')
        if (this.loaclRegistered === ' ' || this.loaclRegistered == true) {
            let self = this;
            wx.checkSession({
                success() {
                    console.log('token is in ok')
                },
                fail() {
                    // session_key 已经失效，需要重新执行登录流程
                    console.log('需要重新执行登录流程')
                    wx.login({
                        success(res) {
                            if (res.code) {
                                self.code = res.code;
                                self.getcode = res.code
                                // 显示登陆按钮
                                self.buttonVisible = true
                                console.log(res.code, '你说你吗废物')
                            }
                        },
                    })
                }
            })
        } else {
            const self = this;
            // session_key 已经失效，需要重新执行登录流程
            console.log('需要重新执行登录流程')
            this.buttonVisible = true
            console.log(this.buttonVisible, '按钮进来了')
            wx.login({
                success(res) {
                    if (res.code) {
                        self.code = res.code;
                        self.getcode = res.code
                        console.log(res.code, '你瞅啥')
                        // 显示登陆按钮
                        self.buttonVisible = true
                    }
                },
            })
        }

    },
    methods: {
        bindGetUserInfo(e) {
            console.log('回调事件后触发sbsbbsbbsbsbb')
            const self = this;
            if (e.mp.detail.iv) {
                console.log('用户按了允许授权按钮')
                // this.changeMusic(true)

                // let { encryptedData, userInfo, iv } = e.mp.detail;
                // console.log(e)
                console.log(self.getcode, 'getcode')
                self.$request('/wechat/phone', 'POST', {
                    code: self.getcode,
                    encryptedData: e.mp.detail.encryptedData,
                    iv: e.mp.detail.iv
                }).then(res => {
                    console.log(res, 'fuvvvvvvv')
                    console.log(res.data.token)
                    wx.setStorageSync('sessionlocalphone', res.data.phone)
                    self.$request('/login', 'POST', {
                        token: res.data.token,
                        phone: res.data.phone,
                    }).then(res => {
                        console.log(res, '你注册过了吗')
                        wx.setStorageSync('sessionlocal', res.data.isRegistered)
                    })
                    console.log(res.data.token, "请求后的token")

                    console.log(res.data.token, '下一个token')
                    wx.setStorage({
                        key: "key",
                        data: res.data.token
                    })
                    console.log(res.data.isRegistered, 'okokoko')

                    console.log('美誉')
                    self.buttonVisible = false
                })
            } else {
                //用户按了拒绝按钮
                console.log('用户按了拒绝按钮');
            }
        },

    }
}
</script>

<style lang="less" scoped>
.Query {
    position: fixed;
    width: 300rpx;
    height: 100rpx;
    z-index: 9998;
    opacity: 0; // border: 1px solid black;
    left: 100px;
    bottom: 200px;
}
</style>
