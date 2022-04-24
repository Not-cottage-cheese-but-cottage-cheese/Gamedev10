<template>
  <div class="container">
    <div class="title">
      <p>Какой пароль популярнее?</p>
    </div>
    <div class="game">
      <div class="box-l">
        <div class="score">Лучший результат</div>
        <div class="score">{{ counter.count }}</div>
        <div class="password">{{ password_1.password }}</div>
      </div>
      <div class="box-r">
        <div class="score">Текущий результат</div>
        <div class="score">{{ currentCount }}</div>
        <div class="password">{{ password_2.password }}</div>
        <div class="button-group">
          <button @click="check(password_2.position)">Популярнее</button>
          <button @click="check(password_1.position)">Не популярнее</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { PASSWORDS } from "@/utils/constants";
import { onMounted, ref } from "vue";
import { useCounterStore } from "@/store/counter";

Array.prototype.random = function () {
  return this[Math.floor(Math.random() * this.length)];
};

export default {
  name: "HightOrLess",
  setup() {
    const currentCount = ref(0);
    const counter = useCounterStore();
    const query = ref("");
    const password_1 = ref("");
    const password_2 = ref("");

    onMounted(() => {
      generatePasswords();
    });

    const generatePasswords = () => {
      password_1.value = PASSWORDS.random();
      password_2.value = PASSWORDS.random();
      while (password_1.value == password_2.value) {
        password_2.value = PASSWORDS.random();
      }
    };

    const check = (popularId) => {
      const realyPopularId =
        password_1.value.position < password_2.value.position
          ? password_1.value.position
          : password_2.value.position;
      if (realyPopularId === popularId) {
        currentCount.value++;
        if (currentCount.value > counter.count) {
          counter.set(currentCount.value);
        }
        password_1.value = password_2.value;
        password_2.value = PASSWORDS.random();
        while (password_1.value == password_2.value) {
          password_2.value = PASSWORDS.random();
        }
      } else {
        currentCount.value = 0;
        generatePasswords();
      }
    };

    return {
      check,
      query,
      counter,
      currentCount,
      password_1,
      password_2,
    };
  },
};
</script>

<style scoped lang="scss">
.container {
  & > .title {
    color: white;
    font-size: 30px;
    font-weight: 600;
  }
}

%box {
  display: flex;
  width: 50%;
  flex-direction: column;
  font-size: 20px;
  font-weight: 600;
  color: white;
  & > .score {
    display: flex;
    margin: 5px;
  }
}

.game {
  display: flex;
  flex-direction: row;
  & > .box-l {
    @extend %box;
    border-right: 1px solid white;
    & > .score {
      justify-content: end;
    }
    & > .password {
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 40px;
    }
  }

  & > .box-r {
    @extend %box;
    border-left: 1px solid white;
    & > .score {
      justify-content: start;
    }
    & > .password {
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 40px;
    }
  }
}
</style>
