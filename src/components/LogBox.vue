<template>
  <div class="wrapper">
    <div class="title-text">
      <div class="title login">เข้าสู่ระบบ</div>
      <div class="title signup">ลงทะเบียน</div>
    </div>
    <div class="form-container">
      <div class="slide-controls">
        <input type="radio" name="slide" id="login" checked>
        <input type="radio" name="slide" id="signup">
        <label for="login" class="slide login">เข้าสู่ระบบ</label>
        <label for="signup" class="slide signup">ลงทะเบียน</label>
        <div class="slider-tab"></div>
      </div>
      <div class="form-inner">
        <form action="#" class="login">
          <div class="field">
            <input v-model="form.email" type="email" placeholder="อีเมล" required>
          </div>
          <div class="field">
            <input v-model="form.password" type="password" placeholder="รหัสผ่าน" required>
          </div>
          <div class="pass-link"><a href="#">ลืมรหัสผ่านเหรอ?</a></div>
          <div class="field btn">
            <div class="btn-layer"></div>
            <input type="submit" value="เข้าสู่ระบบ" @click="Login_to">
          </div>
          <div class="signup-link">ไม่ใช่สมาชิกของเราใช่มั้ย? <a href="#">ลงทะเบียนตรงนี้เลย</a></div>
        </form>
        <form action="#" class="signup" @submit="onSubmit">
          <div class="field">
            <input v-model="form.name" type="name" id="User_name" class="name" placeholder="ชื่อของคุณ" required>
          </div>
          <div class="field">
            <input v-model="form.email" type="email" id="User_Regis" class="email" placeholder="อีเมล" required>
          </div>
          <div class="field">
            <input v-model="form.password" type="password" id="User_pass1" class="password" placeholder="รหัสผ่าน" required>
          </div>
          <div class="field">
            <input v-model="form.password2" type="password" id="User_pass2" class="password2" placeholder="ยืนยันรหัสผ่าน" required>
          </div>
          <div class="field btn">
            <div class="btn-layer"></div>
            <input type="submit" value="ลงทะเบียน">
          </div>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
import { getAuth, createUserWithEmailAndPassword, onAuthStateChanged, updateProfile,
  signInWithEmailAndPassword } from "firebase/auth";
import Swal from 'sweetalert2'


export default {
  name: 'LoginForm',
  mounted() {
    const loginText = this.$el.querySelector(".title-text .login");
    const loginForm = this.$el.querySelector("form.login");
    const loginBtn = this.$el.querySelector("label.login");
    const signupBtn = this.$el.querySelector("label.signup");
    const signupLink = this.$el.querySelector("form .signup-link a");

    signupBtn.onclick = () => {
      loginForm.style.marginLeft = "-50%";
      loginText.style.marginLeft = "-50%";
    };

    loginBtn.onclick = () => {
      loginForm.style.marginLeft = "0%";
      loginText.style.marginLeft = "0%";
    };

    signupLink.onclick = () => {
      signupBtn.click();
      return false;
    };
  },

  data() {
    return {
      form: {
        name: "",
        email: "",
        password: "",
        password2: "",
      },
    };
  },

  created() {
    const auth = getAuth();
    onAuthStateChanged(auth, (user) =>{
      if (user) {
        this.$router.push("/").catch(() => {});
      }
    });
  },

  methods: {
  onSubmit(event) {
    event.preventDefault();
    if (this.form.password !== this.form.password2) {
      Swal.fire({
        icon: 'error',
        title: 'รหัสผ่านไม่ตรงกัน',
        footer: 'กรุณาใส่รหัสผ่านอีกครั้ง'
      })
      this.form.password = '',
      this.form.password2 = ''
      return;
    }
    
    const auth = getAuth();
    createUserWithEmailAndPassword(auth, this.form.email, this.form.password)
      .then(async (userCredential) => {
        await updateProfile(userCredential.user, { displayName: this.form.name });
        this.$router.push("/").catch(() => {});
      })
      .catch((error) => {
        alert(error.message);
      });
  },

  async Login_to(event) {
    event.preventDefault();
    const auth = getAuth();
    await signInWithEmailAndPassword(
      auth,
      this.form.email,
      this.form.password
    )
    .then(() => {
    this.$router.push("/about").catch(() => {});
    })
    .catch(() => {
      Swal.fire({
        icon: 'error',
        title: 'อีเมล หรือ รหัสผ่านผิด!!',
        footer: 'กรุณาตรวจสอบ อีเมล/รหัสผ่าน'
      })
    })
  }
  
},

};

