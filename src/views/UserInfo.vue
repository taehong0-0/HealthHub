<template>
  <v-container>
    <v-row>
      <v-col cols="12" sm="6">
        <h2>유저 정보</h2>
        <v-text-field disabled v-model="userInfo.uName" label="이름"></v-text-field>
        <v-text-field disabled v-model="userInfo.sex" label="성별"></v-text-field>
        <v-text-field type="number" v-model="userInfo.age" label="나이"></v-text-field>
      </v-col>
      <v-col cols="12" sm="6">
        <h2>신체정보</h2>
        <v-text-field type="number" v-model="userInfo.height" label="키"></v-text-field>
        <v-text-field v-model="userInfo.weight" label="몸무게"></v-text-field>
        <h2>약점 부위</h2>
        <v-radio-group v-model="userInfo.weak">
          <v-radio label="하체" value="하체"></v-radio>
          <v-radio label="등" value="등"></v-radio>
          <v-radio label="가슴" value="가슴"></v-radio>
        </v-radio-group>
      </v-col>
      <v-divider vertical></v-divider>
      <v-col cols="12" sm="6">
        <h2>1RM</h2>
        <v-text-field type="number" v-model="userInfo.squat" label="스쿼트"></v-text-field>
        <v-text-field type="number" v-model="userInfo.bench" label="벤치프레스"></v-text-field>
        <v-text-field type="number" v-model="userInfo.dead" label="데드리프트"></v-text-field>
        <h2>숙련도</h2>
        <v-radio-group mandatory v-model="userInfo.proficiency">
          <v-radio label="초급자" value="초급자"></v-radio>
          <v-radio label="중급자" value="중급자"></v-radio>
          <v-radio label="고급자" value="고급자"></v-radio>
        </v-radio-group>
      </v-col>
    </v-row>
    <v-btn @click="test">완료</v-btn>
  </v-container>
</template>
<script>
import VueRouter from '@/router/index.js';
export default {
  created() {
    var id = localStorage.getItem('id');
    console.log(id);
    this.userInfo.uName = localStorage.getItem('uName');
    this.userInfo.email = localStorage.getItem('email');
    this.userInfo.sex = localStorage.getItem('sex');
    if (localStorage.getItem('sex') == 1) {
      this.userInfo.sex = '남자';
    } else if (localStorage.getItem('sex') == 2) {
      this.userInfo.sex = '여자';
    }
    fetch(`http://115.85.183.157:3000/register/userInfo/getUID/${id}`, {
      method: 'GET',
      headers: {
        'Content-Type': 'application/json',
      },
    })
      .then(res => res.json())
      .then(res => {
        var uID = res.uID;
        this.userInfo.uID = uID;
        console.log(uID);
      })
      .catch(err => {
        console.error('회원가입 중 에러 발생');
      });
  },
  data() {
    return {
      userInfo: {
        uID: 0,
        uName: '',
        age: null,
        email: '',
        sex: '',
        height: null,
        weight: null,
        squat: null,
        bench: null,
        dead: null,
        weak: '',
        proficiency: '',
      },
      check: {
        age: false,
        height: false,
        weight: false,
        squat: false,
        bench: false,
        dead: false,
        weak: false,
      },
    };
  },
  methods: {
    checkValid() {
      if (this.userInfo.age !== null && this.userInfo.age > 13) {
        this.check.age = true;
      }
      if (
        this.userInfo.height !== null &&
        this.userInfo.height > 130 &&
        this.userInfo.height < 230
      ) {
        this.check.height = true;
      }
      if (
        this.userInfo.weight !== null &&
        this.userInfo.weight > 30 &&
        this.userInfo.weight < 200
      ) {
        this.check.weight = true;
      }
      if (
        this.userInfo.squat !== null &&
        this.userInfo.squat > 0 &&
        this.userInfo.squat < this.userInfo.weight * 5
      ) {
        this.check.squat = true;
      }
      if (
        this.userInfo.dead !== null &&
        this.userInfo.dead > 0 &&
        this.userInfo.dead < this.userInfo.weight * 5
      ) {
        this.check.dead = true;
      }
      if (
        (this.userInfo.bench !== null) & (this.userInfo.bench > 0) &&
        this.userInfo.bench < this.userInfo.weight * 4
      ) {
        this.check.bench = true;
      }
      if (this.check.weak !== null) {
        this.check.weak = true;
      }
    },
    test() {
      this.saveUser();
    },
    saveUser() {
      this.checkValid();
      console.log(this.check);
      if (
        this.check.age &&
        this.check.height &&
        this.check.weight &&
        this.check.squat &&
        this.check.bench &&
        this.check.dead &&
        this.check.weak
      ) {
        if (this.userInfo.proficiency === '초급자') {
          this.userInfo.proficiency = 1;
        } else if (this.userInfo.proficiency === '중급자') {
          this.userInfo.proficiency = 2;
        } else if (this.userInfo.proficiency === '고급자') {
          this.userInfo.proficiency = 3;
        }
        const req = this.userInfo;
        fetch('http://115.85.183.157:3000/register/userInfo', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(req),
        })
          .then(res => res.json())
          .then(res => {
            if (res.success) {
              alert('성공');
              localStorage.clear();
              VueRouter.push({ path: '/' });
            }
          })
          .catch(err => {
            console.error('회원가입 중 에러 발생');
          });
        VueRouter.push({ path: '/' });
      } else {
        let message = '';
        for (const key in this.check) {
          if (this.check[key] === false) {
            message += key + ' ';
          }
        }
        alert(`${message} 정보를 제대로 입력해주세요.`);
        for (const key in this.check) {
          this.check[key] = false;
        }
      }
    },
  },
};
</script>
