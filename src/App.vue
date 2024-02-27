<script setup>
// vue3 컴포지션 api 가져오기
import { reactive, computed } from 'vue';

// reactive함수: 객체를 반응성으로 만들어주며 객체의 값이 변경되면 리렌더링하여 화면 업데이트
const data = reactive({
  newItem: '',
  items: [],
});

// 할일 배열에서 할일의 완료가 true인 요소의 배열 길이 구하기
// 렌더링마다 각각 변수를 매번 새로 만드므로 변경된 값이 변수에 각각 할당됨 그러므로 const를 사용함
// 완료된 요소를 새로운 배열로 만들고 갯수를 구해줌
const isComplete = computed(
  () => data.items.filter((item) => item.completed).length
);
// computed함수: data.items가 변경될때만 함수 실행(계산결과를 캐싱하여 성능 최적화)
const totalItems = computed(() => data.items.length);

// 할일추가: data.newItem은 입력필드와 연결되있으므로 빈값이 아닐경우 추가
// 추가된 데이터 마다 고유 id 추가하여 데이터 구분 가능하도록 해줌
// 할일추가후 입력필드 빈칸으로 초기화
const addItem = () => {
  if (data.newItem !== '') {
    data.items.push({
      id: data.items.length + 1,
      text: data.newItem,
      completed: false,
    });
    // 할일 추가뒤 입력필드와 연결된 데이터를 빈문자로 초기화 시켜줌(엔터 후 입력 란 초기화 시켜줌)
    data.newItem = '';
  }
};

// 할일삭제
function deleteItem(id) {
  const itemToDelete = data.items.find((item) => item.id === id);
  console.log(itemToDelete);
  // 삭제할 아이템의 위치찾기
  const index = data.items.indexOf(itemToDelete);
  // splice(시작위치, 삭제개수)
  data.items.splice(index, 1);
}
function test() {
  console.log('11');
}
test();
</script>

<template>
  <main class="app">
    <h1>Simple to-do list</h1>
    <div class="todo_count">
      <!-- 템플릿 내부에 데이터 표현시 {{ }} 사용 -->
      완료: {{ isComplete }} / 할 일: {{ totalItems }}
    </div>
    <div class="todo_add">
      <!-- v-model 디렉티브는 폼요소와 데이터를 양방향으로 연결 -->
      <!-- v-on:이벤트명으로 이벤트 연결 -->
      <input
        type="text"
        v-model="data.newItem"
        v-on:keyup.enter="addItem()"
        placeholder="할 일을 입력하세요"
        title="할 일을 입력하세요"
      />
      <button type="submit" class="add_btn" v-on:click="addItem()">Add</button>
    </div>
    <ul class="todo_list">
      <!-- v-bind 디렉티브는 데이터를 단방향으로 연결하여 가져오기만함 -->
      <!-- 리스트는 고유 id를 key속성에 연결하여 리스트의 순서가 바뀌어도 구별가능하게함 -->
      <!-- todo.completed가 true면 completed문자열이 클래스명이 됨 -->
      <li
        v-for="(todo, index) in data.items"
        v-bind:key="todo.id"
        v-bind:class="{ completed: todo.completed }"
      >
        <!-- id가 달라야되므로 id속성과 todo.id 값을 단방향 연결 -->
        <!-- 라벨클릭시 for로 연결된 체크박스가 클릭되어 true, false가 발생되며
        v-model="todo.completed"에 의해 completed속성값이 true, false가됨 -->
        <input
          v-bind:id="`check${todo.id}`"
          type="checkbox"
          v-model="todo.completed"
        />
        <!-- 라벨클릭 시 for로 연결된 체크박스에서 true,false가 발생 -->
        <!-- v-model로 연결된 데이터의 내용이 true,false로 변경됨 -->
        <label v-bind:for="`check${todo.id}`">{{ todo.text }}</label>
        <button
          type="button"
          v-on:click="deleteItem(todo.id)"
          class="remove_btn"
        >
          Remove
        </button>
      </li>
    </ul>
  </main>
</template>

<style scoped>
.app {
  padding: 40px;
}
.app h1 {
  font-size: 30px;
  font-weight: 700;
  margin-bottom: 20px;
}

.todo_count {
  margin: 10px 0;
}

.todo_add {
  display: flex;
}
.todo_add input {
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  flex-grow: 1;
}
.todo_add .add_btn {
  height: 40px;
  padding: 0 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  margin-left: 10px;
  color: #fff;
  background: #333;
  border: none;
}

.todo_list {
  margin-top: 20px;
}
.todo_list li {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}
.todo_list label {
  flex-grow: 1;
}
.todo_list li.completed label {
  color: #ccc;
  text-decoration: line-through;
}
.todo_list .remove_btn {
  margin-left: auto;
  height: 32px;
  padding: 0 5px;
  background: none;
  border: 1px solid var(--point-color1);
  color: var(--point-color1);
  border-radius: 4px;
  margin-left: 20px;
}
</style>
