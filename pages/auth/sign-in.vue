<template>
  <div class="hero-static d-flex align-items-center">
    <div class="w-100">
      <div class="content content-full bg-white">
        <div class="row justify-content-center">
          <div class="col-md-8 col-lg-6 col-xl-4 py-4">
            <!-- Header -->
            <div class="text-center">
              <p class="mb-2">
                <i class="fa fa-2x fa-circle-notch text-primary"></i>
              </p>
              <h1 class="h4 mb-1">
                Sign In
              </h1>
              <h2 class="h6 font-w400 text-muted mb-3">
                Please Sign In to Continue
              </h2>
            </div>
            <!-- END Header -->

            <!-- Sign In Form -->
            <ValidationObserver v-slot="{ handleSubmit }">
              <form @submit.prevent="handleSubmit(onLogin)">
                <div class="py-3">
                  <ValidationProvider rules="required" v-slot="{ errors }">
                    <div class="form-group">
                      <input type="text" class="form-control form-control-lg form-control-alt" id="login-username" name="username" placeholder="Username" v-model="username">
                      <span class="text-danger">{{ errors[0] }}</span>
                    </div>
                  </ValidationProvider>
                  
                  <ValidationProvider rules="required" v-slot="{ errors }">
                    <div class="form-group">
                      <input type="password" class="form-control form-control-lg form-control-alt" id="login-password" name="password" placeholder="Password" v-model="password">
                    </div>
                    <span class="text-danger">{{ errors[0] }}</span>
                  </ValidationProvider>
                </div>
                <div class="form-group row justify-content-center mb-0">
                  <div class="col-md-6 col-xl-5">
                    <button type="submit" class="btn btn-block btn-primary">
                      <i class="fa fa-fw fa-sign-in-alt mr-1"></i> Sign In
                    </button>
                  </div>
                </div>
              </form>
            </ValidationObserver>
            
            <!-- END Sign In Form -->
          </div>
        </div>
      </div>

      <div class="font-size-sm text-center text-muted py-3">
        Built with <i class="fa fa-heart text-danger"></i> by <a class="font-w600" href="https://github.com/DeVoresyah" target="_blank">Rully Ardiansyah</a> <br>
        &copy;<span data-toggle="year-copy">2020</span> - <strong>Alfredo</strong>
      </div>
    </div>
  </div>
</template>

<script>
import { ValidationProvider, ValidationObserver, extend } from 'vee-validate'
import { required } from 'vee-validate/dist/rules'
import { mapState, mapActions } from 'vuex'

extend('required', {
  ...required,
  message: `This field is required.`
})

export default {
  middleware: 'authenticated',
  layout: 'auth',
  head() {
    return {
      title: `Sign In - ${process.env.npm_package_name}`
    }
  },
  components: {
    ValidationProvider,
    ValidationObserver
  },
  data() {
    return {
      username: '',
      password: ''
    }
  },
  computed: {
    ...mapState({
      loginStatus: state => state.auth.doLogin
    })
  },
  methods: {
    onLogin() {
      const { username, password } = this

      const dataToSend = {
        username,
        password
      }

      this.doLogin(dataToSend)
    },
    ...mapActions({
      doLogin: 'auth/doLogin'
    })
  }
}
</script>

<script>
import moment from 'moment'
import TextUtil from '../../lib/TextUtil'
import { ValidationProvider, ValidationObserver, extend } from 'vee-validate'
import { required } from 'vee-validate/dist/rules'
import { mapState, mapActions } from 'vuex'

extend('required', {
  ...required,
  message: `This field is required.`
})

export default {
    middleware: 'category/index',
    head() {
        title: 'Add Product - Admin'
    },
    components: {
        ValidationProvider,
        ValidationObserver
    },
    data() {
        return {
            title: '',
            price: '',
            desc: '',
            stock: '',
            category: '',
            imgPreview: '',
            thumbnail: null,
            errorThumbnail: ''
        }
    },
    computed: {
        priceChange: {
            get() {
                return this.price
            },
            set(value) {
                const regex = /^[0-9\.]*$/g
                this.price = regex.test(value) ? this.formatIdr(value) : ""
            }
        },
        ...mapState({
            addStatus: state => state.product.doAdd,
            categories: state => state.category.list
        })
    },
    methods: {
        formatDate: (date) => moment(new Date(date)).format('YYYY/MM/DD'),
        formatIdr: (int) => new TextUtil().formatMoney(int),
        moneyToInt: (int) => new TextUtil().moneytoInt(int),
        onFileChange(e) {
            const files = e.target.files || e.dataTransfer.files;
            if (!files.length)
                return;
            this.thumbnail = files[0]
            this.createImage(files[0])
            this.errorThumbnail = ""
        },
        createImage(file) {
            const image = new Image()
            const reader = new FileReader()
            const vm = this

            reader.onload = (e) => {
                vm.imgPreview = e.target.result
            };
            reader.readAsDataURL(file)
        },
        removeImage(e) {
            this.imgPreview = ''
            this.thumbnail = null
        },
        onSubmit() {
            const { title, price, desc, stock, category, thumbnail } = this

            if (!this.thumbnail) {
                this.errorThumbnail = "Please select thumbnail product"
                return false
            }

            const dataToSend = new FormData()
            dataToSend.append("thumbnail[thumbnail]", thumbnail)
            dataToSend.append("price", this.moneyToInt(price))
            dataToSend.append("title", title)
            dataToSend.append("desc", desc)
            dataToSend.append("stock", stock)
            dataToSend.append("category", category)

            this.addProduct(dataToSend)
        },
        ...mapActions({
            addProduct: 'product/addProduct'
        })
    }
}
</script>
// hacktoberfest

<style>

</style>
