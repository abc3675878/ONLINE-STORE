<template>
  <div>
    <loading :active.sync="isLoading"></loading>
    <div class="my-5 row justify-content-center">
      <form class="col-md-6" @submit.prevent="payOrder">
        <table class="table">
          <thead>
            <th>品名</th>
            <th>數量</th>
            <th>單價</th>
          </thead>
          <tbody>
            <tr v-for="item in order.products" :key="item.id">
              <td class="align-middle">{{ item.product.title }}</td>
              <td class="align-middle">
                {{ item.qty }}/{{ item.product.unit }}
              </td>
              <td class="align-middle text-right">{{ item.final_total }}</td>
            </tr>
          </tbody>
          <tfoot>
            <tr>
              <td colspan="2" class="text-right">總計</td>
              <td class="text-right">{{ order.total }}</td>
            </tr>
          </tfoot>
        </table>

        <table class="table">
          <tbody>
            <tr>
              <th width="100">Email</th>
              <td>{{ order.user.email }}</td>
            </tr>
            <tr>
              <th>姓名</th>
              <td>{{ order.user.name }}</td>
            </tr>
            <tr>
              <th>收件人電話</th>
              <td>{{ order.user.tel }}</td>
            </tr>
            <tr>
              <th>收件人地址</th>
              <td>{{ order.user.address }}</td>
            </tr>
            <tr>
              <th>付款狀態</th>
              <td>
                <span v-if="!order.is_paid">尚未付款</span>
                <span v-else class="text-success">付款完成</span>
              </td>
            </tr>
          </tbody>
        </table>
        <!-- 此按鈕只有在order.is_paid = false 時顯示 -->
        <div class="text-right" v-if="order.is_paid === false">
          <button class="btn btn-danger">確認付款去</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";

export default {
  data() {
    return {
      orderId: "",
      order: {
        user: {}
      },
      isLoading: false
    };
  },
  components: {
    Loading
  },
  methods: {
    getOrder() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/abc3675878/order/${this.orderId}`;
      this.$http.get(api).then(res => {
        console.log("取得訂單資料", res.data);
        this.order = res.data.order;
        this.isLoading = false;
      });
    },
    payOrder() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/abc3675878/pay/${this.orderId}`;
      this.$http.post(api).then(res => {
        console.log("付款完成", res.data);
        this.order.is_paid = true;
        this.isLoading = false;
      });
    }
  },
  // 在一開始就抓出order id
  created() {
    this.orderId = this.$route.params.orderId;
    this.getOrder();
  }
};
</script>
