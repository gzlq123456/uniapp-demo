<template>
	<view class="content">
    <view v-for="(item,index) in datas" :key="index">
      <text>{{ item.id }}</text>
      <u-upload :fileList="fileLists[`fileList${item.id}`]" @afterRead="afterRead" @delete="deletePic" :name="item.id"
                multiple></u-upload>
    </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
        datas: [{id: '1', name: '1'}, {id: '2', name: '2'}, {id: '3', name: '3'}, {id: '4', name: '4'}, {id: '5', name: '5'}],
        fileLists: {},
			}
		},
		onLoad() {
      this.initPhoto();
		},
		methods: {
      // 初始化图片数组
      initPhoto() {
        for (const item of this.datas) {
          this.$set(this.fileLists,`fileList${item.id}`, [])
        }
      },
      // 删除图片
      deletePic(event) {
        this.fileLists[`fileList${event.name}`].splice(event.index, 1)
      },
      // 新增图片
      async afterRead(event) {
        console.log(event)
        // 当设置 multiple 为 true 时, file 为数组格式，否则为对象格式
        let lists = [].concat(event.file)

        let fileListLen = this.fileLists[`fileList${event.name}`].length
        lists.map((item) => {
          this.fileLists[`fileList${event.name}`].push({
            ...item,
            status: 'uploading',
            message: '上传中'
          })
        })
        for (let i = 0; i < lists.length; i++) {
          const result = await this.uploadFilePromise(lists[i].url)
          let item = this.fileLists[`fileList${event.name}`][fileListLen]
          this.fileLists[`fileList${event.name}`].splice(fileListLen, 1, Object.assign(item, {
            status: 'success',
            message: '',
            url: result
          }))
          fileListLen++
        }
        console.log(this.fileLists[`fileList${event.name}`])
      },
      uploadFilePromise(url) {
        return new Promise((resolve, reject) => {
          let a = uni.uploadFile({
            url: 'http://192.168.2.21:7001/upload', // 仅为示例，非真实的接口地址
            filePath: url,
            name: 'file',
            formData: {
              user: 'test'
            },
            success: (res) => {
              setTimeout(() => {
                resolve(res.data.data)
              }, 1000)
            }
          });
        })
      },
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
