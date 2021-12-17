<template>
     
    <button class="verify_btn" style="cursor: pointer" @click="() => showForm = true">
        Verify
    </button>
   <div class="main_con">
        <div class="" v-if="showForm">
            <transition name="modal">
                <div class="modal_mask">
                    <div class="modal_wrapper" :class="{ 'grey_bg': showForm }">
                        <div class="modal_container">
                            <div class="modal_body" style="position:relative">
                                <slot name="body">
                                    <span style="position: absolute;top:-15px;right:-22px;cursor:pointer" @click="onCancel"> X</span>
                                    <div class="" v-if="showOptions">
                                        <div>
                                            <div @click="itemSelected(item)" class="c_card" v-for="(item, index) in optionsList" :key="index" >
                                                <span>{{ item.toUpperCase() }}</span>
                                            </div>
                                        </div>
                                    </div>
                                    <div v-if="showFormFields">
                                        <form @submit.prevent="onSubmit">
                                            <span v-if="selectedOption.includes('bvn')">
                                                <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">BVN</label></p>
                                                <input v-model="bvnNumber" type="text" id="fname" name="firstname" placeholder="BVN..">
                                                <!-- <input v-model="bvnNumber" pattern="[0-9]{10,}" type="text" id="fname" name="firstname" placeholder="BVN.."> -->
                                            </span>

                                            <span v-if="selectedOption.includes('nin')">
                                                <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">NIN</label></p>
                                                <input v-model="nin" type="text" id="fnin" name="nine" placeholder="NIN..">
                                                <!-- <input v-model="bvnNumber" pattern="[0-9]{10,}" type="text" id="fname" name="firstname" placeholder="BVN.."> -->
                                            </span>

                                            <span v-if="selectedOption.includes('drivers_license')">
                                                <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">FRSC Number</label></p>
                                                <input type="text" v-model="frscNumber" id="fname" name="dlicense" placeholder="FRSC Number..">
                                            </span>

                                            <span v-if="selectedOption.includes('drivers_license')">
                                                <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">Date Of Birth</label></p>
                                                <input type="date" v-model="dob" id="fname" name="dlicense" placeholder="DOB..">
                                            </span>

                                            <!-- <span v-if="selectedOption.includes('vin')">
                                                <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">VIN</label></p>
                                                <input type="text" v-model="vin" id="fname" name="firstname" placeholder="Vin..">
                                            </span> -->
                                        
                                            <input type="submit" :value="submitText">
                                        </form>
                                    </div>
                                    <div v-if="completed">
                                        <div v-if="completed && isSuccessfull" class="" style="text-align: center">
                                            <h2 style="color: green">Congratulations</h2>
                                            <p>{{ message }}</p>
                                        </div>
                                        <div v-if="completed && !isSuccessfull" class="" style="text-align: center">
                                            <h2 style="color: red">Oops, Error</h2>
                                            <p>Your verification was not successfull</p>
                                        </div>
                                    </div>
                                </slot>
                            </div>

                            <div class="modal_footer" v-if="showFormFields">
                                <slot name="footer">
                                    <p style="margin:0;display:flex;justify-content:end">
                                        <span style="cursor: pointer"  @click="goBackToOptions"><span style="font-size:14px">← Back</span></span>
                                    </p>
                                    <!-- <button class="modal-default-button"  @click="() => showForm = false" style="cursor: pointer">
                                        OK
                                    </button> -->
                                </slot>
                            </div>

                            <div class="modal_header">
                                <slot name="header">
                                    <hr>
                                    <p style="text-align:center;margin:0">
                                        <span>Powered by <span style="font-weight: bold"> Vherifai</span></span>
                                    </p> 
                                </slot>
                            </div>
                        </div>
                    </div>
                </div>
            </transition>
        </div>
   </div>
</template>

<script>

