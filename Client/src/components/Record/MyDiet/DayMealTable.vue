<template>
  <div>
    <div class="day-calendar">
      <!-- <span id="month-text"></span> -->
      <div class="day">
        <!-- 전날로 넘어가는 버튼 -->
        <!-- <span class="material-icons navigate-before"> navigate_before </span> -->
        <!-- 오늘 날짜 -->
        <span id="today">
          <input type="date" v-model="userTargetDate" />
        </span>
        <!-- 다음날로 넘어가는 버튼 -->
        <!-- <span class="material-icons navigate-next"> navigate_next </span> -->

        <!-- 수정버튼 -->
        <span class="material-icons settings" @click="editMealTable">
          settings
        </span>
      </div>
      <div class="menu-card">
        <div class="morning">
          <div class="morning-text animate__animated animate__fadeInUp">
            아침
          </div>
          <!-- 만약 데이터가 있으면 보여주고, 없으면 찾으러가기 버튼 활성화-->
          <div class="meal-table-el animate__animated animate__zoomIn" @click="updateMenu(morningData[0].menus, 0)">
            <div 
              v-for="(food, idx) in morningData[0].menus"
              :key="idx"
            >
              {{ food.foodName }} 
            </div>
            <!-- <button
              class="bttn-unite bttn-md bttn-success goToRecommend-btn"
              @click="goPocket"
            >
              내 식단 찾기
            </button> -->
          </div>
        </div>
        <div class="lunch">
          <div
            class="lunch-text to-right-underline animate__animated animate__fadeInUp"
          >
            점심
          </div>
          <div class="meal-table-el animate__animated animate__zoomIn" @click="updateMenu(lunchData[0].menus, 1)">
            <div 
              v-for="(food, idx) in lunchData[0].menus"
              :key="idx"
            >
              {{ food.foodName }} 
            </div>
            <!-- 권장칼로리와 비교해서 색으로 위험여부 보여주기 -->
            <!-- <span class="color-change">{{ sumFoodKcal }} kcal</span> -->
          </div>
        </div>
        <div class="dinner">
          <div
            class="dinner-text to-right-underline animate__animated animate__fadeInUp"
          >
            저녁
          </div>
          <div class="meal-table-el animate__animated animate__zoomIn" @click="updateMenu(dinnerData[0].menus, 2)">
            <div 
              v-for="(food, idx) in dinnerData[0].menus"
              :key="idx"
            >
              {{ food.foodName }} 
            </div>
            <!-- <span class="color-change">{{ sumFoodKcal }} kcal</span> -->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import moment from "vue-moment";
