<template>
  <div>
    <div class="primary-bg text-white pb-10 text-lg text-center">
      <div class="container mx-auto">
        <h2 class="text-4xl font-bold mb-6">Vaccine Registration</h2>
        <p>Complete the registration by verifying your national identity card and mobile number in the form below. The
          place and date of delivery of the vaccine will be informed in due course through SMS message on the mobile
          phone.</p>
      </div>
    </div>

    <div class="py-20">
      <div class="small-container mx-auto">
        <VaccineSteps :theme="step"/>

        <div class="bg-white px-20 py-10 border border-gray-100">

          <div v-if="step == 'step_1'" class="">
            <h3 class="font-bold text-4xl mb-6 text-center">Identity Verification</h3>

            <div v-if="peopleData.hasOwnProperty('success') && !peopleData.success"
                 class="bg-red-200 text-red-900 p-4 mb-6">
              {{ peopleData.message }}
            </div>

            <p class="mb-6">
              <label class="tika-label" for="category_id">Select category </label>
              <select v-model="verifyData.category_id" class="tika-input" id="category_id">
                <option selected="selected" value="">Select a category</option>
                <option v-for="item in categories" :key="item.id" :value="item.id">
                  {{ item.name }}
                </option>
              </select>
            </p>

            <div v-if="verifyData.category_id">
              <div class="flex -mx-4">
                <div class="w-1/2 px-4">
                  <p class="mb-6">
                    <label for="id_no" class="tika-label">National ID Number</label>
                    <input id="id_no" v-model="verifyData.id_no" type="number" class="tika-input"
                           placeholder="Type your national ID card number">
                  </p>
                </div>
                <div class="w-1/2 px-4">
                  <p class="mb-6">
                    <label for="dob" class="tika-label">Date of birth</label>
                    <input id="dob" v-model="verifyData.dob" type="date" class="tika-input"
                           placeholder="Type your national ID card number">
                  </p>
                </div>
              </div>
              <div class="flex -mx-4">
                <div class="w-max px-4">
                  <p>
                    <button @click.prevent="checkMyInformation" class="primary-btn">Check my information</button>
                  </p>
                </div>
              </div>
            </div>

          </div>

          <div v-if="step == 'step_2'" class="">
            <h3 class="font-bold text-4xl mb-6 text-center">User Information</h3>
            <p class="mb-6">
              <label class="tika-label" for="division_id">Select Division </label>
              <select @change.prevent="getAvailableDistrict" v-model="division_id" class="tika-input" id="division_id">
                <option selected="selected" value="">Select a division</option>
                <option v-for="item in divisions" :key="item.id" :value="item.id">{{ item.name }}</option>
              </select>
            </p>

            <p v-if="districts.length" class="mb-6">
              <label class="tika-label" for="district_id">Select District </label>
              <select @change.prevent="getAvailableUpazilas" v-model="district_id" class="tika-input" id="district_id">
                <option selected="selected" value="">Select a District</option>
                <option v-for="item in districts" :key="item.id" :value="item.id">{{ item.name }}</option>
              </select>
            </p>

            <p v-if="upazilas.length" class="mb-6">
              <label class="tika-label" for="upazila_id">Select Upazila </label>
              <select v-model="upazila_id" class="tika-input" id="upazila_id">
                <option selected="selected" value="">Select a Upazila</option>
                <option v-for="item in upazilas" :key="item.id" :value="item.id">{{ item.name }}</option>
              </select>
            </p>

          </div>

          <!--          <div v-if="step == 'step_2'" class="">-->
          <!--            <p v-if="centers.length" class="mb-6">-->
          <!--              <label class="tika-label" for="center_id">Select vaccination center </label>-->
          <!--              <select v-model="center_id" class="tika-input" id="center_id">-->
          <!--                <option selected="selected" value="">Select a center</option>-->
          <!--                <option v-for="item in centers" :key="item.id" :value="item.id">{{ item.name }}</option>-->
          <!--              </select>-->
          <!--            </p>-->
          <!--            <p v-if="!centers.length && upazila_id">-->
          <!--              No center available-->
          <!--            </p>-->


          <!--            <div v-if="center_id" class="">-->
          <!--              <p class="mb-6">-->
          <!--                <label for="name" class="tika-label">Name</label>-->
          <!--                <input id="name" v-model="name" type="text" class="tika-input" placeholder="Type your name">-->
          <!--              </p>-->
          <!--              <p class="mb-6">-->
          <!--                <label for="diabates" class="tika-label">Do you have diabates?</label>-->
          <!--                <select v-model="diabates" class="tika-input" id="diabates">-->
          <!--                  <option selected="selected" value="">Select a value</option>-->
          <!--                  <option value="1">Yes</option>-->
          <!--                  <option value="0">No</option>-->
          <!--                </select>-->
          <!--              </p>-->
          <!--            </div>-->

          <!--            <p v-if="diabates">-->
          <!--              <button @click.prevent="goToStepThree" class="primary-btn">Submit</button>-->
          <!--            </p>-->
          <!--          </div>-->

          <!--          <div v-if="step == 'step_3'">-->
          <!--            <h3 class="font-bold text-4xl mb-6 text-center">Phone verification</h3>-->

          <!--            <div v-if="!smsSent">-->
          <!--              <p class="mb-6">-->
          <!--                <label for="phone_no" class="tika-label">Phone number</label>-->
          <!--                <input id="phone_no" v-model="phone_no" type="text" class="tika-input" placeholder="Type phone number">-->
          <!--              </p>-->
          <!--              <p>-->
          <!--                <button @click.prevent="sendVerificationSMS" class="primary-btn">Send SMS</button>-->
          <!--              </p>-->
          <!--            </div>-->

          <!--            <div v-if="smsSent">-->
          <!--              <p class="mb-6">-->
          <!--                <label for="verify_code" class="tika-label">Verification code-->
          <!--                </label>-->
          <!--                <input id="verify_code" v-model="verify_code" type="text" class="tika-input"-->
          <!--                       placeholder="Type verification code">-->
          <!--              </p>-->
          <!--              <p>-->
          <!--                <button @click.prevent="verifyCode" class="primary-btn">Verify</button>-->
          <!--              </p>-->
          <!--            </div>-->

          <!--          </div>-->

        </div>
      </div>
    </div>

    <div class="container mx-auto">
      <ThreeSteps/>
    </div>
  </div>
