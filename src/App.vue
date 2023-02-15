<script setup>
import { ref, reactive, computed, onMounted, watch } from "vue";

//component imports
import ChildComp from "./ChildComp.vue";
import logo from "./assets/logo.svg";
import deleteIconRegular from "./assets/circle-xmark-regular.svg";
import HelloWorld from "./components/HelloWorld.vue";
import TheWelcome from "./components/TheWelcome.vue";
import fruitsList from "./assets/data";

// reactive
const myTarget = ref("what is my aim?");
const myTargetUnknown = ref(true);
const text = ref("");
const myVar = ref(true);
const counter = reactive({
  count: 0,
});
let id = 0;
const taskInput = ref("");
const submittedTask = ref([]);
const hideCompleted = ref(false);
const someText = ref(null);
const externalTaskId = ref(1);
const externalTask = ref({});
const greeting = ref("hello from parent comp");
const childMsg = ref("no message yet");

// Logging for info / debug
console.log("myTarget: ", myTarget.value);
console.log("taskInput: ", taskInput);
console.log("hidecompleted: ", hideCompleted.value);
console.log("fruitsList: ", fruitsList);

// methods
const increment = () => {
  counter.count++;
};

const decrement = () => {
  counter.count--;
};

//v-model kullanıldığı için gerek kalmadı
/* const onInput = (event) => {
  text.value = event.target.value;
}; */

console.log("main body- myVar: ", myVar.value);
console.log("main body- submittedTask.value: ", submittedTask.value);

const toggleHandler = () => {
  myVar.value = !myVar.value;
  console.log("myVar in the toggle function: ", myVar.value);
};

function aimHandler() {
  if (myTargetUnknown.value == true) {
    myTargetUnknown.value = false;
    myTarget.value = "I will become a Vue.js Master";
  } else {
    myTargetUnknown.value = true;
    myTarget.value = "What is my aim";
  }
}

/* const enterTask = (event) => {
  console.log(event.target.value);
  taskInput.value = event.target.value;
};
 */
const submitTask = () => {
  //event.preventDefault() not needed bc 'submit.prevent' event listener is used
  if (taskInput.value !== "") {
    submittedTask.value.push({
      id: id++,
      taskText: taskInput.value,
      done: false,
    });
  }
  // erase the input field
  taskInput.value = "";
  console.log("submittedTask.value: ", submittedTask.value);
};

const deleteTask = (taskIndex) => {
  console.log("task index: ", taskIndex);
  submittedTask.value.splice(taskIndex, 1);
  console.log("submittedTask after delete: ", submittedTask.value);
};

// my code. too long. using tutorial's code
/* const filteredTasks = computed(() => {
  if (hideCompleted.value) {
    console.log("in computed: hideCompleted.value: ", hideCompleted.value);
    console.log("in computed: submittedTask.value: ", submittedTask.value);
    let returnArray = submittedTask.value.filter((task) => {
      console.log("task: ", task);
      return task.done == false;
    });
    console.log("hideCompleted, return array: ", returnArray);
    return returnArray;
  } else {
    return submittedTask.value;
  }
}); */

// tutorial's code, tweaked
const filteredTasks = computed(() => {
  return hideCompleted.value
    ? submittedTask.value.filter((task) => {
        return !task.done;
      })
    : submittedTask.value;
});

onMounted(() => {
  console.log("component is onMounted!!!");
  someText.value.textContent = "here, as promised, it is updated!!";
  // runs after mounting is done.
  // but, the fruitsList on the DOM is modified in first interaction with DOM
  fruitsList[0] = "changed Fruit on Mount";
  console.log("fruitsList in onMounted()", fruitsList);
});

/* const fetchTaskFromApi = async () => {
  console.log("fetch clicked");
  console.log("externalTaskId.value", externalTaskId.value);
  let response = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${externalTaskId.value}`
  );
  externalTask.value = await response.json();
  console.log(externalTask.value);
  console.log(externalTask.value.title);
  console.log(externalTask.value.userId);
  console.log(externalTask.value.id);
  externalTaskId.value++;
  console.log("new externalTaskId.value", externalTaskId.value);
}; */

//alternative

const fetchTaskFromApi = () => {
  console.log("fetch clicked");
  console.log("externalTaskId.value", externalTaskId.value);
  externalTaskId.value++;
  console.log("increased externalTaskId.value", externalTaskId.value);
};

watch(counter, (newCounter) => {
  console.log("new Counter: ", newCounter.count);
  if (newCounter.count >= 10) {
    text.value = "counter changed to 10+";
  } else {
    text.value = "counter below 10";
  }
});

