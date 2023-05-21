<template>
  <div class="project-page">
    <section class="dashboard-header pt-5">
      <div class="container mx-auto relative">
       <Navbar />
      </div>
    </section>
    <section class="container mx-auto pt-8">
      <div class="flex justify-between items-center mb-6">
        <div class="w-3/4 mr-6">
          <h2 class="text-4xl text-gray-900 mb-2 font-medium">Dashboard</h2>
          <ul class="flex mt-2">
            <li class="mr-6">
              <nuxt-link
                class="text-gray-500 hover:text-gray-800"
                :to="'/dashboard'"
              >
                Your Projects
              </nuxt-link>
            </li>
            <li class="mr-6">
              <nuxt-link
                class="text-gray-500 hover:text-gray-800 font-bold"
                :to="'/dashboard/transactions'"
              >
                Your Transactions
              </nuxt-link>
            </li>
          </ul>
        </div>
        <div class="w-1/4 text-right">
          <nuxt-link
            :to="'/dashboard/projects/create'"
            class="bg-orange-button hover:bg-green-button text-white font-bold py-4 px-4 rounded inline-flex items-center"
          >
            + Create Campaign
          </nuxt-link>
        </div>
      </div>
      <hr />
      <div class="block mb-2">
        <div class="w-full lg:max-w-full lg:flex mb-4" v-for="transaction in transaction.data " :key="transaction.id">
          <div
            class="border h-48 lg:h-auto lg:w-48 flex-none bg-cover rounded-t lg:rounded-t-none lg:rounded-l text-center overflow-hidden"
            :style="'background-color: #bbb; background-position: center; background-image: url(\'' + $axios.defaults.baseURL + '/' + transaction.campaign.image_url + '\')'"
          ></div>
          <nuxt-link
            :to="'/dashboard/projects/' + transaction.id "
            class=" w-full border-r border-b border-l border-gray-400 lg:border-l-0 lg:border-t lg:border-gray-400 bg-white rounded-b lg:rounded-b-none lg:rounded-r p-8 flex flex-col justify-between leading-normal"
          >
            <div class="mb-8">
              <div class="text-gray-900 font-bold text-xl mb-1">
                {{ transaction.campaign.name }}
              </div>
              <p class="text-sm text-gray-600 flex items-center mb-2">
               Jumlah yang diinvestasikan: Rp {{ Intl.NumberFormat('id-ID').format(transaction.amount) }} &middot;
              </p>
              <p class="text-gray-700 text-base">
               Status: {{ transaction.status == "pending" ? "Menunggu Pembayaran" : "Pembayaran Berhasil" }}
              </p>
            </div>
            <div class="flex items-center">
              <!-- Tanggal pembayaran -->
              <div class="text-sm">
                <p class="text-gray-900 leading-none">
                Tanggal Investasi: {{ moment(transaction.created_at).format('dddd, D MMMM YYYY') }}
                </p>
                </div>
            </div>
          </nuxt-link>
        </div>
      </div>
    </section>
    <div class="cta-clip -mt-20"></div>
    <section class="call-to-action bg-purple-progress pt-64 pb-10"></section>
    <Footer />
  </div>
</template>

<script>
import moment from 'moment'
export default {
  data() {
    return {
      transaction: [],
      // transaction created at formatted on moment.js
      // currentDate:  moment().format('YYYY-MM-DD'),
      paid: '',
      // formattedDate: '',
      moment: moment
    }
  },
  computed: {
    // formattedDate() {
    //   // return moment(this.transaction.created_at).format('dddd D mm YY')
    //   // moment with string format
    //   // console.log(this.transaction.data[0].created_at)
    //   // foreach transaction in transaction.data array to format the date
    //   this.transaction.data.map((transaction) => {
    //     transaction.created_at = moment(transaction.created_at).format('dddd D MMMM YYYY')
    //     console.log(transaction.created_at)
    //   })
    // }
  },
  middleware: 'auth',
  async asyncData({ $axios, app}) {
    const transaction = await $axios.$get('/api/v1/transactions')
    return { transaction }
  },
  methods: {
    // format date for transaction date created
    formatDate() {
      this.transaction.data.map((transaction) => {
        transaction.created_at = moment(transaction.created_at).format('dddd D MMMM YYYY')
        console.log(transaction.created_at)
      })
  },
  },
  }
</script>