</script>



<style scoped>
@import url('https://fonts.googleapis.com/css?family=Poppins:400,500,600,700&display=swap');
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Poppins', sans-serif;
}

::selection{
  background: #fa4299;
  color: #fff;
}
.wrapper{
  overflow: hidden;
  max-width: 390px;
  background: #fff;
  padding: 30px;
  border-radius: 5px;
  box-shadow: 0px 15px 20px rgba(0,0,0,0.1);
  max-height: 100%;
}
.wrapper .title-text{
  display: flex;
  width: 200%;
}
.wrapper .title{
  width: 50%;
  font-size: 35px;
  font-weight: 600;
  text-align: center;
  transition: all 0.6s cubic-bezier(0.68,-0.55,0.265,1.55);
}
.wrapper .slide-controls{
  position: relative;
  display: flex;
  height: 50px;
  width: 100%;
  overflow: hidden;
  margin: 30px 0 10px 0;
  justify-content: space-between;
  border: 1px solid lightgrey;
  border-radius: 5px;
}
.slide-controls .slide{
  height: 100%;
  width: 100%;
  color: #fff;
  font-size: 18px;
  font-weight: 500;
  text-align: center;
  line-height: 48px;
  cursor: pointer;
  z-index: 1;
  transition: all 0.6s ease;
}
.slide-controls label.signup{
  color: #000;
}
.slide-controls .slider-tab{
  position: absolute;
  height: 100%;
  width: 50%;
  left: 0;
  z-index: 0;
  border-radius: 5px;
  background: -webkit-linear-gradient(left, #b2454e, #fa4299);
  transition: all 0.6s cubic-bezier(0.68,-0.55,0.265,1.55);
}
input[type="radio"]{
  display: none;
}
#signup:checked ~ .slider-tab{
  left: 50%;
}
#signup:checked ~ label.signup{
  color: #fff;
  cursor: default;
  user-select: none;
}
#signup:checked ~ label.login{
  color: #000;
}
#login:checked ~ label.signup{
  color: #000;
}
#login:checked ~ label.login{
  cursor: default;
  user-select: none;
}
.wrapper .form-container{
  width: 100%;
  overflow: hidden;
}
.form-container .form-inner{
  display: flex;
  width: 200%;
}
.form-container .form-inner form{
  width: 50%;
  transition: all 0.6s cubic-bezier(0.68,-0.55,0.265,1.55);
}
.form-inner form .field{
  height: 50px;
  width: 100%;
  margin-top: 20px;
}
.form-inner form .field input{
  height: 100%;
  width: 100%;
  outline: none;
  padding-left: 15px;
  border-radius: 5px;
  border: 1px solid lightgrey;
  border-bottom-width: 2px;
  font-size: 17px;
  transition: all 0.3s ease;
}
.form-inner form .field input:focus{
  border-color: #fc83bb;
  /* box-shadow: inset 0 0 3px #fb6aae; */
}
.form-inner form .field input::placeholder{
  color: #999;
  transition: all 0.3s ease;
}
form .field input:focus::placeholder{
  color: #b3b3b3;
}
.form-inner form .pass-link{
  margin-top: 5px;
}
.form-inner form .signup-link{
  text-align: center;
  margin-top: 30px;
}
.form-inner form .pass-link a,
.form-inner form .signup-link a{
  color: #fa4299;
  text-decoration: none;
}
.form-inner form .pass-link a:hover,
.form-inner form .signup-link a:hover{
  text-decoration: underline;
}
form .btn{
  height: 50px;
  width: 100%;
  border-radius: 5px;
  position: relative;
  overflow: hidden;
}
form .btn .btn-layer{
  height: 100%;
  width: 300%;
  position: absolute;
  left: -100%;
  background: -webkit-linear-gradient(right, #b2454e, #fa4299, #b2454e, #fa4299);
  border-radius: 5px;
  transition: all 0.4s ease;;
}
form .btn:hover .btn-layer{
  left: 0;
}
form .btn input[type="submit"]{
  height: 100%;
  width: 100%;
  z-index: 1;
  position: relative;
  background: none;
  border: none;
  color: #fff;
  padding-left: 0;
  border-radius: 5px;
  font-size: 20px;
  font-weight: 500;
  cursor: pointer;
}

</style>
