<template>
    <view>
        <!-- 用户信息 开始 -->
        <view class="user_info">
            <view class="user_icon">
                <img mode="widthFix" :src="imgDetail.user.avatar" />
            </view>
            <view class="user_desc">
                <view class="user_name">{{ imgDetail.user.name }}</view>
                <view class="user_time">{{ imgDetail.cnTime }}</view>
            </view>
        </view>
        <!-- 用户信息 结束 -->

        <!-- 高清大图 开始 -->
        <view class="high_img">
            <img mode="widthFix" :src="imgDetail.newThumb" />
        </view>
        <!-- 高清大图 结束 -->

        <!-- 点赞 开始 -->
        <view class="user_rank">
            <view class="rank">
                <text class="iconfont icon-dianzan">{{ imgDetail.rank }}</text>
            </view>
            <view class="user_collect">
                <text class="iconfont icon-soucang">收藏</text>
            </view>
        </view>
        <!-- 点赞 结束 -->

        <!-- 专辑 开始 -->
        <view class="album_wrap">
            <!-- 标题 -->
            <view class="album_title">相关</view>
            <!-- 内容 -->
            <view class="album_list">
                <view class="album_item" v-for="item in album" :key="item.id">
                    <!-- 左边 -->
                    <view class="album_cover">
                        <img mode='aspectFill' :src="item.cover">
                    </view>
                    <!-- 右边 -->
                    <view class="album_info">
                        <view class="album_info_text">专辑</view>
                        <view class="album_name">{{item.name}}</view>
                        <text class="iconfont icon-youjiantou"></text>
                    </view>
                </view>
            </view>
        </view>
        <!-- 专辑 结束 -->

    </view>
</template>

<script>
import moment from 'moment'
// 设置语言为中文
moment.locale("zh-cn");
export default {
    data() {
        return {
            // 图片信息对象，包含着用户头像。。
            imgDetail: {},
            album:[],
            hot:[],
            comment:[]
        };
    },
    onLoad() {
        // console.log(getApp().globalData);
        const { imgList, imgIndex } = getApp().globalData;
        this.imgDetail = imgList[imgIndex];
        this.imgDetail.newThumb =
            this.imgDetail.thumb +
            this.imgDetail.rule.replace("$<Height>", 360);

        // xxx 年前的数据
        this.imgDetail.cnTime = moment(this.imgDetail.atime*1000).fromNow();
        // 获取图片详情的id
        // this.imgDetail.id;
        this.getComment(this.imgDetail.id);
    },
    methods: {
        getComment(id){
            this.request({
                url:`http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
            }).then(result=>{
                // console.log(result);
                this.album = result.data.res.album;
                this.hot = result.data.res.hot;
                this.comment = result.data.res.comment;
            })
        }
    },
};
</script>

<style lang='scss' scoped>
.user_info {
    display: flex;
    padding: 20rpx;
    .user_icon {
        padding: 0 20rpx;
        image {
            width: 88rpx;
            border-radius: 50%;
        }
    }

    .user_desc {
        .user_name {
            color: #000;
            font-weight: 600;
        }

        .user_time {
            color: #ccc;
            font-size: 24rpx;
            padding: 10rpx 0;
        }
    }
}
.user_rank {
    display: flex;
    height: 80rpx;
    border-bottom: 5rpx solid #eee;
  .rank {
      display: flex;
      justify-content: center;
      align-items: center;
      flex: 1;
    .iconfont {

    }
  }

  .user_collect {
      display: flex;
      justify-content: center;
      align-items: center;
      flex: 1;
    .iconfont {

    }
  }
}
.album_wrap {
    padding: 20rpx;
  .album_title {
      padding: 10rpx 0;
  }
  .album_list {
    .album_item {
        display: flex;
        padding: 10rpx 0;
        border-bottom: 6rpx solid #eee;
      .album_cover {
          flex: 1;
        image {
            width: 180rpx;
            height: 180rpx;
        }
      }
      .album_info {
          flex: 3;
          padding-left: 20rpx;
          position: relative;
        .album_info_text {
            width: 100rpx;
            height: 50rpx;
            background-color: $color;
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        .album_name {
            padding: 10rpx 0;
            color: #888;
        }
        .iconfont{
            font-size: 40rpx;
            position: absolute;
            top: 50%;
            transform: translateY(-50%);
            right: 10%;
            color: #000;
        }
      }
    }
  }
}
</style>