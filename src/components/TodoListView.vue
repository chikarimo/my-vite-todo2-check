<script setup>
import {statuses} from "../const/statuses";
import { ref } from 'vue';

let items = ref(JSON.parse(localStorage.getItem("items")) || []);
let inputContent = ref();
let inputLimit = ref();
let inputState = ref();
let isShowModal = ref(false);

//エラーメッセージの内容
let errMsg = ref('');

//削除対象を表すID格納用
let deleteItemId = '';
let deleteItemContent = ref();

let isOnEditOther = false;
function onEdit(id){
    //他に編集モードのタスクがないか調べる
    items.value.map((item) => {
        if(item.onEdit){
            //onEditがtrueのタスクがある=編集中のものがある
            isOnEditOther = true;
            return;
        }
    });
    if(isOnEditOther){
        //エラーメッセージを表示する
        errMsg.value = "他に編集中のタスクがあります";
        isErrMsg.value = true;
        return;
    }
    
    items.value[id].onEdit = true;
    inputContent.value = items.value[id].content;
    inputLimit.value = items.value[id].limit;
    inputState.value = items.value[id].state;
}

let isErrMsg = ref(false);
function onUpdate(id) {
    if(inputContent.value == "" || inputLimit.value == ""){
        errMsg.value = 'タスクの内容と期限を入力してください。';
        isErrMsg.value = true;
        return;
    }
    //タスクの上書き保存を行う
    const newItem = {
        chkflag: items.value[id].chkflag,
        id: id,
        content: inputContent.value,
        limit: inputLimit.value,
        state: inputState.value,
        onEdit: false,
    };

    items.value.splice(id,1,newItem);
    localStorage.setItem("items",JSON.stringify(items.value));
    isErrMsg.value = false;
}

function onUpdateChk(id) {

    const newState = {
        chkflag: !items.value[id].chkflag,
        id: id,
        content: items.value[id].content,
        limit: items.value[id].limit,
        state: items.value[id].state,
        onEdit: false,
    };

    items.value.splice(id,1,newState);
    localStorage.setItem("items",JSON.stringify(items.value));

    isErrMsg.value = false;
}

const showDeleteModal = (id) => {
    isShowModal.value = true;
    deleteItemId = id;
    deleteItemContent = items.value[id].content
}

const onDeleteItem = () => {
    //タスクを削除する処理
    items.value.splice(deleteItemId,1);

    //IDを振りなおす
    items.value = items.value.map((item,index) => ({
        chkflag: item.chkflag,
        id: index,
        content: item.content,
        limit: item.limit,
        state: item.state,
        onEdit: item.onEdit,
    }));
    localStorage.setItem("items",JSON.stringify(items.value));
    isShowModal.value = false;
}

const onHideModal = () => {
    isShowModal.value = false;
}

const sortById = () => {
    items.value.sort((a,b) => a.id - b.id);
    localStorage.setItem("items",JSON.stringify(items.value));
}

const sortByLimit = () => {
    items.value.sort((a,b) => new Date(a.limit) - new Date(b.limit));
    localStorage.setItem("items",JSON.stringify(items.value));
}

const today = new Date();

</script>

<template>
    <main>
        <table>
            <th class="th-id">ID<button v-on:click="sortById()">↓</button></th>
            <th class="th-value">TODO</th>
            <th class="th-limit">期限<button v-on:click="sortByLimit()">↓</button></th>
            <th class="th-state">状態</th>
            <th class="th-edit">編集</th>
            <th class="th-delete">削除</th>
            <tr v-for = "item in items" :key="item.id" v-bind:class="[{ red: new Date(item.limit) < today},{ fin: item.chkflag }]">
                <td>{{ item.id }}</td>
                <td>
                        <input type="checkbox" v-bind:value="item.id" name="todo" v-on:change="onUpdateChk(item.id)" v-bind:checked="item.chkflag">
                        {{ item.content }}
                    <span v-if="!item.onEdit"></span>
                    <input v-else v-model="inputContent" type="text" required/>
                </td>
                <td>
                    <span v-if="!item.onEdit">{{ item.limit }}</span>
                    <input v-else v-model="inputLimit" type="date" required>
                </td>
                <td>
                    <span v-if="!item.onEdit">{{ item.state.value }}</span>
                    <select v-else v-model="inputState">
                        <option
                        v-for="state in statuses"
                        :key="state.id"
                        :value="state"
                        :selected="state.id == item.state.id"
                        >
                            {{state.value}}
                        </option>
                    </select>
                </td>
                <td>
                    <button v-if="!item.onEdit" v-on:click="onEdit(item.id)"  v-bind:class="{ hiddenBtn: item.chkflag}">編集</button>
                    <button v-else v-on:click="onUpdate(item.id)">完了</button>
                </td>
                <td><button v-on:click="showDeleteModal(item.id)">削除</button></td>
            </tr>
        </table>
        <p v-if="isErrMsg">{{ errMsg }}</p>
    </main>
    <div v-if="isShowModal" class="modal">
        <div class="modal-content">
            <p>{{deleteItemContent}}を削除してもよろしいですか？</p>
            <button v-on:click="onDeleteItem()">はい</button>
            <button v-on:click="onHideModal()">キャンセル</button>
        </div>
    </div>
</template>

<style scoped>
.modal{
    position:fixed;
    top:0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.5);
    display: flex;
    justify-content: center;
    align-items: center;
}

.modal-content{
    background: #fff;
    padding: 20px;
    border-radius: 8px;
}

.red{
    color:red;
}

table{
  border:1px solid black;
  border-collapse: collapse;
}

table th{
  border:1px solid black;
  background-color:rgba(200, 150, 229, 0.145);
}

table tr{
    background-color:rgba(185, 38, 171, 0.358);
}

table td{
  border:1px solid black;
  /* background-color:whitesmoke; */
  width:auto;
}

button{
  border-radius:15%;
  background-color:lightcyan;
}

button:hover{
  background-color:rgba(255,255,255,0.5);
}

button:focus{
    background-color:rgba(200,200,240,0.8);
}

.fin{
    text-decoration: line-through;
    background-color: #ddd;
    color: #777;
}

.hiddenBtn{
    display:none;
}
</style>