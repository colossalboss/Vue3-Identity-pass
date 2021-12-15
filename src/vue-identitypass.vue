<template>
     
   <div class="main_con">
       <button class="" style="cursor: pointer" @click="() => showForm = true">
            Verify BVN
        </button>
        <div class="" v-if="showForm">
            <transition name="modal">
                <div class="modal-mask">
                    <div class="modal-wrapper" :class="{ 'grey_bg': showForm }">
                        <div class="modal-container">

                        <div class="modal-header">
                            <slot name="header">
                                <p style="text-align:left;margin:0">
                                    <span>BVN Verifcation</span>
                                </p> 
                            </slot>
                        </div>

                        <div class="modal-body">
                            <slot name="body">
                                <div class="" v-if="showOptions">
                                    <div>
                                        <div @click="itemSelected(item)" class="c_card" v-for="(item, index) in optionsList" :key="index" >
                                            <input type="checkbox">
                                            <span>{{ item.toUpperCase() }}</span>
                                        </div>
                                    </div>
                                </div>
                                <div v-if="showFormFields">
                                    <form @submit.prevent="onSubmit">
                                        <span v-if="selectedOption === 'bvn'">
                                            <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">BVN</label></p>
                                            <input type="text" id="fname" name="firstname" placeholder="BVN..">
                                        </span>

                                        <span v-if="selectedOption === 'phone'">
                                            <p style="text-align:left;margin:0 0;font-size: 12px"><label for="fname">Phone</label></p>
                                            <input type="text" id="fname" name="firstname" placeholder="Phone..">
                                        </span>
                                    
                                        <input type="submit" value="Submit">
                                    </form>
                                </div>
                            </slot>
                        </div>

                        <div class="modal-footer">
                            <slot name="footer">
                                <p style="margin:0;display:flex;justify-content:space-between">
                                    <span style="cursor: pointer"  @click="goBackToOptions"><span style="font-size:20px;font-weight:800">←</span></span>
                                    <span style="cursor: pointer"  @click="onCancel">Cancel</span>
                                </p>
                                <!-- <button class="modal-default-button"  @click="() => showForm = false" style="cursor: pointer">
                                    OK
                                </button> -->
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
    props: [ 'text' ],
    data() {
        return {
            showForm: false,
            options: [ 'Phone', 'BVN', 'BVN_IMAGE' ],
            selectedOption: '',
            showOptions: true,
            showFormFields: false,
        }
    },

    computed: {
        optionsList() {
            return this.options.map(i => i.toLowerCase())
        }
    },

    methods: {
        itemSelected(option) {
            this.selectedOption = option;
            this.showOptions = false;
            this.showFormFields = true;
        },

        onCancel() {
            this.showForm = false;
            this.showOptions = true;
            this.showFormFields = false;
        },
        
        goBackToOptions() {
            this.showFormFields = false;
            this.showOptions = true;
        },

        onSubmit() {
            fetch('https://sandbox.myidentitypass.com/api/v1/biometrics/merchant/data/verification/bvn', {
                    method: "POST",
                    body: { number: '54651333604' },
                    headers: {"Content-type": "application/json; charset=UTF-8", "x-api-key": "test_231qza7t1kxejz21eg26e5:m1YlNf4sqfSQ0GEKnC8j2oZ-dyc"}
                    // headers: {"Content-type": "application/json; charset=UTF-8"}
                })
                .then(response => response.json()) 
                .then(json => console.log(json))
                .catch(err => console.log(err));
        }
    }
}
</script>

<style scoped>
.main_con {
    /* background: #fff; */
}
.modal-mask {
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

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
  /* background: #f2f2f24d; */
}

/* .grey_bg {
    background: #f2f2f24d;
} */

.modal-container {
  width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}

.modal-header h3 {
  margin-top: 0;
  color: #42b983;
}

.modal-body {
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

.modal-enter .modal-container,
.modal-leave-active .modal-container {
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
input[type=text], select {
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

</style>
