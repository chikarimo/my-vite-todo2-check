<script setup>
import {ref} from "vue";
import {statuses} from "../const/statuses";

const input = ref("");
const inputDate = ref("");

const onSubmitForm = () => {

    //ローカルストレージからデータを取得
    const items = JSON.parse(localStorage.getItem("items")) || [];

    //保存するタスクのオブジェクト作成
    const newItem = {
        chkflag: false,
        id: items.length,
        content: input.value,
        limit: inputDate.value,
        state: statuses.NOT_START,
        onEdit: false,
    };

    //配列にデータを保管
    items.push(newItem);
    //配列データをローカルストレージに保存
    localStorage.setItem("items",JSON.stringify(items));
}
</script>

<template>
    <header>
        <form v-on:submit.passive="onSubmitForm">
            <label class="box10">TODO：<input type="text" v-model="input" required/></label><br />
            <label class="box10">期限 &nbsp; ：<input type="date" v-model="inputDate" required/></label>
            <input type="submit" value="登録!" class="btn-stitch" id="sub" />
            <TodoListView />
        </form>
    </header>


</template>

<style scoped>
#sub{
  margin: 0.5em;
  padding: 0.5em 1em;
  text-decoration: none;
  background: #668ad8;
  color: #FFF;
  border-radius: 4px;
  box-shadow: 0px 0px 0px 5px #668ad8;
  border: dashed 1px #FFF;
}

#sub:hover {
  border: dotted 1px #FFF;
}

.box10 {
    padding: 0.5em 1em;
    margin: 2em 0;
    color: #ffffff;
    background: #8f0a7d;/*背景色*/
    border-top: solid 6px #7f14d6;
    box-shadow: 0 3px 4px rgba(0, 0, 0, 0.32);/*影*/
}
.box10 input {
    margin: 1.5em; 
    padding: 0;
}

input:focus {
    background-color:rgba(222, 242, 255, 0.8);
}


</style>