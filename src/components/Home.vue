<template>
  <mdb-container>
    <mdb-row>
      <mdb-col>
        <div class="card col-sm-12 col-md-8 mx-md-auto mx-md-5 my-3 my-md-5">
          <div class="card-body">
            <mdb-row>
              <mdb-col>
                <h3>{{ msg }}</h3>
              </mdb-col>
            </mdb-row>
            <mdb-row>
              <mdb-col>
                <p class="mb-2">
                  This application allows you to specify a address, amount and
                  label which will the generate you a QR Code with options you
                  selected.
                </p>
                <br />
              </mdb-col>
            </mdb-row>
          </div>
        </div>
      </mdb-col>
    </mdb-row>
    <mdb-row>
      <mdb-col>
        <div class="card col-sm-12 col-md-8 mx-md-auto mx-sm-5 mb-5">
          <div class="card-body">
            <mdb-row>
              <mdb-col>
                <form v-on:submit.prevent="submitForm">
                  <label
                    for="nanoPublicAddress"
                    class="bold blue-text font-weight-light"
                    >Public Address</label
                  >
                  <input
                    type="text"
                    id="nanoPublicAddress"
                    class="form-control"
                    placeholder="nano_31fgrmpndor83gjb7fehibmdzsmqwqexzggu8c9hzde3hwcdsn6raxcsr75k"
                    v-model="form.address"
                  />
                  <br />
                  <label
                    for="nanoAmount"
                    class="bold blue-text font-weight-light"
                    >Amount</label
                  >
                  <input
                    type="number"
                    id="nanoAmount"
                    class="form-control"
                    placeholder="0.001"
                    v-model="form.amount"
                  />
                  <br />
                  <label
                    for="nanoLabel"
                    class="bold blue-text font-weight-light"
                    >Label</label
                  >
                  <input
                    type="text"
                    id="nanoLabel"
                    class="form-control"
                    placeholder="Developer Wallet"
                    v-model="form.label"
                  />
                  <div class="d-flex justify-content-center align-item-center mt-5" v-if="showSpinner">
                    <breeding-rhombus-spinner
                      :animation-duration="2000"
                      :size="65"
                      color="#2196f3"
                    />
                  </div>
                  <div v-if="showQr">
                    <br />
                    <img :src="qrcode" alt="Base64 encoded image"/>
                  </div>
                  <div class="text-center py-4 mt-3">
                    <button class="btn btn-outline-black" type="submit">
                      Generate<i class="far fa-paper-plane ml-2"></i>
                    </button>
                  </div>
                </form>
              </mdb-col>
            </mdb-row>
          </div>
        </div>
      </mdb-col>
    </mdb-row>
  </mdb-container>
</template>

<script>
import { mdbContainer, mdbRow, mdbCol } from "mdbvue";
import { BreedingRhombusSpinner } from "epic-spinners";
import axios from "axios";
import _ from 'lodash';

export default {
  name: "Home",
  data() {
    return {
      msg: "Nano QR Code Generator",
      form: {
        address: "",
        amount: "",
        label: "",
      },
      qrcode: "",
      showQr: false,
      showSpinner: false,
    };
  },
  components: {
    mdbContainer,
    mdbRow,
    mdbCol,
    BreedingRhombusSpinner,
  },
  methods: {
    submitForm() {
      this.showQr = false;
      this.showSpinner = !this.showQr;
      let form = _.cloneDeep(this.form);
      form.amount = (form.amount * Math.pow(10, 30)).toLocaleString('fullwide', {useGrouping:false}) 
      axios
        .post("https://nano-qr-generator-api.herokuapp.com/", form, {
          responseType: "blob",
        })
        .then((res) => {
          const urlCreator = window.URL || window.webkitURL;
          this.qrcode = urlCreator.createObjectURL(res.data);
          this.showQr = true;
          this.showSpinner = !this.showQr;
        })
        .catch((error) => {
          console.log(error);
        })
        .finally(() => {
          //Perform action in always
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  font-weight: normal;
  padding-top: 20px;
  padding-bottom: 30px;
}
p {
  color: #969696;
  margin-bottom: 0;
  font-size: 14px;
}
input {
  text-align: center;
}
input::-webkit-input-placeholder {
  color: black; /*Change the placeholder color*/
  opacity: 0.25; /*Change the opacity between 0 and 1*/
  text-align: center;
}
.bold {
  font-weight: 700 !important;
}
</style>
