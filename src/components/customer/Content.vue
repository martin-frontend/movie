<template>
  <div>
    <loading :active.sync="isLoading"></loading>

    <div>
      <nav
        class="navbar navbar-expand-lg navbar-light fixed-top"
        :class="{bgsolid : scroll >= 100}"
      >
        <a class="px-5" href="#" @click.prevent="category = 'all'">
          <img src="static\logo.png" style="width:140px" />
        </a>

        <button
          class="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
          @click.prevent="doSeacher"
        >
          <i class="fas fa-bars"></i>
        </button>

        <div class="collapse navbar-collapse" id="navbarSupportedContent">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item" @click.prevent="category = '動作片'">
              <a class="nav-link" href="#">動作片</a>
            </li>
            <li class="nav-item" @click.prevent="category = '懸疑片'">
              <a class="nav-link" href="#">懸疑片</a>
            </li>

            <li class="nav-item" @click.prevent="category = '喜劇片'">
              <a class="nav-link" href="#">喜劇片</a>
            </li>

            <li class="nav-item" @click.prevent="category = '恐怖片'">
              <a class="nav-link" href="#">恐怖片</a>
            </li>

            <li class="nav-item dropdown more">
              <a
                class="nav-link dropdown-toggle"
                href="#"
                id="dropdownMenuLink"
                data-toggle="dropdown"
                aria-haspopup="true"
                aria-expanded="false"
                @click.prevent
              >更多</a>
              <div class="dropdown-menu" aria-labelledby="dropdownMenuLink">
                <a class="dropdown-item pr-1" href="#" @click.prevent="category = '動畫片'">動畫片</a>
                <a class="dropdown-item" href="#" @click.prevent="category = 'like'">
                  <i class="fa-heart fa-lg fas pr-2"></i>我的最愛
                </a>
              </div>
            </li>
          </ul>
          <form class="form-inline mr-auto" style="position: relative;">
            <input
              class="form-control"
              type="search"
              aria-label="Search"
              style="border-radius: 10px;"
              v-model="search"
            />
            <button
              class="btn btn-success"
              type="submit"
              style="position: absolute; right:0 ;top:0 ;border-radius: 0 10px 10px 0; height: 100%;"
            >
              <i class="fas fa-search"></i>
            </button>
          </form>
          <ul class="navbar-nav pr-4" style="line-height: 54px">
            <router-link to="/login">
              <li class="nav-item">
                <a class="nav-link" href="#">管理員</a>
              </li>
            </router-link>
            <li class="nav-item" style="line-hight:54px">
              <a class="nav-link" href="#">
                <div class="dropdown">
                  <i
                    aria-hidden="true"
                    class="fa fa-shopping-cart text-light fa-lg"
                    id="dropdownMenuButton"
                    data-toggle="dropdown"
                    aria-haspopup="true"
                    aria-expanded="false"
                    style="position: relative;"
                  >
                    <span
                      class="badge badge-pill badge-danger shop-cart"
                      style="font-size:12px"
                    >{{cartNum}}</span>
                  </i>

                  <div
                    data-offset="400"
                    class="dropdown-menu dropdown-menu-right p-3"
                    style="min-width: 400px;"
                    aria-labelledby="dropdownMenuButton"
                  >
                    <h6 class="text-white">已選擇商品</h6>
                    <table class="table mb-0 text-white">
                      <tbody v-for="item in cart.carts" :key="item.id">
                        <tr>
                          <td class="align-middle text-center">
                            <button
                              type="button"
                              class="btn btn-outline-secondary btn-sm"
                              @click="deleteCart(item.id)"
                            >
                              <i class="far fa-trash-alt"></i>
                            </button>
                          </td>
                          <td class="align-middle">{{ item.product.title }}</td>
                          <td class="align-middle">{{ item.qty }}{{ item.product.unit }}</td>
                          <td class="align-middle text-right">{{ item.final_total | currency}}</td>
                        </tr>
                      </tbody>
                    </table>
                    <div class="d-flex border-top border-bottom mb-3 p-1">
                      <span class="text-uppercase text-muted">Total</span>
                      <strong class="ml-auto text-white">{{ cart.total | currency}}</strong>
                    </div>
                    <router-link to="/customer/cartcontent">
                      <button class="btn btn-success btn-block">
                        <i aria-hidden="true" class="fa fa-cart-plus"></i> 購物車內容
                      </button>
                    </router-link>
                  </div>
                </div>
              </a>
            </li>
          </ul>
        </div>
      </nav>
    </div>

    <h2 class="pb-5 text-center" v-if="category==='all'">每日精選隨你看</h2>
    <h2 class="pb-5 text-center" v-if="category==='動作片'">精選動作片隨你看</h2>
    <h2 class="pb-5 text-center" v-if="category==='懸疑片'">精選懸疑片隨你看</h2>
    <h2 class="pb-5 text-center" v-if="category==='喜劇片'">精選喜劇片隨你看</h2>
    <h2 class="pb-5 text-center" v-if="category==='恐怖片'">精選恐怖片隨你看</h2>
    <h2 class="pb-5 text-center" v-if="category==='動畫片'">精選動畫片隨你看</h2>
    <h2 class="pb-5 text-center" v-if="category==='like'">我的最愛</h2>

    <div class="row d-flex justify-content-center">
      <div
        class="col-xl-2 col-lg-6　py-4 pb-5"
        style="min-width: 290px;max-width:350px; position: relative;"
        v-for="item in selectCategory "
        :key="item.id"
      >
        <div class="pic" style="position: relative">
          <div class="tw-video-brand selfmake-brand text-center">
            <span>{{item.category}}</span>
          </div>
          <div style="max-width:320px;  cursor: pointer;" @click.prevent="openListModal(item.id)">
            <img class="img-fluid" :src="item.imageUrl" alt />
          </div>

          <div class="dis w-100">
            <div
              class="card-body position-relative pt-5 pb-3"
              style="background: linear-gradient(hsla(0,0%,9%,0),#111 40%);"
            >
              <div>
                <h3 class="card-title text-left">{{item.title}}</h3>
                <p class="text-left" style="color:#dcddde">{{item.description}}</p>
                <div class="heart position-absolute" style="bottom:20%;right:5%">
                  <i
                    class="fa-heart fa-lg text-white fas fa-2x"
                    :class="{'text-danger' : item.mylike}"
                    @click="item.mylike = !item.mylike"
                  ></i>
                </div>
                <div class="text-left">
                  <button
                    href="#"
                    class="btn btn-outline-light btn-sm infoBtn mr-1"
                    style="color:#dcddde"
                    @click="openListModal(item.id)"
                  >
                    <i class="fas fa-spinner fa-spin" v-if="lodingItme === item.id"></i>查看內容
                  </button>
                  <button
                    class="btn btn-outline-light btn-sm ml-auto infoBtn"
                    style="color:#dcddde"
                    @click="addtoCart(item.id)"
                  >
                    <i class="fas fa-spinner fa-spin" v-if="lodingItme === item.id"></i>
                    <i aria-hidden="true" class="fa fa-cart-plus"></i> 購物車
                  </button>
                </div>
              </div>
            </div>
          </div>
          <p class="pt-2" style="font-size: 20px;color:#aaa">{{item.title}}</p>
        </div>
      </div>
    </div>

    <div
      class="modal fade"
      id="listModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content border-0" style=" background-color: rgba(30, 30, 30, 1)">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">
              <span>{{product.title}}</span>
            </h5>
            <button type="button" class="close text-white" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body text-center d-xl-flex justify-content-center align-items-center">
            <img :src="product.imageUrl" class="img-fluid" style="width:350px" alt />
            <div class="pl-3">
              <blockquote class="blockquote text-left py-3">
                <p class="mb-0">{{product.content}}</p>
              </blockquote>
              <div class="d-flex justify-content-between align-items-baseline">
                <div class="h5" v-if="!product.price">{{product.origin_price | currency}}元</div>
                <del
                  class="h6 text-danger"
                  v-if="product.price"
                >原價{{product.origin_price | currency}}元</del>
                <div class="h5" v-if="product.price">現在只要{{product.price | currency}}元</div>
              </div>
              <select class="form-control mt-3" v-model="product.num">
                <option v-for="num in 10" :value="num" :key="num">選購 {{num}} {{product.unit}}</option>
              </select>
              <div class="d-flex justify-content-end align-items-center pt-4">
                <div
                  class="text-muted text-nowrap mr-3"
                >小計{{product.price * product.num | currency}}元</div>
                <button
                  type="button"
                  class="btn btn-success"
                  @click="addtoCart(product.id,product.num)"
                >加到購物車</button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import $ from "jquery";

