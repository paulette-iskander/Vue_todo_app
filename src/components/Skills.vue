
<template>
  <div class="hello">
    <div class="holder">
      <form @submit.prevent="addSkill">
      <input type="text" placeholder="Enter a skill you have .." v-model="skill" v-validate="'min:5'" name="skill">
     <transition name="alert-in" enter-active-class="animated flipInX" leave-active-class="animated flipOutX">
      <p class="alert" v-if="errors.has('skill')">{{errors.first('skill')}}</p>
     </transition>
      </form>

      <ul>
        <transition-group name="list" enter-active-class="animated bounceInUp" leave-active-class="animated bounceOutDown">
        <li v-for="(data,index) in skills" :key='index'> {{data.skill}}
        <i class="fa fa-minus-circle" v-on:click="remove(index)"></i>
        </li>
        </transition-group>
      </ul> 
      <p>There are the skills that you posses.</p>
    </div>

  </div>
</template>

<script>
/* eslint-disable */

export default {
  data() {
    return {
      skill: "",
      skills: [{ skill: "Vue.js" }, { skill: "Frontend Developer" }]
    };
  },
  methods: {
    addSkill() {
      this.$validator.validateAll().then(result => {
        if (result) {
          this.skills.push({ skill: this.skill });
          //this.skill = "";
          this.$http
            .post("http://localhost:3000/todo", { skill: this.skill })
            .then(function(response) {
              console.log(response.data);
              this.skill = "";
            });
        }
      });
    },
    remove(index) {
      this.skill = this.skills[index];
      console.log(this.skill);
      this.skills.splice(index, 1);
      this.$http
        .delete("http://localhost:3000/todo/" + this.skill._id)
        .then(response => {
          console.log("Deleted");
        });
    }
  },
  mounted() {
    this.$http.get("http://localhost:3000/todo").then(response => {
      this.skills = response.data;
      console.log(this.skills);
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
@import "https://cdn.jsdelivr.net/npm/animate.css@3.5.1";
@import "https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css";
.holder {
  background: #fff;
}
ul {
  margin: 0;
  padding: 0;
  list-style-type: none;
}
ul li {
  padding: 30px;
  font-size: 1.3em;
  background-color: #e0edf4;
  border-left: 5px solid #3eb3f6;
  margin-bottom: 2px;
  color: #3e5252;
}
p {
  text-align: center;
  padding: 30px 0;
  color: grey;
}
.container {
  box-shadow: 0px 0px 40px lightgray;
}
input {
  width: calc(100% - 40px);
  border: 0;
  padding: 20px;
  font-size: 1.3em;
  background-color: #323333;
  color: #687f7f;
}
.alert {
  background: #fdf2ce;
  font-weight: bold;
  display: inline-block;
  padding: 5px;
  margin-top: -20px;
}
.alert-in-enter-active {
  animation: bounce-in 0.5s;
}
.alert-in-leave-active {
  animation: bounce-in 0.5s reverse;
}
@keyframes bounce-in {
  0% {
    transform: scale(0);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(1);
  }
}
</style>