watch(externalTaskId, async (newExternalTaskId) => {
  const res = await fetch(
    `https://jsonplaceholder.typicode.com/todos/${newExternalTaskId}`
  );
  externalTask.value = await res.json();
  console.log("externalTask.value: ", externalTask.value);
});

const receiveMsgFromChild = (msg) => {
  console.log("messsage from child: ", msg);
  childMsg.value = msg;
};
</script>

<!-- Templates ********************** ********************** ********************** -->

<template>
  <div class="container">
    <div>Hello world of Vue.js</div>

    <div class="aim-container" @click="aimHandler">
      {{ myTarget }}
    </div>
    <div
      :class="{
        'image-container': myTargetUnknown,
        'image-container vis': !myTargetUnknown,
      }"
    >
      <img :src="logo" />
    </div>

    <div class="count-button-container">count: {{ counter.count }}</div>
    <div class="increment-decrement-btns">
      <button @click="increment">increment</button>
      <button @click="decrement">decrement</button>
    </div>

    <div class="input-wrapper">
      <input v-model="text" placeholder="type something" />
      <p>{{ text }}</p>
    </div>
    <br />
    <div class="toggle-button-container">
      <button @click="toggleHandler">toggle</button>
      <p v-if="myVar">This is perfect. MyVar is {{ myVar }}</p>
      <p v-else>MyVar is not true. It is {{ myVar }}</p>
    </div>
    <div class="fruits-list">
      <h2>Fruits List</h2>
      <ul>
        <li v-for="fruit in fruitsList" :key="fruitsList.indexOf(fruit)">
          {{ fruit }}
        </li>
      </ul>
    </div>

    <div class="todo-container">
      <h2>To do list:</h2>
      <form class="form-container" @submit.prevent="submitTask">
        <label>Enter task</label>
        <input v-model="taskInput" />
        <button type="submit">enter task</button>
      </form>
      <div>
        <ul>
          <li v-for="task in filteredTasks" :key="task.id">
            <div class="task-wrapper">
              <div>
                <input type="checkbox" v-model="task.done" />
              </div>
              <div
                :class="{ taskText: !task.done, 'taskText done': task.done }"
              >
                {{ task.taskText }}
              </div>
              <div class="deleteButton" @click="deleteTask(task.id)">
                <img :src="deleteIconRegular" />
              </div>
            </div>
          </li>
        </ul>
        <div>
          <button
            class="hide-completed-btn"
            @click="hideCompleted = !hideCompleted"
          >
            {{ hideCompleted ? "Show All" : "Hide Completed" }}
          </button>
        </div>
        <div>
          <p style="color: red" ref="someText">This text is updated onmount</p>
        </div>
      </div>
    </div>
    <!-- fetch external todos from api -->
    <div class="external-todos-container">
      <button @click="fetchTaskFromApi">get another task</button>
      <div class="external - todo">
        <pre>{{ externalTask }}</pre>
      </div>
    </div>
    <div>
      <p>Message From child: {{ childMsg }}</p>
    </div>
    <ChildComp
      @responseFromChild="receiveMsgFromChild"
      :greetingToChild="greeting"
      :textToChild="text"
      :countToChild="counter.count"
    />
  </div>
</template>

<!--  styles: ****** ************************************************************************  -->
<style scoped lang="scss">
.container {
  display: flex;
  flex-direction: column;
  color: white;
}

.input-wrapper {
  width: 500px;
}
.input-wrapper input {
  width: 100%;
}

.aim-container {
  display: inline-block;
  width: 300px;
  color: darkblue;
  border: 1px solid black;
  background-color: green;
  padding: 10px;
}

.image-container {
  display: none;
}
.image-container.vis {
  display: block;
  width: 24px;
  margin: 10px;
}
.image-container.vis img {
  width: 100%;
}

.count-button-container {
  display: flex;
}

.toggle-button-container {
  width: 200px;
}
.toggle-button-container button {
  width: 100%;
}

.form-container {
  display: flex;
  flex-direction: column;
}

.todo-container {
  display: flex;
  flex-direction: column;
  width: 300px;
}

.todo-container button {
  width: 100px;
  margin: 5px 0;
}

.task-wrapper {
  display: flex;
  flex-direction: row;
  justify-content: flex-start;
  background-color: gray;
  margin: 3px 0;
  padding: 3px 3px;

  .taskText {
    margin: 0 5px 0 10px;
    &.done {
      text-decoration: line-through;
      color: yellow;
    }
  }
}
.deleteButton {
  margin: 0 5px 0 auto;
  width: 20px;
}

.task-wrapper img:hover {
  cursor: pointer;
}

@media (min-width: 1024px) {
}
</style>
