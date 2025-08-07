<template>
  <div class="modal-dialog modal-dialog-centered modal-lg">
    <div class="modal-content">
      <div class="modal-header text-center">
        <h3 id="modalTitle" class="modal-title display-5 font-weight-bold">
          <i class="fa fa-arrow-up text-success"></i> Top up Product(s)
        </h3>
        <button type="button" @click.prevent="closeComponent" class="close" data-dismiss="modal">&times;</button>
      </div>
      <div class="card card-primary card-outline">
        <form role="form" ref="form" @keydown="form.onKeydown($event)" @submit.prevent='add(product)'>
          <div class="card-body">
            <div class="modal-body p-0">
              <div class="container-fluid">
                <div class="row">
                  <div class="p-0 m-0">
                    <fieldset class="border border-info p-1">
                      <legend class="p-2 w-auto small font-weight-bold border text-info">Add product to cart <i
                          class="fa fa-shopping-cart "></i></legend>
                      <div class="form-row">
                        <div class="col-12 col-md-8 col-lg-6">
                          <label class="small" for="">Input name of item</label>
                          <input v-model="name" list="products" class="form-control" required="" type="search"
                            ref="name" placeholder="Ex: 300-18 Sports Ordinary tyre">
                          <datalist ref="datalist" id="products">
                          </datalist>
                        </div>
                        <div class="col-12 col-md-4 col-lg-6">
                          <label class="small" for="">Quantity</label>
                          <input type="number" title="How many pc(s) do you want to top up" v-model="cartItem.quantity"
                            required="" min="1" class="form-control" ref="quantity"
                            placeholder="Enter quantity of items">
                          <label v-if="product.stock != undefined" class="small text-danger">Remaining
                            {{ product.stock }}
                            pc(s)</label>
                        </div>
                      </div>
                      <div class="form-row">
                        <div class="col-6">
                          <label class="small" for="">Cost Price</label>
                          <input class="form-control" type="number" title="What's the cost price?"
                            v-model="product.purchase_price" ref="purchase_price" required=""
                            placeholder="Enter cost price">
                          <!-- <label v-if="product.price != undefined" class="small text-danger">Selling at <span
                              style="text-decoration: line-through"> N</span>{{ $root.numeral(product.price) }}</label> -->
                        </div>
                        <div class="col-6">
                          <label class="small" for="">Sale Price</label>
                          <input class="form-control" type="number" title="you can change the sale price if you may"
                            v-model="product.price" ref="sale_price" required="" placeholder="Enter sale price">
                          <!-- <label v-if="product.price != undefined" class="small text-danger">Selling at <span
                              style="text-decoration: line-through"> N</span>{{ $root.numeral(product.price) }}</label> -->
                        </div>

                      </div>
                      <div class="text-center mt-3 m-2">
                        <button type="submit" @submit.prevent='add(product)' ref="nextStep"
                          class="font-weight-bold btn btn-block btn-info small">Add to Cart <i
                            class="fa fa-shopping-cart "></i></button>
                      </div>
                    </fieldset>
                  </div>
                </div>
                <div style="max-height: 37vh;  overflow: auto; " class="row mt-3">
                  <div class="col-sm-12 p-0 m-0">
                    <h3>Bill Adjustment
                      <i class="fas fa-cog fa-spin text-info"></i>
                    </h3>
                    <div class="clearfix"></div>
                    <div class="table-responsive">
                      <table
                        class="table border border-info table-bordered table-small table-striped table-hover dataTable">
                        <thead class="bg-info">
                          <tr role="row ">
                            <th class="text-center p-1">Index</th>
                            <th class="text-center p-1">Name</th>
                            <th class="text-center p-1">Quantity</th>
                            <th class="text-center p-1">Cost price</th>
                            <th class="text-center p-1">Sale price</th>
                            <th class="text-center p-1">Amount</th>
                          </tr>
                        </thead>
                        <tbody id="body">
                          <tr draggable
                            @dragend="deleteItemOnCart(item, index, { index: item.index, quantity: item.quantity })"
                            @dblclick="deleteItemOnCart(item, index, { index: item.index, quantity: item.quantity })"
                            title="double click or swipe to remove this item from cart" v-for="item, index in cart">
                            <td class="text-center p-1 small text-capitalize">{{ index + 1 }}</td>
                            <td class="text-center p-1 small text-capitalize">{{ item.name }}</td>
                            <td @input="editItemOnCart($event, item, index, 'quantity', item.index)"
                              @keypress.enter.prevent="" contenteditable=""
                              class="text-center p-1 small text-capitalize">{{ item.quantity }}</td>
                            <td @input="editItemOnCart($event, item, index, 'purchase_price', item.index)"
                              @keypress.enter.prevent="" contenteditable=""
                              class="text-center p-1 small text-capitalize">{{ item.purchase_price }} </td>
                            <td @input="editItemOnCart($event, item, index, 'sale_price', item.index)"
                              @keypress.enter.prevent="" contenteditable=""
                              class="text-center p-1 small text-capitalize">{{ item.sale_price }} </td>
                            <td :ref="'amount' + item.index" :key="item.index"
                              class="text-center p-1 small text-capitalize">{{ $root.numeral(item.amount) }} </td>
                          </tr>
                          <tr v-if="cart.length == 0">
                            <td colspan="6">
                              <li>
                                <h4 class="text-center small text-secondary">No item(s) in cart yet, add items</h4>
                              </li>
                            </td>
                          </tr>
                        </tbody>
                        <tfoot>
                          <tr>
                            <!-- no scroll row -->
                        

                            <td colspan="6" class="">
                              <h4> <span class="float-right">Total amount: <span class="text-success"
                                    style="text-decoration: line-through">N</span>
                                  {{ $root.numeral(CartTotalAmount) }}</span></h4>
                            </td>
                          </tr>
                        </tfoot>
                      </table>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </form>
        <div class="modal-footer border pt-0 pb-1 border-top-0 border-primary">
          <button @click="closeComponent" type="button" ref="closeButtonPurchase" class="btn btn-danger"
            data-dismiss="modal">Close</button>
          <button type="submit" @click.prevent="processCheckout" :disabled="cart.length == 0"
            class="btn btn-info">Checkout</button>
        </div>
      </div>
    </div>
    <!-- <div class="modal fade" @closeAddPurchase = "closeComponent" @purchaseComplete = "emptyCart" @closeAddProduct = "closeComponent" v-if = "addProductShow" id="addProductComponent"><add-product-component :cart = "cart" v-if = "addProductShow" ></add-product-component></div> -->
  </div>
