<template>
    <view>
        <!-- 专辑背景 开始 -->
        <view class="album_bg">
            <img mode="widthFix" :src="album.cover" />
            <view class="album_info">
                <view class="album_name">{{ album.name }}</view>
                <view class="album_btn">关注专辑</view>
            </view>
        </view>
        <!-- 专辑背景 结束 -->

        <!-- 专辑作者 开始 -->
        <view class="album_author">
            <view class="album_author_info">
                <img mode="widthFix" :src="album.user.avatar" />
                <view class="author_name">{{ album.user.name }}</view>
            </view>
            <view class="album_author_desc">
                <text>{{ album.desc }}</text>
            </view>
        </view>
        <!-- 专辑作者 结束 -->

        <!-- 列表 开始 -->
        <view class="album_list">
            <view class="album_item" v-for="(item, index) in wallpaper" :key="item.id">
                <go-detail :list='wallpaper' :index='index'>
                    <img
                        mode="widthFix"
                        :src="item.thumb + item.rule.replace('$<Height>', 360)"
                    />
                </go-detail>
            </view>
        </view>
        <!-- 列表 结束 -->
    </view>
</template>

<script>
import goDetail from '@/components/goDetail'
export default {
    components:{
        goDetail
    },
    data() {
        return {
            params: {
                limit: 30,
                order: "new",
                skip: 0,
                first: 1,
            },
            id: -1,
            album: {},
            wallpaper: [],
            hasMore: true,
        };
    },
    onLoad(options) {
        console.log(options);
        this.id = options.id;
        // this.id = "5e05dd1ee7bce739c4b3bd20";
        this.getList();
    },
    // 页面触底  上拉加载下一页事件
    onReachBottom() {
        // console.log('6666');
        if (this.hasMore) {
            this.params.first = 0;
            this.params.skip += this.params.limit;
            this.getList();
        } else {
            uni.showToast({
                title: "没有更多数据了~",
                icon: "none",
            });
        }
    },
    methods: {
        getList() {
            this.request({
                url: `http://157.122.54.189:9088/image/v1/wallpaper/album/${this.id}/wallpaper`,
                data: this.params,
            }).then((result) => {
                // console.log(result);
                if (Object.keys(this.album).length == 0) {
                    this.album = result.data.res.album;
                }
                if (result.data.res.wallpaper.length == 0) {
                    this.hasMore = false;
                    uni.showToast({
                        title: "没有更多数据了~",
                        icon: "none",
                    });
                    return;
                }
                this.wallpaper = [...this.wallpaper,...result.data.res.wallpaper,];
            });
        },
    },
};
</script>

<style lang='scss' scoped>
.album_bg {
    position: relative;
    image {
    }

    .album_info {
        position: absolute;
        width: 100%;
        left: 0;
        bottom: 0;
        display: flex;
        justify-content: space-between;
        height: 100rpx;
        align-items: center;
        color: #fff;
        padding: 0 15rpx;
        .album_name {
            font-size: 40rpx;
        }

        .album_btn {
            background-color: $color;
            width: 152rpx;
            height: 60rpx;
            display: flex;
            justify-content: center;
            align-items: center;
            border-radius: 10rpx;
        }
    }
}
.album_author {
    padding: 10rpx;
    .album_author_info {
        padding: 10rpx 0;
        display: flex;
        img {
            width: 50rpx;
        }

        .author_name {
            color: #000;
            margin-left: 15rpx;
        }
    }

    .album_author_desc {
    }
}
.album_list {
    display: flex;
    flex-wrap: wrap;
    .album_item {
        width: 33.33%;
        border: 3rpx solid #fff;
        img {
        }
    }
}
</style>