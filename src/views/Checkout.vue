<template>
  <div>
    <div class="container">
      <div class="row my-5">
        <div class="col-sm-12">
          <img
            style="display:block; margin:auto;"
            src="../assets/images/final logo white.png"
            width="200px"
          />
        </div>
      </div>
      <ValidationObserver v-slot="{ invalid }">
        <div class="row">
          <div
            class="col-12 col-md-7 px-4 py-3 py-sm-0 d-flex align-content-center justify-content-between flex-column"
          >
            <p style="font-size:20px;">SHIPPING INFORMATION</p>

            <div class="w-100 mt-3">
              <ValidationProvider
                rules="required|email"
                v-slot="{ failed, errors }"
              >
                <label
                  class="d-flex justify-content-between p-0 m-0"
                  for="Email"
                >
                  <p>Email *</p>
                  <span v-if="failed" class="text-danger">{{ errors[0] }}</span>
                </label>
                <input v-model="email" type="text" id="Email" class="w-100" />
              </ValidationProvider>
            </div>
            <div class="w-100 mt-3">
              <ValidationProvider
                name="Full Name"
                rules="required"
                v-slot="{ failed, errors }"
              >
                <label
                  class="d-flex justify-content-between p-0 m-0"
                  for="fullName"
                >
                  <p>Full Name *</p>
                  <span v-if="failed" class="text-danger">{{ errors[0] }}</span>
                </label>
                <input
                  v-model="fullName"
                  type="text"
                  id="fullName"
                  class="w-100"
                />
              </ValidationProvider>
            </div>
            <div class="w-100 mt-3">
              <ValidationProvider
                name="Address"
                rules="required"
                v-slot="{ failed, errors }"
              >
                <label
                  class="d-flex justify-content-between p-0 m-0"
                  for="address"
                >
                  <p>Address *</p>
                  <span v-if="failed" class="text-danger">{{ errors[0] }}</span>
                </label>
                <input
                  v-model="address"
                  type="text"
                  id="address"
                  class="w-100"
                />
              </ValidationProvider>
            </div>
            <div class="w-100 mt-3">
              <ValidationProvider
                name="Phone Number"
                rules="digits:10|required"
                v-slot="{ failed, errors }"
              >
                <label
                  class="d-flex justify-content-between p-0 m-0"
                  for="Phone Number"
                >
                  <p>Phone Number *</p>
                  <span v-if="failed" class="text-danger">{{ errors[0] }}</span>
                </label>
                <input
                  v-model="phoneNumber"
                  type="text"
                  maxlength="10"
                  id="Phone Number"
                  class="w-100"
                />
              </ValidationProvider>
            </div>
            <div class="w-100 mt-3">
              <label for="Special Instructions">Special Instructions</label>
              <textarea
                v-model="specialInstructions"
                id="Special Instructions"
                cols="30"
                rows="5"
                class="w-100"
              ></textarea>
            </div>
          </div>
          <div
            style="max-height: 600px;"
            class="col-12 col-md-5 bg_grey d-flex justify-content-between flex-column py-4 px-4 h-100"
          >
            <loading
              :active.sync="isLoading"
              :is-full-page="false"
              :opacity="0.8"
              :can-cancel="true"
              loader="bars"
            ></loading>
            <!-- 購物車內容 -->
            <div class="cart-content h-75">
              <div
                v-for="item in cart.data.carts"
                :key="item.id"
                class="cart-product d-flex mb-3"
              >
                <div class="cart-img">
                  <img :src="item.product.imageUrl" alt="" />
                </div>
                <div
                  class="cart-details d-flex flex-column justify-content-between"
                >
                  <div style="font-weight:bold;" class="product-title">
                    {{ item.product.title }}
                  </div>
                  <div class="product-size">{{ item.product_size }}</div>
                  <div
                    class="product-bottom d-flex align-items-center justify-content-end"
                  >
                    <div class="product-price ">
                      {{ item.product.price | currency }}
                    </div>
                  </div>
                </div>
              </div>
            </div>
            <hr />

            <!-- 購物車底部 -->
            <div class="cart-bottom h-25">
              <div
                v-if="cart.data.total === cart.data.final_total"
                class="cart-price d-flex justify-content-between"
              >
                <input
                  type="text"
                  class="w-75"
                  placeholder="Discount Code"
                  v-model="discountCode"
                />
                <button
                  style="font-size: 12px"
                  class="w-25"
                  @click="addDiscount"
                >
                  APPLY
                </button>
              </div>
              <div v-else class="w-100 bg-danger text-center p-1">
                <span class="text-white"
                  >已套用優惠券 {{ cart.data.carts[0].coupon.code }}</span
                >
              </div>
              <hr />
              <div class="cart-price d-flex justify-content-between">
                <p>Subtotal</p>
                <p>{{ cart.data.total | currency }}</p>
              </div>
              <div
                v-if="cart.data.total !== cart.data.final_total"
                class="cart-price d-flex justify-content-between"
              >
                <p style="color:#ff0000;">Discount</p>
                <p style="color:#ff0000;">- {{ discountValue | currency }}</p>
              </div>
              <div class="cart-price d-flex justify-content-between">
                <p>Total</p>
                <p>{{ cart.data.final_total | currency }}</p>
              </div>
              <button
                :disabled="invalid"
                :class="{ disabledBtn: invalid }"
                @click="placeOrder"
              >
                <div
                  class="h-100 d-flex align-items-center justify-content-center"
                  v-if="!cart_loading"
                  :class="{ disabledBtn: invalid }"
                  :disabled="invalid"
                  style="color: white"
                >
                  PLACE ORDER
                </div>
                <div
                  v-else
                  class="spinner-border text-light"
                  style="width: 1.5rem; height: 1.5rem;"
                  role="status"
                >
                  <span class="sr-only">Loading...</span>
                </div>
              </button>
            </div>
          </div>
        </div>
      </ValidationObserver>
      <div class="row">
        <div class="col my-5"></div>
      </div>
    </div>
  </div>