import { mapActions } from "vuex";
export default {
  name: "DayMealTable",
  data() {
    return {
      userForm: {
        userId: "tori",
        userName: "채은",
        userHeight: 200,
        userWeight: 200,
        userKcal: 2000,
        // 0 남자 1 여자
        userGender: 1,
        // 0 적음 1보통 2많음
        userActivity: "적음",
      },
      editFlag: false,
      show: true,
      sumFoodKcal: 1000,
      userTargetDate: new Date(+new Date() + 3240 * 10000)
        .toISOString()
        .split("T")[0], // 사용자가 선택한 날짜
      todayStr: "",
      morningData: [{ menus: "식단이 없어요!" }],
      lunchData: [{ menus: "식단이 없어요!" }],
      dinnerData: [{ menus: "식단이 없어요!" }],
    };
  },
  props: {
    targetDate: String,
    dayData: Array,
  },
  methods: {
    ...mapActions(["menusUpdate", "mealTimeUpdate", "targetDateUpdate"]),
    goPocket() {
      this.$router.push({ name: "menu" });
    },
    updateMenu(menus, mealTime) {
      if(this.editFlag){
        this.mealTimeUpdate(mealTime)
        if(menus[0].foodName=="작성된 식단이 없어요!"){
          this.menusUpdate([])
        }
        else{
          this.menusUpdate(menus)
        }
        this.targetDateUpdate(this.userTargetDate);
        this.$router.push({
          name: "basket",
          params: { updateDate: this.userTargetDate },
        });
      }
    },
    editMealTable() {
      const editMeal = document.querySelectorAll(".meal-table-el");
      if (this.editFlag == true) {
        this.editFlag = false;
        for (let i = 0; i < editMeal.length; i++) {
          editMeal[i].classList.remove("clicked");
        }
      } else {
        this.editFlag = true;
        for (let i = 0; i < editMeal.length; i++) {
          editMeal[i].classList.add("clicked");
        }
      }
    },
  },
  watch: {
    userTargetDate() {
      this.$emit("dateChange", this.userTargetDate);
    },
    dayData() {
      this.morningData =
        this.dayData.filter((menu) => menu.mealTime == "0").length > 0
          ? this.dayData.filter((menu) => menu.mealTime == "0")
          : [{ menus: [{ foodName: "작성된 식단이 없어요!" }] }];
      this.lunchData =
        this.dayData.filter((menu) => menu.mealTime == "1").length > 0
          ? this.dayData.filter((menu) => menu.mealTime == "1")
          : [{ menus: [{ foodName: "작성된 식단이 없어요!" }] }];
      this.dinnerData =
        this.dayData.filter((menu) => menu.mealTime == "2").length > 0
          ? this.dayData.filter((menu) => menu.mealTime == "2")
          : [{ menus: [{ foodName: "작성된 식단이 없어요!" }] }];
    },
  },
  mounted() {
    this.userTargetDate = this.targetDate;
  },
};
</script>

<style scoped>
.day-calendar {
  width: 40vw;
  height: 100%;
  border-radius: 1.25rem;
}
.day {
  font-size: 1.2vw;
  text-align: center;
  margin-left: 1.5vw;
  font-weight: 700;
}

#month-text {
  font-size: 1vw;
  display: inline-block;
  text-align: center;
  margin-bottom: 1vw;
  margin-top: -1vw;
  position: relative;
  font-weight: 700;
}
.meal-table-el {
  width: calc(35vw / 3);
  height: 15vw;
  border: 0.188rem solid #25ab9b;
  margin: 0.5vw;
  border-radius: 1.1vw;
  position: relative;
  top: 1.1vw;
  text-align: center;
  font-size: 1.1vw;
  display: flex;
  flex-direction: column;
  justify-content: center;
  font-weight: 700;
}
.meal-table-el.clicked {
  animation-name: vibration;
  animation-duration: 0.5s;
  animation-iteration-count: infinite;
}
.meal-table-el.clicked:hover {
  cursor: pointer;
}

.menu-card {
  display: flex;
  justify-content: center;
  align-items: center;
  align-content: center;
  height: 19vw;
  top: 0.6vw;
  position: relative;
}

.morning-text,
.lunch-text,
.dinner-text {
  font-size: 1.2vw;
  text-align: center;
  font-weight: 700;
}

.goToRecommend-btn {
  border-radius: 0.3vw;
  width: 6.25rem;
  font-size: 0.875rem;
  margin: 0 auto;
  position: relative;
  margin-top: 0.625rem;
}
.navigate-before {
  font-size: 1.6vw;
  vertical-align: bottom;
  color: #333;
  position: relative;
  left: -1.5vw;
}
.navigate-next {
  font-size: 1.6vw;
  vertical-align: bottom;
  color: #333;
  position: relative;
  left: 1.5vw;
}
.material-icons:hover {
  cursor: pointer;
  color: #25ab9b;
  transform: scale(1.2);
  transition: transform 0.1s;
  background-color: rgb(221, 221, 221);
  border-radius: 50%;
}

.settings {
  position: relative;
  font-size: 1.4vw;
  left: 2.6vw;
  bottom: -0.2vw;
}

@keyframes vibration {
  0% {
    -webkit-transform: rotate(-5deg);
  }
  50% {
    -webkit-transform: rotate(5deg);
  }
  100% {
    -webkit-transform: rotate(-5deg);
  }
}
</style>
