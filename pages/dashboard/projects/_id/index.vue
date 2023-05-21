<template>
  <div class="project-page">
    <section class="dashboard-header pt-5">
      <div class="container mx-auto relative">
        <Navbar />
      </div>
    </section>
    <section class="container mx-auto pt-8">
      <div class="flex justify-between items-center">
        <div class="w-full mr-6">
          <h2 class="text-4xl text-gray-900 mb-2 font-medium">Dashboard</h2>
        </div>
      </div>
      <div class="flex justify-between items-center">
        <div class="w-3/4 mr-6">
          <h3 class="text-2xl text-gray-900 mb-4">Campaign Details</h3>
        </div>
        <div class="w-1/4 text-right">
          <nuxt-link
            :to="{ name: 'dashboard-projects-id-edit', params: { id: projects.data.id } }"
            class="bg-green-button hover:bg-green-button text-white font-bold px-4 py-1 rounded inline-flex items-center"
          >
            Edit
          </nuxt-link>
        </div>
      </div>
      <div class="block mb-2">
        <div class="w-full lg:max-w-full lg:flex mb-4">
          <div
            class="border w-full border-gray-400 bg-white rounded p-8 flex flex-col justify-between leading-normal"
          >
            <div>
              <div class="text-gray-900 font-bold text-xl mb-2">
                {{ projects.data.name }}
              </div>
              <p class="text-sm font-bold flex items-center mb-1">
                Description
              </p>
              <p class="text-gray-700 w-full text-base flex items-center mb-1">
                {{ projects.data.description }}
              </p>
              <p class="text-sm font-bold flex items-center mb-1 mt-4">
                What Will Funders Get
              </p>
              <ul class="list-disc ml-5" v-for="perk in projects.data.perks" :key="perk">
                <li >{{ perk }}</li>
              </ul>
              <p class="text-sm font-bold flex items-center mb-1 mt-4">
                Investment Plan
              </p>
              <p class="text-4xl text-gray-700">
                Rp. {{ new Intl.NumberFormat('id-ID').format(projects.data.goal_amount) }}
              </p>
            </div>
          </div>
        </div>
      </div>
      <div class="flex justify-between items-center">
        <div class="w-2/4 mr-6">
          <h3 class="text-2xl text-gray-900 mb-4 mt-5">Gallery</h3>
        </div>
        <div class="w-2/4 text-right">
          <input type="file" ref="file" @change="selectFile" class="border p-1 rounded overflow-hidden"/>
          <button
            @click="uploadImage"
            class="bg-green-button hover:bg-green-button text-white font-bold px-4 py-2 rounded inline-flex items-center"
          >
            Upload
          </button>
        </div>
      </div>
      <div class="grid grid-cols-4 -mx-2">
        <div
              v-for="image in projects.data.images"
              :key="image.image_url"
              class="relative w-full bg-white m-2 p-2 border border-gray-400 rounded-20"
            >
              <figure class="item-thumbnail">
                <img
                  :src="$axios.defaults.baseURL + '/' + image.image_url"
                  @click="changeImage($axios.defaults.baseURL + '/' + image.image_url)"
                  alt=""
                  class="rounded-20 w-full"
                />
              </figure>
            </div>
      </div>
      <div class="flex justify-between items-center">
        <div class="w-3/4 mr-6">
          <h3 class="text-2xl text-gray-900 mb-4 mt-5">
            Investor
          </h3>
        </div>
      </div>
      <div class="block mb-2">
        <div class="w-full lg:max-w-full lg:flex mb-4">
          <div
            class="w-full border border-gray-400 lg:border-gray-400 bg-white rounded p-8 flex flex-col justify-between leading-normal"
          >
            <div>
              <div class="text-gray-900 font-bold text-xl mb-1">
                Total Investments : {{ projects.data.backer_count }} Orang
              </div>
              <p class="text-xl text-gray-900 font-bold flex items-center mb-2">
                Rp. {{ new Intl.NumberFormat('id-ID').format(projects.data.current_amount) }} dari target Rp. {{ new Intl.NumberFormat('id-ID').format(projects.data.goal_amount) }}
              </p>
            </div>
          </div>
        </div>
      </div>
      <div class="flex justify-between items-center">
        <div class="w-3/4 mr-6">
          <h3 class="text-2xl text-gray-900 mb-4 mt-5">
            Riwayat Investasi
          </h3>
        </div>
      </div>
      <div class="block mb-2">
        <div class="w-full lg:max-w-full lg:flex mb-4" v-for="history in history.data" :key="history.id">
          <div
            class="w-full border border-gray-400 lg:border-gray-400 bg-white rounded p-8 flex flex-col justify-between leading-normal"
          >
            <div>
              <div class="text-gray-900 font-bold text-xl mb-1">
                {{ history.name }}
              </div>
              <p class="text-2xl text-gray-900 flex items-center mb-2">
                Rp. {{ new Intl.NumberFormat('id-ID').format(history.amount) }}
              </p>
              <p class="text-xl text-gray-900 flex items-center mb-2">
                Tanggal Investasi : {{ moment(history.created_at).format('dddd, DD MMMM YYYY') }}
              </p>
            </div>
          </div>
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
  middleware: 'auth',
  data(){
    return {
      moment: moment,
      selectedFile: undefined,
    }
  },
  methods: {
    selectFile() {
      this.selectedFile = this.$refs.file.files;
    },
    // async loadCampaign() {
    //   const campaign = await this.$axios.$get('/api/v1/campaigns/' + this.$route.params.id)
    //   this.campaign = campaign.data
    // },
    async uploadImage() {
      const formData = new FormData();
      formData.append('campaign_id', this.$route.params.id);
      formData.append('file', this.selectedFile.item(0));
      formData.append('is_primary', true)

      try {
        const response = await this.$axios.post(`/api/v1/campaign-images`, formData, {
          headers: {
            'Content-Type': 'multipart/form-data'
          }
        })
        // console.log(response)
        // this.loadCampaign()
        this.selectedFile = undefined
        // this.$toast.success('Image Uploaded')
        this.$nuxt.refresh()
      } catch (error) {
        console.log(error)
      }
    },
  },
  middleware: 'auth',
  async asyncData({ $axios, params}) {
    const projects = await $axios.$get('/api/v1/campaigns/' + params.id )
    const history = await $axios.$get(`/api/v1/campaigns/` + params.id  + `/transactions`)

    return { projects , history }
  },
}
</script>