</template>

<script>
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/vue-loading.css";
export default {
  components: {
    Loading
  },
  data() {
    return {
      isLoading: false,
      cart_loading: false,
      cart: {
        data: {
          carts: [],
          total: 0
        }
      },
      email: "",
      fullName: "",
      address: "",
      phoneNumber: "",
      specialInstructions: "",
      discountCode: "",
      discountConfirm: {}
    };
  },
  filters: {
    // 價格千分號
    currency: function(num) {
      const n = Number(num);
      return `¥ ${n.toFixed(0).replace(/./g, (c, i, a) => {
        const currency =
          i && c !== "." && (a.length - i) % 3 === 0
            ? `, ${c}`.replace(/\s/g, "")
            : c;
        return currency;
      })}`;
    }
  },
  computed: {
    discountValue() {
      return this.cart.data.total - this.cart.data.final_total;
    }
  },
  methods: {
    placeOrder() {
      this.cart_loading = true;
      const api = `${process.env.VUE_APP_API}api/abc3675878/order`;
      const order = {
        user: {
          name: this.fullName,
          email: this.email,
          tel: this.phoneNumber,
          address: this.address
        },
        message: this.specialInstructions
      };
      this.$http.post(api, { data: order }).then(res => {
        console.log("送出訂單", res.data);
        const orderId = res.data.orderId;
        if (res.data.success) {
          this.$router.push({ name: "Order", params: { id: orderId } });
        }
      });
    },
    getCart() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/abc3675878/cart`;
      this.$http.get(api).then(res => {
        this.isLoading = false;
        console.log("取得購物車", res.data);
        this.cart = res.data;
      });
    },
    addDiscount() {
      this.isLoading = true;
      const api = `${process.env.VUE_APP_API}api/abc3675878/coupon`;
      const discount_code = {
        code: this.discountCode
      };

      this.$http.post(api, { data: discount_code }).then(res => {
        this.isLoading = false;
        console.log("套用優惠券", res.data);
        this.discountConfirm = res.data;
        this.getCart();
      });
    }
  },
  created() {
    this.getCart();
  }
};
</script>

<style lang="scss" scoped>
@import "../assets/variables.scss";

* {
  // border: 1px solid black;
  font-size: 12px;
}

input,
textarea {
  border: 1px solid #b1b1b1;
  text-indent: 15px;
}

input {
  height: 40px;
}

.bg_grey {
  background-color: #f2f2f2;
}

.cart-content {
  overflow: auto;
}

.cart-product {
  height: 80px;

  .cart-img {
    width: 80px;
    height: 80px;
    position: relative;
    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      object-position: top;
    }
  }

  .cart-details {
    width: calc(100% - 80px);
    padding-left: 10px;
  }
}

.cart-bottom {
  button {
    width: 100%;
    height: 40px;
    font-size: 14px;
    border: none;
    background: #000 !important;
    background: transparent;
    transition: all 0.3s ease-in-out;
    color: white !important;
    &:hover {
      background: rgb(100, 100, 100) !important;
    }
  }
  .disabledBtn {
    color: white !important;
    border: 1px solid rgb(214, 214, 214) !important;
    background: rgb(214, 214, 214) !important;
    &:hover {
      background: rgb(214, 214, 214) !important;
    }
  }
}
</style>
