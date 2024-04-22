<script setup>
import { ref, computed } from 'vue';
import { RouterView } from 'vue-router'

const product = ref('');
const price = ref('');
const items = ref({});
const sold = ref({});

const onSubmitForm = () => {
  fetch('https://bornegarderoben-9624d-default-rtdb.europe-west1.firebasedatabase.app/items.json', {
    method:'POST',
    body: JSON.stringify({
      product: product.value,
      price: price.value,
  }),
})
    .then(() => {
      getItems();
    });

  product.value = '';
  price.value = '';
};

const getItems = () => {
  fetch('https://bornegarderoben-9624d-default-rtdb.europe-west1.firebasedatabase.app/items.json', {
    method:'GET',
})
    .then((rawResponse) => { //tager den rå data
      return rawResponse.json(); //omdanner det til json (JavaScript Object Notation)
    })
    .then((response) => {  //her omdanner den det til javascript object
      console.log(response);
      if (response == null) {
  items.value = 0;
} else {
items.value = response;
};
    });
};

getItems();

const deleteItem = (itemId) => {
  fetch(`https://bornegarderoben-9624d-default-rtdb.europe-west1.firebasedatabase.app/items/${itemId}.json`, {
    method: 'DELETE',
  }).then(() => {
    getItems();
  });
};

const getSold = () => {
  fetch('https://bornegarderoben-9624d-default-rtdb.europe-west1.firebasedatabase.app/sold.json', {
    method:'GET',
})
    .then((rawResponse) => {
      return rawResponse.json();
    })
    .then((response) => {
      console.log(response);
      if (response == null) {
  sold.value = 0;
} else {
sold.value = response;
};
    });
};

getSold();

const totalPrice = computed(() => {
  console.log(Object.values(sold.value))
  return Object.values(sold.value).reduce((total, currentItem) => {
    return total + currentItem.price;
  }, 0);
});

</script>

<template>
  <div>
        <img class="logo" src="./assets/logo.png">
    </div>

<div class="body1">
  <div class="box">
    <h1>Tilføj varer:</h1>
  <form v-on:submit.prevent="onSubmitForm">
    <p>Varetekst</p>
    <input class="inputfield" type="text" v-model="product" required/>
    <p>Pris</p>
    <input class="inputfield" type="number" v-model="price" required/>
    <br>
    <br>
    <button class="submitbutton" type="submit">Tilføj vare</button>
  </form>
  </div></div>

  <div class="box">
    <h1>Tilføjede varer:</h1>
      <div class="headline1"><h2>Varetekst</h2>
      <div class="product" v-for="(item, itemId) in items">
        <button class="deletebutton" @click="deleteItem(itemId)">Slet</button> {{ item.product }} <hr class="hr1">
      </div></div>
      <div class="headline2"><h2>Pris</h2>
      <div class="price" v-for="(item, itemId) in items">
        {{ item.price }} kr.<hr class="hr1">
      </div></div>
  </div>
  
  <div class="body1">
    <div class="box">
    <h1>Solgt:</h1>
      <div class="headline1"><h2>Varetekst</h2>
      <div class="product" v-for="(item, price, id) in sold">
        {{ item.product }} <hr class="hr1">
      </div></div>
      <div class="headline2"><h2>Pris</h2>
      <div class="price" v-for="(item, price, id) in sold">
        {{ item.price }} kr. <hr class="hr1">
      </div></div>
      <div class="headline"><h2>Solgt for i alt</h2>
    <div class="price">
      {{ totalPrice }} kr.
    </div></div>
  </div></div>


  <RouterView />
</template>

<style >
</style>
