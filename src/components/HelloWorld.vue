<template>
  <div class="container w-50">
    <h1 class="text-center">{{ msg }}</h1>
      <p v-if="errors.length">
    <b>Please correct the following error<span v-show="errors.length > 1">s</span>:</b>
    <ul>
      <li class="text-danger d-block" v-for="(error, index) in errors" :key="index">{{ error }}</li>
    </ul>
  </p>
    <form class="mt-5" @submit="checkForm">
      <div class="mb-3">
        <label for="exampleInputEmail1" class="form-label">Name</label>
        <input
          type="text"
          class="form-control"
          id="exampleInputEmail1"
          aria-describedby="nameHelp"
          maxlength="50"
          v-model="name"
          @keyup="validateName"
        />
        <div
          id="nameHelp"
          class="form-text text-danger"
          v-show="maxLengthReached"
        >
          Name can't be longet than 50 digits
        </div>
      </div>
      <div class="mb-3">
        <label for="exampleInputEmail1" class="form-label">Email</label>
        <input
          type="email"
          class="form-control"
          id="exampleInputEmail1"
          aria-describedby="emailHelp"
          v-model="email"
        />
      </div>
        <VueRecaptcha ref="recaptcha" class="mb-2"
          @verify="onVerify" sitekey="6Lcp3FsaAAAAACIEIggj9dcf4jWqektfPpZe3cwB">
        </VueRecaptcha>
      <button type="submit" class="btn btn-primary" >
        Submit
      </button>
    </form>
  </div>
</template>

<script>
import VueRecaptcha from 'vue-recaptcha';
export default {
  name: "HelloWorld",
  components:{VueRecaptcha},
  props: {
    msg: String,
  },
  data: () => ({
    name: null,
    email: null,
    maxLengthReached: false,
    robot: false,
    errors: [],
  }),
  methods: {
    validateName: function() {
      this.noName = false;
      if (this.name.length === 50) {
        console.log("maxLengthReached", this.name.length);
        this.maxLengthReached = true;
      } else {
        this.maxLengthReached = false;
      }
    },
    checkEmail: function (email) {
      var re = /^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
      return re.test(email);
    },
    checkForm: function (e) {
      this.errors = [];
      if (!this.name) this.errors.push("Name required.");
      if (!this.email) {
        this.errors.push("Email required.");
      } else if (!this.checkEmail(this.email)) {
        this.errors.push("Valid email required.");
      }
      if(!this.robot) {
        this.errors.push('Please check recaptcha')
      }
      if (!this.errors.length) return true;
      e.preventDefault();
    },
    onVerify: function(response) {
      if (response) this.robot = true;
    }
  },
};
</script>