</template>

<script>
import VaccineSteps from "../components/VaccineSteps";
import ThreeSteps from '../components/ThreeSteps.vue';

export default {
  name: "registration",
  components: {
    ThreeSteps, VaccineSteps
  },
  data() {
    return {
      categories: [],
      divisions: [],
      division_id: '',
      districts: [],
      district_id: '',
      upazilas: [],
      upazila_id: '',
      step: 'step_1',
      peopleData: [],
      verifyData: {
        category_id: '',
        id_no: '',
        dob: ''
      }
    }
  },
  mounted() {
    this.getAvailableCategory();
  },
  methods: {
    getAvailableCategory() {
      this.$axios.get('/categories').then(res => {
        this.categories = res.data;
      })
    },
    checkMyInformation() {
      this.$axios.post('/verify', this.verifyData).then(res => {
        this.peopleData = res.data;
        if (res.data.success) {
          this.step = 'step_2';
          this.getAvailableDivision();
        }
      });
    },
    getAvailableDivision() {
      this.$axios.get('/divisions').then(res => {
        this.divisions = res.data;
        this.getAvailableDistrict();
      })
    },
    getAvailableDistrict() {
      this.$axios.get('/districts?division_id=' + this.division_id).then(res => {
        this.districts = res.data;
        this.getAvailableUpazilas();
      })
    },
    getAvailableUpazilas() {
      this.$axios.get('/upazilas?district_id=' + this.district_id).then(res => {
        this.upazilas = res.data;
      })
    }
  }
}
</script>

<style scoped>

</style>
