<script setup>
import { ref } from 'vue';
import { useRulesStore } from '../stores/rulesStore';

import { toRaw } from 'vue';

const rulesStore = useRulesStore();

const httpMethod = ref('GET');
const url = ref('');
const headers = ref('');
const body = ref('');
const response = ref(null);


function displayExtensionRule(extensionRule) {
    const heading = document.getElementsByClassName("box")[0]
    const target = toRaw(extensionRule)
    heading.innerHTML = target.rule.action.type
  }

function submitRequest(ev) {
  ev.preventDefault();
  const formObject = {
    httpMethod: httpMethod.value,
    url: url.value,
    headers: JSON.parse(headers.value || '{}'),
    body: body.value
  };
  const matchedRule = rulesStore.requestMatcher(formObject)[0];
  if (matchedRule) {
    console.log(matchedRule);
    displayExtensionRule(matchedRule)
  } else {
    alert("No rule matched")
  }
  // httpMethod.value = 'GET';
  // url.value = '';
  // headers.value = '';
  // body.value = '';
}
</script>

<template>
  <div>
    <form @submit.prevent="submitRequest">
      <div>
        <p>HTTP Method:</p>
        <select v-model="httpMethod" id="method">
          <option>GET</option>
          <option>POST</option>
          <option>PUT</option>
          <option>DELETE</option>
          <option>PATCH</option>
        </select>
      </div>
      <div>
        <p>Request URL:</p>
        <input type="url" v-model="url" id="httpRequestURL" required />
      </div>
      <div>
        <p>HTTP Headers:</p>
        <textarea
          v-model="headers"
          id="headers"
          value="HTTP Headers"
        ></textarea>
      </div>
      <div>
        <p>Body:</p>
        <textarea v-model="body" id="body" value="Body"></textarea>
      </div>
      <button type="submit">Submit Request</button>
    </form>
    <div v-if="response">
      <h3>Response</h3>
      <pre>{{ response }}</pre>
    </div>
  </div>
</template>

<style scoped>
form {
  display: flex;
  flex-direction: column;
}

form > div {
  margin-bottom: 1rem;
}

label {
  margin-bottom: 0.5rem;
}

input,
select,
textarea,
button {
  padding: 0.5rem;
  font-size: 1rem;
}
</style>
