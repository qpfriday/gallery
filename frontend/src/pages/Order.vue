<script>
import {computed, reactive} from "vue";
import axios from "axios";
import {addCommas} from "@/scripts/lib";
import router from "@/scripts/router";

export default {
  setup() {
    const state = reactive({
      items: [],
      form: {
        name:"",
        address:"",
        payment:"",
        cardNumber:"",
        items:"",

      }
    })
    const load = () => {
      axios.get("/api/cart/items").then(({data}) => {
        console.log(data);
        state.items = data;
      })
    }
    const submit = () => {
      const args = JSON.parse(JSON.stringify(state.form));
      args.items = JSON.stringify(state.items);
      axios.post("api/orders", args).then(() => {
        alert('주문을 완료하였습니다.');
        router.push({path: "/orders"})
      });
    };

    const computedPrice = computed(() => {
      let result =0;
      for (let i of state.items) {
        result += i.price - i.price * i.discountPer / 100
      }
      return result;
    });

    load();

    return {state, addCommas, computedPrice, submit}
  },
}
</script>

<template>
  <div class="order">
    <div class="container">
      <main>
        <div class="py-5 text-center"><h2>주문하기</h2>
          <p class="lead">Below is an example form built entirely with Bootstrap’s form controls. Each required form
            group has a validation state that can be triggered by attempting to submit the form without completing
            it.</p></div>
        <div class="row g-5">
          <div class="col-md-5 col-lg-4 order-md-last">
            <h4 class="d-flex justify-content-between align-items-center mb-3">
              <span class="text-primary">장바구니 목록</span>
              <span class="badge bg-primary rounded-pill">{{ state.items.length }}</span>
            </h4>
            <ul class="list-group mb-3">
              <li class="list-group-item d-flex justify-content-between lh-sm" v-for="(i, idx) in state.items" :key="idx">
                <div><h6 class="my-0">{{ i.name }}</h6></div>
                <span class="text-body-secondary">
                  {{ addCommas(i.price - i.price * i.discountPer / 100) }} 원
                </span></li>
            </ul>
            <h2 class="text-center total-price">
              {{ addCommas(computedPrice) }} 원
            </h2>
          </div>
          <div class="col-md-7 col-lg-8"><h4 class="mb-3">주문 정보</h4>
            <div class="needs-validation" novalidate="">
              <div class="row g-3">
                <div class="col-12"><label for="username" class="form-label">이름</label>
                  <input type="text"
                         class="form-control"
                         id="username"
                         placeholder="Username"
                         v-model="state.form.name">
                </div>
                <div class="col-12"><label for="address" class="form-label">주소</label>
                  <input type="text"
                         class="form-control"
                         id="address"
                         v-model="state.form.address">
                </div>
              </div>
              <hr class="my-4">
              <h4 class="mb-3">결제 수단</h4>
              <div class="my-3">
                <div class="form-check">
                  <input id="credit" name="paymentMethod" type="radio" class="form-check-input" checked="" v-model="state.form.payment" value="card">
                    <label class="form-check-label" for="card">신용카드</label>
                </div>
                <div class="form-check">
                  <input id="credit" name="paymentMethod" type="radio" class="form-check-input" checked="" v-model="state.form.payment" value="bank">
                  <label class="form-check-label" for="bank">무통장입금</label>
                </div>
              </div>
              <label for="cc-number" class="form-label">카드 번호</label>
              <input
                  type="text" class="form-control" id="cc-number" v-model="state.form.cardNumber">
              <hr class="my-4">
              <button class="w-100 btn btn-primary btn-lg" @click="submit()">결제하기</button>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<style scoped>
</style>