</template>
<script>
export default {
  created() {
    window.dispatchEvent(new Event('sidebar_min'))
    if (localStorage.PurchaseCart) {
      this.cart = JSON.parse(localStorage.PurchaseCart)
    }

  },
  mounted() {
    this.addProductShow = false
  },
  data() {
    return {
      form: new Form(),
      name: "",
      product: {},
      cart: [],
      editMode: false,
      cartCopy: [],
      product: {},
      cartItem: {},
      index: "",
      addProductShow: false,
      CartTotalAmount: 0,
      productHash: new Set(),
      checkout_status: false,
    }
  },
  props: ['products'],
  
  beforeDestroy() {
    this.product = {};
    this.product.quantity = ""
    this.form.reset();
    if (this.cart.length > 0) {
      localStorage.PurchaseCart = JSON.stringify(this.cart)
    }
    else {
      localStorage.removeItem("PurchaseCart")
    }

    // this.$emit('closingAddPurchaseCart')
    window.dispatchEvent(new Event('close_sidebar_min'))
    this.$refs.closeButtonPurchase.click();



  },
  computed: {
    cartLoader() {
      return this.cart
    },
    amount() {
     
      return this.cartItem.purchase_price != "" && this.cartItem.quantity != "" ? this.cartItem.purchase_price * this.cartItem.quantity : 0
    },
    productsRevised() {
      return this.products.map(({ product, stock }) => ({ product, stock }))
    }
  },
  watch: {
    name() {
      this.search()
      this.product = {}
      this.cartItem = {}
      if (this.name == "") {
        this.product = {}
        this.cartItem = {}
        return
      }

      this.products.some((item, index) => {
        if (item.product == this.name) {
          this.product = item
          this.cartItem.index = index
          this.cartItem.brand = item.brand
          this.cartItem.size = item.size
          this.cartItem.category = item.category
          this.cartItem.pku = item.unit
          this.cartItem.product = item.name
          this.cartItem.name = item.product
          this.cartItem.pku = item.unit
          this.cartItem.sale_price = item.price
          this.cartItem.price = item.purchase_price
          this.cartItem.purchase_price = item.purchase_price
          this.index = index
          return true
        }
      })


    },

    cart() {
      this.CartTotalAmount = this.cart.sum('amount')
    }
  },
  methods: {
    add() {
      this.cartItem.amount = this.amount
      this.cartItem.sale_price = this.product.price
      this.cartItem.price = this.product.purchase_price
      this.cartItem.purchase_price = this.product.purchase_price
      this.cart.unshift(this.cartItem)
      
      this.cartItem = {}
      this.name = ""
      this.product = {}
      this.quantity = ""
      this.$root.alert('success', 'success', 'item added to cart')
    },
    loadProducts() {
      this.$emit('load_products')
    },
    closeComponent() {
      if (this.cart.length > 0 && !this.checkout_status) {
        this.$root.alert('warning', 'CAUTION', "You have some items in the purchase cart")
        localStorage.PurchaseCart = JSON.stringify(this.cart)
      }

      this.name = ""
      this.product = {};
      this.form.reset();

      // this.$refs.closeButtonPurchase.click();
      this.$emit('closingAddPurchaseCart')

      this.addProductShow = false
    },
    editItemOnCart(e, item, index, type, productIndex) {
      this.$Progress.start();
      if (e.target.innerHTML == "") {
        this.$Progress.finish()
        return
      }
      this.editMode = true
      let value = parseInt(e.target.innerHTML)
      if (type == "sale_price") {
        this.cart[index].sale_price = value
      } else if (type == "quantity") {
        this.cart[index].quantity = value
      }
      else if (type == "purchase_price") {
        this.cart[index].purchase_price = value
        this.cart[index].price = value
      }
      this.$refs['amount' + productIndex][0].key = productIndex + 1
      this.cart[index].amount = this.cart[index].quantity * this.cart[index].purchase_price
      this.$refs['amount' + productIndex][0].innerHTML = this.cart[index].amount
      this.CartTotalAmount = this.cart.sum('amount')
      this.$Progress.finish()
    },
    deleteItemOnCart(data, index, productIndex) {
      this.$Progress.start();
      this.$swal({
        title: 'Remove Item from Cart',
        text: "You are about to delete an item from cart",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, go ahead!'
      })
        .then((result) => {
          if (result.value) {
            if (this.cart != '') {
              this.cart.splice(index, 1);
              this.$emit('updateProduct', productIndex)
              localStorage.PurchaseCart = JSON.stringify(this.cart)
              // this.products[productIndex.index].stock = parseInt(this.products[productIndex.index].stock) + parseInt(productIndex.quantity)
              this.$root.alert('success', 'success', 'product removed from cart')

            } else { console.log('cart not loaded'); }
          }
          this.$Progress.finish()

        })


      this.$Progress.finish()
    },
    emptyCart(data) {
      this.cart = data
      localStorage.removeItem("purchaseCart")
    },
    processCheckout() {
      this.$Progress.start();
      this.$swal({
        title: 'Checkout Bill',
        text: "Do you want to proceed to Checkout",
        icon: 'info',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, Checkout!'
      })
        .then((result) => {
          if (result.value) {
            this.$root.PurchaseCart = this.cart
            this.$root.alert('success', 'success', 'proceeding to checkout')
            this.$Progress.finish()
            this.checkout_status = true
            this.$router.replace("/billing_purchase")

          } else {
            this.$refs.closeButtonPurchase.click();
            this.addProductShow = false
          }
          this.$Progress.finish()

        })
      this.$Progress.finish()
    },
    search() {
      var datalist = this.$refs.datalist
      var term = this.name.toLowerCase()
      var found = 0
      var frag = document.createDocumentFragment()
      for (var child of [].slice.apply(datalist.childNodes)) {
        datalist.removeChild(child)
      }
      for (var item of this.productsRevised) {

        if (item.product.toLowerCase().includes(term.trim().toLowerCase())) {
          let option = document.createElement("option")
          option.value = item.product
          option.innerHTML = "Remaining " + item.stock
          frag.appendChild(option)
          found++
        }

        if (found == 10) break

      }
      datalist.appendChild(frag)

    }
  },
}

</script>