export default {
  data() {
    return {
      search: "",
      cartNum: "",
      cart: [],
      scroll: "0",
      isLoading: false,
      lodingItme: "",
      newProducts: [],
      product: {},
      products: [],
      category: "all"
    };
  },
  mounted() {
    window.addEventListener("scroll", this.handleScroll);
  },
  methods: {
    handleScroll() {
      this.scroll = document.documentElement.scrollTop;
    },
    getProduct() {
      let vm = this;
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/products/all`;
      vm.isLoading = true;
      this.$http.get(api).then(response => {
        console.log(response.data);
        if (response.data.success) {
          // 移除不要的產品(復仇者聯盟,286368)
          const deleteNames = ["復仇者聯盟4","286368"];
          response.data.products = response.data.products.filter(item => !deleteNames.includes(item.title));
          vm.products = response.data.products;
          vm.products.forEach(item => {
            this.$set(item, "mylike", false);
            this.products = response.data.products;
            // this.selectCategory("all");
            vm.isLoading = false;
          });
        }
      });
    },
    openListModal(id) {
      let vm = this;
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/product/${id}`;
      vm.lodingItme = id;
      this.$http.get(api).then(response => {
        $("#listModal").modal("show");
        console.log(response.data);
        vm.product = response.data.product;
        vm.product.num = 1;
        vm.lodingItme = "";
      });
    },
    addtoCart(id, qty = 1) {
      let vm = this;
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
      vm.lodingItme = id;
      const cart = {
        product_id: id,
        qty
      };
      this.$http.post(api, { data: cart }).then(response => {
        console.log(response.data);
        this.getCart();
        vm.lodingItme = "";
        $("#listModal").modal("hide");
      });
    },
    getCart() {
      let vm = this;
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart`;
      this.$http.get(api).then(response => {
        console.log(response.data);
        if (response.data.success) {
          vm.cart = response.data.data;
          vm.cartNum = vm.cart.carts.length;
          // console.log(vm.cart.total);
        }
      });
    },
    deleteCart(id) {
      let vm = this;
      vm.isLoading = true;
      const api = `${process.env.APIPATH}/api/${process.env.CUSTOMPATH}/cart/${id}`;
      this.$http.delete(api).then(response => {
        console.log(response.data);
        if (response.data.success) {
          vm.isLoading = false;
          this.getCart();
        }
      });
    },
    scrollToTop() {
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  },
  computed: {
    selectCategory(category) {
      this.scrollToTop();
      if (this.search === "") {
        if (this.category === "all") {
          return this.products;
        } else {
          let newProducts = [];
          if (this.category === "like") {
            this.products.forEach(item => {
              if (item.mylike === true) {
                console.log(item);
                newProducts.push(item);
              }
            });
            return newProducts;
          }
          this.products.forEach(item => {
            if (item.category === this.category) {
              newProducts.push(item);
            }
          });
          return newProducts;
        }
      } else {
        let vm = this;
        return vm.products.filter(function(user) {
          return user.title.indexOf(vm.search) !== -1;
        });
      }
    }
  },
  created() {
    this.getProduct();
    this.getCart();
  }
};
</script>

<style scoped>
ul,
li {
  list-style: none;
  text-decoration: none;
}
a {
  text-decoration: none;
}
.navbar-light .navbar-toggler {
  color: rgba(0, 190, 6, 1);
  border-color: rgba(0, 190, 6, 1);
}

.navbar {
  z-index: 100;
  background: linear-gradient(#1a1a1a, transparent);
  transition: background-color 0.5s linear;
}
.nav-item .nav-link {
  color: white;
  font-size: 16px;
  position: relative;
}
.nav-item .nav-link:hover,
.nav-item .nav-link:focus {
  color: rgba(0, 190, 6, 1);
}

/* 更多 */
.dropdown-menu {
  background-color: rgba(30, 30, 30, 1);
}
.dropdown-item {
  color: white;
  background-color: rgba(30, 30, 30, 1);
}
.dropdown-item:hover,
.dropdown-item:focus {
  color: rgba(0, 190, 6, 1);
  background-color: none;
}

.more .show {
  display: flex;
}
.shop-cart {
  position: absolute;
  top: -13px;
  right: -13px;
}

.bgsolid {
  background: rgb(19, 19, 19);
}

/* 購物車 */
.dropdown:active .dropdown-menu-right {
  display: block;
}

.table tr:hover {
  background: rgb(63, 62, 62);
}
/* 垃圾桶 */
.fa-trash-alt:hover {
  color: white;
}

/* 圖片放大 */
.pic {
  overflow: hidden;
}
.pic img {
  transform: scale(1, 1);
  transition: all 1s ease-out;
}
.pic:hover img {
  transform: scale(1.2, 1.2);
}
.infoBtn:hover {
  color: #f8f9fa;
  background-color: rgba(0, 190, 6, 1);
  border-color: #f8f9fa;
}

/* 下標顯示 */
.dis {
  position: absolute;
  opacity: 0;
  bottom: -10px;
  z-index: 1000;
  visibility: hidden;
  transition: all 0.7s;
}
.pic:hover .dis {
  opacity: 1;
  transform: scale(1) translateZ(0);
  visibility: visible;
  cursor: pointer;
}

/* 動作片變色 */
.tw-video-brand {
  position: absolute;
  top: 10px;
  right: 6px;
  z-index: 1000;
}
.tw-video-brand.selfmake-brand {
  background-position: -40px 0;
}
.tw-video-brand {
  z-index: 1;
  display: inline-block;
  background: rgb(156, 27, 27);
  width: 60px;
  height: 25px;
  border-radius: 10px;
  transition: background-color 0.5s linear;
}

.pic:hover .tw-video-brand {
  background: #28a745;
}
</style>
