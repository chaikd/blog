<template>
  <div class="c-album">
    <Title title="图集管理">
      <template #btn v-if="canuse">
        <el-button
          style="padding: 3px 0"
          type="text"
          icon="el-icon-plus"
          @click="visible = true"
          >添加图片/图集</el-button
        >
      </template>
    </Title>
    <div v-if="canuse" class="article-content p-20">
      <el-card class="box-card" v-loading="loading">
        <!-- <AlbumTree :fileData="fileData" @deleted="childDeleted"></AlbumTree> -->
        <AlbumTrees :fileData.sync="fileData"></AlbumTrees>
      </el-card>
    </div>
    <el-dialog
      title="添加图片/图集"
      :visible.sync="visible"
      :destroy-on-close="true"
      :close-on-click-modal="false"
    >
      <AlbumForm @submit="submit"></AlbumForm>
    </el-dialog>
    <div v-if="!canuse" class="noauthor text-center p-20">
      <el-card>
        <p>请配置七牛相关信息</p>
      </el-card>
    </div>
  </div>
</template>
<script lang="ts">
import { Component, Vue, Watch } from "vue-property-decorator";
import Title from "@/components/Title.vue";
import AlbumTrees from "./components/album-trees.vue";
import AlbumForm from "./components/album-form.vue";
import * as albumHttp from "../../../http/api/console/album";
import { Message } from "element-ui";
import { Album } from "./components/Album";
import * as qiniuHttp from '@/http/api/console/qiniu';

@Component({
  components: {
    Title,
    AlbumTrees,
    AlbumForm,
  },
})
export default class CAlbum extends Vue {
  loading = false;
  visible = false;
  canuse = false;
  submit(v) {
    v.parentId = 0;
    albumHttp.addAlbum(v).then((res) => {
      this.getAlbum(0);
      Message.success("添加成功");
      this.visible = false;
    });
  }
  fileData: Array<Album> = [];

  created() {
    this.getAlbum(0);
    this.imgcanuse();
  }

  imgcanuse() {
    qiniuHttp.canuse().then((res: any) => {
      this.canuse = res;
    })
  }

  getAlbum(id) {
    albumHttp.getAlbum(id).then((res: any) => {
      this.fileData = res;
    });
  }

  childDeleted(v, index) {
    this.fileData.splice(index, 1);
  }
}
</script>
<style lang="scss" scoped>
@import "../../../assets/styles/public.scss";
</style>
