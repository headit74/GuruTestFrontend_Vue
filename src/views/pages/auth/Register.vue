<template>
  <b-card class="mt-5" style="width: 600px; margin: auto;">
    <!--begin::Head-->
    <div class="kt-login__head">
      <!--<span class="kt-login__signup-label">Already have an account?</span-->
      <!--&gt;&nbsp;&nbsp;-->
      <router-link
        class="kt-link kt-login__signup-link"
        :to="{ name: 'login' }"
      >
        Login!
      </router-link>
    </div>
    <!--end::Head-->

    <!--begin::Body-->
    <div class="kt-login__body">
      <!--begin::Signin-->
      <div class="kt-login__form">
        <div class="kt-login__title">
          <h3>Sign Up</h3>
        </div>

        <!--begin::Form-->
        <b-form class="" @submit.stop.prevent="onSubmit">
          <b-form-group
            id="example-input-group-0"
            label=""
            label-for="example-input-0"
          >
            <b-form-input
              id="example-input-0"
              name="example-input-0"
              v-model="$v.form.username.$model"
              :state="validateState('username')"
              aria-describedby="input-0-live-feedback"
              placeholder="Username"
            ></b-form-input>

            <b-form-invalid-feedback id="input-0-live-feedback">
              Username is required.
            </b-form-invalid-feedback>
          </b-form-group>

          <b-form-group
            id="example-input-group-1"
            label=""
            label-for="example-input-1"
          >
            <b-form-input
              id="example-input-1"
              name="example-input-1"
              v-model="$v.form.email.$model"
              :state="validateState('email')"
              aria-describedby="input-1-live-feedback"
              placeholder="Email address"
            ></b-form-input>

            <b-form-invalid-feedback id="input-1-live-feedback">
              Email is required and a valid email address.
            </b-form-invalid-feedback>
          </b-form-group>

          <b-form-group
            id="example-input-group-2"
            label=""
            label-for="example-input-2"
          >
            <b-form-input
              type="password"
              id="example-input-2"
              name="example-input-2"
              v-model="$v.form.password.$model"
              :state="validateState('password')"
              aria-describedby="input-2-live-feedback"
              placeholder="Password"
            ></b-form-input>

            <b-form-invalid-feedback id="input-2-live-feedback">
              Password is required.
            </b-form-invalid-feedback>
          </b-form-group>

          <!--begin::Action-->
          <div class="kt-login__actions">
            <b-button
              v-on:click="$router.push('login')"
              class="btn  btn-elevate kt-login__btn-primary"
            >
              {{ $t("AUTH.GENERAL.BACK_BUTTON") }}
            </b-button>

            <b-button
              type="submit"
              id="kt_submit"
              class="btn btn-primary btn-elevate kt-login__btn-primary kt-spinner--light kt-spinner--right"
            >
              {{ $t("AUTH.REGISTER.TITLE") }}
            </b-button>
          </div>
          <!--end::Action-->
        </b-form>
        <!--end::Form-->

        <!--begin::Divider-->
        <div class="kt-login__divider">
          <div class="kt-divider">
            <span></span>
            <span>OR</span>
            <span></span>
          </div>
        </div>
        <!--end::Divider-->

        <!--begin::Options-->
        <div class="kt-login__options">
          <a href="#" class="btn btn-primary kt-btn">
            <i class="fab fa-facebook-f"></i>
            Facebook
          </a>

          <a href="#" class="btn btn-info kt-btn">
            <i class="fab fa-twitter"></i>
            Twitter
          </a>

          <a href="#" class="btn btn-danger kt-btn">
            <i class="fab fa-google"></i>
            Google
          </a>
        </div>
        <!--end::Options-->
      </div>
      <!--end::Signin-->
    </div>
    <!--end::Body-->
  </b-card>
</template>

<script>
import { mapState } from "vuex";
import { REGISTER } from "@/store/auth.module";
import { LOGOUT } from "@/store/auth.module";

import { validationMixin } from "vuelidate";
import { email, required, minLength } from "vuelidate/lib/validators";

export default {
  mixins: [validationMixin],
  name: "register",
  data() {
    return {
      // Remove this dummy login info
      form: {
        email: "",
        password: ""
      }
    };
  },
  validations: {
    form: {
      username: {
        required,
        minLength: minLength(3)
      },
      email: {
        required,
        email
      },
      password: {
        required,
        minLength: minLength(3)
      }
    }
  },
  methods: {
    validateState(name) {
      const { $dirty, $error } = this.$v.form[name];
      return $dirty ? !$error : null;
    },
    resetForm() {
      this.form = {
        username: null,
        email: null,
        password: null
      };

      this.$nextTick(() => {
        this.$v.$reset();
      });
    },
    onSubmit() {
      this.$v.form.$touch();
      if (this.$v.form.$anyError) {
        return;
      }

      const username = this.$v.form.username.$model;
      const email = this.$v.form.email.$model;
      const password = this.$v.form.password.$model;

      // clear existing errors
      this.$store.dispatch(LOGOUT);

      // set spinner to submit button
      const submitButton = document.getElementById("kt_submit");
      submitButton.classList.add("kt-spinner");

      // dummy delay
      setTimeout(() => {
        // send register request
        this.$store
          .dispatch(REGISTER, {
            email: email,
            password: password,
            username: username
          })
          .then(() => this.$router.push({ name: "dashboard" }));

        submitButton.classList.remove("kt-spinner");
      }, 2000);
    }
  },
  computed: {
    ...mapState({
      errors: state => state.auth.errors
    }),
    backgroundImage() {
      return process.env.BASE_URL + "assets/media/bg/bg-4.jpg";
    }
  }
};
</script>