export default {
    props: [ 'reference', 'secretKey', 'options' ],
    data() {
        return {
            showForm: false,
            derivedOptions: [ ],
            selectedOption: '',
            showOptions: true,
            showFormFields: false,
            baseUrl: 'https://vherifai.herokuapp.com/',
            // secretKey: '42f1bc1fb5804b6f966a095ce20c2e7996278',
            // secretKey: '17d84a5d4f2542478a888b4b8ce5506a49416',
            bvnNumber: '',
            isSuccessfull: true,
            completed: false,
            message: '',
            loading: false,
            verificationData: { },
            nin: '',
            vin: '',
            verificationTypes: '',
            frscNumber: '',
            dob: '',
            webHookUrl: '',
        }
    },

    computed: {
        optionsList() {
            return this.derivedOptions.map(i => i.toLowerCase())
        },

        submitText() {
            if (this.loading) return 'Verifying...';
            return 'Submit';
        },

        url() {
            if (this.selectedOption === 'nin') return 'api/server/verify/nin';
            if (this.selectedOption === 'bvn') return 'api/server/verify/bvn';
            if (this.selectedOption.includes(',')) return 'api/server/verify/bulk_verification';
            return 'api/server/verify/bvn';
        },

        formatedDate() {
            if (!this.dob) return '';
            const date = new Date(this.dob).getDate();
            const month = new Date(this.dob).getMonth();
            const year = new Date(this.dob).getFullYear();
            return `${year}-${month + 1}-${date}`;
        }
    },

    methods: {
        itemSelected(option) {
            this.selectedOption = option;
            this.showOptions = false;
            this.showFormFields = true;
        },

        resetData() {
            this.nin = '';
            this.bvnNumber = '';
            this.vin = '';
        },

        onCancel() {
            if (this.verificationData && this.verificationData.message) {
                this.emitData(this.verificationData);
            }
            this.showForm = false;
            this.showOptions = true;
            this.showFormFields = false;
            this.completed = false;
            this.isSuccessfull = false;
            this.resetData()
        },
        
        goBackToOptions() {
            this.showFormFields = false;
            this.showOptions = true;
        },

        emitData(data) {
            this.$emit('completed', data)
        },

        onSubmit() {
            this.loading = true
            fetch(`${this.baseUrl}${this.url}`, {
                method: "POST",
                body: JSON.stringify({
                    niNumber: this.nin,
                    "viNumber": this.vin,
                    "lastName": "",
                    "state": "",
                    "phoneNumber": "",
                    "firstName": "",
                    "dob": this.formatedDate,
                    "frscNumber": this.frscNumber,
                    userReferenceId: this.reference,
                    type: this.options.join(),
                    bvnNumber: this.bvnNumber,
                    url: this.webHookUrl,
                }),
                headers: {"Content-type": "application/json; charset=UTF-8"}
            })
                .then(response => response.json()) 
                .then(json => {
                    console.log(json, "DATA");
                    this.loading = false;
                    const { status, data: { message }, data } = json;
                    this.completed = true;
                    this.showFormFields = false;
                    this.message = message;
                    this.isSuccessfull = status;
                    this.verificationData = json.data;
                })
                .catch(err => {
                    console.log(err.body, "Error ")
                    this.loading = false;
                    this.completed = true;
                    this.isSuccessfull = false;
                    this.showFormFields = false;
                    this.message = "error";
                });
        },

        
    },

    created() {
        if (this.options) this.derivedOptions = this.options;
        fetch(`${this.baseUrl}api/server/getinfofromsecretkey?secretkey=${this.secretKey}`, )
            .then(response => response.json()) 
            .then(json => {
                const { webHookUrl } = json;
                // if (verificationTypes) this.options.push(verificationTypes)
                this.webHookUrl = webHookUrl;
            })
            .catch(err => console.log(err));
    }
}
</script>

<style scoped>
.main_con {
    /* background: #fff; */
}
.modal_mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}

.modal_wrapper {
  display: table-cell;
  vertical-align: middle;
  /* background: #f2f2f24d; */
}

/* .grey_bg {
    background: #f2f2f24d;
} */

.modal_container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal_header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal_body {
  margin: 20px 0;
}

.modal-default-button {
  /* float: right; */
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal_container,
.modal-leave-active .modal_container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}

.c_card {
    padding: 13px 0.5rem;
    text-align: left;
    box-shadow: 0 0.2em 1em rgb(0 0 0 / 10%), 0 1em 2em rgb(0 0 0 / 10%);
    background: #fff;
    margin: 1rem 0;
    cursor:pointer;
}
</style>
<style>
input[type=text], input[type=date], select {
  width: 100%;
  padding: 12px 20px;
  margin: 8px 0;
  display: inline-block;
  border: 1px solid #ccc;
  border-radius: 4px;
  box-sizing: border-box;
}

input[type=submit] {
  width: 100%;
  background-color: #4CAF50;
  color: white;
  padding: 14px 20px;
  margin: 8px 0;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

input[type=submit]:hover {
  background-color: #45a049;
}

.verify_btn {
    cursor: pointer;
    background: #2196f3;
    border-radius: 4px;
    border: none;
    padding: 0.3rem 1rem;
    color: #fff;
    font-weight: bold;
}

</style>
