<style lang="less">
.avatar {
  border-radius: 50%;
}
.green {
  color: green;
}
</style>
<template>
  <div class="table-basic-vue frame-page h-panel w-800">
    <div class="h-panel-bar">
      <span class="h-panel-title">团列表</span>
    </div>
    <div class="h-panel-body">
      <Table :loading="loading" :datas="datas">
        <TableItem prop="id" title="ID"></TableItem>
        <TableItem title="团长">
          <template slot-scope="{ data }">
            <span>{{data.create_user_name}}</span>
          </template>
        </TableItem>
        <TableItem title="成员">
          <template slot-scope="{ data }">
            <div v-for="user in data.users" :key="user.id">
              <span>{{user.user.nick_name}}</span>
            </div>
          </template>
        </TableItem>
        <TableItem prop="status_text" title="状态"></TableItem>
        <TableItem prop="people_num" title="团购人数" unit="人"></TableItem>
        <TableItem title="还需人数">
          <template slot-scope="{ data }">
            <span>{{data.over_people_num}}人</span>
          </template>
        </TableItem>
        <TableItem prop="expired_at" title="结束时间"></TableItem>
        <TableItem title="操作" align="center" :width="200">
          <template slot-scope="{ data }">
            <p-del-button
              permission="addons.TuanGou.goods.complete"
              v-if="data.status === 3"
              text="改为已完成"
              @click="completeAction(datas, data)"
            ></p-del-button>
          </template>
        </TableItem>
      </Table>

      <Pagination
        class="mt-10"
        v-if="pagination.total > 0"
        align="right"
        v-model="pagination"
        @change="changePage"
      />
    </div>
  </div>
</template>
<script>
export default {
  props: ['id'],
  data() {
    return {
      pagination: {
        page: 1,
        size: 10,
        total: 0
      },
      datas: [],
      loading: false
    };
  },
  mounted() {
    this.init();
  },
  methods: {
    init() {
      this.getData(true);
    },
    changePage() {
      this.getData();
    },
    getData(reload = false) {
      if (reload) {
        this.pagination.page = 1;
      }
      this.loading = true;
      let data = this.pagination;
      data.id = this.id;
      R.Extentions.tuanGou.Goods.Items(data).then(resp => {
        this.datas = resp.data.data.data;
        this.pagination.total = resp.data.data.total;
        this.pagination.page = resp.data.data.current_page;
        this.pagination.size = resp.data.data.per_page;
        this.loading = false;
      });
    },
    completeAction(datas, data) {
      R.Extentions.tuanGou.Goods.CompleteItem({ id: data.id }).then(res => {
        HeyUI.$Message.success('操作成功');
        this.getData();
      });
    }
  }
};
</script>
