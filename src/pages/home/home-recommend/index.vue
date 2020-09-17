<template>
    <scroll-view
        @scrolltolower="handleToLower"
        class="recommends_view"
        scroll-y
        v-if="recommends.length > 0"
    >
        <!-- 推荐开始 -->
        <view class="recommend_wrap">
            <navigator
                class="recommend_item"
                v-for="item in recommends"
                :key="item.id"
                :url="`/pages/album/index?id=${item.target}`"
            >
                <img mode="widthFix" :src="item.thumb" />
            </navigator>
        </view>
        <!-- 推荐结束 -->

        <!-- 月份开始 -->
        <view class="monthes_wrap">
            <view class="monthes_title">
                <view class="monthes_title_info">
                    <view class="monthes_info">
                        <text>{{ monthes.DD }}/ </text>
                        {{ monthes.MM }}月
                    </view>
                    <view class="monthes_text">{{ monthes.title }}</view>
                </view>
                <view class="monthes_title_more">更多></view>
            </view>
            <view class="monthes_content">
                <view
                    class="monthes_item"
                    v-for="(item, index) in monthes.items"
                    :key="item.id"
                >
                <go-detail :list='monthes.items' :index='index'>
                    <img
                        mode="aspectFill"
                        :src="item.thumb + item.rule.replace('$<Height>', 360)"
                    />
                </go-detail>
                </view>
            </view>
        </view>
        <!-- 月份结束 -->

        <!-- 热门开始 -->
        <view class="hots_wrap">
            <view class="hots_title">
                <text>热门</text>
            </view>
            <view class="hots_content">
                <view class="hots_item" v-for="(item, index) in hots" :key="item.id">
                    <go-detail :list='hots' :index='index'>
                        <img mode="widthFix" :src="item.thumb" />
                    </go-detail>
                </view>
            </view>
        </view>
        <!-- 热门结束 -->
    </scroll-view>
</template>

<script>
import moment from "moment";
import goDetail from '@/components/goDetail'
export default {
    components:{
        goDetail
    },
    data() {
        return {
            // 推荐列表
            recommends: [],
            // 月份
            monthes: [],
            // 热门
            hots: [],
            // 请求参数
            params: {
                // 要获取几条
                limit: 30,
                // 关键字
                order: "hot",
                // 要跳过几条
                skip: 5,
            },
            // 是否还有下一页
            hasMore: true,
        };
    },
    mounted() {
        this.getList();
    },
    methods: {
        // 获取接口的数据
        getList() {
            this.request({
                url: "http://157.122.54.189:9088/image/v3/homepage/vertical",
                data: this.params,
            }).then((result) => {
                // console.log(result);

                //判断还有没有下一页数据
                if (result.data.res.vertical.length == 0) {
                    this.hasMore = false;
                    uni.showToast({
                        title: "没有更多数据了~",
                        icon: "none",
                    });
                    return;
                }

                if (this.recommends.length == 0) {
                    // 推荐模块
                    this.recommends = result.data.res.homepage[1].items;
                    // 月份模块
                    this.monthes = result.data.res.homepage[2];
                    // 将时间戳改成 号/月 moment.js
                    this.monthes.MM = moment(this.monthes.stime).format("MM");
                    this.monthes.DD = moment(this.monthes.stime).format("DD");
                }
                // 获取热门数据的列表
                this.hots = [...this.hots, ...result.data.res.vertical];
            });
        },
        // 滚动条触底事件
        handleToLower() {
            /* 
            1. 修改参数 skip+=limit;
            2. 重新发送请求 getList();
            3. 请求回来成功 hots 数据的叠加
            */
            if (this.hasMore) {
                this.params.skip += this.params.limit;
                this.getList();
            } else {
                //弹窗提示用户
                uni.showToast({
                    title: "没有数据了",
                    icon: "none",
                });
            }
        },
    },
};
</script>

<style lang="scss" scoped>
.recommends_view {
    // height: 屏幕的高度 - 头部标题的高度
    height: calc(100vh - 36px);
}
.recommend_wrap {
    // box-sizing: border-box;
    display: flex;
    flex-wrap: wrap;
    .recommend_item {
        width: 50%;
        border: 5rpx solid #fff;
    }
}
.monthes_wrap {
    .monthes_title {
        display: flex;
        justify-content: space-between;
        .monthes_title_info {
            color: $color;
            font-size: 30rpx;
            font-weight: 600;
            display: flex;
            justify-content: space-between;
            .monthes_info {
                text {
                    font-size: 36rpx;
                }
            }

            .monthes_text {
                font-size: 34rpx;
                color: #666;
                margin-left: 30rpx;
            }
        }

        .monthes_title_more {
            font-size: 34rpx;
            color: $color;
        }
    }

    .monthes_content {
        display: flex;
        flex-wrap: wrap;
        .monthes_item {
            width: 33.33%;
            border: 5rpx solid #fff;
        }
    }
}

.hots_wrap {
    .hots_title {
        padding: 20rpx;
        text {
            border-left: 15rpx solid $color;
            padding-left: 20rpx;
            font-size: 34rpx;
            font-weight: 600;
        }
    }

    .hots_content {
        display: flex;
        flex-wrap: wrap;
        .hots_item {
            width: 33.33%;
            border: 5rpx solid #fff;
            image {
            }
        }
    }
}
</style>