<template>
    <div class="modal-dialog modal-dialog-centered modal-lg">
        <div class="modal-content">
            <form role="form" ref="form" @keydown="form.onKeydown($event)" @submit.prevent='add'>
                <div class="modal-header text-center">
                    <h3 class="modal-title display-4 font-weight-bold small ">Edit Product Price</h3>
                    <button type="button" @click="closeAddComponent" class="close" data-dismiss="modal">&times;</button>
                </div>
                <div class="card card-primary card-outline">

                    <div class="card-body">
                        <div class="modal-body pt-0 pb-0">
                            <div class="container-fluid">

                                <div class="row">
                                    <div class="col-md-12">
                                        <fieldset class="border border-warning p-2">

                                            <legend class="w-auto small font-weight-bold border bg-warning">Product Info
                                            </legend>
                                            <div class="form-group">
                                                <label for="product">Item</label>
                                                <input disabled="" type=text v-model="product.product" required=""
                                                    class="form-control" ref="product" id="product"
                                                    placeholder="Enter product name">
                                            </div>
                                            <div class="form-group">
                                                <label for="quantity">Quantity</label>
                                                <input disabled="" type=number v-model="product.stock" required=""
                                                    class="form-control" ref="quantity" id="quantity"
                                                    placeholder="Enter quantity">
                                            </div>
                                            <div class="form-group">
                                                <label for="price">Price</label>
                                                <input step="0.01" type=number v-model="form.purchase_price" required=""
                                                    class="form-control" ref="price" id="price"
                                                    placeholder="Enter purchase price">
                                            </div>
                                            <div class="form-group">
                                                <label for="price">Sale Price</label>
                                                <input step="0.01" type=number v-model="form.sale_price" required=""
                                                    class="form-control" ref="sale_price" id="sale_price"
                                                    placeholder="Enter Selling price">
                                            </div>
                                        </fieldset>
                                    </div>
                                    <!-- <div class="col-md-6 ml-auto">
                                        <fieldset class="border border-warning p-2">
                                            <legend class="w-auto small font-weight-bold border bg-warning">Others
                                            </legend>
                                            <div class="form-group">
                                                <label for="brand">Brand </label>
                                                <select disabled="" v-model="product.brand" ref="brand" required
                                                    class="custom-select mr-sm-2" id="brand">
                                                    <option v-if="brands != ''" v-for="brand in brands"
                                                        v-bind:value="brand.type"> {{ brand.type }} </option>
                                                </select>
                                            </div>
                                            <div class="form-group">
                                                <label for="category">Category </label>
                                                <select disabled="" v-model="product.category" ref="category" required
                                                    class="custom-select mr-sm-2" id="category">
                                                    <option v-if="categories != ''" v-for="category in categories"
                                                        v-bind:value="category.name"> {{ category.name }} </option>
                                                </select>
                                            </div>
                                            <div class="form-group">
                                                <label for="size">Size </label>
                                                <select disabled="" v-model="product.size" ref="size" required
                                                    class="custom-select mr-sm-2" id="size">
                                                    <option v-if="sizes != ''" v-for="size in sizes"
                                                        v-bind:value="size.name"> {{ size.name }} </option>
                                                </select>
                                            </div>
                                            <div class="form-group">
                                                <label for="unit">Unit </label>
                                                <select disabled="" v-model="product.unit" ref="pku" required
                                                    class="custom-select mr-sm-2" id="unit">
                                                    <option v-if="units != ''" v-for="unit in units"
                                                        v-bind:value="unit.name"> {{ unit.name }} </option>
                                                </select>
                                            </div>
                                        </fieldset>
                                    </div> -->
                                </div>
                            </div>
                        </div>

                    </div>


                </div>
                <div class="modal-footer border bordr border-top-0 border-primary">
                    <button @click="$emit('close_edit_product')" type="button" ref="closeButton" class="btn btn-danger"
                        data-dismiss="modal">Close</button>
                    <button type="submit"
                        :disabled="form.busy || (form.sale_price == product.price && form.purchase_price == product.purchase_price)"
                        class="btn btn-primary">
                        Save changes
                    </button>
                </div>
            </form>
        </div>
    </div>

</template>
<script>

export default {
    mounted() {

    },
    data() {
        return {
            form: new Form({
                sale_price: this.product.price,
                purchase_price: this.product.purchase_price,
                percent_sale: 0,
            }),
            error: '',
            brands: '',
            categories: '',
            units: '',
            sizes: ''
        }
    },
    props: ['product'],

    created() {

    },
    beforeDestroy() {
        // this.$refs.closeButton.click();
        this.form.reset();
    },
    methods: {
        add() {
            this.product.price = this.form.sale_price;
            this.product.purchase_price = this.form.purchase_price;
            this.$emit('edit_product', this.form, this.product);
            Fire.$emit('product_edited', this.product);
            return true
        },
        closeAddComponent() {
            // window.dispatchEvent(new Event('close_sidebar_min'));
            this.$emit('close_edit_product');
            return true;
        },
        loadBrands() {
            this.form.get('/attributes')
                .then(response => {
                    this.brands = response.data.data.item
                })
        },
        loadCategories() {
            this.form.get('/categories')
                .then(response => {
                    this.categories = response.data.data.item
                })
        },
        loadSizes() {
            this.form.get('/sizes')
                .then(response => {
                    this.sizes = response.data.data.item
                })
        },
        loadUnits() {
            this.form.get('/units')
                .then(response => {
                    this.units = response.data.data.item
                })
        },
    }
}

</script>